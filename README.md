
# Crossplane: Transform Your Kubernetes Cluster into a Universal Control Plane

Crossplane is a powerful open-source extension for Kubernetes that empowers users to transform their Kubernetes clusters into universal control planes. With Crossplane, managing resources becomes seamless as it provides a standardized interface through Kubernetes APIs.

## Key Benefits of Crossplane

- **Custom APIs and Abstractions:** Crossplane enables platform teams to create custom APIs and abstractions using familiar Kubernetes features such as policies, namespaces, and role-based access controls. This means that non-Kubernetes resources can be easily managed alongside Kubernetes resources within a unified environment.

- **Universal Control Plane:** Crossplane extends the capabilities of the Kubernetes control plane to become a universal control plane. This means it can effectively check the existence of resources, report any inconsistencies between the intended and actual states, and take corrective actions, regardless of the resource's location.

## Installation Guide

To install Crossplane in your Kubernetes cluster, follow these steps:

1. Ensure you have an actively supported version of Kubernetes and Helm version 3.2.0 or later.

2. Add the Crossplane Helm repository:
   ```
   helm repo add crossplane-stable https://charts.crossplane.io/stable
   ```

3. Update the local Helm chart cache:
   ```
   helm repo update
   ```

4. Install the Crossplane Helm chart:
   ```
   helm install crossplane --namespace crossplane-system --create-namespace crossplane-stable/crossplane
   ```

5. Check the installed Crossplane pods:
   ```
   kubectl get pods -n crossplane-system
   ```

   Make sure the `crossplane` and `crossplane-rbac-manager` pods are running.

Crossplane creates two deployments in the `crossplane-system` namespace : `crossplane` and `crossplane-rbac-manager`. 

The `crossplane` deployment starts with the `crossplane-init` container, which installs the Crossplane Custom Resource Definitions into the Kubernetes cluster. After the `crossplane-init` container finishes, the `crossplane` pod manages two Kubernetes controllers: 
- The `Package Manager controller` is responsible for installing provider and configuration packages, while the Composition controller installs and manages Crossplane Composite Resource Definitions, Compositions, and Claims.
- The `crossplane-rbac-manager` deployment creates and manages Kubernetes ClusterRoles for the installed Crossplane Providers and their Custom Resource Definitions.


In the image below, you can observe the resources that are installed in a Kubernetes cluster when Crossplane is installed.
For more detailed installation instructions and additional configuration options, refer to the [Crossplane documentation](https://docs.crossplane.io/v1.12/software/install/).

![Crossplane](/resources/install_crossplane.png)

## Providers in Crossplane

Crossplane leverages providers to enable the provisioning of infrastructure on external services. These providers create new Kubernetes APIs and establish mappings between these APIs and the corresponding external APIs.

Supported providers include:

- AWS
- Azure
- GCP
- Kubernetes

By installing providers, you can easily provision and manage infrastructure on various external services within your Kubernetes cluster.

To install a provider, you can use a Crossplane Provider object and specify the package location. For example, to install the AWS Community Provider:

```yaml
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-aws
spec:
  package: xpkg.upbound.io/crossplane-contrib/provider-aws:v0.39.0
```

In the image below, you can observe the resources that are installed in a Kubernetes cluster when a provider is installed.
For more information on installing providers, refer to the [Crossplane documentation](https://docs.crossplane.io/v1.12/concepts/providers/).


![Provider](/resources/install_provider.png)

## Provision Infrastructure

- To utilize **Composite Resources (XRs)** in Crossplane, the initial step involves configuring Crossplane with the desired **Composite Resource Definition (XRD)** and **Composition** resources. The XRD defines the type and schema of the Composite Resource (XR), while the Composition determines its behavior during creation.

- Configuring the XRD entails specifying the structure of the XR using an OpenAPI "structural schema." It outlines the required fields and parameters for the XR.

- The Composition governs the actions performed when an XR is created, updated, or deleted. It establishes a connection between the XR and a set of Managed Resources. Multiple Compositions can be employed for each XRD, allowing for distinct configurations in various environments.

- The Composition specifies the Managed Resources that should be created, updated, or deleted when operating on an XR. It facilitates the mapping of XR fields to Managed Resource fields.

- **Claims** serve as the means to provision and manage XRs. They act as lightweight proxy resources for XR management and provide the interface for application operators. Although similar to XRs, claims are namespaced and have a distinct kind.

- XRs can also compose other XRs, enabling nested layers of abstraction. While XRs cannot directly compose arbitrary Kubernetes resources, this can be accomplished using the Kubernetes and Helm providers.

In conclusion, the combination of XRDs, Compositions, and Claims empowers the provisioning and management of Composite Resources in Crossplane, offering a flexible and customizable solution for infrastructure management.

![Crossplane Provisioning](/resources/provision_infrastrcture.png)

For comprehensive instructions and illustrative examples, please refer to the [Crossplane documentation](https://docs.crossplane.io/v1.12/concepts/composition/).