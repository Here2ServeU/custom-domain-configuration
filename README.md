# Custom Domain Configuration  
### AWS • Azure • Google Cloud • Oracle Cloud (OCI)

This repository provides multi-cloud documentation and examples for configuring custom domains across AWS, Azure, Google Cloud, and OCI. Each cloud has its own folder containing DNS setup guides, CLI steps, and diagrams.

---

## Repository Structure

```
custom-domain-configuration/
├── aws/
│   └── route53-setup.md
├── azure/
│   └── azure-dns-setup.md
├── gcp/
│   └── cloud-dns-setup.md
├── oci/
│   └── oci-dns-setup.md
└── README.md
```

---

## Architecture Overview (DNS + Cloud)

```
                               End Users
                                   │
                                   ▼
                        ┌──────────────────────┐
                        │ Multi‑Cloud DNS Layer │
                        │  AWS / Azure / GCP /  │
                        │        OCI            │
                        └──────────────────────┘
                                   │
        ┌──────────────────────────┼──────────────────────────┐
        ▼                          ▼                          ▼
 ┌───────────────┐        ┌────────────────┐         ┌────────────────┐
 │ AWS Route53   │        │ Azure DNS      │         │ Google Cloud DNS│
 │ + HTTPS Certs │        │ + KeyVault     │         │ + Managed Certs │
 └───────────────┘        └────────────────┘         └────────────────┘
        │                          │                          │
        ▼                          ▼                          ▼
   ALB / API GW               AppGateway/AKS           GKE/CloudRun LB
        │                          │                          │
        ▼                          ▼                          ▼
   Applications               Microservices              Serverless Apps
```

---

## Supported Cloud Providers

| Cloud | DNS Service | Certificate Mgmt | Kubernetes Support | Serverless Support |
|-------|-------------|------------------|--------------------|---------------------|
| **AWS** | Route 53 | ACM | EKS + ALB Ingress | API Gateway, CloudFront |
| **Azure** | Azure DNS | KeyVault Certs | AKS + AGIC | Azure Functions |
| **GCP** | Cloud DNS | Google Managed Certs | GKE Ingress | Cloud Run |
| **OCI** | OCI DNS | OCI Certificate Mgmt | OKE LB | OCI API Gateway |

---

## Quick Start Instructions

Install the required CLI tools:

```
aws --version
az version
gcloud version
oci --version
```

Clone the repo:

```
git clone https://github.com/<your-org>/custom-domain-configuration.git
```

---

## Author

Emmanuel Naweji  
Cloud • DevOps • SRE • FinOps • AI Engineer
