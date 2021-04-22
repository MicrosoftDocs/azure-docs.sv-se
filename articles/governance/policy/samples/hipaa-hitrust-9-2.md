---
title: Information om regelefterlevnad för HIPAA HITRUST 9.2
description: Information om det inbyggda initiativet HIPAA HITRUST 9.2 Regulatory Compliance. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: b2843e569854947a83b93c0dde5529cc865721e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864387"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Information om det inbyggda initiativet HIPAA HITRUST 9.2 Regulatory Compliance

Följande artikel beskriver hur den inbyggda initiativdefinitionen Azure Policy  regelefterlevnad mappar till efterlevnadsdomäner och kontroller i HIPAA HITRUST 9.2. 
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Information om _ägarskap_ finns [i Azure Policy principdefinition](../concepts/definition-structure.md#type) [och Delat ansvar i molnet.](../../../security/fundamentals/shared-responsibility.md)

Följande mappningar är till **HIPAA HITRUST 9.2-kontrollerna.** Använd navigeringen till höger för att gå direkt till en specifik **efterlevnadsdomän.** Många av kontrollerna implementeras med en [Azure Policy](../overview.md) initiativdefinition. Om du vill granska den fullständiga **initiativdefinitionen** öppnar du Princip i Azure Portal och väljer **sidan** Definitioner.
Leta sedan reda på och välj den **inbyggda initiativdefinitionen hitrust/HIPAA-regelefterlevnad.**

Det här inbyggda initiativet distribueras som en del av [HIPAA HITRUST 9.2-skissexempel](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure Policy](../overview.md) definitioner.
> Dessa principer kan hjälpa dig [att utvärdera efterlevnaden](../how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en en-till-en-matchning eller fullständig matchning mellan en kontroll och en eller flera principer. Därför **refererar Kompatibel i** Azure Policy endast till själva principdefinitionerna. Detta säkerställer inte att du är helt kompatibel med alla krav för en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte hanteras av några Azure Policy definitioner för just nu. Därför är efterlevnad i Azure Policy endast en partiell vy över din övergripande efterlevnadsstatus. Associationerna mellan efterlevnadsdomäner, kontroller och Azure Policy för den här efterlevnadsstandarden kan ändras med tiden. Om du vill visa ändringshistoriken kan du gå [till GitHub Commit History (Genomförandehistorik för GitHub).](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)

## <a name="privilege-management"></a>Behörighetshantering

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Åtkomst till hanteringsfunktioner eller administrationskonsoler för system som är värdar för virtualiserade system begränsas till personal baserat på principen om minsta behörighet och stöds via tekniska kontroller.

**ID:** 11180.01c3System.6 – 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Privilegier godkänns och kontrolleras formellt, allokeras till användare enligt behov och händelse för händelse för deras funktionella roll (t.ex. användare eller administratör) och dokumenteras för varje systemprodukt/-element.

**ID:** 1143.01c1System.123 - 01.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanteringsportar exponerar den virtuella datorn för en hög risknivå från Internetbaserade attacker. Dessa attacker försöker råstyra autentiseringsuppgifter för att få administratörsåtkomst till datorn. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Organisationen godkänner uttryckligen åtkomst till specifika säkerhetsreleverade funktioner (distribuerade i maskinvara, programvara och inbyggd programvara) och säkerhetsreleverad information.

**ID:** 1144.01c1System.4 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du utser upp till tre prenumerationsägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Rollbaserad åtkomstkontroll implementeras och kan mappa varje användare till en eller flera roller och varje roll till en eller flera systemfunktioner.

**ID:** 1145.01c2System.1 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare tilldelad till din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger mer än en prenumerationsägare för att få administratörsåtkomstredundans. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Organisationen främjar utveckling och användning av program som undviker behovet av att köra med utökade privilegier och systemrutiner för att undvika behovet av att bevilja behörigheter till användare.

**ID:** 1146.01c2System.23 –01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Externa konton med ägarbehörighet ska tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externa konton med ägarbehörighet bör tas bort från prenumerationen för att förhindra oövervakad åtkomst. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Utökade privilegier tilldelas till ett annat användar-ID än det som används för normal företagsanvändning, alla användare har åtkomst till privilegierade tjänster i en enda roll och sådan privilegierad åtkomst minimeras.

**ID:** 1147.01c2System.456 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inaktuella konton med ägarbehörighet bör tas bort från prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Inaktuella konton med ägarbehörighet bör tas bort från prenumerationen.  Inaktuella konton är konton som har blockerats från att logga in. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Organisationen begränsar åtkomsten till privilegierade funktioner och all säkerhetsreleverad information.

**ID:** 1148.01c2System.78 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som "Ägare, Bidragsläsare, Läsare" i stället för anpassade RBAC-roller, som är felbenägna. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös granskning och hotmodellering |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows-datorer måste uppfylla kraven för säkerhetsalternativ – konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Säkerhetsalternativ – Konton för att begränsa lokal kontoanvändning av tomma lösenord och gästkontostatus. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Organisationen underlättar informationsdelning genom att tillåta behöriga användare att fastställa en affärspartners åtkomst när diskretion tillåts enligt organisationens definition och genom att använda manuella processer eller automatiserade mekanismer för att hjälpa användare att fatta beslut om delning/samarbete för information.

**ID:** 1149.01c2System.9 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ge detaljerad filtrering av de åtgärder som användare kan utföra kan du använda Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes Service-kluster och konfigurera relevanta auktoriseringsprinciper. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>Systemet för åtkomstkontroll för systemkomponenter som lagrar, bearbetar eller överför omfattas av information anges med en standardinställning &quot; för neka &quot; alla.

**ID:** 1150.01c2System.10-01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanteringsportar exponerar den virtuella datorn för en hög risknivå från Internetbaserade attacker. Dessa attacker försöker råstyra autentiseringsuppgifter för att få administratörsåtkomst till datorn. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Organisationen begränsar auktoriseringen till privilegierade konton i informationssystem till en fördefinierad delmängd av användare.

**ID:** 1151.01c3System.1 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du utser upp till tre prenumerationsägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Organisationen granskar körningen av privilegierade funktioner i informationssystem och ser till att informationssystem förhindrar att icke-privilegierade användare kör privilegierade funktioner.

**ID:** 1152.01c3System.2 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare tilldelad till din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger mer än en prenumerationsägare för att få administratörsåtkomstredundans. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>All filsystemåtkomst som inte uttryckligen krävs är inaktiverad och endast behöriga användare tillåts åtkomst till det som uttryckligen krävs för prestanda för användarnas arbetsuppgifter.

**ID:** 1153.01c3System.35 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ge detaljerad filtrering av de åtgärder som användare kan utföra kan du använda Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes Service-kluster och konfigurera relevanta auktoriseringsprinciper. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Leverantörer får minimal systemåtkomst och fysisk åtkomst först när organisationen har utvärderat leverantörens förmåga att uppfylla sina säkerhetskrav och leverantören samtycker till att uppfylla dem.

**ID:** 1154.01c3System.4 - 01.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du utser upp till tre prenumerationsägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Användarautentisering för externa anslutningar

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Starka autentiseringsmetoder som multifaktor, Radius eller Kerberos (för privilegierad åtkomst) och CHAP (för kryptering av autentiseringsuppgifter för uppringningsmetoder) implementeras för alla externa anslutningar till organisationens nätverk.

**ID:** 1116.01j1Organizational.145 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med ägarbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Fjärråtkomst av leverantörer och affärspartner (t.ex. för fjärrunderhåll) inaktiveras/inaktiveras när den inte används.

**ID:** 1117.01j1Organizational.23 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Organisationer implementerar kryptering (t.ex. VPN-lösningar eller privata linjer) och loggar fjärråtkomst till organisationens nätverk av anställda, leverantörer eller tredje &nbsp; part (t.ex. leverantörer).

**ID:** 1118.01j2Organizational.124 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med läsbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Nätverksutrustningen kontrolleras för att se om det finns oväntade uppringningsfunktioner.

**ID:** 1119.01j2Organizational.3 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Fjärradministrationssessioner godkänns, krypteras och använder ökade säkerhetsåtgärder.

**ID:** 1121.01j3Organizational.2 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med ägarbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Om kryptering inte används för fjärranslutningar tillhandahåller CIO eller hans/hennes utsedda representant specifik skriftlig auktorisering.

**ID:** 1173.01j1Organizational.6 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Organisationen skyddar trådlös åtkomst till system som innehåller känslig information genom att autentisera både användare och enheter.

**ID:** 1174.01j1Organizational.7 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med läsbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Fjärråtkomst till företagsinformation i offentliga nätverk sker endast efter lyckad identifiering och autentisering.

**ID:** 1175.01j1Organizational.8 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Organisationen kräver en återanropsfunktion med omautentisering för att verifiera fjärranslutningar från auktoriserade platser.

**ID:** 1176.01j2Organizational.5 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med ägarbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Användar-ID:er som tilldelats till leverantörer granskas i enlighet med organisationens princip för åtkomstgranskning, minst en gång per år.

**ID:** 1177.01j2Organizational.6 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Nodautentisering, inklusive kryptografiska tekniker (t.ex. datorcertifikat), fungerar som ett alternativt sätt att autentisera grupper av fjärranvändare där de är anslutna till en säker, delad dator.

**ID:** 1178.01j2Organizational.7 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med läsbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Informationssystemet övervakar och styr fjärråtkomstmetoder.

**ID:** 1179.01j3Organizational.1 - 01.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Fjärrdiagnostik och portskydd för konfiguration

### <a name="access-to-network-equipment-is-physically-protected"></a>Åtkomst till nätverksutrustning är fysiskt skyddad.

**ID:** 1192.01l1Organizational.1 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Kontroller för åtkomst till diagnostik- och konfigurationsportar omfattar användning av ett nyckellås och implementering av stödprocedurer för att kontrollera fysisk åtkomst till porten.

**ID:** 1193.01l2Organizational.13 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanteringsportar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanteringsportar exponerar den virtuella datorn för en hög risknivå från Internetbaserade attacker. Dessa attacker försöker råstyra autentiseringsuppgifter för att få administratörsåtkomst till datorn. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Portar, tjänster och liknande program som är installerade på en dator eller ett nätverkssystem, som inte krävs specifikt för affärsfunktioner, inaktiveras eller tas bort.

**ID:** 1194.01l2Organizational.2 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning ska vara inaktiverat för webbprogram](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Fjärrfelsökning kräver att inkommande portar öppnas i ett webbprogram. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Organisationen granskar informationssystemet var tredje hundradel och fem (365) dagar för att identifiera och inaktivera onödiga och icke-säkra funktioner, portar, protokoll och/eller tjänster.

**ID:** 1195.01l3Organizational.1 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning ska vara inaktiverat för funktionsappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i funktionsappar. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Organisationen identifierar obehörig (svartlistad) programvara i informationssystemet, förhindrar programkörning i enlighet med en lista över otillåtna (svartlistade) program, använder en princip för att tillåta alla, neka enligt undantag för att förhindra körning av känd obehörig (svartlistad) programvara samt granskar och uppdaterar listan över obehöriga (svartlistade) program varje år.

**ID:** 1196.01l3Organizational.24 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör vara inaktiverat för API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Fjärrfelsökning kräver att inkommande portar öppnas i API-appar. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Organisationen inaktiverar Protokoll för Bluetooth- och peer-to-peer-nätverk i informationssystemet som har fastställts vara onödiga eller icke-säkra.

**ID:** 1197.01l3Organizational.3 - 01.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassningsbara programkontroller för att definiera säkra program ska aktiveras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera programkontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. Detta hjälper dig att skydda dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla regler använder Security Center maskininlärning för att analysera programmen som körs på varje dator och föreslår en lista över kända säkra program. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Uppdelning i nätverk

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Organisationens säkerhetsgatewayer (t.ex. brandväggar) framtvingar säkerhetsprinciper och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelning mellan interna kabelanslutna, interna trådlösa och externa nätverkssegment (t.ex. Internet) inklusive DMZ:er och framtvinga principer för åtkomstkontroll för var och en av domänerna.

**ID:** 0805.01m1Organizational.12 - 01.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar händelsehubben som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät bör inte konfigureras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett gateway-undernät har konfigurerats med en nätverkssäkerhetsgrupp. Om du tilldelar en nätverkssäkerhetsgrupp till ett gateway-undernät slutar gatewayen att fungera. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om att styra trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagringskonto som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda undernätet mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control regler för lista (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Organisationens nätverk är logiskt och fysiskt segmenterat med en definierad säkerhets perimeter och en graderad uppsättning kontroller, inklusive undernät för offentligt tillgängliga systemkomponenter som är logiskt åtskilda från det interna nätverket, baserat på organisationens krav. och trafik styrs baserat på funktioner som krävs och klassificering av data/system baserat på en riskbedömning och deras respektive säkerhetskrav.

**ID:** 0806.01m2Organizational.12356 - 01.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar händelsehubben som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät bör inte konfigureras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett gateway-undernät har konfigurerats med en nätverkssäkerhetsgrupp. Om du tilldelar en nätverkssäkerhetsgrupp till ett gateway-undernät slutar gatewayen att fungera. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om hur du styr trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagringskonto som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda undernätet mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control regler för lista (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Nätverk är åtskilda från nätverk på produktionsnivå vid migrering av fysiska servrar, program eller data till virtualiserade servrar.

**ID:** 0894.01m2Organizational.7 - 01.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Distribuera network watcher när virtuella nätverk skapas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Den här principen skapar en nätverks bevakarresurs i regioner med virtuella nätverk. Du måste se till att det finns en resursgrupp med namnet networkWatcherRG, som ska användas för att distribuera Network Watcher-instanser. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar händelsehubben som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät bör inte konfigureras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett gateway-undernät har konfigurerats med en nätverkssäkerhetsgrupp. Om du tilldelar en nätverkssäkerhetsgrupp till ett gateway-undernät slutar gatewayen att fungera. |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om hur du styr trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagringskonto som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control lista över regler (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Kontroll av nätverksanslutning

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Nätverkstrafiken styrs i enlighet med organisationens princip för åtkomstkontroll via brandväggen och andra nätverksrelaterade begränsningar för varje nätverksåtkomstpunkt eller externa telekommunikationstjänsts hanterade gränssnitt.

**ID:** 0809.01n2Organizational.1234 - 01.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på Internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om att styra trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granskningskrav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda undernätet mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control regler för lista (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Den överförda informationen skyddas och krypteras som minst över öppna offentliga nätverk.

**ID:** 0810.01n2Organizational.5 -01.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på Internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om hur du styr trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control lista över regler (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Undantag till trafikflödesprincipen dokumenteras med ett stöd för uppdrags-/affärs behov, varaktighet för undantaget och granskas minst en gång per år. undantag för trafikflödesprincipen tas bort när de inte längre stöds av ett uttryckligt uppdrags-/affärs behov.

**ID:** 0811.01n2Organizational.6 - 01.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om hur du styr trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granskningskrav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda undernätet mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control regler för lista (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Fjärranslutna enheter som upprättar en icke-fjärranslutning tillåts inte att kommunicera med externa (fjärranslutna) resurser.

**ID:** 0812.01n2Organizational.8 - 01.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på Internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om att styra trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda undernätet mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control regler för lista (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Möjligheten för användare att ansluta till det interna nätverket är begränsad med hjälp av en princip för att neka som standard och tillåt per undantag i hanterade gränssnitt enligt principen för åtkomstkontroll och kraven för kliniska program och affärsprogram.

**ID:** 0814.01n1Organizational.12 - 01.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om hur du styr trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverkssäkerhetsgrupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverkssäkerhetsgrupp (NSG). NSG:er innehåller en lista Access Control lista över regler (ACL) som tillåter eller nekar nätverkstrafik till ditt undernät. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar alla virtuella datorer som är anslutna till ett virtuellt nätverk som inte är godkända. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Användaridentifiering och autentisering

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Organisationen ser till att redundanta användar-ID:er inte utfärdas till andra användare och att alla användare identifieras unikt och autentiseras för både lokal och fjärråtkomst till informationssystem.

**ID:** 11109.01q1Organizational.57 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med ägarbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Icke-organisationsanvändare (alla andra informationssystemsanvändare än organisationsanvändare, till exempel patienter, kunder, leverantörer eller externa leverantörer) eller processer som agerar för icke-organisationsanvändare, som har fastställt att de behöver åtkomst till information som finns i organisationens informationssystem, identifieras unikt och autentiseras.

**ID:** 11110.01q1Organizational.6 – 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>När PKI-baserad autentisering används validerar informationssystemet certifikat genom att konstruera och verifiera en certifieringssökväg till en godkänd förtroendeankare, inklusive kontroll av certifikatstatusinformation; tillämpar åtkomst till motsvarande privata nyckel; mappar identiteten till motsvarande konto för den enskilda personen eller gruppen; och implementerar en lokal cache med återkallningsdata för att stödja sökvägsidentifiering och verifiering om det inte går att komma åt information om återkallade certifikat via nätverket.

**ID:** 11111.01q2System.4 - 01.q **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med läsbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Informationssystemet använder replay-beständig autentiseringsmekanismer som nonce, lösenord vid en tidpunkt eller tidsstämplar för att skydda nätverksåtkomst för privilegierade konton. och för maskinvarutokenbaserad autentisering använder mekanismer som uppfyller minimikraven för token som beskrivs i NIST SP 800-63-2, riktlinjer för elektronisk autentisering.

**ID:** 11112.01q2Organizational.67 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du utser upp till tre prenumerationsägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Organisationen kräver att elektroniska signaturer som är unika för en enskild person inte kan återanvändas av eller tilldelas om till någon annan.

**ID:** 11208.01q1Organizational.8 – 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare tilldelad till din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger fler än en prenumerationsägare för att få administratörsåtkomstredundans. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Elektroniska signaturer och handskrivna signaturer som körs i elektroniska poster ska kopplas till deras respektive elektroniska poster.

**ID:** 11210.01q2Organizational.10 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Kräver att kraven distribueras till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer innehåller en eller flera av de medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Signerade elektroniska poster ska innehålla information som är associerad med signering i läsbart format.

**ID:** 11211.01q2Organizational.11 - 01.q **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som saknar någon av de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer inte innehåller en eller flera medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Användare som utförde privilegierade funktioner (t.ex. systemadministration) använder separata konton när de utför privilegierade funktioner.

**ID:** 1123.01q1System.2 - 01.q **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har extra konton i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer innehåller medlemmar som inte finns med i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Multifaktorautentiseringsmetoder används i enlighet med organisationens policy (t.ex. för fjärråtkomst till nätverk).

**ID:** 1125.01q2System.1 – 01.q **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Kräver att kraven distribueras till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer innehåller en eller flera av de medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Om token tillhandahålls för multifaktorautentisering krävs personlig verifiering innan åtkomst beviljas.

**ID:** 1127.01q2System.3 – 01.q **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som saknar någon av de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Kräver att kraven distribueras till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer inte innehåller en eller flera medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identifiering av risker relaterade till externa parter

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Externa parters åtkomst till organisationens information och system tillåts inte förrän noggrannhet har utförts, lämpliga kontroller har implementerats och ett kontrakt/avtal som återspeglar säkerhetskraven signeras som bekräftar att de förstår och godkänner sina skyldigheter.

**ID:** 1401.05i1Organizational.1239 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granskningskrav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Fjärråtkomstanslutningar mellan organisationen och externa parter krypteras.

**ID:** 1402.05i1Organizational.45 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Åtkomst som beviljas till externa parter är begränsad till det minsta nödvändiga och beviljas endast under den tid som krävs.

**ID:** 1403.05i1Organizational.67 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Noggrannhet hos den externa parten omfattar möten, dokumentgranskning, checklistor, certifieringsgranskningar (t.ex. HITRUST) &nbsp; eller andra fjärranslutna metoder.

**ID:** 1404.05i2Organizational.1– 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Identifieringen av risker relaterade till extern partsåtkomst tar hänsyn till en minimal uppsättning specifikt definierade problem.

**ID:** 1418.05i1Organizational.8 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Organisationen får tillfredsställande garantier om att rimlig informationssäkerhet finns i informationsleverantörskedjan genom att utföra en årlig granskning, som omfattar alla partner/tredjepartsleverantörer som deras leveranskedja för information är beroende av.

**ID:** 1450.05i2Organizational.2 – 05.i **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Molntjänstleverantörer utformar och implementerar kontroller för att minimera och begränsa datasäkerhetsrisker genom korrekt uppdelning av uppgifter, rollbaserad åtkomst och åtkomst med minsta behörighet för all personal i leveranskedjan.

**ID:** 1451.05iCSPOrganizational.2 - 05.i **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Granskningsloggning

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>En säker granskningspost skapas för alla aktiviteter i systemet (skapa, läsa, uppdatera, ta bort) som omfattar den information som omfattas.

**ID:** 1202.09aa1System.1 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Azure Data Lake Store ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Systemuppdateringar på VM-skalningsuppsättningar ska installeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Granska om det finns några saknade säkerhetsuppdateringar och viktiga uppdateringar som ska installeras för att säkerställa att dina vm-skalningsuppsättningar för Windows och Linux är säkra. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Granskningsposter innehåller unikt användar-ID, unikt id för dataämne, utförd funktion och datum/tid då händelsen utfördes.

**ID:** 1203.09aa1System.2 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Logic Apps ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Aktiviteter för privilegierade användare (administratörer, operatörer osv.) omfattar lyckad/misslyckad händelse, tid då händelsen inträffade, det berörda kontot, de processer som berörs och ytterligare information om händelsen.

**ID:** 1204.09aa1System.3 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i IoT Hub ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Loggar med meddelanden som skickas och tas emot underhålls, inklusive datum, tid, ursprung och mål för meddelandet, men inte dess innehåll.

**ID:** 1205.09aa2System.1 – 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Batch-konton ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Granskning är alltid tillgängligt när systemet är aktivt och spårar viktiga händelser, lyckad/misslyckad dataåtkomst, ändringar i systemsäkerhetskonfigurationen, privilegierad eller nyttighetsanvändning, aktivering och avaktivering av skyddssystem (t.ex. A/V och IDS), aktivering och inaktivering av identifierings- och autentiseringsmekanismer samt skapande och borttagning av objekt på &nbsp; systemnivå.

**ID:** 1206.09aa2System.23 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Virtual Machine Scale Sets ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Vi rekommenderar att du aktiverar loggar så att aktivitetsloggen kan återskapas när utredningar krävs i händelse av en incident eller ett komprometterat fel. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Granskningsposter sparas i 90 dagar och äldre granskningsposter arkiveras i ett år.

**ID:** 1207.09aa2System.4 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Händelsehubb ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Granskningsloggar underhålls för hanteringsaktiviteter, system- och programstart/avstängning/fel, filändringar och säkerhetsprincipändringar.

**ID:** 1208.09aa3System.1 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Söktjänster ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Informationssystemet genererar granskningsposter som innehåller följande detaljerade information: (i) det filnamn som används; (ii) program eller kommando som används för att initiera händelsen; och (iii) käll- och måladresser.

**ID:** 1209.09aa3System.2 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnostikloggar i App Services ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Granska aktivering av diagnostikloggar i appen. På så sätt kan du återskapa aktivitetsloggar i undersökningssyfte om en säkerhetsincident inträffar eller om nätverket har komprometterats |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Alla avslöjanden av den information som omfattas inom eller utanför organisationen loggas, inklusive typ av avslöjande, datum/tid för händelsen, mottagaren och avsändaren.

**ID:** 1210.09aa3System.3 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska diagnostikinställning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Granska diagnostikinställning för valda resurstyper |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Resursloggar i Data Lake Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Organisationen verifierar var nittio (90) dag för varje extrahering av den information som registrerats att data raderas eller att de fortfarande krävs.

**ID:** 1211.09aa3System.4 - 09.aa **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granskning på SQL Server ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning av SQL Server bör aktiveras för att spåra databasaktiviteter i alla databaser på servern och spara dem i en granskningslogg. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Resursloggar i Azure Key Vault Managed HSM ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Om du vill återskapa aktivitetsloggar i undersökningssyfte när en säkerhetsincident inträffar eller när nätverket har komprometterats kan du granska genom att aktivera resursloggar på hanterade HSM:er. Följ anvisningarna här: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Key Vault ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Övervaka systemanvändning

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Obehöriga fjärranslutningar till informationssystemen övervakas och granskas minst kvartalsvis, och lämpliga åtgärder vidtas om en obehörig anslutning upptäcks.

**ID:** 1120.09ab3System.9 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ska samla in aktivitetsloggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive globala. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Organisationen övervakar informationssystemet för att identifiera oregelbundenheter eller avvikelser som är indikatorer på ett systemfel eller komprometterande och hjälpa till att bekräfta att systemet fungerar i ett optimalt, motståndskraftigt och säkert tillstånd.

**ID:** 12100.09ab2System.15 -09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-agenten ska installeras på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Den här principen granskar alla virtuella Windows-/Linux-datorer om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Organisationen anger hur ofta granskningsloggar ska granskas, hur granskningarna dokumenteras och specifika roller och ansvarsområden för den personal som utför granskningarna, inklusive de professionella certifieringar eller andra kvalificeringar som krävs.

**ID:** 12101.09ab1Organizational.3 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-agenten bör installeras på Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Den här principen granskar alla Windows-/Linux Virtual Machine Scale Sets om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Organisationen ska regelbundet testa sina övervaknings- och identifieringsprocesser, åtgärda brister och förbättra sina processer.

**ID:** 12102.09ab1Organizational.4 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om agenten inte är installerad, eller om den är installerad men COM-objektet AgentConfigManager.MgmtSvcCfg returnerar att den är registrerad på en annan arbetsyta än det ID som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Alla tillämpliga juridiska krav som rör övervakning av auktoriserad åtkomst och obehöriga åtkomstförsök uppfylls.

**ID:** 1212.09ab1System.1 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor bör samla in loggar för kategorierna "skriva", "ta bort" och "åtgärd"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en loggprofil samlar in loggar för kategorierna "skriva", "ta bort" och "åtgärd" |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Automatiserade system som distribueras i organisationens miljö används för att övervaka viktiga händelser och avvikande aktiviteter, samt analysera systemloggar som resultaten granskas regelbundet.

**ID:** 1213.09ab2System.128 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatisk etablering av Log Analytics-agenten ska vara aktiverat i din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Om du vill övervaka säkerhetsrisker och hot samlar Azure Security Center data från dina virtuella Azure-datorer. Data samlas in av Log Analytics-agenten, tidigare kallad Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till Din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>Övervakning omfattar privilegierade åtgärder, auktoriserad åtkomst eller obehöriga åtkomstförsök, inklusive försök att komma åt inaktiverade konton och systemaviseringar eller fel.

**ID:** 1214.09ab2System.3456 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor bör samla in aktivitetsloggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor loggprofilen som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive globala. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Gransknings- och övervakningssystem som används av organisationen stöder granskningsminskning och rapportgenerering.

**ID:** 1215.09ab2System.7 - 09.ab **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-agenten ska installeras på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Den här principen granskar alla virtuella Windows-/Linux-datorer om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Automatiserade system används för att granska övervakningsaktiviteter i säkerhetssystem (t.ex. IPS/IDS) och systemposter dagligen och identifiera och dokumentera avvikelser.

**ID:** 1216.09ab3System.12 - 09.ab **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics-agenten bör installeras på Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Den här principen granskar alla Windows-/Linux Virtual Machine Scale Sets om Log Analytics-agenten inte är installerad. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Aviseringar genereras för teknisk personal för att analysera och undersöka misstänkt aktivitet eller misstänkta överträdelser.

**ID:** 1217.09ab3System.3 - 09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Kräver att kraven distribueras till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om agenten inte är installerad, eller om den är installerad men COM-objektet AgentConfigManager.MgmtSvcCfg returnerar att den har registrerats på en annan arbetsyta än det ID som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Informationssystemet kan automatiskt bearbeta granskningsposter för intressanta händelser baserat på valbara kriterier.

**ID:** 1219.09ab3System.10 -09.ab **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor bör samla in loggar för kategorierna "skriva", "ta bort" och "åtgärd"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en loggprofil samlar in loggar för kategorierna "skriva", "ta bort" och "åtgärd" |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>Övervakning omfattar inkommande och utgående kommunikation och övervakning av filintegritet.

**ID:** 1220.09ab3System.56 - 09.ab **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatisk etablering av Log Analytics-agenten ska aktiveras i din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |För att övervaka säkerhetsproblem och hot samlar Azure Security Center in data från dina virtuella Azure-datorer. Data samlas in av Log Analytics-agenten, tidigare kallad Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till Log Analytics-arbetsytan för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Administratörs- och operatörsloggar

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Organisationen ser till att korrekt loggning är aktiverat för att granska administratörsaktiviteter. och granskar regelbundet systemadministratörs- och operatörsloggar.

**ID:** 1270.09ad1System.12 – 09.ad **ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Ett intrångsidentifieringssystem som hanteras utanför system- och nätverksadministratörers kontroll används för att övervaka system- och nätverksadministrationsaktiviteter för efterlevnad.

**ID:** 1271.09ad1System.1 – 09.ad **ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Ansvarsfördelning

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Uppdelning av uppgifter används för att begränsa risken för obehöriga eller oavsiktliga ändringar av information och system.

**ID:** 1229.09c1Organizational.1 -09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ge detaljerad filtrering av de åtgärder som användarna kan utföra kan du använda Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes Service-kluster och konfigurera relevanta auktoriseringsprinciper. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Ingen enskild person kan komma åt, ändra eller använda informationssystem utan auktorisering eller identifiering.

**ID:** 1230.09c2Organizational.1 -09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som "Ägare, Bidragsläsare, Läsare" i stället för anpassade RBAC-roller, som är felbenägna. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös granskning och hotmodellering |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Åtkomsten för personer som ansvarar för att administrera åtkomstkontroller är begränsad till det minsta som krävs baserat på varje användares roll och ansvarsområden och dessa personer kan inte komma åt granskningsfunktioner relaterade &nbsp; till dessa kontroller.

**ID:** 1232.09c3Organizational.12 - 09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer måste uppfylla kraven för tilldelning av användarrättigheter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Windows-datorer bör ha de grupprincip inställningarna i kategorin "Tilldelning av användarrättigheter" för att tillåta inloggning lokalt, RDP, åtkomst från nätverket och många andra användaraktiviteter. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Säkerhetsgranskningsaktiviteter är oberoende av varandra.

**ID:** 1276.09c2Organizational.2 – 09.c **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassade prenumerationsägaresroller bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några ägarroller för anpassade prenumerationer. |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Initieringen av en händelse separeras från dess auktorisering för att minska risken för sortering.

**ID:** 1277.09c2Organizational.4 - 09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för "Säkerhetsalternativ – User Account Control"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows-datorer bör ha de angivna grupprincip-inställningarna i kategorin "Säkerhetsalternativ – User Account Control" för läge för administratörer, beteende för fråga om utökade privilegier samt virtualisering av skrivfel för filer och register. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Organisationen identifierar uppgifter som kräver separation och definierar informationssystemets åtkomstauktoriseringar för att stödja uppdelning av uppgifter. och inkompatibla skyldigheter är åtskilda mellan flera användare för att minimera risken för missbruk eller bedrägeri.

**ID:** 1278.09c2Organizational.56 - 09.c **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några ägarroller för anpassade prenumerationer. |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Kontroller mot skadlig kod

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Antivirus och spionprogram installeras, används och uppdateras på alla slutanvändares enheter för att utföra regelbundna genomsökningar av systemen för att identifiera och ta bort obehörig programvara. Servermiljöer där serverprogramutvecklaren uttryckligen rekommenderar att du inte installerar värdbaserad antivirus- och spionprogramsprogramvara kan hantera kravet via en nätverksbaserad identifiering av skadlig kod (NBMD).

**ID:** 0201.09j1Organizational.124 - 09.j **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassningsbara programkontroller för att definiera säkra program ska aktiveras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera programkontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. Detta hjälper dig att skydda dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler använder Security Center maskininlärning för att analysera programmen som körs på varje dator och föreslår en lista över kända säkra program. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Distribuera Microsoft IaaSAntimalware-standardtillägget för Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Den här principen distribuerar ett Microsoft IaaSAntimalware-tillägg med en standardkonfiguration när en virtuell dator inte har konfigurerats med tillägget för program mot skadlig programvara. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Endpoint Protection-lösningen ska installeras på VM-skalningsuppsättningar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Granska förekomsten och hälsotillståndet för en lösning för slutpunktsskydd på dina virtuella datorers skalningsuppsättningar för att skydda dem mot hot och sårbarheter. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware för Azure ska konfigureras för att automatiskt uppdatera skyddssignaturer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Den här principen granskar alla virtuella Windows-datorer som inte har konfigurerats med automatisk uppdatering Microsoft Antimalware signaturerna för skydd. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Övervaka saknat Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection-agent övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Back-up

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Säkerhetskopior av information och programvara görs och testerna av mediet och återställningsprocedurerna utförs regelbundet med lämpliga intervall.

**ID:** 1616.09l1Organizational.16 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Långsiktig geo-redundant säkerhetskopiering bör aktiveras för Azure SQL databaser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Den här principen granskar Azure SQL Database med långsiktig geo-redundant säkerhetskopiering inte aktiverad. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>En formell definition av vilken säkerhetskopieringsnivå som krävs för varje system definieras och dokumenteras, inklusive hur varje system återställs, vilken omfattning av data som ska avbildas, bildfrekvens och varaktighet för kvarhållning baserat på relevanta avtalsmässiga, juridiska, regel- och affärskrav.

**ID:** 1617.09l1Organizational.23 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Säkerhetskopiorna lagras på en fysiskt säker fjärrplats, på ett tillräckligt avstånd för att göra dem någorlunda immuna mot skador på data på den primära platsen, och rimliga fysiska och miljökontroller finns på plats för att säkerställa deras skydd på fjärrplatsen.

**ID:** 1618.09l1Organizational.45 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Inventeringsposter för säkerhetskopiorna, inklusive innehåll och aktuell plats, underhålls.

**ID:** 1619.09l1Organizational.7 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>När säkerhetskopieringstjänsten levereras av tredje part innehåller serviceavtalet detaljerade skydd för att kontrollera sekretess, integritet och tillgänglighet för säkerhetskopieringsinformationen.

**ID:** 1620.09l1Organizational.8 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Skydda din Azure-Virtual Machines genom att aktivera Azure Backup. Azure Backup är en säker och kostnadseffektiv dataskyddslösning för Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Automatiserade verktyg används för att spåra alla säkerhetskopior.

**ID:** 1621.09l2Organizational.1 -09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Långsiktig geo-redundant säkerhetskopiering ska aktiveras för Azure SQL databaser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Den här principen granskar alla Azure SQL Database med långsiktig geo-redundant säkerhetskopiering inte aktiverat. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Integriteten och säkerheten för säkerhetskopiorna bibehålls för att säkerställa framtida tillgänglighet, och eventuella tillgänglighetsproblem med säkerhetskopiorna identifieras och minimeras i händelse av en katastrof i hela området.

**ID:** 1622.09l2Organizational.23 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Den information som omfattas säkerhetskopieras i krypterat format för att säkerställa konfidentialitet.

**ID:** 1623.09l2Organizational.4 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Organisationen utför inkrementella eller differentiella säkerhetskopieringar varje dag och fullständiga säkerhetskopior varje vecka till separata media.

**ID:** 1624.09l3Organizational.12 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Tre (3) generationer av säkerhetskopior (fullständiga plus alla relaterade inkrementella eller differentiella säkerhetskopior) lagras på annan plats och både säkerhetskopieringar på plats och säkerhetskopior på annan plats loggas med namn, datum, tid och åtgärd.

**ID:** 1625.09l3Organizational.34 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Skydda din Azure-Virtual Machines genom att aktivera Azure Backup. Azure Backup är en säker och kostnadseffektiv dataskyddslösning för Azure. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Organisationen ser till att en aktuell, hämtningsbar kopia av den information som omfattas är tillgänglig innan servrarna kan flytta.

**ID:** 1626.09l3Organizational.5 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Organisationen testar säkerhetskopieringsinformationen efter varje säkerhetskopia för att verifiera mediatillförlitlighet och informationsintegritet, och därefter minst en gång per år.

**ID:** 1627.09l3Organizational.6 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Arbetsstyrkans medlemmars roller och ansvarsområden i processen för säkerhetskopiering av data identifieras och förmedlas till personalen. I synnerhet måste BYOD-användare (Bring Your Own Device) utföra säkerhetskopieringar av organisations- och/eller klientdata på sina enheter.

**ID:** 1699.09l1Organizational.10 - 09.l **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Skydda din Azure-Virtual Machines genom att aktivera Azure Backup. Azure Backup är en säker och kostnadseffektiv dataskyddslösning för Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Nätverkskontroller

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Organisationen övervakar all auktoriserad och obehörig trådlös åtkomst till informationssystemet och förbjuder installation av trådlösa åtkomstpunkter (WAPs) om inte uttryckligen godkänts i skrivningar av CIO eller hans/hennes utsedda representant.

**ID:** 0858.09m1Organizational.4 -09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alla nätverksportar ska vara begränsade till nätverkssäkerhetsgrupper som är associerade med den virtuella datorn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center har identifierat att vissa av dina nätverkssäkerhetsgruppers regler för inkommande är för tillåtande. Regler för inkommande trafik bör inte tillåta åtkomst från "Alla" eller "Internet"-intervall. Detta kan potentiellt göra det möjligt för angripare att rikta in sig på dina resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows-datorer bör uppfylla kraven för "Egenskaper för Windows-brandväggen"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows-datorer ska ha de grupprincip inställningarna i kategorin "Egenskaper för Windows-brandväggen" för brandväggstillstånd, anslutningar, regelhantering och meddelanden. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Organisationen säkerställer säkerheten för information i nätverk, tillgänglighet för nätverkstjänster och informationstjänster som använder nätverket och skydd av anslutna tjänster mot obehörig åtkomst.

**ID:** 0859.09m1Organizational.78 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Organisationen hanterar formellt utrustning i nätverket, inklusive utrustning i användarområden.

**ID:** 0860.09m1Organizational.9 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Den här principen distribuerar automatiskt diagnostikinställningar till nätverkssäkerhetsgrupper. Ett lagringskonto med namnet {storagePrefixParameter}{NSGLocation} skapas automatiskt. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>För att identifiera och autentisera enheter i lokala och/eller breda nätverk, inklusive trådlösa nätverk, använder informationssystemet antingen en (i) delad känd informationslösning eller (ii) en autentiseringslösning i organisationen, som är beroende av informationssystemets säkerhetskategorisering. &nbsp;

**ID:** 0861.09m2Organizational.67 -09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows-datorer bör uppfylla kraven för "Säkerhetsalternativ – nätverksåtkomst"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows-datorer ska ha de grupprincip inställningarna i kategorin "Säkerhetsalternativ – Nätverksåtkomst" för att inkludera åtkomst för anonyma användare, lokala konton och fjärråtkomst till registret. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Organisationen ser till att informationssystem skyddar konfidentialitet och integritet för överförd information, inklusive under förberedelse för överföring och mottagning.

**ID:** 0862.09m2Organizational.8 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Organisationen skapar en brandväggskonfiguration som begränsar anslutningar mellan ej betrodda nätverk och eventuella systemkomponenter i den informationsmiljö som omfattas. och eventuella ändringar i brandväggskonfigurationen uppdateras i nätverksdiagrammet.

**ID:** 0863.09m2Organizational.910 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar händelsehubben som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Användningsbegränsningar och implementeringsvägledning definieras formellt för VoIP, inklusive auktorisering och övervakning av tjänsten.

**ID:** 0864.09m2Organizational.12 -09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Organisationen (i) godkänner anslutningar från informationssystemet till andra informationssystem utanför organisationen genom att använda säkerhetsavtal för samtrafik eller andra formella avtal. (ii) dokumenterar varje anslutning, gränssnittsegenskaper, säkerhetskrav och vilken typ av information som förmedlas, (iii) använder ett nekande av alla, tillåt enligt undantagsprincip för att tillåta anslutningar från informationssystemet till andra informationssystem utanför organisationen; och (iv) tillämpar en standardregel för att neka som släpper all trafik via värdbaserade brandväggar eller portfiltreringsverktyg på dess slutpunkter (arbetsstationer, servrar osv.), förutom de tjänster och portar som uttryckligen tillåts.

**ID:** 0865.09m2Organizational.13 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Organisationen beskriver grupper, roller och ansvarsområden för den logiska hanteringen av nätverkskomponenter och säkerställer samordning av och konsekvens i elementen i nätverksinfrastrukturen.

**ID:** 0866.09m3Organizational.1516 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör begränsa nätverksåtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverksåtkomst till lagringskonton bör begränsas. Konfigurera nätverksregler så att endast program från tillåtna nätverk kan komma åt lagringskontot. För att tillåta anslutningar från specifika Internetklienter eller lokala klienter kan åtkomst beviljas till trafik från specifika virtuella Azure-nätverk eller till IP-adressintervall för offentliga Internet |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Trådlösa åtkomstpunkter placeras i säkra områden och stängs av när de inte används (t.ex. helger eller helger).

**ID:** 0867.09m3Organizational.17 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagringskonto som inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Organisationen skapar en brandväggskonfiguration för att begränsa inkommande och utgående trafik till den som krävs för den datamiljö som omfattas.

**ID:** 0868.09m3Organizational.18 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Routerkonfigurationsfilerna skyddas och synkroniseras.

**ID:** 0869.09m3Organizational.19 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Åtkomst till alla proxys nekas, förutom de värdar, portar och tjänster som uttryckligen krävs.

**ID:** 0870.09m3Organizational.20 -09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Auktoritativa DNS-servrar är uppdelade i interna och externa roller.

**ID:** 0871.09m3Organizational.22 - 09.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry inte har konfigurerats för att använda en tjänstslutpunkt för virtuellt nätverk. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Säkerhet för Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Godkända tjänster som tillhandahålls av en nätverkstjänstleverantör/-chef hanteras och övervakas formellt för att säkerställa att de tillhandahålls på ett säkert sätt.

**ID:** 0835.09n1Organizational.1 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agenten för datainsamling för nätverkstrafik ska vara installerad på virtuella Windows-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center använder Microsoft Dependency Agent för att samla in data om nätverkstrafik från dina virtuella Azure-datorer för att aktivera avancerade nätverksskyddsfunktioner, till exempel trafikvisualisering på nätverkskartan, rekommendationer för nätverkshärdning och specifika nätverkshot. |AuditIfNotExists, Disabled |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Virtuella datorer ska migreras till nya Azure Resource Manager resurser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Använd nya Azure Resource Manager för dina virtuella datorer för att tillhandahålla säkerhetsförbättringar som: starkare åtkomstkontroll (RBAC), bättre granskning, Azure Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckelvalv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resursgrupper för enklare säkerhetshantering |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Organisationen godkänner och dokumenterar formellt egenskaperna för varje anslutning från ett informationssystem till andra informationssystem utanför organisationen.

**ID:** 0836.09.n2Organizational.1 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agenten för datainsamling för nätverkstrafik ska vara installerad på virtuella Linux-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center använder Microsoft Dependency Agent för att samla in data om nätverkstrafik från dina virtuella Azure-datorer för att aktivera avancerade nätverksskyddsfunktioner, till exempel trafikvisualisering på nätverkskartan, rekommendationer för nätverkshärdning och specifika nätverkshot. |AuditIfNotExists, Disabled |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Formella avtal med externa informationssystemleverantörer omfattar specifika krav på säkerhet och sekretess.

**ID:** 0837.09.n2Organizational.2 – 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Med övervakning på scenarionivå kan du diagnostisera problem i en vy på nätverksnivå från början till slut. Verktyg för nätverksdiagnostik och visualisering som är Network Watcher hjälper dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Organisationen granskar och uppdaterar säkerhetsavtalen för samtrafik regelbundet för att verifiera efterlevnaden av säkerhetskraven.

**ID:** 0885.09n2Organizational.3 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agenten för datainsamling för nätverkstrafik ska vara installerad på virtuella Linux-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center använder Microsoft Dependency Agent för att samla in nätverkstrafikdata från dina virtuella Azure-datorer för att aktivera avancerade nätverksskyddsfunktioner som trafikvisualisering på nätverkskartan, nätverkshärdningsrekommendationer och specifika nätverkshot. |AuditIfNotExists, inaktiverad |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Organisationen använder och dokument i ett formellt avtal eller andra dokument, antingen i) allow-all, deny-by-exception eller, ii) deny-all, allow-by-exception (rekommenderas), princip för att tillåta specifika informationssystem att ansluta till externa informationssystem.

**ID:** 0886.09n2Organizational.4 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Med övervakning på scenarionivå kan du diagnostisera problem i en vy på nätverksnivå från början till slut. Verktyg för nätverksdiagnostik och visualisering som är Network Watcher hjälper dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Organisationen kräver att externa/outsourcada tjänstleverantörer identifierar de specifika funktioner, portar och protokoll som används för att etablera externa/outsourcada tjänster.

**ID:** 0887.09n2Organizational.5 - 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agenten för datainsamling för nätverkstrafik ska vara installerad på virtuella Windows-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center använder Microsoft Dependency Agent för att samla in data om nätverkstrafik från dina virtuella Azure-datorer för att aktivera avancerade nätverksskyddsfunktioner, till exempel trafikvisualisering på nätverkskartan, rekommendationer för nätverkshärdning och specifika nätverkshot. |AuditIfNotExists, Disabled |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>Kontraktet med den externa/outsourcade tjänstleverantören innehåller specifikationen att tjänstleverantören ansvarar för skyddet av den information som delas.

**ID:** 0888.09n2Organizational.6 – 09.n **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Med övervakning på scenarionivå kan du diagnostisera problem i en vy på nätverksnivå från början till slut. Verktyg för nätverksdiagnostik och visualisering som är Network Watcher hjälper dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Hantering av flyttbara media

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Organisationen, baserat på dataklassificeringsnivån, registrerar media (inklusive bärbara datorer) före användning, inför rimliga begränsningar för hur sådana media ska användas och tillhandahåller en lämplig nivå av fysiskt och logiskt skydd (inklusive kryptering) för media som innehåller omfattas information tills de förstörs eller saneras korrekt.

**ID:** 0301.09o1Organizational.123 - 09.o **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent datakryptering ska aktiveras för att skydda vilodata och uppfylla efterlevnadskrav |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Organisationen skyddar och styr media som innehåller känslig information under transport utanför kontrollerade områden.

**ID:** 0302.09o2Organizational.1 - 09.o **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diskkryptering ska tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan aktiverad diskkryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Digitala och icke-digitala medier som kräver begränsad användning och de specifika skydd som används för att begränsa deras användning identifieras.

**ID:** 0303.09o2Organizational.2 - 09.o **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Icke-frånkopplade diskar ska krypteras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Den här principen granskar alla icke-frånkopplade diskar utan att kryptering har aktiverats. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Organisationen begränsar användningen av skrivbara flyttbara media och personligt ägda flyttbara media i organisationssystem.

**ID:** 0304.09o3Organizational.1 -09.o **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kräv kryptering på Data Lake Store konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Den här principen säkerställer att kryptering är aktiverat på alla Data Lake Store konton |Förneka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL-hanterade instanser bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom att implementera transparent datakryptering (TDE) med din egen nyckel får du ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en extern HSM-backad tjänst och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering transparent datakryptering (TDE) med din egen nyckel ger ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en HSM-backad extern tjänst och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Principer och procedurer för informationsutbyte

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Molntjänstleverantörer använder en branschledd virtualiseringsplattform och standardvirtualiseringsformat (t.ex. Open Virtualization Format, OVF) för att säkerställa samverkan och har dokumenterat anpassade ändringar som gjorts i alla hypervisor-program som används och alla lösningsspecifika virtualiserings hookar som är tillgängliga för kundgranskning.

**ID:** 0662.09sCSPOrganizational.2 – 09.s **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att WEBBappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Med klientcertifikat kan appen begära ett certifikat för inkommande begäranden. Endast klienter som har ett giltigt certifikat kan nå appen. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Organisationen hanterar formellt flera skyddsmekanismer innan informationssystem tillåts använda sig av informationsutbyte.

**ID:** 0901.09s1Organizational.1 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS (Cross Origin Resource Sharing) bör inte tillåta att alla resurser har åtkomst till dina webbappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till webbappen. Tillåt endast att obligatoriska domäner interagerar med din webbapp. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Fjärråtkomst (extern) till organisationens informationstillgångar och åtkomst till externa informationstillgångar (som organisationen inte har någon kontroll för) baseras på tydligt definierade villkor.

**ID:** 0902.09s2Organizational.13 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS bör inte tillåta att alla resurser får åtkomst till dina funktionsappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till funktionsappen. Tillåt endast att obligatoriska domäner interagerar med funktionsappen. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Organisationen upprättar allmänna villkor, i enlighet med alla förtroenderelationer som upprättats med andra organisationer som äger, använder och/eller underhåller externa informationssystem, så att behöriga personer kan (i) komma åt informationssystemet från externa informationssystem; och (ii) bearbeta, lagra eller överföra organisationskontrollerad information med hjälp av externa informationssystem.

**ID:** 0911.09s1Organizational.2 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS bör inte tillåta att alla resurser får åtkomst till din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till din API-app. Tillåt endast att obligatoriska domäner interagerar med din API-app. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Kryptografi används för att skydda sekretessen och integriteten för fjärråtkomstsessioner till det interna nätverket och externa system.

**ID:** 0912.09s1Organizational.4 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning ska vara inaktiverat för webbprogram](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Fjärrfelsökning kräver att inkommande portar öppnas i ett webbprogram. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Starka kryptografiprotokoll används för att skydda den information som omfattas vid överföring över mindre betrodda/öppna offentliga nätverk.

**ID:** 0913.09s1Organizational.5 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning ska vara inaktiverat för funktionsappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i funktionsappar. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Organisationen ser till att krav på kommunikationsskydd, inklusive säkerhet vid utbyte av information, omfattas av granskningar av policyutveckling och efterlevnad.

**ID:** 0914.09s1Organizational.6 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör vara inaktiverat för API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Fjärrfelsökning kräver att inkommande portar öppnas i API-appar. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Organisationen begränsar användningen av organisationskontrollerade bärbara lagringsmedier för behöriga personer i externa informationssystem.

**ID:** 0915.09s2Organizational.2 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att WEBBappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Med klientcertifikat kan appen begära ett certifikat för inkommande begäranden. Endast klienter som har ett giltigt certifikat kan komma åt appen. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Informationssystemet förbjuder fjärraktivering av samarbetsbaserade databehandlingsenheter och ger en uttrycklig indikation på användning för användare som är fysiskt närvarande på enheterna.

**ID:** 0916.09s2Organizational.4 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS (Cross Origin Resource Sharing) bör inte tillåta att alla resurser har åtkomst till dina webbappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till webbappen. Tillåt endast att obligatoriska domäner interagerar med din webbapp. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Molntjänstleverantörer använder säkra (t.ex. icke-klartext och autentiserade) standardiserade nätverksprotokoll för import och export av data och för att hantera tjänsten, och gör ett dokument tillgängligt för konsumenter (klienter) som beskriver relevanta standarder för samverkan och portabilitet som är inblandade.

**ID:** 0960.09sCSPOrganizational.1 – 09.s **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS bör inte tillåta att alla resurser får åtkomst till dina funktionsappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till funktionsappen. Tillåt endast att obligatoriska domäner interagerar med funktionsappen. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Personal utbildas på lämpligt sätt enligt ledande principer och metoder för alla typer av informationsutbyte (paper och electronic).

**ID:** 1325.09s1Organizational.3 - 09.s **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Fjärrfelsökning ska vara inaktiverat för funktionsappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i funktionsappar. Fjärrfelsökning bör vara inaktiverat. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Onlinetransaktioner

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Data som ingår i transaktioner inom elektronisk handel och online kontrolleras för att avgöra om de innehåller den information som omfattas.

**ID:** 0943.09y1Organizational.1 - 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Protokoll som används för att kommunicera mellan alla berörda parter skyddas med hjälp av kryptografiska tekniker (t.ex. SSL).

**ID:** 0945.09y1Organizational.3 – 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som inte innehåller de angivna certifikaten i betrodd rot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Kräver att kraven distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om datorns betrodda rotcertifikatarkiv (Cert:\LocalMachine\Root) inte innehåller ett eller flera av de certifikat som anges av principparametern. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Organisationen kräver kryptering mellan och användning av elektroniska signaturer av var och en av de parter som är inblandade i transaktionen.

**ID:** 0946.09y2Organizational.14 - 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endast säkra anslutningar till Azure Cache for Redis bör vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av endast anslutningar via SSL till Azure Cache for Redis. Användning av säkra anslutningar garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Organisationen ser till att lagringen av transaktionsinformationen finns utanför offentligt tillgängliga miljöer (t.ex. på en lagringsplattform som finns på organisationens intranät) och inte bevaras och exponeras på ett lagringsmedium som är direkt åtkomligt från Internet.

**ID:** 0947.09y2Organizational.2 - 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>När en betrodd auktoritet används (t.ex. för att utfärda och underhålla digitala signaturer och/eller digitala certifikat) är säkerheten integrerad och inbäddad i hela hela processen för hantering av certifikat/signaturer.

**ID:** 0948.09y2Organizational.3 – 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Protokollen som används för kommunikation förbättras för att åtgärda eventuella nya säkerhetsrisk, och de uppdaterade versionerna av protokollen används så snart som möjligt.

**ID:** 0949.09y2Organizational.5 - 09.y **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API-appen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Funktionsappen bör endast vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS säkerställer server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Kontroll av driftprogramvara

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Endast auktoriserade administratörer tillåts att implementera godkända uppgraderingar av programvara, program och programbibliotek, baserat på affärskrav och säkerhetskonsekvenserna av lanseringen.

**ID:** 0605.10h1System.12 - 10.h **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade baslinjen övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows-datorer bör uppfylla kraven för "Säkerhetsalternativ – granskning"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows-datorer bör ha de grupprincip inställningarna i kategorin "Säkerhetsalternativ – granskning" för att framtvinga granskningsprincipunderkategori och stänga av om det inte går att logga säkerhetsgranskningar. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – kontohantering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – Kontohantering för granskning av program, säkerhet och hantering av användargrupper och andra hanteringshändelser. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Program och operativsystem testas med framgång för användbarhet, säkerhet och påverkan före produktion.

**ID:** 0606.10h2System.1 - 10.h **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i containersäkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska säkerhetsrisker i säkerhetskonfigurationen på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Organisationen använder sitt konfigurationskontrollprogram för att behålla kontrollen över all implementerad programvara och dess systemdokumentation och arkivera tidigare versioner av implementerad programvara och tillhörande systemdokumentation.

**ID:** 0607.10h2System.23 – 10.h **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassningsbara programkontroller för att definiera säkra program ska aktiveras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera programkontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. Detta hjälper till att skydda dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler använder Security Center maskininlärning för att analysera programmen som körs på varje dator och föreslår en lista över kända säkra program. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem mot attacker. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Procedurer för ändringskontroll

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Chefer som ansvarar för programsystem ansvarar också för den strikta kontrollen (säkerheten) för projektet eller supportmiljön och ser till att alla föreslagna systemändringar granskas för att kontrollera att de inte äventyrar säkerheten för vare sig systemet eller driftsmiljön.

**ID:** 0635.10k1Organizational.12 - 10.k **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Organisationen hanterar formellt syfte, omfattning, roller, ansvarsområden, hanteringsåtagande, samordning mellan organisationsentiteter och efterlevnad för konfigurationshantering (t.ex. via principer, standarder, processer).

**ID:** 0636.10k2Organizational.1 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Organisationen har utvecklat, dokumenterat och implementerat en konfigurationshanteringsplan för informationssystemet.

**ID:** 0637.10k2Organizational.2 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/-avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Ändringar kontrolleras formellt, dokumenteras och framtvingas för att minimera skadorna på informationssystem.

**ID:** 0638.10k2Organizational.34569 - 10.k **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/-avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Checklistor för installation och sårbarhetsgenomsökningar används för att verifiera konfigurationen av servrar, arbetsstationer, enheter och installationer och se till att konfigurationen uppfyller minimikraven.

**ID:** 0639.10k2Organizational.78 – 10.k **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>När utvecklingen outsourcar ingår procedurer för ändringskontroll för att hantera säkerheten i kontrakten och kräver specifikt att utvecklaren spårar säkerhetsbrister och fellösning i systemet, komponenten eller tjänsten och rapporterar resultat till organisationsdefinierad personal eller roller.

**ID:** 0640.10k2Organizational.1012 - 10.k **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Organisationen använder inte automatiska uppdateringar på kritiska system.

**ID:** 0641.10k2Organizational.11 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/-avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Organisationen utvecklar, dokumenterar och underhåller, under konfigurationskontroll, en aktuell baslinjekonfiguration av informationssystemet och granskar och uppdaterar baslinjen efter behov.

**ID:** 0642.10k3Organizational.12 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/-avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att förhandskrav för gästkonfiguration har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Organisationen (i) upprättar och dokumenterar obligatoriska konfigurationsinställningar för informationsteknikprodukter som används i informationssystemet med de senaste säkerhetskonfigurationsbaslinjerna. (ii) identifierar, dokumenterar och godkänner undantag från de obligatoriska etablerade konfigurationsinställningarna för enskilda komponenter baserat på explicita driftskrav, och (iii) övervakar och kontrollerar ändringar i konfigurationsinställningarna i enlighet med organisationens principer och procedurer.

**ID:** 0643.10k3Organizational.3 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Organisationen använder automatiserade mekanismer för att (i) centralt hantera, tillämpa och verifiera konfigurationsinställningar. (ii) svara på obehöriga ändringar av nätverks- och systemsäkerhetsrelaterade konfigurationsinställningar, och (iii) framtvinga åtkomstbegränsningar och granskning av tvingande åtgärder.

**ID:** 0644.10k3Organizational.4 – 10.k **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för systemgranskningsprinciper – detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de grupprincip inställningarna i kategorin Systemgranskningsprinciper – detaljerad spårning för granskning av DPAPI, processskapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Kontroll av tekniska säkerhetsrisker

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Tekniska säkerhetsrisker identifieras, utvärderas för risk och korrigeras i rätt tid.

**ID:** 0709.10m1Organizational.1 - 10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[En lösning för sårbarhetsbedömning ska vara aktiverad på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Granskar virtuella datorer för att identifiera om de kör en lösning för sårbarhetsbedömning som stöds. En viktig del av alla cyberrisker och säkerhetsprogram är identifiering och analys av sårbarheter. Azure Security Center standardprisnivån omfattar sårbarhetsgenomsökning för dina virtuella datorer utan extra kostnad. Dessutom kan Security Center automatiskt distribuera det här verktyget åt dig. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Sårbarheter i containersäkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska säkerhetsrisker i säkerhetskonfigurationen på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade baslinjen övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem från attacker. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Sårbarheter i DINA SQL-databaser bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Övervaka genomsökningsresultat för sårbarhetsbedömning och rekommendationer för hur du åtgärdar sårbarheter i databasen. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Sårbarhetsbedömning bör aktiveras på SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL Managed Instance som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella säkerhetsrisker i databasen. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Sårbarhetsbedömning bör aktiveras på SQL-servrarna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Granska Azure SQL servrar som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella säkerhetsrisker i databasen. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows-datorer måste uppfylla kraven för "Säkerhetsalternativ – Microsoft Network Server"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows-datorer bör ha de grupprincip inställningarna i kategorin "Säkerhetsalternativ – Microsoft-nätverksserver" för att inaktivera SMB v1-servern. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Det finns en härdad konfigurationsstandard för alla system- och nätverkskomponenter.

**ID:** 0710.10m2Organizational.1 – 10.m **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarhetsbedömning bör aktiveras på SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL Managed Instance som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella sårbarheter i databasen. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Ett program för teknisk sårbarhetshantering finns på plats för att övervaka, utvärdera, rangordna och åtgärda sårbarheter som identifieras i system.

**ID:** 0711.10m2Organizational.23 - 10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[En lösning för sårbarhetsbedömning ska vara aktiverad på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Granskar virtuella datorer för att identifiera om de kör en lösning för sårbarhetsbedömning som stöds. En viktig del av alla cyberrisker och säkerhetsprogram är identifiering och analys av sårbarheter. Azure Security Center standardprisnivån omfattar sårbarhetsgenomsökning för dina virtuella datorer utan extra kostnad. Dessutom kan Security Center automatiskt distribuera det här verktyget åt dig. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Korrigeringar testas och utvärderas innan de installeras.

**ID:** 0713.10m2Organizational.5 -10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade baslinjen övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Programmet för teknisk sårbarhetshantering utvärderas kvartalsvis.

**ID:** 0714.10m2Organizational.7 -10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem från attacker. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>System är korrekt härdade (t.ex. konfigurerade med endast nödvändiga och säkra tjänster, portar och protokoll aktiverade).

**ID:** 0715.10m2Organizational.8 - 10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i containersäkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska säkerhetsrisker i säkerhetskonfigurationen på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Organisationen utför en granskning av företagets säkerhetsstatus efter behov, men inte mindre än en gång inom var tredje hundradels (365) dagar, i enlighet med organisationens IS-procedurer.

**ID:** 0716.10m3Organizational.1 - 10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i DINA SQL-databaser bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Övervaka genomsökningsresultat för sårbarhetsbedömning och rekommendationer för hur du åtgärdar sårbarheter i databasen. |AuditIfNotExists, inaktiverad |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>Sårbarhetsgenomsökningsverktyg innehåller möjligheten att enkelt uppdatera säkerhetsrisker i informationssystemet som genomsöks.

**ID:** 0717.10m3Organizational.2 – 10.m **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem från attacker. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Organisationen söker efter sårbarheter i informationssystemet och värdprogram för att fastställa tillståndet för felreparation månadsvis (automatiskt) och igen (manuellt eller automatiskt) när nya sårbarheter som potentiellt påverkar system och nätverksanslutna miljöer identifieras och rapporteras.

**ID:** 0718.10m3Organizational.34 -10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade baslinjen övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Organisationen uppdaterar listan över sårbarheter i informationssystemet som genomsöks var 30:e dag eller när nya sårbarheter identifieras och rapporteras.

**ID:** 0719.10m3Organizational.5 -10.m **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarhetsbedömning bör aktiveras på SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL Managed Instance som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella sårbarheter i databasen. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Verksamhetskontinui och riskbedömning

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Organisationen identifierar de kritiska affärsprocesser som kräver affärskontinuhet.

**ID:** 1634.12b1Organizational.1 – 12.b **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska virtuella datorer utan konfigurerad haveriberedskap](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Granska virtuella datorer som inte har konfigurerat haveriberedskap. Mer information om haveriberedskap finns i [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Informationssäkerhetsaspekter för affärskontinuisering (i) baseras på identifiering av händelser (eller händelsesekvens) som kan orsaka avbrott i organisationens kritiska affärsprocesser (t.ex. utrustningsfel, mänskliga fel, stöld, brand, naturkatastrofer); (ii) följt av en riskbedömning för att fastställa sannolikheten och effekten av sådana avbrott, med avseende på tid, skador och återställningsperiod, (iii) Baserat på resultatet av riskbedömningen utvecklas en strategi för affärskontinuering för att identifiera den övergripande metoden för affärskontinuering. och (iv) när den här strategin har skapats tillhandahålls godkännande av ledningen och en plan skapas och godkänns för att implementera den här strategin.

**ID:** 1635.12b1Organizational.2 – 12.b **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM ska ha rensningsskydd aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Skadlig borttagning av en Azure Key Vault Managed HSM kan leda till permanent dataförlust. En illvillig insider i din organisation kan potentiellt ta bort och rensa Azure Key Vault Managed HSM. Rensningsskydd skyddar dig mot insiderattacker genom att framtvinga en obligatorisk kvarhållningsperiod för mjuk borttagning Azure Key Vault Managed HSM. Ingen i din organisation eller Microsoft kommer att kunna rensa din Azure Key Vault Managed HSM under kvarhållningsperioden för mjuk borttagning. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Rensningsskydd ska vara aktiverat för nyckelvalv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckelvalv kan leda till permanent dataförlust. En illvillig insider i din organisation kan potentiellt ta bort och rensa nyckelvalv. Rensningsskydd skyddar dig mot insiderattacker genom att framtvinga en obligatorisk kvarhållningsperiod för mjukt borttagna nyckelvalv. Ingen i din organisation eller Microsoft kommer att kunna rensa dina nyckelvalv under kvarhållningsperioden för mjuk borttagning. |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Analys av affärspåverkan används för att utvärdera konsekvenserna av katastrofer, säkerhetsfel, förlust av tjänst och tjänsttillgänglighet.

**ID:** 1637.12b2Organizational.2 – 12.b **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-datorer måste uppfylla kraven för "Säkerhetsalternativ – återställningskonsol"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Windows-datorer bör ha de grupprincip inställningarna i kategorin "Säkerhetsalternativ – återställningskonsol" för att tillåta diskettkopiering och åtkomst till alla enheter och mappar. Den här principen kräver att kraven för gästkonfigurationen har distribuerats till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Riskbedömningar för affärskontinui (i) utförs varje år med fullständig inblandning från ägare av företagsresurser och processer. (ii) ta hänsyn till alla affärsprocesser och är inte begränsad till informationstillgångar, utan innehåller resultat som är specifika för informationssäkerhet; och (iii) identifierar, kvantifierar och prioriterar risker mot viktiga affärsmål och kriterier som är relevanta för organisationen, inklusive kritiska resurser, påverkan av avbrott, tillåtna avbrottstider och återställningsprioriteringar.

**ID:** 1638.12b2Organizational.345 - 12.b **Ownership**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska virtuella datorer utan konfigurerad haveriberedskap](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Granska virtuella datorer som inte har konfigurerat haveriberedskap. Mer information om haveriberedskap finns i [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Tillgängligheten för Azure Policy definitioner kan variera Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om Azure Policy:

- [Översikt över regelefterlevnad.](../concepts/regulatory-compliance.md)
- Se [initiativdefinitionsstrukturen](../concepts/initiative-definition-structure.md).
- Granska andra exempel på [Azure Policy exempel](./index.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig hur [du åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
