# Project X · CI/CD Pipeline for a Containerized App

**312 School · DevOps Bootcamp**  
Chicago, Illinois, USA · November 2026

## What I automated

1. A push to the feature branch started GitHub Actions. The workflow built the app image and ran integration tests against a temporary PostgreSQL service container.
2. After the tests passed, the workflow used GitHub OIDC to assume an AWS role and published the image to Amazon ECR with the full Git commit SHA as its tag.
3. A downstream job wrote that exact tag into `values-dev.yaml` and pushed a bot commit. The workflow checked that the branch still pointed to the commit it had built before updating the values.
4. Argo CD read the updated Git revision, rendered the Helm chart, and applied the Deployment. The Kubernetes workers pulled the tagged image from ECR.

## Evidence

The workflow completed successfully. The tag in ECR matched the tag in the bot commit. Argo CD reported Synced and Healthy, and both app Pods were Ready on the same tag. Opening the app confirmed the visible text change.

The source commit identifies the code used to build the image. The later bot commit records what Dev should run. Those two commits have different SHAs.

## What I can explain in an interview

A workflow that runs `helm upgrade` on each build applies changes at deployment time. Argo CD also checks live state against Git between builds and can correct drift with self-healing. For this lab I used GitHub Actions for testing and publishing, Helm for templating, and Argo CD for deployment and reconciliation.

[Back to the Project X overview](../README.md)
