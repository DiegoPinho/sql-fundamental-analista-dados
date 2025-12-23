# Exercícios - Capítulo 13: Subconsultas - Consultas dentro de consultas

## Exercício 1: Subconsulta básica com IN
Você tem estas tabelas:

```sql
CREATE TABLE departamentos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL
);

CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  departamento_id INTEGER REFERENCES departamentos(id),
  salario DECIMAL(10, 2)
);

INSERT INTO departamentos (nome)
VALUES ('TI'), ('Vendas'), ('RH'), ('Marketing');

INSERT INTO funcionarios (nome, departamento_id, salario)
VALUES 
  ('Alice', 1, 5000.00),
  ('Bob', 2, 4000.00),
  ('Carol', 1, 5500.00),
  ('David', 3, 3500.00),
  ('Elena', 2, 4500.00);
```

Escreva uma consulta que liste todos os funcionários que trabalham nos departamentos de 'TI' ou 'Vendas', usando uma subconsulta.

---

## Exercício 2: Subconsulta com NOT IN

Usando as tabelas do exercício anterior, encontre todos os funcionários que NÃO trabalham no departamento de RH.

---

## Exercício 3: Subconsulta retornando valor único

Encontre todos os funcionários que ganham mais que a média salarial da empresa.

---

## Exercício 4: Subconsulta com MAX

Encontre o(s) funcionário(s) com o maior salário da empresa.

---

## Exercício 5: Subconsulta no SELECT

Liste todos os departamentos mostrando quantos funcionários cada um tem, usando uma subconsulta na lista de colunas do SELECT.

---

## Exercício 6: Subconsulta correlacionada

Para cada funcionário, mostre seu nome, salário e a diferença entre seu salário e a média salarial do seu departamento.

---

## Exercício 7: EXISTS - Verificando existência

Encontre todos os departamentos que TÊM pelo menos um funcionário, usando `EXISTS`.

---

## Exercício 8: NOT EXISTS - Encontrando ausências

Encontre todos os departamentos que NÃO TÊM funcionários, usando `NOT EXISTS`.

---

## Exercício 9: Subconsulta no FROM (tabela derivada)

Crie um relatório que mostre apenas os departamentos com salário médio acima de 4000, usando uma subconsulta no `FROM`.

---

## Exercício 10: Comparando subconsulta vs JOIN

Escreva a mesma consulta de duas formas diferentes: uma usando subconsulta com `IN` e outra usando `INNER JOIN`.

Consulta: Liste nomes de funcionários que trabalham em departamentos cujo nome começa com 'T' ou 'V'.

---

## Exercício 11: Subconsultas aninhadas

Encontre funcionários que ganham mais que a média salarial do departamento de TI.

---

## Exercício 12: ALL e ANY com subconsultas

Crie duas tabelas de produtos:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  preco DECIMAL(10, 2)
);

INSERT INTO produtos (nome, preco)
VALUES 
  ('Notebook', 3000.00),
  ('Mouse', 50.00),
  ('Teclado', 150.00),
  ('Monitor', 1200.00);
```

a) Encontre produtos mais caros que TODOS os produtos que custam menos de 200.
b) Encontre produtos mais caros que ALGUM produto que custa menos de 200.

---

## Exercício 13: Subconsulta para ranking

Para cada funcionário, mostre seu nome, salário e sua posição no ranking salarial (1 = maior salário).

---

## Exercício 14: Subconsulta para encontrar duplicatas

Crie uma tabela de vendas:

```sql
CREATE TABLE vendas (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100),
  quantidade INTEGER,
  data_venda DATE
);

INSERT INTO vendas (produto, quantidade, data_venda)
VALUES 
  ('Notebook', 1, '2024-12-01'),
  ('Mouse', 5, '2024-12-01'),
  ('Notebook', 1, '2024-12-01'),  -- Duplicata
  ('Teclado', 2, '2024-12-02');
```

Encontre produtos que foram vendidos mais de uma vez na mesma data.

---

## Exercício 15: EXISTS vs IN - Performance

Compare estas três formas de encontrar departamentos que têm funcionários:

```sql
-- Forma A: Usando IN
SELECT nome FROM departamentos
WHERE id IN (SELECT departamento_id FROM funcionarios);

