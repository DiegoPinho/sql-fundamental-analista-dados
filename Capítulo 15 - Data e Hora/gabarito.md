# Exercícios - Capítulo 15: Funções de Data e Hora

## Exercício 1: CURRENT_DATE, NOW() e operações básicas

```sql
-- a) Data e hora atual
SELECT 
  NOW() AS data_hora_completa,
  CURRENT_DATE AS apenas_data,
  CURRENT_TIME AS apenas_hora;

-- Resultado (exemplo em 2024-12-23):
--     data_hora_completa      |  apenas_data | apenas_hora
-- ----------------------------+--------------+------------------
--  2024-12-23 14:30:45.123-03 | 2024-12-23   | 14:30:45.123-03

-- b) Dias até o prazo (positivo = futuro, negativo = passado)
SELECT 
  descricao,
  prazo,
  CURRENT_DATE AS hoje,
  prazo - CURRENT_DATE AS dias_ate_prazo
FROM tarefas
ORDER BY dias_ate_prazo;

-- Resultado (assumindo hoje = 2024-12-23):
--     descricao     |   prazo    |   hoje     | dias_ate_prazo
-- ------------------+------------+------------+----------------
--  Relatório mensal | 2024-11-15 | 2024-12-23 |            -38
--  Code review      | 2024-11-18 | 2024-12-23 |            -35
--  Apresentação     | 2024-11-20 | 2024-12-23 |            -33
--  Documentação     | 2024-12-05 | 2024-12-23 |            -18
--  Reunião cliente  | 2024-12-10 | 2024-12-23 |            -13

-- c) Tarefas atrasadas (prazo vencido e não concluídas)
SELECT 
  descricao,
  prazo,
  CURRENT_DATE - prazo AS dias_atraso
FROM tarefas
WHERE data_conclusao IS NULL 
  AND prazo < CURRENT_DATE
ORDER BY dias_atraso DESC;

-- Resultado:
--   descricao   |   prazo    | dias_atraso
-- --------------+------------+-------------
--  Apresentação | 2024-11-20 |          33
--  Documentação | 2024-12-05 |          18
```

**Explicação:**
- `NOW()` retorna timestamp completo (data + hora + timezone);
- `CURRENT_DATE` retorna apenas a data;
- Subtração de datas retorna o número de dias de diferença;
- Valores negativos indicam que a data já passou.

---

## Exercício 2: EXTRACT - Extraindo componentes de data

```sql
-- a) Extrair componentes individuais
SELECT 
  cliente,
  data_pedido,
  EXTRACT(YEAR FROM data_pedido) AS ano,
  EXTRACT(MONTH FROM data_pedido) AS mes,
  EXTRACT(DAY FROM data_pedido) AS dia,
  EXTRACT(HOUR FROM data_pedido) AS hora
FROM vendas
LIMIT 4;

-- Resultado:
--  cliente |     data_pedido     | ano  | mes | dia | hora
-- ---------+---------------------+------+-----+-----+------
--  Maria   | 2024-01-15 14:23:45 | 2024 |   1 |  15 |   14
--  João    | 2024-01-15 09:15:30 | 2024 |   1 |  15 |    9
--  Ana     | 2024-02-10 18:45:20 | 2024 |   2 |  10 |   18
--  Pedro   | 2024-02-28 11:30:00 | 2024 |   2 |  28 |   11

-- b) Total de vendas por mês
SELECT 
  EXTRACT(YEAR FROM data_pedido) AS ano,
  EXTRACT(MONTH FROM data_pedido) AS mes,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total_vendido,
  ROUND(AVG(valor_total), 2) AS ticket_medio
FROM vendas
GROUP BY ano, mes
ORDER BY ano, mes;

-- Resultado:
--  ano  | mes | qtd_vendas | total_vendido | ticket_medio
-- ------+-----+------------+---------------+--------------
--  2024 |   1 |          2 |       1709.90 |       854.95
--  2024 |   2 |          2 |       2430.40 |      1215.20
--  2024 |   3 |          2 |        835.50 |       417.75
--  2024 |   4 |          2 |       2457.30 |      1228.65

-- c) Vendas por trimestre
SELECT 
  EXTRACT(YEAR FROM data_pedido) AS ano,
  EXTRACT(QUARTER FROM data_pedido) AS trimestre,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total_vendido
FROM vendas
GROUP BY ano, trimestre
ORDER BY ano, trimestre;

-- Resultado:
--  ano  | trimestre | qtd_vendas | total_vendido
-- ------+-----------+------------+---------------
--  2024 |         1 |          4 |       4140.30
--  2024 |         2 |          4 |       2457.30

-- BÔNUS: Adicionar nome do trimestre
SELECT 
  EXTRACT(YEAR FROM data_pedido) AS ano,
  EXTRACT(QUARTER FROM data_pedido) AS trimestre,
  'Q' || EXTRACT(QUARTER FROM data_pedido) || '/' || 
    EXTRACT(YEAR FROM data_pedido) AS periodo,
  SUM(valor_total) AS total_vendido
FROM vendas
GROUP BY ano, trimestre
ORDER BY ano, trimestre;

-- Resultado:
--  ano  | trimestre | periodo | total_vendido
-- ------+-----------+---------+---------------
--  2024 |         1 | Q1/2024 |       4140.30
--  2024 |         2 | Q2/2024 |       2457.30
```

