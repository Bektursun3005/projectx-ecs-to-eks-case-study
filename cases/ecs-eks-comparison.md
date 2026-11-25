# Project X · ECS and EKS Migration Rehearsal

**312 School · DevOps Bootcamp**  
Chicago, Illinois, USA · November 2026

## Objective

Run the same paper-trading application on ECS Fargate and Amazon EKS at the same time. Verify that both deployments use the same container image and private PostgreSQL database before considering any traffic cutover.

## My work

- Used a CloudFormation-managed ECS baseline with an ALB, VPC, and private RDS instance. Used modular Terraform to add EKS subnets, a cluster, IAM roles, and a managed system node.
- Installed Karpenter with Helm and verified that it provisioned a workload node. Deployed the app on EKS behind a ClusterIP Service and used a local port-forward for validation.
- Verified AWS account and cluster context before provisioning. Scoped PostgreSQL access to the security group used by the EKS workloads and retrieved credentials from Secrets Manager.
- Wrote portions of the Terraform foundation independently and used AI assistance for parts of the implementation and verification. Reviewed the resulting configuration, plans, and runtime evidence.

## Observed results

| Check | What I observed |
| --- | --- |
| Image identity | ECS and EKS reported the same image digest. |
| Runtime readiness | The ECS task was healthy, and the EKS Pod was Ready. Both health endpoints responded. |
| Shared data | A simulated trade created through ECS appeared through EKS with the same timestamp. |
| Traffic | The existing ALB continued serving ECS throughout this rehearsal. |
| Teardown | I removed workload capacity and temporary infrastructure in dependency order and checked the resulting AWS state. |

The public HTTPS Gateway was built in a later Dev exercise. This rehearsal verified workload parity and shared data while ECS continued serving traffic.

[Back to the Project X overview](../README.md)
