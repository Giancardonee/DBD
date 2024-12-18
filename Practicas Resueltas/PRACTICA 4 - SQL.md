
EJERCICIO 1: 
PARA CREAR LA TABLA: 

CREATE TABLE Cliente (
    idCliente INT PRIMARY KEY,
    nombre VARCHAR(50),
    apellido VARCHAR(50),
    DNI VARCHAR(20),
    telefono VARCHAR(20),
    direccion VARCHAR(100)
);

CREATE TABLE Producto (
    idProducto INT PRIMARY KEY,
    nombreP VARCHAR(50),
    descripcion VARCHAR(255),
    precio DECIMAL(10, 2),
    stock INT
);

CREATE TABLE Factura (
    nroTicket INT PRIMARY KEY,
    total DECIMAL(10, 2),
    fecha DATE,
    hora TIME,
    idCliente INT,
    FOREIGN KEY (idCliente) REFERENCES Cliente(idCliente)
);

CREATE TABLE Detalle (
    nroTicket INT,
    idProducto INT,
    cantidad INT,
    preciounitario DECIMAL(10, 2),
    PRIMARY KEY (nroTicket, idProducto),
    FOREIGN KEY (nroTicket) REFERENCES Factura(nroTicket),
    FOREIGN KEY (idProducto) REFERENCES Producto(idProducto)
);

1) 
SELECT DNI, nombre, apellido, telefono, direccion
FROM cliente
WHERE apellido LIKE 'Pe%'
ORDER BY DNI;

2) 
resolucion 1: 

select nombre,apellido,DNI,telefono,direccion
from cliente c inner join factura f ON c.idCliente=f.idCliente
where year(f.fecha)=2017 
and c.idCliente NOT IN (select f2.idCliente
                       from factura f2 
                       where year(f2.fecha)<>2017) 
resolucion 2:
select nombre,apellido,DNI,telefono,direccion
from cliente c JOIN factura f ON c.idCliente=f.idCliente
where year(f.fecha)=2017
except
select nombre,apellido,DNI,telefono,direccion
from cliente c JOIN factura f ON c.idCliente=f.idCliente
where year(f.fecha)<>2017

3) 
SELECT nombreP, descripcion, precio, stock
FROM cliente c 
JOIN factura f ON f.idCliente = c.idCliente 
JOIN detalle d ON f.nroTicket = d.nroTicket 
JOIN producto p ON d.idProducto = p.idProducto
WHERE c.DNI = '45789456'
and p.idProducto NOT IN (
    select p2.idProducto
    from producto p2 JOIN detalle d2 ON p2.idProducto=d2.idProducto JOIN factura f2 ON f2.nroTicket=d2.nroTicket JOIN cliente c2 ON c2.idCliente=f2.idCliente
    where c2.apellido="Garcia"
    )

4) 

SELECT p.nombreP, p.descripcion, p.precio, p.stock 
FROM producto p 
JOIN detalle d ON p.idProducto = d.idProducto 
WHERE p.idProducto NOT IN ( 
    SELECT p2.idProducto
    FROM detalle d2 
    JOIN factura f2 ON f2.nroTicket = d2.nroTicket 
    JOIN cliente c2 ON f2.idCliente = c2.idCliente
    WHERE c2.telefono LIKE '221%'
)
ORDER BY p.nombreP;

5) 

select p.nombreP,p.descripcion,p.precio, COUNT(d.nroTicket) as veces_vendidas
from producto p LEFT JOIN detalle d ON p.idProducto=d.idProducto
group by p.idProducto,p.nombreP,p.descripcion,p.precio

6) 

