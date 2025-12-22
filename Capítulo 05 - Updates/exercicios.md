# Exercícios - Capítulo 05: Atualizações de Dados (UPDATE)

## Exercício 1: UPDATE básico

Considerando a tabela `produtos` abaixo com um registro:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL
);

INSERT INTO produtos (nome, preco, estoque)
VALUES ('Mouse', 45.90, 50);
```

O preço do mouse mudou para 39.90. Escreva o comando UPDATE para alterar o preço, garantindo que apenas este produto seja atualizado.


---

## Exercício 2: Atualizando múltiplas colunas

Na tabela `produtos` do exercício anterior, você precisa atualizar o mouse para:
- Novo preço: 42.50
- Novo estoque: 75

Escreva o comando UPDATE que faça ambas as alterações de uma só vez.


---

## Exercício 3: O perigo do UPDATE sem WHERE

Analise o cenário abaixo:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL,
  cidade VARCHAR(100) NOT NULL
);

INSERT INTO clientes (nome, email, cidade)
VALUES 
  ('João Silva', 'joao@email.com', 'São Paulo'),
  ('Maria Santos', 'maria@email.com', 'Rio de Janeiro'),
  ('Pedro Costa', 'pedro@email.com', 'Belo Horizonte');

-- Queremos atualizar a cidade de João para 'Campinas'
UPDATE clientes SET cidade = 'Campinas';
```

O que acontecerá após executar este UPDATE? Quantos registros serão afetados?

---

## Exercício 4: Usando diferentes condições no WHERE

Dada a tabela `funcionarios`:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  cargo VARCHAR(80) NOT NULL,
  salario DECIMAL(10, 2) NOT NULL,
  ativo BOOLEAN DEFAULT true
);

INSERT INTO funcionarios (nome, cargo, salario)
VALUES 
  ('Ana Costa', 'Analista', 5000.00),
  ('Bruno Silva', 'Gerente', 8000.00),
  ('Carla Souza', 'Analista', 5500.00);
```

Escreva comandos UPDATE para:

a) Aumentar em 500 reais o salário do funcionário com id 2  
b) Atualizar o cargo de 'Ana Costa' para 'Analista Sênior'  
c) Marcar como inativo (ativo = false) o funcionário 'Bruno Silva'

---

## Exercício 5: Testando com SELECT antes do UPDATE

Você precisa atualizar a tabela `pedidos` abaixo, alterando o status de todos os pedidos com valor acima de 1000.00 para 'Prioritário'.

```sql
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) DEFAULT 'Pendente'
);

INSERT INTO pedidos (cliente, valor)
VALUES 
  ('Empresa A', 500.00),
  ('Empresa B', 1500.00),
  ('Empresa C', 2000.00),
  ('Empresa D', 800.00);
```

Antes de fazer o UPDATE:
1. Escreva um SELECT para verificar quais registros serão afetados
2. Escreva o comando UPDATE

---

## Exercício 6: UPDATE com operadores de comparação

Dada a tabela `estoque`:

```sql
CREATE TABLE estoque (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  quantidade INT NOT NULL,
  alerta_minimo INT DEFAULT 10
);

INSERT INTO estoque (produto, quantidade, alerta_minimo)
VALUES 
  ('Notebook', 5, 10),
  ('Mouse', 150, 20),
  ('Teclado', 8, 15),
  ('Monitor', 25, 10);
```

Escreva comandos UPDATE para:

a) Aumentar em 50 unidades a quantidade de produtos com quantidade menor que 10  
b) Atualizar o alerta_minimo para 25 nos produtos com quantidade maior ou igual a 100  
c) Reduzir em 10 unidades a quantidade do produto 'Monitor'

---

## Exercício 7: Identificando erros em UPDATE

Identifique os erros nos comandos UPDATE abaixo e forneça as versões corrigidas:

```sql
-- Comando A
UPDATE produtos 
  preco = 100.00 
WHERE id = 1;

-- Comando B
UPDATE clientes 
SET email = 'novo@email.com'
WHERE id = 2

-- Comando C
UPDATE funcionarios 
SET salario == 6000.00 
WHERE nome = 'João';

