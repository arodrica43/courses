# SQL Avanzado para Analytics - Capítulo 1 (Detallado)

## Fundamentos y antecedentes de TSQL

Transact-SQL (TSQL) es una extensión robusta del lenguaje SQL estándar, desarrollada conjuntamente por Microsoft y Sybase. TSQL amplía las capacidades de SQL al incorporar características de programación avanzadas, control de flujo y manejo de errores.

Características clave de TSQL:
1. Procedimientos almacenados: Permiten encapsular lógica compleja y mejorar el rendimiento.
2. Funciones definidas por el usuario: Facilitan la reutilización de código y la modularidad.
3. Triggers: Automatizan respuestas a eventos en la base de datos.
4. Transacciones: Garantizan la integridad de los datos en operaciones complejas.

TSQL se distingue del SQL estándar por su sintaxis extendida para declaraciones de variables, control de flujo (IF, WHILE), y manejo de errores (TRY...CATCH). Estas características hacen que TSQL sea especialmente poderoso para el análisis de datos y la creación de aplicaciones de bases de datos robustas.

## Primeras consultas

Las consultas en TSQL son la base del análisis de datos. Una consulta básica sigue esta estructura:

```sql
SELECT columna1, columna2, ...
FROM tabla
WHERE condición
ORDER BY columna [ASC|DESC]
```

Elementos clave:
- SELECT: Especifica las columnas a recuperar.
- FROM: Indica la tabla o tablas de origen.
- WHERE: Filtra las filas basándose en condiciones.
- ORDER BY: Ordena los resultados.

Operadores avanzados:
- LIKE: Para búsquedas de patrones en texto.
- IN: Compara con múltiples valores.
- BETWEEN: Selecciona un rango de valores.

Ejemplo avanzado:
```sql
SELECT e.nombre, e.salario, d.nombre_departamento
FROM empleados e
INNER JOIN departamentos d ON e.id_departamento = d.id
WHERE e.salario > 50000 AND d.nombre_departamento LIKE 'Ventas%'
ORDER BY e.salario DESC
```

Esta consulta combina datos de dos tablas, aplica filtros complejos y ordena los resultados.

## Nulls

NULL en SQL representa la ausencia de un valor o un valor desconocido. Es fundamental entender que NULL no es cero, una cadena vacía, o falso. El manejo correcto de NULLs es crucial para evitar errores en los análisis.

Características importantes de NULL:
1. Cualquier operación con NULL resulta en NULL.
2. NULL no es igual a NULL (se usa IS NULL para comparar).
3. Los NULLs afectan los resultados de funciones de agregación.

Funciones para manejar NULLs:
- ISNULL(columna, valor_alternativo): Reemplaza NULL con un valor específico.
- COALESCE(valor1, valor2, ...): Devuelve el primer valor no NULL de la lista.

Ejemplo:
```sql
SELECT 
    nombre,
    COALESCE(telefono, email, 'Sin contacto') AS contacto
FROM clientes
```

Esta consulta proporciona un método de contacto, priorizando teléfono sobre email, y usa 'Sin contacto' si ambos son NULL.

## Consultas agrupadas

Las consultas agrupadas son esenciales para el análisis de datos agregados. La cláusula GROUP BY permite agrupar filas que comparten valores comunes en una o más columnas.

Estructura básica:
```sql
SELECT columna1, función_agregación(columna2)
FROM tabla
GROUP BY columna1
HAVING condición_de_grupo
```

Funciones de agregación comunes:
- SUM(): Suma de valores.
- AVG(): Promedio.
- COUNT(): Recuento de filas.
- MAX() y MIN(): Valores máximo y mínimo.

La cláusula HAVING se usa para filtrar grupos, similar a WHERE para filas individuales.

Ejemplo avanzado:
```sql
SELECT 
    d.nombre_departamento,
    AVG(e.salario) as salario_promedio,
    COUNT(e.id) as num_empleados
FROM empleados e
JOIN departamentos d ON e.id_departamento = d.id
GROUP BY d.nombre_departamento
HAVING COUNT(e.id) > 5 AND AVG(e.salario) > 60000
ORDER BY salario_promedio DESC
```

Esta consulta calcula el salario promedio y el número de empleados por departamento, filtrando departamentos con más de 5 empleados y un salario promedio superior a 60,000.

## Relación de tablas-vistas

Las relaciones entre tablas se establecen mediante JOINs, que son fundamentales para combinar datos de múltiples fuentes.

Tipos principales de JOIN:
1. INNER JOIN: Retorna filas cuando hay coincidencia en ambas tablas.
2. LEFT JOIN: Retorna todas las filas de la tabla izquierda y las coincidencias de la derecha.
3. RIGHT JOIN: Inverso del LEFT JOIN.
4. FULL OUTER JOIN: Retorna todas las filas cuando hay una coincidencia en cualquiera de las tablas.

Ejemplo de JOIN complejo:
```sql
SELECT 
    c.nombre_cliente,
    p.nombre_producto,
    v.fecha_venta,
    v.cantidad * p.precio as total_venta
FROM ventas v
INNER JOIN clientes c ON v.id_cliente = c.id
INNER JOIN productos p ON v.id_producto = p.id
LEFT JOIN descuentos d ON v.id_venta = d.id_venta
WHERE v.fecha_venta BETWEEN '2023-01-01' AND '2023-12-31'
```

