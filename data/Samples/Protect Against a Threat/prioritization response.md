# Posture Assessment - Threat Intelligence Report: Actor Profile: Storm-0501

Report date: 2026-11-09

### Executive Summary

**The organization is significantly exposed to exposures associated with Storm-0501.**

Microsoft Defender signals confirm several technique-level conditions that align to the Storm-0501 exposure model: internet-reachable CVE-bearing VMs, a context-listed CVE on contoso-app-srv-26, weak MFA on a critical-resource IAM user, broad storage exposure and shared-key access, exposed Key Vault and database control gaps, and internet-exposed app/function authentication gaps. No named-product exposure to ManageEngine, Citrix NetScaler, or ColdFusion was confirmed; that limits product-specific claims, not the preventive priorities.

### Impact

* Internet-reachable VM, web, function, storage, database and Kubernetes management surfaces increase initial-access and follow-on abuse opportunity.
* Shared keys, missing MFA, weak database authentication, vault exposure and limited recovery controls increase data-access and impact risk after credential or service compromise.

## Overall risk prioritization

> Priorities are ranked by the potential impact on critical assets × the likelihood of exploitation, adjusted by each exposure's relevance to the analyzed threat.

| Priority | Risk | Likelihood | Potential Impact | Critical Assets | Why Prioritized | TI Relevance | Recommendations |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 🟠 #1 | Internet-reachable CVE-bearing Azure VMs expose the clearest initial-access path | High | High - Internet-exposed CVE-bearing VMs are selected remediation targets | 9 virtual machines; sample: [contoso-sql-28 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e2599166-bfbd-5e59-89a4-6b24591e2d56), [contoso-app-srv-26 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=efad77fa-a939-5112-b08d-3de579972a8c), [contoso-app-srv-29 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=525e972c-97d6-5b5e-ac19-0f844d0638f9) | Ranks first because all selected VMs are directly internet exposed and CVE-bearing; eight have exploit-available CVEs and contoso-app-srv-26 matches CVE-2023-36874 evidence. | Direct - Reduces public service and management exposure used for initial access. | • All network ports should be restricted on network security groups associated to your virtual machine<br>• Management ports of virtual machines should be protected with just-in-time network access control<br>• Management ports should be closed on your virtual machines |
| 🟠 #2 | Internet-exposed Windows Server 2019 VMs need OS update remediation | High | High - Patch gaps remain on internet-exposed Windows Server 2019 VMs | 2 virtual machines: [contoso-app-srv-26 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=efad77fa-a939-5112-b08d-3de579972a8c), [contoso-app-srv-22 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=9ae992c1-1e2c-5e1d-9b5d-2edba12cee57) | Ranks immediately after exposure reduction because it addresses the selected Windows Server 2019 patch gap, including the CVE-2023-36874 VM. | Direct - Reduces post-compromise privilege-escalation potential on internet-exposed Windows Server 2019 VMs. | • Update windows_server_2019 |
| 🟠 #3 | Critical-resource IAM user alexb@contoso.com lacks MFA coverage | High | High - A critical-resource IAM principal lacks selected MFA controls | 1 IAM user: [alexb@contoso.com ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=75df30ed-2ca0-557c-aeaa-2050be720225) | Ranks before broader data-service work because it is the clearest selected valid-account control gap on a high-risk principal. | Direct - Interrupts valid-account and console-password compromise paths without MFA. | • MFA should be enabled for all IAM users<br>• Ensure multi-factor authentication (MFA) is enabled for all IAM users that have a console password |
| 🔴 #4 | Four internet-exposed storage accounts have public/anonymous access risk | High | Critical - Public access can enable anonymous data access or exfiltration preconditions | 4 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe) | Ranks as the first data action because public-access recommendation edges are Critical and align directly to exfiltration. | Direct - Reduces anonymous or public data access preconditions. | • Storage account public access should be disallowed |
| 🟠 #5 | Twenty-three storage accounts allow shared-key authorization paths | High | High - Shared keys can provide broad storage access after secret theft | 23 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe) | Ranks next because shared keys enable broad data access after secret theft and many selected accounts carry sensitive-data, AI-grounding, internet-exposure, or critical-resource drivers. | Direct - Removes durable shared keys that bypass user MFA and Conditional Access. | • Storage accounts should prevent shared key access |
| 🟠 #6 | Twenty-seven storage accounts need private/network isolation | High | High - Broad storage population remains reachable without selected private/network controls | 27 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe) | Ranks after public/shared-key storage because it has the broadest data-store population: 27 of 28 observed storage accounts have selected network-isolation pairs and populated internet-exposure signals. | Direct - Constrains internet-reachable storage endpoints to approved networks. | • Storage account should use a private link connection<br>• Storage accounts should restrict network access using virtual network rules |
| 🟠 #7 | Critical Foundry/Search/Cognitive resources still allow local key authentication | Medium-high | High - Local keys remain enabled on critical AI/search resources | 6 AI/search resources; sample: [contoso-ai-02 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=0a7a65a6-f5d2-5ddc-9cb5-d8b70c79de20), [contoso-ai-01 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=22be13fe-ed5e-5d00-b13a-5a44f6ee8f25), [contoso-ai-16 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=412d2899-c73a-5fd9-9e65-cbe8e68f6433) | Ranks with data-auth work because selected resources have Critical relationship risk with internet exposure, lateral movement, and AI-grounding drivers. | Direct - Removes local keys for internet-exposed AI/search resources that can be abused after credential or secret theft. | • Microsoft Foundry resources should have key access disabled (disable local authentication) |
| 🟠 #8 | All observed Key Vaults need private/firewall isolation | Medium-high | High - All observed vaults have selected network-isolation gaps | 8 Key Vaults; sample: [contoso-kv-17 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=d074feb0-c8bd-5469-bfcc-6540b13d7b53), [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-18 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=cb79dfc1-fd41-5b6c-ab33-02783bce83f5) | Ranks below storage because the population is smaller, but every observed vault has selected network-isolation pairs and internet-exposure signals. | Medium - Limits external reachability to secrets, keys and vault access. | • Azure Key Vaults should use private link<br>• Firewall should be enabled on Key Vault |
| 🟠 #9 | Six Key Vaults lack deletion protection against destructive impact | Medium-high | High - Selected vaults lack recovery protection against deletion impact | 6 Key Vaults; sample: [contoso-kv-17 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=d074feb0-c8bd-5469-bfcc-6540b13d7b53), [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-18 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=cb79dfc1-fd41-5b6c-ab33-02783bce83f5) | Ranks after vault network isolation because it protects recovery and secret integrity if an identity is compromised. | Direct - Reduces destructive recovery sabotage. | • Key vaults should have deletion protection enabled |
| Medium-high #10 | Three Key Vaults need RBAC-mode access control | Medium | Medium-high - Selected vault subset needs stronger least-privilege enforcement | 3 Key Vaults: [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-11 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=f10b1394-eed0-5a5c-a0b2-8ac476b907f3), [contoso-ai-10 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2699b799-8ca7-5ca2-95af-2720347db7a1) | Ranks after network and deletion controls because it affects a narrower vault subset but improves least-privilege enforcement. | Medium - Constrains permission sprawl and post-compromise secret access. | • Role-Based Access Control should be used on Azure Keyvault Services (AKV) |
| 🟠 #11 | Seven internet-exposed App Service apps lack authentication | High | Medium-high - Internet-exposed apps lack selected authentication controls | 7 web apps; sample: [contoso-webapp-05 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=4d11f9ce-a710-5c69-86fc-f8a7a7ef65aa), [contoso-webapp-13 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=a61343ea-afea-55b8-8dfb-cd5da9dd4d7b), [contoso-webapp-19 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ae1d8b27-e74c-5a94-823b-4267db5a38d0) | Ranks after core VM and data controls because direct internet exposure and missing authentication are confirmed, though CVE/exploit enrichment was not established. | Direct - Blocks anonymous public web app access. | • App Service apps should have authentication enabled |
| 🟠 #12 | Five internet-exposed Function apps need restricted network access | High | Medium-high - Function endpoints remain internet-exposed without selected restrictions | 5 function apps; sample: [contoso-function-06 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=7412ab90-02f2-52cf-bbd2-b175d06a612d), [contoso-function-30 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2eddc0a2-adf7-59cf-a6f6-c682b6369ff6), [contoso-asset-33 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bb144211-60e2-5347-94a7-163d0d72fc4f) | Ranks beside web authentication because it reduces exposed serverless entry points used for public-facing app abuse. | Direct - Reduces public function endpoint reachability. | • Restricted network access should be configured on Internet exposed Function app |
| 🟠 #13 | Two Function apps lack authentication controls | High | Medium-high - Selected function apps have direct internet exposure and missing authentication controls | 2 function apps: [contoso-function-30 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2eddc0a2-adf7-59cf-a6f6-c682b6369ff6), [contoso-asset-33 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bb144211-60e2-5347-94a7-163d0d72fc4f) | Ranks with serverless network restriction because both selected functions have direct internet exposure and authentication recommendation pairs. | Direct - Blocks anonymous function invocation and function app access. | • Authentication should be enabled on Azure Functions<br>• Function apps should have authentication enabled |
| Medium-high #14 | Two web apps should enforce HTTPS-only access | Medium-high | Medium - HTTPS-only enforcement protects credentials and sessions | 2 web apps: [contoso-webapp-04 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=06bdb13a-4add-5666-85fe-d36ff23eeb88), [contoso-asset-08 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2e3a5845-0fe9-58cf-a488-8efed51da23e) | Ranks after missing-auth controls because it protects credentials and sessions but does not by itself remove anonymous access. | Direct - Prevents cleartext credential or session exposure. | • Web Application should only be accessible over HTTPS |
| 🟠 #15 | Four Cosmos DB accounts need network isolation | High | High - Selected Cosmos DB accounts have internet-exposure evidence and missing network isolation | 4 Cosmos DB accounts; sample: [contoso-cosmos-03 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ac0049ef-33c1-505f-bad9-220e5b33ba7d), [contoso-sql-09 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=6c04ae70-aec5-503d-9b7d-ace5dc8c47f2), [contoso-sql-07 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=dc1b7d34-13b8-5f42-b052-6d2301962e2a) | Ranks below broader storage and vault actions because the population is smaller, but every selected Cosmos account has internet-exposure evidence. | Direct - Constrains public database network paths and confines database access to private endpoints. | • Azure Cosmos DB accounts should have firewall rules<br>• Azure Cosmos DB should disable public network access<br>• CosmosDB accounts should use private link |
| 🟠 #16 | Three Cosmos DB accounts should use Entra-only authentication | Medium-high | High - Key/local credential paths remain on selected internet-exposed data services | 3 Cosmos DB accounts: [contoso-cosmos-03 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ac0049ef-33c1-505f-bad9-220e5b33ba7d), [contoso-sql-09 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=6c04ae70-aec5-503d-9b7d-ace5dc8c47f2), [contoso-sql-07 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=dc1b7d34-13b8-5f42-b052-6d2301962e2a) | Ranks with database controls because it reduces value of stolen keys or passwords on internet-exposed data services. | Direct - Removes key/local credential database access paths. | • Azure Cosmos DB accounts should use Azure Active Directory as the only authentication method |
| 🟠 #17 | Two SQL servers need private access and Entra-governed authentication | Medium-high | High - Selected SQL servers have exposure and weak-authentication drivers | 2 SQL servers: [contoso-sql-20 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=67561325-6ba4-5ea8-891c-a7aad0a3f394), [contoso-sql-14 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e917d4c7-fc82-5fe8-bbcb-07eac9712516) | Ranks after Cosmos because it affects two servers; Contoso SQL assets also had exposure and weak-authentication drivers. | Direct - Constrains SQL network access and removes legacy SQL-authentication paths. | • Private endpoint connections on Azure SQL Database should be enabled<br>• Public network access on Azure SQL Database should be disabled<br>• Azure SQL Database should have Azure Active Directory Only Authentication enabled<br>• SQL servers should have an Azure Active Directory administrator provisioned |
| Medium-high #18 | Two SQL database firewall scopes are too broad or unmanaged | Medium | Medium-high - Database-scoped firewall hygiene remains broad or unmanaged | 2 SQL databases: [master ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=12c98076-b387-5b54-904c-f269d9aac617), [master (contoso-sql-14/master) ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8296ca06-1515-5fad-ad96-c86ff3894de7) | Ranks after server-level private and authentication controls because targets are database-scoped firewall hygiene on a smaller set. | Direct - Reduces broad SQL firewall access and maintains minimal exposure. | • Server-level firewall rules should not grant excessive access for SQL Servers<br>• Server-level firewall rules should be tracked and maintained at a strict minimum on SQL Servers |
| 🟠 #19 | Two SQL user databases need long-term geo-redundant backup | Medium | High - Selected SQL user databases need stronger recovery protection | 2 SQL databases: [Contoso-Users-25 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=80959791-7db2-53e0-aaf6-b4163d77d05e), [Contoso-Users-21 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=b0448e96-eca6-5a56-9be8-b72a71cff1a5) | Ranks as recovery containment: smaller population, but directly limits destructive-impact outcomes. | Direct - Improves recoverability after deletion or ransomware impact. | • Long-term geo-redundant backup should be enabled for Azure SQL Databases |
| 🟠 #20 | Thirteen AKS clusters need restricted/private control-plane access | Medium-high | High - Selected AKS control planes need restricted/private access controls | 13 AKS clusters; sample: [contoso-aks-31 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e4d7839e-e98b-5de7-b179-0e42cb94c3f8), [contoso-aks-32 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2a442801-ed39-5d43-a3d3-7350c8787eb0), [contoso-aks-23 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bc35a52e-2f65-5cbf-95be-ca4b44ebbf25) | Ranks after directly internet-exposed VMs, apps and data because selected recommendations are High severity and broad, but graph direct-internet exposure fields were not populated for these cluster nodes. | Medium - Reduces reachable Kubernetes API management plane and public control-plane endpoint exposure. | • Kubernetes API server should be configured with restricted access<br>• Private endpoint access should be enabled for the control plane in Azure Kubernetes Service (AKS) clusters<br>• Private nodes should be configured on Azure Kubernetes Service (AKS) clusters<br>• Public endpoints should be disabled on private Azure Kubernetes Service (AKS) clusters |
| 🟠 #21 | Cloud Security subscription has owner-sprawl control-plane risk | Medium | High - Subscription owner compromise would have subscription-level blast radius | 1 subscription: [Cloud Security ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=5025793a-4934-5b20-add3-4ad67c191c79) | Ranks after direct exposure, authentication and data actions because edge risk was Low, but owner compromise would have subscription-level blast radius. | Medium - Reduces control-plane blast radius after owner-account compromise. | • A maximum of 3 owners should be designated for subscriptions |

