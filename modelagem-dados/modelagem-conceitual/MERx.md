# Modelo Entidade-Relacionamento (MER)

## Introdução

O Modelo Entidade-Relacionamento (MER) é uma técnica de modelagem conceitual amplamente utilizada para representar a estrutura lógica de um banco de dados. Desenvolvido por Peter Chen em 1976, o MER fornece uma representação gráfica clara e intuitiva dos dados e seus relacionamentos.

## Componentes Fundamentais

### Entidades

Uma entidade representa um objeto ou conceito do mundo real que pode ser identificado de forma única e sobre o qual desejamos armazenar informações.

#### Características das Entidades
- **Conjunto de entidades**: Coleção de entidades similares
- **Instância de entidade**: Um objeto específico dentro do conjunto
- **Identificação única**: Cada instância deve ser distinguível

#### Tipos de Entidades

##### Entidades Fortes
- Existem independentemente de outras entidades
- Possuem chave primária própria
- Representadas por retângulos simples

**Exemplo:**
```
CLIENTE - existe independentemente
Atributos: id_cliente, nome, cpf, email
```

##### Entidades Fracas
- Dependem de outra entidade para existir
- Não possuem chave primária completa própria
- Representadas por retângulos duplos

**Exemplo:**
```
DEPENDENTE - depende de FUNCIONARIO
Atributos: nome, data_nascimento, parentesco
```

#### Representação Gráfica
- **Entidade Forte**: Retângulo simples
- **Entidade Fraca**: Retângulo duplo
- **Nome da entidade**: Em maiúsculas, no singular

### Atributos

Atributos são propriedades ou características das entidades que descrevem aspectos relevantes dos objetos modelados.

#### Tipos de Atributos

##### Atributos Simples vs Compostos

**Atributos Simples**: Indivisíveis
```
nome, idade, salario
```

**Atributos Compostos**: Podem ser divididos em partes menores
```
endereco {
    rua,
    numero,
    bairro,
    cidade,
    cep
}
```

##### Atributos Monovalorados vs Multivalorados

**Monovalorados**: Um único valor por entidade
```
cpf, data_nascimento
```

**Multivalorados**: Múltiplos valores por entidade
```
telefones {telefone1, telefone2, telefone3}
```

##### Atributos Armazenados vs Derivados

**Armazenados**: Valores explicitamente armazenados
```
data_nascimento, salario
```

**Derivados**: Calculados a partir de outros atributos
```
idade (derivada de data_nascimento)
salario_anual (derivado de salario_mensal)
```

#### Representação Gráfica
- **Atributo simples**: Elipse simples
- **Atributo composto**: Elipse ligada a elipses menores
- **Atributo multivalorado**: Elipse dupla
- **Atributo derivado**: Elipse tracejada
- **Chave primária**: Atributo sublinhado

### Relacionamentos

Relacionamentos representam associações entre duas ou mais entidades.

#### Grau de Relacionamento

##### Relacionamento Binário (Grau 2)
Associação entre duas entidades
```
CLIENTE ----compra----> PRODUTO
FUNCIONARIO ----trabalha----> DEPARTAMENTO
```

##### Relacionamento Ternário (Grau 3)
Associação entre três entidades
```
MEDICO ----consulta----> PACIENTE
           |
           v
        MEDICAMENTO
```

##### Relacionamento Unário (Grau 1)
Relacionamento de uma entidade consigo mesma
```
FUNCIONARIO ----supervisiona----> FUNCIONARIO
PESSOA ----casada_com----> PESSOA
```

#### Cardinalidade

A cardinalidade especifica quantas instâncias de uma entidade podem estar associadas a quantas instâncias de outra entidade.

##### Tipos de Cardinalidade

**Um para Um (1:1)**
```
PESSOA ----possui----> PASSAPORTE
- Uma pessoa possui no máximo um passaporte
- Um passaporte pertence a exatamente uma pessoa
```

**Um para Muitos (1:N)**
```
DEPARTAMENTO ----possui----> FUNCIONARIO
- Um departamento pode ter vários funcionários
- Um funcionário pertence a exatamente um departamento
```

**Muitos para Um (N:1)**
```
FUNCIONARIO ----trabalha----> DEPARTAMENTO
- Vários funcionários trabalham em um departamento
- Um funcionário trabalha em um departamento
```

**Muitos para Muitos (N:M)**
```
ESTUDANTE ----matricula----> DISCIPLINA
- Um estudante pode se matricular em várias disciplinas
- Uma disciplina pode ter vários estudantes matriculados
```

#### Participação

##### Participação Total (Obrigatória)
- Toda instância da entidade deve participar do relacionamento
- Representada por linha dupla

##### Participação Parcial (Opcional)
- Nem toda instância precisa participar do relacionamento
- Representada por linha simples

**Exemplo:**
```
FUNCIONARIO ====trabalha==== DEPARTAMENTO
(linha dupla: todo funcionário deve trabalhar em um departamento)

FUNCIONARIO ----gerencia---- PROJETO
(linha simples: nem todo funcionário gerencia projeto)
```

### Relacionamentos com Atributos

Relacionamentos podem ter atributos próprios, especialmente em relacionamentos N:M.

**Exemplo:**
```
FUNCIONARIO ----trabalha----> PROJETO
Atributos do relacionamento: horas_trabalhadas, data_inicio
```

## Restrições de Integridade

