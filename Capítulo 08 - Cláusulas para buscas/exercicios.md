# Exercícios - Capítulo 08: Cláusulas para Buscas

## Exercício 1: WHERE com operadores de comparação

Dada a tabela `produtos`:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL,
  categoria VARCHAR(50) NOT NULL
);

INSERT INTO produtos (nome, preco, estoque, categoria)
VALUES 
  ('Notebook', 3500.00, 15, 'Eletrônicos'),
  ('Mouse', 45.90, 100, 'Periféricos'),
  ('Teclado', 120.00, 50, 'Periféricos'),
  ('Monitor', 800.00, 20, 'Eletrônicos'),
  ('Webcam', 250.00, 30, 'Periféricos');
```

Escreva consultas para:

a) Produtos com preço maior que 200  
b) Produtos com estoque menor ou igual a 30  
c) Produtos que NÃO sejam da categoria 'Eletrônicos'  
d) Produtos com preço entre 100 e 300 (usando >= e <=)

---

## Exercício 2: Operador AND

Usando a tabela `produtos` do exercício anterior, escreva consultas para:

a) Produtos da categoria 'Periféricos' com preço menor que 100  
b) Produtos com estoque maior que 20 e preço menor que 1000  
c) Produtos da categoria 'Eletrônicos' com estoque maior que 10

---

## Exercício 3: Operador OR

Usando a tabela `produtos`, escreva consultas para:

a) Produtos que custam menos de 50 OU mais de 500  
b) Produtos da categoria 'Eletrônicos' OU com estoque menor que 20  
c) Produtos chamados 'Mouse' OU 'Teclado'

---

## Exercício 4: Combinando AND e OR

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
  ('Diego Lima', 'Assistente', 3000.00, 'RH'),
  ('Eva Santos', 'Gerente', 9000.00, 'TI');
```

Escreva uma consulta para funcionários que:
- Trabalham no departamento 'TI' E ganham mais de 5000
- OU são Gerentes de qualquer departamento

---

## Exercício 5: Operador NOT

Usando a tabela `funcionarios` do exercício anterior:

a) Liste funcionários que NÃO são Analistas  
b) Liste funcionários que NÃO trabalham em TI e NÃO são Assistentes  
c) Liste funcionários cujo salário NÃO está entre 4000 e 6000

---

## Exercício 6: BETWEEN

Dada a tabela `vendas`:

```sql
CREATE TABLE vendas (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  quantidade INT NOT NULL,
  preco_unitario DECIMAL(10, 2) NOT NULL,
  data_venda DATE NOT NULL
);

INSERT INTO vendas (produto, quantidade, preco_unitario, data_venda)
VALUES 
  ('Notebook', 2, 3500.00, '2023-11-01'),
  ('Mouse', 10, 45.90, '2023-11-05'),
  ('Teclado', 5, 120.00, '2023-11-10'),
  ('Monitor', 3, 800.00, '2023-11-15'),
  ('Webcam', 8, 250.00, '2023-11-20');
```

Escreva consultas para:

a) Vendas com quantidade entre 3 e 8 (inclusive)  
b) Vendas com preço unitário entre 100 e 500  
c) Vendas realizadas entre 05/11/2023 e 15/11/2023  
d) Vendas com quantidade NÃO entre 5 e 10

---

## Exercício 7: Operador IN

Dada a tabela `clientes`:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  cidade VARCHAR(100) NOT NULL,
  estado VARCHAR(2) NOT NULL,
  status VARCHAR(20) NOT NULL
);

INSERT INTO clientes (nome, cidade, estado, status)
VALUES 
  ('João Silva', 'São Paulo', 'SP', 'Ativo'),
  ('Maria Santos', 'Rio de Janeiro', 'RJ', 'Inativo'),
  ('Pedro Costa', 'Belo Horizonte', 'MG', 'Ativo'),
  ('Ana Oliveira', 'Curitiba', 'PR', 'Ativo'),
  ('Carlos Lima', 'Salvador', 'BA', 'Pendente');
