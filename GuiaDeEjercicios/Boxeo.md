-- 01_seed_data.sql
USE pizzeria;

-- Clientes
INSERT INTO customers (nombre, telefono, email, direccion, fecha_registro) VALUES
('Carlos Perez','+57 3120000000','carlos@example.com','Calle 1 #10-20','2024-11-15'),
('María López','+57 3101111111','maria@example.com','Carrera 2 #20-30','2024-12-02'),
('Andrés Gómez','+57 3152222222','andres@example.com','Calle 3 #30-40','2025-08-20'),
('Sofía Ramirez','+57 3173333333','sofia@example.com','Carrera 4 #40-50','2025-09-05'),
('Jorge Martínez','+57 3184444444','jorge@example.com','Calle 5 #50-60','2025-01-10');

-- Productos (pizzas, panzarottis, bebidas, postres)
INSERT INTO products (nombre, categoria, descripcion, precio, preparado) VALUES
('Pizza Margarita','pizza','Tomate, mozzarella, albahaca',25.00, TRUE),
('Pizza Pepperoni','pizza','Pepperoni, mozzarella, salsa roja',30.00, TRUE),
('Panzarotti Jamón y Queso','panzarotti','Jamón, queso',12.00, TRUE),
('Panzarotti Pollo BBQ','panzarotti','Pollo, salsa BBQ',13.00, TRUE),
('Coca-Cola 500ml','bebida','Gaseosa 500ml',4.00, FALSE),
('Agua 500ml','bebida','Agua embotellada 500ml',2.50, FALSE),
('Tiramisú','postre','Postre italiano',6.50, FALSE),
('Pan de Ajo','otro','Acompañamiento',5.00, TRUE);

-- Ingredientes
INSERT INTO ingredients (nombre) VALUES
('Mozzarella'),('Tomate'),('Albahaca'),('Pepperoni'),('Jamón'),('Queso'),('Pollo'),('Salsa BBQ');

-- product_ingredients (relacion)
INSERT INTO product_ingredients (product_id, ingredient_id, cantidad) VALUES
(1,1,'al gusto'), (1,2,'salsa'), (1,3,'hojas'),
(2,1,'al gusto'), (2,4,'10 unidades'), (2,2,'salsa'),
(3,5,'50g'), (3,6,'50g'),
(4,7,'80g'), (4,8,'al gusto');

-- Adiciones
INSERT INTO additions (nombre, precio) VALUES
('Extra Queso',2.00),
('Salsa Boloñesa',1.50),
('Aceitunas',1.00),
('Champiñones',1.50),
('Extra Pepperoni',2.50);

-- product_additions (permitir adiciones en pizzas y panzarottis)
INSERT INTO product_additions (product_id, addition_id) VALUES
(1,1),(1,3),(1,4),
(2,1),(2,5),(2,3),
(3,1),(3,3),
(4,1),(4,4);

-- Combos
INSERT INTO combos (nombre, descripcion, precio) VALUES
('Combo Familiar 1 Pizza + 2 Bebidas','1 Pizza grande + 2 bebidas 500ml',32.00),
('Combo Pareja 2 Pizzas Median + Pan de Ajo','2 pizzas medianas + pan de ajo',50.00);

-- combo_items
INSERT INTO combo_items (combo_id, product_id, cantidad) VALUES
(1,1,1),(1,5,2),
(2,1,2),(2,8,1);

-- Menu items
INSERT INTO menu_items (tipo, ref_id, disponible, fecha_inicio) VALUES
('producto',1,TRUE,'2025-01-01'),
('producto',2,TRUE,'2025-01-01'),
('producto',3,TRUE,'2025-01-01'),
('producto',5,TRUE,'2025-01-01'),
('combo',1,TRUE,'2025-06-01'),
('combo',2,TRUE,'2025-06-01');

-- Pedidos (variados en septiembre y octubre 2025)
INSERT INTO orders (customer_id, fecha, tipo, estado, total, observaciones, direccion_envio) VALUES
(1,'2025-09-05 12:30:00','consumir_local','entregado',36.00,'Mesa 4',NULL),
(2,'2025-09-10 19:00:00','recoger','listo',34.00,'Por favor caliente',NULL),
(3,'2025-09-20 18:15:00','recoger','entregado',18.50,'Sin cebolla',NULL),
(1,'2025-09-25 13:00:00','consumir_local','entregado',56.50,'Cumpleaños',NULL),
(4,'2025-09-28 20:00:00','recoger','entregado',29.00,NULL,NULL),
(5,'2025-10-02 21:30:00','consumir_local','pendiente',40.50,'Mesa 1',NULL),
(2,'2025-09-15 18:00:00','recoger','entregado',15.00,NULL,NULL),
(3,'2025-09-30 14:40:00','consumir_local','entregado',22.00,NULL,NULL),
(1,'2025-10-06 12:05:00','recoger','listo',44.00,NULL,NULL);

-- Order items (productos individuales y combos) — nota: nombre_cached y precios según momento
INSERT INTO order_items (order_id, product_id, combo_id, nombre_cached, precio_unit, cantidad, subtotal) VALUES
(1,1,NULL,'Pizza Margarita',25.00,1,25.00),
(1,5,NULL,'Coca-Cola 500ml',4.00,2,8.00),
(2,NULL,1,'Combo Familiar 1 Pizza + 2 Bebidas',32.00,1,32.00),
(2,3,NULL,'Panzarotti Jamón y Queso',12.00,1,12.00), -- ejemplo: edge case: both? but our check forbids both: here we used combo only; but keep one per row
(3,4,NULL,'Panzarotti Pollo BBQ',13.00,1,13.00),
(3,5,NULL,'Coca-Cola 500ml',4.00,1,4.00),
(4,2,NULL,'Pizza Pepperoni',30.00,1,30.00),
(4,8,NULL,'Pan de Ajo',5.00,1,5.00),
(4,5,NULL,'Coca-Cola 500ml',4.00,2,8.00),
(5,2,NULL,'Pizza Pepperoni',30.00,1,30.00),
(5,1,NULL,'Pizza Margarita',25.00,1,25.00),
(6,1,NULL,'Pizza Margarita',25.00,1,25.00),
(6,5,NULL,'Coca-Cola 500ml',4.00,2,8.00),
(7,3,NULL,'Panzarotti Jamón y Queso',12.00,1,12.00),
(8,1,NULL,'Pizza Margarita',25.00,1,25.00),
(8,7,NULL,'Tiramisú',6.50,1,6.50),
(9,NULL,2,'Combo Pareja 2 Pizzas Median + Pan de Ajo',50.00,1,50.00);

-- Order item additions (ej. extra queso)
INSERT INTO order_item_additions (order_item_id, addition_id, precio, cantidad, subtotal) VALUES
(1,1,2.00,1,2.00), -- Pedido 1: Pizza Margarita con extra queso
(4,1,2.00,1,2.00),
(6,3,1.00,1,1.00),
(10,5,2.50,1,2.50),
(11,1,2.00,1,2.00),
(15,1,2.00,1,2.00);

-- Actualizar totales en orders (suma simple, en práctica la app hace esto)
UPDATE orders o
SET total = (
  SELECT IFNULL(SUM(oi.subtotal),0) + IFNULL((SELECT SUM(oia.subtotal) FROM order_item_additions oia JOIN order_items oi2 ON oia.order_item_id=oi2.id WHERE oi2.order_id = o.id),0)
  FROM order_items oi WHERE oi.order_id = o.id
)
WHERE EXISTS (SELECT 1 FROM order_items oi WHERE oi.order_id = o.id);