resolucion 1: 
select nombre,apellido,DNI,telefono,direccion
from cliente
where cliente.idCliente IN (
    select c1.idCliente 
    from cliente c1 JOIN factura f1 ON c1.idCliente=f1.idCliente JOIN detalle d1 ON d1.nroTicket=f1.nroTicket JOIN producto p1 ON p1.idProducto=d1.idProducto
    where p1.nombreP='prod1'
)
and cliente.idCliente IN (
    select c2.idCliente 
    from cliente c2 JOIN factura f2 ON c2.idCliente=f2.idCliente JOIN detalle d2 ON d2.nroTicket=f2.nroTicket JOIN producto p2 ON p2.idProducto=d2.idProducto
    where p2.nombreP='prod2'
    ) 
 and cliente.idCliente NOT IN (
    select c3.idCliente 
    from cliente c3 JOIN factura f3 ON c3.idCliente=f3.idCliente JOIN detalle d3 ON d3.nroTicket=f3.nroTicket JOIN producto p3 ON p3.idProducto=d3.idProducto
    where p2.nombreP='prod3'
     ) 

resolucion 2: Agrupando y contando para ver si coincide con la condicion
select nombre,apellido,DNI,telefono,direccion
from cliente c JOIN factura f ON c.idCliente=f.idCliente JOIN detalle d ON d.nroTicket=f.nroTicket JOIN producto p ON p.idProducto=d.idProducto
where p.nombreP IN ('prod1','prod2') 
    AND c.idCliente NOT IN (
        SELECT c2.idCliente
        FROM cliente c2
        JOIN factura f2 ON c2.idCliente = f2.idCliente
        JOIN detalle d2 ON f2.nroTicket = d2.nroTicket
        JOIN producto p2 ON d2.idProducto = p2.idProducto
        WHERE p2.nombreP = 'prod3'
    )
   group by c.idCliente
   having COUNT(DISTINCT p.idProducto)=2

   7) 

select f.nroTicket,f.total,f.fecha,f.hora,c.DNI
from cliente c JOIN factura f ON c.idCliente=f.idCliente JOIN detalle d ON d.nroTicket=f.nroTicket JOIN producto p ON p.idProducto=d.idProducto
where (p.nombreP='prod38') or (YEAR(f.fecha)=2019) 

8)

insert into cliente (idCliente,nombre,apellido,DNI,telefono,direccion) VALUES (500002,"Jorge Luis","Castor","40578999","221-4400789","11 entre 500 y 501 nro:2587")

9) 

SELECT f.nroTicket, f.total, f.fecha, f.hora
FROM cliente c
JOIN factura f ON c.idCliente = f.idCliente
JOIN detalle d ON d.nroTicket = f.nroTicket
JOIN producto p ON p.idProducto = d.idProducto
WHERE c.nombre = 'Jorge' AND c.apellido = 'Pérez'
AND f.nroTicket NOT IN (
    SELECT d2.nroTicket
    FROM detalle d2 JOIN producto p2 ON p2.idProducto = d2.idProducto
    WHERE p2.nombreP = 'Z'
)

10)

select c.DNI,c.apellido,c.nombre
from cliente c 
where c.idCliente IN (
    select c2.idCliente 
    from cliente c2 JOIN factura f2 ON f2.idCliente=c2.idCliente
    group by c2.idCliente
    having sum(f2.total) > 10000000
    ) 

Ejercicio 2: 

1) 

select ar.especie,ar.años,ar.calle,ar.nro,loc.nombreL
from podador pr JOIN poda pd ON pr.DNI=pd.DNI JOIN arbol ar ON ar.nroArbol=pd.nroArbol JOIN localidad loc ON loc.codigoPostal=pr.codigoPostalVive
where (pr.nombre='Jua' and pr.apellido='Perez') OR (pr.nombre='Jose' and pr.apellido='Garcia')

2) 

select p.DNI,p.nombre,p.apellido,p.fnac,l.nombreL
from localidad l JOIN podador p ON p.codigoPostalVive=l.codigoPostal JOIN poda ON poda.DNI=p.DNI
where year(poda.fecha)=2023

3) 

solucion 1:USANDO NOT IN (mejor para mi, es mas corta)
select a.especie,a.años,a.calle,a.nro,l.nombreL
from arbol a JOIN localidad l ON a.codigoPostal=l.codigoPostal
where a.nroArbol NOT IN (
    select a.nroArbol
    from arbol a JOIN poda p ON p.nroArbol=a.nroArbol
    ) 