**Explicação:**
- `EXTRACT()` permite extrair qualquer componente de data/hora;
- `QUARTER` retorna 1-4 representando os trimestres;
- Útil para agregações e análises temporais;
- Pode agrupar por múltiplos níveis (ano + mês, ano + trimestre, etc.).

---

## Exercício 3: DATE_PART e dia da semana

```sql
-- a) DATE_PART (sintaxe alternativa)
SELECT 
  cliente,
  data_pedido,
  DATE_PART('year', data_pedido) AS ano,
  DATE_PART('month', data_pedido) AS mes,
  DATE_PART('day', data_pedido) AS dia
FROM vendas
LIMIT 3;

-- Resultado idêntico ao EXTRACT

-- b) Dia da semana de cada venda (0=domingo, 6=sábado)
SELECT 
  cliente,
  data_pedido,
  EXTRACT(DOW FROM data_pedido) AS num_dia_semana,
  TO_CHAR(data_pedido, 'Day') AS nome_dia_semana
FROM vendas;

-- Resultado:
--  cliente  |     data_pedido     | num_dia_semana | nome_dia_semana
-- ----------+---------------------+----------------+-----------------
--  Maria    | 2024-01-15 14:23:45 |              1 | Monday
--  João     | 2024-01-15 09:15:30 |              1 | Monday
--  Ana      | 2024-02-10 18:45:20 |              6 | Saturday
--  Pedro    | 2024-02-28 11:30:00 |              3 | Wednesday

-- c) Análise por dia da semana
SELECT 
  EXTRACT(DOW FROM data_pedido) AS num_dia,
  CASE EXTRACT(DOW FROM data_pedido)
    WHEN 0 THEN 'Domingo'
    WHEN 1 THEN 'Segunda'
    WHEN 2 THEN 'Terça'
    WHEN 3 THEN 'Quarta'
    WHEN 4 THEN 'Quinta'
    WHEN 5 THEN 'Sexta'
    WHEN 6 THEN 'Sábado'
  END AS dia_semana,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total_vendido,
  ROUND(AVG(valor_total), 2) AS ticket_medio
FROM vendas
GROUP BY num_dia
ORDER BY num_dia;

-- Resultado:
--  num_dia | dia_semana | qtd_vendas | total_vendido | ticket_medio
-- ---------+------------+------------+---------------+--------------
--        1 | Segunda    |          2 |       1709.90 |       854.95
--        2 | Terça      |          1 |        678.80 |       678.80
--        3 | Quarta     |          2 |       2497.20 |      1248.60
--        5 | Sexta      |          1 |        156.70 |       156.70
--        6 | Sábado     |          2 |        979.90 |       489.95

-- Encontrar o dia da semana com mais vendas
SELECT 
  CASE EXTRACT(DOW FROM data_pedido)
    WHEN 0 THEN 'Domingo'
    WHEN 1 THEN 'Segunda'
    WHEN 2 THEN 'Terça'
    WHEN 3 THEN 'Quarta'
    WHEN 4 THEN 'Quinta'
    WHEN 5 THEN 'Sexta'
    WHEN 6 THEN 'Sábado'
  END AS dia_semana,
  SUM(valor_total) AS total_vendido
FROM vendas
GROUP BY EXTRACT(DOW FROM data_pedido)
ORDER BY total_vendido DESC
LIMIT 1;

-- Resultado:
--  dia_semana | total_vendido
-- ------------+---------------
--  Quarta     |       2497.20
```

**Explicação:**
- `DATE_PART('campo', data)` é equivalente a `EXTRACT(campo FROM data)`;
- `DOW` (Day Of Week) retorna 0-6 (domingo a sábado);
- `TO_CHAR(data, 'Day')` retorna o nome do dia em inglês;
- Use CASE para traduzir números para nomes em português.

---

## Exercício 4: DATE_TRUNC - Truncamento de datas

