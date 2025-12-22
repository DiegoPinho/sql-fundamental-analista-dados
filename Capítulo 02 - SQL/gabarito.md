## Exercício 1: Criando sua primeira tabela
```sql
CREATE TABLE produtos (
  id INT,
  nome VARCHAR(100),
  preco DECIMAL(10, 2),
  estoque INT,
  descricao TEXT
);
```

---

## Exercício 2: Escolhendo tipos de dados corretos

- a) `VARCHAR(255)` - endereços de email têm tamanho limitado
- b) `DATE` - tipo específico para datas
- c) `BOOLEAN` - valores verdadeiro/falso
- d) `DECIMAL(10, 2)` - valores monetários precisam de precisão decimal
- e) `TEXT` - texto longo sem limite específico
- f) `INT` - quantidades são números inteiros

---

## Exercício 3: Identificando erros

Erros presentes no comando:

1. Falta vírgula após `id INTEGER`
2. Falta vírgula após `nome VARCHAR(200)`
3. Falta vírgula após `endereco TEXT`
4. Falta ponto e vírgula no final do comando

Comando corrigido:

```sql
CREATE TABLE clientes (
  id INTEGER,
  nome VARCHAR(200),
  telefone VARCHAR(15),
  endereco TEXT,
  ativo BOOLEAN
);
```

---

## Exercício 4: Tabela de funcionários

```sql
CREATE TABLE funcionarios (
  codigo INT,
  nome_completo VARCHAR(150),
  data_admissao DATE,
  cargo VARCHAR(80),
  salario DECIMAL(10, 2),
  observacoes TEXT
);
```

---

## Exercício 5: Verificando a estrutura

```sql
SELECT * FROM reunioes;
```

Este comando seleciona todas as colunas (`*`) e todas as linhas da tabela `reunioes`. Como a tabela está vazia, mostrará apenas os nomes das colunas, confirmando que a estrutura foi criada corretamente.

---

## Exercício 6: Tabela de vendas

```sql
CREATE TABLE vendas (
  numero_venda INT,
  data_venda DATE,
  nome_vendedor VARCHAR(100),
  nome_cliente VARCHAR(100),
  valor_total DECIMAL(10, 2),
  forma_pagamento VARCHAR(50),
  observacoes TEXT
);
```

---

## Exercício 7: Análise de tipos de dados

1. Economia de espaço: Tipos de dados apropriados ocupam apenas o espaço necessário. Por exemplo, usar `INT` para um número pequeno é mais eficiente que usar `TEXT`;

2. Validação de dados: O banco de dados pode validar automaticamente se o dado inserido é do tipo correto. Por exemplo, uma coluna do tipo `DATE` só aceitará datas válidas;

3. Performance em consultas: Operações com dados do tipo correto são mais rápidas. Por exemplo, comparar números inteiros é mais rápido que comparar textos;

4. Funcionalidades específicas: Cada tipo de dado oferece operações específicas. Por exemplo, tipos `DATE` permitem cálculos de diferença entre datas, enquanto tipos numéricos permitem operações matemáticas.

---

## Exercício 8: Tabela de livros

```sql
CREATE TABLE livros (
  codigo INT,
  titulo VARCHAR(255),
  autor VARCHAR(150),
  isbn VARCHAR(13),
  data_publicacao DATE,
  numero_paginas INT,
  disponivel BOOLEAN,
  sinopse TEXT
);
```

---

## Exercício 9: Corrigindo a sintaxe

Erros encontrados:

1. Faltam parênteses após o nome da tabela
2. Falta vírgula após `id_pedido INT`
3. Falta vírgula após `data_pedido DATE`
4. Vírgula faltando entre os parâmetros do DECIMAL (deve ser vírgula, não espaço)
5. Falta vírgula após `total DECIMAL(10, 2)`
6. Falta ponto e vírgula no final
7. Falta fechar os parênteses

Comando corrigido:

```sql
CREATE TABLE pedidos (
  id_pedido INT,
  data_pedido DATE,
  cliente VARCHAR(100),
  total DECIMAL(10, 2),
  status VARCHAR(20)
);
```

---

## Exercício 10: Projeto prático

```sql
CREATE TABLE eventos (
  id INT,
  nome VARCHAR(255),
  data_evento DATE,
  horario_inicio VARCHAR(10),
  local VARCHAR(200),
  capacidade_maxima INT,
  descricao TEXT,
  confirmado BOOLEAN
);

-- verificando a estrutura da tabela
SELECT * FROM eventos;
```

O resultado esperado será uma tabela vazia mostrando todas as colunas criadas:

```
 id | nome | data_evento | horario_inicio | local | capacidade_maxima | descricao | confirmado 
----+------+-------------+----------------+-------+-------------------+-----------+------------
(0 rows)
```