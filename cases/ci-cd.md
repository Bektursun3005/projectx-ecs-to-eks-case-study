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

## What I learned

I wanted to know whether the version that passed CI was the version actually running in Dev. After I pushed the change, I opened the successful Actions run and checked that the full commit SHA was on the image in ECR. Then I opened the bot commit and found the same tag in `values-dev.yaml`. Argo CD showed `Synced` and `Healthy`, and both Ready Pods reported that same tag. I opened the app and saw the new label.

Following the tag made the handoff clear to me. GitHub Actions publishes the image and records its tag in Git. Argo CD reads that tag and updates the Deployment. With self-healing enabled, Argo CD can also restore the Git version if someone changes the live Deployment by hand between builds.

[Back to the Project X overview](../README.md)