```sql
-- a) Truncamento em diferentes níveis
SELECT 
  data_pedido AS original,
  DATE_TRUNC('hour', data_pedido) AS truncado_hora,
  DATE_TRUNC('day', data_pedido) AS truncado_dia,
  DATE_TRUNC('month', data_pedido) AS truncado_mes,
  DATE_TRUNC('year', data_pedido) AS truncado_ano
FROM vendas
LIMIT 3;

-- Resultado:
--       original        |    truncado_hora    |   truncado_dia   |   truncado_mes   |  truncado_ano
-- ----------------------+---------------------+------------------+------------------+------------------
--  2024-01-15 14:23:45  | 2024-01-15 14:00:00 | 2024-01-15 00:00 | 2024-01-01 00:00 | 2024-01-01 00:00
--  2024-01-15 09:15:30  | 2024-01-15 09:00:00 | 2024-01-15 00:00 | 2024-01-01 00:00 | 2024-01-01 00:00
--  2024-02-10 18:45:20  | 2024-02-10 18:00:00 | 2024-02-10 00:00 | 2024-02-01 00:00 | 2024-01-01 00:00

-- b) Comparação DATE_TRUNC vs EXTRACT para agrupamento mensal
-- Com DATE_TRUNC (mais elegante):
SELECT 
  DATE_TRUNC('month', data_pedido) AS mes,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total
FROM vendas
GROUP BY mes
ORDER BY mes;

-- Resultado:
--          mes          | qtd_vendas |  total
-- ----------------------+------------+---------
--  2024-01-01 00:00:00  |          2 | 1709.90
--  2024-02-01 00:00:00  |          2 | 2430.40
--  2024-03-01 00:00:00  |          2 |  835.50
--  2024-04-01 00:00:00  |          2 | 2457.30

-- Com EXTRACT (mais verboso):
SELECT 
  EXTRACT(YEAR FROM data_pedido) AS ano,
  EXTRACT(MONTH FROM data_pedido) AS mes,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total
FROM vendas
GROUP BY ano, mes
ORDER BY ano, mes;

-- c) Relatório semanal
SELECT 
  DATE_TRUNC('week', data_pedido) AS semana_inicio,
  DATE_TRUNC('week', data_pedido) + INTERVAL '6 days' AS semana_fim,
  COUNT(*) AS qtd_vendas,
  SUM(valor_total) AS total_vendido,
  ROUND(AVG(valor_total), 2) AS ticket_medio
FROM vendas
GROUP BY semana_inicio
ORDER BY semana_inicio;

-- Resultado:
--    semana_inicio    |     semana_fim      | qtd_vendas | total_vendido | ticket_medio
-- --------------------+---------------------+------------+---------------+--------------
--  2024-01-15 00:00:00| 2024-01-21 00:00:00 |          2 |       1709.90 |       854.95
--  2024-02-05 00:00:00| 2024-02-11 00:00:00 |          1 |         89.90 |        89.90
--  2024-02-26 00:00:00| 2024-03-03 00:00:00 |          1 |       2340.50 |      2340.50
--  2024-03-04 00:00:00| 2024-03-10 00:00:00 |          1 |        678.80 |       678.80
--  2024-03-18 00:00:00| 2024-03-24 00:00:00 |          1 |        156.70 |       156.70
--  2024-04-01 00:00:00| 2024-04-07 00:00:00 |          1 |        890.00 |       890.00
--  2024-04-15 00:00:00| 2024-04-21 00:00:00 |          1 |       1567.30 |      1567.30
```

**Explicação:**
- `DATE_TRUNC()` "arredonda para baixo" mantendo o tipo timestamp;
- Mais eficiente que EXTRACT para agrupamentos porque mantém a data completa;
- No PostgreSQL, semanas começam na segunda-feira por padrão;
- Pode truncar em: microseconds, milliseconds, second, minute, hour, day, week, month, quarter, year, decade, century, millennium.

---

## Exercício 5: AGE - Calculando diferenças de tempo

```sql
-- a) Tempo de empresa
SELECT 
  nome,
  data_admissao,
  AGE(CURRENT_DATE, data_admissao) AS tempo_empresa
FROM funcionarios
ORDER BY data_admissao;

-- Resultado (assumindo hoje = 2024-12-23):
--     nome      | data_admissao |      tempo_empresa
-- --------------+---------------+------------------------
--  Bruno Costa  | 2018-07-01    | 6 years 5 mons 22 days
--  Diego Lima   | 2019-11-20    | 5 years 1 mon 3 days
--  Ana Silva    | 2020-03-15    | 4 years 9 mons 8 days
--  Carla Souza  | 2022-01-10    | 2 years 11 mons 13 days

-- b) Idade atual
SELECT 
  nome,
  data_nascimento,
  AGE(CURRENT_DATE, data_nascimento) AS idade,
  EXTRACT(YEAR FROM AGE(CURRENT_DATE, data_nascimento)) AS anos
FROM funcionarios
ORDER BY data_nascimento;

-- Resultado:
--     nome      | data_nascimento |          idade           | anos
-- --------------+-----------------+--------------------------+------
--  Bruno Costa  | 1985-12-10      | 39 years 13 days         |   39
--  Diego Lima   | 1988-03-15      | 36 years 9 mons 8 days   |   36
--  Ana Silva    | 1990-05-20      | 34 years 7 mons 3 days   |   34
--  Carla Souza  | 1995-08-25      | 29 years 3 mons 29 days  |   29

-- c) Funcionários com mais de 5 anos
SELECT 
  nome,
  data_admissao,
  AGE(CURRENT_DATE, data_admissao) AS tempo_empresa
FROM funcionarios
WHERE AGE(CURRENT_DATE, data_admissao) > INTERVAL '5 years'
ORDER BY data_admissao;

-- Resultado:
--     nome     | data_admissao |      tempo_empresa
-- -------------+---------------+------------------------
--  Bruno Costa | 2018-07-01    | 6 years 5 mons 22 days
--  Diego Lima  | 2019-11-20    | 5 years 1 mon 3 days

-- BÔNUS: Converter AGE para total de dias
SELECT 
  nome,
  CURRENT_DATE - data_admissao AS dias_trabalhados,
  ROUND((CURRENT_DATE - data_admissao) / 365.25, 1) AS anos_trabalhados
FROM funcionarios
ORDER BY dias_trabalhados DESC;

-- Resultado:
--     nome      | dias_trabalhados | anos_trabalhados
-- --------------+------------------+------------------
--  Bruno Costa  |             2366 |              6.5
--  Diego Lima   |             1859 |              5.1
--  Ana Silva    |             1744 |              4.8
--  Carla Souza  |             1078 |              3.0
```

