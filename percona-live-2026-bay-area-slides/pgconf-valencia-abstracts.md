# PGConf Valencia 2026: Talk Submissions

Three submissions, three different abstraction heights. Vendor-neutral framing, grounded in features that real-world PostgreSQL operators on Kubernetes actually ship.

---

## 1. The DBaaS Gap: What PostgreSQL Operators Don't Give You and How to Build It

**Format:** 40 min talk + Q&A
**Level:** Intermediate
**Audience:** Platform engineers, SREs, PG DBAs building internal database platforms

### Abstract

A PostgreSQL operator deploys clusters, runs failover, schedules backups, and ships PITR. But a managed database-as-a-service is more than that. It is self-service, multi-tenant, observable, cost-attributed, and continuously verified. None of that comes in the operator box, regardless of which one you pick.

This talk maps the honest gap between *operator* and *DBaaS*. Today's PG operators on Kubernetes share a common shape: HA, backups, PITR, TLS, monitoring exporters. What they all leave to you is everything around it. We will name the gaps one by one: a self-service portal that hides YAML from developers, tenant isolation via namespaces and NetworkPolicies, credential vending via External Secrets or Vault, restore testing on a schedule, unified backup status across a fleet, RTO and RPO enforcement, and chargeback.

Attendees will leave with a checklist of platform-team work that turns any PG operator into a real DBaaS, and concrete patterns we have seen succeed in production.

### Why it works
- PGConf audience: many of them are building exactly this
- Vendor-neutral framing, applicable regardless of operator choice
- Concrete: every gap is named, with a build pattern

### Takeaways
1. What an operator does for free, and what it deliberately leaves to you
2. The seven platform-layer gaps and how to fill them
3. A reference architecture for a single-engine PG DBaaS on Kubernetes

---

## 2. The Anatomy of a PostgreSQL Backup CRD: pgBackRest, Multi-Repo, and Tiered Storage

**Format:** 40 min talk + Q&A
**Level:** Intermediate to advanced
**Audience:** PG DBAs, backup engineers, Kubernetes operators

### Abstract

Backing up a PostgreSQL cluster on Kubernetes looks simple from the outside: apply a CR, get a backup. Underneath, there are several layers working together, and each one shapes what you can recover and how fast.

This talk takes a closer look at those layers. We will see how pgBackRest works under the hood, how Kubernetes operators expose it, and how a multi-repo setup opens up tiered storage strategies that are hard to get any other way. Along the way we will touch on full, differential, and incremental backups, point-in-time recovery via WAL archiving, dedicated Backup and Restore CRs, multi-cloud storage backends, and clone-from-backup flows for spinning up a new cluster from an existing repository.

### Why it works
- Technically deep, niche-but-loved
- pgBackRest multi-repo enables architectures that are hard to build any other way
- Audience leaves with backup architecture they can apply Monday morning

### Takeaways
1. How pgBackRest backups, retention, and repos actually work
2. How to design a multi-repo strategy for cost, DR, and recovery time
3. How Kubernetes operators expose this engine, and where the abstraction leaks

---

## 3. Multi-Region Disaster Recovery for PostgreSQL on Kubernetes: A Practical Walkthrough

**Format:** 40 min talk + Q&A
**Level:** Intermediate
**Audience:** PG DBAs, SREs, anyone responsible for a recovery SLA

### Abstract

Cross-region disaster recovery for PostgreSQL is not something the engine solves on its own. PostgreSQL gives you streaming replication and a WAL archive; building a real multi-region setup on top of those primitives is the job of the layer above.

On Kubernetes, a PG operator takes you most of the way. A standby cluster can run read-only in a second region, follow WAL from a streaming source or a pgBackRest repo in object storage, and track the primary continuously. Promotion is a single declarative change in the standby's CR, after which the in-cluster failover machinery takes over.

This talk is a practical walkthrough of that setup. We will look at how to wire up the primary, the standby, and the storage that connects them, what observability you get along the way, and how a planned promotion actually unfolds. We will also be honest about what the operator does not do: cross-cluster outage detection, fencing the old primary, and application traffic redirection. For each, we will point at patterns that work.

### Why it works
- Concrete and demo-able
- Vendor-neutral but technically specific
- Honest about the operator-vs-platform boundary
- DR runbook content is evergreen and reusable

### Takeaways
1. How operators model the standby cluster, and where the abstractions differ
2. The YAML for the primary, the standby, and the promotion
3. The split-brain and traffic-redirection patterns the operator does not provide