solucion 2:USANDO EXCEPT (DIFERENCIA)
select a.especie, a.años, a.calle, a.nro, l.nombreL
from arbol a
join localidad l ON a.codigoPostal = l.codigoPostal
EXCEPT
select a.especie, a.años, a.calle, a.nro, l.nombreL
from arbol a
join poda p ON p.nroArbol = a.nroArbol
join localidad l ON a.codigoPostal = l.codigoPostal;

solucion 3: USANDO NOT EXISTS

select a.especie, a.años, a.calle, a.nro, l.nombreL
from arbol a
join localidad l ON a.codigoPostal = l.codigoPostal
where not exists (
    select 1
    from poda p
    where p.nroArbol = a.nroArbol
);

4) 
--------
solucion 1: usando not in

select a.especie,a.años,a.calle,a.nro,l.nombreL
from arbol a JOIN localidad l ON l.codigoPostal=a.codigoPostal JOIN poda p ON p.nroArbol=a.nroArbol
where year(p.fecha)=2022 and a.nroArbol NOT IN 
( 
 select p.nroArbol
 from poda p
 where YEAR(p.fecha)=2023
) 

solucion 2: usando except

SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
FROM arbol a INNER JOIN localidad l ON (a.codigoPostal = l.codigoPostal)
INNER JOIN poda po ON (a.nroArbol = po.nroArbol)
WHERE YEAR(po.fecha) = 2022
EXCEPT (
SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
FROM arbol a INNER JOIN localidad l ON (a.codigoPostal = l.codigoPostal)
INNER JOIN poda po ON (a.nroArbol = po.nroArbol)
WHERE YEAR(po.fecha) = 2023) 

5) 
select p.DNI,p.nombre,p.apellido,p.fnac,l.nombreL
from podador JOIN localidad l ON l.codigoPostal=p.codigoPostalVive 
where p.DNI IN ( 
  select p.DNI
  from podador p JOIN poda ON poda.DNI=p.DNI 
  where YEAR(poda.fecha)=2024 and p.apellido LIKE '%ata'
)
order by p.apellido,p.nombre 

6) 

select p.DNI,p.apellido,p.nombre,p.telefono,p.fnac
from podador p JOIN poda ON poda.DNI=p.DNI JOIN arbol a ON a.nroArbol=poda.nroARbol
where a.especie='coniferas' and p.DNI NOT IN 
(
  select p.dni
  from podador p JOIN poda ON poda.DNI=p.DNI JOIN arbol a ON a.nroArbol=poda.nroARbol
  where a.especie<>'coniferas' 

)

7) 


select a.especie
from arbol a
where a.nroArbol IN 
(
    select a.nroArbol
    from arbol a JOIN localidad l ON l.codigoPostal=a.codigoPostal
    where l.nombreL='La Plata'

    intersect 

    select a.nroArbol
    from arbol a JOIN localidad l ON l.codigoPostal=a.codigoPostal
    where l.nombre='Salta'
) 
// 
select a.especie
from arbol a
where a.nroArbol IN 
(
    select a.nroArbol
    from arbol a JOIN localidad l ON l.codigoPostal=a.codigoPostal
    where l.nombreL='La Plata'
) AND a.nroArbol IN (
    select a.nroArbol
    from arbol a JOIN localidad l ON l.codigoPostal=a.codigoPostal
    where l.nombre='Salta'
) 

8) 

DELETE FROM podador WHERE DNI=22234566

9) 

select nombreL,descripcion,#habitantes
from localidad
where codigoPostal IN 
(
  select a.codigoPostal 
  from arbol a
  group by a.codigoPostal
  HAVING COUNT(a.nroArbol) <100
)


Ejercicio 3: 

1) 

