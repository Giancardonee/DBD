# Tips que tenia anotados 
(( Les diria que consulten los tips, generalmente la mayoria me los fueron diciendo los ayudantes, pero capaz los anote de una manera que se puedan tener distintas interpretaciones ))
## Conceptual: 
- Siempre que tengo 1,1 los atributos que pensamos que van en la relación , van del lado que tiene 1,1
- Voy a tener atributos en la relación cuando tenemos relacion n a n 
- Si tenemos dos relaciones con cardinalidad 1,1 de ambos lados probablemente este mal planteado (hay excepciones que este bien)
- Solamente si tenemos cardinalidad 1,1 podemos recibir un id externo de otra entidad que esté relacionada
- Historial / registro histórico siempre tienen fecha desde y fecha hasta (0,1)

## Logico/Fisico: 
- # Logico:
- Se debe eliminar jerarquias y atributos polivalentes. 
- Prestar atencion a las coberturas de las jerarquias para saber de que manera resolver el pasaje a logico
- # Fisico:
- Siempre que tenga cardinalidad 1,1, me traigo todo hacia mi misma entidad (atributos en la relacion si es que hay, y la pk de la otra entidad me la traigo como fk)
- Si tengo N a N **siempre** la relacion se convierte en entidad 
 

## Consultas (AR & SQL): 
- Para hacer una division en AR solo debo quedarme con la PK de la entidad que quiero que coincida con todos los registros
- Para hacer una division en SQL debo hacer la doble negacion.
- Producto natural lo deberiamos aplicar cuando tengamos **solo un atributo en comun**(con mismo nombre y dominio.)
- Para hacer una diferencia,union,e interseccion ambas tablas deben ser compatibles (misma cantidad de atributos y mismo dominio)
- ## Que significa que ambas tablas sean compatibles?
- Misma cantidad de atributos: Ambas relaciones deben tener el mismo número de columnas.
- Mismo dominio para cada atributo correspondiente: Las columnas en las mismas posiciones deben tener el mismo tipo de datos (por ejemplo, si la primera columna en una tabla es de tipo entero, la primera columna en la otra también debe ser de tipo entero).
