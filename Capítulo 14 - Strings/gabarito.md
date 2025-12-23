# Exercícios - Capítulo 14: Funções de String - Manipulando textos

## Exercício 1: CONCAT - Concatenação básica

```sql
-- a) Descrição completa
SELECT 
  nome,
  marca,
  preco,
  CONCAT(nome, ' - ', marca, ' (R$ ', preco, ')') AS descricao_concat,
  nome || ' - ' || marca || ' (R$ ' || preco || ')' AS descricao_pipes
FROM produtos;

-- Resultado:
--       nome        |   marca   | preco  |           descricao
-- ------------------+-----------+--------+----------------------------------------
--  Mouse Gamer      | Logitech  | 150.00 | Mouse Gamer - Logitech (R$ 150.00)
--  Teclado Mecânico | Razer     | 450.00 | Teclado Mecânico - Razer (R$ 450.00)
--  Headset          | HyperX    | 300.00 | Headset - HyperX (R$ 300.00)

-- b) Teste com NULL
SELECT 
  CONCAT('Produto: ', NULL, ' - Sem marca') AS com_concat,
  'Produto: ' || NULL || ' - Sem marca' AS com_pipes;

-- Resultado:
--      com_concat           | com_pipes
-- -------------------------+-----------
--  Produto:  - Sem marca   | NULL
--
-- CONCAT trata NULL como string vazia
-- || retorna NULL se qualquer parte for NULL
```

**Explicação:**
- `CONCAT()` é mais seguro quando há risco de valores NULL, pois os trata como strings vazias;
- `||` é mais conciso, mas retorna NULL se qualquer operando for NULL;
- Podemos concatenar números diretamente - SQL converte automaticamente para string.

---

## Exercício 2: UPPER e LOWER - Padronização de texto

```sql
-- a) E-mails em minúsculas
SELECT 
  nome,
  email AS email_original,
  LOWER(email) AS email_padronizado
FROM clientes;

-- Resultado:
--       nome        |      email_original      |   email_padronizado
-- ------------------+--------------------------+----------------------
--  Ricardo Pereira  | RICARDO@abc.com          | ricardo@abc.com
--  Juliana Costa    | ju.costa@abc.com         | ju.costa@abc.com
--  MARCOS SANTOS    | marcos@abc.com           | marcos@abc.com
--  paulo oliveira   | p_oliveira@abc.com       | p_oliveira@abc.com

-- b) Cidades padronizadas (primeira letra maiúscula)
-- PostgreSQL tem INITCAP() que capitaliza primeira letra de cada palavra
SELECT 
  cidade AS cidade_original,
  INITCAP(cidade) AS cidade_padronizada
FROM clientes;

-- Resultado:
--  cidade_original | cidade_padronizada
-- -----------------+--------------------
--  São Paulo       | São Paulo
--  sao paulo       | Sao Paulo
--  Salvador        | Salvador
--  salvador        | Salvador

-- c) Busca case-insensitive
SELECT 
  nome,
  cidade
FROM clientes
WHERE LOWER(cidade) = LOWER('São Paulo');
-- ou
WHERE UPPER(cidade) = UPPER('SÃO PAULO');

-- Resultado:
--       nome        |  cidade
-- ------------------+-----------
--  Ricardo Pereira  | São Paulo
--  Juliana Costa    | sao paulo
```

**Explicação:**
- `LOWER()` e `UPPER()` são essenciais para comparações que ignoram capitalização;
- `INITCAP()` no PostgreSQL capitaliza a primeira letra de cada palavra;
- Usar funções de case em WHERE permite encontrar registros independente de como foram escritos.

---

## Exercício 3: LENGTH - Validação de tamanho

```sql
-- a) Nomes longos (> 15 caracteres)
SELECT 
  nome,
  LENGTH(nome) AS tamanho
FROM clientes
WHERE LENGTH(nome) > 15
ORDER BY tamanho DESC;

-- Resultado:
--       nome         | tamanho
-- -------------------+---------
--   Ricardo Pereira  |      20  (tem espaços extras!)
--  paulo oliveira    |      14  (não aparece, < 15)

-- b) E-mails suspeitos (< 10 caracteres)
SELECT 
  email,
  LENGTH(email) AS tamanho
FROM clientes
WHERE LENGTH(email) < 10;

-- Resultado:
-- (Provavelmente vazio, pois e-mails válidos têm pelo menos usuario@dom.com = 11 chars)

-- c) Identificar espaços extras
SELECT 
  nome,
  LENGTH(nome) AS tamanho_com_espacos,
  LENGTH(TRIM(nome)) AS tamanho_sem_espacos,
  LENGTH(nome) - LENGTH(TRIM(nome)) AS espacos_extras
FROM clientes
WHERE LENGTH(nome) != LENGTH(TRIM(nome));

-- Resultado:
--       nome         | tam_com | tam_sem | extras
-- -------------------+---------+---------+--------
--   Ricardo Pereira  |      20 |      17 |      3
```

