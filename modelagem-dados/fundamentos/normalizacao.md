# Normalização de Dados

## Introdução à Normalização

A normalização é um processo sistemático de organização de dados em um banco de dados relacional para eliminar redundâncias e dependências indesejáveis, garantindo integridade e eficiência.

## Objetivos da Normalização

### Principais Metas
- **Eliminar redundância**: Evitar armazenamento desnecessário de dados
- **Prevenir anomalias**: Inserção, atualização e remoção
- **Garantir integridade**: Manter consistência dos dados
- **Otimizar armazenamento**: Usar espaço de forma eficiente
- **Facilitar manutenção**: Simplificar alterações futuras

### Problemas Resolvidos
- **Anomalia de Inserção**: Impossibilidade de inserir dados sem outros relacionados
- **Anomalia de Atualização**: Necessidade de alterar múltiplas ocorrências
- **Anomalia de Remoção**: Perda não intencional de informações

## Dependências Funcionais

### Conceito Fundamental
Uma dependência funcional X → Y significa que o valor de X determina univocamente o valor de Y.

### Tipos de Dependências

#### Dependência Funcional Total
- Y depende funcionalmente de X
- X é o conjunto mínimo de atributos que determina Y
- Exemplo: CPF → Nome (o CPF determina completamente o nome)

#### Dependência Funcional Parcial
- Y depende de apenas parte de uma chave composta
- Exemplo: (ID_Pedido, ID_Produto) → Nome_Produto
- Nome_Produto depende apenas de ID_Produto

#### Dependência Funcional Transitiva
- X → Y e Y → Z, então X → Z
- Exemplo: ID_Funcionario → ID_Departamento → Nome_Departamento

## Formas Normais

### Primeira Forma Normal (1FN)

#### Definição
Uma relação está em 1FN se:
- Todos os atributos contêm apenas valores atômicos
- Não há grupos repetitivos
- Cada atributo tem um único valor por tupla

#### Exemplo de Violação da 1FN
```
CLIENTE
+------------+----------------+---------------------------+
| ID_Cliente | Nome           | Telefones                 |
+------------+----------------+---------------------------+
| 001        | João Silva     | 11-1111-1111, 11-2222-2222|
| 002        | Maria Santos   | 11-3333-3333              |
+------------+----------------+---------------------------+
```

#### Correção para 1FN
```
CLIENTE
+------------+----------------+
| ID_Cliente | Nome           |
+------------+----------------+
| 001        | João Silva     |
| 002        | Maria Santos   |
+------------+----------------+

CLIENTE_TELEFONE
+------------+----------------+
| ID_Cliente | Telefone       |
+------------+----------------+
| 001        | 11-1111-1111   |
| 001        | 11-2222-2222   |
| 002        | 11-3333-3333   |
+------------+----------------+
```

### Segunda Forma Normal (2FN)

#### Definição
Uma relação está em 2FN se:
- Está em 1FN
- Todos os atributos não-chave dependem funcionalmente da chave primária completa
- Não há dependências parciais

#### Exemplo de Violação da 2FN
```
ITEM_PEDIDO
+------------+------------+----------------+---------------+----------+
| ID_Pedido  | ID_Produto | Nome_Produto   | Preco_Produto | Qtde     |
+------------+------------+----------------+---------------+----------+
| 001        | P001       | Notebook       | 2500.00       | 2        |
| 001        | P002       | Mouse          | 50.00         | 1        |
| 002        | P001       | Notebook       | 2500.00       | 1        |
+------------+------------+----------------+---------------+----------+
```
**Problema**: Nome_Produto e Preco_Produto dependem apenas de ID_Produto

#### Correção para 2FN
```
PRODUTO
+------------+----------------+---------------+
| ID_Produto | Nome_Produto   | Preco_Produto |
+------------+----------------+---------------+
| P001       | Notebook       | 2500.00       |
| P002       | Mouse          | 50.00         |
+------------+----------------+---------------+

ITEM_PEDIDO
+------------+------------+----------+
| ID_Pedido  | ID_Produto | Qtde     |
+------------+------------+----------+
| 001        | P001       | 2        |
| 001        | P002       | 1        |
| 002        | P001       | 1        |
+------------+------------+----------+
```

### Terceira Forma Normal (3FN)

#### Definição
Uma relação está em 3FN se:
- Está em 2FN
- Não há dependências transitivas
- Todos os atributos não-chave dependem diretamente da chave primária

