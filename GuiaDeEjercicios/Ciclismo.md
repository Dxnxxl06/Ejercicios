SELECT p.nombre, SUM(dp.cantidad) AS total_vendidos
FROM detalle_pedido dp
JOIN productos p ON dp.id_item = p.id_producto
WHERE dp.tipo_item = 'producto'
GROUP BY p.nombre
ORDER BY total_vendidos DESC;

SELECT c.nombre, SUM(dp.cantidad * dp.precio_unitario) AS ingresos
FROM detalle_pedido dp
JOIN combos c ON dp.id_item = c.id_combo
WHERE dp.tipo_item = 'combo'
GROUP BY c.nombre;

SELECT tipo, COUNT(*) AS total_pedidos
FROM pedidos
GROUP BY tipo;

SELECT a.nombre, COUNT(*) AS veces_solicitada
FROM detalle_adiciones da
JOIN adiciones a ON da.id_adicion = a.id_adicion
GROUP BY a.nombre
ORDER BY veces_solicitada DESC;

SELECT p.categoria, SUM(dp.cantidad) AS total_vendidos
FROM detalle_pedido dp
JOIN productos p ON dp.id_item = p.id_producto
WHERE dp.tipo_item = 'producto'
GROUP BY p.categoria;

SELECT c.id_cliente, AVG(dp.cantidad) AS promedio_pizzas
FROM pedidos c
JOIN detalle_pedido dp ON c.id_pedido = dp.id_pedido
JOIN productos p ON dp.id_item = p.id_producto
WHERE p.categoria = 'pizza'
GROUP BY c.id_cliente;

SELECT DAYNAME(fecha_pedido) AS dia_semana, SUM(dp.cantidad * dp.precio_unitario) AS total_ventas
FROM pedidos
JOIN detalle_pedido dp ON pedidos.id_pedido = dp.id_pedido
GROUP BY dia_semana;

SELECT SUM(dp.cantidad) AS total_panzarottis_extra_queso
FROM detalle_pedido dp
JOIN productos p ON dp.id_item = p.id_producto
JOIN detalle_adiciones da ON da.id_detalle = dp.id_detalle
JOIN adiciones a ON da.id_adicion = a.id_adicion
WHERE p.categoria = 'panzarotti' AND a.nombre LIKE '%queso%';

SELECT DISTINCT dp.id_pedido
FROM detalle_pedido dp
JOIN combo_detalle cd ON dp.id_item = cd.id_combo
JOIN productos p ON cd.id_producto = p.id_producto
WHERE p.categoria = 'bebida';

SELECT c.id_cliente, COUNT(p.id_pedido) AS total_pedidos
FROM clientes c
JOIN pedidos p ON c.id_cliente = p.id_cliente
WHERE MONTH(p.fecha_pedido) = MONTH(CURRENT_DATE - INTERVAL 1 MONTH)
GROUP BY c.id_cliente
HAVING total_pedidos > 5;

SELECT SUM(dp.cantidad * dp.precio_unitario) AS total_no_elaborados
FROM detalle_pedido dp
JOIN productos p ON dp.id_item = p.id_producto
WHERE p.elaborado = FALSE;

SELECT AVG(adiciones_por_pedido) AS promedio_adiciones
FROM (
  SELECT id_pedido, COUNT(*) AS adiciones_por_pedido
  FROM detalle_adiciones da
  JOIN detalle_pedido dp ON da.id_detalle = dp.id_detalle
  GROUP BY id_pedido
) t;

SELECT COUNT(*) AS combos_vendidos
FROM detalle_pedido
WHERE tipo_item = 'combo'
AND MONTH(fecha_pedido) = MONTH(CURRENT_DATE - INTERVAL 1 MONTH);

SELECT c.id_cliente, COUNT(DISTINCT tipo) AS tipos_pedido
FROM pedidos c
GROUP BY c.id_cliente
HAVING tipos_pedido > 1;

SELECT COUNT(DISTINCT dp.id_detalle) AS productos_con_adiciones
FROM detalle_pedido dp
JOIN detalle_adiciones da ON dp.id_detalle = da.id_detalle;

SELECT id_pedido
FROM detalle_pedido
GROUP BY id_pedido
HAVING COUNT(DISTINCT id_item) > 3;

SELECT DATE(fecha_pedido) AS fecha, AVG(dp.cantidad * dp.precio_unitario) AS promedio_ingresos
FROM pedidos p
JOIN detalle_pedido dp ON p.id_pedido = dp.id_pedido
GROUP BY fecha;

SELECT c.id_cliente
FROM pedidos c
JOIN detalle_pedido dp ON c.id_pedido = dp.id_pedido
JOIN productos p ON dp.id_item = p.id_producto
LEFT JOIN detalle_adiciones da ON da.id_detalle = dp.id_detalle
WHERE p.categoria = 'pizza'
GROUP BY c.id_cliente
HAVING SUM(CASE WHEN da.id_adicion IS NOT NULL THEN 1 ELSE 0 END) / COUNT(*) > 0.5;

SELECT 
  (SUM(CASE WHEN p.elaborado = FALSE THEN dp.cantidad * dp.precio_unitario ELSE 0 END) /
   SUM(dp.cantidad * dp.precio_unitario)) * 100 AS porcentaje_no_elaborados
FROM detalle_pedido dp
JOIN productos p ON dp.id_item = p.id_producto;

SELECT DAYNAME(fecha_pedido) AS dia_semana, COUNT(*) AS pedidos_recoger
FROM pedidos
WHERE tipo = 'recoger'
GROUP BY dia_semana
ORDER BY pedidos_recoger DESC
LIMIT 1;