-- Forma B: Usando EXISTS
SELECT nome FROM departamentos d
WHERE EXISTS (
  SELECT 1 FROM funcionarios f 
  WHERE f.departamento_id = d.id
);

-- Forma C: Usando JOIN + DISTINCT
SELECT DISTINCT d.nome
FROM departamentos d
INNER JOIN funcionarios f ON d.id = f.departamento_id;
```

Qual você usaria e por quê?

---

## Exercício 16: Subconsulta com UPDATE

Dê um aumento de 10% para todos os funcionários que ganham menos que a média salarial da empresa, usando uma subconsulta.

---

## Exercício 17: Subconsulta com DELETE

Delete todos os funcionários de departamentos que não têm nenhum outro funcionário (departamentos com apenas 1 funcionário).

---

## Exercício 18: Subconsulta em INSERT

Crie uma tabela de histórico salarial e insira nela todos os funcionários que ganham acima da média:

```sql
CREATE TABLE historico_salarios_altos (
  id SERIAL PRIMARY KEY,
  funcionario_nome VARCHAR(100),
  salario DECIMAL(10, 2),
  data_registro DATE DEFAULT CURRENT_DATE
);
```

---

## Exercício 19: Subconsulta complexa - Segundo maior salário

Encontre o segundo maior salário da empresa (sem usar LIMIT ou OFFSET).

---

## Exercício 20: Questão conceitual - Quando usar subconsulta?

Para cada cenário abaixo, indique se você usaria subconsulta ou JOIN, e justifique:

a) Listar produtos e suas categorias
b) Verificar se existem pedidos sem cliente
c) Calcular o total de vendas por vendedor
d) Encontrar clientes que nunca fizeram pedidos
e) Mostrar para cada produto quantas vezes foi vendido

---

## Exercício Desafio: Sistema de recomendações

Você está construindo um sistema de recomendação para uma livraria online:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  cidade VARCHAR(50)
);

CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200),
  genero VARCHAR(50),
  preco DECIMAL(10, 2)
);

CREATE TABLE compras (
  id SERIAL PRIMARY KEY,
  cliente_id INTEGER REFERENCES clientes(id),
  livro_id INTEGER REFERENCES livros(id),
  data_compra DATE,
  avaliacao INTEGER CHECK (avaliacao BETWEEN 1 AND 5)
);

INSERT INTO clientes (nome, cidade)
VALUES 
  ('Ana', 'São Paulo'),
  ('Bruno', 'Rio de Janeiro'),
  ('Carla', 'São Paulo'),
  ('Diego', 'Belo Horizonte');

INSERT INTO livros (titulo, genero, preco)
VALUES 
  ('1984', 'Ficção', 35.00),
  ('Sapiens', 'História', 45.00),
  ('O Hobbit', 'Fantasia', 40.00),
  ('Cosmos', 'Ciência', 50.00),
  ('Neuromancer', 'Ficção', 38.00),
  ('Clean Code', 'Tecnologia', 55.00);

INSERT INTO compras (cliente_id, livro_id, data_compra, avaliacao)
VALUES 
  (1, 1, '2024-01-15', 5),  -- Ana comprou 1984 (Ficção)
  (1, 2, '2024-02-10', 4),  -- Ana comprou Sapiens (História)
  (2, 1, '2024-01-20', 5),  -- Bruno comprou 1984 (Ficção)
  (2, 3, '2024-02-15', 4),  -- Bruno comprou O Hobbit (Fantasia)
  (3, 2, '2024-01-25', 3),  -- Carla comprou Sapiens (História)
  (3, 4, '2024-03-01', 5);  -- Carla comprou Cosmos (Ciência)
```

**Desafio:** Crie uma consulta complexa que recomende livros para cada cliente baseado nestas regras:

1. Não recomendar livros que o cliente já comprou
2. Recomendar livros do mesmo gênero que o cliente comprou e avaliou com 4 ou 5 estrelas
3. Recomendar livros comprados por outros clientes da mesma cidade
4. Mostrar: nome do cliente, livro recomendado, motivo da recomendação, preço
