# ArangoDB StatefulSet for Kubernetes
Run arangodb as a statefulset in the cluster. Uses volume mount to persist the database.

This repository provides Kubernetes manifests for running **ArangoDB as a StatefulSet** within a Kubernetes cluster.  The deployment uses **persistent volume mounts** to ensure database data is retained across pod restarts, rescheduling, and node failures—making it suitable for development, testing, and production environments.

---

## Overview

ArangoDB is a native multi-model database supporting:

- Document
- Graph
- Key/value

This StatefulSet-based deployment enables:

- Stable network identities for each ArangoDB pod
- Persistent storage using Kubernetes volumes
- Predictable pod startup and shutdown ordering
- Compatibility with Kubernetes-native operations

---

## Architecture

- **Workload Type:** StatefulSet  
- **Storage:** PersistentVolumeClaim (PVC) per pod  
- **Networking:** Stable DNS names via headless service  
- **Data Persistence:** Volume-mounted ArangoDB data directory  


## Prerequisites

- Kubernetes 1.22+
- kubectl configured for your cluster
- A default StorageClass (or one explicitly defined)
- Persistent volumes supported by your environment


## Deployment

### 1. Clone the repository

```bash
git clone https://github.com/pdvd-arangodb/arangodb-statefulset.git
cd arangodb-statefulset

```

2. Create required resources

Apply configuration and secrets first:

```bash
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml

```

3. Deploy the StatefulSet

```bash
kubectl apply -f service.yaml
kubectl apply -f statefulset.yaml

```

4. Verify Deployment

```bash
kubectl get pods
kubectl get pvc
kubectl get svc

```
You should see pods similar to:

```sql

arangodb-0   Running
arangodb-1   Running
arangodb-2   Running

```

### Accessing ArangoDB
Port-forward (local access)
   
```bash

kubectl port-forward svc/arangodb 8529:8529

```
Then open:
http://localhost:8529

| Service          | Port |
| ---------------- | ---- |
| Web UI / API     | 8529 |
| Cluster Internal | 8529 |


Persistent Storage

Each ArangoDB pod receives its own persistent volume:

- Data path:

```bash
/var/lib/arangodb3
```

- Storage behavior:

  - Data persists if pods restart
  - Data persists if pods move nodes
  - Data remains until PVC is deleted

### To Remove All Data

```bash
kubectl delete pvc -l app=arangodb
```


### Scaling

To scale the StatefulSet:
```bash
kubectl scale statefulset arangodb --replicas=3
```

StatefulSets ensure:

- Ordered pod creation
- Ordered termination
- Stable identity per instance

### Configuration

Configuration options can be modified through:

- ConfigMap – ArangoDB settings
- Secrets – root password, JWT secrets
- StatefulSet – resources, volume size, replica count

Common settings include:

- Memory limits
- Storage size
- Authentication mode
- Cluster vs single-server mode
- Production Considerations

For production environments, consider:

- Enabling ArangoDB cluster mode
- Using separate Agency, DBServer, and Coordinator roles
- Setting resource requests and limits
- Configuring backups
- Enabling TLS
- Restricting network access via NetworkPolicies
- Monitoring with Prometheus exporters

### Compatibility

Tested with:
- Kubernetes (EKS, GKE, AKS, k3s)
- CSI-backed persistent volumes
- ArangoDB 3.x


## References

[https://www.arangodb.com/docs/](https://www.arangodb.com/docs/)
[https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
[https://www.arangodb.com/docs/stable/deployment-kubernetes.html](https://www.arangodb.com/docs/stable/deployment-kubernetes.html)


### Community

- Website: [https://ortelius.io](https://ortelius.io)
- GitHub: [https://github.com/ortelius](https://github.com/ortelius)
- Discord: [https://discord.gg/ortelius](https://discord.gg/ortelius)

Maintained by the Ortelius open-source community.