#### Exemplo de Violação da 3FN
```
FUNCIONARIO
+-------------+----------------+---------------+--------------------+
| ID_Func     | Nome           | ID_Depto      | Nome_Depto         |
+-------------+----------------+---------------+--------------------+
| 001         | João Silva     | D001          | Vendas             |
| 002         | Maria Santos   | D002          | Marketing          |
| 003         | Pedro Costa    | D001          | Vendas             |
+-------------+----------------+---------------+--------------------+
```
**Problema**: Nome_Depto depende de ID_Depto, não diretamente de ID_Func

#### Correção para 3FN
```
DEPARTAMENTO
+---------------+--------------------+
| ID_Depto      | Nome_Depto         |
+---------------+--------------------+
| D001          | Vendas             |
| D002          | Marketing          |
+---------------+--------------------+

FUNCIONARIO
+-------------+----------------+---------------+
| ID_Func     | Nome           | ID_Depto      |
+-------------+----------------+---------------+
| 001         | João Silva     | D001          |
| 002         | Maria Santos   | D002          |
| 003         | Pedro Costa    | D001          |
+-------------+----------------+---------------+
```

### Forma Normal de Boyce-Codd (FNBC)

#### Definição
Uma relação está em FNBC se:
- Está em 3FN
- Para toda dependência funcional X → Y, X é uma superchave

#### Exemplo de Violação da FNBC
```
AULA
+------------+------------+----------+--------+
| ID_Aluno   | Disciplina | Prof     | Sala   |
+------------+------------+----------+--------+
| A001       | BD         | João     | S101   |
| A001       | POO        | Maria    | S102   |
| A002       | BD         | João     | S101   |
+------------+------------+----------+--------+
```
**Problema**: Prof → Disciplina (professor determina disciplina)

#### Correção para FNBC
```
PROFESSOR_DISCIPLINA
+----------+------------+
| Prof     | Disciplina |
+----------+------------+
| João     | BD         |
| Maria    | POO        |
+----------+------------+

AULA
+------------+----------+--------+
| ID_Aluno   | Prof     | Sala   |
+------------+----------+--------+
| A001       | João     | S101   |
| A001       | Maria    | S102   |
| A002       | João     | S101   |
+------------+----------+--------+
```

### Quarta Forma Normal (4FN)

#### Definição
Uma relação está em 4FN se:
- Está em FNBC
- Não possui dependências multivaloradas não triviais

#### Exemplo de Violação da 4FN
```
FUNCIONARIO_HABILIDADE_PROJETO
+-------------+-------------+-------------+
| ID_Func     | Habilidade  | Projeto     |
+-------------+-------------+-------------+
| F001        | Java        | P001        |
| F001        | Java        | P002        |
| F001        | Python      | P001        |
| F001        | Python      | P002        |
+-------------+-------------+-------------+
```

#### Correção para 4FN
```
FUNCIONARIO_HABILIDADE
+-------------+-------------+
| ID_Func     | Habilidade  |
+-------------+-------------+
| F001        | Java        |
| F001        | Python      |
+-------------+-------------+

FUNCIONARIO_PROJETO
+-------------+-------------+
| ID_Func     | Projeto     |
+-------------+-------------+
| F001        | P001        |
| F001        | P002        |
+-------------+-------------+
```

## Processo de Normalização

### Passo a Passo

#### 1. Análise da Estrutura Inicial
- Identificar chaves primárias
- Mapear dependências funcionais
- Detectar violações das formas normais

#### 2. Aplicação Sistemática
- Começar pela 1FN
- Progredir sequencialmente (1FN → 2FN → 3FN → FNBC)
- Validar cada etapa antes de prosseguir

#### 3. Verificação Final
- Confirmar eliminação de redundâncias
- Testar integridade referencial
- Validar com requisitos de negócio

### Algoritmo de Normalização

```
ALGORITMO NORMALIZAÇÃO:
1. PARA cada tabela:
   a. Verificar 1FN
      - Eliminar grupos repetitivos
      - Criar tabelas separadas se necessário
   
   b. Verificar 2FN
      - Identificar dependências parciais
      - Criar tabelas para atributos dependentes
   
   c. Verificar 3FN
      - Identificar dependências transitivas
      - Separar em tabelas independentes
   
   d. Verificar FNBC
      - Identificar violações de superchave
      - Reorganizar estruturas conforme necessário

2. Validar integridade referencial
3. Documentar mudanças realizadas
```

## Desnormalização

