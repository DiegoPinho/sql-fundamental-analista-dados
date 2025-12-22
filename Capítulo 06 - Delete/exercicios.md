# Exercícios - Capítulo 06: Remoção de Dados (DELETE)

## Exercício 1: DELETE básico

Considerando a tabela `produtos`:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL
);

INSERT INTO produtos (nome, preco, estoque)
VALUES 
  ('Mouse', 45.90, 50),
  ('Teclado', 120.00, 30),
  ('Monitor', 800.00, 15);
```

Escreva um comando DELETE para remover apenas o produto com id igual a 2.

---

## Exercício 2: O perigo do DELETE sem WHERE

Analise o cenário abaixo:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL
);

INSERT INTO clientes (nome, email)
VALUES 
  ('João Silva', 'joao@email.com'),
  ('Maria Santos', 'maria@email.com'),
  ('Pedro Costa', 'pedro@email.com'),
  ('Ana Oliveira', 'ana@email.com');

-- Intenção: deletar apenas o cliente João
DELETE FROM clientes;
```

O que acontecerá após executar este DELETE? Quantos registros permanecerão na tabela?

---

## Exercício 3: DELETE com múltiplas condições

Dada a tabela `estoque`:

```sql
CREATE TABLE estoque (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  quantidade INT NOT NULL,
  data_entrada DATE NOT NULL,
  vencido BOOLEAN DEFAULT false
);

INSERT INTO estoque (produto, quantidade, data_entrada, vencido)
VALUES 
  ('Leite', 0, '2023-10-01', true),
  ('Pão', 50, '2023-12-01', false),
  ('Queijo', 0, '2023-09-15', true),
  ('Manteiga', 20, '2023-11-20', false);
```

Escreva um DELETE para remover produtos que estão com quantidade zero E estão vencidos.

---

## Exercício 4: Testando antes de deletar

Você precisa deletar todos os pedidos cancelados da tabela abaixo:

```sql
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(150) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  status VARCHAR(50) NOT NULL
);

INSERT INTO pedidos (cliente, valor, status)
VALUES 
  ('Empresa A', 500.00, 'Concluído'),
  ('Empresa B', 1500.00, 'Cancelado'),
  ('Empresa C', 2000.00, 'Concluído'),
  ('Empresa D', 800.00, 'Cancelado');
```

Antes de deletar:
1. Escreva um SELECT para verificar quais registros serão removidos
2. Escreva o comando DELETE

---

## Exercício 5: DELETE vs TRUNCATE

Explique a diferença entre os comandos abaixo e quando usar cada um:

```sql
-- Comando A
DELETE FROM produtos;

-- Comando B
TRUNCATE TABLE produtos;
```

---

## Exercício 6: DELETE vs DROP

Qual é a diferença entre os comandos abaixo?

```sql
-- Comando A
DELETE FROM reunioes;

-- Comando B
TRUNCATE TABLE reunioes;

-- Comando C
DROP TABLE reunioes;
```

---

## Exercício 7: Praticando TRUNCATE

Dada a tabela `logs`:

```sql
CREATE TABLE logs (
  id SERIAL PRIMARY KEY,
  mensagem TEXT NOT NULL,
  data_hora TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO logs (mensagem)
VALUES 
  ('Usuário fez login'),
  ('Relatório gerado'),
  ('Erro na importação'),
  ('Backup concluído');
```

Você precisa limpar todos os logs antigos para começar uma nova auditoria. Escreva o comando mais eficiente para remover todos os registros mantendo a estrutura da tabela.

---

## Exercício 8: Soft Delete (Exclusão Lógica)

Implemente um sistema de exclusão lógica para a tabela `usuarios`:

```sql
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(255) NOT NULL
);

INSERT INTO usuarios (nome, email)
VALUES 
  ('João', 'joao@email.com'),
  ('Maria', 'maria@email.com'),
  ('Pedro', 'pedro@email.com');
```

1. Adicione uma coluna `ativo` com valor padrão TRUE
2. "Remova" o usuário João usando exclusão lógica
3. Escreva uma consulta que mostre apenas usuários ativos

---

## Exercício 9: Soft Delete com data de exclusão

Aprimore o sistema de exclusão lógica registrando quando cada registro foi "excluído":

```sql
CREATE TABLE produtos_loja (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL
);

INSERT INTO produtos_loja (nome, preco)
VALUES 
  ('Notebook', 3500.00),
  ('Mouse', 45.90),
  ('Teclado', 120.00);
```

1. Adicione uma coluna `data_exclusao` do tipo TIMESTAMP que permite NULL
2. "Remova" o produto Mouse registrando a data/hora atual
3. Consulte apenas produtos que NÃO foram excluídos

---

## Exercício 10: Arquivamento de dados

Implemente um sistema de arquivamento:

```sql
CREATE TABLE vendas (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  data_venda DATE NOT NULL
);

INSERT INTO vendas (produto, valor, data_venda)
VALUES 
  ('Notebook', 3500.00, '2022-01-15'),
  ('Mouse', 45.90, '2023-11-20'),
  ('Monitor', 800.00, '2021-06-10');
```

