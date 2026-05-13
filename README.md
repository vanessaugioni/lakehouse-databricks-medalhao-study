# Projeto: Lakehouse com Databricks e Arquitetura Medalhão

> Pipeline de dados com *Databricks Free Edition*, *Delta Lake*, *Supabase* e *Arquitetura Medalhão* | Arquitetura de Dados | SATC

[![Python](https://img.shields.io/badge/python-3.11-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Databricks](https://img.shields.io/badge/databricks-free_edition-FF3621?logo=databricks&logoColor=white)](https://community.cloud.databricks.com/)
[![Apache Spark](https://img.shields.io/badge/spark-3.4.1-E25A1C?logo=apachespark&logoColor=white)](https://spark.apache.org/)
[![Delta Lake](https://img.shields.io/badge/delta_lake-3.2.0-003366?logoColor=white)](https://delta.io/)
[![Supabase](https://img.shields.io/badge/supabase-postgresql-3ECF8E?logo=supabase&logoColor=white)](https://supabase.com/)
[![MkDocs](https://img.shields.io/badge/docs-mkdocs-526CFE?logo=materialformkdocs&logoColor=white)](https://vanessaugioni.github.io/spark-delta-databricks-study)


## Participantes

| Nome | GitHub |
|---|---|
| Vanessa Ugioni | [@vanessaugioni](https://github.com/vanessaugioni) |
| Gabriel Muller | [@GabrielNM12](https://github.com/GabrielNM12) |
| Bettina da Silva | [@berbett](https://github.com/berbett) |


## Documentação
As explicações sobre as tecnologias utilizadas estão disponíveis no MkDocs do projeto:

🔗 https://vanessaugioni.github.io/lakehouse-databricks-medalhao-study


## Estrutura do Projeto

```
spark-delta-databricks-study/
├── docs/                          # Fontes do MkDocs
├── notebooks/
│   ├── 01_landing.py              # Extração do Supabase → LANDING.DADOS
│   ├── 02_bronze.py               # LANDING → BRONZE (Delta Lake)
│   ├── 03_silver.py               # BRONZE → SILVER (Data Quality)
│   └── 04_gold.py                 # SILVER → GOLD (Modelo Dimensional)
├── mkdocs.yml
└── README.md
```

## Sobre o Projeto

Este repositório implementa um pipeline de dados completo no **Databricks Free Edition**, extraindo dados de um banco **Supabase (PostgreSQL)** e processando-os em camadas progressivas seguindo a **Arquitetura Medalhão**:

- **Landing** — dados brutos extraídos via JDBC e gravados como Delta Lake
- **Bronze** — consolidação dos dados em Delta Lake com versionamento
- **Silver** — aplicação de Data Quality (nulos, validações, padronização)
- **Gold** — modelo dimensional Ralph Kimball (dimensões + tabela fato)

Todo o pipeline é orquestrado por um **Job no Databricks**, executando os notebooks sequencialmente.

## Pré-requisitos

| Ferramenta | Versão | Link |
|---|---|---|
| Databricks Free Edition | **latest** | [community.cloud.databricks.com](https://community.cloud.databricks.com/) |
| Supabase | **latest** | [supabase.com](https://supabase.com/) |

> ⚠️ Este projeto roda inteiramente na nuvem. Não é necessário instalar Spark ou Java localmente.

## Configuração

### 1. Clonar o repositório

```bash
git clone https://github.com/vanessaugioni/lakehouse-databricks-medalhao-study.git
cd lakehouse-databricks-medalhao-study
```

### 2. Importar os notebooks no Databricks

1. Acesse o [Databricks Community Edition](https://community.cloud.databricks.com)
2. No menu lateral, clique em **Workspace → Import**
3. Importe os 4 arquivos da pasta `notebooks/` na ordem indicada

### 3. Configurar as credenciais do Supabase

No notebook `00_landing.py`, preencha as variáveis:

```python
SUPABASE_HOST = "db.xxxx.supabase.co"
SUPABASE_USER = "postgres"
SUPABASE_PASS = "sua-senha"
SUPABASE_DB   = "postgres"
SUPABASE_PORT = 5432
```

> ⚠️ Nunca commite credenciais no GitHub. Em produção, utilize **Databricks Secrets**.

### 4. Criar o cluster

1. No menu lateral, clique em **Compute → Create Cluster**
2. Configurações recomendadas:
   - **Runtime:** `13.3 LTS (Scala 2.12, Spark 3.4.1)`
   - **Mode:** Single Node

### 5. Criar e executar o Job

1. Acesse **Workflows → Create Job**
2. Adicione as 4 tasks em sequência com dependência entre elas:

| Task | Notebook | Depende de |
|---|---|---|
| task_landing | `00_landing` | — |
| task_bronze | `01_bronze` | task_landing |
| task_silver | `02_silver` | task_bronze |
| task_gold | `03_gold` | task_silver |

3. Clique em **Run Now** para executar o pipeline completo

## Notebooks

| Notebook | Descrição |
|---|---|
| `01_landing.py` | Extrai todas as tabelas do Supabase via JDBC e grava em `landing.dados` |
| `02_bronze.py` | Lê os dados de `landing.dados` e regrava em `bronze` como Delta Lake |
| `03_silver.py` | Aplica Data Quality no `bronze` e grava no schema `silver` |
| `04_gold.py` | Constrói o modelo dimensional Kimball no schema `gold` |

## Modelo Dimensional (Gold)

```
gold.dim_departamento
gold.dim_funcionario
gold.dim_projeto
gold.fct_alocacao  ← tabela fato
```

## Repositórios Relacionados

| Trabalho | Repositório |
|---|---|
| Trabalho 1 | [spark-delta-minio-study](https://github.com/vanessaugioni/spark-delta-minio-study) |
| Trabalho 2 | [spark-delta-minio-study-2](https://github.com/vanessaugioni/spark-delta-minio-study-2) |
| **Trabalho 3** | Este repositório |

## 📝 Referências

- 💻 [jlsilva01/spark-delta-minio-sqlserver](https://github.com/jlsilva01/spark-delta-minio-sqlserver)
- 📘 [Documentação Delta Lake](https://docs.delta.io/)
- 📘 [Documentação Databricks](https://docs.databricks.com/)
- 📘 [Documentação Supabase](https://supabase.com/docs)
- 📘 [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)


Desenvolvido para a disciplina de Arquitetura de Dados — SATC