**Explicação:**
- `AGE(data_final, data_inicial)` retorna interval legível (anos, meses, dias);
- `AGE(data)` sem segundo argumento usa CURRENT_DATE como data final;
- Pode extrair componentes do interval com EXTRACT;
- Útil para cálculos de tempo de serviço, idade, etc.

---

## Exercício 6: INTERVAL - Operações com períodos

```sql
-- a) Operações básicas com INTERVAL
SELECT 
  CURRENT_DATE AS hoje,
  CURRENT_DATE + INTERVAL '1 day' AS amanha,
  CURRENT_DATE + INTERVAL '1 week' AS proxima_semana,
  CURRENT_DATE + INTERVAL '1 month' AS proximo_mes,
  CURRENT_DATE + INTERVAL '1 year' AS proximo_ano,
  CURRENT_DATE - INTERVAL '30 days' AS trinta_dias_atras;

-- Resultado (exemplo para 2024-12-23):
--    hoje     |   amanha   | proxima_semana | proximo_mes |  proximo_ano | trinta_dias_atras
-- ------------+------------+----------------+-------------+--------------+-------------------
--  2024-12-23 | 2024-12-24 | 2024-12-30     | 2025-01-23  | 2025-12-23   | 2024-11-23

-- b) Combinando múltiplos intervalos
SELECT 
  CURRENT_DATE AS hoje,
  CURRENT_DATE + INTERVAL '2 years 3 months 15 days' AS data_futura,
  CURRENT_DATE - INTERVAL '1 year 6 months' AS data_passada;

-- Resultado:
--    hoje     |  data_futura |  data_passada
-- ------------+--------------+---------------
--  2024-12-23 | 2027-04-07   | 2023-06-23

-- c) Vendas dos últimos 60 dias
SELECT 
  cliente,
  data_pedido,
  valor_total
FROM vendas
WHERE data_pedido >= NOW() - INTERVAL '60 days'
  AND data_pedido <= NOW()
ORDER BY data_pedido DESC;

-- Para tarefas nos próximos 30 dias
SELECT 
  descricao,
  prazo,
  prazo - CURRENT_DATE AS dias_restantes
FROM tarefas
WHERE prazo BETWEEN CURRENT_DATE 
  AND CURRENT_DATE + INTERVAL '30 days'
ORDER BY prazo;

-- BÔNUS: Intervalos com horas, minutos e segundos
SELECT 
  NOW() AS agora,
  NOW() + INTERVAL '2 hours 30 minutes' AS daqui_2h30,
  NOW() - INTERVAL '45 minutes' AS quarenta_cinco_min_atras;

-- Operações aritméticas com intervalos
SELECT 
  INTERVAL '1 day' * 7 AS uma_semana,
  INTERVAL '1 hour' * 24 AS um_dia_em_horas,
  INTERVAL '2 weeks' / 2 AS uma_semana_alt;
```

**Explicação:**
- `INTERVAL` permite especificar períodos de forma legível;
- Pode combinar múltiplas unidades: 'X years Y months Z days';
- Suporta: years, months, weeks, days, hours, minutes, seconds;
- Pode multiplicar/dividir intervalos por números;
- Muito útil para cálculos de prazos e análises temporais.

---

## Exercício 7: TO_CHAR - Formatação de datas

