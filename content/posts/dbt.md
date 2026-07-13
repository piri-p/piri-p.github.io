---
title: "DBT"
date: 2026-07-13
description: ""
tags: [""]
---

Distilled from resource at https://transformation-lab.datagym.io

## Concepts

* **Model** is a SELECT statement saved in .sql file. `dbt run` builds without test and turns it into a View and `dbt show --select <model_name>` shows sample rows on console.
* **ref()** gives dbt dependency graph of the models. `dbt build` (optionally with `--select`) builds the dependency graph into lineage and `dbt compile` to show SQL statement after resolving ref().
* Pipeline has **staging -> intermediate -> mart** connected via ref().
  * **staging** cleans raw input
  * **intermediate** joins, filters, or aggregates
  * **marts** are polished output for business
* {{config(materialized='table')}} makes Model a Table instead of View which may take longer time to build.
  * When to use Table - frequently queried table than it's rebuilt, costly to build.
* **Sources** are upstream tables that dbt takes data from. They may sit in some warehouse; you didn't build them yourselves. dbt DAG has no knowledge on their freshness or to test them. They are handled in sources.yaml.
  * Operated on by source(<source_name>, <source_table>)
* **Seeds** are small, slow-changing CSVs that don't need to be in DB, ok to be in version control. Loaded into model. Used similarly with ref.

```yaml
version: 2

sources:
  - name: raw    # the schema
    tables:
      - name: customers
      - name: orders
```

* **Schema + Testing** lives in schema.yaml. One entry per model can test their columns e.g. not_null, unique.

```yaml
version: 2

models:
  - name: stg_customers
    columns:
      - name: id
        data_tests:
          - not_null
          - unique
      - name: email
        data_tests:
          - not_null
```

## Commands
 
* dbt show - preview
* dbt run - build without test
* dbt build - build with test
* --select (short: -s) apply to specific model rather than the whole project
* dbt compile - show compiled SQL command
* dbt seed - load small, slow-changing CSV like country code mapping into dbt
