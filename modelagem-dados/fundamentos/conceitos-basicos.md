# Conceitos Básicos de Modelagem de Dados

## 🎯 Introdução

A modelagem de dados é o processo de criar uma representação abstrata de dados e suas relações para um sistema de informação. É fundamental para o desenvolvimento de sistemas eficientes e bem estruturados.

## 📊 O que é Modelagem de Dados?

A **modelagem de dados** é o processo de análise e definição de todos os diferentes tipos de dados que seu negócio coleta e armazena, bem como as relações entre esses tipos de dados.

### Objetivos principais:
- **Organização**: Estruturar dados de forma lógica
- **Integridade**: Garantir consistência e qualidade
- **Performance**: Otimizar acesso e consultas
- **Manutenibilidade**: Facilitar mudanças futuras

## 🏗️ Níveis de Modelagem

### 1. Modelagem Conceitual
- **Foco**: O que o sistema deve conter
- **Público**: Analistas de negócio, usuários finais
- **Características**:
  - Alto nível de abstração
  - Independente de tecnologia
  - Foca nos requisitos de negócio

### 2. Modelagem Lógica
- **Foco**: Como o sistema será estruturado
- **Público**: Analistas de sistemas, desenvolvedores
- **Características**:
  - Estruturas de dados detalhadas
  - Regras de negócio definidas
  - Independente de SGBD específico

### 3. Modelagem Física
- **Foco**: Como o sistema será implementado
- **Público**: DBAs, desenvolvedores
- **Características**:
  - Específica para um SGBD
  - Otimizada para performance
  - Define estruturas físicas

## 🔑 Elementos Fundamentais

### Entidades
Representam objetos do mundo real sobre os quais queremos armazenar informações.

**Exemplos**:
- Cliente
- Produto
- Pedido
- Funcionário

### Atributos
São características ou propriedades das entidades.

**Tipos de atributos**:
- **Simples**: Não podem ser divididos (ex: nome)
- **Compostos**: Podem ser divididos (ex: endereço)
- **Únicos**: Valor único para cada instância (ex: CPF)
- **Multivalorados**: Podem ter múltiplos valores (ex: telefones)

### Relacionamentos
Representam associações entre entidades.

**Cardinalidades**:
- **1:1** (Um para Um): Ex: Pessoa ↔ CPF
- **1:N** (Um para Muitos): Ex: Cliente → Pedidos
- **N:M** (Muitos para Muitos): Ex: Produtos ↔ Categorias

## 🎨 Notações e Diagramas

### Modelo Entidade-Relacionamento (MER)
- **Entidades**: Retângulos
- **Atributos**: Elipses
- **Relacionamentos**: Losangos
- **Chaves primárias**: Atributos sublinhados

### Diagrama Entidade-Relacionamento (DER)
Representação gráfica do MER usando símbolos padronizados.

## 📋 Processo de Modelagem

### Etapa 1: Levantamento de Requisitos
- Entrevistas com usuários
- Análise de documentos
- Observação de processos
- Identificação de regras de negócio

### Etapa 2: Identificação de Entidades
- Substantivos importantes
- Objetos sobre os quais se armazena dados
- Coisas tangíveis ou conceitos

### Etapa 3: Identificação de Atributos
- Características das entidades
- Dados que precisam ser armazenados
- Propriedades relevantes para o negócio

### Etapa 4: Identificação de Relacionamentos
- Verbos que conectam entidades
- Associações entre objetos
- Dependências funcionais

### Etapa 5: Definição de Cardinalidades
- Quantas instâncias se relacionam
- Restrições de integridade
- Regras de negócio específicas

## ⚠️ Desafios Comuns

### Problemas de Identificação
- **Entidades vs. Atributos**: Quando algo deve ser entidade?
- **Relacionamentos complexos**: Como modelar associações ternárias?
- **Redundância**: Como evitar informações duplicadas?

### Soluções Práticas
1. **Regra dos substantivos**: Entidades são substantivos importantes
2. **Teste de instância**: Se tem múltiplas instâncias, é entidade
3. **Análise de dependência**: Atributos dependem de entidades

## 🏆 Boas Práticas

### Nomenclatura
- Use nomes descritivos e consistentes
- Evite abreviações desnecessárias
- Mantenha padrão de nomenclatura
- Use singular para entidades

### Documentação
- Documente regras de negócio
- Explique decisões de modelagem
- Mantenha versionamento
- Use exemplos práticos

### Validação
- Revise com usuários finais
- Teste cenários de uso
- Verifique integridade referencial
- Valide cardinalidades

## 🎓 Exercícios Práticos

### Exercício 1: Identificação de Entidades
**Cenário**: Sistema de biblioteca
**Tarefa**: Identifique entidades, atributos e relacionamentos

### Exercício 2: Modelagem Conceitual
**Cenário**: Sistema de vendas online
**Tarefa**: Crie um MER básico

### Exercício 3: Análise de Cardinalidades
**Cenário**: Sistema acadêmico
**Tarefa**: Defina cardinalidades entre entidades

## 📚 Próximos Passos

1. **Normalizacao.md**: Aprenda sobre formas normais
2. **Tipos-modelos.md**: Explore diferentes tipos de modelos
3. **Modelagem Conceitual**: Pratique com exemplos reais

## 🔗 Links Úteis

- [Modelo Entidade-Relacionamento - Wikipedia](https://pt.wikipedia.org/wiki/Modelo_entidade_relacionamento)
- [Tutorial sobre MER](https://www.devmedia.com.br/modelo-entidade-relacionamento-mer-e-diagrama-entidade-relacionamento-der/14332)

---

**Próximo**: [Normalização](normalizacao.md) | **Voltar**: [README](../README.md)