**Explicação:**
- `LENGTH()` retorna o número de caracteres incluindo espaços;
- Comparar `LENGTH(nome)` com `LENGTH(TRIM(nome))` revela espaços extras;
- Útil para validação de dados e identificação de problemas de qualidade.

---

## Exercício 4: TRIM - Limpeza de espaços

```sql
-- a) Visualizar nomes limpos
SELECT 
  nome AS nome_original,
  TRIM(nome) AS nome_limpo,
  CONCAT('[', nome, ']') AS com_colchetes_antes,
  CONCAT('[', TRIM(nome), ']') AS com_colchetes_depois
FROM clientes;

-- Resultado:
--    nome_original     |   nome_limpo     | com_colchetes_antes  | com_colchetes_depois
-- ---------------------+------------------+----------------------+---------------------
--   Ricardo Pereira    | Ricardo Pereira  | [ Ricardo Pereira ]  | [Ricardo Pereira]
--  Juliana Costa       | Juliana Costa    | [Juliana Costa]      | [Juliana Costa]

-- b) Diferença entre TRIM, LTRIM, RTRIM
SELECT 
  '  exemplo  ' AS original,
  TRIM('  exemplo  ') AS com_trim,
  LTRIM('  exemplo  ') AS com_ltrim,
  RTRIM('  exemplo  ') AS com_rtrim;

-- Resultado:
--   original    | com_trim | com_ltrim   | com_rtrim
-- --------------+----------+-------------+------------
--    exemplo    | exemplo  | exemplo     |   exemplo
-- (espaços)     (limpo)    (só direita)  (só esquerda)

-- c) UPDATE para limpar dados
-- SEMPRE teste com SELECT antes de UPDATE!
SELECT id, nome, TRIM(nome) AS nome_limpo
FROM clientes
WHERE nome != TRIM(nome);

-- Se estiver correto, faça o UPDATE:
UPDATE clientes
SET nome = TRIM(nome)
WHERE nome != TRIM(nome);

-- Verificar:
SELECT nome FROM clientes;
```

**Explicação:**
- `TRIM()`: Remove espaços do início E do fim;
- `LTRIM()`: Remove apenas do início (Left);
- `RTRIM()`: Remove apenas do fim (Right);
- Sempre teste com SELECT antes de UPDATE para evitar erros!

---

## Exercício 5: SUBSTRING - Extração de partes

```sql
-- a) Categoria (primeiros 3 caracteres)
SELECT 
  codigo,
  SUBSTRING(codigo FROM 1 FOR 3) AS categoria,
  -- ou sintaxe alternativa:
  SUBSTR(codigo, 1, 3) AS categoria_alt
FROM cod_produtos;

-- Resultado:
--     codigo      | categoria
-- ----------------+-----------
--  ELE-2024-001   | ELE
--  MOV-2024-055   | MOV
--  ELE-2023-120   | ELE
--  LIV-2024-003   | LIV

-- b) Ano (posição 5, tamanho 4)
SELECT 
  codigo,
  SUBSTRING(codigo FROM 5 FOR 4) AS ano
FROM cod_produtos;

-- Resultado:
--     codigo      | ano
-- ----------------+------
--  ELE-2024-001   | 2024
--  MOV-2024-055   | 2024
--  ELE-2023-120   | 2023
--  LIV-2024-003   | 2024

-- c) Número (últimos 3 caracteres)
-- Opção 1: Sabendo que são os últimos 3
SELECT 
  codigo,
  SUBSTRING(codigo FROM LENGTH(codigo) - 2) AS numero
FROM cod_produtos;

-- Opção 2: Sabendo a posição fixa (posição 10)
SELECT 
  codigo,
  SUBSTRING(codigo FROM 10 FOR 3) AS numero
FROM cod_produtos;

-- Resultado:
--     codigo      | numero
-- ----------------+--------
--  ELE-2024-001   | 001
--  MOV-2024-055   | 055
--  ELE-2023-120   | 120
--  LIV-2024-003   | 003

-- BÔNUS: Todas as partes de uma vez
SELECT 
  codigo,
  SUBSTRING(codigo FROM 1 FOR 3) AS categoria,
  SUBSTRING(codigo FROM 5 FOR 4) AS ano,
  SUBSTRING(codigo FROM 10 FOR 3) AS numero
FROM cod_produtos;
```

