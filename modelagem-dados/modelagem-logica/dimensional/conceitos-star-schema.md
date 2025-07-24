# Conceitos de Star Schema - Modelagem Dimensional

## Introdução ao Star Schema

O Star Schema (Esquema Estrela) é uma arquitetura de modelagem dimensional amplamente utilizada em data warehouses e sistemas OLAP (Online Analytical Processing). Seu nome deriva da estrutura visual que se assemelha a uma estrela, com uma tabela de fatos central cercada por tabelas de dimensões.

## Fundamentos da Modelagem Dimensional

### Diferenças: OLTP vs OLAP

#### OLTP (Online Transaction Processing)
- **Objetivo**: Processar transações operacionais
- **Modelagem**: Altamente normalizada (3FN, FNBC)
- **Consultas**: Simples, poucos registros
- **Performance**: Otimizada para inserção/atualização
- **Usuários**: Aplicações operacionais

#### OLAP (Online Analytical Processing)
- **Objetivo**: Análise e relatórios
- **Modelagem**: Desnormalizada (Star/Snowflake)
- **Consultas**: Complexas, muitos registros
- **Performance**: Otimizada para leitura
- **Usuários**: Analistas e tomadores de decisão

## Componentes do Star Schema

### 1. Tabela de Fatos (Fact Table)

#### Características
- **Central**: Núcleo do star schema
- **Métricas**: Contém medidas numéricas (fatos)
- **Chaves Estrangeiras**: Referências para tabelas de dimensão
- **Granularidade**: Define o nível de detalhe dos dados

#### Tipos de Fatos
- **Aditivos**: Podem ser somados em todas as dimensões (vendas, quantidade)
- **Semi-aditivos**: Somáveis em algumas dimensões (saldo bancário)
- **Não-aditivos**: Não podem ser somados (percentuais, taxas)

#### Exemplo de Tabela de Fatos
```sql
CREATE TABLE Fato_Vendas (
    id_tempo        INTEGER REFERENCES Dim_Tempo(id_tempo),
    id_produto      INTEGER REFERENCES Dim_Produto(id_produto),
    id_cliente      INTEGER REFERENCES Dim_Cliente(id_cliente),
    id_loja         INTEGER REFERENCES Dim_Loja(id_loja),
    quantidade      INTEGER,
    valor_venda     DECIMAL(10,2),
    custo           DECIMAL(10,2),
    desconto        DECIMAL(8,2),
    PRIMARY KEY (id_tempo, id_produto, id_cliente, id_loja)
);
```

### 2. Tabelas de Dimensão (Dimension Tables)

#### Características
- **Descritivas**: Contêm atributos textuais e categóricos
- **Desnormalizadas**: Podem conter redundância controlada
- **Chave Substituta**: Geralmente usam chaves surrogate
- **Hierarquias**: Suportam drill-down e roll-up

#### Tipos de Dimensões

##### Dimensão Temporal
```sql
CREATE TABLE Dim_Tempo (
    id_tempo            INTEGER PRIMARY KEY,
    data_completa       DATE,
    ano                 INTEGER,
    trimestre           INTEGER,
    mes                 INTEGER,
    nome_mes            VARCHAR(20),
    dia                 INTEGER,
    dia_semana          INTEGER,
    nome_dia_semana     VARCHAR(20),
    eh_feriado          BOOLEAN,
    eh_fim_de_semana    BOOLEAN
);
```

##### Dimensão Produto
```sql
CREATE TABLE Dim_Produto (
    id_produto          INTEGER PRIMARY KEY,
    codigo_produto      VARCHAR(20),
    nome_produto        VARCHAR(100),
    categoria           VARCHAR(50),
    subcategoria        VARCHAR(50),
    marca               VARCHAR(50),
    cor                 VARCHAR(30),
    tamanho             VARCHAR(10),
    peso                DECIMAL(8,2),
    preco_custo         DECIMAL(8,2),
    preco_venda         DECIMAL(8,2)
);
```

##### Dimensão Cliente
```sql
CREATE TABLE Dim_Cliente (
    id_cliente          INTEGER PRIMARY KEY,
    codigo_cliente      VARCHAR(20),
    nome_cliente        VARCHAR(100),
    segmento            VARCHAR(30),
    regiao              VARCHAR(50),
    cidade              VARCHAR(50),
    estado              VARCHAR(30),
    pais                VARCHAR(30),
    idade               INTEGER,
    genero              VARCHAR(10),
    renda_familiar      DECIMAL(10,2)
);
```

