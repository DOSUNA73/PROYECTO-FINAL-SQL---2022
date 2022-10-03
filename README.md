# PROYECTO-FINAL-SQL---2022
MODELO DE NEGOCIOS MUSIC STORE - OSUNA DAVID

 --CREACION DE TABLAS 
 
CREATE SCHEMA MUSIC_STORE; 
USE MUSIC_STORE;

CREATE TABLE CLIENTE (
idCliente INT NOT NULL AUTO_INCREMENT,
Nombres VARCHAR (50),
Apellidos VARCHAR (50), 
Direccion VARCHAR (100),
Telefono INT NOT NULL,
Apertura_cuenta DATETIME DEFAULT (CURRENT_DATE),
PRIMARY KEY (IdCliente)) ;

CREATE TABLE PUESTO (
idPuesto INT NOT NULL AUTO_INCREMENT,
puesto VARCHAR (50) NOT NULL,
salario_bruto FLOAT NOT NULL,
PRIMARY KEY (idPuesto)
);

CREATE TABLE EMPLEADO (
idEmpleado INT NOT NULL auto_increment,
Nombres VARCHAR (50),
Apellidos VARCHAR (50),
Direccion VARCHAR (100),
Telefono INT NOT NULL,
idPuesto INT NOT NULL,
PRIMARY KEY (idEmpleado),
FOREIGN KEY (idPuesto) REFERENCES PUESTO (idPuesto)
);

CREATE TABLE PROVEEDOR (
idProveedor INT NOT NULL auto_increment,
Razon_Social VARCHAR  (100) not null, 
Direccion VARCHAR  (50) null ,
Telefono INT NOT NULL ,
email VARCHAR (50) NULL, 
PRIMARY KEY (idProveedor));

CREATE TABLE CATEGORIA (
idCategoria INT NOT NULL AUTO_INCREMENT,
Categoria VARCHAR (20) NOT NULL,
PRIMARY KEY (idCategoria)
);

CREATE TABLE PRODUCTO (
idProducto INT NOT NULL auto_increment,
Producto VARCHAR (100) not null, 
idProveedor INT NOT NULL,
idCategoria INT NOT NULL,
Stock INT NOT NULL, 
Precio FLOAT NOT NULL,
PRIMARY KEY (idProducto),
FOREIGN KEY (idProveedor) REFERENCES PROVEEDOR (idProveedor),
FOREIGN KEY (idCategoria) REFERENCES CATEGORIA (idCategoria)
);

CREATE TABLE FACTURACION (
idFacturacion INT NOT NULL AUTO_INCREMENT,
idCliente INT NOT NULL ,
idEmpleado INT NOT NULL ,
idProveedor INT NOT NULL,
idProducto INT NOT NULL,
precio INT,
unidades INT, 
Fecha DATETIME DEFAULT (CURRENT_DATE),
PRIMARY KEY (idFacturacion),  
FOREIGN KEY (idCliente) REFERENCES CLIENTE (idCliente),
FOREIGN KEY (idEmpleado) REFERENCES EMPLEADO (idEmpleado),
FOREIGN KEY (idProveedor) REFERENCES PROVEEDOR (idProveedor),
FOREIGN KEY (idProducto) REFERENCES PRODUCTO (idProducto)
);


CREATE TABLE PEDIDO (
idPedido INT NOT NULL AUTO_INCREMENT,
idCliente INT NOT NULL,
direccion VARCHAR (100),
idProducto INT NOT NULL,
PRIMARY KEY (idPedido), 
FOREIGN KEY (idCliente) REFERENCES CLIENTE (idCliente),
FOREIGN KEY (idProducto) REFERENCES PRODUCTO (idProducto)
);

-- CREACION DE TABLAS LOG PARA TRIGGERS

CREATE TABLE LOG_MOVIMIENTO_CLIENTE (
idLog_Cliente INT NOT NULL auto_increment, 
Fecha DATETIME NOT NULL,
UsuarioID VARCHAR (50) NOT NULL,
idCliente INT NOT NULL,
Nombres VARCHAR (50),
Apellidos VARCHAR (50), 
Direccion VARCHAR (100),
Tipo_movimiento VARCHAR (100),
PRIMARY KEY (idLog_Cliente)
);

