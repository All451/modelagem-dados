# Conversão do MER para Modelo Relacional

## Introdução

A conversão do Modelo Entidade-Relacionamento (MER) para o modelo relacional é um processo sistemático que transforma os conceitos conceituais em estruturas de dados implementáveis em sistemas de gerenciamento de banco de dados relacionais.

## Regras de Conversão

### 1. Conversão de Entidades

#### Entidades Regulares
- Cada entidade forte torna-se uma **tabela**
- Cada atributo simples torna-se uma **coluna**
- A chave primária da entidade torna-se a **chave primária da tabela**
- Atributos multivalorados são tratados separadamente

**Exemplo:**
```
Entidade: CLIENTE (id_cliente, nome, email, telefone)
↓
Tabela: CLIENTE
- id_cliente (PK)
- nome
- email  
- telefone
```

#### Entidades Fracas
- Tornam-se tabelas que incluem:
  - Seus próprios atributos
  - A chave primária da entidade forte relacionada (como chave estrangeira)
  - A chave primária é composta pela chave parcial + chave estrangeira

**Exemplo:**
```
Entidade Forte: PEDIDO (id_pedido, data, valor_total)
Entidade Fraca: ITEM_PEDIDO (numero_item, quantidade, preco_unitario)
↓
Tabela: ITEM_PEDIDO
- id_pedido (FK)
- numero_item
- quantidade
- preco_unitario
- PK: (id_pedido, numero_item)
```

### 2. Conversão de Atributos

#### Atributos Simples
- Tornam-se colunas na tabela correspondente

#### Atributos Compostos
- Decompostos em atributos simples
- Cada componente torna-se uma coluna separada

**Exemplo:**
```
Atributo: endereco (rua, numero, bairro, cidade, cep)
↓
Colunas: endereco_rua, endereco_numero, endereco_bairro, endereco_cidade, endereco_cep
```

#### Atributos Multivalorados
- Criam uma nova tabela com:
  - Chave estrangeira referenciando a entidade original
  - Coluna para o valor do atributo multivalorado
  - Chave primária composta

**Exemplo:**
```
Entidade: PESSOA (id_pessoa, nome)
Atributo multivalorado: telefones
↓
Tabela: PESSOA_TELEFONE
- id_pessoa (FK)
- telefone
- PK: (id_pessoa, telefone)
```

#### Atributos Derivados
- Geralmente não são armazenados
- Calculados quando necessário
- Se armazenados, tratados como atributos simples

### 3. Conversão de Relacionamentos

#### Relacionamento 1:1 (Um para Um)

**Abordagem 1: Fusão de Tabelas**
- Combinar as duas entidades em uma única tabela
- Usado quando a participação é total

**Abordagem 2: Chave Estrangeira**
- Adicionar chave estrangeira em uma das tabelas
- Escolher a entidade com participação total

**Exemplo:**
```
PESSOA (1) ←→ (1) PASSAPORTE
↓
Opção 1: PESSOA_PASSAPORTE (id_pessoa, nome, numero_passaporte, data_emissao)
Opção 2: PESSOA (id_pessoa, nome, id_passaporte)
         PASSAPORTE (id_passaporte, numero, data_emissao)
```

#### Relacionamento 1:N (Um para Muitos)
- Adicionar chave estrangeira na tabela do lado "N"
- A chave estrangeira referencia a chave primária do lado "1"

**Exemplo:**
```
DEPARTAMENTO (1) ←→ (N) FUNCIONARIO
↓
DEPARTAMENTO (id_departamento, nome)
FUNCIONARIO (id_funcionario, nome, salario, id_departamento)
```

#### Relacionamento N:M (Muitos para Muitos)
- Criar nova tabela (tabela de ligação)
- Incluir chaves estrangeiras de ambas as entidades
- Chave primária composta pelas chaves estrangeiras
- Incluir atributos do relacionamento

**Exemplo:**
```
ALUNO (N) ←→ (M) DISCIPLINA
Relacionamento: MATRICULA (data_matricula, nota_final)
↓
ALUNO (id_aluno, nome, curso)
DISCIPLINA (id_disciplina, nome, carga_horaria)
MATRICULA (id_aluno, id_disciplina, data_matricula, nota_final)
PK: (id_aluno, id_disciplina)
```

### 4. Conversão de Relacionamentos Ternários

- Criar tabela com chaves estrangeiras das três entidades
- Chave primária composta pelas três chaves estrangeiras
- Incluir atributos específicos do relacionamento

