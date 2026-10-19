# Presentation Script

## Slide 1: Title Slide

"Hey everyone, welcome! My name is George Kechagias and today we're going to talk about database backups — and specifically how to make them automated, consistent, and safe using Kubernetes Operators."

---

## Slide 2: About Me

"A quick intro — I'm a Senior Software Engineer at Percona, where I work on the Percona Kubernetes Operators. I'm based in Athens, Greece. So... I spend most of my workday thinking about how to make databases behave nicely inside Kubernetes — which is not always easy."

---

## Slide 3: Agenda

"Here's what we'll cover today. We'll start with why backups matter — which sounds obvious, but you'd be surprised how often they're an afterthought. Then we'll look at what actually goes wrong in production, the specific challenges of backing up databases in containers, the tooling available, how Kubernetes Operators solve these problems, some live examples, and we'll wrap up with best practices. And of course, Q&A at the end."

---

## Slide 4: Why Backups Matter

"So, why do backups matter? Let me give you a few scenarios as a warmup. 

Human error — someone runs a DELETE without a WHERE clause. A bad migration drops a column. 

Infrastructure failures — disks corrupt, nodes crash, cloud volumes just... disappear sometimes.

replication is NOT backup. If your data gets corrupted, that corruption replicates to every replica you have. Replication protects you from hardware failure, not from data corruption.

Business logic bugs — a bug on the application layer silently writes bad data for days before anyone notices.

Regulations like GDPR actually require data backup and restore options, so backups aren't just a nice-to-have; they can be a legal requirement."

---

## Slide 5: What Actually Breaks in Production

"Now let's talk about what actually breaks. And I'm not talking about hypothetical scenarios — these are things that happen in real production environments.

Your backups succeed... but your restores fail. You've been running backups for months and then the day you actually need to restore, it doesn't work.

Failover during backup — the replica you're backing up gets promoted mid-backup, and now your backup is inconsistent.

Replication lag corrupts the backup. You back up from a lagging replica thinking it's current, but it's actually missing recent transactions.

And the worst one — nobody notices failures for weeks. The backup job has been failing silently and everyone just assumed it was fine.

So here's the question: can Kubernetes Operators help with all of this? And that's what the rest of this talk is about."

---

## Slide 6: Challenges — Backing Up Databases in Containers

"Let's see why this is particularly hard in Kubernetes....That orchestration.

First, in kubernetes, pods reschedule, restart, scale up and down. Your backup jobs need to tolerate all of that gracefully.

Second, you're dealing with stateful data in a stateless world. Persistent Volumes add considerablecomplexity around mounting, locking, and snapshotting.

Third, consistency and transaction safety. You can't just do a naive filesystem snapshot of a database — you need to respect WAL files for Postgres, the oplog for MongoDB, binlogs for MySQL. Otherwise you end up with a corrupt backup that's useless."

---

## Slide 7: Challenges (cont.)

"And there's more.

Operational complexity — when teams cobble together ad-hoc CronJobs and shell scripts, you end up with snowflake configurations that are impossible to audit or reproduce.

the challenge of Multi-topology awareness — you need to know whether you're backing up a primary, a replica, or a shard. You need to target the right instance without impacting performance for your end users.

Observability: are my backups succeeding? Restore tests are rarely automated, so you often don't know that for sure until it's too late.

The bottom line: traditional backup approaches don't map cleanly to Kubernetes primitives. Maybe we can do something better."

---

## Slide 8: Backup Tooling

"So let's look at some of the tools available out there. These are open source, battle-tested, production-grade backup tools.

For MySQL, there's Percona XtraBackup — or PXB. It does hot, non-blocking backups, supports full and incremental, compression, encryption, point-in-time recovery, and can stream directly to S3, GCS, or Azure.

For MongoDB, there's Percona Backup for MongoDB — PBM. It handles consistent backups across sharded clusters and replica sets, supports logical, physical, and incremental backups, plus PITR and cloud storage.

For PostgreSQL, there's pgBackRest. Full, differential, and block-level incremental backups, parallel backup and restore, encrypted repos, page checksum validation, and cloud storage support.

---

## Slide 9: Bad Bunny Slide

*(pause for laughs)* 

Nowadays Kubernetes Operators are everywhere, and databases are not an exception! The idea is that they are making the management of a database cluster easier. And they are becoming very attractive to teams that have no DBAs, but platform and software engineers working with web services.......

---

## Slide 10: Transition

"So here's the key thing — PXB, PBM, and pgBackRest are exactly what the Percona Operators use under the hood - other OSS operators use the same or similar tooling as well. The Operators don't reinvent backup tooling; they orchestrate these proven tools in a Kubernetes-native way."

---

## Slide 11: Some Kubernetes Operators

"So let's look at the Operators themselves.

We have the Percona Operator for MySQL based on Percona XtraDB Cluster — that's PXC. There's also one based on Percona Server for MySQL — PS. Then the Percona Operator for MongoDB — PSMDB. And the Percona Operator for PostgreSQL — PG.