```

Escreva consultas para:

a) Clientes dos estados SP, RJ ou MG  
b) Clientes com status 'Ativo' ou 'Pendente'  
c) Clientes que NÃO são de SP, RJ ou MG  
d) Clientes cujo nome está na lista: 'João Silva', 'Ana Oliveira', 'Carlos Lima'

---

## Exercício 8: Operador LIKE com %

Dada a tabela `livros`:

```sql
CREATE TABLE livros (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(255) NOT NULL,
  autor VARCHAR(150) NOT NULL,
  editora VARCHAR(100) NOT NULL
);

INSERT INTO livros (titulo, autor, editora)
VALUES 
  ('Aprendendo SQL', 'João Silva', 'TechBooks'),
  ('SQL Avançado', 'Maria Santos', 'DataPress'),
  ('Introdução ao Python', 'Pedro Costa', 'CodePublish'),
  ('SQL para Analistas', 'Ana Oliveira', 'TechBooks'),
  ('Programação em Java', 'Carlos Lima', 'JavaPress');
```

Escreva consultas para:

a) Livros cujo título contém a palavra 'SQL'  
b) Livros cujo título começa com 'SQL'  
c) Livros cujo título termina com 'Python'  
d) Autores cujo nome contém 'Silva'  
e) Livros da editora que contém 'Tech'

---

## Exercício 9: Operador LIKE com _ (underscore)

Dada a tabela `codigos`:

```sql
CREATE TABLE codigos (
  id SERIAL PRIMARY KEY,
  codigo VARCHAR(10) NOT NULL,
  descricao VARCHAR(100) NOT NULL
);

INSERT INTO codigos (codigo, descricao)
VALUES 
  ('A-001', 'Produto tipo A, série 1'),
  ('A-002', 'Produto tipo A, série 2'),
  ('B-001', 'Produto tipo B, série 1'),
  ('A-1234', 'Produto tipo A, série especial'),
  ('C-99', 'Produto tipo C');
```

Escreva consultas para:

a) Códigos que começam com 'A-00' seguido de exatamente 1 dígito  
b) Códigos que começam com qualquer letra, seguido de '-00' e exatamente 1 dígito  
c) Códigos que têm exatamente 5 caracteres totais

---

## Exercício 10: ILIKE (case-insensitive)

Dada a tabela `emails`:

```sql
CREATE TABLE emails (
  id SERIAL PRIMARY KEY,
  remetente VARCHAR(150) NOT NULL,
  assunto VARCHAR(255) NOT NULL
);

INSERT INTO emails (remetente, assunto)
VALUES 
  ('joao@empresa.com', 'URGENTE: Reunião amanhã'),
  ('maria@empresa.com', 'Relatório de vendas'),
  ('pedro@empresa.com', 'urgente: Aprovação necessária'),
  ('ana@empresa.com', 'Fatura do mês'),
  ('carlos@empresa.com', 'Urgente - Atualização de sistema');
```

a) Busque emails cujo assunto contenha 'urgente' (maiúscula ou minúscula) usando LIKE com LOWER  
b) Busque emails cujo assunto contenha 'urgente' usando ILIKE  
c) Qual a diferença entre as duas abordagens?

---

## Exercício 11: IS NULL e IS NOT NULL

Dada a tabela `tarefas`:

```sql
CREATE TABLE tarefas (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  responsavel VARCHAR(150),
  data_conclusao DATE,
  observacoes TEXT
);

INSERT INTO tarefas (titulo, responsavel, data_conclusao, observacoes)
VALUES 
  ('Tarefa 1', 'João', '2023-11-15', 'Concluída'),
  ('Tarefa 2', NULL, NULL, NULL),
  ('Tarefa 3', 'Maria', NULL, 'Em andamento'),
  ('Tarefa 4', 'Pedro', '2023-11-20', NULL),
  ('Tarefa 5', NULL, NULL, 'Aguardando atribuição');