CREATE TABLE LOG_CONTROL_FACTURACION (
idFactura INT NOT NULL AUTO_INCREMENT, 
usuario VARCHAR (50), 
cliente VARCHAR (100), 
producto VARCHAR (100), 
unidades INT, 
Precio FLOAT, 
fecha DATETIME, 
tipo_movimiento VARCHAR (50),
PRIMARY KEY  (idFactura) );

-- TRIGGERS

CREATE TRIGGER trg_CLIENTE 
AFTER INSERT ON CLIENTE
FOR EACH ROW
INSERT INTO LOG_MOVIMIENTO_CLIENTE 
(idLog_Cliente, Fecha, UsuarioID, idCliente, Nombres, Apellidos, Direccion, Tipo_movimiento)
VALUES
(NULL, NOW(), SESSION_USER(), NEW.idCliente, NEW.nombres, NEW.apellidos, NEW.direccion, 'INSERT (REGISTRO AGREGADO)');


CREATE TRIGGER trg_control_facturacion
BEFORE INSERT ON facturacion
FOR EACH ROW
INSERT INTO LOG_CONTROL_FACTURACION 
			(idFactura, usuario, cliente, producto, unidades, precio, fecha, tipo_movimiento )
VALUES		(SESSION_USER(), NEW.idcliente, NEW.idproducto, NEW.unidades, NEW.precio, NOW(), 'INSERT');

-- INSERT REGISTROS

INSERT INTO CLIENTE (idCliente, nombres, apellidos, direccion, telefono, apertura_cuenta)
VALUES (NULL, 'marco','melian', 'av. de mayo 1300 CABA', 01167858999, '2022-08-09'),
(NULL,'sergio','aguero', 'av. eva peron 5083 BERAZATEGUI', 01123325544, '2022-04-27'),
(NULL,'damian','mendoza', 'av. cnel diaz 2020 CABA', 01155887676,'2022-08-09'),
(NULL,'agustin','dos santos', 'larrea 977 CABA', 01122407785, '2019-06-02'),
(NULL,'pedro','cabrera', 'garibaldi 137 QUILMES', 01167858999, '2018-12-12');

INSERT INTO PUESTO (idPuesto, puesto, salario_bruto)
VALUES (NULL, 'Vendedor', 70000),
(NULL, 'Encargado', 100000),
(NULL, 'Repositor', 50000);

INSERT INTO EMPLEADO (idEmpleado, nombres, apellidos, direccion, telefono, idPuesto)
VALUES (NULL, 'pablo','ambrosio', 'av. de corriente 1300 CABA', 01164564579, 1),
(NULL,'gustavo','fernandez', 'videla 145 QUILMES', 01185685642, 1),
(NULL,'guillermo','lencinas', 'pichincha 4455 CABA', 01114578566, 2),
(NULL,'aaron','paul', '47th st MANHATTAN', 212675677, 2),
(NULL,'sergio','asad', 'uriburu 1115 CABA', 01178459988, 3);

INSERT INTO PROVEEDOR (idProveedor, razon_social, direccion, telefono, email)
VALUES (NULL,'todo musica SA','av. warnes 2280 CABA',55448000 ,'todomusica@todom.com.ar'),
(NULL,'the rock store SA','av. boyaca 157 CABA',45505566,'tiendarockstore@hotmail.com.ar'),
(NULL,'central media SAC','av. carabobo 1070 CABA',42582269,'contactocentralm@gmail.com.ar'),
(NULL,'pro musica SAC','talcahuano 170 CABA',43822700,'promusic@prom.com.ar'),
(NULL,'aitana musica SA','irigoyen 2020',42537744,'aitanamusic@aitana.com.ar');

INSERT INTO CATEGORIA (idCategoria, Categoria)
VALUES (NULL, 'Cuerdas'),
(NULL, 'Percusión'),
(NULL, 'Viento'),
(NULL, 'Electrónico');

INSERT INTO PRODUCTO (idProducto, producto, idProveedor, idCategoria, Stock, Precio)
VALUES (NULL, 'Guitarra Fender Mexico', 1, 1, 20, 1000),
(NULL, 'Bateria Lazer 1232', 2, 2, 10, 5000),
(NULL, 'Oboe OXES', 1, 3, 12, 3000);
INSERT INTO PRODUCTO (`idProducto`, `Producto`, `idProveedor`, `idCategoria`, `Stock`, `Precio`) 
VALUES 	(NULL, 'Bajo Fender USA', '3', '1', '19', '70000'),
		(NULL, 'Shaker Meinl USA', '1', '2', '6', '1890'),
		(NULL, 'Bajo tipo JAZZ BASS', '4', '1', '700', '45000'),
		(NULL,'Guitarra clasica YAMAHA', '3', '1', '15', '7488'),
		(NULL,'Ukelele Soprano', '1', '1', '16', '2750'),
		(NULL,'Ukelele rojo', '1', '1', '60', '2750'),
		(NULL,'Saxo YAMAHA', '5', '3', '77', '3000'),
		(NULL,'teclado 8 octavas', '2', '4', '100', '7000');
        