```sql
-- a) Formatos brasileiros comuns
SELECT 
  data_pedido AS original,
  TO_CHAR(data_pedido, 'DD/MM/YYYY') AS formato_br,
  TO_CHAR(data_pedido, 'DD/MM/YY') AS formato_br_curto,
  TO_CHAR(data_pedido, 'DD-MM-YYYY') AS com_traco,
  TO_CHAR(data_pedido, 'YYYY-MM-DD') AS formato_iso
FROM vendas
LIMIT 3;

-- Resultado:
--       original        | formato_br | formato_br_curto | com_traco  | formato_iso
-- ----------------------+------------+------------------+------------+-------------
--  2024-01-15 14:23:45  | 15/01/2024 | 15/01/24         | 15-01-2024 | 2024-01-15
--  2024-01-15 09:15:30  | 15/01/2024 | 15/01/24         | 15-01-2024 | 2024-01-15
--  2024-02-10 18:45:20  | 10/02/2024 | 10/02/24         | 10-02-2024 | 2024-02-10

-- b) Formatos com hora
SELECT 
  data_pedido AS original,
  TO_CHAR(data_pedido, 'DD/MM/YYYY HH24:MI') AS data_hora_24h,
  TO_CHAR(data_pedido, 'DD/MM/YYYY HH:MI AM') AS data_hora_12h,
  TO_CHAR(data_pedido, 'HH24:MI:SS') AS apenas_hora
FROM vendas
LIMIT 3;

-- Resultado:
--       original        |   data_hora_24h   |    data_hora_12h    | apenas_hora
-- ----------------------+-------------------+---------------------+-------------
--  2024-01-15 14:23:45  | 15/01/2024 14:23  | 15/01/2024 02:23 PM | 14:23:45
--  2024-01-15 09:15:30  | 15/01/2024 09:15  | 15/01/2024 09:15 AM | 09:15:30
--  2024-02-10 18:45:20  | 10/02/2024 18:45  | 10/02/2024 06:45 PM | 18:45:20

-- c) Formatos extensos (em inglês por padrão)
SELECT 
  data_pedido AS original,
  TO_CHAR(data_pedido, 'Day, DD de Month de YYYY') AS extenso,
  TO_CHAR(data_pedido, 'Day') AS dia_semana,
  TO_CHAR(data_pedido, 'Month') AS mes_nome,
  TO_CHAR(data_pedido, 'Mon') AS mes_abreviado
FROM vendas
LIMIT 3;

-- Resultado:
--       original        |              extenso                | dia_semana |  mes_nome  | mes_abreviado
-- ----------------------+-------------------------------------+------------+------------+---------------
--  2024-01-15 14:23:45  | Monday   , 15 de January   de 2024 | Monday     | January    | Jan
--  2024-01-15 09:15:30  | Monday   , 15 de January   de 2024 | Monday     | January    | Jan
--  2024-02-10 18:45:20  | Saturday , 10 de February  de 2024 | Saturday   | February   | Feb

-- BÔNUS: Relatório formatado para apresentação
SELECT 
  cliente,
  TO_CHAR(data_pedido, 'DD/MM/YYYY') AS data,
  TO_CHAR(data_pedido, 'HH24:MI') AS hora,
  'R$ ' || TO_CHAR(valor_total, '9999.99') AS valor
FROM vendas
ORDER BY data_pedido;

-- Códigos de formato mais usados:
-- DD   = dia (01-31)
-- MM   = mês (01-12)
-- YYYY = ano (4 dígitos)
-- YY   = ano (2 dígitos)
-- HH24 = hora 24h (00-23)
-- HH   = hora 12h (01-12)
-- MI   = minutos (00-59)
-- SS   = segundos (00-59)
-- AM/PM = indicador AM/PM
-- Day  = nome do dia da semana
-- Month = nome do mês
-- Mon  = mês abreviado
```

**Explicação:**
- `TO_CHAR()` converte data/timestamp para string formatada;
- Muito útil para relatórios e apresentação de dados;
- Formato brasileiro típico: 'DD/MM/YYYY HH24:MI';
- Nomes de dia/mês vêm em inglês (pode configurar locale);
- Também pode formatar números (veremos no padrão '9999.99').

---

## Exercício 8: Fusos horários - TIMESTAMPTZ