-- Comando D
UPDATE pedidos 
WHERE status = 'Cancelado'
SET valor = 0;
```

---

## Exercício 8: UPDATE com cálculos

Uma loja está fazendo uma promoção. Na tabela `produtos_loja`:

```sql
CREATE TABLE produtos_loja (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco_original DECIMAL(10, 2) NOT NULL,
  preco_promocional DECIMAL(10, 2),
  categoria VARCHAR(50) NOT NULL
);

INSERT INTO produtos_loja (nome, preco_original, categoria)
VALUES 
  ('Smart TV', 2000.00, 'Eletrônicos'),
  ('Geladeira', 3000.00, 'Eletrodomésticos'),
  ('Notebook', 3500.00, 'Eletrônicos'),
  ('Fogão', 1500.00, 'Eletrodomésticos');
```

Escreva comandos UPDATE para:

a) Aplicar 10% de desconto (preco_promocional) em todos os produtos da categoria 'Eletrônicos'  
b) Aplicar 15% de desconto em todos os produtos com preço original acima de 2500.00


---

## Exercício 9: UPDATE com múltiplas condições

Dada a tabela `alunos`:

```sql
CREATE TABLE alunos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  nota_final DECIMAL(5, 2),
  frequencia INT,
  status VARCHAR(20) DEFAULT 'Em andamento'
);

INSERT INTO alunos (nome, nota_final, frequencia)
VALUES 
  ('Alice', 8.5, 90),
  ('Bruno', 5.0, 70),
  ('Carla', 7.5, 85),
  ('Diego', 4.5, 60);
```

Um aluno é aprovado se: nota_final >= 7.0 E frequencia >= 75  
Escreva um UPDATE para marcar como 'Aprovado' os alunos que atendem a esses critérios.

---

## Exercício 10: UPDATE baseado em datas

Considere a tabela `tarefas`:

```sql
CREATE TABLE tarefas (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  data_criacao DATE NOT NULL,
  data_conclusao DATE,
  status VARCHAR(50) DEFAULT 'Pendente'
);

INSERT INTO tarefas (titulo, data_criacao, data_conclusao)
VALUES 
  ('Tarefa 1', '2023-10-01', NULL),
  ('Tarefa 2', '2023-10-15', '2023-10-20'),
  ('Tarefa 3', '2023-11-01', NULL),
  ('Tarefa 4', '2023-11-10', '2023-11-15');
```

Escreva comandos UPDATE para:

a) Atualizar o status para 'Concluída' em todas as tarefas que têm data_conclusao preenchida  
b) Atualizar o status para 'Atrasada' em tarefas criadas antes de '2023-11-01' e ainda sem data_conclusao

---

## Exercício 11: Cenário de correção de dados

Um erro de importação fez com que todos os emails na tabela `contatos` ficassem em maiúsculas. Você precisa corrigi-los.

```sql
CREATE TABLE contatos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL,
  telefone VARCHAR(15)
);

INSERT INTO contatos (nome, email, telefone)
VALUES 
  ('Ana', 'ANA@EMAIL.COM', '11999999999'),
  ('Bruno', 'BRUNO@EMAIL.COM', '11988888888'),
  ('Carla', 'CARLA@EMAIL.COM', '11977777777');
```

Pesquise e escreva um comando UPDATE que converta todos os emails para minúsculas. (Dica: PostgreSQL tem uma função chamada `LOWER()`)

---

## Exercício 12: UPDATE com RETURNING

O PostgreSQL permite usar a cláusula `RETURNING` com UPDATE para ver os valores atualizados. Dada a tabela:

```sql
CREATE TABLE inventario (
  id SERIAL PRIMARY KEY,
  item VARCHAR(100) NOT NULL,
  quantidade INT NOT NULL
);

INSERT INTO inventario (item, quantidade)
VALUES ('Caneta', 100);
```

Escreva um UPDATE que reduza a quantidade em 25 unidades e retorne o id e a nova quantidade.

---

## Exercício 13: Questão conceitual

Por que é considerado uma boa prática testar a condição do WHERE com um SELECT antes de executar um UPDATE? Cite pelo menos duas razões.

---

## Exercício 14: UPDATE condicional complexo

Uma empresa precisa atualizar o status de seus colaboradores baseado em múltiplas regras:

```sql
CREATE TABLE colaboradores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  tempo_empresa_anos INT NOT NULL,
  faltas INT DEFAULT 0,
  nivel VARCHAR(50) DEFAULT 'Júnior'
);

