# Merge Multiple DDL Files into a Single Schema

This guide explains how to merge multiple DDL (Data Definition Language) files into a single schema file and apply it to a PostgreSQL database. This is particularly useful when you have a large number of `.ddl` files (e.g., 199 files) and want to consolidate them for easier management and deployment.

## Table of Contents
1. [What is the Task?](#what-is-the-task)
2. [What is the Problem?](#what-is-the-problem)
3. [Why Are We Doing This?](#why-are-we-doing-this)
4. [Step-by-Step Process](#step-by-step-process)
   - [Step 1: Install and Set Up PostgreSQL](#step-1-install-and-set-up-postgresql)
   - [Step 2: Create a Test Database](#step-2-create-a-test-database)
   - [Step 3: Create Sample DDL Files](#step-3-create-sample-ddl-files)
   - [Step 4: Merge the DDL Files](#step-4-merge-the-ddl-files)
   - [Step 5: Apply the Merged Schema](#step-5-apply-the-merged-schema)
   - [Step 6: Automate the Process](#step-6-automate-the-process)
5. [Final Notes](#final-notes)

## What is the Task?
The task is to merge multiple `.ddl` files into a single schema file and apply it to a PostgreSQL database. This simplifies schema management and ensures proper dependency handling.

## What is the Problem?
- **Manual Merging is Tedious**: Combining hundreds of `.ddl` files manually is time-consuming and error-prone.
- **Dependency Issues**: Tables must be created before their dependents (e.g., users before orders), and foreign keys must be added after all tables are created.
- **Scalability**: As the number of `.ddl` files grows, manual processes become impractical.

## Why Are We Doing This?
- **Simplify Schema Management**: A single merged file is easier to manage, version control, and deploy.
- **Automate Repetitive Tasks**: Automation reduces human error and saves time.
- **Ensure Correct Dependency Order**: Proper ordering of `CREATE TABLE` and `ALTER TABLE` statements ensures the schema is applied correctly.

## Step-by-Step Process

### Step 1: Install and Set Up PostgreSQL
Install PostgreSQL:
```bash
sudo yum install postgresql15-server
```
Initialize the database:
```bash
sudo /usr/pgsql-15/bin/postgresql-15-setup initdb
```
Start and enable PostgreSQL:
```bash
sudo systemctl start postgresql-15
sudo systemctl enable postgresql-15
```

### Step 2: Create a Test Database
Switch to the postgres user:
```bash
sudo -i -u postgres
```
Create the database:
```bash
psql -c "CREATE DATABASE test_db;"
```

### Step 3: Create Sample DDL Files
Create the following `.ddl` files in a directory (e.g., `/var/lib/pgsql/ddls`):
- `users.ddl`
- `products.ddl`
- `categories.ddl`
- `orders.ddl`
- `order_items.ddl`
- `product_categories.ddl`

### Step 4: Merge the DDL Files
Navigate to the DDL directory:
```bash
cd /var/lib/pgsql/ddls
```
Merge the files:
```bash
cat users.ddl products.ddl categories.ddl orders.ddl order_items.ddl product_categories.ddl > merged_schema.sql
```
Manually add foreign key constraints to `merged_schema.sql` if needed.

### Step 5: Apply the Merged Schema
Apply the schema:
```bash
psql -d test_db -f /var/lib/pgsql/ddls/merged_schema.sql
```
Verify the schema:
```bash
psql -d test_db -c "\dt"
psql -d test_db -c "\d orders"
```


## Final Notes
- **Dependency Handling**: The script ensures `CREATE TABLE` statements are processed before `ALTER TABLE` statements.
- **Scalability**: This approach works for any number of `.ddl` files.
- **Error Handling**: If errors occur, check the merged file for duplicates or invalid syntax.