```sql
-- a) Visualizar no fuso local (assumindo -03 Brasília)
SELECT 
  descricao,
  momento AS horario_original,
  momento::TIMESTAMP AS sem_timezone
FROM eventos_globais;

-- Resultado (convertido para -03):
--      descricao       |       horario_original      |    sem_timezone
-- ---------------------+-----------------------------+---------------------
--  Reunião SP          | 2024-12-15 10:00:00-03      | 2024-12-15 10:00:00
--  Webinar NY          | 2024-12-15 12:00:00-03      | 2024-12-15 12:00:00
--  Conferência Londres | 2024-12-15 10:00:00-03      | 2024-12-15 10:00:00
--  Workshop Tokyo      | 2024-12-14 22:00:00-03      | 2024-12-14 22:00:00

-- b) Converter todos para UTC
SELECT 
  descricao,
  momento AT TIME ZONE 'UTC' AS horario_utc
FROM eventos_globais;

-- Resultado:
--      descricao       |     horario_utc
-- ---------------------+---------------------
--  Reunião SP          | 2024-12-15 13:00:00
--  Webinar NY          | 2024-12-15 15:00:00
--  Conferência Londres | 2024-12-15 10:00:00
--  Workshop Tokyo      | 2024-12-15 01:00:00

-- c) Converter para múltiplos fusos
SELECT 
  descricao,
  momento AT TIME ZONE 'America/Sao_Paulo' AS horario_brasil,
  momento AT TIME ZONE 'America/New_York' AS horario_ny,
  momento AT TIME ZONE 'Europe/London' AS horario_londres,
  momento AT TIME ZONE 'Asia/Tokyo' AS horario_tokyo
FROM eventos_globais;

-- Resultado:
--      descricao       | horario_brasil      | horario_ny          | horario_londres     | horario_tokyo
-- ---------------------+---------------------+---------------------+---------------------+---------------------
--  Reunião SP          | 2024-12-15 10:00:00 | 2024-12-15 08:00:00 | 2024-12-15 13:00:00 | 2024-12-15 22:00:00
--  Webinar NY          | 2024-12-15 12:00:00 | 2024-12-15 10:00:00 | 2024-12-15 15:00:00 | 2024-12-16 00:00:00
--  Conferência Londres | 2024-12-15 07:00:00 | 2024-12-15 05:00:00 | 2024-12-15 10:00:00 | 2024-12-15 19:00:00
--  Workshop Tokyo      | 2024-12-14 22:00:00 | 2024-12-14 20:00:00 | 2024-12-15 01:00:00 | 2024-12-15 10:00:00

-- d) Ordem cronológica (qual acontece primeiro)
SELECT 
  descricao,
  momento,
  momento AT TIME ZONE 'UTC' AS horario_utc
FROM eventos_globais
ORDER BY momento;

-- Resultado:
--      descricao       |         momento             |     horario_utc
-- ---------------------+-----------------------------+---------------------
--  Workshop Tokyo      | 2024-12-14 22:00:00-03      | 2024-12-15 01:00:00  <- Primeiro!
--  Conferência Londres | 2024-12-15 10:00:00-03      | 2024-12-15 10:00:00
--  Reunião SP          | 2024-12-15 10:00:00-03      | 2024-12-15 13:00:00
--  Webinar NY          | 2024-12-15 12:00:00-03      | 2024-12-15 15:00:00  <- Último!

-- BÔNUS: Ver fuso horário atual da sessão
SHOW timezone;

-- Mudar temporariamente o fuso
SET timezone = 'America/New_York';
SELECT NOW();

-- Voltar para padrão
RESET timezone;
```

**Explicação:**
- `TIMESTAMP WITH TIME ZONE` (ou `TIMESTAMPTZ`) armazena em UTC internamente;
- Exibe automaticamente no fuso configurado na sessão;
- `AT TIME ZONE` converte para qualquer fuso;
- Use nomes IANA: 'America/Sao_Paulo', 'Europe/London', 'Asia/Tokyo';
- Comparações e ordenações funcionam corretamente independente do fuso.

---

## Exercício 9: Análise temporal avançada - Séries temporais