**Explicação:**
- `SUBSTRING(string FROM inicio FOR tamanho)` é a sintaxe SQL padrão;
- `SUBSTR(string, inicio, tamanho)` é uma alternativa mais curta;
- Posições começam em 1, não em 0;
- `LENGTH(codigo) - 2` pega os últimos 3 caracteres independente do tamanho total.

---

## Exercício 6: POSITION e SUBSTRING - Extração dinâmica

```sql
-- a) Domínio do e-mail (após @)
SELECT 
  email,
  POSITION('@' IN email) AS posicao_arroba,
  SUBSTRING(email FROM POSITION('@' IN email) + 1) AS dominio
FROM clientes;

-- Resultado:
--          email           | posicao_arroba |  dominio
-- -------------------------+----------------+-----------
--  ricardo@abc.com         |              8 | abc.com
--  ju.costa@abc.com        |              9 | abc.com
--  marcos@abc.com          |              7 | abc.com
--  p_oliveira@abc.com      |             11 | abc.com

-- b) Nome de usuário (antes do @)
SELECT 
  email,
  SUBSTRING(email FROM 1 FOR POSITION('@' IN email) - 1) AS usuario
FROM clientes;

-- Resultado:
--          email           |  usuario
-- -------------------------+------------
--  ricardo@abc.com         | ricardo
--  ju.costa@abc.com        | ju.costa
--  marcos@abc.com          | marcos
--  p_oliveira@abc.com      | p_oliveira

-- c) Primeiro nome (antes do primeiro espaço)
SELECT 
  nome,
  CASE 
    WHEN POSITION(' ' IN TRIM(nome)) > 0 THEN
      SUBSTRING(TRIM(nome) FROM 1 FOR POSITION(' ' IN TRIM(nome)) - 1)
    ELSE
      TRIM(nome)
  END AS primeiro_nome
FROM clientes;

-- Resultado:
--       nome         | primeiro_nome
-- -------------------+---------------
--  Ricardo Pereira   | Ricardo
--  Juliana Costa     | Juliana
--  MARCOS SANTOS     | MARCOS
--  paulo oliveira    | paulo

-- Versão simplificada (assumindo que sempre há espaço):
SELECT 
  TRIM(nome) AS nome_completo,
  SUBSTRING(TRIM(nome) FROM 1 FOR POSITION(' ' IN TRIM(nome)) - 1) AS primeiro_nome
FROM clientes
WHERE POSITION(' ' IN TRIM(nome)) > 0;
```

**Explicação:**
- `POSITION(substring IN string)` retorna a posição da primeira ocorrência;
- Retorna 0 se não encontrar a substring;
- Muito útil combinado com SUBSTRING para extração dinâmica;
- O `CASE` trata nomes sem espaço (que não têm sobrenome).

---

## Exercício 7: REPLACE - Substituição e limpeza

```sql
-- a) Padronizar telefones (remover formatação)
SELECT 
  nome,
  telefone AS original,
  REPLACE(
    REPLACE(
      REPLACE(
        REPLACE(telefone, '(', ''),
      ')', ''),
    ' ', ''),
  '-', '') AS telefone_limpo
FROM contatos;

-- Resultado:
--  nome  |      original       | telefone_limpo
-- -------+---------------------+----------------
--  Ana   | (11) 98765-4321     | 11987654321
--  Bruno | 11987654321         | 11987654321
--  Carla | (21) 9 9876-5432    | 2199876543 2
--  Diego | 21-99876-5432       | 2199876543 2

-- b) Substituir domínio de e-mail
SELECT 
  email AS email_antigo,
  REPLACE(email, '@abc.com', '@empresa.com.br') AS email_novo
FROM clientes;

-- Resultado:
--     email_antigo        |       email_novo
-- ------------------------+---------------------------
--  ricardo@abc.com        | ricardo@empresa.com.br
--  ju.costa@abc.com       | ju.costa@empresa.com.br
--  marcos@abc.com         | marcos@empresa.com.br

-- c) Remover acentos (exemplo com São Paulo)
SELECT 
  cidade AS original,
  REPLACE(
    REPLACE(cidade, 'ã', 'a'),
  'õ', 'o') AS sem_acento
FROM clientes
WHERE cidade LIKE '%ão%' OR cidade LIKE '%ã%';

-- Resultado:
--   original   | sem_acento
-- -------------+------------
--  São Paulo   | Sao Paulo

-- BÔNUS: Função completa para remover múltiplos acentos
SELECT 
  REPLACE(
    REPLACE(
      REPLACE(
        REPLACE(
          REPLACE(
            REPLACE(cidade, 'á', 'a'),
          'à', 'a'),
        'ã', 'a'),
      'é', 'e'),
    'í', 'i'),
  'ó', 'o') AS sem_acentos
FROM clientes;
```

