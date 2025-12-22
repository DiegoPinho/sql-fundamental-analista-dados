# Exercícios - Capítulo 06: Remoção de Dados (DELETE)

## Exercício 1: DELETE básico

```sql
DELETE FROM produtos 
WHERE id = 2;
```

Verificando o resultado:
```sql
SELECT * FROM produtos;
```

Resultado esperado:
```
 id |  nome   | preco  | estoque 
----+---------+--------+---------
  1 | Mouse   |  45.90 |      50
  3 | Monitor | 800.00 |      15
(2 rows)
```

---

## Exercício 2: O perigo do DELETE sem WHERE

**Todos os registros serão deletados!** A tabela ficará vazia porque não foi especificada a cláusula WHERE.

Resultado:
```sql
SELECT * FROM clientes;
-- (0 rows)
```

**Comando correto:**
```sql
DELETE FROM clientes 
WHERE nome = 'João Silva';
```

Ou usando o id (mais seguro):
```sql
DELETE FROM clientes 
WHERE id = 1;
```

**Lição importante**: Sempre use WHERE ao executar DELETE, a menos que você realmente queira deletar TODOS os registros.

---

## Exercício 3: DELETE com múltiplas condições

```sql
DELETE FROM estoque 
WHERE quantidade = 0 AND vencido = true;
```

Resultado após o DELETE:
```sql
SELECT * FROM estoque;
```

```
 id | produto  | quantidade | data_entrada | vencido 
----+----------+------------+--------------+---------
  2 | Pão      |         50 | 2023-12-01   | false
  4 | Manteiga |         20 | 2023-11-20   | false
(2 rows)
```

---

## Exercício 4: Testando antes de deletar

1. SELECT para verificar os registros:
```sql
SELECT * FROM pedidos WHERE status = 'Cancelado';
```

Resultado:
```
 id |  cliente  | valor  |  status   
----+-----------+--------+-----------
  2 | Empresa B | 1500.00| Cancelado
  4 | Empresa D |  800.00| Cancelado
(2 rows)
```

2. Comando DELETE:
```sql
DELETE FROM pedidos 
WHERE status = 'Cancelado';
```

---

## Exercício 5: DELETE vs TRUNCATE

**DELETE FROM produtos:**
- Remove todos os registros um por um
- Registra cada exclusão individualmente no log de transações
- Mais lento para grandes volumes de dados
- Pode ser usado com WHERE para deletar registros específicos
- Pode ser revertido se estiver em uma transação (ROLLBACK)

**TRUNCATE TABLE produtos:**
- Remove todos os registros de uma só vez
- Não registra cada exclusão individualmente
- Muito mais rápido para grandes volumes de dados
- NÃO pode ser usado com WHERE
- Reseta contadores de SERIAL/AUTO_INCREMENT
- Mais difícil de reverter

**Quando usar cada um:**
- Use **DELETE** quando precisar remover registros específicos (com WHERE)
- Use **TRUNCATE** quando quiser limpar toda a tabela rapidamente

**Ambos mantêm a estrutura da tabela intacta.**

---

## Exercício 6: DELETE vs DROP

**DELETE FROM reunioes:**
- Remove todos os dados da tabela
- Mantém a estrutura da tabela
- Tabela continua existindo e pode receber novos dados

**TRUNCATE TABLE reunioes:**
- Remove todos os dados da tabela (mais rápido que DELETE)
- Mantém a estrutura da tabela
- Tabela continua existindo e pode receber novos dados

**DROP TABLE reunioes:**
- Remove a tabela COMPLETAMENTE
- Apaga tanto os dados quanto a estrutura
- Tabela deixa de existir no banco de dados
- Para usar a tabela novamente, será necessário recriá-la com CREATE TABLE

**Resumo:**
- DELETE/TRUNCATE: mantêm a tabela, removem apenas os dados
- DROP: remove tudo (dados + estrutura)

---

## Exercício 7: Praticando TRUNCATE

```sql
TRUNCATE TABLE logs;
```

Este é o comando mais eficiente porque:
- Remove todos os registros de uma vez
- É muito mais rápido que `DELETE FROM logs`
- Reseta o contador do SERIAL (próximo id será 1)
- Mantém a estrutura da tabela intacta