```

Escreva consultas para:

a) Tarefas sem responsável atribuído  
b) Tarefas que já foram concluídas (têm data_conclusao)  
c) Tarefas sem observações  
d) Tarefas que têm responsável MAS ainda não foram concluídas

---

## Exercício 12: LIMIT básico

Dada a tabela `produtos_ranking`:

```sql
CREATE TABLE produtos_ranking (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  vendas_totais INT NOT NULL,
  avaliacao DECIMAL(3, 2) NOT NULL
);

INSERT INTO produtos_ranking (nome, vendas_totais, avaliacao)
VALUES 
  ('Produto A', 1500, 4.8),
  ('Produto B', 2300, 4.5),
  ('Produto C', 890, 4.9),
  ('Produto D', 3200, 4.2),
  ('Produto E', 1100, 4.7),
  ('Produto F', 2800, 4.6);
```

Escreva consultas para:

a) Os 3 produtos mais vendidos  
b) Os 2 produtos com melhor avaliação  
c) Os 4 primeiros produtos quando ordenados alfabeticamente  
d) Apenas o produto menos vendido

---

## Exercício 13: LIMIT com OFFSET (Paginação)

Usando a tabela `produtos_ranking` do exercício anterior, implemente uma paginação mostrando 2 produtos por página, ordenados por vendas (decrescente).

Escreva consultas para:

a) Página 1 (produtos 1 e 2)  
b) Página 2 (produtos 3 e 4)  
c) Página 3 (produtos 5 e 6)

---

## Exercício 14: Combinação complexa

Dada a tabela `pedidos_completos`:

```sql
CREATE TABLE pedidos_completos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  produto VARCHAR(100) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) NOT NULL,
  data_pedido DATE NOT NULL,
  observacoes TEXT
);

INSERT INTO pedidos_completos (cliente, produto, valor, status, data_pedido, observacoes)
VALUES 
  ('Empresa A', 'Notebook', 3500.00, 'Entregue', '2023-11-01', 'Cliente satisfeito'),
  ('Empresa B', 'Mouse', 45.90, 'Pendente', '2023-11-05', NULL),
  ('Empresa A', 'Teclado', 120.00, 'Entregue', '2023-11-08', 'Entrega rápida'),
  ('Empresa C', 'Monitor', 800.00, 'Cancelado', '2023-11-10', NULL),
  ('Empresa B', 'Webcam', 250.00, 'Entregue', '2023-11-12', NULL),
  ('Empresa D', 'Notebook', 3500.00, 'Pendente', '2023-11-15', 'Aguardando pagamento'),
  ('Empresa A', 'Mouse', 45.90, 'Entregue', '2023-11-18', 'Segunda compra');
```

Crie uma consulta que retorne:
- Pedidos da 'Empresa A' OU 'Empresa B'
- Com status 'Entregue' OU 'Pendente'
- Com valor maior que 50
- Que NÃO tenham observações
- Ordenados por data (mais recente primeiro)
- Limitado aos 3 primeiros resultados

---

## Exercício 15: Busca de padrões complexos

Dada a tabela `usuarios_sistema`:

```sql
CREATE TABLE usuarios_sistema (
  id SERIAL PRIMARY KEY,
  username VARCHAR(50) NOT NULL,
  email VARCHAR(255) NOT NULL,
  telefone VARCHAR(20),
  cargo VARCHAR(100)
);

INSERT INTO usuarios_sistema (username, email, telefone, cargo)
VALUES 
  ('joao.silva', 'joao.silva@empresa.com', '11-98765-4321', 'Desenvolvedor Senior'),
  ('maria_santos', 'maria@gmail.com', '21-99876-5432', 'Analista Junior'),
  ('pedro.costa', 'pedro.costa@empresa.com', NULL, 'Gerente de Projetos'),
  ('ana_oliveira', 'ana@outlook.com', '11-97654-3210', 'Desenvolvedor Pleno'),
  ('carlos.lima', 'carlos.lima@empresa.com', '31-96543-2109', NULL);