## Vantagens do Star Schema

### Performance
- **Consultas Simples**: JOINs diretos entre fato e dimensões
- **Índices Eficientes**: Facilita criação de índices bitmap
- **Paralelização**: Consultas podem ser facilmente paralelizadas
- **Cache**: Dimensões pequenas ficam em cache

### Usabilidade
- **Intuitivo**: Estrutura facilmente compreensível
- **Ferramentas BI**: Suporte nativo em ferramentas OLAP
- **Navegação**: Drill-down e roll-up naturais
- **Flexibilidade**: Fácil adição de novas dimensões

### Manutenção
- **Simplicidade**: Menos JOINs complexos
- **ETL**: Processo de carga mais direto
- **Documentação**: Estrutura autodocumentada
- **Debugging**: Fácil identificação de problemas

## Snowflake Schema

### Conceito
Variação do Star Schema onde as dimensões são normalizadas, criando uma estrutura que lembra um floco de neve.

### Exemplo: Dimensão Produto Normalizada
```sql
-- Tabela principal da dimensão
CREATE TABLE Dim_Produto (
    id_produto          INTEGER PRIMARY KEY,
    codigo_produto      VARCHAR(20),
    nome_produto        VARCHAR(100),
    id_categoria        INTEGER REFERENCES Dim_Categoria(id_categoria),
    id_marca            INTEGER REFERENCES Dim_Marca(id_marca)
);

-- Tabela de categoria (normalizada)
CREATE TABLE Dim_Categoria (
    id_categoria        INTEGER PRIMARY KEY,
    nome_categoria      VARCHAR(50),
    id_subcategoria     INTEGER REFERENCES Dim_Subcategoria(id_subcategoria)
);

-- Tabela de subcategoria
CREATE TABLE Dim_Subcategoria (
    id_subcategoria     INTEGER PRIMARY KEY,
    nome_subcategoria   VARCHAR(50)
);

-- Tabela de marca
CREATE TABLE Dim_Marca (
    id_marca            INTEGER PRIMARY KEY,
    nome_marca          VARCHAR(50),
    pais_origem         VARCHAR(30)
);
```

### Comparação: Star vs Snowflake

| Aspecto | Star Schema | Snowflake Schema |
|---------|-------------|------------------|
| Estrutura | Desnormalizada | Normalizada |
| Número de Tabelas | Menor | Maior |
| Espaço em Disco | Maior | Menor |
| Performance | Melhor | Menor |
| Manutenção | Mais Simples | Mais Complexa |
| Integridade | Menor | Maior |

## Tipos de Tabelas de Fatos

### 1. Tabela de Fatos Transacionais
Armazenam eventos de negócio individuais.

```sql
CREATE TABLE Fato_Vendas_Transacional (
    id_transacao        INTEGER PRIMARY KEY,
    id_tempo            INTEGER,
    id_produto          INTEGER,
    id_cliente          INTEGER,
    quantidade          INTEGER,
    valor_unitario      DECIMAL(8,2),
    valor_total         DECIMAL(10,2)
);
```

### 2. Tabela de Fatos Snapshot Periódico
Capturam estado em intervalos regulares.

```sql
CREATE TABLE Fato_Estoque_Mensal (
    id_tempo            INTEGER,
    id_produto          INTEGER,
    id_loja             INTEGER,
    quantidade_inicial  INTEGER,
    quantidade_final    INTEGER,
    valor_medio         DECIMAL(8,2),
    PRIMARY KEY (id_tempo, id_produto, id_loja)
);
```

### 3. Tabela de Fatos Acumulativa
Rastreiam processo de negócio ao longo do tempo.

```sql
CREATE TABLE Fato_Pedido_Pipeline (
    id_pedido           INTEGER PRIMARY KEY,
    id_cliente          INTEGER,
    data_pedido         DATE,
    data_aprovacao      DATE,
    data_producao       DATE,
    data_envio          DATE,
    data_entrega        DATE,
    valor_pedido        DECIMAL(10,2),
    status_atual        VARCHAR(20)
);
```

## Dimensões Especiais

### Dimensão Tempo
Fundamental em quase todos os data warehouses.