---

## Exercício 8: Soft Delete (Exclusão Lógica)

1. Adicionando a coluna `ativo`:
```sql
ALTER TABLE usuarios 
ADD COLUMN ativo BOOLEAN DEFAULT TRUE;
```

2. "Removendo" o usuário João (exclusão lógica):
```sql
UPDATE usuarios 
SET ativo = FALSE 
WHERE nome = 'João';
```

3. Consultando apenas usuários ativos:
```sql
SELECT * FROM usuarios 
WHERE ativo = TRUE;
```

Resultado:
```
 id | nome  |      email       | ativo 
----+-------+------------------+-------
  2 | Maria | maria@email.com  | true
  3 | Pedro | pedro@email.com  | true
(2 rows)
```

---

## Exercício 9: Soft Delete com data de exclusão

1. Adicionando a coluna:
```sql
ALTER TABLE produtos_loja 
ADD COLUMN data_exclusao TIMESTAMP NULL;
```

2. "Removendo" o Mouse:
```sql
UPDATE produtos_loja 
SET data_exclusao = NOW() 
WHERE nome = 'Mouse';
```

Ou usando CURRENT_TIMESTAMP:
```sql
UPDATE produtos_loja 
SET data_exclusao = CURRENT_TIMESTAMP 
WHERE nome = 'Mouse';
```

3. Consultando produtos ativos (não excluídos):
```sql
SELECT * FROM produtos_loja 
WHERE data_exclusao IS NULL;
```

Resultado:
```
 id |   nome   | preco   | data_exclusao 
----+----------+---------+---------------
  1 | Notebook | 3500.00 | 
  3 | Teclado  |  120.00 | 
(2 rows)
```

---

## Exercício 10: Arquivamento de dados

1. Criando a tabela de arquivo:
```sql
CREATE TABLE vendas_arquivadas AS TABLE vendas 
WITH NO DATA;
```

Ou criando manualmente:
```sql
CREATE TABLE vendas_arquivadas (
  id SERIAL PRIMARY KEY,
  produto VARCHAR(100) NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  data_venda DATE NOT NULL
);
```

2. Movendo vendas antigas para arquivo:
```sql
INSERT INTO vendas_arquivadas 
SELECT * FROM vendas 
WHERE EXTRACT(YEAR FROM data_venda) <= 2022;
```

3. Deletando da tabela principal:
```sql
DELETE FROM vendas 
WHERE EXTRACT(YEAR FROM data_venda) <= 2022;
```

Verificando:
```sql
SELECT * FROM vendas;
-- Apenas a venda de 2023 (Mouse)

SELECT * FROM vendas_arquivadas;
-- Vendas de 2021 e 2022 (Notebook e Monitor)
```

---

## Exercício 11: DELETE com subconsulta

```sql
DELETE FROM produtos_cat 
WHERE categoria_id IN (
  SELECT id FROM categorias WHERE ativa = false
);
```

Ou usando JOIN (mais avançado):
```sql
DELETE FROM produtos_cat 
WHERE categoria_id = (
  SELECT id FROM categorias 
  WHERE nome = 'Descontinuados'
);
```

Verificação:
```sql
SELECT * FROM produtos_cat;
```

Resultado (apenas produtos de categorias ativas):
```
 id |    nome    | categoria_id 
----+------------+--------------
  1 | TV         |            1
  3 | Smartphone |            1
(2 rows)
```

---

## Exercício 12: DELETE com RETURNING

```sql
DELETE FROM tarefas 
WHERE concluida = true
RETURNING id, titulo;
```

Resultado:
```
 id |     titulo      
----+-----------------
  1 | Estudar SQL
  3 | Revisar conteúdo
(2 rows)
```

Isso é útil para:
- Confirmar quais registros foram deletados
- Registrar em logs
- Exibir confirmação para o usuário

---

## Exercício 13: Identificando erros

**Comando A** - Falta `FROM`:
```sql
DELETE FROM produtos WHERE id = 1;
```

**Comando B** - Falta ponto e vírgula:
```sql
DELETE FROM clientes
WHERE nome = 'João';
```

