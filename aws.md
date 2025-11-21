# Domain Setup for t2s-services.com

## Overview
This guide explains how to configure Route53, create a hosted zone, and map subdomains for ArgoCD, Grafana, and the Express Web App deployed on AWS EKS.

---

## 1. Create a Hosted Zone in Route53

To create a public hosted zone for your domain:

```bash
aws route53 create-hosted-zone   --name t2s-services.com   --caller-reference "$(date)"
```

This returns:
- Hosted Zone ID  
- Name Servers (NS records)

---

## 2. Add Nameservers to Your Domain Registrar

Log into your domain provider (GoDaddy, Google Domains, Namecheap, etc.) and update the domain's Nameserver section with the four AWS nameservers returned in step 1.

Propagation can take anywhere from 5 minutes to 24 hours.

---

## 3. Verify Hosted Zone Exists

```bash
aws route53 list-hosted-zones-by-name --dns-name t2s-services.com
```

You should see your hosted zone listed.

---

## 4. Retrieve Your ALB Ingress Hostname

Your applications run behind an AWS Load Balancer created by Kubernetes Ingress.

Retrieve the hostname:

```bash
kubectl get ingress -A
```

Copy the ALB DNS hostname (for example: `k8s-default-alb-12345678.us-east-1.elb.amazonaws.com`).

---

## 5. Create DNS Records for Subdomains

Replace:
- `<ALB_HOSTNAME>` with the real ALB hostname  
- `<HOSTED_ZONE_ID>` with your hosted zone ID  

### ArgoCD  
```bash
aws route53 change-resource-record-sets   --hosted-zone-id <HOSTED_ZONE_ID>   --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "argocd.t2s-services.com",
        "Type": "CNAME",
        "TTL": 300,
        "ResourceRecords": [{ "Value": "<ALB_HOSTNAME>" }]
      }
    }]
  }'
```

### Grafana  
```bash
aws route53 change-resource-record-sets   --hosted-zone-id <HOSTED_ZONE_ID>   --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "grafana.t2s-services.com",
        "Type": "CNAME",
        "TTL": 300,
        "ResourceRecords": [{ "Value": "<ALB_HOSTNAME>" }]
      }
    }]
  }'
```

### Express Web App  
```bash
aws route53 change-resource-record-sets   --hosted-zone-id <HOSTED_ZONE_ID>   --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "app.t2s-services.com",
        "Type": "CNAME",
        "TTL": 300,
        "ResourceRecords": [{ "Value": "<ALB_HOSTNAME>" }]
      }
    }]
  }'
```

---

## 6. Optional: Issue SSL Certificates (ACM)

Request wildcard + subdomain certificates:

```bash
aws acm request-certificate   --domain-name "t2s-services.com"   --subject-alternative-names "app.t2s-services.com" "grafana.t2s-services.com" "argocd.t2s-services.com"   --validation-method DNS
```

Add validation CNAME records when prompted.

---

## 7. Test All Endpoints

After DNS propagates, validate:

- https://argocd.t2s-services.com  
- https://grafana.t2s-services.com  
- https://app.t2s-services.com  

All endpoints should resolve to your EKS ALB ingress.

---

## Summary

You have successfully:
- Created a Route53 hosted zone  
- Mapped subdomains for ArgoCD, Grafana, and the Express Web App  
- Connected DNS to your Kubernetes ingress  
- Prepared SSL for secure enterprise access  

This completes the domain integration with your EKS platform.
