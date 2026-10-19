# The Storage Layer Behind Kubernetes Database Operators That Enables Real-World DBaaS

Percona Live 2026 Amsterdam · Kubernetes track
George Kechagias — Senior Software Engineer, Percona

> Skeleton. Every slide has a one-line **Point** (the single thing the audience takes away),
> a rough **content** sketch, and `Notes:` for what you say out loud.
> `[TODO]` marks a gap; `[SOURCE]` marks a claim that needs a citation or a measurement.
> Time column budgets 50 min of content + 10 min Q&A.

---

## Deck style (from `Percona Live Amsterdam Slide Template.pdf`)

Every slide uses one of the three template masters. 16:9 (720×405 pt). Font: **Poppins**
throughout — ExtraLight/Light for body, SemiBold/Bold for headings and the **Point** line.

| Token | Hex | Use |
| --- | --- | --- |
| Charcoal | `#282727` | Dark background; body text on light slides |
| White | `#FFFFFF` | Light background; body text on dark slides |
| Violet | `#653DF4` | Single accent — the **Point** line, arrows, highlighted table cells |
| Lime | `#F6FE54` | Sparingly: one number or word per slide, section numbers, "LIVE" wordmark |

Masters:

- **Title** — charcoal, big Percona Live lockup on the left, mountain photo cutting in from the
  bottom-right on a diagonal. Slides 1 and 40 only.
- **Dark content** — charcoal, mountain sliver top-right, small logo bottom-right. Section
  dividers and payoff slides. Reads as a beat change.
- **Light content** — white, charcoal wedge bottom-left with the logo, violet triangle top-right.
  The default: all body, table, diagram and code slides.

Rules of thumb:

- Never two consecutive dark content slides — the master is a punctuation mark, not a mode.
- Diagrams and tables always go on Light content; the charcoal wedge sits bottom-left, so keep
  the lower-left ~25% of those slides clear.
- One accent per slide. Violet for the takeaway line, lime for at most one figure.
- `© 2026 Percona` and the logo are baked into every master — don't re-add them.

`python3 build.py` renders this file into `deck.html` (and `deck.pdf` via Chrome print) using the
three master pages of the template PDF as slide backgrounds, so the artwork is the template's own.
In the deck: arrows/space navigate, `n` toggles speaker notes, `f` goes fullscreen.

---

## Section 0 — Open (0:00–0:04, 4 min)

Layout: Dark content

---

### Slide 1 — Title

Layout: Title

- Talk title, name, role (the Percona Live lockup is part of the master)
- Handle / QR to slides repo, bottom-left in white on the charcoal

Notes: 20 seconds. Don't read the title.

---

### Slide 2 — Who I am, and why storage

Layout: Light content

- One line on what I work on (Percona Kubernetes operators)
- Why the storage layer is the part that decides whether your DBaaS is real
- No life story

Notes: Credibility in 30 seconds, then get out.

---

### Slide 3 — The promise vs the reality

Layout: Dark content

- Left: the DBaaS promise — "resize it", "restore it to 14:32", "fail over to another region"
- Right: what those three actually require from the storage layer
- **Point:** every DBaaS feature you sell is a storage capability you're depending on

Notes: This is the frame for the whole talk. [TODO] pick a real customer-shaped example.

---

### Slide 4 — What this talk is / isn't

Layout: Light content

- Is: how operators drive PV, CSI, snapshots and object storage across the DB lifecycle
- Is: where the operator's job ends and the storage controller's job begins
- Isn't: a CSI driver benchmark shootout; isn't vendor selection advice

Notes: Sets expectations, prevents the wrong Q&A.

---

### Slide 5 — Roadmap

Layout: Light content

- Lifecycle spine: **provision → run → expand → back up → restore/PITR → clone → DR**
- Walked once, concretely, on the PS operator; then the same spine for PostgreSQL and MongoDB

Notes: Call back to this slide between sections so people know where they are.

---

## Section 1 — From the CR to the volume: what the PS operator actually does (0:04–0:12, 8 min)

Layout: Dark content

---

### Slide 6 — The storage API you actually write

Layout: Light content