**Explicação:**
- `REPLACE()` substitui TODAS as ocorrências, não apenas a primeira;
- Podemos encadear múltiplos REPLACE() para fazer várias substituições;
- Útil para padronização, limpeza e normalização de dados;
- Para remover caracteres, use string vazia como substituição: `REPLACE(str, 'X', '')`.

---

## Exercício 8: SPLIT_PART - Dividir strings

```sql
-- SPLIT_PART divide string por delimitador e retorna a parte N
-- Sintaxe: SPLIT_PART(string, delimitador, posicao)

SELECT 
  endereco_completo,
  SPLIT_PART(endereco_completo, ', ', 1) AS rua,
  SPLIT_PART(endereco_completo, ', ', 2) AS numero,
  SPLIT_PART(endereco_completo, ', ', 3) AS bairro,
  SPLIT_PART(endereco_completo, ', ', 4) AS cidade,
  SPLIT_PART(endereco_completo, ', ', 5) AS estado
FROM enderecos;

-- Resultado:
--                    endereco_completo                    |        rua         | numero |      bairro       |   cidade   | estado
-- ---------------------------------------------------------+--------------------+--------+-------------------+------------+--------
--  Rua das Flores, 123, Jardim Primavera, São Paulo, SP   | Rua das Flores     | 123    | Jardim Primavera  | São Paulo  | SP
--  Av. Paulista, 1000, Bela Vista, São Paulo, SP          | Av. Paulista       | 1000   | Bela Vista        | São Paulo  | SP
--  Rua XV de Novembro, 50, Centro, Salvador, BA           | Rua XV de Novembro | 50     | Centro            | Salvador   | BA

-- Exemplo: Buscar todos de SP
SELECT 
  SPLIT_PART(endereco_completo, ', ', 4) AS cidade,
  SPLIT_PART(endereco_completo, ', ', 5) AS estado,
  COUNT(*) AS total
FROM enderecos
WHERE SPLIT_PART(endereco_completo, ', ', 5) = 'SP'
GROUP BY cidade, estado;

-- Resultado:
--   cidade    | estado | total
-- ------------+--------+-------
--  São Paulo  | SP     |     2
```

**Explicação:**
- `SPLIT_PART()` é específico do PostgreSQL (em outros bancos pode ser diferente);
- Divide a string pelo delimitador e retorna a parte na posição especificada;
- Posições começam em 1;
- Muito útil para dados em CSV ou com padrões de separação;
- Se a posição não existir, retorna string vazia.

---

## Exercício 9: Combinação de funções - Limpeza completa