INSERT INTO colaboradores (nome, tempo_empresa_anos, faltas)
VALUES 
  ('João', 5, 2),
  ('Maria', 3, 8),
  ('Pedro', 7, 1),
  ('Ana', 2, 15);
```

Escreva UPDATE para mudar o nível para 'Sênior' dos colaboradores que têm:
- 5 ou mais anos de empresa E
- Menos de 5 faltas

---

## Exercício 15: Reversão de UPDATE

Você executou acidentalmente este UPDATE:

```sql
CREATE TABLE produtos_preco (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL
);

INSERT INTO produtos_preco (nome, preco)
VALUES 
  ('Produto A', 100.00),
  ('Produto B', 200.00),
  ('Produto C', 150.00);

-- UPDATE acidental sem WHERE!
UPDATE produtos_preco SET preco = 99.99;
```

Todos os preços foram alterados para 99.99. Assumindo que você não tem backup, o que você aprendeu neste capítulo que poderia ter evitado este problema?

---

## Exercício 16: Desafio - Sistema de biblioteca

Você gerencia uma biblioteca e precisa atualizar vários registros:

```sql
CREATE TABLE livros_biblioteca (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(255) NOT NULL,
  disponivel BOOLEAN DEFAULT true,
  data_emprestimo DATE,
  data_devolucao_prevista DATE,
  multa DECIMAL(10, 2) DEFAULT 0
);

INSERT INTO livros_biblioteca (titulo, disponivel, data_emprestimo, data_devolucao_prevista)
VALUES 
  ('1984', false, '2023-11-01', '2023-11-15'),
  ('Dom Casmurro', true, NULL, NULL),
  ('O Cortiço', false, '2023-11-10', '2023-11-24'),
  ('Grande Sertão', false, '2023-10-20', '2023-11-03');
```

Considerando que hoje é 25/11/2023, escreva comandos UPDATE para:

a) Marcar como disponível os livros que já passaram da data_devolucao_prevista  
b) Aplicar multa de R$ 5,00 por dia de atraso nos livros com devolução atrasada  
   (calcule os dias entre data_devolucao_prevista e hoje: '2023-11-25')  
c) Limpar (NULL) as datas de empréstimo dos livros que voltaram a ficar disponíveis

---

## Exercício 17: UPDATE com valores NULL

Analise a tabela `vendedores`:

```sql
CREATE TABLE vendedores (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  comissao_percentual DECIMAL(5, 2),
  meta_mensal DECIMAL(10, 2)
);

INSERT INTO vendedores (nome, comissao_percentual, meta_mensal)
VALUES 
  ('Carlos', 5.00, 10000.00),
  ('Diana', NULL, NULL),
  ('Eduardo', 3.50, 8000.00);
```

Escreva UPDATE para:

a) Definir comissao_percentual como 4.00 para vendedores que ainda não têm comissão definida  
b) Definir meta_mensal como 5000.00 para vendedores sem meta

---

## Exercício 18: Desafio final - E-commerce

Um e-commerce precisa fazer várias atualizações em massa:

```sql
CREATE TABLE pedidos_ecommerce (
  id SERIAL PRIMARY KEY,
  numero_pedido VARCHAR(20) UNIQUE NOT NULL,
  cliente VARCHAR(150) NOT NULL,
  valor_total DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) DEFAULT 'Aguardando Pagamento',
  desconto_aplicado DECIMAL(10, 2) DEFAULT 0,
  data_pedido DATE NOT NULL
);

INSERT INTO pedidos_ecommerce (numero_pedido, cliente, valor_total, data_pedido)
VALUES 
  ('PED001', 'João Silva', 1500.00, '2023-11-20'),
  ('PED002', 'Maria Santos', 800.00, '2023-11-21'),
  ('PED003', 'Pedro Costa', 2500.00, '2023-11-22'),
  ('PED004', 'Ana Oliveira', 450.00, '2023-11-23');
```

Crie uma sequência de UPDATEs para:

1. Aplicar 10% de desconto em pedidos acima de R$ 1000  
2. Atualizar status para 'Processando' nos pedidos com desconto aplicado  
3. Atualizar o valor_total subtraindo o desconto aplicado
