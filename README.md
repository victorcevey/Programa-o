CREATE DATABASE empresa_vendas;
USE empresa_vendas;
-- Criar tabela de clientes
CREATE TABLE clientes (
 id INT AUTO_INCREMENT PRIMARY KEY,
 nome VARCHAR(100) NOT NULL,
 cidade VARCHAR(50),
 estado VARCHAR(2)
);
-- Criar tabela de produtos
CREATE TABLE produtos (
 id INT AUTO_INCREMENT PRIMARY KEY,
 nome VARCHAR(100) NOT NULL,
 preco DECIMAL(10,2) NOT NULL,
 categoria VARCHAR(50)
);
-- Criar tabela de vendas
CREATE TABLE vendas (
 id INT AUTO_INCREMENT PRIMARY KEY,
 cliente_id INT,
 produto_id INT,
 quantidade INT NOT NULL,
 data_venda DATE NOT NULL,
 FOREIGN KEY (cliente_id) REFERENCES clientes(id),
 FOREIGN KEY (produto_id) REFERENCES produtos(id)
);
-- Inserir dados de exemplo
INSERT INTO clientes (nome, cidade, estado) VALUES
('João Silva', 'São Paulo', 'SP'),
('Maria Santos', 'Rio de Janeiro', 'RJ'),
('Carlos Oliveira', 'Belo Horizonte', 'MG'),
('Ana Costa', 'São Paulo', 'SP'),
('Pedro Alves', 'Curitiba', 'PR');
INSERT INTO produtos (nome, preco, categoria) VALUES
('Notebook', 3500.00, 'Eletrônicos'),
('Smartphone', 1500.00, 'Eletrônicos'),
('Mesa de Escritório', 800.00, 'Móveis'),
('Cadeira', 450.00, 'Móveis'),
('Monitor', 1200.00, 'Eletrônicos');

INSERT INTO vendas (cliente_id, produto_id, quantidade, data_venda) VALUES
(1, 1, 1, '2023-01-15'),
(1, 2, 2, '2023-01-20'),
(2, 3, 1, '2023-02-10'),
(3, 4, 4, '2023-02-15'),
(4, 5, 2, '2023-03-05'),
(5, 1, 1, '2023-03-10'),
(2, 2, 1, '2023-03-15'),
(3, 3, 2, '2023-04-01'),
(4, 4, 3, '2023-04-10'),
(5, 5, 1, '2023-05-05');

SELECT AVG(preco) AS media_preco
FROM produtos;

SELECT COUNT(*) AS total_clientes
FROM clientes;

SELECT SUM(quantidade) AS total_produtos_vendidos
FROM vendas;

SELECT estado, COUNT(*) AS quantidade_clientes
FROM clientes
GROUP BY estado;

SELECT p.nome, SUM(v.quantidade * p.preco) AS valor_total_vendas
FROM vendas v
JOIN produtos p ON v.produto_id = p.id
GROUP BY p.nome;

SELECT AVG(quantidade) AS media_quantidade
FROM vendas;

SELECT c.nome, SUM(v.quantidade * p.preco) AS total_gasto
FROM vendas v
JOIN clientes c ON v.cliente_id = c.id
JOIN produtos p ON v.produto_id = p.id
GROUP BY c.nome;

SELECT c.nome, AVG(v.quantidade * p.preco) AS media_gasto
FROM vendas v
JOIN clientes c ON v.cliente_id = c.id
JOIN produtos p ON v.produto_id = p.id
GROUP BY c.nome;

SELECT c.nome, COUNT(DISTINCT v.produto_id) AS produtos_diferentes
FROM vendas v
JOIN clientes c ON v.cliente_id = c.id
GROUP BY c.nome;

SELECT p.categoria, SUM(v.quantidade * p.preco) AS total_vendas
FROM vendas v
JOIN produtos p ON v.produto_id = p.id
GROUP BY p.categoria
HAVING total_vendas > 2000;

SELECT c.nome, AVG(v.quantidade * p.preco) AS media_compra
FROM vendas v
JOIN clientes c ON v.cliente_id = c.id
JOIN produtos p ON v.produto_id = p.id
GROUP BY c.nome
HAVING media_compra > 1000;

SELECT p.nome, SUM(v.quantidade) AS total_vendido
FROM vendas v
JOIN produtos p ON v.produto_id = p.id
GROUP BY p.nome
HAVING total_vendido > 3;

SELECT
 MONTH(data_venda) AS mes,
 AVG(quantidade * preco) AS media_vendas
FROM vendas v
JOIN produtos p ON v.produto_id = p.id
GROUP BY mes
ORDER BY mes;