SELECT i.DNI, i.nombre, i.apellido, i.direccion, i.email
FROM integrante i
WHERE YEAR(i.fecha_nacimiento) BETWEEN 1980 AND 1990
AND i.codigoB IN (
    SELECT r.codigoB
    FROM recital r
    WHERE YEAR(r.fecha) = 2023
);


2) 

resolucion 1: 

SELECT b.nombreBanda, b.genero_musical, b.año_creacion
FROM banda b
WHERE b.codigoB IN (
    SELECT r.codigoB
    FROM recital r
    WHERE YEAR(r.fecha) = 2023
)
AND b.codigoB NOT IN (
    SELECT r.codigoB
    FROM recital r
    WHERE YEAR(r.fecha) = 2022
);



resolucion 2: 

select b.nombreBanda,b.genero_musical,b.año_creacion
from banda b JOIN recital r ON b.codigoB=r.codigoB
where YEAR(fecha)=2023 and b.codigoB NOT IN 
(
    select r.codigoB
    from recital r
    where YEAR(r.fecha)=2022
)

3) 

select b.nombreBanda,r.fecha,r.hora,e.nombre_escenario,e.ubicacion
from banda b JOIN recital R ON b.codigoB=r.codigoB JOIN escenario e ON e.nroEscenario=r.nroEscenario
where r.fecha='04/12/2023' 

4) 

SELECT i.DNI, i.nombre, i.apellido, i.email
FROM integrante i
WHERE i.codigoB IN (
    SELECT r.codigoB
    FROM recital r
    JOIN escenario e ON e.nroEscenario = r.nroEscenario
    WHERE e.nombre_escenario = 'Gustavo Cerati'
    
    INTERSECT
    
    SELECT r.codigoB
    FROM recital r
    JOIN escenario e ON e.nroEscenario = r.nroEscenario
    WHERE e.nombre_escenario = 'Carlos Gardel'
);


5) 

SELECT b.nombreB, b.genero_musical, b.año_creacion
FROM banda b
JOIN integrante i ON i.codigoB = b.codigoB
GROUP BY b.codigoB, b.nombreBanda, b.genero_musical, b.año_creacion
HAVING COUNT(i.DNI) > 8;

6) 

SELECT e.nombre_escenario, e.ubicacion, e.descripcion
FROM escenario e
JOIN recital r ON r.nroEscenario = e.nroEscenario
JOIN banda b ON b.codigoB = r.codigoB
WHERE b.genero_musical = 'rock and roll'
AND e.nroEscenario NOT IN (
    SELECT r1.nroEscenario
    FROM recital r1
    JOIN banda b1 ON b1.codigoB = r1.codigoB
    WHERE b1.genero_musical <> 'rock and roll'
)
ORDER BY e.nombre_escenario;

7) 

SELECT b.nombreB, b.genero_musical, b.año_creacion
FROM banda b
JOIN recital r ON b.codigoB = r.codigoB
JOIN escenario e ON e.nroEscenario = r.nroEscenario
WHERE e.cubierto = TRUE 
  AND YEAR(r.fecha) = 2023;

8) 

SELECT e.nroEscenario, e.nombre_escenario, e.ubicacion, e.descripcion, COUNT(r.nroEscenario) AS recitales2024
FROM escenario e 
LEFT JOIN recital r ON r.nroEscenario = e.nroEscenario AND YEAR(r.fecha) = 2024
GROUP BY e.nroEscenario, e.nombre_escenario, e.ubicacion, e.descripcion;

9) 

UPDATE banda
SET nombreBanda = 'Memphis la Blusera'
WHERE nombreBanda = 'Mempis la Blusera';

Ejercicio 4: 

1) 

SELECT p.DNI,a.Legajo,p.Apellido,p.Nombre
FROM persona p JOIN alumno a ON a.DNI=p.DNI
WHERE YEAR(a.Año_Ingreso)<2014

2) 

