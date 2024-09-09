# XC vK8s
Virtual Kubernetes (vK8s) in F5 Distributed Cloud allows users to run Kubernetes workloads across multiple cloud environments without managing the underlying Kubernetes control plane. It offers seamless integration with F5’s Distributed Cloud Services, enabling centralized management, scaling, and security for Kubernetes applications across hybrid and multi-cloud infrastructures.

Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vK8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

* RE (Regional Edge deployment - no infrastructure on-prem - more restrictions)
* RE + CE (Regional Edge and Customer Edge deployment - mixed infrastructure)
* CE (Customer Edge deployment - infrastructure required - less restrictions)

This walkthrough is designed to progress through the setup in that order. Start with the RE deployment and then build on your knowledge from there. 