### Restrições de Domínio
Definem os valores válidos para os atributos.

```
idade: inteiro entre 0 e 120
salario: decimal positivo
status: {'ativo', 'inativo', 'suspenso'}
```

### Restrições de Chave
Garantem a unicidade das instâncias.

#### Chave Candidata
Conjunto mínimo de atributos que identifica unicamente uma instância.

#### Chave Primária
Chave candidata escolhida para identificar as instâncias.

#### Chave Alternativa
Chaves candidatas que não foram escolhidas como primárias.

### Restrições de Integridade Referencial
Garantem consistência entre relacionamentos.

```
- Toda referência a uma entidade deve apontar para uma instância existente
- Restrições de inserção, atualização e exclusão
```

## Entidade Associativa

Quando um relacionamento N:M possui atributos ou precisa se relacionar com outras entidades, pode ser convertido em uma entidade associativa.

**Antes:**
```
MEDICO ----consulta----> PACIENTE
Atributos: data, hora, diagnostico
```

**Depois:**
```
MEDICO ----realiza----> CONSULTA ----recebe----> PACIENTE
CONSULTA tem atributos: data, hora, diagnostico
```

## Generalização e Especialização

### Generalização
Processo de identificar características comuns entre entidades e criar uma superclasse.

### Especialização  
Processo de identificar características específicas e criar subclasses.

**Exemplo:**
```
PESSOA (superclasse)
├── FUNCIONARIO (subclasse)
│   └── atributos específicos: matricula, salario
└── CLIENTE (subclasse)
    └── atributos específicos: limite_credito
```

#### Tipos de Especialização

##### Disjunta vs Sobreposta
- **Disjunta**: Uma instância só pode pertencer a uma subclasse
- **Sobreposta**: Uma instância pode pertencer a múltiplas subclasses

##### Total vs Parcial
- **Total**: Toda instância da superclasse deve pertencer a alguma subclasse
- **Parcial**: Algumas instâncias podem não pertencer a nenhuma subclasse

## Agregação

A agregação permite tratar um relacionamento como uma entidade de nível superior.

**Exemplo:**
```
(FUNCIONARIO trabalha PROJETO) supervisiona GERENTE
```

Aqui, o relacionamento "trabalha" é tratado como uma unidade que se relaciona com GERENTE.

## Exemplo Completo: Sistema de Biblioteca

### Entidades
```
USUARIO
- codigo_usuario (PK)
- nome
- telefone
- endereco {rua, numero, bairro, cidade}
- tipo ('estudante', 'professor', 'funcionario')

LIVRO
- isbn (PK)
- titulo
- editora
- ano_publicacao
- numero_paginas

AUTOR
- codigo_autor (PK)
- nome
- nacionalidade
- data_nascimento

EMPRESTIMO
- codigo_emprestimo (PK)
- data_emprestimo
- data_devolucao_prevista
- data_devolucao_efetiva
- status ('ativo', 'devolvido', 'atrasado')
```

### Relacionamentos
```
USUARIO (1) ----realiza----> (N) EMPRESTIMO
EMPRESTIMO (N) ----inclui----> (1) LIVRO
AUTOR (N) ----escreve----> (N) LIVRO
```

### Regras de Negócio
1. Um usuário pode realizar vários empréstimos
2. Um empréstimo é de exatamente um livro
3. Um livro pode ter vários autores
4. Um autor pode escrever vários livros
5. Um livro só pode estar em um empréstimo ativo por vez

## Validação do Modelo

### Verificações Essenciais

1. **Completude**: Todos os requisitos estão representados?
2. **Correção**: O modelo reflete corretamente a realidade?
3. **Minimalidade**: Não há redundâncias desnecessárias?
4. **Expressividade**: O modelo é claro e compreensível?
5. **Consistência**: Não há contradições internas?

### Técnicas de Validação

1. **Revisão com stakeholders**
2. **Análise de cenários de uso**
3. **Verificação de regras de negócio**
4. **Simulação de operações**

## Limitações do Modelo ER

1. **Semântica limitada**: Nem todas as restrições podem ser expressas
2. **Relacionamentos complexos**: Dificuldade com relacionamentos ternários ou superiores
3. **Aspectos temporais**: Limitações para modelar dados históricos
4. **Operações**: Não modela comportamentos, apenas estrutura

## Extensões do Modelo ER

### Modelo ER Estendido (EER)
- Especialização/Generalização
- Agregação
- Relacionamentos de categoria

### Modelo ER Temporal
- Incorpora aspectos temporais
- Histórico de mudanças
- Validade temporal

## Ferramentas para MER

### Ferramentas Gratuitas
- **brModelo**: Brasileira, específica para MER
- **Draw.io**: Online, templates ER
- **Lucidchart**: Online, colaborativo

### Ferramentas Comerciais
- **ERwin**: Ferramenta profissional
- **PowerDesigner**: Suite completa
- **Visio**: Microsoft, templates ER

## Conclusão

O Modelo Entidade-Relacionamento é uma ferramenta fundamental na modelagem conceitual de dados. Sua representação gráfica intuitiva facilita a comunicação entre analistas, desenvolvedores e usuários finais, enquanto sua base teórica sólida garante modelos consistentes e bem estruturados.

A compreensão profunda dos conceitos do MER é essencial para criar modelos que representem adequadamente os requisitos do negócio e sirvam como base sólida para as fases subsequentes do desenvolvimento do banco de dados.