### Quando Considerar
- Performance crítica em consultas
- Relatórios complexos frequentes
- Data warehousing
- Sistemas OLAP

### Técnicas de Desnormalização
- **Tabelas Resumo**: Agregações pré-calculadas
- **Campos Calculados**: Valores derivados armazenados
- **Junção de Tabelas**: Combinar tabelas relacionadas
- **Duplicação Controlada**: Redundância planejada

### Cuidados na Desnormalização
- Manter integridade através de triggers
- Documentar redundâncias introduzidas
- Monitorar consistência dos dados
- Avaliar impacto na manutenção

## Ferramentas para Normalização

### Ferramentas Automatizadas
- **Microsoft SQL Server**: Database Tuning Advisor
- **Oracle**: SQL Access Advisor
- **PostgreSQL**: pg_stat_statements
- **MySQL**: Performance Schema

### Análise Manual
- Diagramas de dependências funcionais
- Matrizes de relacionamento
- Árvores de decomposição
- Verificação de formas normais

## Exemplos Práticos

### Caso 1: Sistema de Vendas

#### Estrutura Original (Não Normalizada)
```
VENDA
+----------+----------+-------------+----------+----------+----------+----------+
| ID_Venda | Data     | ID_Cliente  | Nome_Cli | ID_Prod  | Nome_Prod| Qtde     |
+----------+----------+-------------+----------+----------+----------+----------+
| V001     | 01/01/24 | C001        | João     | P001     | Notebook | 1        |
| V001     | 01/01/24 | C001        | João     | P002     | Mouse    | 2        |
| V002     | 02/01/24 | C002        | Maria    | P001     | Notebook | 1        |
+----------+----------+-------------+----------+----------+----------+----------+
```

#### Após Normalização (3FN)
```
CLIENTE
+-------------+----------+
| ID_Cliente  | Nome     |
+-------------+----------+
| C001        | João     |
| C002        | Maria    |
+-------------+----------+

PRODUTO
+----------+----------+
| ID_Prod  | Nome     |
+----------+----------+
| P001     | Notebook |
| P002     | Mouse    |
+----------+----------+

VENDA
+----------+----------+-------------+
| ID_Venda | Data     | ID_Cliente  |
+----------+----------+-------------+
| V001     | 01/01/24 | C001        |
| V002     | 02/01/24 | C002        |
+----------+----------+-------------+

ITEM_VENDA
+----------+----------+----------+
| ID_Venda | ID_Prod  | Qtde     |
+----------+----------+----------+
| V001     | P001     | 1        |
| V001     | P002     | 2        |
| V002     | P001     | 1        |
+----------+----------+----------+
```

## Exercícios Práticos

### Exercício 1: Identificação de Formas Normais
Analise a tabela abaixo e identifique qual forma normal ela viola:

```
PEDIDO_COMPLETO
+----------+----------+----------+----------+----------+----------+
| ID_Ped   | Data     | Cliente  | Tel_Cli  | Produtos | Valores  |
+----------+----------+----------+----------+----------+----------+
| P001     | 01/01/24 | João     | 111-1111 | A,B,C    | 10,20,30 |
+----------+----------+----------+----------+----------+----------+
```

### Exercício 2: Normalização Completa
Normalize a seguinte estrutura até a 3FN:

```
FUNCIONARIO_PROJETO
+----------+----------+----------+----------+----------+----------+
| ID_Func  | Nome     | ID_Depto | Nome_Dep | ID_Proj  | Nome_Pro |
+----------+----------+----------+----------+----------+----------+
| F001     | João     | D001     | TI       | PR001    | Site     |
| F001     | João     | D001     | TI       | PR002    | App      |
| F002     | Maria    | D002     | RH       | PR003    | Sistema  |
+----------+----------+----------+----------+----------+----------+
```

## Considerações Finais

### Vantagens da Normalização
- Redução de redundância
- Melhoria da integridade
- Facilidade de manutenção
- Economia de espaço
- Flexibilidade para mudanças

### Desvantagens da Normalização
- Aumento no número de tabelas
- Consultas mais complexas
- Possível impacto na performance
- Maior complexidade de desenvolvimento

### Boas Práticas
- Normalize durante o design
- Considere desnormalização apenas quando necessário
- Documente todas as decisões
- Monitore performance continuamente
- Mantenha equilíbrio entre normalização e usabilidade

A normalização é uma ferramenta fundamental para criar bancos de dados eficientes e confiáveis. O domínio dessas técnicas é essencial para qualquer profissional que trabalhe com modelagem de dados.