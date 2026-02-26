# Kubernetes Security -- Advanced Expert Guide

Author: Sriram Palepu Purpose: Advanced Interview & Production Reference

------------------------------------------------------------------------

# 1. Kubernetes Security Architecture Overview

                    +----------------------+
                    |      Users / CI      |
                    +----------+-----------+
                               |
                               v
                      +-----------------+
                      |   API Server    |
                      +-----------------+
                               |
            -------------------------------------------
            |                    |                    |
            v                    v                    v
         etcd             Controller Manager      Scheduler
            |
            v
      Worker Nodes (Kubelet + Container Runtime)
            |
            v
       Pods / Containers

Security must be enforced at every layer.

------------------------------------------------------------------------

# 2. RBAC -- Advanced Implementation

## Example: Namespace Scoped Role

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: deployment-manager
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "create", "update"]
```

## ClusterRole Example

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: read-only-all
rules:
- apiGroups: [""]
  resources: ["pods", "services", "namespaces"]
  verbs: ["get", "list"]
```

## Binding Example

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: bind-dev-user
  namespace: dev
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: deployment-manager
  apiGroup: rbac.authorization.k8s.io
```

Best Practices: - Never assign cluster-admin in production - Use
separate ServiceAccounts per application - Disable
automountServiceAccountToken if not needed

------------------------------------------------------------------------

# 3. Network Policies -- Zero Trust Model

## Default Deny All Policy

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

## Allow Frontend → Backend Only

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

------------------------------------------------------------------------

# 4. Pod Security -- Restricted Profile

## Secure Deployment Example

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      securityContext:
        runAsNonRoot: true
      containers:
      - name: app
        image: nginx:stable
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "256Mi"
```

------------------------------------------------------------------------

# 5. Secrets Management

## Enable Encryption at Rest (API Server Flag)

--encryption-provider-config=/etc/kubernetes/encryption-config.yaml

## Secret Example

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: ZGJ1c2Vy
  password: cGFzc3dvcmQ=
```

Production Recommendation: - Use HashiCorp Vault - Rotate secrets
regularly - Avoid storing in Git

------------------------------------------------------------------------

# 6. Admission Controllers

Example: Enforce Resource Limits via LimitRange

``` yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: resource-limits
spec:
  limits:
  - default:
      cpu: 500m
      memory: 512Mi
    defaultRequest:
      cpu: 100m
      memory: 128Mi
    type: Container
```

Advanced Tools: - OPA (Open Policy Agent) - Kyverno

------------------------------------------------------------------------

# 7. Runtime Security

Architecture:

    Pod Runtime
        |
        v
    Falco Agent
        |
        v
    Alert Manager / SIEM

Detects: - Privilege escalation - Unexpected shell execution -
Suspicious network calls

------------------------------------------------------------------------

# 8. Disaster Recovery Strategy

1.  etcd snapshot backup: etcdctl snapshot save snapshot.db

2.  Store in S3

3.  Periodic restore testing

4.  Infrastructure as Code (Terraform / Ansible)

------------------------------------------------------------------------

# 9. Secure CI/CD Pipeline Flow

    Developer → Git Push
            → Jenkins Pipeline
            → SonarQube Scan
            → Trivy Image Scan
            → Docker Build
            → Push to Registry
            → Deploy to Kubernetes

Pipeline must fail if: - Critical vulnerabilities found - Code quality
gate fails

------------------------------------------------------------------------

# 10. End-to-End Production Security Checklist

\[ \] RBAC least privilege\
\[ \] Network policies applied\
\[ \] Non-root containers\
\[ \] Resource limits defined\
\[ \] Image scanning enabled\
\[ \] Secrets encrypted\
\[ \] Audit logging enabled\
\[ \] etcd backups configured\
\[ \] Monitoring + Alerting enabled

------------------------------------------------------------------------

# Conclusion

Kubernetes security is layered defense. Production clusters require Zero
Trust networking, strict RBAC, workload hardening, runtime monitoring,
and continuous compliance validation.

This guide serves as an advanced reference for interviews and real-world
production implementation.
