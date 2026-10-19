Slide 1 

 PSMDB 1.22 release was very packed: starting with Automatic Storage Resizing

  Running out of disk space is one of the most common causes of MongoDB outages in Kubernetes. The PSMDB Operator can now monitor PVC usage and automatically expand volumes when they hit a configurable threshold up to a
  defined maximum. This removes the need for manual intervention during traffic spikes, and generally reduces the reconfiguration overhead.

 then with Vault Integration

You can now store and manage system user credentials directly in HashiCorp Vault. The Operator authenticates to Vault, retrieves passwords at cluster creation, and continuously syncs changes. This is critical for teams with strict compliance requirements or separation of duties between DBAs and security teams, providing a single, auditable source of truth for credentials.


  then some Logcollector feature improvements

  When persistent logging is enabled, logs can grow unchecked and fill your disk — or in general not be retained long enough post-incident analysis. You can now customize rotation schedules and retention
  policies either inline in the Custom Resource or via a ConfigMap, so log behavior is more controlled.

  the operator now supports Restore to a Cluster with Different Replica Set Names

  Previously, restores required the target cluster to mirror the exact source replica set naming and that was a significant constraint across restores on different environments. Now with this release you can define explicit source-to-target mappings in your PerconaServerMongoDBRestore resource object, making it straightforward to recover into differently-named clusters without restructuring anything else in your configuration.

  ---
  Slide 2 

here some more nice features with the new release:

  Hook Script Support was introduced
  You can now run a custom shell script before mongod, mongos, or pbm-agent starts — without building custom images or patching the Operator in any way. The use case here is that these scripts can run security checks, download external tools,
  or register nodes in external systems.

  Custom Environment Variables are supported
  You can now inject environment variables for mongod, mongos, and logcollector directly from the Custom Resource, a ConfigMap, or a Secret. This lets you pass credentials, feature flags, or API tokens per
  environment without duplication and hardcoding values.

  then we have Private CA for TLS with S3-Compatible Storage
  Teams using internal or self-signed certificate authorities for S3-compatible object storage can now provide their own CA bundle. The Operator uses it to verify TLS connections, and each storage can carry its own
  CA.

  we also have the MinIO Storage Type
  The new minio storage type uses the native MinIO SDK instead of the AWS SDK v2. This matters because some S3-compatible services don't support AWS Signature Version 4, which previously caused silent failures. This
  broadens compatibility with on-premise and private cloud backup targets.

  Service Mesh Integration
  The Operator now sets the appProtocol: mongo field on all services it creates. For clusters running Istio or similar meshes, this enables the mesh to correctly identify and handle MongoDB traffic — enabling
  reliable mutualTLS and proper protocol-aware routing without manual configuration.

  Configurable PBM Startup Timeout
  You can now configure how long the Operator waits for PBM to report that a backup has begun. That value was hardcoded to 120 seconds before this release, so now you can tune it to match your cluster's requirements. 

  finally, the Cluster Readiness Reflects PBM State
  The new PBMReady condition in the Custom Resource status surfaces whether PBM has fully configured all storages. Automation pipelines can now gate backup jobs on this condition rather than relying on timing
  heuristics.