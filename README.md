# migrain

A lightweight PHP CLI tool for fine‑grained MySQL schema migrations.

---

## 0. Concept

`migrain` (migration + fine grain) applies and rolls back SQL migrations in small, controlled steps.  
It tracks applied migrations in a JSON history file and executes each migration inside a transaction to ensure consistency.

---

## 1. Features

- **Stepwise execution**: apply or roll back N migrations at a time  
- **Transactional safety**: each `.up.sql` or `.down.sql` runs in its own transaction  
- **History tracking**: records applied migrations in a JSON file  
- **Status reporting**: shows pending vs. applied migrations  
- **Simple file conventions**: pairs of `.up.sql` and `.down.sql` named identically

---

## 2. Usage

1. **Install**  
   ```bash
   cp migrain.php /usr/local/bin/migrain
   chmod +x /usr/local/bin/migrain
````

2. **Prepare**

   * Create a directory for migrations, e.g. `db/migrations/`
   * Add files:

     ```
     20250101_create_users.up.sql
     20250101_create_users.down.sql
     20250215_add_index.up.sql
     20250215_add_index.down.sql
     ```

3. **Run**

   ```bash
   migrain \
     --path=./db/migrations/history.json \
     --name=mydb \
     --user=dbuser \
     [--host=127.0.0.1] \
     [--step=N]
   ```

   * `--step=0` (default): status
   * `--step>0`: apply next N pending migrations
   * `--step<0`: roll back last |N| applied migrations

4. **Password Prompt**
   You will be prompted for the MySQL password (input hidden).

---

## 3. Limitations

* **MySQL only**: uses PDO MySQL; other databases not supported
* **No interactive rollback**: runs in batch mode, no prompts per migration
* **Flat file history**: large numbers of migrations may slow JSON read/write
* **No dependency resolution**: migrations must be ordered by filename
* **Single-user focus**: concurrent runs may conflict on history file

