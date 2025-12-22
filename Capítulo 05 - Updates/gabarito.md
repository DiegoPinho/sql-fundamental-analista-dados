# Exercícios - Capítulo 05: Atualizações de Dados (UPDATE)

## Exercício 1: UPDATE básico

```sql
UPDATE produtos 
SET preco = 39.90 
WHERE id = 1;
```

Ou, se não soubermos o id:

```sql
UPDATE produtos 
SET preco = 39.90 
WHERE nome = 'Mouse';
```

---

## Exercício 2: Atualizando múltiplas colunas

```sql
UPDATE produtos 
SET 
  preco = 42.50,
  estoque = 75
WHERE nome = 'Mouse';
```

Ou usando o id:

```sql
UPDATE produtos 
SET 
  preco = 42.50,
  estoque = 75
WHERE id = 1;
```

---

## Exercício 3: O perigo do UPDATE sem WHERE

**Todos os 3 registros** serão atualizados. Todos os clientes terão a cidade alterada para 'Campinas' porque não foi especificada uma cláusula WHERE.

Resultado após o UPDATE:

```
 id |     nome      |      email       |  cidade  
----+---------------+------------------+----------
  1 | João Silva    | joao@email.com   | Campinas
  2 | Maria Santos  | maria@email.com  | Campinas
  3 | Pedro Costa   | pedro@email.com  | Campinas
```

**Comando correto:**

```sql
UPDATE clientes 
SET cidade = 'Campinas' 
WHERE nome = 'João Silva';
```

Ou:

```sql
UPDATE clientes 
SET cidade = 'Campinas' 
WHERE id = 1;
```

---

## Exercício 4: Usando diferentes condições no WHERE

a) Aumentar salário do id 2:
```sql
UPDATE funcionarios 
SET salario = salario + 500 
WHERE id = 2;
```

b) Atualizar cargo da Ana:
```sql
UPDATE funcionarios 
SET cargo = 'Analista Sênior' 
WHERE nome = 'Ana Costa';
```

c) Marcar Bruno como inativo:
```sql
UPDATE funcionarios 
SET ativo = false 
WHERE nome = 'Bruno Silva';
```

---

## Exercício 5: Testando com SELECT antes do UPDATE

1. SELECT para verificar os registros que serão afetados:
```sql
SELECT * FROM pedidos WHERE valor > 1000.00;
```

Resultado esperado:
```
 id |  cliente   | valor  |  status  
----+------------+--------+----------
  2 | Empresa B  | 1500.00| Pendente
  3 | Empresa C  | 2000.00| Pendente
```

2. Comando UPDATE:
```sql
UPDATE pedidos 
SET status = 'Prioritário' 
WHERE valor > 1000.00;
```

---

## Exercício 6: UPDATE com operadores de comparação

a) Aumentar quantidade dos produtos abaixo de 10:
```sql
UPDATE estoque 
SET quantidade = quantidade + 50 
WHERE quantidade < 10;
```

b) Atualizar alerta_minimo dos produtos com estoque >= 100:
```sql
UPDATE estoque 
SET alerta_minimo = 25 
WHERE quantidade >= 100;
```

c) Reduzir quantidade do Monitor:
```sql
UPDATE estoque 
SET quantidade = quantidade - 10 
WHERE produto = 'Monitor';
```

---

## Exercício 7: Identificando erros em UPDATE

**Comando A** - Falta a palavra-chave SET:
```sql
UPDATE produtos 
SET preco = 100.00 
WHERE id = 1;
```

**Comando B** - Falta ponto e vírgula no final:
```sql
UPDATE clientes 
SET email = 'novo@email.com'
WHERE id = 2;
```

**Comando C** - Está usando `==` em vez de `=`:
```sql
UPDATE funcionarios 
SET salario = 6000.00 
WHERE nome = 'João';
```

**Comando D** - WHERE deve vir depois de SET:
```sql
UPDATE pedidos 
SET valor = 0
WHERE status = 'Cancelado';
```

---

## Exercício 8: UPDATE com cálculos

a) 10% de desconto em Eletrônicos:
```sql
UPDATE produtos_loja 
SET preco_promocional = preco_original * 0.90 
WHERE categoria = 'Eletrônicos';
```

b) 15% de desconto em produtos acima de 2500:
```sql
UPDATE produtos_loja 
SET preco_promocional = preco_original * 0.85 
WHERE preco_original > 2500.00;
```

---

## Exercício 9: UPDATE com múltiplas condições

```sql
UPDATE alunos 
SET status = 'Aprovado' 
WHERE nota_final >= 7.0 AND frequencia >= 75;
```

Resultado após o UPDATE:
- Alice: Aprovado (nota 8.5, frequência 90)
- Bruno: Em andamento (nota 5.0, frequência 70)
- Carla: Aprovado (nota 7.5, frequência 85)
- Diego: Em andamento (nota 4.5, frequência 60)

---

## Exercício 10: UPDATE baseado em datas

a) Marcar como concluída as tarefas com data_conclusao:
```sql
UPDATE tarefas 
SET status = 'Concluída' 
WHERE data_conclusao IS NOT NULL;
```

b) Marcar como atrasada tarefas antigas sem conclusão:
```sql
UPDATE tarefas 
SET status = 'Atrasada' 
WHERE data_criacao < '2023-11-01' AND data_conclusao IS NULL;
```

---

## Exercício 11: Cenário de correção de dados

```sql
UPDATE contatos 
SET email = LOWER(email);
```

Resultado:
```
 id | nome  |      email       
----+-------+------------------
  1 | Ana   | ana@email.com   
  2 | Bruno | bruno@email.com 
  3 | Carla | carla@email.com 
```

