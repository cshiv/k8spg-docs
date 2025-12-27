# Installation Methods for Percona PostgreSQL Operator

This document provides an overview of the different methods available to install the Percona PostgreSQL Operator. Each method is suitable for different environments and use cases.

## Installation Methods Overview

### 1. kubectl Installation

**Description:** The standard and recommended method for installing the Percona PostgreSQL Operator using Kubernetes command-line tool (`kubectl`). This method involves applying YAML manifests directly to your Kubernetes cluster.

**Use Cases:**
- Standard Kubernetes deployments
- Environments where you need full control over the installation process
- CI/CD pipelines that prefer declarative YAML-based deployments

**Key Characteristics:**
- Uses `kubectl apply` commands to deploy operator manifests
- Requires applying Custom Resource Definitions (CRD), RBAC, and Operator deployment files
- Can be done via single bundle file (`bundle.yaml`) or individual components
- Works with any standard Kubernetes distribution

**Documentation:** See [kubectl installation guide](../docs/kubectl.md) for detailed steps.

---

### 2. Helm Installation

**Description:** Install the Percona PostgreSQL Operator using Helm, the package manager for Kubernetes. This method provides a simplified installation process through Helm charts.

**Use Cases:**
- Teams familiar with Helm package management
- Environments where Helm is already in use
- Simplified deployment and upgrade workflows

**Key Characteristics:**
- Uses Helm charts from Percona's Helm repository
- Supports easy customization via `--set` parameters
- Simplifies version management and upgrades
- Requires Helm v3 installed

**Documentation:** See [Helm installation guide](../docs/helm.md) for detailed steps.

---

### 3. OpenShift Installation

**Description:** Specialized installation methods for Red Hat OpenShift platform. The Percona PostgreSQL Operator is a Red Hat Certified Operator, ensuring full compatibility with OpenShift lifecycle management.

**Installation Sub-methods:**

#### 3.1 Operator Lifecycle Manager (OLM)
- **Description:** Install via OpenShift's OperatorHub web interface using the Operator Lifecycle Manager
- **Use Cases:** OpenShift environments where GUI-based installation is preferred
- **Key Characteristics:**
  - Web-based installation through OperatorHub
  - Integrated with OpenShift's operator management system
  - Supports version selection and namespace configuration through UI

#### 3.2 Command-Line Interface (CLI)
- **Description:** Install using `oc` (OpenShift CLI) commands, similar to kubectl but optimized for OpenShift
- **Use Cases:** OpenShift environments where CLI-based installation is preferred
- **Key Characteristics:**
  - Uses `oc` command instead of `kubectl`
  - Follows similar pattern to standard kubectl installation
  - Includes OpenShift-specific configurations (e.g., Security Context Constraints)

**Documentation:** See [OpenShift installation guide](../docs/openshift.md) for detailed steps.

---

### 4. Percona Everest Installation

**Description:** Install and manage Percona PostgreSQL clusters through Percona Everest, a cloud-native database platform that provides automated database operations.

**Use Cases:**
- Organizations looking for a complete database platform solution
- Teams that want simplified database lifecycle management
- Environments requiring automated day-one and day-two operations

**Key Characteristics:**
- Provides both API and Web GUI for database management
- Automates deployment, scaling, updates, backups, and monitoring
- Simplifies database operations with minimal manual intervention
- Suitable for teams wanting to reduce database administration overhead

**Documentation:** See [Everest installation guide](../docs/everest.md) for more information.

---

### 5. Custom Installation

**Description:** Install the Percona PostgreSQL Operator with customized configuration parameters. This method allows you to modify default settings during installation.

**Installation Sub-methods:**

#### 5.1 Custom Installation with kubectl
- **Description:** Clone the operator repository, modify configuration files, and apply custom manifests
- **Use Cases:** When you need to customize operator or cluster configurations before deployment
- **Key Characteristics:**
  - Requires cloning the operator repository
  - Allows editing of YAML files before applying
  - Full control over all configuration parameters

#### 5.2 Custom Installation with Helm
- **Description:** Use Helm with custom `--set` parameters to override default values
- **Use Cases:** When you need quick customization without modifying files
- **Key Characteristics:**
  - Pass custom values via command-line flags
  - No need to clone or modify repository files
  - Supports all Custom Resource options as Helm values

**Documentation:** See [Custom installation guide](../docs/custom-install.md) for detailed steps.

---

### 6. Cluster-Wide Installation

**Description:** Deploy the operator in cluster-wide mode, allowing a single operator instance to manage PostgreSQL clusters across multiple namespaces.

**Use Cases:**
- Multi-tenant environments
- Organizations managing multiple namespaces
- Scenarios where centralized operator management is preferred

**Key Characteristics:**
- One operator instance can watch multiple namespaces
- Uses special cluster-wide configuration files (e.g., `cw-bundle.yaml`)
- Requires configuration of `WATCH_NAMESPACE` environment variable
- Alternative to namespace-scoped deployment (default)

**Documentation:** See [Cluster-wide deployment guide](../docs/cluster-wide.md) for detailed configuration.

---

## Choosing the Right Method

| Method | Best For | Complexity | Platform Support |
|--------|----------|------------|------------------|
| **kubectl** | Standard Kubernetes, full control | Medium | All Kubernetes distributions |
| **Helm** | Helm-based workflows, simplified management | Low | All Kubernetes distributions |
| **OpenShift (OLM)** | OpenShift GUI users | Low | Red Hat OpenShift only |
| **OpenShift (CLI)** | OpenShift CLI users | Medium | Red Hat OpenShift only |
| **Everest** | Complete platform solution | Low | Kubernetes with Everest |
| **Custom** | Specific configuration needs | Medium-High | Depends on base method |
| **Cluster-Wide** | Multi-namespace management | Medium | All Kubernetes distributions |

## Prerequisites

Regardless of the installation method chosen, you typically need:

- A Kubernetes cluster (or OpenShift for OpenShift methods)
- Appropriate cluster access permissions
- kubectl or oc CLI tool installed
- For Helm: Helm v3 installed
- For Everest: Percona Everest platform installed

## Next Steps

After choosing an installation method:

1. Review the detailed installation guide for your chosen method
2. Ensure all prerequisites are met
3. Follow the step-by-step installation instructions
4. Verify the operator installation
5. Deploy your first PostgreSQL cluster

For detailed installation steps, refer to the specific documentation for each method listed above.


