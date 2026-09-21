# Aviação ANAC — VoeBem Analytics

Materiais de estudo da turma da Imersão Engenharia de Dados — setembro/2026, com dados da ANAC, Python, SQL e Databricks.

A VoeBem Analytics é a consultoria fictícia usada no projeto: o objetivo é investigar atrasos, cancelamentos e pontualidade dos voos. Este repositório é uma iniciativa colaborativa da turma, sem vínculo oficial com a Alura.

## Primeira vez por aqui?

Siga o **[Comece aqui — guia para iniciantes](docs/comece-aqui.md)**: do download dos materiais à primeira consulta. O percurso é pelo navegador, com os dados incluídos e sem instalar Python ou Git no computador.

**Estado desta versão do fork:** execução completa validada em um workspace Databricks pessoal em 21/09/2026, incluindo as camadas Bronze, Silver e Gold, pipeline de qualidade e testes do Genie Agent. Os resultados e as ressalvas encontradas durante a validação estão documentados em [`docs/validacao-pessoal.md`](docs/validacao-pessoal.md).

## Minha execução e refinamentos

Nesta versão do fork, executei o pipeline completo no Databricks e validei os resultados utilizando as consultas SQL de referência do projeto.

Além da execução do pipeline, realizei testes manuais do Genie Agent e refinei seus metadados e instruções após identificar ambiguidades de interpretação, especialmente relacionadas a causalidade, rankings e métricas de atraso.

Os testes realizados e as ressalvas encontradas estão documentados em [`docs/validacao-pessoal.md`](docs/validacao-pessoal.md).

## Resultados observados

- A tabela Gold `obt_voos` foi materializada com 1.014.664 registros de etapas de voo no período analisado.
- Voos internacionais apresentaram atraso médio de partida de 19,36 minutos, contra 5,13 minutos nos voos domésticos.
- O percentual de voos atrasados foi de 25,37% nos voos internacionais e 15,86% nos domésticos.
- A validação do Genie Agent identificou ambiguidades entre frequência e severidade de atraso e também usos indevidos de linguagem causal, levando ao refinamento dos metadados e das instruções do agente.

## Tecnologias e conceitos praticados

- Databricks e Spark
- Python e PySpark
- SQL
- Delta Lake
- Arquitetura Medallion: Bronze, Silver e Gold
- Pipelines de qualidade e quarentena de dados
- Modelagem analítica e OBT
- Governança e documentação de metadados
- Genie Agent e consultas em linguagem natural
- Validação de respostas de IA contra consultas SQL de referência

## Estrutura

- `dados/`: 15 CSVs da ANAC incluídos no material original: 12 meses de VRA (agosto/2025 a julho/2026) e três cadastros de referência.
- `notebooks/`: ingestão Bronze, transformação Silver e governança Gold. Os arquivos `.py` estão no formato de notebooks Databricks.
- `pipelines/qualidade/`: regras de qualidade e quarentena para diagnóstico.
- `sql/gold/`: dimensão de aeroportos, fato de voos e tabela de consumo (OBT).
- `sql/gabarito/`: consultas para as perguntas de negócio.
- `scripts/`: download dos dados e utilitários opcionais de execução e Genie.
- `genie/`: instruções e configuração de exemplo para perguntas em linguagem natural.
- `docs/`: fontes, perguntas de negócio e registro de aceitação fornecido no material original.

## Preparação

A instalação local é necessária somente se você optar pelos scripts de terminal. Use Python 3.10 ou superior para esses scripts. Eles utilizam a biblioteca padrão. Os notebooks dependem do ambiente Spark/Databricks e não devem ser executados como scripts Python locais.

1. Baixe ou clone este repositório e abra um terminal na pasta do projeto.
2. Os CSVs já estão incluídos em `dados/`, portanto não é necessário baixá-los novamente. Para recuperar arquivos ausentes, execute `python scripts/baixar_anac.py`. O script usa a janela agosto/2025 a julho/2026, reaproveita arquivos existentes e atualiza `docs/fontes.md` com o registro do download. A disponibilidade de novos downloads depende do portal de origem.
3. Em seu ambiente de estudos Databricks, execute `sql/00_preparar_ambiente.sql`. É necessário ter permissão para criar catálogo, schemas e volume.
4. Envie o conteúdo de `dados/vra/` para `/Volumes/voebem/bronze/arquivos/vra/` e o conteúdo de `dados/referencias/` para `/Volumes/voebem/bronze/arquivos/referencias/`.
5. Importe os arquivos de `notebooks/` como notebooks Databricks.

O código usa o catálogo `voebem`. Se escolher outro nome, ajuste as referências nos notebooks, SQL e configuração do Genie. As cargas usam sobrescrita ou `CREATE OR REPLACE`: execute no ambiente destinado a este projeto.

## Sequência de estudo e execução

1. `notebooks/03_bronze_vra.py`
2. `notebooks/04_bronze_referencias.py`
3. `notebooks/05_silver_espelho.py`
4. Configure um pipeline de qualidade com os três arquivos de `pipelines/qualidade/`, catálogo `voebem` e schema `silver`, e execute-o no Databricks.
   Para usar `sql/metricas_qualidade.sql`, configure também a publicação do event log do pipeline na tabela `voebem.silver.eventos_qualidade`.
5. Execute `sql/gold/01_dim_aeroporto.sql`, `02_fato_voos.sql` e `03_obt_voos.sql`, nessa ordem.
6. Execute `notebooks/09_governanca_gold.py`.
7. Explore as consultas em `sql/gabarito/` e as perguntas em `docs/perguntas-de-negocio.md`.
8. Opcional: configure um espaço Genie com `voebem.gold.obt_voos`, usando os exemplos e instruções de `genie/`. O script `python scripts/montar_genie_space.py` regenera o JSON; ele não cria o espaço no serviço.

A numeração original foi preservada; os números ausentes não representam arquivos faltando neste pacote.

## Utilitários opcionais

Os scripts que acessam o Databricks exigem a CLI instalada e autenticada no seu próprio workspace. O perfil padrão é `alura-imersao`, substituível pela variável `DATABRICKS_CONFIG_PROFILE`.

Para consultar seu espaço Genie no PowerShell:

```powershell
$env:DATABRICKS_CONFIG_PROFILE = "alura-imersao"
$env:GENIE_SPACE_ID = "ID_DO_SEU_ESPACO"
python scripts/perguntar_genie.py "Quais aeroportos concentram os maiores atrasos?"
```

Os scripts `.sh` exigem Bash, como Git Bash ou WSL. Para `rodar_notebook.sh`, defina também `DATABRICKS_WORKSPACE_PATH` com a pasta dos notebooks no workspace. O script `rodar_pipeline.sh` recebe o ID do seu pipeline como primeiro argumento. As variáveis devem ser definidas no terminal em que o script será executado.

## Validação e contribuições

Compare as respostas do Genie com as consultas SQL. Os números e resultados em `docs/teste-aceitacao.md` são registros do material original; não são uma validação de uma execução no seu ambiente e podem variar com a atualização dos dados.

Para contribuir, descreva a alteração e como verificou o resultado em um pull request. Não inclua credenciais ou configurações pessoais. Ao alterar os CSVs, informe a fonte, o período e o motivo da atualização para preservar a reprodução dos exercícios.

## Créditos

Contexto educacional: Imersão Engenharia de Dados da Alura, setembro/2026. Fonte dos dados: ANAC, conforme `docs/fontes.md`. O código-base foi preservado a partir do material compartilhado da imersão, com ajustes de preparação para publicação. 