---

## Exercício 12: UPDATE com RETURNING

```sql
UPDATE inventario 
SET quantidade = quantidade - 25 
WHERE item = 'Caneta'
RETURNING id, quantidade;
```

Resultado esperado:
```
 id | quantidade 
----+------------
  1 |         75
(1 row)
```

---

## Exercício 13: Questão conceitual

1. Prevenção de erros: O SELECT permite visualizar exatamente quais registros serão afetados pelo UPDATE, evitando que você atualize registros indesejados por engano.

2. Verificação da quantidade: Você pode confirmar quantos registros serão atualizados. Se o número for diferente do esperado (muito alto ou muito baixo), você pode revisar sua condição antes de fazer alterações irreversíveis.

3. Validação da lógica: Permite verificar se a condição WHERE está correta e realmente filtra os registros que você deseja modificar, especialmente quando usa condições complexas com AND, OR, etc.

4. Segurança dos dados: Em produção, atualizar dados incorretamente pode causar problemas sérios. Testar primeiro com SELECT é uma camada extra de segurança.

---

## Exercício 14: UPDATE condicional complexo

Primeiro, teste com SELECT:

```sql
SELECT * FROM colaboradores 
WHERE tempo_empresa_anos >= 5 AND faltas < 5;
```

Resultado do SELECT (registros que serão afetados):
```
 id | nome  | tempo_empresa_anos | faltas | nivel  
----+-------+--------------------+--------+--------
  1 | João  |                  5 |      2 | Júnior
  3 | Pedro |                  7 |      1 | Júnior
```

Comando UPDATE:

```sql
UPDATE colaboradores 
SET nivel = 'Sênior' 
WHERE tempo_empresa_anos >= 5 AND faltas < 5;
```

---

## Exercício 15: Reversão de UPDATE

**Problema**: O UPDATE foi executado sem a cláusula WHERE, afetando todos os registros da tabela.

**O que poderia ter evitado:**

1. **Sempre usar WHERE**: Nunca executar UPDATE sem WHERE, a menos que você realmente queira atualizar todos os registros.

2. **Testar com SELECT primeiro**: Executar um SELECT com a mesma condição WHERE para ver quais registros seriam afetados:
   ```sql
   SELECT * FROM produtos_preco WHERE id = 1;
   -- Depois fazer:
   UPDATE produtos_preco SET preco = 99.99 WHERE id = 1;
   ```

3. **Usar transações**: Em operações críticas, usar BEGIN/COMMIT/ROLLBACK para poder desfazer em caso de erro:
   ```sql
   BEGIN;
   UPDATE produtos_preco SET preco = 99.99;
   SELECT * FROM produtos_preco; -- Verificar o resultado
   -- Se estiver errado: ROLLBACK;
   -- Se estiver correto: COMMIT;
   ```

4. **Double-check**: Sempre revisar o comando antes de executar, especialmente em produção.

**Sem backup, os dados originais foram perdidos e será muito difícil recuperá-los.**

---

## Exercício 16: Desafio - Sistema de biblioteca

a) Marcar como disponível livros atrasados:
```sql
UPDATE livros_biblioteca 
SET disponivel = true 
WHERE data_devolucao_prevista < '2023-11-25' AND disponivel = false;
```

b) Aplicar multa (livros atrasados até 25/11):
```sql
UPDATE livros_biblioteca 
SET multa = ('2023-11-25'::DATE - data_devolucao_prevista) * 5.00
WHERE data_devolucao_prevista < '2023-11-25' AND disponivel = false;
```

Nota: O livro "Grande Sertão" tem 22 dias de atraso (03/11 até 25/11), multa = 110.00

c) Limpar datas dos livros disponíveis:
```sql
UPDATE livros_biblioteca 
SET 
  data_emprestimo = NULL,
  data_devolucao_prevista = NULL
WHERE disponivel = true;
```

---

## Exercício 17: UPDATE com valores NULL

a) Atualizar comissão NULL:
```sql
UPDATE vendedores 
SET comissao_percentual = 4.00 
WHERE comissao_percentual IS NULL;
```

b) Atualizar meta NULL:
```sql
UPDATE vendedores 
SET meta_mensal = 5000.00 
WHERE meta_mensal IS NULL;
```

**Importante**: Use `IS NULL` e não `= NULL` para verificar valores nulos!

---

## Exercício 18: Desafio final - E-commerce

1. Aplicar desconto de 10% em pedidos > 1000:
```sql
UPDATE pedidos_ecommerce 
SET desconto_aplicado = valor_total * 0.10 
WHERE valor_total > 1000.00;
```

2. Atualizar status dos pedidos com desconto:
```sql
UPDATE pedidos_ecommerce 
SET status = 'Processando' 
WHERE desconto_aplicado > 0;
```

3. Subtrair o desconto do valor total:
```sql
UPDATE pedidos_ecommerce 
SET valor_total = valor_total - desconto_aplicado 
WHERE desconto_aplicado > 0;
```

Resultado final esperado:
```
 id | numero_pedido |   cliente    | valor_total | status      | desconto_aplicado
----+---------------+--------------+-------------+-------------+-------------------
  1 | PED001        | João Silva   |     1350.00 | Processando |            150.00
  2 | PED002        | Maria Santos |      800.00 | Aguardando  |              0.00
  3 | PED003        | Pedro Costa  |     2250.00 | Processando |            250.00
  4 | PED004        | Ana Oliveira |      450.00 | Aguardando  |              0.00
```

**Dica**: Em situações reais, você provavelmente usaria uma nova coluna `valor_com_desconto` em vez de modificar `valor_total`, para manter o histórico do valor original.