**Exemplo:**
```
FORNECEDOR (N) ←→ PRODUTO (M) ←→ PROJETO (P)
Relacionamento: FORNECIMENTO (quantidade, data_entrega)
↓
FORNECIMENTO (id_fornecedor, id_produto, id_projeto, quantidade, data_entrega)
PK: (id_fornecedor, id_produto, id_projeto)
```

### 5. Conversão de Especializações/Generalizações

#### Abordagem 1: Tabela Única
- Uma tabela para toda a hierarquia
- Incluir atributo discriminador
- Usar valores NULL para atributos não aplicáveis

#### Abordagem 2: Tabelas Separadas
- Tabela para superclasse
- Tabela para cada subclasse
- Subclasses referenciam superclasse via chave estrangeira

#### Abordagem 3: Apenas Subclasses
- Eliminar tabela da superclasse
- Cada subclasse inclui todos os atributos

**Exemplo - Especialização de VEICULO:**
```
Abordagem 1: VEICULO (id_veiculo, marca, modelo, tipo, num_portas, capacidade_carga)

Abordagem 2: VEICULO (id_veiculo, marca, modelo)
             CARRO (id_veiculo, num_portas)
             CAMINHAO (id_veiculo, capacidade_carga)

Abordagem 3: CARRO (id_veiculo, marca, modelo, num_portas)
             CAMINHAO (id_veiculo, marca, modelo, capacidade_carga)
```

## Exemplo Prático Completo

### MER Original
```
Entidades:
- CLIENTE (id_cliente, nome, email, telefones*)
- PEDIDO (id_pedido, data, valor_total)
- PRODUTO (id_produto, nome, preco, categoria)

Relacionamentos:
- CLIENTE (1) ←→ (N) PEDIDO
- PEDIDO (N) ←→ (M) PRODUTO [ITEM_PEDIDO: quantidade, preco_unitario]
```

### Modelo Relacional Resultante
```sql
-- Tabela para entidade CLIENTE
CLIENTE (
    id_cliente INTEGER PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100)
);

-- Tabela para atributo multivalorado telefones
CLIENTE_TELEFONE (
    id_cliente INTEGER,
    telefone VARCHAR(20),
    PRIMARY KEY (id_cliente, telefone),
    FOREIGN KEY (id_cliente) REFERENCES CLIENTE(id_cliente)
);

-- Tabela para entidade PEDIDO
PEDIDO (
    id_pedido INTEGER PRIMARY KEY,
    data DATE,
    valor_total DECIMAL(10,2),
    id_cliente INTEGER,
    FOREIGN KEY (id_cliente) REFERENCES CLIENTE(id_cliente)
);

-- Tabela para entidade PRODUTO
PRODUTO (
    id_produto INTEGER PRIMARY KEY,
    nome VARCHAR(100),
    preco DECIMAL(8,2),
    categoria VARCHAR(50)
);

-- Tabela para relacionamento N:M
ITEM_PEDIDO (
    id_pedido INTEGER,
    id_produto INTEGER,
    quantidade INTEGER,
    preco_unitario DECIMAL(8,2),
    PRIMARY KEY (id_pedido, id_produto),
    FOREIGN KEY (id_pedido) REFERENCES PEDIDO(id_pedido),
    FOREIGN KEY (id_produto) REFERENCES PRODUTO(id_produto)
);
```

## Verificação da Conversão

### Checklist de Validação
- [ ] Todas as entidades foram convertidas em tabelas
- [ ] Todas as chaves primárias foram definidas
- [ ] Relacionamentos foram implementados corretamente
- [ ] Atributos multivalorados foram normalizados
- [ ] Integridade referencial foi preservada
- [ ] Restrições de cardinalidade foram respeitadas

### Normalização Pós-Conversão
Após a conversão, é importante verificar se o modelo está nas formas normais apropriadas:
- **1FN**: Eliminar grupos repetitivos
- **2FN**: Eliminar dependências parciais
- **3FN**: Eliminar dependências transitivas

## Considerações Finais

A conversão do MER para modelo relacional é fundamental para a implementação de bancos de dados eficientes. O processo deve considerar:

1. **Performance**: Escolher abordagens que otimizem consultas frequentes
2. **Integridade**: Garantir que todas as restrições sejam preservadas
3. **Manutenibilidade**: Criar estruturas claras e bem documentadas
4. **Escalabilidade**: Considerar crescimento futuro dos dados

A prática constante e análise de diferentes cenários são essenciais para dominar este processo de conversão.