---
title: "dbt"
date: 2026-07-13
description: ""
tags: ["dbt"]
---

Distilled from resource at https://transformation-lab.datagym.io

## Concepts

* **Model** is a SELECT statement saved in .sql file. `dbt run` builds without test and turns it into a View and `dbt show --select <model_name>` shows sample rows on console.
* **ref()** gives dbt dependency graph of the models. `dbt build` (optionally with `--select`) builds the dependency graph into lineage and `dbt compile` to show SQL statement after resolving ref().
* Pipeline has **staging -> intermediate -> mart** connected via ref().
  * **staging** (stg_) cleans raw input, one per model
  * **intermediate** (int_) joins, filters, or aggregates
  * **marts** (dim_ or fct_) are polished output for business
* {{config(materialized='table')}} makes Model a Table instead of View which may take longer time to build.
  * When to use Table - frequently queried table than it's rebuilt, costly to build.
* **Sources** are upstream tables that dbt takes data from. They may sit in some warehouse; you didn't build them yourselves. dbt DAG has no knowledge on their freshness or to test them. They are handled in sources.yaml.
  * Operated on by source(<source_name>, <source_table>)

```yaml
# source.yaml
version: 2

sources:
  - name: raw    # the schema
    tables:
      - name: customers
      - name: orders
```

* **Seeds** are small, slow-changing CSVs that don't need to be in DB, ok to be in version control. Loaded into model. Used similarly with ref.
* **Schema + Testing** lives in schema.yaml. One entry per model can test their columns e.g. not_null, unique, relationships (all values in column X must exist in column Y), accepted_values.

```yaml
# schema.yaml
version: 2

models:
  - name: stg_customers
    description: "This is table"
    columns:
      - name: id
        description: "This is column"
        data_tests:
          - not_null
          - unique
  - name: stg_orders
    columns:
      - name: status
        data_tests:
          - accepted_values:
              arguments:
                values: ['paid', 'refunded', 'pending']
      - name: customer_id
        data_tests:
          - relationships:
              arguments:
                to: ref('stg_customers')
                field: id

```

* **singular tests**: tests/ folder, SQL statement to check that no_ (e.g. no_future_date -> should return no row)

Description can also be added. Recommended to be meaningful e.g. FK to which table, NULL when.

## Commands
 
* dbt show - preview
* dbt run - build without test
* dbt build - build with test (run + test but skip downstream failures if upstream fails somewhere)
* --select (short: -s) apply to specific model rather than the whole project
  * -s +<model_name> for upstream, <model_name>+ for downstream
* dbt compile - show compiled SQL command
* dbt seed - load small, slow-changing CSV like country code mapping into dbt
* dbt test
