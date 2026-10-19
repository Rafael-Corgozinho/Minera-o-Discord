## Análise de Comunidades do Discord com Ciência de Dados

Projeto de Ciência de Dados voltado à organização, exploração e análise de mensagens de comunidades do Discord. O objetivo é construir um pipeline reproduzível para investigar padrões de atividade, interações e características linguísticas em diferentes servidores e canais.

> **Status:** coleta realizada; auditoria, organização dos dados e análises exploratórias em desenvolvimento. As análises de processamento de linguagem natural (NLP) ainda serão avaliadas e validadas antes de serem aplicadas em larga escala.

## Visão geral

O projeto parte de uma base com milhões de mensagens coletadas de diferentes servidores do Discord. O objetivo é transformar esses dados em uma estrutura organizada e consultável, que permita estudar tanto padrões gerais das comunidades quanto interações concentradas em canais menores.

As perguntas de interesse incluem:

- Como a atividade varia entre servidores e canais?
- Quais são os padrões temporais de participação e interação?
- Como se distribuem respostas, links, menções e outros elementos das mensagens?
- Quais temas aparecem com maior frequência nas conversas?
- Que métodos podem ser utilizados para estudar sentimento, toxicidade e discurso de ódio, considerando suas limitações?
- Como comparar comunidades sem confundir o volume absoluto de mensagens com a proporção de determinado fenômeno?

Essas perguntas orientam o trabalho, mas não representam resultados já confirmados.

## Dimensão atual dos dados

| Indicador | Quantidade |
|---|---:|
| Servidores coletados | Aproximadamente 100 |
| Arquivos Parquet | 2.051 |
| Mensagens | 15.886.553 |
| Colunas por arquivo inspecionado | 23 |

Os arquivos examinados apresentam um schema consistente, embora variem bastante em número de mensagens. A hipótese atual é que cada arquivo represente um canal de um servidor específico. Essa correspondência ainda precisa ser validada sistematicamente com os identificadores dos registros.

## Estrutura dos dados

Entre os campos identificados nos arquivos estão:

| Campo | Descrição |
|---|---|
| `guild_id` | Identificador do servidor |
| `channel_id` | Identificador do canal |
| `id` | Identificador da mensagem |
| `author.id` | Identificador do autor |
| `content` | Conteúdo textual da mensagem |
| `timestamp` | Data e hora da mensagem |
| `mentions` | Informações sobre usuários mencionados |
| `attachments` | Anexos associados à mensagem |
| `embeds` | Conteúdo incorporado |
| `edited_timestamp` | Data de edição, quando aplicável |

A estrutura conceitual do projeto é:

```text
Servidor (guild)
└── Canais
    └── Mensagens
        ├── Autor
        ├── Timestamp
        ├── Conteúdo
        └── Metadados
             |
             v
     Features e análises
             |
             v
   Agregações e visualizações
```

Os dados originais serão preservados. As transformações, classificações e métricas serão produzidas em etapas derivadas, mantendo os identificadores necessários para rastrear os resultados até os registros de origem.

## Princípios metodológicos

### 1. Preservar os dados originais

Os arquivos Parquet originais não devem ser alterados ou apagados durante a limpeza. As etapas de normalização e análise devem produzir resultados derivados e reproduzíveis.

### 2. Não descartar canais pequenos antecipadamente

Canais com poucas mensagens podem conter interações relevantes que seriam diluídas em canais muito movimentados. A inclusão de um canal deve depender da pergunta de pesquisa, e não apenas do número de mensagens.

### 3. Classificar sem excluir

Os canais poderão receber categorias como conversacional, administrativo, anúncios, comandos de bots ou desconhecido. Essa classificação servirá para selecionar dados em análises específicas, sem apagar os registros originais.

### 4. Diferenciar contagens de proporções

As análises deverão considerar tanto valores absolutos quanto proporções. Uma quantidade elevada de mensagens classificadas como tóxicas não significa, por si só, que um canal tenha uma taxa maior do fenômeno. Proporções calculadas em amostras pequenas também exigem cautela.

