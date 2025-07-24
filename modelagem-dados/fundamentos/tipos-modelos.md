# Tipos de Modelos de Dados

## Introdução

A modelagem de dados é um processo estruturado que envolve diferentes níveis de abstração, cada um com propósitos específicos e características distintas. Esta abordagem em camadas permite uma transição gradual desde a compreensão conceitual do negócio até a implementação física no sistema de gerenciamento de banco de dados.

## Modelo Conceitual

### Definição
O modelo conceitual é uma representação de alto nível que descreve a estrutura geral dos dados sem se preocupar com detalhes de implementação. Foca na compreensão do domínio do problema e nas regras de negócio.

### Características
- **Independente de tecnologia**: Não considera aspectos específicos de SGBDs
- **Orientado ao negócio**: Reflete a visão dos usuários finais
- **Alto nível de abstração**: Foca nos conceitos principais
- **Comunicação**: Facilita o diálogo entre analistas e usuários

### Componentes Principais
- **Entidades**: Objetos ou conceitos do mundo real
- **Relacionamentos**: Associações entre entidades
- **Atributos**: Propriedades das entidades
- **Restrições**: Regras de negócio e integridade

### Notações Utilizadas
- **Modelo Entidade-Relacionamento (MER)**
- **Diagrama Entidade-Relacionamento (DER)**
- **UML - Unified Modeling Language**

### Exemplo Prático
```
Entidade: CLIENTE
Atributos: nome, cpf, email, telefone

Entidade: PEDIDO
Atributos: número, data, valor_total

Relacionamento: CLIENTE faz PEDIDO (1:N)
```

## Modelo Lógico

### Definição
O modelo lógico representa a estrutura dos dados de forma mais detalhada, considerando o tipo de banco de dados que será utilizado, mas ainda independente do SGBD específico.

### Características
- **Dependente do tipo de BD**: Considera se é relacional, orientado a objetos, etc.
- **Independente de SGBD**: Não especifica fornecedor ou versão
- **Detalha estruturas**: Define tipos de dados, chaves, índices
- **Normalização**: Aplica regras de normalização

### Tipos de Modelos Lógicos

#### Modelo Relacional
- Organiza dados em tabelas (relações)
- Utiliza chaves primárias e estrangeiras
- Segue princípios de normalização
- Suporte a integridade referencial

#### Modelo Orientado a Objetos
- Dados e métodos encapsulados em objetos
- Suporte a herança e polimorfismo
- Identidade de objetos
- Relacionamentos por referência

#### Modelo Hierárquico
- Estrutura em árvore
- Relacionamentos pai-filho
- Navegação através de ponteiros
- Adequado para dados com estrutura hierárquica

#### Modelo em Rede
- Extensão do modelo hierárquico
- Permite múltiplos pais
- Estrutura de grafo
- Maior flexibilidade que o hierárquico

### Exemplo de Modelo Lógico Relacional
```sql
CLIENTE (
    id_cliente INTEGER PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cpf CHAR(11) UNIQUE,
    email VARCHAR(100),
    telefone VARCHAR(15)
)

PEDIDO (
    id_pedido INTEGER PRIMARY KEY,
    numero VARCHAR(20) UNIQUE,
    data_pedido DATE,
    valor_total DECIMAL(10,2),
    id_cliente INTEGER,
    FOREIGN KEY (id_cliente) REFERENCES CLIENTE(id_cliente)
)
```

## Modelo Físico

### Definição
O modelo físico descreve como os dados serão armazenados fisicamente no banco de dados, considerando aspectos específicos do SGBD escolhido e otimizações de performance.

### Características
- **Dependente de SGBD**: Considera particularidades do sistema específico
- **Otimização**: Foca em performance e eficiência
- **Detalhamento técnico**: Especifica estruturas físicas de armazenamento
- **Implementação**: Pronto para execução no SGBD

### Aspectos Considerados
- **Tipos de dados específicos**: Conforme SGBD utilizado
- **Índices**: Para otimização de consultas
- **Particionamento**: Divisão física dos dados
- **Tablespaces**: Organização do armazenamento
- **Constraints**: Restrições de integridade
- **Triggers**: Procedimentos automáticos
- **Views**: Visões dos dados

### Exemplo de Modelo Físico (PostgreSQL)
```sql
CREATE TABLE cliente (
    id_cliente SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cpf CHAR(11) UNIQUE,
    email VARCHAR(100),
    telefone VARCHAR(15),
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ativo BOOLEAN DEFAULT TRUE
);

CREATE INDEX idx_cliente_cpf ON cliente(cpf);
CREATE INDEX idx_cliente_nome ON cliente(nome);

CREATE TABLE pedido (
    id_pedido SERIAL PRIMARY KEY,
    numero VARCHAR(20) UNIQUE NOT NULL,
    data_pedido DATE DEFAULT CURRENT_DATE,
    valor_total NUMERIC(10,2) CHECK (valor_total >= 0),
    id_cliente INTEGER NOT NULL,
    status VARCHAR(20) DEFAULT 'PENDENTE',
    FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente)
        ON DELETE RESTRICT ON UPDATE CASCADE
);

CREATE INDEX idx_pedido_cliente ON pedido(id_cliente);
CREATE INDEX idx_pedido_data ON pedido(data_pedido);
```