```sql
SELECT 
  -- Dados originais
  id,
  nome AS nome_original,
  email AS email_original,
  cidade AS cidade_original,
  
  -- 1 e 2: Nome limpo e padronizado
  INITCAP(TRIM(nome)) AS nome_limpo,
  
  -- 3: E-mail em minúsculas
  LOWER(email) AS email_padronizado,
  
  -- 4: Domínio do e-mail
  SUBSTRING(
    LOWER(email) 
    FROM POSITION('@' IN email) + 1
  ) AS dominio,
  
  -- 5: Cidade padronizada
  INITCAP(cidade) AS cidade_padronizada,
  
  -- 6: Identificador único (3 letras + 4 números do ID)
  CONCAT(
    UPPER(SUBSTRING(TRIM(nome) FROM 1 FOR 3)),
    LPAD(id::TEXT, 4, '0')
  ) AS identificador_unico
  
FROM clientes;

-- Resultado:
--  id | nome_original    | email_original      | cidade_original | nome_limpo       | email_padronizado   | dominio | cidade_padronizada | identificador_unico
-- ----+------------------+---------------------+-----------------+------------------+---------------------+---------+--------------------+--------------------
--   1 | Ricardo Pereira  | RICARDO@abc.com     | São Paulo       | Ricardo Pereira  | ricardo@abc.com     | abc.com | São Paulo          | RIC0001
--   2 | Juliana Costa    | ju.costa@abc.com    | sao paulo       | Juliana Costa    | ju.costa@abc.com    | abc.com | Sao Paulo          | JUL0002
--   3 | MARCOS SANTOS    | marcos@abc.com      | Salvador        | Marcos Santos    | marcos@abc.com      | abc.com | Salvador           | MAR0003
--   4 | paulo oliveira   | p_oliveira@abc.com  | salvador        | Paulo Oliveira   | p_oliveira@abc.com  | abc.com | Salvador           | PAU0004

-- EXPLICAÇÃO das funções usadas:
-- TRIM(): Remove espaços extras
-- INITCAP(): Primeira letra maiúscula em cada palavra
-- LOWER(): Converte para minúsculas
-- UPPER(): Converte para maiúsculas
-- SUBSTRING(): Extrai parte da string
-- POSITION(): Encontra posição do @
-- CONCAT(): Junta strings
-- LPAD(): Preenche à esquerda (4 dígitos com zeros)
-- ::TEXT: Converte número para texto

-- Versão para UPDATE (aplicar limpeza permanentemente):
UPDATE clientes
SET 
  nome = INITCAP(TRIM(nome)),
  email = LOWER(email),
  cidade = INITCAP(cidade);
```

**Explicação:**
Esta é uma consulta de "ETL" (Extract, Transform, Load) básica:
- Combina múltiplas funções de string para limpeza completa;
- `LPAD(id::TEXT, 4, '0')` converte ID para texto e preenche com zeros à esquerda;
- Demonstra pipeline de transformação: dado bruto → múltiplas funções → dado limpo;
- Fundamental para preparação de dados antes de análises.

---

## Exercício 10: Desafio - Validação e formatação de CPF

