# Notebooks

O pipeline é composto por 4 notebooks Python executados no Databricks, cada um responsável por uma camada da Arquitetura Medalhão.

---

## 01_landing.py — Extração para Landing

**Responsabilidade:** Conectar ao Supabase via JDBC e extrair todas as tabelas, gravando-as como Delta Lake no schema `landing.dados`.

**Principais operações:**
- Criação do schema `landing` se não existir
- Conexão JDBC com o PostgreSQL do Supabase
- Leitura de cada tabela com `spark.read.jdbc`
- Gravação em `landing.dados.<tabela>` no formato Delta com `mode("overwrite")`

**Tabelas geradas:**
```
landing.dados.departamentos
landing.dados.funcionarios
landing.dados.projetos
landing.dados.alocacoes
```

---

## 02_bronze.py — Landing → Bronze

**Responsabilidade:** Ler os dados da camada Landing e regravá-los no schema `bronze` como Delta Lake.

**Principais operações:**
- Criação do schema `bronze` se não existir
- Leitura das tabelas Delta de `landing.dados`
- Gravação em `bronze.<tabela>` com `mode("overwrite")`
- Preview dos dados gravados

**Tabelas geradas:**
```
bronze.departamentos
bronze.funcionarios
bronze.projetos
bronze.alocacoes
```

---

## 03_silver.py — Data Quality (Bronze → Silver)

**Responsabilidade:** Aplicar regras de qualidade de dados nas tabelas Bronze e gravar no schema `silver`.

**Regras de Data Quality aplicadas:**

| Regra | Tabela | Descrição |
|---|---|---|
| Nulos obrigatórios | Todas | Remove linhas com `id` ou `nome` nulos |
| Salário positivo | funcionarios | Filtra `salario > 0` |
| Horas positivas | alocacoes | Filtra `horas_semanais > 0` |
| Trim de texto | Todas | Remove espaços extras em campos string |
| Metadados | Todas | Adiciona `dt_ingestao` e `origem` |

**Tabelas geradas:**
```
silver.departamentos
silver.funcionarios
silver.projetos
silver.alocacoes
```

---

## 04_gold.py — Modelo Dimensional (Silver → Gold)

**Responsabilidade:** Construir o modelo dimensional seguindo a metodologia Ralph Kimball a partir dos dados Silver.

**Principais operações:**
- Criação do schema `gold` se não existir
- Geração de chaves surrogate (`sk_`) com `monotonically_increasing_id()`
- Criação das dimensões com atributos descritivos
- Criação da tabela fato com métricas e chaves estrangeiras

**Tabelas geradas:**
```
gold.dim_departamento
gold.dim_funcionario
gold.dim_projeto
gold.fct_alocacao
```

## 📝 Referências

- 💻 [jlsilva01/spark-delta-minio-sqlserver](https://github.com/jlsilva01/spark-delta-minio-sqlserver)
- 📘 [Documentação Delta Lake](https://docs.delta.io/)
- 📘 [Documentação Databricks](https://docs.databricks.com/)
- 📘 [Documentação Supabase](https://supabase.com/docs)
- 📘 [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)
