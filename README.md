CREATE DATABASE orbita_alfa;

USE orbita_alfa;

CREATE TABLE lotes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    descripcion VARCHAR(100),
    puja_actual INT,
    lider VARCHAR(50)
);

CREATE TABLE comerciantes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    saldo INT
);

INSERT INTO lotes(descripcion, puja_actual, lider)
VALUES
('Restos de Fuselaje de Titanio', 500, 'Sin líder'),
('Nucleo de Hiperpropulsor Dañado', 1200, 'Sin líder');

INSERT INTO comerciantes(nombre, saldo)
VALUES
('HanSolo', 2000),
('Watto', 800),
('Nebula9', 5000);