- `spec.mysql.volumeSpec.persistentVolumeClaim` — a bare `PersistentVolumeClaimSpec`:
  `resources.requests.storage: 2Gi`, optional `storageClassName`, optional `accessModes`
- It also takes `emptyDir` and `hostPath`; PVC wins, then hostPath, then emptyDir
- **Point:** the operator's entire storage API is a PVC spec you hand it — everything else is consequence

Notes: Show the four lines from `deploy/cr.yaml`. The room expects something bigger; that's the joke.

---

### Slide 7 — What the operator builds out of those four lines

Layout: Light content

- One StatefulSet `cluster1-mysql`, with `volumeClaimTemplates: [datadir]` (+ any `sidecarPVCs`)
- The StatefulSet controller expands the template per ordinal:
  `datadir-cluster1-mysql-0/-1/-2` → three PVCs, three PVs, one per replica
- The operator keeps reconciling those PVCs' labels/annotations, and ignores any whose ordinal
  is `>= .spec.mysql.size`
- **Point:** the operator authors one claim; the StatefulSet controller multiplies it per replica

Notes: This is the whole provisioning path in one diagram. Everything later is a variation on it.

---

### Slide 8 — So what is actually on that volume?

Layout: Light content

- `/var/lib/mysql` — the MySQL datadir, nothing exotic: `mysql.ibd`, per-table `.ibd`,
  `#innodb_redo/`, undo tablespaces, the doublewrite files, binlogs
- Data, redo and binlog share one filesystem, one PV, one throughput budget
- **Point:** "the volume" is a single filesystem the database is writing to three different ways

Notes: [TODO] paste a real `ls -la /var/lib/mysql` from a running pod — the actual bytes land better
than a description of them.

---

### Slide 9 — …and the operator's own state, in the same datadir

Layout: Dark content

- `bootstrap.lock`, `clone.lock` — this pod is initializing, or cloning from a donor right now
- `full-cluster-crash`, `clusterset-recovery`, `sleep-forever` — refuse to start, a human must look
- `cr-version` — which operator version last touched this data directory
- The entrypoint reads these on every start to decide: initialize, clone, join, or stop
- **Point:** the PV is not only the database's — it's where the operator keeps the state it cannot
  keep in the CR, because it has to survive the pod

Notes: The "I didn't know that" slide of the section. These files are why a PV that looks healthy can
still refuse to come up. [SOURCE] percona-server-mysql-operator `build/ps-entrypoint.sh`.

---

### Slide 10 — Not everything the pod mounts is storage

Layout: Light content

- `datadir` → PVC. `bin`, `mysqlsh`, `tmp` → emptyDir. `config` → ConfigMap. `users`, `tls` → Secrets
- The init container installs the operator's own binaries into the shared emptyDir on every start
- **Point:** exactly one of those mounts survives a reschedule — be sure you know which

Notes: Quick slide. It kills the "why did my config change disappear" class of question.

---

### Slide 11 — How a replica actually gets its data

Layout: Light content

- `mysql-0` initializes an empty datadir; `mysql-1`, `mysql-2` get a fresh, empty PVC and then run
  `CLONE INSTANCE FROM <donor>` — MySQL fills the volume over the network
- No snapshot, no volume clone, no CSI involvement at all
- **Point:** at the storage layer a new replica is just an empty PVC; the copy is the database's job

Notes: Plant this now — the cloning section later is the "what if the storage layer did that copy
instead" answer.

---

## Section 2 — The storage layer underneath, minus the mythology (0:12–0:18, 6 min)

Layout: Dark content

---

### Slide 12 — The objects that actually matter

Layout: Light content

- The same objects the last section just created, named properly:
  PVC / PV / StorageClass / VolumeAttachment — one line each
- Diagram: who creates what, who mutates what — operator, StatefulSet controller, CSI
- **Point:** the operator authors intent; controllers reconcile it. It cannot force storage to comply.

Notes: Keep it fast — most of the room knows this, but the ownership boundary is the payload.

---

### Slide 13 — CSI in one slide

Layout: Light content

