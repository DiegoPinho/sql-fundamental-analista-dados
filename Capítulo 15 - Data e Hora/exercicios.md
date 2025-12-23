# Exercícios - Capítulo 15: Funções de Data e Hora

## Exercício 1: CURRENT_DATE, NOW() e operações básicas

Crie uma tabela de tarefas e explore as funções básicas de data:

```sql
CREATE TABLE tarefas (
  id SERIAL PRIMARY KEY,
  descricao VARCHAR(200),
  data_criacao DATE,
  prazo DATE,
  data_conclusao DATE
);

INSERT INTO tarefas (descricao, data_criacao, prazo, data_conclusao)
VALUES 
  ('Relatório mensal', '2024-11-01', '2024-11-15', '2024-11-14'),
  ('Apresentação', '2024-11-10', '2024-11-20', NULL),
  ('Code review', '2024-11-15', '2024-11-18', '2024-11-17'),
  ('Documentação', '2024-11-20', '2024-12-05', NULL),
  ('Reunião cliente', '2024-12-01', '2024-12-10', '2024-12-09');
```

a) Mostre a data e hora atual usando NOW() e CURRENT_DATE
b) Calcule quantos dias faltam (ou passaram) para o prazo de cada tarefa
c) Identifique tarefas atrasadas (prazo vencido e sem data_conclusao)

---

## Exercício 2: EXTRACT - Extraindo componentes de data

Use a tabela de vendas do capítulo:

```sql
CREATE TABLE vendas (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(100),
  valor_total DECIMAL(10,2),
  data_pedido TIMESTAMP
);

INSERT INTO vendas (cliente, valor_total, data_pedido)
VALUES 
  ('Maria', 459.90, '2024-01-15 14:23:45'),
  ('João', 1250.00, '2024-01-15 09:15:30'),
  ('Ana', 89.90, '2024-02-10 18:45:20'),
  ('Pedro', 2340.50, '2024-02-28 11:30:00'),
  ('Juliana', 678.80, '2024-03-05 16:20:45'),
  ('Carlos', 156.70, '2024-03-20 10:05:15'),
  ('Fernanda', 890.00, '2024-04-02 15:50:20'),
  ('Roberto', 1567.30, '2024-04-15 08:30:10');
```

a) Extraia ano, mês, dia e hora de cada pedido
b) Calcule o total de vendas por mês
c) Identifique o trimestre de cada venda e calcule total por trimestre

---

## Exercício 3: DATE_PART e dia da semana

a) Use DATE_PART (alternativa ao EXTRACT) para extrair componentes
b) Descubra em qual dia da semana cada venda foi feita
c) Calcule qual dia da semana tem mais vendas e maior faturamento

---

## Exercício 4: DATE_TRUNC - Truncamento de datas

a) Use DATE_TRUNC para agrupar vendas por diferentes períodos
b) Compare DATE_TRUNC com EXTRACT para agrupamento mensal
c) Crie um relatório de vendas semanais

---

## Exercício 5: AGE - Calculando diferenças de tempo

Crie uma tabela de funcionários e calcule:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  data_admissao DATE,
  data_nascimento DATE
);

INSERT INTO funcionarios (nome, data_admissao, data_nascimento)
VALUES 
  ('Ana Silva', '2020-03-15', '1990-05-20'),
  ('Bruno Costa', '2018-07-01', '1985-12-10'),
  ('Carla Souza', '2022-01-10', '1995-08-25'),
  ('Diego Lima', '2019-11-20', '1988-03-15');
```

a) Calcule o tempo de empresa de cada funcionário
b) Calcule a idade atual de cada funcionário
c) Identifique funcionários com mais de 5 anos de empresa

---

## Exercício 6: INTERVAL - Operações com períodos

a) Use INTERVAL para adicionar e subtrair períodos de tempo
b) Calcule datas futuras e passadas
c) Encontre vendas dos últimos 60 dias e próximos 30 dias

---

## Exercício 7: TO_CHAR - Formatação de datas

a) Formate datas em diferentes padrões brasileiros
b) Extraia e formate componentes específicos
c) Crie relatórios com datas formatadas para apresentação

---

## Exercício 8: Fusos horários - TIMESTAMPTZ

Crie uma tabela para eventos globais e pratique conversões de fuso:

```sql
CREATE TABLE eventos_globais (
  id SERIAL PRIMARY KEY,
  descricao VARCHAR(100),
  momento TIMESTAMP WITH TIME ZONE
);

INSERT INTO eventos_globais (descricao, momento)
VALUES 
  ('Reunião SP', '2024-12-15 10:00:00-03'),
  ('Webinar NY', '2024-12-15 10:00:00-05'),
  ('Conferência Londres', '2024-12-15 10:00:00+00'),
  ('Workshop Tokyo', '2024-12-15 10:00:00+09');
```

a) Veja como os horários são convertidos para seu fuso local
b) Converta todos para UTC
c) Converta para diferentes fusos horários
d) Calcule qual evento acontece primeiro

---

## Exercício 9: Análise temporal avançada - Séries temporais

Crie um relatório de vendas diárias, preenchendo dias sem vendas com zero.

Use as vendas da tabela inicial e gere uma série de datas.

---

## Exercício 10: Desafio - Sistema de agendamento com múltiplos fusos

Crie um sistema de agendamento global que:

```sql
CREATE TABLE reunioes_globais (
  id SERIAL PRIMARY KEY,
  titulo VARCHAR(200),
  organizador VARCHAR(100),
  fuso_organizador VARCHAR(50),
  horario_organizador TIMESTAMP,
  duracao_minutos INTEGER
);

INSERT INTO reunioes_globais 
  (titulo, organizador, fuso_organizador, horario_organizador, duracao_minutos)
VALUES 
  ('Daily Standup', 'Ana Silva', 'America/Sao_Paulo', '2024-12-20 09:00:00', 15),
  ('Sprint Planning', 'John Smith', 'America/New_York', '2024-12-20 14:00:00', 120),
  ('Code Review', 'Yuki Tanaka', 'Asia/Tokyo', '2024-12-20 10:00:00', 60),
  ('All Hands Meeting', 'Emma Watson', 'Europe/London', '2024-12-20 15:00:00', 90);
```

Crie consultas que:
a) Mostre todos os horários em UTC
b) Converta para o fuso do usuário (ex: São Paulo)
c) Calcule horário de término
d) Identifique conflitos de horário para um participante em São Paulo
e) Crie uma agenda formatada para apresentação
```