```

Escreva consultas para:

a) Usuários com email corporativo (contém @empresa.com)  
b) Usuários cujo username contém ponto (.)  
c) Desenvolvedores (cargo contém 'Desenvolvedor') que têm telefone cadastrado  
d) Usuários cujo cargo termina com 'Senior' OU 'Pleno'  
e) Top 2 usuários com email corporativo, ordenados por username

---

## Exercício 16: Questão conceitual - Ordem das cláusulas

Explique por que a consulta abaixo está ERRADA e corrija-a:

```sql
SELECT nome, preco 
LIMIT 5
FROM produtos 
WHERE preco > 100 
ORDER BY preco DESC;
```

---

## Exercício 17: Desafio final - Análise de vendas

Você é analista de dados de um e-commerce. Crie consultas para responder:

```sql
CREATE TABLE transacoes_ecommerce (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  produto VARCHAR(100) NOT NULL,
  categoria VARCHAR(50) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  quantidade INT NOT NULL,
  status VARCHAR(50) NOT NULL,
  data_compra DATE NOT NULL,
  avaliacao INT,
  comentario TEXT
);

INSERT INTO transacoes_ecommerce (cliente, produto, categoria, valor, quantidade, status, data_compra, avaliacao, comentario)
VALUES 
  ('João Silva', 'Notebook Dell', 'Eletrônicos', 3500.00, 1, 'Entregue', '2023-11-01', 5, 'Excelente produto'),
  ('Maria Santos', 'Mouse Logitech', 'Periféricos', 45.90, 2, 'Entregue', '2023-11-03', 4, NULL),
  ('João Silva', 'Teclado Mecânico', 'Periféricos', 350.00, 1, 'Entregue', '2023-11-05', NULL, NULL),
  ('Pedro Costa', 'Monitor LG', 'Eletrônicos', 800.00, 1, 'Cancelado', '2023-11-07', NULL, NULL),
  ('Ana Oliveira', 'Webcam HD', 'Periféricos', 250.00, 1, 'Entregue', '2023-11-10', 5, 'Ótima qualidade'),
  ('Maria Santos', 'Notebook Asus', 'Eletrônicos', 4200.00, 1, 'Entregue', '2023-11-12', 3, 'Veio com defeito'),
  ('Carlos Lima', 'Mouse Razer', 'Periféricos', 180.00, 1, 'Pendente', '2023-11-15', NULL, NULL),
  ('João Silva', 'Mousepad', 'Acessórios', 35.00, 2, 'Entregue', '2023-11-18', 5, NULL);
```

Responda:

a) Quais são os 3 clientes que mais compraram (por quantidade de transações)?  
b) Produtos entregues com avaliação ruim (1, 2 ou 3) que têm comentários  
c) Transações de Eletrônicos OU Periféricos, com valor acima de 100, entregues em novembro (após dia 05)  
d) Produtos que ainda não foram avaliados, excluindo os cancelados e pendentes  
e) Top 5 transações por valor total (valor × quantidade), de qualquer categoria exceto 'Acessórios'

---

## Exercício 18: Armadilhas comuns

Identifique os erros nas consultas abaixo e corrija-os:

```sql
-- Consulta A
SELECT nome, preco 
FROM produtos 
WHERE preco = NULL;

-- Consulta B
SELECT * FROM clientes 
WHERE cidade = 'são paulo';  -- cidade pode estar como 'São Paulo'

-- Consulta C
SELECT produto, valor 
FROM vendas 
WHERE valor BETWEEN 100 200;

-- Consulta D
SELECT nome 
FROM funcionarios 
WHERE departamento IN 'TI', 'Vendas', 'RH';
```

---

## Exercício 19: Boas práticas

Reescreva a consulta abaixo aplicando boas práticas de legibilidade e performance:

```sql
select * from produtos where categoria='Eletrônicos' or categoria='Periféricos' and preco>100 and preco<1000 or estoque>=10 order by preco limit 5;
```