### 5. Validar métodos automatizados

Classificadores de sentimento, toxicidade ou discurso de ódio podem cometer erros e apresentar vieses. Antes de serem utilizados em grande escala, deverão ser testados em amostras e avaliados de acordo com critérios explícitos.

## Pipeline planejado

```text
Parquets originais
        |
        v
1. Auditoria e validação
        |
        v
2. Inventário de servidores e canais
        |
        v
3. Organização e consultas
        |
        v
4. Análise exploratória
        |
        v
5. Extração de features
        |
        v
6. Prototipagem e validação de NLP
        |
        v
7. Agregações, gráficos e relatórios
```

### Etapas de trabalho

1. **Auditoria dos dados:** verificar schema, tipos, valores nulos, identificadores, duplicatas e consistência dos arquivos.
2. **Organização:** confirmar a relação entre arquivos, servidores, canais e mensagens.
3. **Análise exploratória:** calcular estatísticas por canal e servidor, estudar a distribuição dos volumes e analisar a atividade temporal.
4. **Features básicas:** extrair características como comprimento das mensagens, palavras, links, menções e emojis.
5. **NLP:** testar métodos de análise de sentimento, identificação de tópicos e classificação de conteúdo, com validação prévia.
6. **Integração:** reunir resultados, gerar visualizações e documentar os procedimentos e as limitações.

## Tecnologias previstas

- **Python:** scripts de processamento, análise e experimentação.
- **Parquet:** armazenamento colunar dos dados.
- **DuckDB:** consultas analíticas sobre arquivos Parquet sem exigir que toda a base seja carregada na memória.
- **Pandas:** inspeção e manipulação de amostras e tabelas menores.
- **Bibliotecas de NLP e visualização:** serão selecionadas conforme as perguntas de pesquisa e os resultados dos testes iniciais.

As tecnologias listadas representam a stack prevista; nem todas as etapas ou bibliotecas estão necessariamente implementadas.

## Divisão de trabalho

O trabalho pode ser organizado em cinco frentes:

| Frente | Responsabilidade | Entrega esperada |
|---|---|---|
| 1. Engenharia e integridade | Auditar arquivos, schema, IDs e duplicatas | Inventário e relatório de qualidade |
| 2. Organização dos dados | Relacionar servidores, canais e mensagens | Estrutura consultável e inventários |
| 3. Análise exploratória | Estatísticas, distribuição de volumes e atividade temporal | Relatório exploratório e gráficos |
| 4. NLP | Testar features textuais e classificadores em amostras | Protótipo avaliado e limitações documentadas |
| 5. Metodologia e integração | Definir métricas, reunir resultados e criar visualizações | Relatório integrado ou painel |

As frentes podem avançar em paralelo quando possível, desde que compartilhem definições consistentes de identificadores, métricas e formatos de entrega.


## Reprodutibilidade e responsabilidade

- Preservar os dados brutos e documentar as transformações.
- Versionar o código e registrar parâmetros relevantes.
- Utilizar definições consistentes para as métricas.
- Distinguir resultados observados de hipóteses.
- Proteger identificadores e conteúdos sensíveis, limitando o acesso aos dados ao necessário para o projeto.
- Considerar os erros e as limitações dos modelos antes de interpretar classificações automáticas como evidência.

## Objetivo final

Construir um pipeline de Ciência de Dados capaz de processar milhões de mensagens de forma organizada, eficiente e reproduzível, permitindo explorar padrões de atividade e linguagem em comunidades do Discord. O projeto prioriza a integridade dos dados, a validação metodológica e a possibilidade de investigar tanto tendências gerais quanto interações relevantes em canais pequenos.

---

*Documento de acompanhamento do projeto. Objetivos, ferramentas e etapas podem ser atualizados conforme a auditoria e os experimentos avançarem.*