```sql
-- Exemplo de população da dimensão tempo
INSERT INTO Dim_Tempo VALUES 
(20240101, '2024-01-01', 2024, 1, 1, 'Janeiro', 1, 2, 'Segunda', FALSE, FALSE),
(20240102, '2024-01-02', 2024, 1, 1, 'Janeiro', 2, 3, 'Terça', FALSE, FALSE),
-- ... mais registros
```

### Dimensão Degenerada
Atributo dimensional armazenado na tabela de fatos.

```sql
CREATE TABLE Fato_Vendas_Com_Degenerada (
    numero_nota_fiscal  VARCHAR(20),  -- Dimensão degenerada
    id_tempo            INTEGER,
    id_produto          INTEGER,
    quantidade          INTEGER,
    valor               DECIMAL(10,2)
);
```

### Dimensão Lixo (Junk Dimension)
Agrupa atributos de baixa cardinalidade.

```sql
CREATE TABLE Dim_Flags_Venda (
    id_flags            INTEGER PRIMARY KEY,
    eh_promocao         BOOLEAN,
    eh_desconto         BOOLEAN,
    tipo_pagamento      VARCHAR(20),
    canal_venda         VARCHAR(20)
);
```

## Técnicas de Modelagem Dimensional

### 1. Identificação de Processos de Negócio
- Vendas
- Compras
- Produção
- Marketing
- Recursos Humanos

### 2. Definição da Granularidade
- **Granularidade Alta**: Mais detalhe, mais registros
- **Granularidade Baixa**: Menos detalhe, menos registros
- **Exemplo**: Venda por item vs venda por pedido

### 3. Identificação de Dimensões
- Quem (Cliente, Vendedor)
- O que (Produto, Serviço)
- Quando (Tempo)
- Onde (Localização)
- Como (Canal, Método)
- Por que (Motivo, Campanha)

### 4. Identificação de Fatos
- Métricas aditivas
- Contadores
- Valores calculados

## Exemplo Completo: Sistema de Vendas

### Estrutura do Star Schema
```sql
-- Dimensão Tempo
CREATE TABLE Dim_Tempo (
    id_tempo            INTEGER PRIMARY KEY,
    data_completa       DATE,
    ano                 INTEGER,
    mes                 INTEGER,
    dia                 INTEGER,
    trimestre           INTEGER,
    nome_mes            VARCHAR(20),
    nome_dia_semana     VARCHAR(20)
);

-- Dimensão Produto
CREATE TABLE Dim_Produto (
    id_produto          INTEGER PRIMARY KEY,
    nome_produto        VARCHAR(100),
    categoria           VARCHAR(50),
    subcategoria        VARCHAR(50),
    marca               VARCHAR(50),
    preco_custo         DECIMAL(8,2),
    preco_venda         DECIMAL(8,2)
);

-- Dimensão Cliente
CREATE TABLE Dim_Cliente (
    id_cliente          INTEGER PRIMARY KEY,
    nome_cliente        VARCHAR(100),
    segmento            VARCHAR(30),
    cidade              VARCHAR(50),
    estado              VARCHAR(30),
    regiao              VARCHAR(30)
);

-- Dimensão Vendedor
CREATE TABLE Dim_Vendedor (
    id_vendedor         INTEGER PRIMARY KEY,
    nome_vendedor       VARCHAR(100),
    territorio          VARCHAR(50),
    gerente             VARCHAR(100)
);

-- Tabela de Fatos
CREATE TABLE Fato_Vendas (
    id_tempo            INTEGER,
    id_produto          INTEGER,
    id_cliente          INTEGER,
    id_vendedor         INTEGER,
    quantidade          INTEGER,
    valor_venda         DECIMAL(10,2),
    custo_produto       DECIMAL(10,2),
    desconto            DECIMAL(8,2),
    PRIMARY KEY (id_tempo, id_produto, id_cliente, id_vendedor),
    FOREIGN KEY (id_tempo) REFERENCES Dim_Tempo(id_tempo),
    FOREIGN KEY (id_produto) REFERENCES Dim_Produto(id_produto),
    FOREIGN KEY (id_cliente) REFERENCES Dim_Cliente(id_cliente),
    FOREIGN KEY (id_vendedor) REFERENCES Dim_Vendedor(id_vendedor)
);
```

