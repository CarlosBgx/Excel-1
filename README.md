En este proyecto se normaliza una base de datos que contiene 15 columnas que no cumplen la 1FN ni la 2FN
Puede consultar la base de datos original y normalizada descargando el archivo tienda_ficticia.xlsx
Puede consultar el diagrama de la tabla normalizada en este vínculo: https://app.mural.co/t/personal60065/m/personal60065/1738171172892/1b9fe56149b7383d84ad9a62bbed1bd538ea7036?sender=u8378c6fa94e2d50ef6d50063

- ID cliente: Identificador único para cada cliente.
- Nombre cliente: Nombre completo del cliente (ejemplo: "Ana Pérez").
- Teléfono y correo: Ambos datos combinados en una misma columna, los números están en formatos variados
- Dirección completa: Dirección con ciudad y código postal
- Fecha de compra: Fecha en la que se realizó la compra.
- Producto: Nombre del producto comprado.
- Categoría del producto: Categoría del producto (duplicada y redundante).
- Cantidad y precio: Número de productos y su precio combinados en una columna (ejemplo: "3x120").
- Total compra: Total calculado de la compra (redundancia).
- Método de pago: Forma de pago utilizada.
- Promociones aplicadas: Información combinada de varias promociones.
- ID factura: Número único de factura.
- Fecha de entrega: Fecha en la que se entregó el producto.
- Empleado asociado: Nombre del empleado que gestionó la compra.
- Comentarios: Observaciones adicionales de la compra.

- Proceso de normalización 1FN:
1. La columna teléfono y correo se separa según el delimitador /
Los números están en formatos muy varios, algunos tienen una x, otros un espacio, signo -, etc. Se utiliza la fórmula:
=SUSTITUIR(SUSTITUIR(SUSTITUIR(SUSTITUIR(SUSTITUIR([@Teléfono]; "("; ""); ")"; ""); "-"; ""); "x"; ""); "."; "")
Para eliminar todos los caracteres adicionales dentro de la columna teléfono

2. La columna Dirección completa tiene la dirección, ciudad, estado y código postal juntos. Por ej.: 3573 Walnut Way Suite 942, Kentstad, AZ, 45949
Se utilizan las siguientes fórmulas para separarlo en cuatro columnas:
Dirección: =IZQUIERDA([@[Dirección Completa]];ENCONTRAR(",";[@[Dirección Completa]])-1)
Esta retorna todos los caracteres desde el inicio hasta la primera coma, luego extrae esa información restándole un espacio para evitar la coma, por ej.: 3573 Walnut Way Suite 942

Ciudad: =EXTRAE([@[Dirección Completa]]; ENCONTRAR(",";[@[Dirección Completa]])+2; ENCONTRAR(",";[@[Dirección Completa]];ENCONTRAR(",";[@[Dirección Completa]])+1)-ENCONTRAR(",";[@[Dirección Completa]])-2)
Esta función encuentra la primera coma, le suma dos caracteres como punto de inicio y extrae la información hasta la segunda coma, por ej. Kentstad

Estado: =DERECHA([@[Dirección Completa]]; LARGO([@[Dirección Completa]]) - ENCONTRAR(",";[@[Dirección Completa]]) - 1)
Esta fórmula extrae el texto desde la primera coma hasta el final, luego usamos

=EXTRAE([@Temporal]; ENCONTRAR(",";[@Temporal]) + 2; ENCONTRAR(",";[@Temporal]; ENCONTRAR(",";[@Temporal]) + 1) - ENCONTRAR(",";[@Temporal]) - 2)
Esta fórmula ubica la primera coma, le suma dos como punto de partida y extrae el número de caracteres que hay hasta dos caracteres antes de la segunda coma, por ej. AZ

Código postal: =DERECHA([@[Dirección Completa]]; 5)
Esta fórmula empieza a contar 5 caracteres desde el final de la dirección y los extrae, por ej. 45949

4. La columna cantidad y precio contiene dos valores que deberían estar separados, ej. 4x1913, esto indica que se compraron 4 productos por un valor de $1913 c/u
Se separan usando la función Texto en Columnas, tomando como separador la x

5. Se verifica la presencia de duplicados y se borran en caso de que existan

6. Se modifica el formato de código postal y teléfono para que sean reconocidos como números

7. Se modifica el formato de la columna de precios para que sea reconocido como una moneda

8. Se reemplazan los espacios vacíos de la columna de comentarios por N/A

- Proceso de normalización 2FN:
(*) Clave primaria
(X) Clave foránea

1. Se crea una nueva tabla de clientes que contiene las columnas
ID Cliente (*)
Nombre Cliente
Teléfono 
Correo
Dirección
ID_Ciudad (X)

2. Se crea una nueva tabla llamada Ciudad que contiene las columnas
ID_Ciudad (*) 
Ciudad
ID_Estado (X)

3. Se crea una nueva tabla llamada Estado que contiene las columnas
ID_Estado(*)
Estado
Código_postal

4. Se crea una nueva tabla llamada Empleado que contiene las columnas
ID_Empleado(*)
Empleado_Asociado

5. Se crea una nueva tabla llamada Productos que contiene las columnas
ID_Producto (*)
Producto
ID_Categoría (X)

6. Se crea una nueva tabla llamada Categorías que contiene las columnas
ID_Categoría (*)
Categoría_del_Producto

7. Se crea una nueva tabla llamada Métodos de pago que contiene las columnas
ID_MetodoPago (*)
Método_de_Pago

8. Se crea una nueva tabla llamada Promociones que contiene las columnas
ID_Promocion (*)
Promoción aplicada

9. Se crea una nueva tabla llamada Factura que contiene las columnas
ID_Factura(*)
ID_Cliente(X)
ID_Método_de_Pago
ID_Promoción
Fecha de Compra
Fecha_de_Entrega

10. Se crea una nueva tabla llamada Detalle_Factura que contiene las columnas
ID_Detalle_Factura
ID_Factura
ID_Producto
Cantidad
Precio
Total_Compra
Comentarios

Puede consultar el diagrama de las tabla normalizada en este vínculo: https://app.mural.co/t/personal60065/m/personal60065/1738171172892/1b9fe56149b7383d84ad9a62bbed1bd538ea7036?sender=u8378c6fa94e2d50ef6d50063
