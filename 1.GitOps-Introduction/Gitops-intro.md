# What is GitOps?

**GitOps** is a framework where the entire code delivery process is controlled via Git. It also includes infrastructure and application definition as code and automation to deploy changes and rollbacks.

GitOps can be considered an extension of **infrastructure as code (IaC)** that uses Git as the version control system.

## Overview

The core idea here is to have the infrastructure applications, and all related components **declaratively** defined in one or more Git repositories.

In the GitOps scenario, an **automated process** ensures that the **desired state** in the repository and the **actual state** in the production environment always match.

A **GitOps operator** runs within one of the **Kubernetes clusters**. It continuously **monitors** and **pulls changes** from the Git repository and applies them within the cluster it is running. It can also apply them in a different cluster as well.

Meanwhile, if a developer merges new code to the application Git repo, an **automated CI process** kicks in and does a round of **unit testing**, whilst the application reads the **Docker image** and pushes it to the container repository.

Finally, it also updates the **Kubernetes manifest** within another Git repository.

The GitOps operator identifies the difference between the **desired state** in the Git repo versus the **actual state** in the cluster, it **pulls** the changes and makes sure that the **desired state** always matches the **actual state**.

GitOps also eases the **rollback process**. Since all the code is version controlled in Git, a team member can issue a simple **Git revert command** to go undo any changes which are made earlier.

Since the repo is continuously monitored by a GitOps operator, it picks up the changes and **rolls back to the previous state**.

## GitOps Principles

GitOps has four principles:

1.**Declarative**

A system managed by GitOps must have its desired state expressed **declaratively**.

**Declarative vs imperative approach:**

**Declarative:**
Gitops demands the entire system including infrastructure and application manifest to be declared in a **declarative state**.

**Imperative:**
It discourages the use of the **imperative approach** as it uses a series of explicit commands to change the desired state.

The imperative approach also makes **reconciliation difficult** because it does not store any state.

2.**Versioned and Immutable**

**Desired state** is stored in a way that enforces **immutability**, **versioning**, and retains a complete version history.

The second principle is to make use of Git. All the **declarative files** also known as the desired state, are stored in a Git repository. It provides version control and also enforces **immutability**. Since the desired state is stored and versioned, it is essentially known as the **source of truth state**.

3.**Pulled Automatically**

Software agents automatically pull the desired state declarations from the source.

Once we store the desired state in Git, we must allow any changes to the state to be applied automatically. This brings us to the third principle.
Gitops operators, also known as software agents, automatically pull the desired state from Git and apply them in one or more environments or clusters.

The Gitops operator can run in one of the cluster and can make or push changes to other clusters as well.

4.**Continuously Reconciled**

Software agents continuously observe the actual system state and attempt to apply the desired state.

The final principle talks about **reconciliation**. The GitOps operator also makes sure that the entire system is **self-healing** to reduce the risk of human errors.

The operator continuously loops through three steps: **observe, diff, and act**.

In the **observe** step, it checks the Git repository for any changes to the desired state.

In the **diff** step, it compares the resources received from the previous step to the actual state of the cluster.

In the **Act** step, it uses a reconciliation function and tries to match the actual state to the desired state.

## DevOps Vs GitOps

- **GitOps and DevOps** share some of the same goals with a few differences.

- **GitOps** is typically used with containerization technologies like **OpenShift and Kubernetes**, while **DevOps** can be used with any application.

- In a typical **DevOps pipeline**:
  - A developer writes code using an IDE and commits them to the source code management system.
  - A CI process picks up the latest code and runs through a series of stages to test and build the artifacts before creating an image and publishing it to a container repository.
  - The continuous deployment pipeline connects with the Kubernetes cluster and applies a `kubectl` imperative command to push the changes to the clusters.

- Within a **GitOps pipeline**:
  - We essentially have two Git repositories, one for the application code and the other for Kubernetes manifests.
  - The continuous integration steps remain the same until publishing an image to the container registry.
  - The next step is to clone the manifest configuration repository, for example, update the manifest with the new image name, commit, and push to the branch.
  - The pipeline will also raise a pull request to the manifest repository.
  - A team member will review the pull request, suggest any changes, or if everything is fine, approve it and merge the changes.
  - At this point, the Git operator, running within a Kubernetes cluster, will pull the changes from the repository and synchronize them within the cluster.