```sql
-- Consulta completa de validação e formatação de CPF

WITH cpf_limpo AS (
  -- Passo 1: Remover toda formatação
  SELECT 
    id,
    nome,
    cpf AS cpf_original,
    REPLACE(
      REPLACE(
        REPLACE(
          REPLACE(cpf, '.', ''),
        '-', ''),
      ' ', ''),
    '/', '') AS cpf_apenas_numeros
  FROM cadastros
),
cpf_validado AS (
  -- Passo 2: Validar tamanho (deve ter 11 dígitos)
  SELECT 
    *,
    LENGTH(cpf_apenas_numeros) AS tamanho,
    CASE 
      WHEN LENGTH(cpf_apenas_numeros) = 11 THEN 'VÁLIDO'
      WHEN LENGTH(cpf_apenas_numeros) < 11 THEN 'CURTO DEMAIS'
      ELSE 'LONGO DEMAIS'
    END AS status_validacao
  FROM cpf_limpo
)
-- Passo 3: Aplicar formatação padrão para CPFs válidos
SELECT 
  id,
  nome,
  cpf_original,
  cpf_apenas_numeros,
  tamanho,
  status_validacao,
  CASE 
    WHEN status_validacao = 'VÁLIDO' THEN
      CONCAT(
        SUBSTRING(cpf_apenas_numeros FROM 1 FOR 3), '.',
        SUBSTRING(cpf_apenas_numeros FROM 4 FOR 3), '.',
        SUBSTRING(cpf_apenas_numeros FROM 7 FOR 3), '-',
        SUBSTRING(cpf_apenas_numeros FROM 10 FOR 2)
      )
    ELSE 
      'CPF INVÁLIDO'
  END AS cpf_formatado
FROM cpf_validado
ORDER BY status_validacao, nome;

-- Resultado:
--  id |    nome     |   cpf_original    | cpf_apenas_numeros | tamanho | status_validacao |  cpf_formatado
-- ----+-------------+-------------------+--------------------+---------+------------------+-----------------
--   1 | Ana Silva   | 123.456.789-00    | 12345678900        |      11 | VÁLIDO           | 123.456.789-00
--   2 | Bruno Costa | 12345678900       | 12345678900        |      11 | VÁLIDO           | 123.456.789-00
--   3 | Carla Souza | 111.222.333-44    | 11122233344        |      11 | VÁLIDO           | 111.222.333-44
--   4 | Diego Lima  | 555-666-777-88    | 55566677788        |      11 | VÁLIDO           | 555.666.777-88
--   5 | Elena Rocha | 999 888 777 66    | 99988877766        |      11 | VÁLIDO           | 999.888.777-66

-- VERSÃO SIMPLIFICADA (sem CTE):
SELECT 
  nome,
  cpf AS original,
  
  -- CPF limpo (só números)
  REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') AS limpo,
  
  -- Validação
  CASE 
    WHEN LENGTH(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '')) = 11 
    THEN 'OK'
    ELSE 'INVÁLIDO'
  END AS status,
  
  -- Formatação (só se válido)
  CASE 
    WHEN LENGTH(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '')) = 11 THEN
      CONCAT(
        SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 1 FOR 3), '.',
        SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 4 FOR 3), '.',
        SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 7 FOR 3), '-',
        SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 10 FOR 2)
      )
    ELSE NULL
  END AS cpf_padronizado
FROM cadastros;

-- APLICAÇÃO PRÁTICA: UPDATE para corrigir os dados
UPDATE cadastros
SET cpf = CONCAT(
  SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 1 FOR 3), '.',
  SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 4 FOR 3), '.',
  SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 7 FOR 3), '-',
  SUBSTRING(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '') FROM 10 FOR 2)
)
WHERE LENGTH(REPLACE(REPLACE(REPLACE(REPLACE(cpf, '.', ''), '-', ''), ' ', ''), '/', '')) = 11;

-- BÔNUS: Criar função reutilizável
CREATE OR REPLACE FUNCTION formatar_cpf(cpf_input TEXT)
RETURNS TEXT AS $$
DECLARE
  cpf_limpo TEXT;
BEGIN
  -- Remover formatação
  cpf_limpo := REPLACE(REPLACE(REPLACE(REPLACE(cpf_input, '.', ''), '-', ''), ' ', ''), '/', '');
  
  -- Validar tamanho
  IF LENGTH(cpf_limpo) != 11 THEN
    RETURN NULL;
  END IF;
  
  -- Formatar
  RETURN CONCAT(
    SUBSTRING(cpf_limpo FROM 1 FOR 3), '.',
    SUBSTRING(cpf_limpo FROM 4 FOR 3), '.',
    SUBSTRING(cpf_limpo FROM 7 FOR 3), '-',
    SUBSTRING(cpf_limpo FROM 10 FOR 2)
  );
END;
$$ LANGUAGE plpgsql;

-- Usar a função:
SELECT 
  nome,
  cpf AS original,
  formatar_cpf(cpf) AS cpf_formatado
FROM cadastros;
```

**Explicação detalhada:**

1. **Limpeza (Remoção de formatação):**
   - Múltiplos `REPLACE()` encadeados removem `.`, `-`, espaços e `/`;
   - Deixa apenas os dígitos numéricos.

2. **Validação:**
   - `LENGTH()` verifica se tem exatamente 11 caracteres;
   - `CASE` categoriza como VÁLIDO, CURTO ou LONGO.

3. **Formatação padrão:**
   - `SUBSTRING()` divide em blocos: 3 + 3 + 3 + 2;
   - `CONCAT()` junta com separadores corretos (`.` e `-`).

4. **CTE (Common Table Expression):**
   - `WITH` cria "tabelas temporárias" para organizar a lógica;
   - Facilita leitura e manutenção;
   - Evita repetição de código.

5. **Função personalizada:**
   - Encapsula a lógica para reutilização;
   - Pode ser chamada em qualquer consulta: `formatar_cpf(cpf)`;
   - Retorna NULL se inválido.

**Casos de uso no mundo real:**
- Importação de dados de fontes diversas (cada uma com formato diferente);
- Padronização de dados legados;
- Validação em ETL antes de carregar em data warehouse;
- Limpeza de dados de formulários web (usuários digitam de várias formas);
- Preparação para envio a APIs externas que exigem formato específico.

**Extensões possíveis:**
- Adicionar validação de dígitos verificadores do CPF;
- Criar funções similares para CNPJ, telefone, CEP;
- Implementar regex para validações mais complexas;
- Criar triggers para validar automaticamente em INSERT/UPDATE.

Este exercício demonstra como funções de string são essenciais para garantir qualidade e consistência de dados em sistemas reais!