# Exercícios - Capítulo 07: Operações de Busca (SELECT)

## Exercício 1: SELECT básico

Dada a tabela `funcionarios`:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  cargo VARCHAR(80) NOT NULL,
  salario DECIMAL(10, 2) NOT NULL,
  departamento VARCHAR(100) NOT NULL
);

INSERT INTO funcionarios (nome, cargo, salario, departamento)
VALUES 
  ('Ana Silva', 'Analista', 5000.00, 'TI'),
  ('Bruno Costa', 'Gerente', 8000.00, 'Vendas'),
  ('Carla Souza', 'Analista', 5500.00, 'TI'),
  ('Diego Lima', 'Assistente', 3000.00, 'RH');
```

Escreva consultas SELECT para:

a) Buscar todos os dados de todos os funcionários  
b) Buscar apenas os nomes dos funcionários  
c) Buscar nome e salário de todos os funcionários

---

## Exercício 2: Ordem das colunas no SELECT

Usando a tabela `funcionarios` do exercício anterior, mostre como a ordem das colunas no SELECT afeta o resultado.

Escreva duas consultas:
a) Selecione salario, nome, cargo (nesta ordem)  
b) Selecione cargo, departamento, nome (nesta ordem)

---

## Exercício 3: Usando AS para renomear colunas

A tabela `produtos` tem nomes de colunas pouco descritivos:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nm VARCHAR(100) NOT NULL,
  prc DECIMAL(10, 2) NOT NULL,
  qtd INT NOT NULL
);

INSERT INTO produtos (nm, prc, qtd)
VALUES 
  ('Notebook', 3500.00, 15),
  ('Mouse', 45.90, 100),
  ('Teclado', 120.00, 50);
```

Escreva uma consulta que:
- Renomeie `nm` para "Nome do Produto"
- Renomeie `prc` para "Preço (R$)"
- Renomeie `qtd` para "Quantidade em Estoque"

---

## Exercício 4: SELECT DISTINCT básico

Dada a tabela `vendas`:

```sql
CREATE TABLE vendas (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  vendedor VARCHAR(150) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  data_venda DATE NOT NULL
);

INSERT INTO vendas (produto, vendedor, valor, data_venda)
VALUES 
  ('Notebook', 'João', 3500.00, '2023-11-01'),
  ('Mouse', 'Maria', 45.90, '2023-11-02'),
  ('Notebook', 'João', 3500.00, '2023-11-03'),
  ('Teclado', 'Pedro', 120.00, '2023-11-04'),
  ('Mouse', 'Maria', 45.90, '2023-11-05');
```

a) Liste todos os produtos vendidos (com repetições)  
b) Liste todos os produtos vendidos sem repetições  
c) Liste todos os vendedores sem repetições

---

## Exercício 5: DISTINCT com múltiplas colunas

Usando a tabela `vendas` do exercício anterior, explique a diferença entre:

```sql
-- Consulta A
SELECT DISTINCT produto FROM vendas;

-- Consulta B
SELECT DISTINCT produto, vendedor FROM vendas;
```

Qual será o resultado de cada uma?

---

## Exercício 6: ORDER BY crescente e decrescente

Dada a tabela `alunos`:

```sql
CREATE TABLE alunos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  nota DECIMAL(5, 2) NOT NULL,
  idade INT NOT NULL
);

INSERT INTO alunos (nome, nota, idade)
VALUES 
  ('Alice', 8.5, 20),
  ('Bruno', 7.0, 22),
  ('Carla', 9.2, 19),
  ('Diego', 6.5, 21),
  ('Eva', 8.8, 20);
```

Escreva consultas para:

a) Listar alunos ordenados por nota (menor para maior)  
b) Listar alunos ordenados por nota (maior para menor)  
c) Listar alunos ordenados por nome (ordem alfabética)  
d) Listar alunos ordenados por idade (decrescente)

---

## Exercício 7: ORDER BY com múltiplas colunas

Usando a tabela `alunos` do exercício anterior, ordene os alunos:
- Primeiro por idade (decrescente)
- Depois por nota (decrescente)

Isso significa que alunos da mesma idade aparecerão ordenados por nota.

---

## Exercício 8: Combinando WHERE, DISTINCT e ORDER BY

Dada a tabela `pedidos`:

```sql
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  produto VARCHAR(100) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) NOT NULL,
  data_pedido DATE NOT NULL
);

INSERT INTO pedidos (cliente, produto, valor, status, data_pedido)
VALUES 
  ('Empresa A', 'Notebook', 3500.00, 'Concluído', '2023-11-01'),
  ('Empresa B', 'Mouse', 45.90, 'Pendente', '2023-11-02'),
  ('Empresa A', 'Teclado', 120.00, 'Concluído', '2023-11-03'),
  ('Empresa C', 'Monitor', 800.00, 'Concluído', '2023-11-04'),
  ('Empresa B', 'Notebook', 3500.00, 'Cancelado', '2023-11-05');
```

Escreva uma consulta que retorne:
- Apenas clientes únicos
- Que fizeram pedidos com status 'Concluído'
- Ordenados alfabeticamente

---

## Exercício 9: Ordenação com valores NULL

Dada a tabela `tarefas`:

```sql
CREATE TABLE tarefas (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  data_conclusao DATE,
  prioridade INT
);

INSERT INTO tarefas (titulo, data_conclusao, prioridade)
VALUES 
  ('Tarefa A', '2023-12-01', 1),
  ('Tarefa B', NULL, 2),
  ('Tarefa C', '2023-11-15', 3),
  ('Tarefa D', NULL, 1),
  ('Tarefa E', '2023-12-10', 2);
```

a) Ordene por data_conclusao (crescente). Onde aparecerão os valores NULL?  
b) Ordene por data_conclusao (decrescente). Onde aparecerão os valores NULL?  
c) Ordene por data_conclusao crescente, masforce os NULL a aparecerem primeiro

---

## Exercício 10: NULLS FIRST e NULLS LAST

Usando a tabela `tarefas` do exercício anterior, escreva consultas que:

a) Ordenem por prioridade crescente com NULL no final  
b) Ordenem por prioridade decrescente com NULL no final


---

## Exercício 11: SELECT com cálculos

Dada a tabela `produtos_loja`:

```sql
CREATE TABLE produtos_loja (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco_custo DECIMAL(10, 2) NOT NULL,
  preco_venda DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL
);

INSERT INTO produtos_loja (nome, preco_custo, preco_venda, estoque)
VALUES 
  ('Notebook', 2500.00, 3500.00, 10),
  ('Mouse', 20.00, 45.90, 100),
  ('Teclado', 60.00, 120.00, 50);
```

Escreva uma consulta que mostre:
- Nome do produto
- Margem de lucro (preco_venda - preco_custo) com alias "Lucro por Unidade"
- Valor total em estoque (preco_venda * estoque) com alias "Valor Total"
- Ordenado por margem de lucro (decrescente)

---

## Exercício 12: Questão conceitual - SELECT *

Explique por que usar `SELECT *` pode ser problemático em produção, mesmo sendo conveniente durante o desenvolvimento. Cite pelo menos três motivos.

---

## Exercício 13: Consulta complexa combinada

Dada a tabela `livros`:

```sql
CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(255) NOT NULL,
  autor VARCHAR(150) NOT NULL,
  genero VARCHAR(100) NOT NULL,
  ano_publicacao INT,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL
);

INSERT INTO livros (titulo, autor, genero, ano_publicacao, preco, estoque)
VALUES 
  ('1984', 'George Orwell', 'Ficção', 1949, 35.00, 10),
  ('Dom Casmurro', 'Machado de Assis', 'Romance', 1899, 28.00, 5),
  ('O Cortiço', 'Aluísio Azevedo', 'Romance', NULL, 32.00, 8),
  ('Fundação', 'Isaac Asimov', 'Ficção', 1951, 42.00, 15),
  ('1984', 'George Orwell', 'Ficção', 1949, 35.00, 3);
```

Crie uma consulta que:
1. Mostre apenas títulos e autores únicos (elimine duplicatas)
2. Apenas de livros do gênero 'Ficção'
3. Com preço maior que 30.00
4. Ordenados por título alfabeticamente
5. Renomeie as colunas para "Título do Livro" e "Nome do Autor"

---

## Exercício 14: Desafio - Análise de vendas

Dada a tabela `transacoes`:

