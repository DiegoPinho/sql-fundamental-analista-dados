# Exercícios - Capítulo 03: Inserções (INSERT)

## Exercício 1: Primeira inserção

Considerando a tabela `produtos` criada abaixo:

```sql
CREATE TABLE produtos (
  id INT,
  nome VARCHAR(100),
  preco DECIMAL(10, 2),
  estoque INT,
  descricao TEXT
);
```

Insira um produto com os seguintes dados:
- id: 1
- nome: "Notebook"
- preco: 3500.00
- estoque: 15
- descricao: "Notebook para uso profissional"

---

## Exercício 2: Ordem diferente das colunas

Usando a mesma tabela `produtos`, insira um novo produto alterando a ordem das colunas. Use os seguintes dados:
- nome: "Mouse"
- descricao: "Mouse sem fio"
- preco: 45.90
- id: 2
- estoque: 50

---

## Exercício 3: Identificando o erro de formato de data

O comando abaixo tentará inserir dados na tabela `vendas`:

```sql
CREATE TABLE vendas (
  id INT,
  data_venda DATE,
  produto VARCHAR(100),
  quantidade INT
);

INSERT INTO vendas (id, data_venda, produto, quantidade)
VALUES (1, '25-12-2023', 'Teclado', 3);
```

Por que este comando resultará em erro? Como corrigi-lo?


---

## Exercício 4: Erro de número de colunas

Identifique e corrija o erro no comando SQL abaixo:

```sql
CREATE TABLE funcionarios (
  id INT,
  nome VARCHAR(150),
  cargo VARCHAR(80),
  salario DECIMAL(10, 2)
);

INSERT INTO funcionarios (id, nome, cargo)
VALUES (1, 'Maria Silva', 'Analista de Dados', 5500.00, 'Setor Financeiro');
```

---

## Exercício 5: Múltiplas inserções

Usando a tabela `clientes` abaixo, insira 3 clientes de uma só vez:

```sql
CREATE TABLE clientes (
  id INT,
  nome VARCHAR(200),
  email VARCHAR(255),
  telefone VARCHAR(15),
  cidade VARCHAR(100)
);
```

Dados dos clientes:
1. Id: 1, Nome: "João Santos", Email: "joao@email.com", Telefone: "11987654321", Cidade: "São Paulo"
2. Id: 2, Nome: "Ana Costa", Email: "ana@email.com", Telefone: "21976543210", Cidade: "Rio de Janeiro"
3. Id: 3, Nome: "Carlos Pereira", Email: "carlos@email.com", Telefone: "31965432109", Cidade: "Belo Horizonte"

---

## Exercício 6: Comportamento atômico

Analise o comando abaixo e responda: quantos registros serão inseridos na tabela `pedidos`?

```sql
CREATE TABLE pedidos (
  id INT,
  data_pedido DATE,
  cliente VARCHAR(100),
  valor DECIMAL(10, 2)
);

INSERT INTO pedidos (id, data_pedido, cliente, valor)
VALUES
  (1, '2023-11-01', 'Empresa A', 1500.00),
  (2, '2023-32-15', 'Empresa B', 2300.00),
  (3, '2023-11-20', 'Empresa C', 1800.00);
```

---

## Exercício 7: Usando SERIAL

Crie uma tabela chamada `tarefas` com auto-incremento no id e insira duas tarefas SEM especificar o id manualmente:

Estrutura da tabela:
- id: deve ser auto-incrementado
- titulo: VARCHAR(200)
- status: VARCHAR(50)
- data_criacao: DATE

Tarefas a inserir:
1. Titulo: "Estudar SQL", Status: "Pendente", Data: "2023-12-01"
2. Titulo: "Fazer exercícios", Status: "Em andamento", Data: "2023-12-02"

---

## Exercício 8: RETURNING clause

Insira um novo livro na tabela abaixo e retorne o id que foi gerado automaticamente:

```sql
CREATE TABLE livros (
  id SERIAL,
  titulo VARCHAR(255),
  autor VARCHAR(150),
  ano_publicacao INT
);
```

Dados do livro:
- Titulo: "Banco de Dados Relacionais"
- Autor: "Carlos Silva"
- Ano: 2023

---

## Exercício 9: Inserção completa de dados

Crie uma tabela `eventos` e insira 4 eventos usando uma única operação INSERT:

Estrutura:
- id: SERIAL
- nome_evento: VARCHAR(255)
- data_evento: DATE
- local: VARCHAR(200)
- capacidade: INT
- confirmado: BOOLEAN

Eventos:
1. "Workshop de SQL", "2024-01-15", "Auditório Principal", 100, true
2. "Palestra sobre Data Science", "2024-01-20", "Sala 201", 50, true
3. "Meetup de Tecnologia", "2024-02-05", "Espaço Coworking", 80, false
4. "Hackathon 2024", "2024-02-28", "Centro de Convenções", 200, true

---

## Exercício 10: Corrigindo múltiplos erros

O código abaixo contém vários erros. Identifique todos eles e forneça a versão corrigida:

```sql
CREATE TABLE cursos (
  id SERIAL,
  nome VARCHAR(150),
  duracao_horas INT,
  data_inicio DATE,
  ativo BOOLEAN
);

INSERT INTO cursos (nome, duracao_horas, data_inicio)
VALUES
  ('SQL Básico', 40, '15-01-2024', true),
  ('Python Avançado', 60, '2024-01-20', false),
  'JavaScript Intermediário', 50, '2024-02-01', true;
```

---

## Exercício 11: Desafio - Sistema de biblioteca

Crie um sistema completo para gerenciar empréstimos de livros em uma biblioteca:

1. Crie uma tabela `emprestimos` com:
   - id (auto-incrementado)
   - livro (VARCHAR 200)
   - usuario (VARCHAR 150)
   - data_emprestimo (DATE)
   - data_devolucao (DATE)
   - devolvido (BOOLEAN)

2. Insira 5 empréstimos de uma só vez

3. Use RETURNING para mostrar todos os ids gerados

---

## Exercício 12: Questão conceitual

Por que é recomendado usar o tipo `SERIAL` para colunas de identificação (id) em vez de gerenciar manualmente os valores inteiros? Cite pelo menos três vantagens.