```sql
-- Gerar série de datas (todos os dias de um período)
WITH periodo AS (
  SELECT 
    MIN(DATE(data_pedido)) AS data_inicio,
    MAX(DATE(data_pedido)) AS data_fim
  FROM vendas
),
todas_datas AS (
  SELECT 
    generate_series(
      (SELECT data_inicio FROM periodo),
      (SELECT data_fim FROM periodo),
      '1 day'::INTERVAL
    )::DATE AS data
)
SELECT 
  td.data,
  TO_CHAR(td.data, 'Day') AS dia_semana,
  COALESCE(COUNT(v.id), 0) AS qtd_vendas,
  COALESCE(SUM(v.valor_total), 0) AS total_vendido
FROM todas_datas td
LEFT JOIN vendas v 
  ON DATE(v.data_pedido) = td.data
GROUP BY td.data
ORDER BY td.data;

-- Resultado (mostra TODOS os dias, mesmo sem vendas):
--    data     | dia_semana | qtd_vendas | total_vendido
-- ------------+------------+------------+---------------
--  2024-01-15 | Monday     |          2 |       1709.90
--  2024-01-16 | Tuesday    |          0 |          0.00
--  2024-01-17 | Wednesday  |          0 |          0.00
--  ...
--  2024-02-10 | Saturday   |          1 |         89.90
--  2024-02-11 | Sunday     |          0 |          0.00
--  ...

-- VERSÃO SIMPLIFICADA: Vendas por semana
WITH vendas_semanais AS (
  SELECT 
    DATE_TRUNC('week', data_pedido) AS semana,
    COUNT(*) AS qtd_vendas,
    SUM(valor_total) AS total
  FROM vendas
  GROUP BY semana
)
SELECT 
  semana,
  TO_CHAR(semana, 'DD/MM/YYYY') AS inicio_semana,
  qtd_vendas,
  total,
  ROUND(total / qtd_vendas, 2) AS ticket_medio
FROM vendas_semanais
ORDER BY semana;

-- ANÁLISE: Comparar vendas do mês atual vs mês anterior
WITH vendas_por_mes AS (
  SELECT 
    DATE_TRUNC('month', data_pedido) AS mes,
    SUM(valor_total) AS total
  FROM vendas
  GROUP BY mes
)
SELECT 
  mes,
  total AS vendas_mes,
  LAG(total) OVER (ORDER BY mes) AS vendas_mes_anterior,
  total - LAG(total) OVER (ORDER BY mes) AS diferenca,
  ROUND(
    ((total - LAG(total) OVER (ORDER BY mes)) / 
     LAG(total) OVER (ORDER BY mes) * 100), 2
  ) AS percentual_crescimento
FROM vendas_por_mes
ORDER BY mes;

-- Resultado:
--          mes          | vendas_mes | vendas_mes_anterior | diferenca | percentual_crescimento
-- ----------------------+------------+---------------------+-----------+------------------------
--  2024-01-01 00:00:00  |    1709.90 |                NULL |      NULL |                   NULL
--  2024-02-01 00:00:00  |    2430.40 |             1709.90 |    720.50 |                  42.13
--  2024-03-01 00:00:00  |     835.50 |             2430.40 |  -1594.90 |                 -65.62
--  2024-04-01 00:00:00  |    2457.30 |              835.50 |   1621.80 |                 194.08
```

**Explicação:**
- `generate_series()` cria sequência de datas;
- `LEFT JOIN` garante que todos os dias apareçam (mesmo sem vendas);
- `COALESCE()` substitui NULL por 0;
- `LAG()` window function acessa valor da linha anterior;
- Essencial para análises temporais e detecção de tendências.

---

## Exercício 10: Desafio - Sistema de agendamento com múltiplos fusos