SELECT pr.DNI, pr.Matricula, pe.Apellido, pe.Nombre
FROM profesor pr
JOIN persona pe ON pe.DNI = pr.DNI
JOIN profesor_curso prcurso ON prcurso.DNI = pr.DNI
JOIN curso cur ON cur.cod_curso = prcurso.cod_curso
GROUP BY pr.DNI, pr.Matricula, pe.Apellido, pe.Nombre
HAVING SUM(cur.Duracion) > 100
ORDER BY pr.DNI;

3) 
//

SELECT p.DNI, p.Apellido, p.Nombre, p.Genero, p.Fecha_Nacimiento
FROM persona p
JOIN alumno a ON a.DNI = p.DNI
JOIN alumno_curso ac ON ac.DNI = a.DNI
JOIN curso c ON c.cod_curso = ac.cod_curso
WHERE c.nombre = 'Diseño de Bases de Datos' AND ac.Año = 2023;

 4) 

SELECT p.DNI, p.Apellido, p.Nombre, ac.Calificacion
FROM persona p
JOIN alumno a ON a.DNI = p.DNI
JOIN alumno_curso ac ON ac.DNI = a.DNI
JOIN curso c ON c.cod_curso = ac.cod_curso
JOIN profesor_curso pc ON pc.cod_curso = c.cod_curso
JOIN profesor prof ON prof.DNI = pc.DNI
WHERE ac.Calificacion > 8
  AND prof.Nombre = 'Juan' AND prof.Apellido = 'Garcia'
ORDER BY p.Apellido, p.Nombre;

5) 

SELECT p.DNI,p.Apellido,p.Nombre,pr.Matricula
FROM persona p JOIN profesor pr ON pr.DNI=p.DNI JOIN titulo_profesor tprofesor ON tprofesor.DNI=pr.DNI
GROUP BY p.DNI,p.Apellido,p.Nombre,pr.Matricula
HAVING COUNT(tprofesor.Cod_Titulo) > 3

6) 

SELECT p.DNI,p.Apellido,p.Nombre,SUM(c.Duracion) AS CantHS, AVG(c.Duracion) AS PromedioHS
from persona p JOIN profesor_curso pc ON pc.DNI=p.DNI JOIN curso c ON c.codCurso=pc.CodCurso
GROUP BY p.DNI,p.Apellido,p.Nombre

7) 
-- Este ejercicio me lo corrigio chatgpt, a priori no pude encontrarle la vuelta para poder hacerlo de una forma menos rancia. No me gusta para nada el Limit 1. 

SELECT c.Nombre, c.Descripcion
FROM curso c
WHERE c.Cod_Curso IN 
(
    -- Curso con más alumnos inscriptos en 2024
    SELECT ac.Cod_Curso
    FROM alumno_curso ac
    --WHERE YEAR(ac.año) = 2024--
    GROUP BY ac.Cod_Curso
    ORDER BY COUNT(ac.DNI) DESC
    LIMIT 1

    UNION ALL

    -- Curso con menos alumnos inscriptos en 2024
    SELECT ac2.Cod_Curso
    FROM alumno_curso ac2
    WHERE YEAR(ac2.año) = 2024
    GROUP BY ac2.Cod_Curso
    ORDER BY COUNT(ac2.DNI) ASC
    LIMIT 1
)

8) 

SELECT p.DNI,p.Apellido,p.Nombre.a.Legajo
from persona p JOIN alumno a ON p.DNI=a.DNI
where a.DNI IN 
(
 SELECT a2.DNI
 FROM alumno a2 JOIN Alumno_curso ac ON ac.DNI=a.DNI JOIN curso c ON c.Cod_Curso=ac.Cod_curso
 WHERE c.Nombre LIKE '%BD%'
)
and a.DNI NOT IN 
(
    SELECT a3.DNI 
    FROM alumno a3 JOIN alumno_curso ac2 ON ac2.DNI=a3.DNI 
    WHERE YEAR(ac2.Año)=2023
)

9) 

-- Damos de alta el nuevo profesor 
INSERT INTO PROFESOR (DNI, Matricula, Nro_expediente) VALUES ("45200777", "AA-DFAS12", 32);

