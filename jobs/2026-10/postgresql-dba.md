# PostgreSQL Database Administrator — Khalti

**Company:** [Khalti](https://khalti.com)  
**Posted:** October 06, 2026  
**Location:** Kathmandu  
**Experience Required:** 3+ years  
**Education:** Bachelor's in Computer Science, Information Technology, Computer Engineering, or related field  
**Source:** [LinkedIn posting](https://lnkd.in/p/gRTxqR-c)

---

## Job Description

### Cluster Administration & HA

- Install, configure, and administer PostgreSQL clusters across production and non-production environments
- Configure and troubleshoot streaming replication (sync/async), replication slots, WAL archiving, standby servers, failover and switchover
- Build and maintain HA infrastructure using distributed consensus, and HAProxy/Keepalived (or equivalent) for traffic management
- Design and troubleshoot failover paths — not just operate an existing cluster, but reason about how it fails and recovers

### Connection Management

- Configure and manage PgBouncer: pool modes, connection limits, saturation behavior, and connection-related incident troubleshooting

### Upgrades & Migrations

- Plan and execute PostgreSQL major-version upgrades and migrations off legacy environments, including compatibility assessment, rollback strategy, downtime planning, and post-upgrade validation

### Backup & Recovery

- Implement and troubleshoot physical and logical backup/recovery, WAL archiving, and Point-in-Time Recovery (PITR)

### Performance & Troubleshooting

- Diagnose locks, blocking, deadlocks, long-running transactions, connection exhaustion, replication lag, WAL accumulation, checkpoint pressure, autovacuum stalls, table/index bloat, and disk/I/O constraints
- Read EXPLAIN/EXPLAIN ANALYZE plans and drive query, indexing, partitioning, and configuration tuning
- Tune core parameters (buffers, mems, WAL/checkpoint settings, connection limits, autovacuum) against actual workload characteristics
- Determine whether a given performance problem originates in SQL, the planner, PostgreSQL config, locking, replication, OS resources, or storage I/O

### Monitoring & Automation

- Install and maintain PMM, Grafana, Prometheus (or equivalent) for database observability
- Build alerting for replication, WAL, connections, locks, transactions, checkpoints, autovacuum, database growth, CPU, memory, disk, and I/O
- Automate DBA operations with Bash/Shell, Python, Ansible (or equivalent)

### SQL & Documentation

- Write, review, and optimize complex SQL — CTEs, window functions, joins, subqueries, aggregations, data manipulation
- Maintain configuration, architecture, backup/recovery, and operational documentation
- Perform root-cause analysis on production incidents and drive prevention, not just remediation

---

## Tech Stack

| Category | Tools / Skills |
|----------|----------------|
| Database | PostgreSQL |
| Connection Pooling | PgBouncer |
| HA & Failover | HAProxy, Keepalived, distributed consensus (etcd / Patroni) |
| Backup & Recovery | WAL archiving, PITR, pg_basebackup, pgBackRest |
| Monitoring | PMM (Percona), Prometheus, Grafana |
| Automation | Bash/Shell, Python, Ansible |
| SQL | CTEs, window functions, joins, subqueries, EXPLAIN/EXPLAIN ANALYZE |

---

## Learning Resources

| Topic | Resource |
|-------|----------|
| PostgreSQL internals | [postgresql.org/docs](https://www.postgresql.org/docs/) |
| Streaming replication | [PostgreSQL replication docs](https://www.postgresql.org/docs/current/warm-standby.html) |
| WAL & PITR | [PostgreSQL WAL docs](https://www.postgresql.org/docs/current/wal-intro.html) |
| PgBouncer | [pgbouncer.org](https://www.pgbouncer.org/config.html) |
| Patroni (HA) | [patroni.readthedocs.io](https://patroni.readthedocs.io/) |
| HAProxy | [haproxy.org](https://www.haproxy.org/#docs) |
| PMM | [docs.percona.com/pmm](https://docs.percona.com/percona-monitoring-and-management/) |
| Prometheus | [prometheus.io/docs](https://prometheus.io/docs/introduction/overview/) |
| Grafana | [grafana.com/tutorials](https://grafana.com/tutorials/) |
| Ansible | [docs.ansible.com](https://docs.ansible.com/) |
| SQL & indexes | [use-the-index-luke.com](https://use-the-index-luke.com/) |
| SQL practice | [pgexercises.com](https://pgexercises.com/) |
| DBA real-world writing | [depesz.com](https://www.depesz.com/), [cybertec-postgresql.com/en/blog](https://www.cybertec-postgresql.com/en/blog/) |

---

## Breakdown — What Each Thing Actually Is

> This section explains every technology in the job description so you know what to learn and where to start.

---

### 1. PostgreSQL — Core

The whole job revolves around this. Not just writing queries — they want someone who understands how PostgreSQL works as a system internally.

| Topic | What it means |
|-------|---------------|
| Streaming replication (sync/async) | How Postgres copies data to standby servers in real-time. Sync = primary waits for standby to confirm. Async = primary doesn't wait — faster but risk of data loss on crash |
| Replication slots | Ensures WAL data isn't deleted before a replica has consumed it. Useful but dangerous if a replica falls behind — can cause disk to fill up |
| WAL archiving | Saving Write-Ahead Logs to external storage (S3, NFS) — needed for PITR and disaster recovery |
| Standby servers | Read replicas / hot standbys. Traffic can be routed to them for reads, or promoted on primary failure |
| Failover & switchover | Failover = unplanned (primary crashed). Switchover = planned (maintenance). Both involve promoting a standby to primary |
| PITR | Point-in-Time Recovery — restore the DB to exactly the state it was in at any past moment using WAL archives |
| EXPLAIN / EXPLAIN ANALYZE | EXPLAIN shows the query plan. EXPLAIN ANALYZE actually runs it and shows real timing. Used to find why a query is slow |
| Autovacuum | Background process that reclaims space from deleted/updated rows. If it falls behind, table bloat and performance degrade badly |
| Table/index bloat | Dead tuples that weren't cleaned up — wastes disk space and slows scans |
| Partitioning | Splitting one large table into smaller physical parts (by date, range, list) — improves query performance and manageability |

**Where to learn:**
- [Official PostgreSQL docs](https://www.postgresql.org/docs/) — actually very readable, not just reference material
- [depesz.com](https://www.depesz.com/) — Hubert Lubaczewski, one of the best Postgres writers out there
- [Cybertec PostgreSQL blog](https://www.cybertec-postgresql.com/en/blog/) — real production-level DBA content
- *PostgreSQL: Up and Running* (O'Reilly book) — solid starting point

---

### 2. PgBouncer — Connection Pooling

PostgreSQL creates a new process for every connection. At scale, this is expensive. PgBouncer sits in front of Postgres and manages a shared pool of connections so the DB doesn't get overwhelmed.

**Pool modes — the most important thing to understand:**
- `session` — one server connection held for the entire client session. Safest but least efficient
- `transaction` — server connection assigned only for the duration of a transaction. Most commonly used
- `statement` — connection released after every single statement. Very aggressive, incompatible with multi-statement transactions

**What breaks when you misconfigure this:**
- Too many clients → connection exhaustion → Postgres starts rejecting connections
- Wrong pool mode → prepared statements break, session-level settings don't work as expected

**Key config params:** `max_client_conn`, `default_pool_size`, `server_idle_timeout`, `pool_mode`

**Where to learn:**
- [PgBouncer docs](https://www.pgbouncer.org/config.html)
- [Percona PgBouncer guide](https://www.percona.com/blog/pgbouncer-overview-use-cases-and-configuration/)

---

### 3. High Availability — Patroni + etcd + HAProxy + Keepalived

When the primary Postgres server dies, something needs to automatically detect this, promote a standby, and redirect traffic. This is what the HA stack does.

- **Patroni** — runs on each Postgres node. Manages leader election, automatic failover, and health checks. Uses a consensus store to decide who's primary
- **etcd (or Consul)** — the consensus store. All Patroni nodes write to it and read from it to agree on who's the current leader. Prevents split-brain (two nodes thinking they're primary at the same time)
- **HAProxy** — sits in front of all Postgres nodes. Checks which one is currently the primary (via Patroni's REST API) and routes write traffic there. Read traffic can go to standbys
- **Keepalived** — manages a floating virtual IP (VIP). Your app connects to one fixed IP address and Keepalived makes sure that IP points to the active HAProxy node

**The full flow on failure:**
1. Primary crashes
2. Patroni on standbys detects this via etcd (primary stopped writing)
3. Patroni elects a new primary (the most up-to-date standby)
4. HAProxy health check detects the new primary within seconds
5. Traffic is automatically routed to the new primary
6. Application sees zero config change — it's still connecting to the same VIP

**Where to learn:**
- [Patroni docs](https://patroni.readthedocs.io/)
- [HAProxy docs](https://www.haproxy.org/#docs)
- Search: "Patroni etcd HAProxy setup" on Percona and Cybertec blogs — step-by-step guides exist

---

### 4. Backup & Recovery — pgBackRest / pg_basebackup + WAL archiving + PITR

Backups are not optional and not simple at the DBA level.

- **pg_basebackup** — built-in tool to take a physical base backup of the entire cluster
- **pgBackRest** — more advanced backup tool with compression, encryption, parallel restore, retention management, and built-in WAL archiving
- **WAL archiving** — continuously archive WAL segments to external storage. Required for PITR
- **PITR** — combine a base backup + WAL archives to restore the database to any point in time (e.g., 3 minutes before someone ran `DROP TABLE`)

Physical backups are fast to restore. Logical backups (`pg_dump`) are portable but slow. A real production setup uses both.

**Where to learn:**
- [pgBackRest docs](https://pgbackrest.org/user-guide.html)
- [PostgreSQL backup docs](https://www.postgresql.org/docs/current/backup.html)

---

### 5. Monitoring — PMM, Prometheus, Grafana

You need visibility into what the database is doing at all times.

- **PMM (Percona Monitoring and Management)** — purpose-built for Postgres/MySQL. Bundles Prometheus + Grafana + query analytics in one tool. Shows slow queries, wait events, replication lag, etc
- **Prometheus** — scrapes metrics from exporters (like `postgres_exporter`) and stores them as time-series data
- **Grafana** — dashboards on top of Prometheus. Visualize anything: connection counts, WAL size, bloat, checkpoint frequency

**What to alert on (per this JD):**
replication lag, WAL accumulation, active connections, lock waits, checkpoint pressure, autovacuum stalls, table/index growth, CPU, memory, disk, I/O

**Where to learn:**
- [PMM docs](https://docs.percona.com/percona-monitoring-and-management/)
- [Prometheus getting started](https://prometheus.io/docs/introduction/overview/)
- [Grafana tutorials](https://grafana.com/tutorials/)
- [postgres_exporter](https://github.com/prometheus-community/postgres_exporter) — the bridge between Postgres and Prometheus

---

### 6. Automation — Bash/Shell, Python, Ansible

DBA work has a lot of repetition. You automate it so you're not doing it manually every time.

- **Bash/Shell** — backup scripts, cron jobs, log parsing, health check scripts, quick DB maintenance tasks
- **Python** — heavier scripts: monitoring integrations, custom alerting, migration tooling, incident automation. Use `psycopg2` or `psycopg3` to interact with Postgres
- **Ansible** — deploy Postgres config changes across multiple nodes, run rolling upgrades, manage cluster state without logging in to each server manually

**Where to learn:**
- [Bash scripting guide](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Ansible getting started](https://docs.ansible.com/ansible/latest/getting_started/)
- [psycopg3 docs](https://www.psycopg.org/psycopg3/docs/) — Python + Postgres

---

### 7. SQL — Advanced

They want someone who can read, write, and optimize complex SQL — not just basic CRUD.

| Topic | What it is |
|-------|------------|
| CTEs | `WITH` clauses — break complex queries into named steps for readability |
| Window functions | `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()`, `SUM() OVER(...)` — compute across rows without collapsing them |
| Complex joins | Outer joins, self-joins, lateral joins, anti-joins |
| Subqueries | Correlated (references outer query) vs non-correlated. Know when each is appropriate |
| EXPLAIN ANALYZE | Reading cost estimates, actual rows vs estimated rows, sequential vs index scans, hash joins vs nested loops |
| Index design | B-tree (default), partial indexes, expression indexes, covering indexes. Knowing which one fits which query |
| Partitioning | Range, list, hash — and what queries benefit or suffer from each |

**Where to learn:**
- [use-the-index-luke.com](https://use-the-index-luke.com/) — best free resource on indexes, period
- [Mode SQL tutorial](https://mode.com/sql-tutorial/sql-window-functions/) — good for window functions
- [pgexercises.com](https://pgexercises.com/) — practice SQL directly in the browser
- [explain.depesz.com](https://explain.depesz.com/) — paste any EXPLAIN output and get a visual breakdown

---

*Added by: [@virtualabishek](https://github.com/virtualabishek)*
