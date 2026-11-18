# KosDB

LevelDB-based database server with authentication, replication, and TLS.

## Installation

See [docs/INSTALL.md](docs/INSTALL.md) for detailed instructions.

## Documentation

- [Installation Guide](docs/INSTALL.md)
- [Configuration](docs/CONFIGURATION.md)
- [Operations](docs/OPERATIONS.md)
- [Quick Reference](docs/QUICK_REFERENCE.md)

## To start in debian/ubuntu first install -> apt install python3-plyvel
python3 server.py -h # For help...

python3 server.py --prepare-admin admin --prepare-password adminpassword

python3 server.py --port 3306


# now you can connect and login
...


## Cluster Proxy (experimental)

Run a front proxy that auto-starts and routes across multiple KosDB nodes.

```bash
# Uses config.cluster.json: master-slave with 1 primary + 1 replica by default
python3 cluster_proxy.py --config config.cluster.json

# Only proxy, backend nodes already running
python3 cluster_proxy.py --config config.cluster.json --no-manage

# Debug log file
python3 cluster_proxy.py --config config.cluster.json --log /tmp/proxy.log
```

Client connects to the proxy port (default 5556) exactly like a single KosDB server:

```text
nc localhost 5556
LOGIN admin admin
CREATE DATABASE mydb
USE mydb
CREATE TABLE users (id INTEGER PRIMARY KEY, email TEXT UNIQUE)
```

Proxy commands:
- `SHOW CLUSTER STATUS` – node health and auth state
- `HELP`

Per-node logs are written to `logs/cluster/node_<id>.log`.

## Export and Import

KosDB includes offline export/import utilities that operate directly on the LevelDB data files.

> **Important:** The server must be stopped while using `--export` or `--import` to avoid LevelDB lock conflicts.

### Export

```bash
# Export a whole database
python3 server.py --export <database>

# Export with custom output file
python3 server.py --export <database> --export-to backup.sql

# Export a single table
python3 server.py --export <database>.<table>
```

### Import

```bash
python3 server.py --import backup.sql
```

The dump format is plain SQL compatible with KosDB's command parser. `DROP TABLE IF EXISTS`, `CREATE TABLE`, `INSERT`, and `SET TABLE ... next_id` statements are supported. During import, existing tables are automatically recreated.

## License

MIT License
