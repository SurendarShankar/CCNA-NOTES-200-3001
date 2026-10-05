# DAY 63 : TERRAFORM

- **Infrastructure as Code (IaC)** is the practice of provisioning and managing infrastructure (servers, networks, cloud resources) using machine-readable configuration files (code) instead of manual configuration (e.g., CLI/GUI).

- **Configuration management** tools (e.g., Ansible, Puppet, Chef) focus on managing existing infrastructure by installing software, configuring settings (e.g., router configurations), and maintaining system state.

- **Infrastructure provisioning** tools (e.g., Terraform) focus on creating, modifying, and deleting infrastructure resources.

- **Mutable infrastructure** can be modified after deployment (e.g., applying updates, patches, or configuration changes).
- **Immutable infrastructure** cannot be changed after deployment; changes involve replacing the previous resource with a new version.

- A **procedural (imperative)** approach follows explicit steps in a specific order to achieve the desired outcome.
- A **declarative** approach defines the desired end state, and the IaC tool figures out the steps needed to achieve the goal.

- **Terraform** is an open-source IaC tool developed by HashiCorp.
  - It is primarily a provisioning tool used for deploying infrastructure on **providers** like AWS, Azure, GCP, Kubernetes, etc.
    - It interacts with these providers via their APIs.
  - Like Ansible, it uses a **push model** and is **agentless**.
  - Main components: **Terraform Core, configuration files, state file, providers**.
  - The basic Terraform workflow consists of three main steps:
    - **Write:** Define the desired state of your infrastructure resources in configuration files.
    - **Plan:** Verify the changes that will be executed before applying them.
    - **Apply:** Execute the plan to provision and manage the infrastructure resources.
  - Terraform Core is written in **Go**, and configuration files are written in **HashiCorp Configuration Language (HCL)**, a DSL.