---

## 1 - Internet-reachable CVE-bearing Azure VMs expose the clearest initial-access path

Nine selected Azure VMs are directly internet exposed and CVE-bearing. Eight have exploit-available CVEs, and contoso-app-srv-26 matches CVE-2023-36874 evidence. Actual approved ports and application dependencies were not enumerated; Microsoft Defender recommendation pairs identify the remediation targets.

🟠 **Impact:** High - The selected cohort exposes public service and management paths on CVE-bearing VMs.

🟠 **Likelihood:** High - Direct internet exposure, CVE evidence and open recommendation-target pairs increase exploitation likelihood.

**Why Prioritized:** This ranks first because it is the clearest initial-access path in the selected evidence and directly reduces public VM and management exposure before follow-on fixes.

**TI Relevance:** Direct - Restricting inbound VM/NSG ports, closing public management ports and enforcing just-in-time access reduce public-facing service exposure used for initial access and management-plane abuse.

**Critical Assets:** 9 virtual machines; sample: [contoso-sql-28 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e2599166-bfbd-5e59-89a4-6b24591e2d56), [contoso-app-srv-26 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=efad77fa-a939-5112-b08d-3de579972a8c), [contoso-app-srv-29 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=525e972c-97d6-5b5e-ac19-0f844d0638f9)

