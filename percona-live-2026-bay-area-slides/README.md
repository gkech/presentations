# Automated, Consistent, and Safe: Database Backups with Kubernetes Operators

Slides for the talk presented at **BuildDevCon 2026** by George Kechagias.

## Abstract

Kubernetes Operators bring application-aware backup orchestration to MySQL, MongoDB, and PostgreSQL — handling scheduling, consistency, PITR, and restore lifecycle through simple CRs. This talk covers how Percona Operators implement backups under the hood, what can go wrong in production, and how to build a production-grade backup strategy on Kubernetes.

## Slides

The presentation is written in [Marp](https://marp.app/) markdown. To build:

```bash
# Install Marp CLI
npm install -g @marp-team/marp-cli

# Build HTML
make html

# Build PDF
make pdf

# Preview with live reload
make preview
```

Or use the Makefile targets directly with `npx`:

```bash
npx @marp-team/marp-cli slides.md --html -o dist/slides.html
```

## Repo Structure

```
.
├── slides.md              # Marp presentation source
├── images/                # Images used in the slides
├── examples/
│   ├── mysql-pxc/
│   │   ├── backup.yaml              # PerconaXtraDBClusterBackup CR
│   │   ├── restore.yaml             # PerconaXtraDBClusterRestore CR
│   │   └── cluster-with-schedule.yaml  # Backup schedule excerpt
│   ├── postgresql/
│   │   ├── backup.yaml              # PerconaPGBackup CR
│   │   └── cluster-with-schedule.yaml  # pgBackRest schedule excerpt
│   └── mongodb/
│       ├── backup.yaml              # PerconaServerMongoDBBackup CR
│       └── restore.yaml             # PerconaServerMongoDBRestore CR
├── Makefile
└── README.md
```

## Resources

- [Percona Operator for MySQL (PXC)](https://docs.percona.com/percona-operator-for-mysql/pxc/)
- [Percona Operator for MongoDB](https://docs.percona.com/percona-operator-for-mongodb/)
- [Percona Operator for PostgreSQL](https://docs.percona.com/percona-operator-for-postgresql/v2/)
- [Percona XtraBackup](https://docs.percona.com/percona-xtrabackup/8.0/index.html)
- [Percona Backup for MongoDB](https://docs.percona.com/percona-backup-mongodb/index.html)
- [pgBackRest](https://pgbackrest.org/)
- [Percona Community Forum](https://forums.percona.com/)
- [Percona GitHub](https://github.com/percona)
