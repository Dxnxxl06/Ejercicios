DROP DATABASE IF EXISTS pizzeria;
CREATE DATABASE pizzeria CHARACTER SET utf8mb4 COLLATE utf8mb4_spanish_ci;
USE pizzeria;

CREATE TABLE clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    correo VARCHAR(100) UNIQUE,
    direccion VARCHAR(255),
    fecha_registro DATE DEFAULT CURRENT_DATE
);

CREATE TABLE productos (
    id_producto INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(150) NOT NULL,
    categoria ENUM('pizza','panzarotti','bebida','postre','otro') NOT NULL,
    descripcion TEXT,
    precio DECIMAL(10,2) NOT NULL,
    elaborado BOOLEAN NOT NULL DEFAULT TRUE,
    activo BOOLEAN NOT NULL DEFAULT TRUE,
    fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE ingredientes (
    id_ingrediente INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE producto_ingrediente (
    id_producto INT NOT NULL,
    id_ingrediente INT NOT NULL,
    cantidad VARCHAR(50),
    PRIMARY KEY (id_producto, id_ingrediente),
    FOREIGN KEY (id_producto) REFERENCES productos(id_producto) ON DELETE CASCADE,
    FOREIGN KEY (id_ingrediente) REFERENCES ingredientes(id_ingrediente) ON DELETE CASCADE
);

CREATE TABLE adiciones (
    id_adicion INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    precio DECIMAL(10,2) NOT NULL DEFAULT 0.00,
    activa BOOLEAN NOT NULL DEFAULT TRUE
);

CREATE TABLE producto_adicion (
    id_producto INT NOT NULL,
    id_adicion INT NOT NULL,
    PRIMARY KEY (id_producto, id_adicion),
    FOREIGN KEY (id_producto) REFERENCES productos(id_producto) ON DELETE CASCADE,
    FOREIGN KEY (id_adicion) REFERENCES adiciones(id_adicion) ON DELETE CASCADE
);

CREATE TABLE combos (
    id_combo INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(150) NOT NULL,
    descripcion TEXT,
    precio DECIMAL(10,2) NOT NULL,
    activo BOOLEAN NOT NULL DEFAULT TRUE,
    fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE combo_detalle (
    id_combo INT NOT NULL,
    id_producto INT NOT NULL,
    cantidad INT NOT NULL DEFAULT 1,
    PRIMARY KEY (id_combo, id_producto),
    FOREIGN KEY (id_combo) REFERENCES combos(id_combo) ON DELETE CASCADE,
    FOREIGN KEY (id_producto) REFERENCES productos(id_producto) ON DELETE CASCADE
);

CREATE TABLE menu (
    id_menu INT AUTO_INCREMENT PRIMARY KEY,
    tipo ENUM('producto','combo') NOT NULL,
    id_referencia INT NOT NULL,
    disponible BOOLEAN NOT NULL DEFAULT TRUE,
    fecha_inicio DATE,
    fecha_fin DATE
);

CREATE TABLE pedidos (
    id_pedido INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT,
    tipo ENUM('consumir_en_local','recoger') NOT NULL,
    estado ENUM('pendiente','en_preparacion','listo','entregado','cancelado') DEFAULT 'pendiente',
    fecha_pedido DATETIME DEFAULT CURRENT_TIMESTAMP,
    notas TEXT,
    FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente) ON DELETE SET NULL
);

CREATE TABLE detalle_pedido (
    id_detalle INT AUTO_INCREMENT PRIMARY KEY,
    id_pedido INT NOT NULL,
    tipo_item ENUM('producto','combo') NOT NULL,
    id_item INT NOT NULL,
    cantidad INT NOT NULL DEFAULT 1,
    precio_unitario DECIMAL(10,2) NOT NULL,
    notas TEXT,
    FOREIGN KEY (id_pedido) REFERENCES pedidos(id_pedido) ON DELETE CASCADE
);

CREATE TABLE detalle_adiciones (
    id_detalle INT NOT NULL,
    id_adicion INT NOT NULL,
    cantidad INT DEFAULT 1,
    PRIMARY KEY (id_detalle, id_adicion),
    FOREIGN KEY (id_detalle) REFERENCES detalle_pedido(id_detalle) ON DELETE CASCADE,
    FOREIGN KEY (id_adicion) REFERENCES adiciones(id_adicion) ON DELETE CASCADE
);