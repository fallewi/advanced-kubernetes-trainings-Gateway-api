# Kubernetes Gateway API Training Repository

![Kubernetes](https://img.shields.io/badge/Kubernetes-Gateway_API-326CE5?logo=kubernetes&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-success)
![Level](https://img.shields.io/badge/Level-Beginner_to_Advanced-blue)

## Overview

Welcome to the **Kubernetes Gateway API Training Repository**.

This repository provides a comprehensive collection of practical examples, hands-on exercises, and real-world scenarios designed to help engineers master the **Kubernetes Gateway API** ecosystem, from fundamental concepts to advanced production-grade use cases.

The objective is to offer a structured learning path for Platform Engineers, DevOps Engineers, Cloud Engineers, Kubernetes Administrators, and Solution Architects who want to understand and implement modern Kubernetes traffic management patterns using Gateway API.

---

## About the Author

This repository is maintained and provided by:

**Fall Lewis**  
*DevOps & Cloud Expert*

With extensive experience in Cloud Native technologies, Kubernetes platforms, Infrastructure as Code, GitOps, and Platform Engineering, Fall Lewis created this repository to help professionals accelerate their adoption of Gateway API and modern Kubernetes networking practices.

---

## What is Gateway API?

Gateway API is the next-generation Kubernetes networking API designed to evolve beyond traditional Ingress resources.

It provides:

- Improved role-oriented design
- Enhanced extensibility
- Multi-team ownership capabilities
- Advanced traffic routing
- Cross-namespace communication
- Progressive delivery support
- Better integration with service meshes
- Standardized networking resources

Official documentation:

- https://gateway-api.sigs.k8s.io

Hands-On Labs

Each section includes practical labs that enable learners to:

- Deploy resources
- Observe traffic flows
- Troubleshoot configurations
- Simulate production scenarios
- Validate expected behavior

Install Gateway APi

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.3.0/standard-install.yaml
helm install my-nginx-gateway-fabric oci://ghcr.io/nginx/charts/nginx-gateway-fabric --version 2.6.5
kubectl get gatewayclass
```

---

# Advanced Topics Covered

This repository goes beyond basic tutorials and explores:

- Gateway API Filters
- Backend Traffic Policies
- Gateway API Extensions
- Multi-Cluster Gateway API
- Gateway API with Cilium
- Gateway API with Istio
- External DNS Integration
- Cert-Manager Integration
- Security Hardening
- Production Troubleshooting
- Platform Engineering Patterns

---

# Target Audience

This repository is designed for:

- Kubernetes Administrators
- DevOps Engineers
- Platform Engineers
- Cloud Engineers
- Site Reliability Engineers (SRE)
- Solution Architects
- Cloud Native Enthusiasts

---

# Prerequisites

Before starting, you should have:

- Basic Kubernetes knowledge
- kubectl installed
- Access to a Kubernetes cluster
- Familiarity with networking concepts
- Understanding of Services and Ingress resources

Recommended Kubernetes version:

```text
v1.29+
```

---

# Recommended Gateway API Implementations

The examples can be adapted for:

- Cilium
- Istio
- Kong
- Envoy Gateway
- NGINX Gateway Fabric
- HAProxy Kubernetes Gateway
- Traefik

---

# Contributing

Contributions are welcome.

You can contribute by:

- Adding new Gateway API examples
- Improving documentation
- Reporting issues
- Proposing production use cases
- Sharing troubleshooting scenarios

Please open an issue or submit a pull request.

---

# Goals of This Repository

By completing this training repository, learners will be able to:

- Understand Gateway API architecture
- Design production-ready traffic management solutions
- Implement advanced routing strategies
- Secure Kubernetes ingress traffic
- Build scalable multi-tenant platforms
- Integrate Gateway API with modern cloud-native ecosystems

---

# License

This repository is provided for educational and professional training purposes.

Please refer to the LICENSE file for details.

---

## Star the Repository

If this repository helps you learn Gateway API, consider giving it a ⭐ and sharing it with the Kubernetes community.

---

**Maintained by Fall Lewis — DevOps & Cloud Expert**