**Comando C** - Não usa asterisco (*) no DELETE:
```sql
DELETE FROM pedidos WHERE id = 5;
```

**Comando D** - Ponto e vírgula no lugar errado:
```sql
DELETE FROM vendas
WHERE data_venda < '2020-01-01';
```

---

## Exercício 14: Questão conceitual sobre soft delete

**Vantagens da Exclusão Lógica:**

1. **Recuperação de dados**: Possibilita "desfazer" exclusões acidentais facilmente
2. **Auditoria**: Mantém histórico completo de todas as operações
3. **Integridade referencial**: Evita problemas com chaves estrangeiras
4. **Análises históricas**: Permite relatórios que incluem dados "excluídos"
5. **Conformidade legal**: Algumas legislações exigem manutenção de histórico

**Desvantagens da Exclusão Lógica:**

1. **Tamanho do banco**: Dados "excluídos" continuam ocupando espaço
2. **Performance**: Consultas podem ficar mais lentas com muitos dados inativos
3. **Complexidade**: Todas as consultas precisam filtrar registros ativos
4. **Confusão**: Desenvolvedores podem esquecer de filtrar dados inativos
5. **Manutenção**: Necessita estratégias de arquivamento/limpeza periódica

**Quando usar cada uma:**
- **Soft delete**: Dados importantes, sistemas com auditoria, necessidade de histórico
- **DELETE físico**: Dados temporários, logs, caches, dados sem valor histórico

---

## Exercício 15: Cenário de limpeza de dados

Primeiro, testar com SELECT:
```sql
SELECT * FROM sessoes 
WHERE ativa = false 
  AND data_criacao < '2023-12-01';
```

Resultado (registros que serão deletados):
```
 id | usuario_id | token  | data_criacao        | data_expiracao      | ativa 
----+------------+--------+---------------------+---------------------+-------
  1 |          1 | abc123 | 2023-10-01 10:00:00 | 2023-10-01 22:00:00 | false
  3 |          3 | ghi789 | 2023-11-15 14:00:00 | 2023-11-15 23:00:00 | false
```

Comando DELETE:
```sql
DELETE FROM sessoes 
WHERE ativa = false 
  AND data_criacao < '2023-12-01';
```

---

## Exercício 16: Comparando estratégias de remoção

a) **TRUNCATE**
- Justificativa: Precisa remover todos os registros rapidamente, cache não precisa de histórico

b) **DELETE (físico)**
- Justificativa: Legislação de proteção de dados exige remoção completa dos dados pessoais
```sql
DELETE FROM clientes WHERE id = 123;
```

c) **Soft Delete**
- Justificativa: Produto precisa existir para manter integridade dos pedidos históricos
```sql
UPDATE produtos SET ativo = false WHERE id = 456;
```

d) **DROP**
- Justificativa: Tabela inteira não é mais necessária, melhor removê-la completamente
```sql
DROP TABLE tabela_testes;
```

e) **DELETE ou Arquivamento**
- Justificativa: Remover logs antigos mas pode querer mover para arquivo primeiro
```sql
-- Opção 1: Arquivar e deletar
INSERT INTO logs_arquivados 
  SELECT * FROM logs WHERE data < CURRENT_DATE - INTERVAL '90 days';
DELETE FROM logs WHERE data < CURRENT_DATE - INTERVAL '90 days';

-- Opção 2: Apenas deletar
DELETE FROM logs WHERE data < CURRENT_DATE - INTERVAL '90 days';
```

---

## Exercício 17: Desafio - Sistema completo de gerenciamento

1. Inativar produtos com estoque zero:
```sql
UPDATE produtos_sistema 
SET 
  ativo = false,
  data_inativacao = CURRENT_TIMESTAMP
WHERE estoque = 0;
```

2. Consultar apenas produtos ativos:
```sql
SELECT * FROM produtos_sistema 
WHERE ativo = true;
```

3. Consultar produtos inativos nos últimos 30 dias:
```sql
SELECT * FROM produtos_sistema 
WHERE ativo = false 
  AND data_inativacao >= CURRENT_TIMESTAMP - INTERVAL '30 days';
```

