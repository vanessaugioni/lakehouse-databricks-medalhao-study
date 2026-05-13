# Como Executar

## Pré-requisitos

- Conta no [Databricks Free Edition](https://community.cloud.databricks.com)
- Banco de dados no [Supabase](https://supabase.com) com as tabelas criadas
- Git instalado na máquina local

---

## 1. Clonar o Repositório

```bash
git clone https://github.com/seu-usuario/spark-delta-databricks-study.git
cd spark-delta-databricks-study
```

---

## 2. Configurar o Supabase

No painel do Supabase, acesse **Settings → Database** e anote:

- **Host:** `db.xxxx.supabase.co`
- **Port:** `5432`
- **Database:** `postgres`
- **User:** `postgres`
- **Password:** sua senha

---

## 3. Importar os Notebooks no Databricks

1. Acesse o [Databricks Community Edition](https://community.cloud.databricks.com)
2. No menu lateral, clique em **Workspace**
3. Clique em **Import** (ícone ⬇️)
4. Importe os 4 arquivos da pasta `notebooks/` na seguinte ordem:
   - `01_landing.py`
   - `02_bronze.py`
   - `03_silver.py`
   - `04_gold.py`

---

## 4. Preencher as Credenciais

Abra o notebook `01_landing.py` e preencha as variáveis no início do arquivo:

```python
SUPABASE_HOST = "db.xxxx.supabase.co"   # ← seu host aqui
SUPABASE_USER = "postgres"
SUPABASE_PASS = "sua-senha"              # ← sua senha aqui
SUPABASE_DB   = "postgres"
SUPABASE_PORT = 5432
```

!!! warning "Segurança"
    Nunca commite credenciais no GitHub. Use **Databricks Secrets** em produção:
    ```python
    SUPABASE_PASS = dbutils.secrets.get(scope="supabase", key="password")
    ```

---

## 5. Criar o Cluster

1. No menu lateral, clique em **Compute**
2. Clique em **Create Cluster**
3. Configurações recomendadas:
   - **Cluster name:** `pipeline-medalhao`
   - **Databricks Runtime:** `13.3 LTS (Scala 2.12, Spark 3.4.1)`
   - **Node type:** Single node (Free Edition)
4. Clique em **Create Cluster** e aguarde inicializar

---

## 6. Criar o Job

1. No menu lateral, clique em **Workflows**
2. Clique em **Create Job**
3. Configure o nome: `pipeline_medalhao`
4. Adicione as tasks em sequência:

| Task | Notebook | Depende de |
|---|---|---|
| task_landing | `01_landing` | — |
| task_bronze | `02_bronze` | task_landing |
| task_silver | `03_silver` | task_bronze |
| task_gold | `04_gold` | task_silver |

5. Para cada task, selecione o cluster criado no passo anterior

---

## 7. Executar o Pipeline

1. Na tela do Job, clique em **Run Now**
2. Acompanhe a execução de cada task no painel
3. Ao finalizar, verifique os schemas criados em **Data → Schemas**:
   - `landing`
   - `bronze`
   - `silver`
   - `gold`

---

## 8. Verificar os Resultados

Crie um novo notebook e execute para confirmar as tabelas Gold:

```python
# Dimensões
display(spark.table("gold.dim_departamento"))
display(spark.table("gold.dim_funcionario"))
display(spark.table("gold.dim_projeto"))

# Fato
display(spark.table("gold.fct_alocacao"))
```

---

## Documentação Local (MkDocs)

```bash
pip install mkdocs mkdocs-material
mkdocs serve
```

Acesse em `http://localhost:8000`

## 📝 Referências

- 💻 [jlsilva01/spark-delta-minio-sqlserver](https://github.com/jlsilva01/spark-delta-minio-sqlserver)
- 📘 [Documentação Delta Lake](https://docs.delta.io/)
- 📘 [Documentação Databricks](https://docs.databricks.com/)
- 📘 [Documentação Supabase](https://supabase.com/docs)
- 📘 [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)
