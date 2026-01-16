---
title: "Designing a Configurable Azure Function API"
slug: "configurable-azure-function-api"
date: "2026-01-16"
tags: [azure, serverless, architecture]
summary: "How I designed a single Azure Function endpoint to serve multiple resources using environment variables instead of duplicating code."
status: published
---

## Context

When building my personal website, I wanted a small set of APIs to serve structured content such as my profile, CV, and later, technical articles.

The obvious approach would have been to create one Azure Function per resource (`GetProfile`, `GetCv`, `GetArticles`, etc.). That works, but it introduces duplication very early in a project that is intentionally small.

Instead, I chose to design **a configurable Azure Function** driven by environment variables.

This article explains why.

---

## The problem with one-function-per-resource

In small projects, multiple Azure Functions quickly lead to:

- duplicated Blob access logic  
- repeated error handling  
- inconsistent response shapes  
- more deployment surface than necessary  

None of these are *big* problems individually, but together they increase maintenance cost without adding real value.

The goal was not flexibility at any cost — it was **clarity and control**.

---

## The core idea: configuration over duplication

Rather than hardcoding which resource an API returns, I decided to externalize that decision.

Each Azure Function instance is configured with:

- the Blob container name
- the resource path or file
- optional behavior flags

All of that is controlled via **environment variables**, not code.

This allows the *same* function logic to serve different content.

---

## Environment-driven design

At a minimum, the function relies on variables like:

- `ARTICLES_CONTAINER`
- `RESOURCE_PATH`
- `RESOURCE_TYPE`

This means I can deploy the same function code and change behavior by configuration alone.

From an operational perspective, this has a few advantages:

- configuration changes do not require code changes
- behavior is visible directly in Azure
- local and cloud environments behave consistently

---

## Why Azure Blob Storage

Blob Storage is a good fit here because:

- content is static
- reads are cheap and fast
- access patterns are predictable
- it integrates cleanly with Azure Functions

For articles specifically, Blob becomes the **source of truth**, while the API acts as a controlled gateway.

This separation matters later when content grows.

---

## API responsibility boundaries

The Azure Function is intentionally limited to:

- reading content from Blob
- transforming it (Markdown → HTML)
- adding metadata
- returning a clean JSON response

It does **not**:

- manage content
- store state
- implement publishing logic
- act as a CMS

Those responsibilities stay outside the API.

---

## Why this approach scales *enough*

This setup is not meant to scale to millions of users.

It *is* meant to scale in terms of:

- clarity
- maintainability
- future extensions (like articles)

If the system ever outgrows this approach, replacing it will be trivial because the API contract is already clean.

Premature complexity would not buy me anything here.

---

## What I would change in a larger system

If this were a large
