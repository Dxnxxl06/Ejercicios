
- -- DDL: esquema para pizzería
DROP DATABASE IF EXISTS pizzeria;
CREATE DATABASE pizzeria CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE pizzeria;

-- Clientes
CREATE TABLE customers (
  id INT AUTO_INCREMENT PRIMARY KEY,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100),
  phone VARCHAR(20),
  email VARCHAR(150),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Ingredientes
CREATE TABLE ingredients (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE
);

-- Adiciones (extras)
CREATE TABLE additions (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  price DECIMAL(10,2) NOT NULL DEFAULT 0.00
);

-- Productos generales (pizza, panzarotti, bebida, postre, etc.)
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(150) NOT NULL,
  category ENUM('pizza','panzarotti','bebida','postre','otro') NOT NULL,
  description TEXT,
  base_price DECIMAL(10,2) NOT NULL DEFAULT 0.00,
  is_prepared BOOLEAN NOT NULL DEFAULT TRUE, -- si se prepara en sitio (pizzas/panzarottis)
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Relación producto - ingrediente (n:m)
CREATE TABLE product_ingredients (
  product_id INT NOT NULL,
  ingredient_id INT NOT NULL,
  PRIMARY KEY (product_id, ingredient_id),
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
  FOREIGN KEY (ingredient_id) REFERENCES ingredients(id) ON DELETE CASCADE
);

-- Qué adiciones son aplicables a qué productos (opcional)
CREATE TABLE product_additions (
  product_id INT NOT NULL,
  addition_id INT NOT NULL,
  PRIMARY KEY (product_id, addition_id),
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
  FOREIGN KEY (addition_id) REFERENCES additions(id) ON DELETE CASCADE
);

-- Combos
CREATE TABLE combos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(150) NOT NULL,
  description TEXT,
  combo_price DECIMAL(10,2) NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Items por combo (qué productos y cantidades)
CREATE TABLE combo_items (
  combo_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL DEFAULT 1,
  PRIMARY KEY (combo_id, product_id),
  FOREIGN KEY (combo_id) REFERENCES combos(id) ON DELETE CASCADE,
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT
);

-- Menú (puede listar productos y combos; referencia la entidad por tipo)
CREATE TABLE menu_items (
  id INT AUTO_INCREMENT PRIMARY KEY,
  item_type ENUM('product','combo') NOT NULL,
  item_id INT NOT NULL, -- product.id o combo.id según item_type
  display_price DECIMAL(10,2) NOT NULL,
  available BOOLEAN NOT NULL DEFAULT TRUE,
  notes VARCHAR(255),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Pedidos
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  customer_id INT,
  order_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  total DECIMAL(12,2) NOT NULL DEFAULT 0.00,
  order_type ENUM('dine_in','takeaway') NOT NULL, -- comer en local o recoger
  status ENUM('pending','preparing','ready','completed','cancelled') DEFAULT 'pending',
  notes TEXT,
  FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE SET NULL
);

-- Líneas del pedido (puede referenciar product o combo)
CREATE TABLE order_items (
  id INT AUTO_INCREMENT PRIMARY KEY,
  order_id INT NOT NULL,
  product_id INT NULL,
  combo_id INT NULL,
  quantity INT NOT NULL DEFAULT 1,
  unit_price DECIMAL(10,2) NOT NULL, -- precio unitario aplicado
  subtotal DECIMAL(12,2) NOT NULL, -- quantity * unit_price + additions
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT,
  FOREIGN KEY (combo_id) REFERENCES combos(id) ON DELETE RESTRICT,
  CHECK ( (product_id IS NOT NULL AND combo_id IS NULL) OR (product_id IS NULL AND combo_id IS NOT NULL) )
);

-- Adiciones aplicadas a una línea de pedido
CREATE TABLE order_item_additions (
  id INT AUTO_INCREMENT PRIMARY KEY,
  order_item_id INT NOT NULL,
  addition_id INT NOT NULL,
  price DECIMAL(10,2) NOT NULL,
  FOREIGN KEY (order_item_id) REFERENCES order_items(id) ON DELETE CASCADE,
  FOREIGN KEY (addition_id) REFERENCES additions(id) ON DELETE RESTRICT
);

-- Opcional: auditoría de precios o historial (no estrictamente requerido)
CREATE TABLE price_history (
  id INT AUTO_INCREMENT PRIMARY KEY,
  entity_type ENUM('product','combo','menu') NOT NULL,
  entity_id INT NOT NULL,
  old_price DECIMAL(10,2),
  new_price DECIMAL(10,2),
  changed_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