**Attack Path:** Internet-reachable VM -> exposed service or management port -> possible initial access -> follow-on host or identity abuse

**Recommendations:**

- All network ports should be restricted on network security groups associated to your virtual machine
- Management ports of virtual machines should be protected with just-in-time network access control
- Management ports should be closed on your virtual machines

---

## 2 - Internet-exposed Windows Server 2019 VMs need OS update remediation

Two selected internet-exposed Windows Server 2019 VMs need OS update remediation, including contoso-app-srv-26 with CVE-2023-36874 evidence. The graph evidence did not expose a per-recommendation-to-CVE fix map.

🟠 **Impact:** High - Patch gaps remain on selected internet-exposed Windows Server 2019 VMs.

🟠 **Likelihood:** High - Internet exposure and same-asset CVE evidence raise the urgency of applying updates.

**Why Prioritized:** This ranks immediately after exposure reduction because it addresses the selected patch gap on internet-exposed Windows Server 2019 VMs after public-path reduction.

**TI Relevance:** Direct - Updating Windows Server 2019 reduces post-compromise privilege-escalation potential on internet-exposed Windows Server 2019 VMs, including the CVE-2023-36874 VM.

**Critical Assets:** 2 virtual machines: [contoso-app-srv-26 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=efad77fa-a939-5112-b08d-3de579972a8c), [contoso-app-srv-22 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=9ae992c1-1e2c-5e1d-9b5d-2edba12cee57)