INSERT INTO FACTURACION (idFacturacion, idCliente, idEmpleado, idProveedor,idProducto, precio, unidades, Fecha) 
VALUES	('1', '2', '2', '1', '2', '1000', '2', '2017-01-25'),
		('2', '2', '1', '2', '3', '9000', '3', '2017-03-07'),
		('3', '1', '3', '3', '7', '70000', '1', '2018-05-10'),
		('4', '4', '2', '5', '13', '3000', '1', '2020-06-06'),
		('5', '5', '1', '1', '11', '5500', '2', '2022-06-05');

-- TCL

SET @@AUTOCOMMIT=0 -- seteamos el autoccomit en 0 (autoguardado desactivado)

START TRANSACTION;

INSERT INTO empleado VALUES (7, 'Gustavo', 'Aguirre','av callao 1640', 1548484944) ; 
INSERT INTO empleado VALUES (8, 'pablo', 'gomez','eva peron 5083', 42562563) ; 
INSERT INTO empleado VALUES (9, 'maria', 'costas','158 1930', 42537132) ; 
INSERT INTO empleado VALUES (10, 'silvia', 'cuevas','cnel diaz 1515', 6702255) ; 

SAVEPOINt insert_01 ;


INSERT INTO empleado VALUES (11, 'alfredo', 'perez', 'cabildo 1212', 1516708040 ) ; 
INSERT INTO empleado VALUES (12, 'agostina', 'flores', 'calle falsa 123',20405588) ; 
INSERT INTO empleado VALUES (13, 'ursula', 'vazquez', 'pampa y la via 111',1550701111) ; 
INSERT INTO empleado VALUES (14, 'ivan', 'navi', 'pringles 12',1234455) ;
 
ROLLBACK TO insert_01;
RELEASE SAVEPOINT insert_01; 

COMMIT; 

SET @@AUTOCOMMIT =1;

-- FUNCTIONS

CREATE FUNCTION `modificar_stock` (idProducto INT, stockNuevo INT)
RETURNS INTEGER
DETERMINISTIC 
BEGIN

 UPDATE producto SET producto.stock = stockNuevo WHERE producto.idProducto = idProducto 

RETURN 1;

END // 


-- VISTAS

CREATE OR REPLACE VIEW vw_percusion AS
SELECT * FROM PRODUCTO 
WHERE idCategoria = '2';


CREATE VIEW vw_stock_producto AS
SELECT * FROM producto
WHERE stock<=10;


CREATE VIEW vw_producto_proveedor AS
SELECT 	prod.idProducto,
		prod.producto,
		prov.razon_social as proveedor, 
		prod.stock 
FROM producto prod 
INNER JOIN proveedor prov ON prov.idProveedor = prod.idProducto;  


-- STORED PROCEDURES


DROP PROCEDURE IF EXISTS sp_agregar_cliente;
USE music_store
DELIMITER //
CREATE PROCEDURE  sp_agregar_cliente(id INT, nombres VARCHAR(20), apellidos VARCHAR(15), direccion VARCHAR (100), 
telefono INT)
BEGIN
	
    INSERT INTO cliente (idCliente, Nombres, Apellidos, Direccion, Telefono)
	VALUES (NULL, nombres, apellidos, direccion, telefono);
    
END //

-- DCL CREACION DE USUARIOS Y PERMISOS

CREATE USER IF NOT EXISTS 'user01'@'localhost' IDENTIFIED BY 'user01ABC' ;
CREATE USER IF NOT EXISTS 'user02'@'localhost' IDENTIFIED BY 'user02ABC' ;

GRANT SELECT ON MUSIC_STORE.* TO 'user01'@'localhost' ;

FLUSH PRIVILEGES;

GRANT SELECT, INSERT, UPDATE ON MUSIC_STORE.* TO 'user02'@'localhost' ;


