# Exercícios - Capítulo 16: Expressões e Operadores Avançados

## Exercício 1: CASE WHEN - Classificação e categorização

Crie uma tabela de produtos com preços e estoque:

```sql
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  preco DECIMAL(10,2),
  estoque INTEGER,
  categoria VARCHAR(50)
);

INSERT INTO produtos (nome, preco, estoque, categoria)
VALUES 
  ('Notebook', 3500.00, 5, 'Eletrônicos'),
  ('Mouse', 45.00, 150, 'Eletrônicos'),
  ('Cadeira Gamer', 1200.00, 8, 'Móveis'),
  ('Mesa', 800.00, 3, 'Móveis'),
  ('Headset', 250.00, 0, 'Eletrônicos'),
  ('Teclado Mecânico', 450.00, 25, 'Eletrônicos'),
  ('Monitor 27"', 1500.00, 12, 'Eletrônicos'),
  ('Webcam', 350.00, NULL, 'Eletrônicos');
```

a) Use CASE WHEN para classificar produtos por faixa de preço: 'Barato' (< 100), 'Médio' (100-1000), 'Caro' (> 1000)
b) Classifique o nível de estoque: 'Esgotado' (0), 'Baixo' (1-10), 'Médio' (11-50), 'Alto' (> 50), 'Indefinido' (NULL)
c) Crie uma coluna 'status_venda' que combine preço e estoque: 'Promoção' (barato e alto estoque), 'Normal', 'Atenção' (estoque baixo)

---

## Exercício 2: COALESCE - Tratamento de valores NULL

Crie uma tabela de funcionários com alguns dados faltantes:

```sql
CREATE TABLE funcionarios (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  salario DECIMAL(10,2),
  bonus DECIMAL(10,2),
  comissao DECIMAL(10,2),
  departamento VARCHAR(50)
);

INSERT INTO funcionarios (nome, salario, bonus, comissao, departamento)
VALUES 
  ('Ana Silva', 5000.00, 1000.00, 500.00, 'Vendas'),
  ('Bruno Costa', 4500.00, NULL, 800.00, 'Vendas'),
  ('Carla Souza', 6000.00, 1500.00, NULL, 'TI'),
  ('Diego Lima', 3500.00, NULL, NULL, NULL),
  ('Elena Rocha', 5500.00, 800.00, 600.00, 'Marketing');
```

a) Calcule o salário total (salário + bônus + comissão) tratando NULL como zero
b) Mostre o departamento, usando 'Não Atribuído' quando for NULL
c) Crie uma coluna de remuneração garantida usando o primeiro valor disponível entre: comissão, bônus, ou 500 (mínimo)

---

## Exercício 3: NULLIF - Evitando divisão por zero

Crie uma tabela de vendas por vendedor:

```sql
CREATE TABLE vendas_vendedor (
  id SERIAL PRIMARY KEY,
  vendedor VARCHAR(100),
  meta_mensal DECIMAL(10,2),
  vendas_realizadas DECIMAL(10,2),
  qtd_clientes INTEGER
);

INSERT INTO vendas_vendedor (vendedor, meta_mensal, vendas_realizadas, qtd_clientes)
VALUES 
  ('João', 50000.00, 65000.00, 25),
  ('Maria', 50000.00, 48000.00, 18),
  ('Pedro', 50000.00, 50000.00, 0),  -- Nenhum cliente!
  ('Ana', 50000.00, 0.00, 0),        -- Sem vendas!
  ('Carlos', 50000.00, 72000.00, 30);
```

a) Calcule o percentual de atingimento da meta evitando divisão por zero
b) Calcule o ticket médio (vendas / qtd_clientes) evitando divisão por zero
c) Crie uma classificação que considere tanto atingimento quanto ticket médio

---

## Exercício 4: Operador módulo (%) - Distribuição e padrões

Crie uma tabela de pedidos:

```sql
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(100),
  valor DECIMAL(10,2),
  data_pedido DATE
);

INSERT INTO pedidos (cliente, valor, data_pedido)
VALUES 
  ('Cliente A', 150.00, '2024-12-01'),
  ('Cliente B', 200.00, '2024-12-02'),
  ('Cliente C', 180.00, '2024-12-03'),
  ('Cliente D', 300.00, '2024-12-04'),
  ('Cliente E', 250.00, '2024-12-05'),
  ('Cliente F', 175.00, '2024-12-06'),
  ('Cliente G', 220.00, '2024-12-07'),
  ('Cliente H', 190.00, '2024-12-08'),
  ('Cliente I', 280.00, '2024-12-09'),
  ('Cliente J', 160.00, '2024-12-10');
```

a) Distribua os pedidos entre 3 equipes de atendimento usando o operador módulo
b) Identifique pedidos com ID par e ímpar
c) Distribua pedidos em grupos de 5 (0-4, 5-9, 10-14...) usando módulo e divisão inteira

---

## Exercício 5: Funções matemáticas - Cálculos complexos

Crie uma tabela de investimentos:

```sql
CREATE TABLE investimentos (
  id SERIAL PRIMARY KEY,
  cliente VARCHAR(100),
  valor_inicial DECIMAL(10,2),
  taxa_anual DECIMAL(5,2),  -- Percentual
  anos INTEGER
);

INSERT INTO investimentos (cliente, valor_inicial, taxa_anual, anos)
VALUES 
  ('Ana', 10000.00, 8.5, 5),
  ('Bruno', 25000.00, 7.2, 10),
  ('Carla', 50000.00, 6.8, 3),
  ('Diego', 15000.00, 9.0, 7),
  ('Elena', 30000.00, 7.5, 4);
```

a) Calcule o valor futuro usando juros compostos: VF = VP × (1 + taxa)^anos
b) Use ROUND, CEIL e FLOOR para diferentes arredondamentos
c) Calcule o ganho percentual total e a raiz quadrada da taxa (análise de risco)
d) Use ABS para calcular diferenças absolutas entre investimentos