4. Deletar produtos inativos há mais de 1 ano:
```sql
DELETE FROM produtos_sistema 
WHERE ativo = false 
  AND data_inativacao < CURRENT_TIMESTAMP - INTERVAL '1 year';
```

5. Criar tabela de arquivo e mover produtos:
```sql
-- Criar tabela de arquivo
CREATE TABLE produtos_arquivados AS TABLE produtos_sistema 
WITH NO DATA;

-- Mover produtos inativos há mais de 6 meses
INSERT INTO produtos_arquivados 
SELECT * FROM produtos_sistema 
WHERE ativo = false 
  AND data_inativacao < CURRENT_TIMESTAMP - INTERVAL '6 months';

-- Deletar da tabela principal
DELETE FROM produtos_sistema 
WHERE ativo = false 
  AND data_inativacao < CURRENT_TIMESTAMP - INTERVAL '6 months';
```

---

## Exercício 18: Prevenção de exclusão acidental

**Boas práticas para DELETE em produção:**

1. Sempre use WHERE: Nunca execute DELETE sem WHERE, a menos que realmente queira deletar tudo
   ```sql
   -- ✅ Bom
   DELETE FROM users WHERE id = 123;
   
   -- ❌ Perigoso
   DELETE FROM users;
   ```

2. Teste com SELECT primeiro: Verifique quais registros serão afetados
   ```sql
   -- Primeiro: verificar
   SELECT * FROM users WHERE status = 'inactive';
   
   -- Depois: deletar
   DELETE FROM users WHERE status = 'inactive';
   ```

3. Use transações: Em operações críticas, use BEGIN/COMMIT/ROLLBACK
   ```sql
   BEGIN;
   DELETE FROM users WHERE id = 123;
   SELECT * FROM users; -- Verificar
   -- Se correto: COMMIT;
   -- Se errado: ROLLBACK;
   ```

4. Faça backup antes: Sempre tenha um backup recente antes de operações destrutivas

5. Use RETURNING: Veja o que está sendo deletado
   ```sql
   DELETE FROM users WHERE id = 123 
   RETURNING id, nome, email;
   ```

6. Limite a quantidade: Para deletes em massa, use LIMIT para processar em lotes
   ```sql
   DELETE FROM logs 
   WHERE data < '2020-01-01' 
   LIMIT 1000;
   ```

7. Considere soft delete: Para dados importantes, use exclusão lógica em vez de física

8. Documente: Registre o motivo da exclusão, especialmente em produção

9. Dupla verificação: Tenha procedimentos de aprovação para deletes em produção

10. Monitore: Configure alertas para operações de DELETE em tabelas críticas

---

## Exercício 19: Questão conceitual final

**Por que DELETE é "destrutivo":**

1. Irreversibilidade: Uma vez executado (e commitado), não há CTRL+Z. Os dados são perdidos permanentemente.

2. Sem confirmação automática: O SQL não pede "Tem certeza?" antes de deletar. Se o comando estiver correto sintaticamente, será executado.

3. Efeito em cascata: Dependendo das foreign keys, pode deletar dados em outras tabelas relacionadas.

4. Performance: DELETE pode ser muito rápido, deletando milhares de registros em segundos, sem tempo para reverter.

**Implicações em produção:**

1. Perda de dados críticos: Clientes, vendas, transações financeiras - dados essenciais podem ser perdidos

2. Impacto nos negócios: 
   - Perda de receita
   - Problemas legais/regulatórios
   - Perda de confiança dos clientes

3. Custo de recuperação:
   - Tempo de downtime
   - Esforço para restaurar backups
   - Possível perda permanente se não houver backup

4. Responsabilidade profissional: Erros graves podem resultar em demissão ou consequências legais

**Como mitigar os riscos:**

- Implementar controles de acesso rigorosos
- Usar transações para operações críticas
- Manter backups regulares e testados
- Implementar soft delete quando apropriado
- Ter procedimentos de aprovação para DELETEs em produção
- Treinar equipe em boas práticas
- Usar ferramentas que exigem confirmação explícita
- Implementar auditorias de todas as operações DELETE