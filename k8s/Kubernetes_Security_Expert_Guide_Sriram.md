# Kubernetes Security -- Complete Expert Guide

Author: Sriram Palepu\
Role: DevOps Engineer\
Purpose: Personal Reference & GitHub Documentation

------------------------------------------------------------------------

# 1. Kubernetes Security Layers

Kubernetes security can be divided into multiple layers:

1.  Infrastructure Security
2.  Control Plane Security
3.  RBAC (Access Control)
4.  Network Security
5.  Workload Security
6.  Secrets Management
7.  Supply Chain Security
8.  Monitoring & Auditing

------------------------------------------------------------------------

# 2. RBAC (Role-Based Access Control)

## Core Concept

RBAC defines:

-   WHO → User / Group / ServiceAccount
-   WHAT → Verbs (get, list, create, delete)
-   ON WHAT → Resources (pods, deployments, secrets)

## Components

-   Role (Namespace scoped)
-   ClusterRole (Cluster-wide)
-   RoleBinding
-   ClusterRoleBinding

## Best Practices

-   Follow least privilege principle
-   Avoid giving cluster-admin access
-   Separate namespaces per environment
-   Use ServiceAccounts for applications
-   Disable anonymous access

------------------------------------------------------------------------

# 3. Network Security

## Default Behavior

All pods can communicate with each other by default.

## Network Policies

NetworkPolicy restricts traffic between pods.

Best Practice: - Deny all traffic by default - Allow only required
communication - Implement Zero Trust model

Prevents: - Lateral movement - Internal attacks

------------------------------------------------------------------------

# 4. Pod Security Standards

Levels: - Privileged - Baseline - Restricted (Recommended for
Production)

Restricted Level Enforces: - No privileged containers - No root user -
No hostPath volumes - No privilege escalation

Example securityContext:

-   runAsNonRoot: true
-   allowPrivilegeEscalation: false
-   readOnlyRootFilesystem: true

------------------------------------------------------------------------

# 5. Secrets Management

Important: Kubernetes secrets are base64 encoded, not encrypted by
default.

Best Practices: - Enable encryption at rest - Use HashiCorp Vault -
Avoid storing secrets in Git - Rotate secrets regularly

------------------------------------------------------------------------

# 6. Container Security

-   Use minimal base images (Alpine, Distroless)
-   Run containers as non-root
-   Scan images using Trivy
-   Set resource limits to prevent DoS

------------------------------------------------------------------------

# 7. Control Plane Security

-   Secure API Server with TLS
-   Enable audit logging
-   Restrict etcd access
-   Encrypt etcd data

------------------------------------------------------------------------

# 8. Admission Controllers

Used to validate or mutate requests before storing in etcd.

Examples: - ResourceQuota - LimitRanger - PodSecurity - OPA / Kyverno

Use Case: Block pods running as root.

------------------------------------------------------------------------

# 9. Runtime Security

Tools: - Falco - Sysdig - Aqua Security

Used to detect abnormal runtime behavior.

------------------------------------------------------------------------

# 10. Disaster Recovery Security

-   etcd backups
-   Store backups in S3
-   Regular restore testing
-   Documented recovery plan

------------------------------------------------------------------------

# 11. Compliance & Auditing

-   Enable audit logs
-   Monitor suspicious activities
-   Required for SOC2, ISO 27001, PCI-DSS

------------------------------------------------------------------------

# 12. End-to-End Secure Kubernetes Strategy

1.  Infrastructure: VPC, IAM, private subnets
2.  Control Plane: RBAC, TLS, audit logs
3.  Network: Network policies
4.  Workloads: Non-root containers, limits
5.  Secrets: Vault integration
6.  Monitoring: Prometheus + Alerts

------------------------------------------------------------------------

# Final Notes

This document summarizes production-grade Kubernetes security practices.
Designed for interview preparation and real-world implementation
reference.