## Modelo Dimensional

### Definição
O modelo dimensional é especializado para sistemas de apoio à decisão (Data Warehouses), organizando dados de forma otimizada para análises e relatórios.

### Características
- **Orientado a consultas**: Otimizado para leitura e análise
- **Estrutura desnormalizada**: Prioriza performance sobre redundância
- **Perspectiva temporal**: Mantém histórico dos dados
- **Agregações**: Suporte a diferentes níveis de sumarização

### Componentes Principais

#### Tabelas Fato
- Contêm métricas quantitativas
- Chaves estrangeiras para dimensões
- Medidas aditivas, semi-aditivas ou não-aditivas
- Grande volume de registros

#### Tabelas Dimensão
- Contêm atributos descritivos
- Contexto para análise dos fatos
- Hierarquias de agregação
- Relativamente pequenas

### Esquemas Dimensionais

#### Star Schema (Esquema Estrela)
- Tabela fato central
- Dimensões conectadas diretamente
- Estrutura simples e intuitiva
- Performance otimizada

#### Snowflake Schema (Esquema Floco de Neve)
- Dimensões normalizadas
- Reduz redundância
- Estrutura mais complexa
- Possível impacto na performance

#### Galaxy Schema (Esquema Galáxia)
- Múltiplas tabelas fato
- Dimensões compartilhadas
- Adequado para análises complexas
- Maior flexibilidade

### Exemplo de Modelo Dimensional
```sql
-- Tabela Fato Vendas
CREATE TABLE fato_vendas (
    sk_produto INTEGER,
    sk_cliente INTEGER,
    sk_tempo INTEGER,
    sk_loja INTEGER,
    quantidade INTEGER,
    valor_venda NUMERIC(10,2),
    custo NUMERIC(10,2),
    lucro NUMERIC(10,2),
    PRIMARY KEY (sk_produto, sk_cliente, sk_tempo, sk_loja)
);

-- Dimensão Produto
CREATE TABLE dim_produto (
    sk_produto INTEGER PRIMARY KEY,
    id_produto INTEGER,
    nome_produto VARCHAR(100),
    categoria VARCHAR(50),
    subcategoria VARCHAR(50),
    marca VARCHAR(50)
);

-- Dimensão Tempo
CREATE TABLE dim_tempo (
    sk_tempo INTEGER PRIMARY KEY,
    data DATE,
    ano INTEGER,
    mes INTEGER,
    trimestre INTEGER,
    dia_semana VARCHAR(20),
    eh_feriado BOOLEAN
);
```

## Comparação entre os Modelos

| Aspecto | Conceitual | Lógico | Físico | Dimensional |
|---------|------------|--------|--------|-------------|
| **Nível de Abstração** | Alto | Médio | Baixo | Médio |
| **Dependência de Tecnologia** | Independente | Parcial | Total | Parcial |
| **Público Alvo** | Usuários/Analistas | Projetistas | DBAs/Desenvolvedores | Analistas BI |
| **Foco Principal** | Negócio | Estrutura | Implementação | Análise |
| **Detalhamento** | Baixo | Médio | Alto | Médio |

## Processo de Desenvolvimento

### Sequência Tradicional
1. **Levantamento de Requisitos**
2. **Modelo Conceitual**
3. **Modelo Lógico**
4. **Modelo Físico**
5. **Implementação**

### Abordagens Alternativas
- **Top-Down**: Do conceitual para o físico
- **Bottom-Up**: Do físico existente para o conceitual
- **Middle-Out**: Inicia no lógico
- **Prototipagem**: Desenvolvimento iterativo

## Boas Práticas

### Para Todos os Modelos
- Manter documentação atualizada
- Usar nomenclatura consistente
- Validar com stakeholders
- Considerar requisitos não-funcionais

### Específicas por Modelo
- **Conceitual**: Foco na clareza e compreensão
- **Lógico**: Atenção à normalização e integridade
- **Físico**: Priorizar performance e manutenibilidade
- **Dimensional**: Otimizar para consultas analíticas

## Ferramentas de Modelagem

### Conceitual e Lógico
- **brModelo**: Ferramenta brasileira gratuita
- **MySQL Workbench**: Integrado com MySQL
- **ERwin**: Solução corporativa
- **PowerDesigner**: Suite completa da SAP

### Físico
- **SGBD específicos**: pgAdmin, MySQL Workbench, SQL Server Management Studio
- **Ferramentas universais**: DBeaver, Navicat

### Dimensional
- **Pentaho**: Suite BI open source
- **Microsoft SQL Server Integration Services**
- **Talend**: Plataforma de integração

## Conclusão

A compreensão dos diferentes tipos de modelos de dados é fundamental para o sucesso de projetos de banco de dados. Cada modelo tem seu papel específico no ciclo de desenvolvimento, desde a compreensão inicial do negócio até a implementação otimizada no sistema de produção.

A escolha adequada do tipo de modelo e da abordagem de desenvolvimento depende de fatores como complexidade do projeto, recursos disponíveis, prazos e requisitos específicos de performance e manutenibilidade.