```sql
-- a) Todos os horários em UTC
SELECT 
  titulo,
  organizador,
  fuso_organizador,
  horario_organizador,
  (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
    AT TIME ZONE 'UTC' AS horario_utc
FROM reunioes_globais
ORDER BY horario_utc;

-- Resultado:
--       titulo        |  organizador  | fuso_organizador  |  horario_organizador |     horario_utc
-- --------------------+---------------+-------------------+----------------------+---------------------
--  Daily Standup      | Ana Silva     | America/Sao_Paulo | 2024-12-20 09:00:00  | 2024-12-20 12:00:00
--  Code Review        | Yuki Tanaka   | Asia/Tokyo        | 2024-12-20 10:00:00  | 2024-12-20 01:00:00
--  All Hands Meeting  | Emma Watson   | Europe/London     | 2024-12-20 15:00:00  | 2024-12-20 15:00:00
--  Sprint Planning    | John Smith    | America/New_York  | 2024-12-20 14:00:00  | 2024-12-20 19:00:00

-- b) Converter para fuso de São Paulo
SELECT 
  titulo,
  organizador,
  (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
    AT TIME ZONE 'America/Sao_Paulo' AS horario_sao_paulo,
  fuso_organizador AS fuso_original
FROM reunioes_globais
ORDER BY horario_sao_paulo;

-- Resultado:
--       titulo        |  organizador  |   horario_sao_paulo  | fuso_original
-- --------------------+---------------+----------------------+-------------------
--  Code Review        | Yuki Tanaka   | 2024-12-19 22:00:00  | Asia/Tokyo
--  Daily Standup      | Ana Silva     | 2024-12-20 09:00:00  | America/Sao_Paulo
--  All Hands Meeting  | Emma Watson   | 2024-12-20 12:00:00  | Europe/London
--  Sprint Planning    | John Smith    | 2024-12-20 16:00:00  | America/New_York

-- c) Calcular horário de término
SELECT 
  titulo,
  (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
    AT TIME ZONE 'America/Sao_Paulo' AS inicio_sp,
  (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
    AT TIME ZONE 'America/Sao_Paulo' 
    + (duracao_minutos || ' minutes')::INTERVAL AS termino_sp,
  duracao_minutos || ' min' AS duracao
FROM reunioes_globais
ORDER BY inicio_sp;

-- Resultado:
--       titulo        |      inicio_sp      |     termino_sp      | duracao
-- --------------------+---------------------+---------------------+---------
--  Code Review        | 2024-12-19 22:00:00 | 2024-12-19 23:00:00 | 60 min
--  Daily Standup      | 2024-12-20 09:00:00 | 2024-12-20 09:15:00 | 15 min
--  All Hands Meeting  | 2024-12-20 12:00:00 | 2024-12-20 13:30:00 | 90 min
--  Sprint Planning    | 2024-12-20 16:00:00 | 2024-12-20 18:00:00 | 120 min

-- d) Identificar conflitos de horário
WITH agenda_sp AS (
  SELECT 
    id,
    titulo,
    (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
      AT TIME ZONE 'America/Sao_Paulo' AS inicio,
    (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
      AT TIME ZONE 'America/Sao_Paulo' 
      + (duracao_minutos || ' minutes')::INTERVAL AS fim
  FROM reunioes_globais
)
SELECT 
  a1.titulo AS reuniao_1,
  a1.inicio AS inicio_1,
  a1.fim AS fim_1,
  a2.titulo AS reuniao_2,
  a2.inicio AS inicio_2,
  a2.fim AS fim_2,
  'CONFLITO!' AS alerta
FROM agenda_sp a1
JOIN agenda_sp a2 
  ON a1.id < a2.id
WHERE a1.inicio < a2.fim 
  AND a1.fim > a2.inicio
ORDER BY a1.inicio;

-- Resultado (se houver conflitos):
-- Neste exemplo não há conflitos, mas a query detectaria se houvesse

-- e) Agenda formatada para apresentação
SELECT 
  ROW_NUMBER() OVER (ORDER BY horario_sp) AS ordem,
  TO_CHAR(horario_sp, 'DD/MM/YYYY') AS data,
  TO_CHAR(horario_sp, 'HH24:MI') AS horario,
  TO_CHAR(horario_sp + (duracao_minutos || ' minutes')::INTERVAL, 'HH24:MI') 
    AS termino,
  titulo,
  organizador,
  duracao_minutos || 'min' AS duracao
FROM (
  SELECT 
    titulo,
    organizador,
    duracao_minutos,
    (horario_organizador || ' ' || fuso_organizador)::TIMESTAMPTZ 
      AT TIME ZONE 'America/Sao_Paulo' AS horario_sp
  FROM reunioes_globais
) AS reunioes_convertidas
ORDER BY horario_sp;

-- Resultado (formatado para apresentação):
--  ordem |    data    | horario | termino |       titulo        |  organizador  | duracao
-- -------+------------+---------+---------+---------------------+---------------+---------
--      1 | 19/12/2024 | 22:00   | 23:00   | Code Review         | Yuki Tanaka   | 60min
--      2 | 20/12/2024 | 09:00   | 09:15   | Daily Standup       | Ana Silva     | 15min
--      3 | 20/12/2024 | 12:00   | 13:30   | All Hands Meeting   | Emma Watson   | 90min
--      4 | 20/12/2024 | 16:00   | 18:00   | Sprint Planning     | John Smith    | 120min

-- BÔNUS: Criar função para verificar disponibilidade
CREATE OR REPLACE FUNCTION verificar_disponibilidade(
  data_hora_inicio TIMESTAMPTZ,
  data_hora_fim TIMESTAMPTZ,
  fuso_participante TEXT
)
RETURNS TABLE (
  titulo VARCHAR,
  conflito BOOLEAN
) AS $$
BEGIN
  RETURN QUERY
  SELECT 
    rg.titulo,
    TRUE AS conflito
  FROM reunioes_globais rg
  WHERE (rg.horario_organizador || ' ' || rg.fuso_organizador)::TIMESTAMPTZ 
          AT TIME ZONE fuso_participante < data_hora_fim
    AND (rg.horario_organizador || ' ' || rg.fuso_organizador)::TIMESTAMPTZ 
          AT TIME ZONE fuso_participante 
          + (rg.duracao_minutos || ' minutes')::INTERVAL > data_hora_inicio;
END;
$$ LANGUAGE plpgsql;

-- Usar a função:
SELECT * FROM verificar_disponibilidade(
  '2024-12-20 09:00:00'::TIMESTAMPTZ,
  '2024-12-20 10:00:00'::TIMESTAMPTZ,
  'America/Sao_Paulo'
);
```

**Explicação completa:**

1. **Conversão de fusos:**
   - Concatena horário + fuso e converte para TIMESTAMPTZ
   - `AT TIME ZONE` converte para qualquer fuso desejado
   - Crucial para aplicações globais

2. **Cálculo de término:**
   - Adiciona INTERVAL ao horário de início
   - `duracao_minutos || ' minutes'` cria interval dinamicamente

3. **Detecção de conflitos:**
   - Self JOIN para comparar todas as reuniões entre si
   - Lógica: `inicio1 < fim2 AND fim1 > inicio2`
   - Evita duplicatas com `a1.id < a2.id`

4. **Formatação para apresentação:**
   - `TO_CHAR()` para datas legíveis
   - `ROW_NUMBER()` para numeração sequencial
   - CTEs para organizar lógica complexa

5. **Função personalizada:**
   - Encapsula lógica de verificação
   - Reutilizável em toda aplicação
   - Retorna tabela com conflitos