**Attack Path:** Internet-exposed Windows Server VM -> unremediated OS gap -> possible privilege escalation -> follow-on compromise

**Recommendations:**

Update windows_server_2019

---

## 3 - Critical-resource IAM user alexb@contoso.com lacks MFA coverage

The critical-resource IAM user alexb@contoso.com lacks selected MFA coverage. Sign-in history, key age and account owner were not available in the recommendation route.

🟠 **Impact:** High - A high-risk critical-resource principal could be used if credentials are compromised.

🟠 **Likelihood:** High - Open MFA recommendation-target pairs indicate a valid-account control gap.

**Why Prioritized:** This ranks before broader data-service work because it is the clearest selected valid-account control gap on a high-risk principal.

**TI Relevance:** Direct - MFA interrupts valid-account use and console-password compromise paths without MFA.

**Critical Assets:** 1 IAM user: [alexb@contoso.com ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=75df30ed-2ca0-557c-aeaa-2050be720225)

**Attack Path:** Compromised credentials -> IAM user without MFA -> possible account access -> critical-resource abuse

**Recommendations:**

- MFA should be enabled for all IAM users
- Ensure multi-factor authentication (MFA) is enabled for all IAM users that have a console password

---

## 4 - Four internet-exposed storage accounts have public/anonymous access risk

Four selected internet-exposed storage accounts have public or anonymous access risk. The evidence verifies open Microsoft Defender recommendation-target pairs; it does not prove active anonymous reads or data sensitivity.

🔴 **Impact:** Critical - Public access can enable anonymous data access or exfiltration preconditions.

🟠 **Likelihood:** High - Public-access recommendation edges and internet-exposure signals are present on the selected cohort.

**Why Prioritized:** This is the first data action because public-access recommendation edges are Critical and align directly to exfiltration.

**TI Relevance:** Direct - Disallowing public access reduces anonymous/public data access and exfiltration preconditions.

**Critical Assets:** 4 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe)