SELECT c.nome, SUM(v.quantidade * p.preco) AS total_gasto
FROM vendas v
JOIN clientes c ON v.cliente_id = c.id
JOIN produtos p ON v.produto_id = p.id
GROUP BY c.nome
ORDER BY total_gasto DESC
LIMIT 1;

SELECT
 p.categoria,
 SUM(v.quantidade * p.preco) AS total_categoria,
 (SUM(v.quantidade * p.preco) / (SELECT SUM(quantidade * preco) FROM
 vendas v JOIN produtos p ON v.produto_id = p.id)) * 100 AS porcentagem
FROM vendas v
JOIN produtos p ON v.produto_id = p.id
GROUP BY p.categoria;

SELECT v.id AS venda_id, c.nome AS cliente, p.nome AS produto, v.quantidade, v.data_venda
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id
INNER JOIN produtos p ON v.produto_id = p.id;

SELECT p.nome AS produto, p.preco, p.categoria, v.quantidade
FROM vendas v
INNER JOIN produtos p ON v.produto_id = p.id;

SELECT v.id AS venda_id, c.nome AS cliente, p.nome AS produto
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id AND c.cidade = 'São Paulo
'
INNER JOIN produtos p ON v.produto_id = p.id;

SELECT v.id AS venda_id, c.nome AS cliente, p.nome AS produto,
 v.quantidade * p.preco AS valor_total
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id
INNER JOIN produtos p ON v.produto_id = p.id
WHERE v.quantidade * p.preco > 1000;

SELECT DISTINCT c.nome AS cliente, p.categoria
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id
INNER JOIN produtos p ON v.produto_id = p.id
WHERE p.categoria = 'Eletrônicos';

SELECT v.data_venda, c.nome AS cliente, p.nome AS produto, v.quantidade
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id
INNER JOIN produtos p ON v.produto_id = p.id
WHERE v.data_venda BETWEEN '2023-01-01' AND '2023-03-31';

SELECT c.nome AS cliente, p.nome AS produto, v.quantidade
FROM clientes c
LEFT JOIN vendas v ON c.id = v.cliente_id
LEFT JOIN produtos p ON v.produto_id = p.id;

SELECT p.nome AS produto, v.quantidade, v.data_venda
FROM produtos p
LEFT JOIN vendas v ON p.id = v.produto_id;

SELECT c.nome AS cliente, p.nome AS produto, v.data_venda,
 v.quantidade * p.preco AS valor_total
FROM clientes c
LEFT JOIN vendas v ON c.id = v.cliente_id
LEFT JOIN produtos p ON v.produto_id = p.id;

SELECT c.nome AS cliente, SUM(v.quantidade * p.preco) AS total_gasto
FROM clientes c
INNER JOIN vendas v ON c.id = v.cliente_id
INNER JOIN produtos p ON v.produto_id = p.id
GROUP BY c.nome;

SELECT p.nome AS produto, SUM(v.quantidade) AS total_vendido
FROM produtos p
INNER JOIN vendas v ON p.id = v.produto_id
GROUP BY p.nome
ORDER BY total_vendido DESC
LIMIT 1;

SELECT c.nome AS cliente, COUNT(DISTINCT v.produto_id) AS produtos_diferentes
FROM clientes c
INNER JOIN vendas v ON c.id = v.cliente_id
GROUP BY c.nome
HAVING produtos_diferentes > 1;

SELECT p.nome AS produto_nao_vendido
FROM produtos p
LEFT JOIN vendas v ON p.id = v.produto_id
WHERE v.id IS NULL;

SELECT c.estado, SUM(v.quantidade * p.preco) AS total_vendas
FROM vendas v
INNER JOIN clientes c ON v.cliente_id = c.id
INNER JOIN produtos p ON v.produto_id = p.id
GROUP BY c.estado;

SELECT c.nome
FROM clientes c
WHERE NOT EXISTS (
 SELECT p.id
 FROM produtos p
 WHERE p.categoria = 'Eletrônicos'
 AND NOT EXISTS (
 SELECT 1
 FROM vendas v
 WHERE v.cliente_id = c.id AND v.produto_id = p.id
 )
);

SELECT p.nome
FROM produtos p
WHERE NOT EXISTS (
 SELECT DISTINCT c.estado
 FROM clientes c
 WHERE NOT EXISTS (
 SELECT 1
 FROM vendas v
 WHERE v.produto_id = p.id AND v.cliente_id = c.id
 )
);

SELECT c.nome
FROM clientes c
WHERE NOT EXISTS (
 SELECT DISTINCT MONTH(v.data_venda) AS mes
 FROM vendas v
 WHERE YEAR(v.data_venda) = 2023
 AND NOT EXISTS (
 SELECT 1
 FROM vendas v2
 WHERE v2.cliente_id = c.id
 AND MONTH(v2.data_venda) = mes
 AND YEAR(v2.data_venda) = 2023
 )
);
