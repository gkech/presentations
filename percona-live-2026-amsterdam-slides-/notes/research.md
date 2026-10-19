# Research backlog

Fill these in before the deck is content-complete — each one backs a claim on a slide.

## To verify / source
- [ ] CSI spec version + which capabilities are actually optional (EXPAND_VOLUME, CREATE_DELETE_SNAPSHOT, CLONE_VOLUME)
- [ ] Kubernetes version gates: online volume expansion, `RecoverVolumeExpansionFailure`, VolumeSnapshot GA, VolumeGroupSnapshot status
- [ ] Per-cloud CSI matrix: EBS / PD / Azure Disk — expansion limits, snapshot consistency, cross-AZ & cross-region copy semantics
- [ ] Percona operator behaviours to state accurately: PG, PS/PXC, PSMDB — what each does on PVC resize, backup, restore, clone
- [ ] pgBackRest / XtraBackup / PBM: what each needs from the filesystem vs object storage
- [ ] Filesystem freeze semantics vs DB-level quiesce for snapshot consistency (per engine)
- [ ] StorageClass `allowVolumeExpansion`, `volumeBindingMode`, reclaim policy — failure modes when set wrong

## Numbers to collect
- [ ] Restore time: object-storage restore vs snapshot-backed volume clone, same dataset size
- [ ] Cost delta: snapshots vs object storage at N TB, 30-day retention
- [ ] Local NVMe vs network-attached block: p99 write latency for a representative workload

## Stories / war stories to pick from
- [ ] A resize that got stuck and how it was recovered
- [ ] A snapshot restore that produced a corrupt/inconsistent DB and why
- [ ] A cross-region DR failover and what the RPO actually turned out to be


## Kubernetes Storage: A Deep Dive — Hristo Stoychev (Medium, 2026-02-24)

https://medium.com/@h.stoychev87/kubernetes-storage-a-deep-dive-97c6600cb3e3
(Medium 403s CLI fetches; read through a reader proxy.)

What went into the deck, and where:

- StorageClass `parameters` carry the performance contract (gp3 `type`/`iops`/`throughput`)
  → "You choose a name; you inherit a capability set", and the callback in
  "The ceiling is in the contract".
- One default StorageClass per cluster; no default + no `storageClassName` = Pending forever.
- PV lifecycle Available → Bound → Released → Failed, and the `claimRef` that must be cleared
  before a Retained PV rebinds → "Four objects, three owners".
- Access modes are enforced per *node*, not per pod; `ReadWriteOncePod` is the strict one.
- CSI sidecars (provisioner, attacher, resizer, snapshotter, node-driver-registrar) and why a
  controller pod shows 6/6; staging is per-node and shared, publishing is the per-pod bind mount
  → "CSI, in one slide".
- Pending (bind/provision) vs ContainerCreating (attach/stage/mount) as the first split in
  triage, plus `kubectl describe pvc|pod` and `kubectl get volumeattachment`
  → "From claim to mount, and where it hangs" and "Signals from Kubernetes".
- `volumeBindingMode: Immediate` provisioning into the wrong zone → "Your volume pins your pod".
- `persistentVolumeClaimRetentionPolicy` (`whenDeleted`/`whenScaled`, stable in 1.27)
  → new slide "Scaling down is a data decision".
- StorageClass-scoped `ResourceQuota` → the `ExceededQuota` bullet on the expansion slide.
- `fsGroup` recursive chown and `fsGroupChangePolicy: OnRootMismatch`
  → new slide "The slow start nobody attributes to storage".

Not used on slides, kept here:

- Kubernetes 1.35 in-place pod restart (`RestartAllContainers`, alpha, feature gate
  `RestartAllContainersOnContainerExits`): keeps UID, node, IP and PVC bindings, so no
  detach/reattach. Parked as a backup slide — verify the status before the conference.
- VolumeSnapshot went GA in 1.20; snapshot restore and clone both go through `dataSource`,
  clone requires same namespace and same StorageClass.
- Encryption: storage-layer (`encrypted: true` + `kmsKeyId` on the class) vs etcd
  `EncryptionConfiguration` — the latter does nothing for PV data.
- `emptyDir` with `medium: Memory` is tmpfs and counts against the container memory limit.