**Attack Path:** Public storage exposure -> anonymous or public access -> possible data access -> exfiltration preconditions

**Recommendations:**

Storage account public access should be disallowed

---

## 5 - Twenty-three storage accounts allow shared-key authorization paths

Twenty-three selected storage accounts allow shared-key authorization paths. Current key consumers and application dependencies were not enumerated.

🟠 **Impact:** High - Shared keys can enable broad data access after secret theft.

🟠 **Likelihood:** High - Many selected accounts carry sensitive-data, AI-grounding, internet-exposure or critical-resource drivers.

**Why Prioritized:** This ranks after public storage access because shared keys enable broad data access after secret theft across a larger selected storage population.

**TI Relevance:** Direct - Preventing shared key access removes durable shared keys that bypass user MFA and Conditional Access.

**Critical Assets:** 23 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe)

**Attack Path:** Secret theft -> shared storage key use -> broad storage access -> possible data exposure

**Recommendations:**

Storage accounts should prevent shared key access

---

## 6 - Twenty-seven storage accounts need private/network isolation

Twenty-seven selected storage accounts need private or network isolation. Dependency mapping and private DNS readiness must be validated before enforcement.

🟠 **Impact:** High - A broad storage population remains exposed without selected network isolation controls.

🟠 **Likelihood:** High - Selected network-isolation pairs and populated internet-exposure signals are present for 27 of 28 observed storage accounts.

**Why Prioritized:** This ranks after public/shared-key storage because it covers the broadest selected data-store population while requiring dependency validation before restriction.

**TI Relevance:** Direct - Private link and virtual network rules constrain internet-reachable storage endpoints to approved networks.

**Critical Assets:** 27 storage accounts; sample: [contosostorage27 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=39fa834f-f644-5e41-90a1-c436723570b9), [contosostorage15 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8a72bb49-65fa-5a35-a636-79edf4ab276a), [contoso-asset-24 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=463eea2d-223f-537f-943b-39ad757938fe)

**Attack Path:** Internet-reachable storage endpoint -> network access without private isolation -> possible data access -> exfiltration preconditions

**Recommendations:**

- Storage account should use a private link connection
- Storage accounts should restrict network access using virtual network rules

---

## 7 - Critical Foundry/Search/Cognitive resources still allow local key authentication

Six selected Foundry, Search and Cognitive resources still allow local key authentication. Current key consumers and application owners were not identified.

🟠 **Impact:** High - Local keys remain on critical AI/search resources with relationship risk.

**Likelihood:** Medium-high - Selected resources have internet exposure, lateral movement and AI-grounding drivers, though fixed asset scoring is sparse.

**Why Prioritized:** This ranks with data-auth work because selected resources have Critical relationship risk with internet exposure, lateral movement and AI-grounding drivers.

**TI Relevance:** Direct - Disabling local authentication removes local keys for internet-exposed AI/search resources that can be abused after credential or secret theft.

**Critical Assets:** 6 AI/search resources; sample: [contoso-ai-02 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=0a7a65a6-f5d2-5ddc-9cb5-d8b70c79de20), [contoso-ai-01 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=22be13fe-ed5e-5d00-b13a-5a44f6ee8f25), [contoso-ai-16 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=412d2899-c73a-5fd9-9e65-cbe8e68f6433)

**Attack Path:** Credential or secret theft -> local key use -> AI/search resource access -> possible data or grounding abuse

**Recommendations:**

Microsoft Foundry resources should have key access disabled (disable local authentication)

---

## 8 - All observed Key Vaults need private/firewall isolation

All eight observed Key Vaults have selected private-link or firewall recommendation pairs and internet-exposure signals. Secret contents and downstream dependencies were not inspected.

🟠 **Impact:** High - Vault exposure affects access to secrets and keys.

**Likelihood:** Medium-high - Every observed vault has selected network-isolation pairs and internet-exposure signals.

**Why Prioritized:** This ranks below storage because the population is smaller, but every observed vault has selected network-isolation evidence.

**TI Relevance:** Medium - Private link and firewall controls limit external reachability to secrets, keys and vault access.

**Critical Assets:** 8 Key Vaults; sample: [contoso-kv-17 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=d074feb0-c8bd-5469-bfcc-6540b13d7b53), [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-18 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=cb79dfc1-fd41-5b6c-ab33-02783bce83f5)

**Attack Path:** External vault reachability -> possible secret/key access -> credential or service abuse

**Recommendations:**

- Azure Key Vaults should use private link
- Firewall should be enabled on Key Vault

---

## 9 - Six Key Vaults lack deletion protection against destructive impact

Six selected Key Vaults lack deletion protection. Two vaults lacked selected deletion-protection pairs; that is not proof those vaults are safe outside the selected evidence.

🟠 **Impact:** High - Missing deletion protection can undermine recovery and secret integrity after compromise.

**Likelihood:** Medium-high - Selected vaults have open deletion-protection recommendation-target pairs.