- To summarize, in the **DevOps pipeline**, the changes were pushed to the cluster, and in **GitOps**, the changes were pulled by the operator.

## Push vs Pull based Deployments

## Push-based Deployments

- Most deployment tools use a **push-based approach** via a CI/CD pipeline.
- In a push-based approach:
  - Application code goes through various stages of a CI pipeline, and the updates are pushed to the Kubernetes cluster.
  - The CI system needs read/write access to the Kubernetes cluster, which entails exposing Kubernetes credentials outside the cluster and storing them in a CI system, raising potential security issues.
  - The CI system has read-only access to the Git repository and read/write access to the container registry, while the Kubernetes cluster only has read-only access to the container registry.
- Benefits of push-based approach:
  - Deployment is not restricted to a particular plugin, allowing flexibility.
  - Jenkins CI offers multiple plugins and approaches for Kubernetes Deployment, including secret management and HELM chart deployment.
- Challenges of push-based approach:
  - Deployment approach is tied to the CI system, necessitating a rebuild if switching CI tools.
  - Cluster login information is embedded in the CI system for deployment purposes, granting read/write access to the Kubernetes cluster.

## Pull-based Deployments

- In a **pull-based approach**, the GitOps operator deploys new images from within the Kubernetes cluster.
- The operator checks a container repository for new images or a Git repository for manifest updates.
- In a pull-based approach:
  - The CI/CD system has read/write access to the container registry but not to the Kubernetes cluster.
- Benefits of pull-based approach:
  - Deployment approach is not coupled to the CI/CD pipeline, as deployments are internal to the cluster.
  - No external user or client can modify the cluster.
  - GitOps operator supports a multi-tenant model and works with multiple repositories and namespaces.
  - Secrets can be encrypted using various tools and pushed to Git repository or managed using HashiCorp Vault.
  - GitOps operators can scan container registries for newer image versions and deploy the latest image in Kubernetes cluster.
- Challenges of pull-based approach:
  - Managing secrets is trickier compared to the push-based model.
  - Per GitOps principles, even secrets could be declaratively published to Git repository.
  - Deploying Helm charts requires encrypting secrets using tools like HashiCorp Vault or Bitnami Sealed Secrets, adding complexity to the process.

## GitOps Feature Set and Use Cases

## Feature Set

1. **Single Source of Truth**: In GitOps, the Git repository serves as the single source of truth for both infrastructure and application configuration. All changes to the system are made through Git commits, ensuring consistency and version control.

2. **Everything as Code**: GitOps promotes the practice of managing infrastructure and application configuration as code. This includes infrastructure as code (IaC) for provisioning resources and application configuration as code, such as Kubernetes manifests or Helm charts.

3. **Everything is Auditable**: Since all changes are made through Git commits, GitOps provides a complete audit trail of every change made to the system. This facilitates compliance, troubleshooting, and understanding the evolution of the infrastructure and applications over time.

4. **CI/CD Automation**: GitOps leverages continuous integration and continuous deployment (CI/CD) pipelines to automate the deployment process. This includes automated testing, building, and deploying applications based on triggers from Git events.

5. **Continuous Deployment - Application**: Continuous deployment of applications in GitOps involves automatically deploying application changes to production environments after passing through automated tests. This ensures rapid and reliable delivery of new features and bug fixes.

6. **Continuous Deployment - Cluster Resources**: GitOps extends continuous deployment practices to cluster resources, including Kubernetes objects such as pods, services, and deployments. Changes to infrastructure configuration trigger automated updates to the cluster.

7. **Continuous Deployment - Infrastructure**: GitOps automates the deployment of infrastructure changes using tools like Terraform or CloudFormation. Infrastructure changes, such as provisioning new resources or updating configurations, are managed through GitOps pipelines.

8. **Detecting/Avoiding - Configuration Drift**: GitOps continuously monitors the state of the system and detects any configuration drift from the desired state defined in the Git repository. It automatically reconciles the drift by applying the correct configuration, ensuring consistency and reliability.

9. **Multi-cluster - Deployments**: GitOps supports deploying and managing applications across multiple Kubernetes clusters. This enables organizations to implement multi-cluster strategies for high availability, disaster recovery, or geographic distribution of workloads.

## Use Cases