1. Crie uma tabela `vendas_arquivadas` com a mesma estrutura de `vendas`
2. Mova as vendas de 2021 e 2022 para a tabela de arquivo
3. Delete as vendas arquivadas da tabela principal

---

## Exercício 11: DELETE com subconsulta

Dadas as tabelas:

```sql
CREATE TABLE categorias (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  ativa BOOLEAN DEFAULT true
);

CREATE TABLE produtos_cat (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  categoria_id INT NOT NULL
);

INSERT INTO categorias (nome, ativa)
VALUES ('Eletrônicos', true), ('Descontinuados', false);

INSERT INTO produtos_cat (nome, categoria_id)
VALUES 
  ('TV', 1),
  ('Radio Antigo', 2),
  ('Smartphone', 1);
```

Delete todos os produtos que pertencem a categorias inativas.

---

## Exercício 12: DELETE com RETURNING

O PostgreSQL permite usar RETURNING com DELETE para ver os registros que foram removidos.

```sql
CREATE TABLE tarefas (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200) NOT NULL,
  concluida BOOLEAN DEFAULT false
);

INSERT INTO tarefas (titulo, concluida)
VALUES 
  ('Estudar SQL', true),
  ('Fazer exercícios', false),
  ('Revisar conteúdo', true);
```

Delete todas as tarefas concluídas e retorne os títulos das tarefas removidas.

---

## Exercício 13: Identificando erros

Identifique os erros nos comandos DELETE abaixo e corrija-os:

```sql
-- Comando A
DELETE produtos WHERE id = 1;

-- Comando B
DELETE FROM clientes
WHERE nome = 'João'

-- Comando C
DELETE * FROM pedidos WHERE id = 5;

-- Comando D
DELETE FROM vendas;
WHERE data_venda < '2020-01-01';
```

---

## Exercício 14: Questão conceitual sobre soft delete

Quais são as vantagens e desvantagens de usar exclusão lógica (soft delete) em vez de exclusão física (DELETE)?

---

## Exercício 15: Cenário de limpeza de dados

Você gerencia uma tabela de sessões de usuários e precisa limpar sessões antigas:

```sql
CREATE TABLE sessoes (
  id SERIAL PRIMARY KEY,
  usuario_id INT NOT NULL,
  token VARCHAR(255) NOT NULL,
  data_criacao TIMESTAMP NOT NULL,
  data_expiracao TIMESTAMP NOT NULL,
  ativa BOOLEAN DEFAULT true
);

INSERT INTO sessoes (usuario_id, token, data_criacao, data_expiracao, ativa)
VALUES 
  (1, 'abc123', '2023-10-01 10:00:00', '2023-10-01 22:00:00', false),
  (2, 'def456', '2023-12-01 08:00:00', '2023-12-01 20:00:00', true),
  (3, 'ghi789', '2023-11-15 14:00:00', '2023-11-15 23:00:00', false);
```

Delete todas as sessões que estão inativas E foram criadas antes de dezembro de 2023.

---

## Exercício 16: Comparando estratégias de remoção

Para cada cenário abaixo, escolha a melhor estratégia de remoção (DELETE, TRUNCATE, DROP ou Soft Delete) e justifique:

a) Limpar toda a tabela de cache temporário no final do dia  
b) Remover um cliente específico que solicitou exclusão de dados (LGPD/GDPR)  
c) Desativar um produto que não é mais vendido mas aparece em pedidos antigos  
d) Remover completamente uma tabela de testes que não é mais necessária  
e) Limpar logs de sistema com mais de 90 dias

---

## Exercício 17: Desafio - Sistema completo de gerenciamento

Implemente um sistema completo de gerenciamento de produtos com as seguintes funcionalidades:

```sql
CREATE TABLE produtos_sistema (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  preco DECIMAL(10, 2) NOT NULL,
  estoque INT NOT NULL,
  ativo BOOLEAN DEFAULT true,
  data_inativacao TIMESTAMP NULL
);

INSERT INTO produtos_sistema (nome, preco, estoque)
VALUES 
  ('Produto A', 100.00, 0),
  ('Produto B', 200.00, 50),
  ('Produto C', 150.00, 0),
  ('Produto D', 300.00, 20);
```

Crie comandos SQL para:

1. Inativar (soft delete) produtos com estoque zero
2. Consultar apenas produtos ativos
3. Consultar produtos inativos nos últimos 30 dias
4. Deletar fisicamente produtos inativos há mais de 1 ano
5. Criar uma tabela de arquivo e mover produtos inativos para lá

---

## Exercício 18: Prevenção de exclusão acidental

Você está trabalhando em produção e precisa deletar alguns registros específicos. Quais práticas de segurança você deve seguir? Liste pelo menos 5 boas práticas.

---

## Exercício 19: Questão conceitual final

Explique por que o comando DELETE é considerado uma operação "destrutiva" e quais são as implicações disso em um ambiente de produção.