- Controller plugin vs node plugin; the calls that matter: Create, Attach, NodeStage, Expand, Snapshot, Clone
- **Point:** most of these are *optional capabilities* — your feature set is whatever your driver implements

Notes: [SOURCE] CSI spec version + optional capability list.

---

### Slide 14 — The capability matrix is the real API

Layout: Light content

- Table: capability × (EBS / GCE PD / Azure Disk / Ceph-RBD / local)
- Expansion, online expansion, snapshot, cross-AZ, cross-region copy, clone
- **Point:** the operator's behaviour changes per-cluster because of this table

Notes: [TODO] build and verify the table — this is a slide people photograph.

---

### Slide 15 — Block vs file vs object

Layout: Light content

- Where each fits in a database deployment, and where each will hurt you
- NFS/RWX for a primary data dir: why not
- **Point:** databases want block for data, object for durability. Don't cross the streams.

Notes: [SOURCE] p99 latency numbers if I can produce them; otherwise state as design guidance.

---

### Slide 16 — StatefulSets are a scheduling contract, not a storage one

Layout: Light content

- Stable identity + stable PVC binding; what that buys and what it doesn't
- Topology, `volumeBindingMode: WaitForFirstConsumer`, zone pinning as an availability constraint
- **Point:** your volume pins your pod to a failure domain

Notes: This is where a lot of "why won't it reschedule" pain comes from.

---

## Section 3 — Provisioning decisions you can't undo (0:18–0:23, 5 min)

Layout: Dark content

---

### Slide 17 — What the operator decides at provision time

Layout: Light content

- StorageClass choice, size, access mode, topology spread
- Decisions that are effectively permanent (SC immutability on a PVC, reclaim policy)
- **Point:** provisioning is where you silently choose which future features you'll have

Notes: Strongest "you will regret this later" slide — make it land.

---

### Slide 18 — Sizing, and the fact that you can only go up

Layout: Light content

- Expansion is one-way in practice; shrinking means migrate
- Cost of over-provisioning vs the operational cost of the resize path
- **Point:** pick a growth strategy, not a number

Notes: [TODO] concrete guidance: initial size heuristic per engine.

---

### Slide 19 — Local NVMe: the tempting trap

Layout: Light content

- Throughput/latency win vs no snapshot, no reattach, node loss = replica loss
- When it's the right call (replicas as the durability unit, fast rebuild)
- **Point:** local disks push the durability burden onto replication and backups

Notes: Nuance, not a ban. [TODO] rebuild-time number.

---

### Slide 20 — Provisioning failure modes

Layout: Light content

- Pending PVC, no capacity in zone, quota, driver not installed
- What a good operator surfaces in CR status vs what it hides
- **Point:** the operator's job is to make storage failures legible

Notes: Show a real status/conditions snippet. [TODO] capture from a live cluster.

---

## Section 4 — Online volume expansion (0:23–0:28, 5 min)

Layout: Dark content

---

### Slide 21 — The two-phase expansion, honestly

Layout: Light content

- ControllerExpandVolume → NodeExpandVolume → filesystem grow
- Online vs offline; when a pod restart is still required
- **Point:** "online" is a property of the driver + filesystem, not of the operator

Notes: [SOURCE] version gates.

---

### Slide 22 — Why operators can't just edit the PVC (and how they do anyway)

Layout: Dark content

- The PS operator edits each PVC in place, stamps `percona.com/pvc-resize-in-progress` on the CR,
  and watches PVC conditions + events until every claim reports the new size
- Then it deletes the StatefulSet with `--cascade=orphan` purely to update the immutable
  `volumeClaimTemplates`; the new set adopts the running pods back
- Guardrails: requests are rounded up to whole GiB, shrink requests are reverted, and expansion
  only happens at all if `spec.storageScaling.enableVolumeScaling` is set
- `storageScaling.autoscaling` goes further: `df -B1 /var/lib/mysql` in the pod, grow by
  `growthStep` past `triggerThresholdPercent`, up to `maxSize`
- **Point:** this is the clearest example of operator work existing purely to route around a K8s limitation

