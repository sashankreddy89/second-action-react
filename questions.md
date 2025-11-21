1. I've been exploring the infrastructure while following the KT videos and noticed something this about our security setup:
- There are no SG rules in zeus-staging-lb for IP whitelisting, but when I tried accessing from my personal laptop, I couldn't get in
- However, I can see these CIDR blocks being configured in k8s-base-manifest.yaml:
```
# GE IPs & GE Zscaler public IPs (https://internet.ge.com/docs/zscaler-public-ip-space/) & GitLab Runner IP addresses
    TF_VAR_https_cidr_blocks:'["10.0.0.0/8","165.156.39.0/26","165.156.40.0/26","165.156.34.0/24","165.156.37.0/24","194.9.244.0/26","194.9.245.0/26","165.156.29.64/26","165.156.28.64/26","165.156.31.64/26","54.156.136.49/32","3.217.220.189/32","172.30.0.0/22",{{ env["VPC_CIDR_BLOCK"] }},{{ env["NAT_GATEWAY_CIDR"] }},{{ env["PRISMA_PROXIES"] }} ] '
```
So help me understand - **are we handling access control at the ingress controller level instead of ALB security groups?** Where exactly are these CIDR blocks being applied, and how does this protect our environments? I don't have access to look inside the PDI image to trace this further.

2. It was mentioned that we need to configure proxy settings at the browser level to access Zeus environments. **Why were we doing that previously, and why is it working now without those settings?** I noticed the environments aren't accessible if I use the shortcut configured with that proxy.

---

3. I'm trying to understand our multi-site architecture since the org is moving toward this. I see zeus-qa-site-a and site-b are both in the same subnet. **Could you explain what 'multi-site' means in our context?** From what I know, multi-site usually means deploying across different AZs or regions for HA and resilience. What's our approach here, and how does it achieve the resilience we're aiming for?

4. I noticed the **loadbalancer-cp VPC is different from the jump box VPC, and they're peered.** What's the reasoning behind this separation? Is it for security isolation, or are there other architectural benefits?

---

5. Help me understand the day-to-day operations:
- **Are the config files within the jumpbox updated automatically, or do we manually fetch them from S3?**
- **Which environments run 24/7?** I know staging is always up, but what about others?
- Since office hours differ between India and USA, **how do we decide when to destroy/provision environments?**

6. **I need help understanding how the base PDI is being used and how the infrastructure creation flows.** I can see Terraform variables and manifests, but I'm missing the connection between PDI execution and actual infra provisioning. Could you walk me through this process?

7. When we need to provision additional infrastructure like load balancers or other resources, **do we strictly use Terraform/IaC, or do we sometimes provision directly from AWS console?** What's the standard practice?

---

8. I noticed **the DNS for accessing UI changed to using subdomains like "service" and "admin."** What drove this change?
- **Which DNS provider are we using - Route 53?**
- **How do we create and manage TLS certificates?** Where are they used across the infrastructure?

9. **How do we decide how many VPCs are needed?** Looking at the derms-pte GitHub page, it seems like we might have around 12 environments. Do we have a VPC per environment, or is there a different strategy?

---

10. It was said in the KT video that developers will be creating the helm charts.
- **Whose responsibility is it to create images for all the modules** - developers, DevOps, or both working together?
- **Who creates the helm charts?**

11. **Are the runners always up and running, or do they get destroyed with environments?**

---

12. On-Premises Deployment Package
- **Who's responsible for creating on-prem deployment packages?**
- **What exactly does it contain, and how is it created?**

13. When we do deployments (on-prem or cloud), **what assumptions do we make about customer infrastructure?**
- Do we assume they have a DevOps team managing K8s clusters and HA?
- Do all customers need HA setup?
- **How does cloud deployment differ from on-prem on the customer side?**
- **If a customer wants cloud on Azure/GCP instead of AWS, how do we handle that?**

---

14. Since the Foundation platform provides MinIO, ArgoCD, Prometheus, etc., **should DERMS modules integrate directly using their SDKs/APIs (like MinIO SDK), or will the foundation team provide abstraction layers or integration guidelines?**

---

15. Team & Account Structure:
- **Does each team have a separate AWS account?**
- **How many DevOps engineers are on the DERMS team?**

**16. How does the organization decide which tools to use** - like GitLab vs GitHub Actions, or RKE2 vs OpenShift? **Do we prefer open-source tools?** I'm curious about the evaluation criteria.

**17. Any tips or resources for understanding security group rules** for EC2 and other resources? I want to get better at reading and understanding these configurations.

---

I'm trying to understand the workflow and responsibilities between DevOps and developers. 
In one of the KT videos, it was mentioned that developers create helm charts and Docker images, 
and then we refine them. But I want to understand:

- What does a typical day look like for the DevOps team here?
- How much DevOps knowledge do our developers typically have?
- When developers create helm charts or Docker images, at what point do we step in? 
  Do we review and refine, or do we guide them through the process?
- What's the distinction between what the foundation team provides (PDIs/images for DevOps tools) 
  and what our DERMS DevOps team is responsible for?
