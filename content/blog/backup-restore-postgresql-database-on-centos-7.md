+++
title = "Backup and Restore PostgreSQL Database on CentOS 7"
date = "2024-02-29"
description = "Backup and Restore PostgreSQL Database on CentOS 7"
tags = [
    "postgresql"
]
+++

Databases are crucial components of many systems and software. Thus, knowing how to backup and restore them becomes an essential skill. This article will guide you through the process of backing up and restoring a database in PostgreSQL on a CentOS 7 system.

## **Backup**

The first step is to backup the database. In PostgreSQL, you can use the `pg_dump` command to achieve this. Here's an example of using this command:

```bash
pg_dump -h 0.0.0.0 -p 5432 -U postgres -F c -b -v -f "/usr/local/backup/db.dump" db -W
```

| Command Option | Description |
| --- | --- |
| -h | Specifies the host |
| -p | Specifies the port |
| -U | Specifies the user |
| -F c | Creates a custom-format archive |
| -b | Includes large objects in the dump |
| -v | Enables verbose mode |
| -f | Specifies the filename for the output |
| db | Indicates the name of the database to be dumped |
| -W | prompts for password |

Before executing this command, make sure the backup directory exists by running

```bash
mkdir /usr/local/backup
```

## **Restore**

Once the backup is complete, the next step is to restore it. The `pg_restore` command is used for this purpose. Here's an example of how to use it:

```bash
pg_restore -d db -U postgres -C "usr/local/backup/db.dump" -W
```

| Command Option | Description |
| --- | --- |
| -d | Specifies the database to restore |
| -U | Specifies the user |
| -C | Specifies the path to the dump file |
| -W | prompts for password |

This command instructs PostgreSQL to restore the database `db` using the user `postgres` and the dump file `db.dump`.

## **Troubleshooting**

In case of encountering errors like `FATAL: Peer authentication failed for user "postgres"` or `FATAL: Indent authentication failed for user "postgres"`, the issue usually lies with the `pg_hba.conf` file.

To solve this, change `peer` or `indent` to `md5` in `pg_hba.conf`. Here's an example of how to do this:

```bash
# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

The `pg_hba.conf` file is usually located in `/var/lib/pgsql/<version>/data/pg_hba.conf`.

By following these steps, you can effectively backup and restore your PostgreSQL databases on a CentOS 7 system.