Notes: Likely the most "I didn't know that" slide in the deck. Give it room. Source:
`pkg/controller/ps/volumes.go`, `volume_autoscaling.go` (verify against the release you demo).

---

### Slide 23 — When expansion gets stuck

Layout: Light content

- The events the operator keys on: `Resizing`, `FileSystemResizeRequired`, then
  `VolumeResizeFailed` / `ExceededQuota` / `StorageClassNotSupportResize`
- On failure it reverts the CR to the last configured size, so the cluster stops re-trying a resize
  the storage layer has already refused
- `RecoverVolumeExpansionFailure`, quota hit mid-resize, per-cloud resize cooldowns
- **Point:** have a runbook before you need one

Notes: [TODO] tell the war story here.

---

### Slide 24 — Expansion, rolled out across a cluster

Layout: Light content

- One replica at a time, ordering, primary last
- Why the naive "resize everything" is an availability event

Notes: Diagram, animated across replicas if the template allows.

---

## Section 5 — Backups (0:28–0:35, 7 min)

Layout: Dark content

---

### Slide 25 — Two families of backup, two different storage stories

Layout: Light content

- Engine-native to object storage (pgBackRest / XtraBackup / PBM)
- Volume snapshots via CSI
- **Point:** they fail differently, cost differently, and restore at different speeds

Notes: This is the spine of the section.

---

### Slide 26 — Object-storage backups: what the operator orchestrates

Layout: Light content

- Job/CronJob, credentials, retention, where the backup pod runs relative to the data
- Reading from a replica; the network path the bytes take
- **Point:** portable, engine-consistent, slow to restore at size

Notes: [TODO] restore-time-at-scale number.

---

### Slide 27 — CSI snapshots: what they actually guarantee

Layout: Dark content

- VolumeSnapshotClass, VolumeSnapshot, VolumeSnapshotContent — the object graph
- Crash-consistent by default; what "consistent" means per engine
- **Point:** a snapshot of a running DB is a crash, and your DB must be able to survive one

Notes: The most commonly misunderstood thing in the whole talk. Slow down.

---

### Slide 28 — Getting to application consistency

Layout: Light content

- fsfreeze, engine-level quiesce/lock, checkpoint-then-snapshot
- Multi-volume: data + WAL on separate PVCs, and why VolumeGroupSnapshot matters
- **Point:** consistency across volumes is not free and not automatic

Notes: [SOURCE] VolumeGroupSnapshot maturity as of 2026.

---

### Slide 29 — Snapshots vs object storage: choose per requirement

Layout: Light content

- Table: RTO, RPO, cost, portability, cross-region, retention limits, blast radius
- **Point:** most production setups need both, for different tiers

Notes: Second photographed slide. Make the table clean.

---

### Slide 30 — The backup you never tested

Layout: Light content

- Verification: restore-and-check as a first-class operator feature
- Snapshot dependency on the source volume/account — the deletion footgun
- **Point:** untested backups are a storage-cost line item, not a recovery strategy

Notes: Short, punchy, moral of the section.

---

## Section 6 — Restore, PITR, cloning (0:35–0:40, 5 min)

Layout: Dark content

---

### Slide 31 — Restore is a provisioning problem

Layout: Light content

- Restore-from-snapshot = PVC with a `dataSource`; restore-from-object-store = fresh PVC + engine restore
- **Point:** the operator is choosing between "new volume" and "new data" every time

Notes: Diagram both paths side by side.

---

### Slide 32 — PITR: base + a continuous stream

Layout: Light content

- WAL / binlog / oplog shipped to object storage; the recovery target
- Why PITR is fundamentally an object-storage feature, not a snapshot feature
- **Point:** snapshots give you points; the log gives you the space between them

Notes: One clear diagram: base backup + log stream + target time.

---

### Slide 33 — Snapshot + log: the hybrid that wins

Layout: Dark content

- Fast base restore from snapshot, then roll forward with the log
- What the operator must coordinate for this to work
- **Point:** best RTO/RPO combination available today

Notes: [TODO] confirm which Percona operators support this shape.

---

### Slide 34 — Cloning: the underrated feature

Layout: Light content

