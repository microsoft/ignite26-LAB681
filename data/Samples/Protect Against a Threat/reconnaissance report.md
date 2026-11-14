# Reconnaissance Results

## Summary

Within the accessible Azure subscriptions and visible Entra tenant, the collection found 0 cloud-hosted candidate resources attributable to ManageEngine DesktopCentral/Endpoint Central CVE-2022-47966, Citrix NetScaler/ADC VPX CVE-2023-4966, or Adobe ColdFusion CVE-2023-29300/CVE-2023-38203. The supporting searches covered ARM-visible resources, Defender for Cloud software inventory, ARM deployment metadata, and visible Entra application and service principal display names. Candidate-specific public IP, FQDN, load balancer, application gateway, NAT, backend, effective NSG, attached-identity enrichment, and reachability checks were not applicable because no candidates were found.

## Scope

Question: Using the supplied Storm-0501 threat-intelligence context, identify whether accessible Azure subscriptions or the visible Entra tenant contain cloud-hosted candidates for the report initial-access exposures: ManageEngine DesktopCentral/Endpoint Central CVE-2022-47966, Citrix NetScaler/ADC VPX CVE-2023-4966, and Adobe ColdFusion CVE-2023-29300 or CVE-2023-38203.

Scope covered accessible Azure subscriptions `30972bee-bf21-5264-a6a8-c2beb04460c1` and `a3ca96be-f3b4-5a4e-a13b-384be2c97d2c`, plus visible Entra tenant `b4792fa2-2d52-51f5-8b60-8b88f2432262`. Threat context from [Threat intelligence report](shared/vulnerability_report.md) was used only to define the product and CVE search terms. Collection began at `2026-11-09T21:08:36.457840+00:00`, Entra display-name search ran at `2026-11-09T21:13:49.875988+00:00`, and the batch was finalized at `2026-11-09T21:14:30.791776+00:00`.

## Findings

| Search area | Observed result | Meaning for the question | Evidence |
| --- | --- | --- | --- |
| Candidate records | 0 fingerprint records and `candidate_count` 0. | No cloud-hosted candidate resource was recorded for the requested products or CVEs in this batch. | [Fingerprint records](shared/recon-evidence/evidence-fingerprint-records.md); [Collection summary](shared/recon-evidence/evidence-collection-summary.md) |
| ARM-visible resources | 0 results from Resource Graph target-term search across resource names, types, tags, SKU, plan, identity, and properties. | No ARM-visible resource matched ManageEngine, DesktopCentral, Endpoint Central, Zoho, Citrix, NetScaler, ADC VPX, ColdFusion, cfusion, or the listed CVEs. | [ARM resource target search](shared/recon-evidence/evidence-arm-resource-target-search.md) |
| Defender for Cloud software inventory | 1,058 software records across 13 hosts were visible; 0 matched the target product or CVE terms. | Defender software inventory did not identify installed software attributable to the requested products or CVEs. | [Defender software inventory counts](shared/recon-evidence/evidence-defender-software-inventory-counts.md); [Defender software target search](shared/recon-evidence/evidence-defender-software-target-search.md) |
| SecurityResources | 0 results from SecurityResources target-term search. | SecurityResources records did not contain visible matches for the requested product or CVE terms. | [SecurityResources target search](shared/recon-evidence/evidence-securityresources-target-search.md) |
| ARM deployment metadata | 177 subscription deployments and 876 resource-group deployments across 73 resource groups were scanned; `matches` was empty and `errors` was empty. | Deployment metadata did not identify deployments attributable to the requested products or CVEs. | [ARM deployment metadata search](shared/recon-evidence/evidence-arm-deployment-metadata-search.md) |
| Entra display names | Visible applications and service principals returned 0 results for Citrix, NetScaler, ADC, VPX, ManageEngine, DesktopCentral, Endpoint Central, EndpointCentral, Zoho, ColdFusion, and cfusion. | Visible Entra application and service principal names did not identify context for the requested products. | [Entra display name target search](shared/recon-evidence/evidence-entra-displayname-search.md) |

Resource coverage for ARM-visible inventory used the following nonzero counts.

| Resource type | Candidates searched |
| --- | ---: |
| Virtual machines | 14 |
| Virtual machine scale sets | 12 |
| VM extensions | 56 |
| Container apps | 4 |
| Container instances | 2 |
| App Service sites | 23 |
| AKS clusters | 13 |
| Container registries | 10 |
| Public IP addresses | 53 |
| Network interfaces | 21 |
| Load balancers | 14 |
| Network security groups | 30 |

Evidence: [Resource type counts](shared/recon-evidence/evidence-resource-type-counts.md).

## Coverage and limitations

- Kubernetes pod images inside AKS clusters and ACR repository contents were not enumerated.
- Full ARM deployment templates were not exported, and pruned deployment history is unknown.
- Defender software coverage is limited to Microsoft.Security/softwareInventories visible through Azure Resource Graph; software not inventoried by Defender for Cloud or Microsoft Defender for Endpoint, or hidden in guest data plane, remains unknown.
- No guest data-plane inspection or workload execution was performed.
- Because no candidate workload resources were found, candidate-specific network exposure enrichment and reachability checks were not performed.

## Evidence files

| File | What it contains | Supports |
| --- | --- | --- |
| [Fingerprint records](shared/recon-evidence/evidence-fingerprint-records.md) | Empty JSONL-derived candidate record array from `shared/recon/fingerprint.jsonl`. | Zero candidate records for this batch. |
| [Collection summary](shared/recon-evidence/evidence-collection-summary.md) | Batch question, candidate count, collection times, subscriptions, tenant, and coverage limits. | Summary, scope, and coverage statements. |
| [Resource type counts](shared/recon-evidence/evidence-resource-type-counts.md) | Resource Graph query and counts for ARM-visible resource types searched. | Resource coverage counts. |
| [Defender software inventory counts](shared/recon-evidence/evidence-defender-software-inventory-counts.md) | Defender for Cloud software inventory record and host counts. | Software inventory coverage. |
| [Defender software target search](shared/recon-evidence/evidence-defender-software-target-search.md) | Defender software inventory target-term query and empty result set. | No matching software inventory records. |
| [ARM resource target search](shared/recon-evidence/evidence-arm-resource-target-search.md) | Resource Graph target-term query and empty result set. | No matching ARM-visible resources. |
| [SecurityResources target search](shared/recon-evidence/evidence-securityresources-target-search.md) | SecurityResources target-term query and empty result set. | No matching SecurityResources records. |
| [ARM deployment metadata search](shared/recon-evidence/evidence-arm-deployment-metadata-search.md) | Deployment scan counts, subscriptions, errors, and empty matches. | No matching deployment metadata. |
| [Entra display name target search](shared/recon-evidence/evidence-entra-displayname-search.md) | Microsoft Graph display-name searches for applications and service principals. | No matching visible Entra application or service principal names. |