All of them support scheduled and on-demand backups, backup to S3-compatible storage, and point-in-time recovery. So you get a consistent experience across all three database engines."

---

## Slide 12: How Operator Backups Work (Very High Level)

"At a very high level, here's how it works.

You define a Custom Resource — like a PerconaXtraDBClusterBackup. The Operator picks that up and orchestrates the backup. Data gets streamed to object storage — S3, GCS, Azure. The Operator tracks the backup status and metadata for you. And when you need to restore, you create a separate restore CR.

It's declarative, it's Kubernetes-native, and it's automated."

---

## Slide 13: Under the Hood — PXB in the PXC Operator

"Let me go a bit deeper into how the PXC Operator handles MySQL backups specifically.

There are two backup modes. The default is Job-based — the Operator creates a Kubernetes Job that runs xtrabackup piped to xbcloud, which streams directly to object storage. No local staging needed, which is great because you don't need extra disk space on your nodes.

Then there's the sidecar mode, which is currently in alpha. A gRPC server runs alongside your PXC pods, and the Operator calls grpc methods CreateBackup on the primary directly. This avoids spinning up a separate pod entirely."

---

## Slide 14: Under the Hood (cont.)

"Some key design decisions worth highlighting.

Lease-based serialization — there's a Kubernetes Lease that ensures only one backup runs at a time per cluster. You don't want two backups fighting over the same database. There's an opt-in parallel mode if you need it.

There's a state machine for backup CRs — they transition through New, Starting, Running, and then Succeeded or Failed, with configurable deadlines at each stage.

The streaming pipeline — xtrabackup pipes directly to xbcloud, which streams to your cloud storage. No intermediate files.

Cluster health gating — backups are automatically suspended if the cluster isn't in a Ready state, preventing inconsistent snapshots.

And PITR — a dedicated collector pod tails binlogs from the primary and uploads them to object storage continuously, so you can do point-in-time recovery between full backups. This is huge for minimizing data loss."

---

## Slide 15: Demo — MySQL (PXC) Backup

"Let's see this in action. Here's a MySQL backup — it's just a simple YAML file. You specify the cluster name and the storage name, apply it with kubectl, and that's it. The Operator takes care of everything else.

You can check the status with kubectl get pxc-backup and watch it go through the state transitions."

---

## Slide 16: Demo — PostgreSQL Backup

"Same idea for PostgreSQL. A PerconaPGBackup CR, you specify the cluster and the repo name, apply it, and the Operator handles the rest using pgBackRest under the hood.

As you can see in the screenshot, the backup status is tracked and reported right there in the CR status."

---

## Slide 17: Scheduling Backups

"In general, you don't want to be creating backup CRs manually every day. All four Operators mentioned support cron-based scheduling right in the cluster CR.

You define a schedule — like run at 2 AM daily — set a retention policy to keep the last 7 backups, point it at your storage, and you're done. No external cron needed. Retention policies are built in. And you can enable PITR alongside scheduled backups for complete coverage."

---

## Slide 18: Restoring from a Backup

"Restoring is just as straightforward. You create a restore CR, reference the backup you want to restore from, and the Operator handles the full restore lifecycle.

You can restore to the same cluster or to a completely new one. And if PITR is enabled, you can restore to a specific point in time — not just to whenever the last full backup happened.

---

## Slide 19: Backup Best Practices

"Alright, let's talk best practices. These are things I've seen matter in production. You can consider them as well.

Number one — test your restores regularly. Green backup status does not mean usable backups. Spin up a fresh cluster in a separate namespace and actually try to restore. Automate this if you can.

Number two — use operator-managed schedules. Let the Operator handle retries, health checks, and coordination instead of rolling your own CronJobs.

Number three — store credentials in Kubernetes Secrets. Never hardcode your S3 keys in backup CRs.

Number four — monitor backup CR status. Set up alerts on Failed states. Don't just assume everything is fine because it was fine last week.

Number five — version your CRs in Git. Treat your backup and cluster definitions as infrastructure as code.

And the golden rule: no restores tested equals no backups."

---

## Slide 20: Percona Live Promo

"Quick shout-out — if you're interested in diving deeper into any of this, check out Percona Live. It's a great conference for all things open-source databases. There is a call for speakers open right now, so if you want to share your knowledge, experience, etc with the open source community, this is a great chance."

---

## Slide 21: What to Remember

"Let me leave you with the key takeaways.

Backups are a first-class workload, not an afterthought. Treat them with the same importance as your application deployments.

Kubernetes Operators make backups application-aware and database-aware. They understand your database topology and handle the complexity for you.

If you don't test restores, you don't have backups. I cannot stress this enough.

PITR is mandatory for real-world data safety. Full daily backups alone aren't enough.

And backup configuration is part of your production setup. It should be reviewed, versioned, and treated as seriously as any other production config.

Automate backups. Practice restores. Improve your sleep. And drink water."

---

## Slide 22: Q&A

"That's it from me! Thank you all for your time. I've got some resource links here — the Percona Community Forum, Percona's GitHub, and the repository for this slide deck if you want to grab it.

I'm happy to take any questions — thank you again!"