- **Rollback Application - Git Repo**: Rolling back changes to applications by reverting to a previous state in the Git repository.
- **Continuous Deployment - Application**: Automatically deploying application changes to production after passing tests.
- **Continuous Deployment - Cluster Resources**: Automating updates to Kubernetes objects based on changes in the Git repository.
- **Continuous Deployment - Infrastructure**: Automatically provisioning or updating infrastructure resources through GitOps pipelines.
- **Detecting/Avoiding - Configuration Drift**: Monitoring for and correcting any discrepancies between the desired and actual state of the system.
- **Multi-cluster - Deployments**: Deploying and managing applications across multiple Kubernetes clusters.

## GitOps Benefits and Challenges

### Benefits

- **Lightweight and Vendor-Neutral**: GitOps relies on Git as the primary source of truth, making it lightweight and vendor-neutral. It allows teams to use Git repositories without being tied to specific platforms or vendors.

- **Faster, Safer, Immutable, and Reproducible Deployment**: GitOps enables faster and safer deployments by leveraging automation and infrastructure as code (IaC) principles. Deployments are immutable and reproducible, leading to increased reliability and consistency.

- **Eliminating Configuration Drift**: By continuously comparing the actual state of the system with the desired state defined in the Git repository, GitOps helps eliminate configuration drift. This ensures that the system remains consistent and compliant with the desired configuration.

- **Uses Familiar Tools and Processes**: GitOps leverages familiar tools and processes, such as Git version control and CI/CD pipelines, making it easier for teams to adopt and integrate into their existing workflows.

- **Revisions with History**: GitOps provides a complete revision history of all changes made to the system, allowing teams to track and understand the evolution of configurations over time. This facilitates auditing, troubleshooting, and rollback procedures.

### Challenges

- **Doesn't Help with Secret Management**: GitOps does not inherently address the challenge of secret management, such as securely storing and distributing sensitive information like API keys or passwords. Additional tools or processes may be needed to manage secrets securely.

- **Number of Git Repositories**: Managing a large number of Git repositories can become challenging, especially in organizations with complex or distributed systems. It may require careful organization and governance to maintain consistency and avoid confusion.

- **Challenges with Programmatic Updates**: Programmatic updates to configurations or infrastructure can introduce complexity and potential risks if not properly managed. It's essential to have robust testing and validation processes in place to mitigate these challenges.

- **Governance Other Than PR Approval**: GitOps typically relies on pull requests (PRs) for approving and merging changes, which may not always align with governance requirements in certain organizations. Alternative governance mechanisms may need to be implemented to ensure compliance and security.

- **Malformed YAML/Config Manifests**: Managing YAML or configuration manifests manually can lead to syntax errors or malformed configurations, causing deployment failures or unexpected behavior. Proper validation and testing of manifests are essential to prevent such issues.

## GitOps Projects/Tools

- **ArgoCD**: A declarative, GitOps continuous delivery tool for Kubernetes.

- **Atlantis**: A tool for automating Terraform workflows via pull requests.

- **Autoapply**: A GitOps tool for automatically applying Kubernetes manifests from Git repositories.

- **FluxCD**: A GitOps operator for Kubernetes that automatically ensures that the state of your cluster matches the configuration defined in Git.

- **CloudBees Rollout**: A feature flagging and experimentation platform for Kubernetes.

- **HELM Operator**: An operator for managing Helm charts via GitOps principles.

- **Faros**: A GitOps tool for managing Kubernetes configurations.

- **Ignite**: A GitOps tool for managing infrastructure and Kubernetes clusters.

- **Flagger**: A progressive delivery tool that automates the promotion of canary deployments in Kubernetes.

- **JenkinsX**: A CI/CD automation tool for Kubernetes, offering GitOps capabilities.

- **GitKube**: A GitOps operator for Kubernetes, enabling cluster and application management via Git.

- **PipeCD**: A continuous delivery tool for deploying applications using GitOps practices.

- **Werf**: A GitOps tool for building and deploying applications to Kubernetes.

- **Weave Cloud**: A platform for GitOps workflows, providing monitoring, deployment, and management capabilities for Kubernetes.

- **Weave GitOps Core**: A GitOps toolkit for managing Kubernetes clusters and applications.

- **KubeStack**: A GitOps platform for managing Kubernetes clusters and applications.