```sql
CREATE TABLE transacoes (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  categoria VARCHAR(50) NOT NULL,
  vendedor VARCHAR(150) NOT NULL,
  quantidade INT NOT NULL,
  preco_unitario DECIMAL(10, 2) NOT NULL,
  data_venda DATE NOT NULL
);

INSERT INTO transacoes (produto, categoria, vendedor, quantidade, preco_unitario, data_venda)
VALUES 
  ('Notebook', 'Eletrônicos', 'João', 2, 3500.00, '2023-11-01'),
  ('Mouse', 'Periféricos', 'Maria', 5, 45.90, '2023-11-02'),
  ('Notebook', 'Eletrônicos', 'João', 1, 3500.00, '2023-11-03'),
  ('Teclado', 'Periféricos', 'Pedro', 3, 120.00, '2023-11-04'),
  ('Mouse', 'Periféricos', 'Maria', 10, 45.90, '2023-11-05'),
  ('Monitor', 'Eletrônicos', 'Ana', 2, 800.00, '2023-11-06');
```

Crie uma consulta que mostre:
1. Categorias únicas de produtos
2. Valor total vendido por categoria (quantidade * preco_unitario, renomeado como "Total por Categoria")
3. Apenas categorias com total vendido acima de 500.00
4. Ordenadas por valor total (decrescente)

**Dica**: Você precisará usar conceitos que ainda não vimos (agregação), então tente fazer o máximo que conseguir com o que aprendeu.

---

## Exercício 15: SELECT com filtros em texto

Dada a tabela `clientes`:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL,
  cidade VARCHAR(100) NOT NULL,
  estado VARCHAR(2) NOT NULL
);

INSERT INTO clientes (nome, email, cidade, estado)
VALUES 
  ('Ana Silva', 'ana@email.com', 'São Paulo', 'SP'),
  ('Bruno Costa', 'bruno@email.com', 'Rio de Janeiro', 'RJ'),
  ('Carla Santos', 'carla@email.com', 'São Paulo', 'SP'),
  ('Diego Alves', 'diego@email.com', 'Belo Horizonte', 'MG'),
  ('Eva Lima', 'eva@email.com', 'São Paulo', 'SP');
```

Escreva consultas para:

a) Listar cidades únicas onde há clientes  
b) Listar estados únicos ordenados alfabeticamente  
c) Listar nomes de clientes de São Paulo, ordenados alfabeticamente

---

## Exercício 16: Entendendo a ordem de execução

Explique em que ordem o PostgreSQL processa as cláusulas do comando abaixo:

```sql
SELECT DISTINCT categoria, preco
FROM produtos
WHERE preco > 100.00
ORDER BY categoria ASC;
```

---

## Exercício 17: Prática final integrada

Você é analista de dados de uma empresa de e-commerce. Crie consultas para responder às perguntas abaixo:

```sql
CREATE TABLE pedidos_ecommerce (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  produto VARCHAR(100) NOT NULL,
  categoria VARCHAR(50) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  desconto DECIMAL(10, 2) DEFAULT 0,
  status VARCHAR(50) NOT NULL,
  data_pedido DATE NOT NULL
);

INSERT INTO pedidos_ecommerce (cliente, produto, categoria, valor, desconto, status, data_pedido)
VALUES 
  ('Empresa A', 'Notebook', 'Eletrônicos', 3500.00, 200.00, 'Entregue', '2023-11-01'),
  ('Empresa B', 'Mouse', 'Periféricos', 45.90, 0, 'Entregue', '2023-11-02'),
  ('Empresa A', 'Teclado', 'Periféricos', 120.00, 10.00, 'Entregue', '2023-11-03'),
  ('Empresa C', 'Monitor', 'Eletrônicos', 800.00, 50.00, 'Cancelado', '2023-11-04'),
  ('Empresa B', 'Notebook', 'Eletrônicos', 3500.00, 150.00, 'Entregue', '2023-11-05'),
  ('Empresa D', 'Mouse', 'Periféricos', 45.90, 0, 'Entregue', '2023-11-06');
```

Perguntas:

a) Quais categorias de produtos foram vendidas (sem repetição)?  
b) Quais clientes fizeram pedidos entregues (sem repetição), ordenados alfabeticamente?  
c) Liste produto, valor e valor final (valor - desconto) dos pedidos entregues, ordenados por valor final decrescente  
d) Quais produtos únicos foram cancelados?