### Consultas Analíticas Típicas

#### Vendas por Região e Período
```sql
SELECT 
    dc.regiao,
    dt.ano,
    dt.trimestre,
    SUM(fv.valor_venda) as total_vendas,
    SUM(fv.quantidade) as total_quantidade
FROM Fato_Vendas fv
JOIN Dim_Cliente dc ON fv.id_cliente = dc.id_cliente
JOIN Dim_Tempo dt ON fv.id_tempo = dt.id_tempo
WHERE dt.ano = 2024
GROUP BY dc.regiao, dt.ano, dt.trimestre
ORDER BY dc.regiao, dt.trimestre;
```

#### Top 10 Produtos por Vendas
```sql
SELECT 
    dp.nome_produto,
    dp.categoria,
    SUM(fv.valor_venda) as total_vendas,
    SUM(fv.quantidade) as total_quantidade
FROM Fato_Vendas fv
JOIN Dim_Produto dp ON fv.id_produto = dp.id_produto
JOIN Dim_Tempo dt ON fv.id_tempo = dt.id_tempo
WHERE dt.ano = 2024
GROUP BY dp.id_produto, dp.nome_produto, dp.categoria
ORDER BY total_vendas DESC
LIMIT 10;
```

## Implementação e Otimização

### Índices Recomendados
```sql
-- Índices na tabela de fatos
CREATE INDEX idx_fato_tempo ON Fato_Vendas(id_tempo);
CREATE INDEX idx_fato_produto ON Fato_Vendas(id_produto);
CREATE INDEX idx_fato_cliente ON Fato_Vendas(id_cliente);

-- Índices compostos para consultas frequentes
CREATE INDEX idx_fato_tempo_produto ON Fato_Vendas(id_tempo, id_produto);
CREATE INDEX idx_fato_cliente_tempo ON Fato_Vendas(id_cliente, id_tempo);
```

### Particionamento
```sql
-- Particionamento por tempo (PostgreSQL)
CREATE TABLE Fato_Vendas_2024 PARTITION OF Fato_Vendas
FOR VALUES FROM ('20240101') TO ('20250101');

CREATE TABLE Fato_Vendas_2023 PARTITION OF Fato_Vendas
FOR VALUES FROM ('20230101') TO ('20240101');
```

### Agregações Pré-calculadas
```sql
-- Tabela de agregação mensal
CREATE TABLE Fato_Vendas_Mensal (
    ano                 INTEGER,
    mes                 INTEGER,
    id_produto          INTEGER,
    id_cliente          INTEGER,
    total_vendas        DECIMAL(12,2),
    total_quantidade    INTEGER,
    PRIMARY KEY (ano, mes, id_produto, id_cliente)
);
```

## Considerações de Design

### Chaves Substitutas vs Naturais
- **Substituta**: Mais performance, independência de mudanças
- **Natural**: Mais intuitiva, menor espaço

### Tratamento de Mudanças Dimensionais
- **Tipo 0**: Não permite mudanças
- **Tipo 1**: Sobrescreve valores antigos
- **Tipo 2**: Mantém histórico com versões
- **Tipo 3**: Mantém valor atual e anterior

### Dimensões Grandes
- Usar mini-dimensões
- Implementar hierarchias outrigger
- Considerar arquitetura híbrida

## Ferramentas para Star Schema

### Ferramentas de Modelagem
- **PowerDesigner**: Modelagem dimensional
- **ER/Studio**: Suporte a data warehouse
- **Kimball Toolkit**: Metodologia específica

### Ferramentas ETL
- **Talend**: Open source
- **Pentaho**: Kettle/Spoon
- **Microsoft SSIS**: Integration Services
- **Apache Airflow**: Orquestração moderna

### Ferramentas OLAP
- **Microsoft Analysis Services**
- **Oracle OLAP**
- **Mondrian**: Open source
- **Apache Kylin**: Big data OLAP

## Conclusão

O Star Schema é a base fundamental para data warehousing eficiente. Suas principais vantagens incluem:

- **Simplicidade**: Fácil compreensão e implementação
- **Performance**: Otimizado para consultas analíticas
- **Flexibilidade**: Suporta diversos tipos de análise
- **Ferramentas**: Amplo suporte em soluções BI

O domínio destes conceitos é essencial para profissionais que trabalham com business intelligence e análise de dados.