- PVC `dataSource` from a PVC or a snapshot; CSI clone vs "restore into a new cluster"
- Use cases: staging refresh, adding a replica without a full sync, forensic copies
- **Point:** cloning turns a replica rebuild from hours into minutes

Notes: [TODO] rebuild-vs-clone timing.

---

### Slide 35 — Where cloning bites you

Layout: Light content

- Same-SC / same-zone / same-account constraints; provisioned-copy vs CoW performance
- Cloning a running primary — the consistency question, again

Notes: Ties back to slide 21.

---

## Section 7 — Cross-region DR (0:40–0:43, 3 min)

Layout: Dark content

---

### Slide 36 — Three ways to be in another region

Layout: Light content

- Replicate the database · copy backups · replicate the storage
- **Point:** only one of these is really available to a Kubernetes operator today, and it's the DB layer

Notes: Frame the section.

---

### Slide 37 — Why storage-layer cross-region replication mostly isn't the answer

Layout: Light content

- Snapshot copy is asynchronous and coarse; volume-level sync replication is rare and expensive
- Regional PVs: what they do and don't cover
- **Point:** you don't get sub-minute RPO from the storage layer alone

Notes: [SOURCE] per-cloud snapshot copy semantics.

---

### Slide 38 — Cross-cluster replication as the DR primitive

Layout: Dark content

- Async DB replication to a standby cluster in another region, driven by the operator
- What the storage layer contributes: independent PVs, independent failure domain
- **Point:** DR is a database problem with a storage prerequisite

Notes: [TODO] name the concrete Percona operator features here.

---

### Slide 39 — Failover, and the thing everyone forgets

Layout: Light content

- Promotion, DNS/endpoint switch, and the old primary's volumes
- Split-brain risk when the "failed" region comes back with its data intact
- **Point:** fencing is a storage decision too

Notes: Good place for a short war story.

---

## Section 8 — The other two engines (0:43–0:46, 3 min)

Layout: Dark content

---

### Slide 40 — PostgreSQL

Layout: Light content

- Data dir + WAL layout, pgBackRest, WAL archiving, snapshot considerations
- What changes vs the MySQL walkthrough: WAL can live on its own PVC, so consistency spans volumes
- Operator specifics [TODO]

Notes: One slide, not five. Structure it as a diff against Section 1 so the comparison is visual.

---

### Slide 41 — MongoDB

Layout: Light content

- Sharded clusters: per-shard volumes and the cross-shard consistency problem
- What changes vs the MySQL walkthrough: N independent PVCs that must be consistent with each other
- PBM, oplog, why multi-volume consistency gets hard fastest here
- Operator specifics [TODO]

Notes: Land the sharding point — it's the strongest argument for group snapshots.

---

### Slide 42 — Side by side

Layout: Light content

- One table: what each engine needs from storage, and where each one is most fragile
- **Point:** the storage requirements differ more than the operators' APIs suggest

Notes: Summary slide for the section.

---

## Section 9 — Close (0:46–0:50, 4 min)

Layout: Dark content

---

### Slide 43 — Who owns what

Layout: Light content

- The boundary diagram from slide 6, filled in with everything covered
- Operator: intent, ordering, engine coordination · CSI/K8s: the actual bytes
- **Point:** debugging is faster when you know which side of the line you're on

Notes: Payoff slide — callback to the opening frame.

---

### Slide 44 — Checklist to take home

Layout: Light content

- 6–8 concrete items: verify SC expansion, pick topology deliberately, test restores, know your driver's capability matrix, plan multi-volume consistency, budget snapshot cost, write the stuck-resize runbook
- **Point:** the actionable slide people screenshot

Notes: Read them out loud, one line each.

---

### Slide 45 — Thanks / Q&A

Layout: Title

- Slides repo QR, contact, Percona operator links
- Prompt the room with two seed questions in case Q&A is slow

Notes: [TODO] pick the two seed questions.

---

## Backup slides (only if asked)

- Detailed CSI call sequence for expansion
- Full per-cloud capability matrix
- Cost model spreadsheet: snapshots vs object storage at scale
- The stuck-expansion recovery runbook, step by step