-- Voy a dar de alta el título también (asumo que el título con código 8883 no se encuentra registrado). 
INSERT INTO TITULO (Cod_Titulo, Nombre, Descripcion) VALUES (8883, "Prof. Educación Física", "Se viste deportivo");

-- Le asignamos el título al profesor. 
INSERT INTO TITULO_PROFESOR (Cod_Titulo, DNI, Fecha) VALUES (8883, "45200777", "2024-10-02");

10) 

UPDATE PERSONA p 
SET p.Estado_Civil = 'Divorciado'
WHERE p.DNI IN 
(
    SELECT a.DNI
    FROM alumno a
    where a.Legajo='2020/09'
)

11) 

-- Primero debemos dar de baja en aquellas tablas donde aparezca: DNI como FK
 DELETE FROM ALUMNO-CURSO WHERE DNI="30568989"
 DELETE FROM ALUMNO WHERE DNI="30568989"
 -- Despues eliminamos al alumno de la tabla persona. Donde esta el DNI como PK
 DELETE FROM PERSONA WHERE DNI="30568989"

 Ejercicio 5: 

 1) 

SELECT ag.RazonSocial, ag.Direccion, ag.telef
FROM agencia ag
WHERE ag.RazonSocial IN 
(
    SELECT v.razon_social
    FROM Viaje v 
    JOIN Ciudad c ON c.CodigoPostal = v.cpOrigen 
    JOIN Cliente cl ON cl.DNI = v.DNI
    WHERE c.nombreCiudad = 'La Plata' AND cl.apellido = 'Roma'
)
ORDER BY ag.RazonSocial, ag.telef;

2) 
// no se si hay que hacerlo de un cliente en particular y se comieron el dni o algo para identificarlo, o si debe ser de todos los clientes.

3) 
SELECT ag.Razon_Social, ag.direccion, ag.telef
FROM agencia ag
WHERE ag.Razon_Social IN 
(
    SELECT ag1.Razon_Social
    FROM agencia ag1 
    JOIN viaje v ON v.razon_social = ag1.Razon_Social
    WHERE YEAR(v.fecha) = 2019 OR ag1.e_mail LIKE '%@jmail.com'
);

4) 

SELECT cl.DNI, cl.nombre, cl.apellido, cl.telefono 
FROM cliente cl
WHERE cl.DNI IN 
(
    SELECT cl2.DNI
    FROM cliente cl2 
    JOIN viaje v ON v.DNI = cl2.DNI 
    JOIN ciudad c ON c.codigoPostal = v.cpDestino
    WHERE c.nombreCiudad = 'Coronel Brandsen'
)
AND cl.DNI NOT IN 
(
    SELECT cl3.DNI
    FROM cliente cl3 
    JOIN viaje v2 ON v2.DNI = cl3.DNI 
    JOIN ciudad c2 ON c2.codigoPostal = v2.cpDestino
    WHERE c2.nombreCiudad <> 'Coronel Brandsen'
);

5) 
SELECT COUNT (v.cpDestino) AS Viajes_A_VillaElisa
from viaje v JOIN ciudad c ON c.codigoPostal=v.cpDestino
where v.razon_social='TAXI Y' and c.nombreCiudad="Villa Elisa"

 6) 
 -- Esta doble negacion me rompio la cabeza. Esta picante. 
 -- Me quedo con aquellos clientes que no tienen ninguna agencia que no haya registrado un viaje.

SELECT c.nombre, c.apellido, c.direccion, c.telefono
FROM cliente c
WHERE NOT EXIST (
    SELECT *
    FROM agencia a
    WHERE NOT EXIST (
        SELECT *
        FROM viaje v 
        WHERE (a.razon_social = v.razon_social) AND (v.DNI = c.DNI)
    )
)

7) 
UPDATE cliente c SET c.telefono='221-4400897'
WHERE c.DNI = 38495444 

8) 

