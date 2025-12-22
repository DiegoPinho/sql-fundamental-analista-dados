# Exercícios - Capítulo 09: Funções Agregadoras

## Exercício 1: COUNT básico

Você trabalha em uma empresa de e-commerce e precisa saber quantos produtos estão cadastrados no catálogo. A tabela `produtos` contém todos os produtos da loja.

Escreva uma consulta SQL que retorne o número total de produtos cadastrados.

---

## Exercício 2: COUNT com coluna específica

Na mesma tabela `produtos`, existe uma coluna chamada `descricao` que pode conter valores `NULL` para produtos que ainda não tiveram suas descrições cadastradas.

Escreva duas consultas:
a) Uma que conte o total de produtos;
b) Outra que conte apenas os produtos que têm descrição cadastrada.

---

## Exercício 3: COUNT DISTINCT

A tabela `vendas` registra todas as transações da loja. Cada venda tem um `cliente_id` associado. Um mesmo cliente pode fazer várias compras.

Escreva consultas SQL que retornem:
a) O número total de vendas;
b) O número de clientes únicos que fizeram compras.

---

## Exercício 4: COUNT com WHERE

A tabela `pedidos` tem uma coluna `status` que pode ter os valores: 'pendente', 'processando', 'enviado', 'entregue' ou 'cancelado'.

Escreva consultas SQL que retornem:
a) O número total de pedidos;
b) O número de pedidos entregues;
c) O número de pedidos cancelados;
d) O número de pedidos ativos (não cancelados).

---

## Exercício 5: SUM básico

A tabela `vendas` possui uma coluna `valor_total` que registra o valor de cada venda.

Escreva uma consulta SQL que calcule o faturamento total da empresa (soma de todas as vendas).

---

## Exercício 6: SUM com WHERE

A tabela `vendas` também possui uma coluna `data_venda` do tipo DATE.

Escreva consultas SQL que calculem:
a) O faturamento total de março de 2024;
b) O faturamento total do primeiro trimestre de 2024 (janeiro a março).

---

## Exercício 7: AVG básico

A tabela `produtos` tem uma coluna `preco` que indica o preço de cada produto.

Escreva uma consulta SQL que calcule o preço médio dos produtos, arredondado para 2 casas decimais.

---

## Exercício 8: AVG com NULL

A tabela `avaliacoes` contém avaliações de produtos feitas pelos clientes. A coluna `nota` pode ter valores de 1 a 5, ou `NULL` para produtos ainda não avaliados.

Dados de exemplo:
- Produto A: notas 5, 4, 5, NULL, 3
- Produto B: notas 2, 3, NULL, 4

a) Explique como o PostgreSQL calcula a média quando há valores `NULL`;
b) Escreva a consulta para calcular a nota média de todos os produtos.

---

## Exercício 9: MIN e MAX com números

A tabela `produtos` tem uma coluna `estoque` que indica a quantidade em estoque de cada produto.

Escreva uma consulta SQL que mostre:
- O menor estoque entre todos os produtos;
- O maior estoque entre todos os produtos;
- A diferença entre o maior e o menor estoque.

---

## Exercício 10: MIN e MAX com datas

A tabela `pedidos` tem uma coluna `data_pedido` do tipo DATE.

Escreva uma consulta SQL que mostre:
a) A data do primeiro pedido registrado;
b) A data do último pedido registrado;
c) Quantos dias se passaram entre o primeiro e o último pedido.

---

## Exercício 11: MIN e MAX com texto

A tabela `clientes` tem uma coluna `nome` com os nomes dos clientes.

Escreva uma consulta SQL que mostre:
a) O primeiro nome em ordem alfabética;
b) O último nome em ordem alfabética.

---

## Exercício 12: Múltiplas funções agregadoras - Estatísticas de vendas

A tabela `vendas` tem as colunas `id`, `data_venda`, `valor_total` e `desconto_aplicado`.

Crie um relatório estatístico completo das vendas que mostre:
- Número total de vendas;
- Faturamento total (soma dos valores);
- Ticket médio (valor médio por venda), arredondado para 2 decimais;
- Menor e maior valor de venda;
- Total de descontos concedidos.

---

## Exercício 13: Estatísticas condicionais

A tabela `vendas` tem uma coluna `forma_pagamento` que pode ser 'cartão', 'dinheiro', 'pix' ou 'boleto'.

Escreva consultas separadas que mostrem estatísticas apenas para vendas pagas com PIX:
a) Número de vendas com PIX;
b) Faturamento total com PIX;
c) Ticket médio das vendas com PIX;
d) Menor e maior valor de venda com PIX.

Depois, combine todas em uma única consulta.

---

## Exercício 14: Comparação de períodos

A tabela `vendas` registra vendas ao longo do tempo na coluna `data_venda`.

Crie um relatório que compare as vendas de dois meses:
- Março de 2024;
- Abril de 2024.

Para cada mês, mostre:
- Número de vendas;
- Faturamento total;
- Ticket médio.

