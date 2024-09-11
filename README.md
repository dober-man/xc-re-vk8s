# XC vK8s
Virtual Kubernetes (vK8s) in F5 Distributed Cloud allows users to run Kubernetes workloads across multiple cloud environments without managing the underlying Kubernetes control plane. It offers seamless integration with F5’s Distributed Cloud Services, enabling centralized management, scaling, and security for Kubernetes applications across hybrid and multi-cloud infrastructures.

Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vK8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

**RE (Regional Edge deployment - no infrastructure on-prem - RE specific restrictions)**

<img width="983" alt="image" src="https://github.com/user-attachments/assets/e4a4decf-6aff-4f6d-8a2a-cec7b91982dc">

**RE + CE (Regional Edge and Customer Edge deployment - mixed restrictions)**

<img width="1084" alt="image" src="https://github.com/user-attachments/assets/1165a25e-c1c7-447a-b1e1-3487d822f297">

**CE (Customer Edge deployment - infrastructure required - CE specific restrictions)**

<img width="1053" alt="image" src="https://github.com/user-attachments/assets/cdfacc52-a72d-4432-a2e4-1a68692147d9">


This guide is designed around the fundamentals of a Regional Edge Deployment. Start with (1-re-vk8s.md) and then build on your knowledge from there by progressing though each section.  