Vistas:
Las vistas son consultas almacenadas que se comportan como tablas virtuales. Ofrecen varias ventajas:
1. Simplificación de consultas complejas.
2. Abstracción de la estructura de datos subyacente.
3. Control de acceso a datos sensibles.

Ejemplo de creación de vista:
```sql
CREATE VIEW ventas_anuales AS
SELECT 
    YEAR(fecha_venta) as año,
    SUM(cantidad * precio) as total_ventas
FROM ventas
JOIN productos ON ventas.id_producto = productos.id
GROUP BY YEAR(fecha_venta)
```

Esta vista simplifica el análisis de ventas anuales sin necesidad de repetir la lógica compleja en cada consulta.

## Análisis de datos

El análisis avanzado de datos en TSQL se potencia con funciones de ventana (window functions), que realizan cálculos a través de conjuntos de filas relacionadas con la fila actual.

Funciones de ventana comunes:
1. ROW_NUMBER(): Asigna números únicos a filas.
2. RANK() y DENSE_RANK(): Asignan rankings, con y sin huecos respectivamente.
3. LAG() y LEAD(): Acceden a datos de filas anteriores o posteriores.
4. SUM() OVER(): Calcula sumas acumulativas.

Ejemplo de análisis con funciones de ventana:
```sql
SELECT 
    fecha_venta,
    producto,
    venta_diaria,
    SUM(venta_diaria) OVER (ORDER BY fecha_venta) as venta_acumulada,
    AVG(venta_diaria) OVER (ORDER BY fecha_venta ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as promedio_movil_7dias
FROM ventas_diarias
```

Esta consulta calcula ventas acumuladas y un promedio móvil de 7 días, herramientas poderosas para el análisis de tendencias.

## Comenzando a automatizar en SQL

La automatización en SQL se logra principalmente a través de procedimientos almacenados, funciones y triggers.

Procedimientos almacenados:
```sql
CREATE PROCEDURE ActualizarInventario
    @producto_id INT,
    @cantidad INT
AS
BEGIN
    UPDATE inventario
    SET stock = stock - @cantidad
    WHERE id_producto = @producto_id

    IF @@ROWCOUNT = 0
        THROW 50001, 'Producto no encontrado', 1

    IF (SELECT stock FROM inventario WHERE id_producto = @producto_id) < 0
        THROW 50002, 'Stock insuficiente', 1
END
```

Este procedimiento actualiza el inventario y maneja errores comunes.

Funciones definidas por el usuario:
```sql
CREATE FUNCTION CalcularDescuento
(
    @precio DECIMAL(10,2),
    @cantidad INT
)
RETURNS DECIMAL(10,2)
AS
BEGIN
    DECLARE @descuento DECIMAL(10,2)
    SET @descuento = CASE
        WHEN @cantidad > 100 THEN 0.2
        WHEN @cantidad > 50 THEN 0.1
        ELSE 0.05
    END
    RETURN @precio * (1 - @descuento)
END
```

Esta función calcula descuentos basados en la cantidad comprada.

## Tratamiento de la información

El tratamiento eficaz de la información implica manipular y transformar datos para el análisis.

Manipulación de cadenas:
```sql
SELECT 
    UPPER(nombre) as nombre_mayusculas,
    LEFT(apellido, 1) + '.' as inicial_apellido,
    SUBSTRING(email, CHARINDEX('@', email) + 1, LEN(email)) as dominio_email
FROM usuarios
```

Manejo de fechas:
```sql
SELECT 
    fecha_pedido,
    DATEADD(day, 7, fecha_pedido) as fecha_entrega_estimada,
    DATEDIFF(day, fecha_pedido, GETDATE()) as dias_transcurridos
FROM pedidos
```

Conversión de tipos:
```sql
SELECT 
    CAST(precio as INT) as precio_redondeado,
    CONVERT(varchar(10), fecha_venta, 103) as fecha_formato_europeo
FROM ventas
```

## Exploración de datos

La exploración de datos en SQL implica técnicas para examinar y entender las características de los datos almacenados.

Estadísticas descriptivas:
```sql
SELECT 
    COUNT(*) as total_registros,
    AVG(precio) as precio_promedio,
    STDEV(precio) as desviacion_estandar_precio,
    MIN(precio) as precio_minimo,
    MAX(precio) as precio_maximo
FROM productos
```

Detección de outliers:
```sql
WITH stats AS (
    SELECT 
        AVG(precio) as precio_medio,
        STDEV(precio) as precio_std
    FROM productos
)
SELECT *
FROM productos, stats
WHERE precio < (precio_medio - 2 * precio_std)
   OR precio > (precio_medio + 2 * precio_std)
```

Esta consulta identifica productos con precios que se desvían significativamente de la media.

Muestreo de datos:
```sql
SELECT TOP 10 PERCENT *
FROM grandes_datos
ORDER BY NEWID()
```

Esta técnica selecciona una muestra aleatoria del 10% de los datos, útil para análisis preliminares en conjuntos de datos muy grandes.


