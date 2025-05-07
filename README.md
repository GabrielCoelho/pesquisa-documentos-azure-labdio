# Laboratório: Explorando o Azure AI Search e Mineração de Conhecimento

## Introdução

Este repositório documenta minha experiência prática no laboratório de Azure AI Search, onde trabalhei em um cenário de mineração de conhecimento para a empresa fictícia "Fourth Coffee" - uma rede nacional de cafeterias. O desafio era construir uma solução que facilitasse a busca de insights sobre experiências de clientes a partir de avaliações.

## Objetivos do Laboratório

* Criar recursos necessários no Azure
* Extrair dados de uma fonte de dados (avaliações de clientes)
* Enriquecer dados com habilidades de IA
* Utilizar o indexador do Azure no portal
* Consultar o índice de pesquisa
* Revisar resultados salvos em um Knowledge Store

## Recursos do Azure Utilizados

Para este laboratório, foram necessários três recursos principais:

1. **Azure AI Search**: gerencia a indexação e consultas
2. **Azure AI Services**: fornece serviços de IA para enriquecer os dados
3. **Storage Account**: armazena documentos brutos e outros dados

## Passo a Passo Detalhado

### 1. Criação de Recursos no Azure

Primeiro, provisionei os seguintes recursos no Azure Portal:

* **Azure AI Search** (Nível Básico)
* **Azure AI Services** (Tier Standard S0)
* **Storage Account** com redundância local (LRS)

Todos os recursos foram configurados na mesma região para garantir compatibilidade e melhor desempenho.

### 2. Upload de Documentos para o Azure Storage

Após a criação dos recursos:

1. Criei um container chamado "coffee-reviews" no Storage Account
2. Configurei acesso anônimo ao blob para facilitar o acesso aos dados
3. Baixei o pacote de avaliações de café de [https://aka.ms/mslearn-coffee-reviews](https://aka.ms/mslearn-coffee-reviews)
4. Fiz upload dos arquivos extraídos para o container

### 3. Indexação dos Documentos

Utilizei o assistente "Import data" do Azure AI Search para:

1. Conectar com o Azure Blob Storage como fonte de dados
2. Configurar a extração de conteúdo e metadados
3. Adicionar habilidades cognitivas através de um skillset personalizado:
   * Ativei OCR para processar texto em imagens
   * Configurei o campo de dados de origem como merged_content
   * Defini a granularidade de enriquecimento para páginas (chunks de 5000 caracteres)
   * Selecionei campos enriquecidos específicos:
     * Extração de nomes de locais (locations)
     * Extração de frases-chave (keyphrases)
     * Detecção de sentimento (sentiment)
     * Geração de tags de imagens (imageTags)
     * Geração de legendas para imagens (imageCaption)

4. Salvei os enriquecimentos em um Knowledge Store, incluindo:
   * Projeções de imagens
   * Documentos
   * Páginas
   * Frases-chave
   * Entidades
   * Detalhes e referências de imagens

5. Customizei o índice alvo:
   * Nomeei como "coffee-index"
   * Configurei a chave para metadata_storage_path
   * Marquei campos importantes como filtráveis

6. Criei o indexador:
   * Nomeei como "coffee-indexer"
   * Executei uma vez para processar todos os documentos

### 4. Consulta ao Índice

Utilizei o Search Explorer do Azure AI Search para testar diferentes consultas:

1. **Consulta geral**: Utilizei a consulta `search=*&$count=true` para retornar todos os documentos no índice.

2. **Filtragem por localização**: Utilizei `search=locations:'Chicago'&$count=true` para encontrar reviews relacionadas a Chicago (3 resultados).

3. **Filtragem por sentimento**: Utilizei `search=sentiment:'negative'&$count=true` para identificar avaliações negativas (1 resultado).

Analisei os resultados e observei como o score de pesquisa (@search.score) é atribuído pelo mecanismo para indicar a relevância dos resultados.

### 5. Exploração do Knowledge Store

Explorei o Knowledge Store criado no Azure Storage:

1. Acessei o container "knowledge-store" para visualizar:
   * Projeções de objetos (objectprojection.json) para cada documento
   * Imagens extraídas dos documentos
   * Tabelas com entidades e frases-chave

2. Analisei as frases-chave extraídas na tabela "coffeeSkillsetKeyPhrases", observando como os campos podem ser vinculados como em um banco de dados relacional.

## Insights e Aprendizados

* A mineração de conhecimento do Azure permite transformar dados não estruturados em informações acionáveis
* O enriquecimento com IA melhora significativamente a capacidade de busca e análise:
  * Extração de locais mencionados nas avaliações
  * Análise de sentimento para identificar experiências positivas e negativas
  * Extração automática de frases-chave para entender temas recorrentes

* O Knowledge Store oferece valor adicional ao preservar os enriquecimentos em um formato utilizável para análises posteriores

* Os filtros de pesquisa permitem segmentar rapidamente os dados (ex: encontrar avaliações negativas em Chicago)

## Conclusão

Este laboratório demonstrou como o Azure AI Search pode ser aplicado para resolver problemas reais de negócios, como analisar feedback de clientes de uma rede de cafeterias. A solução criada permite que a Fourth Coffee extraia insights valiosos de avaliações de clientes de forma escalável, identificando tendências, locais problemáticos e temas recorrentes nas experiências dos clientes.

As técnicas demonstradas podem ser aplicadas a diversos cenários de negócios onde a análise de dados não estruturados é necessária, como análise de feedback de produtos, processamento de documentos internos ou mineração de conhecimento de bases de dados de suporte ao cliente.

---

## Recursos Adicionais

* [Página de serviço do Azure AI Search](https://azure.microsoft.com/services/search/)
* [Documentação do Azure AI Search](https://docs.microsoft.com/azure/search/)
* [Tutorial de mineração de conhecimento](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