**Why Prioritized:** This ranks after vault network isolation because it protects recovery and secret integrity if an identity is compromised.

**TI Relevance:** Direct - Enabling deletion protection reduces destructive recovery sabotage.

**Critical Assets:** 6 Key Vaults; sample: [contoso-kv-17 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=d074feb0-c8bd-5469-bfcc-6540b13d7b53), [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-18 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=cb79dfc1-fd41-5b6c-ab33-02783bce83f5)

**Attack Path:** Compromised identity -> vault deletion or purge attempt -> possible recovery sabotage -> operational impact

**Recommendations:**

Key vaults should have deletion protection enabled

---

## 10 - Three Key Vaults need RBAC-mode access control

Three selected Key Vaults need RBAC-mode access control. Five observed vaults had no selected RBAC pair; non-inclusion is not proof of healthy access posture.

**Impact:** Medium-high - RBAC mode improves least-privilege enforcement on the selected vault subset.

🟡 **Likelihood:** Medium - Open recommendation-target pairs identify the selected access-control gap.

**Why Prioritized:** This ranks after network and deletion controls because it affects a narrower vault subset but improves least-privilege enforcement.

**TI Relevance:** Medium - RBAC mode constrains permission sprawl and post-compromise secret access.

**Critical Assets:** 3 Key Vaults: [contoso-kv-12 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=70c10afc-8ada-5b97-af55-4ee2618e7ad9), [contoso-kv-11 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=f10b1394-eed0-5a5c-a0b2-8ac476b907f3), [contoso-ai-10 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2699b799-8ca7-5ca2-95af-2720347db7a1)

**Attack Path:** Excessive vault permissions -> compromised identity -> possible secret access -> service or credential abuse

**Recommendations:**

Role-Based Access Control should be used on Azure Keyvault Services (AKV)

---

## 11 - Seven internet-exposed App Service apps lack authentication

Seven selected internet-exposed App Service apps lack authentication. App roles, managed identities and downstream data sensitivity were not enumerated.

**Impact:** Medium-high - Public web apps can allow anonymous access where authentication is missing.

🟠 **Likelihood:** High - Direct internet exposure and missing authentication are confirmed for the selected apps.

**Why Prioritized:** This ranks after core VM and data controls because direct internet exposure and missing authentication are confirmed, though CVE/exploit enrichment was not established.

**TI Relevance:** Direct - Enabling App Service authentication blocks anonymous public web app access.

**Critical Assets:** 7 web apps; sample: [contoso-webapp-05 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=4d11f9ce-a710-5c69-86fc-f8a7a7ef65aa), [contoso-webapp-13 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=a61343ea-afea-55b8-8dfb-cd5da9dd4d7b), [contoso-webapp-19 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ae1d8b27-e74c-5a94-823b-4267db5a38d0)

**Attack Path:** Internet-exposed web app -> missing authentication -> anonymous access -> possible application abuse

**Recommendations:**

App Service apps should have authentication enabled

---

## 12 - Five internet-exposed Function apps need restricted network access

Five selected internet-exposed Function apps need restricted network access. Webhook and external integration dependencies were not validated.

**Impact:** Medium-high - Public function endpoints remain reachable without selected network restrictions.

🟠 **Likelihood:** High - The selected functions have direct internet exposure and open recommendation-target pairs.

**Why Prioritized:** This ranks beside web authentication because it reduces exposed serverless entry points used for public-facing app abuse.

**TI Relevance:** Direct - Restricted network access reduces public function endpoint reachability.

**Critical Assets:** 5 function apps; sample: [contoso-function-06 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=7412ab90-02f2-52cf-bbd2-b175d06a612d), [contoso-function-30 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2eddc0a2-adf7-59cf-a6f6-c682b6369ff6), [contoso-asset-33 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bb144211-60e2-5347-94a7-163d0d72fc4f)

**Attack Path:** Internet-exposed function endpoint -> unrestricted inbound access -> possible endpoint abuse -> follow-on service access

**Recommendations:**

Restricted network access should be configured on Internet exposed Function app

---

## 13 - Two Function apps lack authentication controls

Two selected Function apps lack authentication controls. Business owner exceptions and integration tokens were not inspected.

**Impact:** Medium-high - Directly internet-exposed functions can be invoked without selected authentication controls.

🟠 **Likelihood:** High - Both selected functions have direct internet exposure and authentication recommendation pairs.

**Why Prioritized:** This ranks with serverless network restriction because both selected functions have direct internet exposure and authentication recommendation pairs.

**TI Relevance:** Direct - Enabling Azure Functions/App Service authentication blocks anonymous function invocation and function app access.

**Critical Assets:** 2 function apps: [contoso-function-30 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2eddc0a2-adf7-59cf-a6f6-c682b6369ff6), [contoso-asset-33 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bb144211-60e2-5347-94a7-163d0d72fc4f)

**Attack Path:** Internet-exposed function -> missing authentication -> anonymous invocation -> possible function abuse