SELECT ag.razon_social, ag.direccion, ag.telef
FROM agencia ag
JOIN viaje v ON v.razon_social = ag.razon_social
GROUP BY ag.razon_social, ag.direccion, ag.telef
HAVING COUNT(v.fecha) = (
    SELECT MAX(cantViajes) 
    FROM (
        SELECT COUNT(*) AS cantViajes
        FROM viaje
        GROUP BY razon_social
    ) 
)

9) 
SELECT cl.nombre,cl.apellido,cl.direccion,cl.telefono
from cliente cl JOIN viaje v ON cl.DNI=v.DNI
group by cl.dni,cl.nombre,cl.apellido
having count(cl.dni) >= 10

10) 
-- Primero borramos el registro de aquellas tablas donde tenemos el DNI como FK 
DELETE FROM viaje
WHERE DNI=40325692 

-- Despues borramos el registro en la tabla donde tenemos el DNI como PK 
DELETE FROM cliente
WHERE DNI=40325692

Ejercicio 6: 

1) 
SELECT repuesto.nombre,repuesto.stock,repuesto.precio
FROM repuesto
ORDER BY (repuesto.precio)

2) 
SELECT repuesto.nombre, repuesto.stock, repuesto.precio
FROM repuesto 
WHERE repuesto.codRep IN 
(  
    -- Repuestos que se usaron en reparaciones durante 2023
    SELECT repuesto1.codRep
    FROM repuesto repuesto1 
    JOIN repuestoReparacion rprp ON rprp.codRep = repuesto1.codRep 
    JOIN reparacion ON reparacion.nroReparac = rprp.nroReparac
    WHERE YEAR(rprp.fecha) = 2023
)
AND repuesto.codRep NOT IN 
(  
    -- Repuestos que se usaron en reparaciones del técnico "Jose Gonzalez"
    SELECT repuesto2.codRep
    FROM repuesto repuesto2 
    JOIN repuestoReparacion rprp2 ON rprp2.codRep = repuesto2.codRep 
    JOIN reparacion reparacion2 ON reparacion2.nroReparac = rprp2.nroReparac 
    JOIN tecnico t ON t.codTec = reparacion2.codTec
    WHERE t.nombre = "Jose Gonzalez"
);

3) 
SELECT t.nombre,t.especialidad
from tecnico t
where t.codTec NOT IN 
(
    select t2.codTec
    from tecnico t2 JOIN reparacion rp2 ON rp2.codTec=t2.codTec
)
ORDER BY t.nombre ASC

4) 
SELECT t.nombre,t.especialidad
from tecnico t
where t.codTec IN
(
  select t2.codTec 
  from tecnico t2 JOIN reparacion r ON t2.codTec=r.codTec
  where YEAR(r.fecha)=2022
)
and t.codTec NOT IN 
(
  select t3.codTec 
  from tecnico t3 JOIN reparacion r2 ON t3.codTec=r2.codTec
  where YEAR(r2.fecha)<>2022 
)

5) 
SELECT repuesto.nombre,repuesto.stock,COUNT(DISTINCT repuestoReparacion.codTec) AS cantidad_tecnicos
FROM repuesto LEFT JOIN repuestoReparacion ON repuesto.codRep = repuestoReparacion.codRep
GROUP BY repuesto.codRep, repuesto.nombre, repuesto.stock;

6) 
SELECT t.nombre, t.especialidad
FROM tecnico t 
JOIN reparacion r ON r.codTec = t.codTec
GROUP BY t.codTec, t.nombre, t.especialidad
HAVING COUNT(*) >= ALL ( -- tecnicos con mayor cant de reparaciones
    SELECT COUNT(*)
    FROM reparacion r
    GROUP BY r.codTec
)
UNION
SELECT t.nombre, t.especialidad
FROM tecnico t 
JOIN reparacion r ON r.codTec = t.codTec
GROUP BY t.codTec, t.nombre, t.especialidad
HAVING COUNT(*) <= ALL ( -- tecnicos con menor cant de reparaciones. 
    SELECT COUNT(*)
    FROM reparacion r
    GROUP BY r.codTec
);