---

## Exercício 15: Análise de estoque

A tabela `produtos` tem as colunas `id`, `nome`, `categoria`, `preco`, `estoque` e `custo`.

Crie um relatório gerencial que mostre:
- Total de produtos cadastrados;
- Total de produtos em estoque (soma de todos os estoques);
- Valor total do estoque (soma de estoque × preço para cada produto);
- Preço médio dos produtos;
- Produto mais barato (menor preço);
- Produto mais caro (maior preço);
- Número de categorias diferentes.

---

## Exercício 16: Identificando erros - Mistura de colunas

Um analista júnior escreveu a seguinte consulta para tentar mostrar cada produto com o total de vendas:

```sql
SELECT nome, COUNT(*)
FROM produtos;
```

a) Por que esta consulta está errada?
b) O que acontecerá quando tentar executá-la?
c) Como você corrigiria esta consulta? (dica: isso será abordado no próximo capítulo)

---

## Exercício 17: Identificando erros - WHERE com funções agregadoras

Outro analista tentou encontrar categorias que têm mais de 10 produtos com a seguinte consulta:

```sql
SELECT categoria
FROM produtos
WHERE COUNT(*) > 10;
```

a) Por que esta consulta está errada?
b) O que o erro significa?
c) Qual seria a forma correta? (será abordado no próximo capítulo)

---

## Exercício 18: Cuidado com divisão por zero

Um analista quer calcular o valor médio por produto em um pedido com a seguinte consulta:

```sql
SELECT 
  pedido_id,
  valor_total / COUNT(*) AS valor_medio_produto
FROM itens_pedido
WHERE pedido_id = 999;  -- Pedido que não existe
```

a) O que pode dar errado com esta consulta?
b) Como você protegeria contra esse erro?
c) Reescreva a consulta de forma mais segura.

---

## Exercício 19: Análise completa - Dashboard de vendas

Você precisa criar um dashboard executivo com KPIs (indicadores-chave de desempenho) de vendas do mês atual. A tabela `vendas` tem as colunas:
- `id` (identificador da venda)
- `data_venda` (data da transação)
- `valor_total` (valor da venda)
- `custo_produtos` (custo dos produtos vendidos)
- `desconto_aplicado` (desconto concedido)
- `cliente_id` (identificador do cliente)
- `vendedor_id` (identificador do vendedor)
- `forma_pagamento` (método de pagamento)

Crie uma consulta SQL única que retorne os seguintes KPIs para dezembro de 2024:
1. Total de vendas realizadas;
2. Faturamento bruto (soma dos valores totais);
3. Total de descontos concedidos;
4. Faturamento líquido (faturamento bruto - descontos);
5. Custo total dos produtos vendidos;
6. Lucro bruto (faturamento líquido - custo);
7. Ticket médio (valor médio por venda);
8. Menor e maior valor de venda;
9. Número de clientes únicos que compraram;
10. Número de vendedores únicos que realizaram vendas.

---

## Exercício 20: Questão conceitual - NULL em agregações

Considere a seguinte tabela `avaliacoes_produtos` com estas linhas:

| produto_id | nota | comentario |
|------------|------|------------|
| 1          | 5    | "Excelente"|
| 1          | 4    | NULL       |
| 1          | NULL | "Sem nota" |
| 2          | 3    | "Regular"  |
| 2          | NULL | NULL       |

Responda:
a) Qual será o resultado de `SELECT COUNT(*) FROM avaliacoes_produtos WHERE produto_id = 1;`?
b) Qual será o resultado de `SELECT COUNT(nota) FROM avaliacoes_produtos WHERE produto_id = 1;`?
c) Qual será o resultado de `SELECT COUNT(comentario) FROM avaliacoes_produtos WHERE produto_id = 1;`?
d) Qual será o resultado de `SELECT AVG(nota) FROM avaliacoes_produtos WHERE produto_id = 1;`?
e) Qual será o resultado de `SELECT COUNT(*), COUNT(nota), COUNT(comentario) FROM avaliacoes_produtos;`?

---

## Exercício Desafio: Análise de performance de vendedores

Você é analista de dados em uma empresa de vendas. A tabela `vendas` registra todas as transações com as colunas:
- `id`, `data_venda`, `vendedor_id`, `valor_total`, `custo`, `meta_atingida` (BOOLEAN)

A empresa tem interesse em um relatório geral (sem separar por vendedor) que mostre:
1. Total de vendas realizadas no ano de 2024;
2. Faturamento total;
3. Custo total;
4. Margem de lucro percentual média (calculada como: (valor_total - custo) / valor_total * 100);
5. Número de vendas que atingiram a meta;
6. Percentual de vendas que atingiram a meta;
7. Número de vendedores únicos que realizaram pelo menos uma venda;
8. Melhor performance individual (maior valor de venda);
9. Primeira e última venda do ano.

Crie a consulta SQL completa. Arredonde valores percentuais para 2 casas decimais.