**Recommendations:**

- Authentication should be enabled on Azure Functions
- Function apps should have authentication enabled

---

## 14 - Two web apps should enforce HTTPS-only access

Two selected web apps should enforce HTTPS-only access. The evidence does not prove that HTTP is actively used.

🟡 **Impact:** Medium - HTTPS-only enforcement protects credentials and sessions from cleartext exposure.

**Likelihood:** Medium-high - Recommendation pairs identify the selected posture gap.

**Why Prioritized:** This ranks after missing-auth controls because it protects credentials and sessions but does not by itself remove anonymous access.

**TI Relevance:** Direct - HTTPS-only access prevents cleartext credential or session exposure.

**Critical Assets:** 2 web apps: [contoso-webapp-04 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=06bdb13a-4add-5666-85fe-d36ff23eeb88), [contoso-asset-08 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2e3a5845-0fe9-58cf-a488-8efed51da23e)

**Attack Path:** Web access over HTTP -> possible cleartext session or credential exposure -> account or application abuse

**Recommendations:**

Web Application should only be accessible over HTTPS

---

## 15 - Four Cosmos DB accounts need network isolation

Four selected Cosmos DB accounts need firewall, public-network-access and private-link remediation. Application connection paths and exact data sensitivity were not verified.

🟠 **Impact:** High - Public database network paths remain on selected Cosmos DB accounts.

🟠 **Likelihood:** High - Every selected Cosmos account has internet-exposure evidence.

**Why Prioritized:** This ranks below broader storage and vault actions because the population is smaller, but every selected Cosmos account has internet-exposure evidence.

**TI Relevance:** Direct - Firewall rules, disabling public network access and private link constrain public database network paths.

**Critical Assets:** 4 Cosmos DB accounts; sample: [contoso-cosmos-03 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ac0049ef-33c1-505f-bad9-220e5b33ba7d), [contoso-sql-09 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=6c04ae70-aec5-503d-9b7d-ace5dc8c47f2), [contoso-sql-07 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=dc1b7d34-13b8-5f42-b052-6d2301962e2a)

**Attack Path:** Internet-exposed database endpoint -> public network path -> possible data-service access -> data exposure

**Recommendations:**

- Azure Cosmos DB accounts should have firewall rules
- Azure Cosmos DB should disable public network access
- CosmosDB accounts should use private link

---

## 16 - Three Cosmos DB accounts should use Entra-only authentication

Three selected Cosmos DB accounts should use Entra-only authentication. Current key consumers were not identified.

🟠 **Impact:** High - Key/local credential paths remain on selected data services.

**Likelihood:** Medium-high - The selected accounts are internet-exposed data services with open authentication recommendation-target pairs.

**Why Prioritized:** This ranks with database controls because it reduces value of stolen keys or passwords on internet-exposed data services.

**TI Relevance:** Direct - Entra-only authentication removes key/local credential database access paths.

**Critical Assets:** 3 Cosmos DB accounts: [contoso-cosmos-03 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=ac0049ef-33c1-505f-bad9-220e5b33ba7d), [contoso-sql-09 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=6c04ae70-aec5-503d-9b7d-ace5dc8c47f2), [contoso-sql-07 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=dc1b7d34-13b8-5f42-b052-6d2301962e2a)

**Attack Path:** Stolen database key or credential -> local/key authentication -> possible database access -> data exposure

**Recommendations:**

Azure Cosmos DB accounts should use Azure Active Directory as the only authentication method

---

## 17 - Two SQL servers need private access and Entra-governed authentication

Two selected SQL servers need private access and Entra-governed authentication controls. Connection-string inventory and legacy client compatibility were not validated.

🟠 **Impact:** High - Selected SQL servers have exposure and weak-authentication drivers.

**Likelihood:** Medium-high - Open recommendation-target pairs identify private access and Entra authentication gaps.

**Why Prioritized:** This ranks after Cosmos because it affects two servers, and Contoso SQL assets also had exposure and weak-authentication drivers.

**TI Relevance:** Direct - Private endpoints, disabled public network access, Entra-only authentication and Entra administrator provisioning constrain SQL network and legacy authentication paths.

**Critical Assets:** 2 SQL servers: [contoso-sql-20 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=67561325-6ba4-5ea8-891c-a7aad0a3f394), [contoso-sql-14 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e917d4c7-fc82-5fe8-bbcb-07eac9712516)

**Attack Path:** Public SQL path or legacy authentication -> possible database access -> data exposure or administration abuse

**Recommendations:**

- Private endpoint connections on Azure SQL Database should be enabled
- Public network access on Azure SQL Database should be disabled
- Azure SQL Database should have Azure Active Directory Only Authentication enabled
- SQL servers should have an Azure Active Directory administrator provisioned

---

## 18 - Two SQL database firewall scopes are too broad or unmanaged

Two selected SQL database firewall scopes are too broad or unmanaged. Recommendation targets are database nodes named master; server relationships should be validated during change planning.

