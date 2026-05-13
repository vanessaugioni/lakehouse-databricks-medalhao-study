# Lakehouse com Databricks & Arquitetura Medalhão

Bem-vindo à documentação do **Trabalho 3** — implementação de um pipeline de dados completo utilizando **Databricks Free Edition**, **Delta Lake** e **Arquitetura Medalhão**.

---

## Sobre o Projeto

Este projeto é o terceiro trabalho de uma série que explora tecnologias modernas de engenharia de dados. Enquanto os trabalhos 1 e 2 utilizaram MinIO como armazenamento local, o trabalho 3 migra o pipeline para a nuvem com o **Databricks**.

O objetivo é demonstrar na prática como construir um **Lakehouse** completo, passando pelos estágios de ingestão, qualidade de dados e modelagem dimensional.

---

## Tecnologias Utilizadas

| Tecnologia | Papel no Projeto |
|---|---|
| **Supabase** | Banco de dados fonte (PostgreSQL gerenciado) |
| **Databricks Free Edition** | Plataforma de processamento e orquestração |
| **Delta Lake** | Formato de armazenamento em todas as camadas |
| **Apache Spark** | Engine de processamento distribuído |
| **Python / PySpark** | Linguagem dos notebooks |
| **MkDocs Material** | Documentação do projeto |

---

## Camadas da Arquitetura

=== "Landing"
    Dados extraídos diretamente do Supabase e salvos como tabelas Delta no schema `landing.dados`. Representa a zona de pouso dos dados brutos, sem qualquer transformação.

=== "Bronze"
    Leitura dos dados da camada Landing e regravação no schema `bronze` em formato Delta Lake. Os dados ainda são brutos, mas já versionados e rastreáveis.

=== "Silver"
    Aplicação de regras de **Data Quality**: remoção de nulos, validação de tipos, padronização de textos e enriquecimento com metadados de ingestão. Gravados no schema `silver`.

=== "Gold"
    Dados modelados seguindo a metodologia **Ralph Kimball**: tabelas dimensionais (`dim_`) e tabela fato (`fct_`). Prontos para consumo por ferramentas de BI e análise.

---

## Repositórios Relacionados

- [Trabalho 1](https://github.com/seu-usuario/spark-delta-minio-study) — Delta Lake + Iceberg com MinIO
- [Trabalho 2](https://github.com/seu-usuario/spark-delta-minio-study-2) — Extração CSV + DML Delta Lake
- [Trabalho 3](https://github.com/vanessaugioni/lakehouse-databricks-medalhao-study) — Esse repositório. 

