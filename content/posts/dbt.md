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
 
* dbt show - preview
* dbt run - build without test
* dbt build - build with test
* --select apply to specific model
* dbt compile - show compiled SQL command