**Impact:** Medium-high - Broad or unmanaged firewall scopes increase SQL exposure.

🟡 **Likelihood:** Medium - Open recommendation-target pairs identify firewall rule hygiene gaps.

**Why Prioritized:** This ranks after server-level private and authentication controls because targets are database-scoped firewall hygiene on a smaller set.

**TI Relevance:** Direct - Reviewing excessive grants and maintaining strict minimum firewall rules reduces broad SQL firewall access.

**Critical Assets:** 2 SQL databases: [master ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=12c98076-b387-5b54-904c-f269d9aac617), [master (contoso-sql-14/master) ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=8296ca06-1515-5fad-ad96-c86ff3894de7)

**Attack Path:** Broad SQL firewall scope -> network access from excessive sources -> possible database access -> data exposure

**Recommendations:**

- Server-level firewall rules should not grant excessive access for SQL Servers
- Server-level firewall rules should be tracked and maintained at a strict minimum on SQL Servers

---

## 19 - Two SQL user databases need long-term geo-redundant backup

Two selected SQL user databases need long-term geo-redundant backup. Recovery-vault-specific posture was not covered, and no Recovery Services vault graph nodes were observed.

🟠 **Impact:** High - Missing long-term geo-redundant backup can weaken recovery from destructive impact.

🟡 **Likelihood:** Medium - Recommendation-target pairs identify the selected recovery control gap.

**Why Prioritized:** This ranks as recovery containment: smaller population, but directly limits destructive-impact outcomes.

**TI Relevance:** Direct - Long-term geo-redundant backup improves recoverability after deletion or ransomware impact.

**Critical Assets:** 2 SQL databases: [Contoso-Users-25 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=80959791-7db2-53e0-aaf6-b4163d77d05e), [Contoso-Users-21 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=b0448e96-eca6-5a56-9be8-b72a71cff1a5)

**Attack Path:** Destructive database impact -> insufficient long-term backup -> limited restore options -> operational impact

**Recommendations:**

Long-term geo-redundant backup should be enabled for Azure SQL Databases

---

## 20 - Thirteen AKS clusters need restricted/private control-plane access

Thirteen selected AKS clusters need restricted/private control-plane access. Namespace sensitivity, cluster identities and ingress exposure were not expanded, and direct-internet exposure fields were not populated for these cluster nodes.

🟠 **Impact:** High - Selected AKS control planes need restricted/private access controls.

**Likelihood:** Medium-high - Selected recommendations are High severity and broad, but direct-internet exposure was not populated for these cluster nodes.

**Why Prioritized:** This ranks after directly internet-exposed VMs, apps and data because the selected AKS controls are broad, but less directly internet-exposed in the available graph evidence.

**TI Relevance:** Medium - Restricting API server access, enabling private control-plane endpoint access, configuring private nodes and disabling public endpoints reduce reachable Kubernetes management-plane abuse.

**Critical Assets:** 13 AKS clusters; sample: [contoso-aks-31 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=e4d7839e-e98b-5de7-b179-0e42cb94c3f8), [contoso-aks-32 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=2a442801-ed39-5d43-a3d3-7350c8787eb0), [contoso-aks-23 ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=bc35a52e-2f65-5cbf-95be-ca4b44ebbf25)

**Attack Path:** Reachable Kubernetes control plane -> management-plane abuse -> cluster configuration or workload impact

**Recommendations:**

- Kubernetes API server should be configured with restricted access
- Private endpoint access should be enabled for the control plane in Azure Kubernetes Service (AKS) clusters
- Private nodes should be configured on Azure Kubernetes Service (AKS) clusters
- Public endpoints should be disabled on private Azure Kubernetes Service (AKS) clusters

---

## 21 - Cloud Security subscription has owner-sprawl control-plane risk

The Cloud Security subscription has owner-sprawl control-plane risk. Individual owner identities were not returned in the selected route.

🟠 **Impact:** High - Owner compromise would have subscription-level blast radius.

🟡 **Likelihood:** Medium - Edge risk was Low, but the selected recommendation identifies excessive standing owner exposure.

**Why Prioritized:** This ranks after direct exposure, authentication and data actions because owner compromise would have subscription-level blast radius, but it is less direct than the preceding selected exposure paths.

**TI Relevance:** Medium - Reducing standing subscription Owners to three or fewer reduces control-plane blast radius after owner-account compromise.

**Critical Assets:** 1 subscription: [Cloud Security ↗](https://security.microsoft.com/cloud-resource/v2?viewid=overview&id=5025793a-4934-5b20-add3-4ad67c191c79)

**Attack Path:** Owner-account compromise -> excessive subscription ownership -> control-plane abuse -> subscription-level impact

**Recommendations:**

A maximum of 3 owners should be designated for subscriptions

---

## Coverage

Current coverage is based on exposure data from MDE, MDC, and AI workloads. Additional products will be added over time.
[View all recommendations](https://security.microsoft.com/exposure-recommendations)
