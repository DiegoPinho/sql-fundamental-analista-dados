# Exercícios - Capítulo 14: Funções de String - Manipulando textos

## Exercício 1: CONCAT - Concatenação básica

Crie uma tabela de produtos e use concatenação para criar descrições completas:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  marca VARCHAR(50),
  preco DECIMAL(10, 2)
);

INSERT INTO produtos (nome, marca, preco)
VALUES 
  ('Mouse Gamer', 'Logitech', 150.00),
  ('Teclado Mecânico', 'Razer', 450.00),
  ('Headset', 'HyperX', 300.00);
```

a) Crie uma descrição completa no formato: "nome - marca (R$ preco)"
b) Compare o comportamento de `CONCAT()` vs `||` quando um valor é NULL

---

## Exercício 2: UPPER e LOWER - Padronização de texto

Use a tabela de clientes do capítulo:

```sql
CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(150),
  email VARCHAR(100),
  cidade VARCHAR(100)
);

INSERT INTO clientes (nome, email, cidade)
VALUES 
  (' Ricardo Pereira ', 'RICARDO@abc.com', 'São Paulo'),
  ('Juliana Costa', 'ju.costa@abc.com', 'sao paulo'),
  ('MARCOS SANTOS', 'marcos@abc.com', 'Salvador'),
  ('paulo oliveira', 'p_oliveira@abc.com', 'salvador');
```

a) Padronize todos os e-mails para minúsculas
b) Padronize todas as cidades para formato "Primeira Letra Maiúscula"
c) Busque clientes de "São Paulo" de forma case-insensitive

---

## Exercício 3: LENGTH - Validação de tamanho

Usando a tabela de clientes anterior:

a) Encontre nomes com mais de 15 caracteres
b) Identifique e-mails suspeitos (muito curtos, com menos de 10 caracteres)
c) Mostre o tamanho de cada nome e identifique quais têm espaços extras

---

## Exercício 4: TRIM - Limpeza de espaços

a) Limpe todos os espaços extras dos nomes na tabela clientes
b) Demonstre a diferença entre TRIM, LTRIM e RTRIM
c) Atualize a tabela removendo espaços extras (usando UPDATE)

---

## Exercício 5: SUBSTRING - Extração de partes

Crie uma tabela de códigos de produto:

```sql
CREATE TABLE cod_produtos (
  codigo VARCHAR(20)
);

INSERT INTO cod_produtos (codigo)
VALUES 
  ('ELE-2024-001'),
  ('MOV-2024-055'),
  ('ELE-2023-120'),
  ('LIV-2024-003');
```

Os códigos seguem o padrão: CATEGORIA-ANO-NUMERO

a) Extraia apenas a categoria (primeiros 3 caracteres)
b) Extraia apenas o ano (caracteres 5 a 8)
c) Extraia apenas o número (últimos 3 caracteres)

---

## Exercício 6: POSITION e SUBSTRING - Extração dinâmica

Usando a tabela de clientes, extraia:

a) O domínio dos e-mails (tudo após o @)
b) O nome de usuário dos e-mails (tudo antes do @)
c) O primeiro nome (tudo antes do primeiro espaço)

---

## Exercício 7: REPLACE - Substituição e limpeza

Crie uma tabela de telefones com formatos inconsistentes:

```sql
CREATE TABLE contatos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  telefone VARCHAR(20)
);

INSERT INTO contatos (nome, telefone)
VALUES 
  ('Ana', '(11) 98765-4321'),
  ('Bruno', '11987654321'),
  ('Carla', '(21) 9 9876-5432'),
  ('Diego', '21-99876-5432');
```

a) Padronize todos os telefones removendo parênteses, espaços e hífens
b) Substitua o domínio "@abc.com" por "@empresa.com.br" nos e-mails da tabela clientes
c) Remova acentos de "São Paulo" (substitua ã por a)

---

## Exercício 8: SPLIT_PART - Dividir strings

Crie uma tabela de endereços completos:

```sql
CREATE TABLE enderecos (
  id SERIAL PRIMARY KEY,
  endereco_completo VARCHAR(200)
);

INSERT INTO enderecos (endereco_completo)
VALUES 
  ('Rua das Flores, 123, Jardim Primavera, São Paulo, SP'),
  ('Av. Paulista, 1000, Bela Vista, São Paulo, SP'),
  ('Rua XV de Novembro, 50, Centro, Salvador, BA');
```

Formato: RUA, NUMERO, BAIRRO, CIDADE, ESTADO

Use `SPLIT_PART()` para extrair cada componente.

---

## Exercício 9: Combinação de funções - Limpeza completa

Use TODAS as funções aprendidas para criar uma consulta de limpeza completa dos dados de clientes. A consulta deve:

1. Remover espaços extras do nome
2. Padronizar nome para formato "Nome Sobrenome" (INITCAP)
3. Converter e-mail para minúsculas
4. Extrair domínio do e-mail
5. Padronizar cidade (INITCAP)
6. Criar um identificador único: primeiras 3 letras do nome + últimos 4 dígitos do id


---

## Exercício 10: Desafio - Validação e formatação de CPF

Crie uma tabela com CPFs em vários formatos diferentes:

```sql
CREATE TABLE cadastros (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  cpf VARCHAR(20)
);

INSERT INTO cadastros (nome, cpf)
VALUES 
  ('Ana Silva', '123.456.789-00'),
  ('Bruno Costa', '12345678900'),
  ('Carla Souza', '111.222.333-44'),
  ('Diego Lima', '555-666-777-88'),
  ('Elena Rocha', '999 888 777 66');
```

Crie uma consulta que:

a) Remova toda formatação do CPF (deixe só números)
b) Valide se o CPF tem exatamente 11 dígitos após limpeza
c) Reaplique a formatação padrão: XXX.XXX.XXX-XX
d) Marque CPFs inválidos (diferentes de 11 dígitos)