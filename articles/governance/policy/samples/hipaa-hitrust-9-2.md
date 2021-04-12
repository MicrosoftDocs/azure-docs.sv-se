---
title: Information om regelefterlevnad för HIPAA HITRUST 9,2
description: Information om det inbyggda initiativet HIPAA HITRUST 9,2-kontroll. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 03/31/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: db8f3c887a6ed7beb2febb6910bec32e7b0ea218
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096401"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Information om det inbyggda initiativet HIPAA HITRUST 9,2-kontroll

I följande artikel beskrivs hur den inbyggda definitionen av den Azure Policy reglerande kraven mappar till **efterlevnad av domäner** och **kontroller** i HIPAA HiTRUST 9,2.
Mer information om den här standarden för efterlevnad finns i [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). För att förstå _ägarskap_, se [Azure policy princip definition](../concepts/definition-structure.md#type) och [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).

Följande mappningar är till **HIPAA HITRUST 9,2** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en speciell **domän för efterlevnad**. Många av kontrollerna implementeras med en [Azure policy](../overview.md) initiativ definition. Om du vill granska den fullständiga initiativ definitionen öppnar du **princip** i Azure Portal och väljer sidan **definitioner** .
Leta sedan reda på och välj den inbyggda definitionen av **HiTRUST/HIPAA** -regler för regelefterlevnad.

Det här inbyggda initiativet distribueras som en del av [skiss exemplet HIPAA HITRUST 9,2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../overview.md) -definitioner.
> Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som refererar till själva princip definitionerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan domäner, kontroller och Azure Policy definitioner för kompatibiliteten kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Behörighets hantering

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Åtkomst till hanterings funktioner eller administrativa konsoler för system som är värdar för virtualiserade system är begränsade till personal baserat på principen om minsta behörighet och support genom tekniska kontroller.

**ID**: 11180.01 c3system. 6 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Behörigheterna godkänns och kontrol leras formellt, allokeras till användare på ett behov av att använda och händelse-för-händelse för deras funktions roll (t. ex. användare eller administratör) och dokumenteras för varje system produkt/-element.

**ID**: 1143.01 c1system. 123-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanterings portar exponerar din virtuella dator på en hög risk nivå från Internetbaserade attacker. Dessa angrepp försöker att få administratörs behörighet till datorn. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Organisationen ger uttryckligen till gång till vissa säkerhetsrelaterade funktioner (distribuerade i maskin vara, program vara och inbyggd program vara) och säkerhetsrelaterad information.

**ID**: 1144.01 c1system. 4 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du anger upp till tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Rollbaserad åtkomst kontroll implementeras och kan mappa varje användare till en eller flera roller, och varje roll till en eller flera system funktioner.

**ID**: 1145.01 c2system. 1 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare som tilldelats din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger fler än en prenumerations ägare för att få administratörs åtkomst för redundans. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Organisationen främjar utvecklingen och användningen av program som gör att du slipper köra med utökade privilegier och system rutiner för att undvika behovet av att bevilja användare behörighet.

**ID**: 1146.01 c2system. 23-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Externa konton med ägar behörigheter bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externa konton med ägar behörigheter bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Utökade privilegier tilldelas till ett annat användar-ID än de som används för normal företags användning, alla användare har åtkomst till privilegierade tjänster i en enda roll och sådan privilegie rad åtkomst minimeras.

**ID**: 1147.01 c2system. 456-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration.  Föråldrade konton är konton som har blockerats från att logga in. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Organisationen begränsar åtkomsten till privilegierade funktioner och all säkerhetsrelaterad information.

**ID**: 1148.01 c2system. 78-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som "ägare, Contribute", läsare "i stället för anpassade RBAC-roller som är fel känsliga. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös gransknings-och hot modellering |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows-datorer bör uppfylla kraven för "säkerhets alternativ – konton"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-konton för att begränsa lokal konto användning av tomma lösen ord och gäst konto status. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Organisationen underlättar informations delningen genom att göra det möjligt för behöriga användare att fastställa en affärs partners åtkomst när det är möjligt att tillåta detta som definieras av organisationen och genom att använda manuella processer eller automatiserade mekanismer för att hjälpa användare att fatta informations delning/samarbets beslut.

**ID**: 1149.01 c2system. 9-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ha detaljerad filtrering för de åtgärder som användarna kan utföra använder Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes-tjänstekluster och konfigurera relevanta Auktoriseringsprinciper. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>Åtkomst kontroll systemet för system komponenter som lagrar, bearbetar eller överför information som omfattas anges med standard &quot; inställningen Deny-all &quot; .

**ID**: 1150.01 c2system. 10 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanterings portar exponerar din virtuella dator på en hög risk nivå från Internetbaserade attacker. Dessa angrepp försöker att få administratörs behörighet till datorn. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Organisationen begränsar auktoriseringen till privilegierade konton på informations system till en fördefinierad delmängd av användarna.

**ID**: 1151.01 c3system. 1 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du anger upp till tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Organisationen granskar körningen av privilegierade funktioner på informations system och säkerställer att informations system hindrar icke-privilegierade användare från att köra privilegierade funktioner.

**ID**: 1152.01 c3system. 2 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare som tilldelats din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger fler än en prenumerations ägare för att få administratörs åtkomst för redundans. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>All fil system åtkomst som inte uttryckligen krävs är inaktive rad och endast auktoriserade användare får åtkomst till det som uttryckligen krävs för användarens jobb uppgifter.

**ID**: 1153.01 c3system. 35-01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ha detaljerad filtrering för de åtgärder som användarna kan utföra använder Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes-tjänstekluster och konfigurera relevanta Auktoriseringsprinciper. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Leverantörer tillhandahålls med minimalt system och fysisk åtkomst när organisationen bedömer att entreprenörens förmåga att uppfylla sina säkerhets krav och entreprenören samtycker till att följa.

**ID**: 1154.01 c3system. 4 – 01. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du anger upp till tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Användarautentisering för externa anslutningar

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Starka autentiseringsmetoder, till exempel Multi-Factor, RADIUS eller Kerberos (för privilegie rad åtkomst) och CHAP (för kryptering av autentiseringsuppgifter för uppringnings metoder) implementeras för alla externa anslutningar till organisationens nätverk.

**ID**: 1116.01 j1organizational. 145-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Fjärråtkomst av leverantörer och affärs partner (t. ex. för fjärrunderhåll) är inaktiverat/inaktiverat när det inte används.

**ID**: 1117.01 j1organizational. 23-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Organisationer implementerar kryptering (t. ex. &nbsp; VPN-lösningar eller privata rader) och loggar fjärråtkomst till organisationens nätverk av anställda, entreprenörer eller tredje part (t. ex. leverantörer).

**ID**: 1118.01 j2organizational. 124-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Nätverks utrustning kontrol leras för ej förväntade uppringnings funktioner.

**ID**: 1119.01 j2organizational. 3-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Fjärr administrations sessioner är auktoriserade, krypterade och använder ökade säkerhets åtgärder.

**ID**: 1121.01 j3organizational. 2-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Om kryptering inte används för fjärr anslutningar, ger CIO eller dennes utsedda representant ett särskilt skriftligt tillstånd.

**ID**: 1173.01 j1organizational. 6-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Organisationen skyddar trådlös åtkomst till system som innehåller känslig information genom att autentisera både användare och enheter.

**ID**: 1174.01 j1organizational. 7-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Fjärråtkomst till företags information i offentliga nätverk sker bara efter lyckad identifiering och autentisering.

**ID**: 1175.01 j1organizational. 8-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Organisationen kräver en callback-funktion med reautentisering för att verifiera fjärr anslutningar från behöriga platser.

**ID**: 1176.01 j2organizational. 5-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Användar-ID: n som tilldelas till leverantörer granskas i enlighet med organisationens princip för åtkomst granskning, minst en gång per år.

**ID**: 1177.01 j2organizational. 6-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Node Authentication, inklusive kryptografiska metoder (t. ex. dator certifikat), fungerar som ett alternativt sätt att autentisera grupper av fjärranslutna användare där de är anslutna till en säker, delad dator funktion.

**ID**: 1178.01 j2organizational. 7-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>Informations systemet övervakar och styr metoder för fjärråtkomst.

**ID**: 1179.01 j3organizational. 1-01. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Fjärrdiagnostik och konfiguration av port skydd

### <a name="access-to-network-equipment-is-physically-protected"></a>Åtkomst till nätverks utrustning är fysiskt skyddad.

**ID**: 1192.01 l1organizational. 1 – 01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Kontroller för åtkomst till diagnostik-och konfigurations portar omfattar användning av nyckel lås och implementering av stöd procedurer för att kontrol lera fysisk åtkomst till porten.

**ID**: 1193.01 l2organizational. 13-01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanterings portar exponerar din virtuella dator på en hög risk nivå från Internetbaserade attacker. Dessa angrepp försöker att få administratörs behörighet till datorn. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Portar, tjänster och liknande program som är installerade på en dator eller ett nätverks system, som inte särskilt krävs för företags funktioner, är inaktiverade eller borttagna.

**ID**: 1194.01 l2organizational. 2 – 01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för webb program](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Fjärrfelsökning kräver att inkommande portar öppnas i ett webb program. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Organisationen granskar informations systemet inom var 365 (365) dagar för att identifiera och inaktivera onödiga och icke-säkra funktioner, portar, protokoll och/eller tjänster.

**ID**: 1195.01 l3organizational. 1 – 01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för Function-appar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i Function Apps. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Organisationen identifierar obehörigt (svartlistat) program vara i informations systemet, förhindrar program körning i enlighet med en lista över obehöriga (svartlistade) program vara, använder en Tillåt-alla, neka-by-undantags princip för att förhindra körning av känt obehöriga (svartlistade) program vara per år.

**ID**: 1196.01 l3organizational. 24-01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Fjärrfelsökning kräver att inkommande portar öppnas i API Apps. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Organisationen inaktiverar Bluetooth-och peer-to-peer-nätverksprotokoll i informations systemet som bedöms vara onödigt eller icke-säkra.

**ID**: 1197.01 l3organizational. 3 – 01. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera program kontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. På så sätt kan du förstärka dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler, Security Center använda Machine Learning för att analysera programmen som körs på varje dator och föreslå listan över kända säkra program. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Uppdelning i nätverk

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Organisationens säkerhetsgatewayer (t. ex. brand väggar) tillämpar säkerhets principer och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelning mellan interna tråd bundna, interna trådlösa och externa nätverks segment (t. ex. Internet), inklusive DMZs och tvingande principer för åtkomst kontroll för varje domän.

**ID**: 0805.01 m1organizational. 12 – 01. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Event Hub bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar eventuella Händelsehubben som inte är konfigurerade för att använda en tjänst slut punkt för virtuella nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät ska inte konfigureras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett Gateway-undernät har kon figurer ATS med en nätverks säkerhets grupp. Om du tilldelar en nätverks säkerhets grupp till ett Gateway-undernät kommer gatewayen att sluta fungera. |autentiseringsregel |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagrings konton som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Organisationernas nätverk är logiskt och fysiskt segmenterade med en definierad säkerhets perimeter och en graderad uppsättning kontroller, inklusive under nätverk för offentligt tillgängliga system komponenter som är logiskt åtskilda från det interna nätverket, baserat på organisationens krav. trafiken styrs baserat på funktioner som krävs och klassificeringen av data/system baserat på en riskbedömning och deras respektive säkerhets krav.

**ID**: 0806.01 m2organizational. 12356-01. m **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Event Hub bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar eventuella Händelsehubben som inte är konfigurerade för att använda en tjänst slut punkt för virtuella nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät ska inte konfigureras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett Gateway-undernät har kon figurer ATS med en nätverks säkerhets grupp. Om du tilldelar en nätverks säkerhets grupp till ett Gateway-undernät kommer gatewayen att sluta fungera. |autentiseringsregel |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagrings konton som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Nätverk är åtskiljda från nätverk på produktions nivå när du migrerar fysiska servrar, program eller data till virtualiserade servrar.

**ID**: 0894.01 m2organizational. 7 – 01. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Distribuera nätverks övervakare när virtuella nätverk skapas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Den här principen skapar en nätverks övervaknings resurs i regioner med virtuella nätverk. Du måste se till att det finns en resurs grupp med namnet networkWatcherRG som ska användas för att distribuera Network Watcher-instanser. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Event Hub bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar eventuella Händelsehubben som inte är konfigurerade för att använda en tjänst slut punkt för virtuella nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Gateway-undernät ska inte konfigureras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Den här principen nekar om ett Gateway-undernät har kon figurer ATS med en nätverks säkerhets grupp. Om du tilldelar en nätverks säkerhets grupp till ett Gateway-undernät kommer gatewayen att sluta fungera. |autentiseringsregel |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagrings konton som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Nätverks anslutnings kontroll

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Nätverks trafiken kontrol leras i enlighet med organisationens åtkomst kontroll princip via brand vägg och andra nätverksrelaterade begränsningar för varje nätverks åtkomst punkt eller extern telekommunikations tjänsts hanterade gränssnitt.

**ID**: 0809.01 n2organizational. 1234-01. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Information som överförs är skyddad och minst krypterad över öppna, offentliga nätverk.

**ID**: 0810.01 n2organizational. 5-01. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Undantag till trafikflödes principen dokumenteras med ett support uppdrag/affärs behov, varaktigheten för undantag och granskas minst årligen. undantag för trafik flödes princip tas bort när det inte längre stöds av ett explicit uppdrag/affärs behov.

**ID**: 0811.01 n2organizational. 6-01. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Fjärranslutna enheter som upprättar en icke-fjärr anslutning tillåts inte kommunicera med externa resurser (fjär).

**ID**: 0812.01 n2organizational. 8-01. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Möjligheten för användare att ansluta till det interna nätverket är begränsad med hjälp av en princip för nekad-till-standard och Tillåt-vid-undantag på hanterade gränssnitt enligt principen för åtkomst kontroll och kraven för kliniska och affärs program.

**ID**: 0814.01 n1organizational. 12-01. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Den här principen granskar en virtuell dator som är ansluten till ett virtuellt nätverk som inte är godkänd. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Användar identifiering och autentisering

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Organisationen ser till att redundanta användar-ID: n inte utfärdas till andra användare och att alla användare unikt identifieras och autentiseras för både lokal och fjärran sluten åtkomst till informations system.

**ID**: 11109.01 q1organizational. 57-01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Icke-organisatoriska användare (alla andra informations system än organisations användare, t. ex. patienter, kunder, entreprenörer eller utländska medborgare) eller processer som agerar på uppdrag av icke-organisatoriska användare som har bestämt sig för att behöva åtkomst till information som finns på organisationens informations system, identifieras och autentiseras unikt.

**ID**: 11110.01 q1organizational. 6 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>När PKI-baserad autentisering används verifierar informations systemet certifikat genom att skapa och verifiera en certifierings Sök väg till ett godkänt förtroende ankare, inklusive kontrol lera information om certifikat status. tillämpar åtkomst till motsvarande privata nyckel. mappar identiteten till motsvarande konto för personen eller gruppen. och implementerar en lokal cache med återkallnings data som stöder Sök vägs identifiering och verifiering i händelse av att det inte går att komma åt information om återkallade certifikat via nätverket.

**ID**: 11111.01 q2system. 4 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>Informations systemet använder Replay-resistenta autentiseringsmekanismer som temporärt, eng ång slö sen ord eller tidsstämplar för att skydda nätverks åtkomsten för privilegierade konton. och för maskinvaru-tokenbaserad autentisering används mekanismer som uppfyller minimi kraven för token som diskuteras i rikt linjerna för elektronisk autentisering i NIST SP 800-63-2.

**ID**: 11112.01 q2organizational. 67-01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Högst 3 ägare bör anges för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Vi rekommenderar att du anger upp till tre prenumerations ägare för att minska risken för intrång av en komprometterad ägare. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Organisationen kräver att elektroniska signaturer, unika för en individ, inte kan återanvändas av eller omtilldelas till, någon annan.

**ID**: 11208.01 q1organizational. 8 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas fler än en ägare som tilldelats din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Vi rekommenderar att du anger fler än en prenumerations ägare för att få administratörs åtkomst för redundans. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Elektroniska signaturer och handskrivna signaturer som körs på elektroniska poster måste kopplas till sina respektive elektroniska poster.

**ID**: 11210.01 q2organizational. 10 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen administratörer innehåller en eller flera av de medlemmar som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Signerade elektroniska poster skall innehålla information som är kopplad till det läsbara formatet för signering.

**ID**: 11211.01 q2organizational. 11-01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som saknar angivna medlemmar i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen administratörer inte innehåller en eller flera medlemmar som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Användare som utförde privilegierade funktioner (t. ex. system administration) använder separata konton när de utför de privilegierade funktionerna.

**ID**: 1123.01 q1system. 2 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har extra konton i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen administratörer innehåller medlemmar som inte finns med i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Multi-Factor Authentication-metoder används i enlighet med organisations principer (t. ex. för fjärråtkomst till fjärrnätverk).

**ID**: 1125.01 q2system. 1 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen administratörer innehåller en eller flera av de medlemmar som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>När token anges för Multi-Factor Authentication krävs autentisering personligen innan åtkomst beviljas.

**ID**: 1127.01 q2system. 3 – 01. f **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som saknar angivna medlemmar i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen administratörer inte innehåller en eller flera medlemmar som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identifiering av risker relaterade till externa parter

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Det är inte tillåtet att få åtkomst till organisations information och system av externa parter förrän noggrannhet har genomförts, att lämpliga kontroller har genomförts och ett kontrakt/avtal som återspeglar säkerhets kraven är signerade och accepterar deras skyldigheter.

**ID**: 1401.05 i1organizational. 1239-05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Fjärr anslutnings anslutningar mellan organisationen och externa parter är krypterade.

**ID**: 1402.05 i1organizational. 45-05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Åtkomst beviljas till externa parter är begränsad till det minimum som krävs och beviljas endast för den tid som krävs.

**ID**: 1403.05 i1organizational. 67-05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Noggrannhet av den externa parten innehåller intervjuer, dokument granskning, check listor, certifierings granskningar (t. ex. HITRUST) &nbsp; eller andra fjärranslutna medel.

**ID**: 1404.05 i2organizational. 1 – 05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Identifieringen av risker relaterade till åtkomst till externa parter tar hänsyn till en minimal uppsättning särskilt definierade problem.

**ID**: 1418.05 i1organizational. 8-05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Organisationen erhåller tillfredsställande garantier som det finns skälig informations säkerhet för i informations kedjan genom att göra en årlig granskning, som omfattar alla partner/tredje parts-leverantörer som deras informations leverans kedja är beroende av.

**ID**: 1450.05 i2organizational. 2 – 05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Moln tjänst leverantörer utformar och implementerar kontroller för att minimera och innehålla data säkerhets risker genom korrekt uppdelning av uppgifter, rollbaserad åtkomst och åtkomst med minst privilegier för all personal i sin leverans kedja.

**ID**: 1451.05 icsporganizational. 2 – 05. jag **äger**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Gransknings loggning

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>En säker gransknings post skapas för alla aktiviteter i systemet (skapa, läsa, uppdatera, ta bort) som involverar information.

**ID**: 1202.09 aa1system. 1-09. AA- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Azure Data Lake Store ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Granska om det finns några saknade system säkerhets uppdateringar och viktiga uppdateringar som ska installeras för att säkerställa att skalnings uppsättningar för virtuella Windows-och Linux-datorer är säkra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Gransknings poster inkluderar unikt användar-ID, unikt ID för data ämne, funktion som utförs och datum/tid då händelsen utfördes.

**ID**: 1203.09 aa1system. 2-09. AA- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Logic Apps ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Aktiviteter för privilegierade användare (administratörer, operatörer osv.) inkluderar händelsen lyckades eller misslyckades, tid då händelsen inträffade, det konto som är involverad och ytterligare information om händelsen.

**ID**: 1204.09 aa1system. 3-09. AA- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i IoT Hub ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Loggar av meddelanden som skickas och tas emot underhålls inklusive datum, tid, ursprung och mål för meddelandet, men inte innehållet.

**ID**: 1205.09 aa2system. 1-09. AA- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i batch-konton måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Granskning är alltid tillgängligt när systemet är aktivt och spårar viktiga händelser, lyckade/misslyckade data åtkomst, ändringar i systemets säkerhets konfiguration, privilegie rad eller användning, eventuella larm, &nbsp; aktivering och inaktive ring av skydds system (t. ex. A/V och ID), aktivering och inaktive ring av identifierings-och autentiseringsmekanismer samt skapande och borttagning av objekt på objekt.

**ID**: 1206.09 aa2system. 23-09. AA- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Virtual Machine Scale Sets ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Vi rekommenderar att du aktiverar loggar så att aktivitets spårningen kan återskapas när undersökningar krävs vid en incident eller en kompromiss. |AuditIfNotExists, inaktiverat |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Gransknings poster sparas i 90 dagar och äldre gransknings poster arkiveras i ett år.

**ID**: 1207.09 aa2system. 4-09. AA- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Azure Stream Analytics ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Händelsehubben måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Gransknings loggar underhålls för hanterings aktiviteter, start/avstängning av system och program, fil ändringar och säkerhets princip ändringar.

**ID**: 1208.09 aa3system. 1-09. AA- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Sök tjänster måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Service Bus ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>Informations systemet genererar gransknings poster som innehåller följande detaljerade information: (i) fil namn (i) fil namn användes. (II) program eller kommando som används för att starta händelsen; och (III) käll-och mål adresser.

**ID**: 1209.09 aa3system. 2-09. AA- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Diagnostikloggar i App Services ska vara aktive rad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Granska aktivering av diagnostikloggar i appen. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte om en säkerhets incident inträffar eller nätverket komprometteras |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Alla upplysningar om täckt information i eller utanför organisationen loggas, inklusive typ av avslöjande, datum/tid för händelsen, mottagaren och avsändaren.

**ID**: 1210.09 aa3system. 3-09. AA- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska diagnostikinställning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Granska diagnostikinställningar för valda resurs typer |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Resurs loggar i Data Lake Analytics ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Organisationen verifierar var 90 (90) dagar för varje utdrag av täckt information som har registrerats att data raderas eller att användningen fortfarande krävs.

**ID**: 1211.09 aa3system. 4-09. AA- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granskning på SQL Server måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på din SQL Server ska vara aktive rad för att spåra databas aktiviteter i alla databaser på servern och spara dem i en Gransknings logg. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Resurs loggar i Azure Key Vault hanterad HSM måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Om du vill återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras, kan du granska genom att aktivera resurs loggar på hanterade HSM: er. Följ anvisningarna här: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Key Vault ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Övervaka system användning

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Obehöriga fjärr anslutningar till informations systemen övervakas och granskas minst varje kvartal och lämplig åtgärd vidtas om en obehörig anslutning upptäcks.

**ID**: 1120.09 ab3system. 9-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor ska samla in aktivitets loggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor logg profil som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive global. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Organisationen övervakar informations systemet för att identifiera oegentligheter eller avvikelser som visar om systemet fungerar dåligt eller komprometteras och hjälper till att bekräfta att systemet fungerar med optimal, elastisk och säker status.

**ID**: 12100.09 ab2system. 15-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics agenten ska installeras på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Den här principen granskar alla virtuella Windows-och Linux-datorer om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Organisationen anger hur ofta gransknings loggar granskas, hur granskningarna dokumenteras och vilka roller och ansvars områden hos personalen som genomför granskningarna, inklusive de professionella certifieringar eller andra kvalifikationer som krävs.

**ID**: 12101.09 ab1organizational. 3-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics agenten ska installeras på Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Den här principen granskar eventuella Windows/Linux-Virtual Machine Scale Sets om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Organisationen skall regelbundet testa sina övervaknings-och identifierings processer, åtgärda brister och förbättra sina processer.

**ID**: 12102.09 ab1organizational. 4-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om agenten inte är installerad eller om den är installerad men COM-objektet AgentConfigManager. MgmtSvcCfg returnerar att det är registrerat på en annan arbets yta än det ID som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Alla tillämpliga juridiska krav för övervakning av auktoriserad åtkomst och obehöriga åtkomst försök är uppfyllda.

**ID**: 1212.09 ab1system. 1-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en logg profil samlar in loggar för kategorier "Write," Delete "och" Action " |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Automatiserade system som distribueras i hela organisationens miljö används för att övervaka nyckel händelser och avvikande aktivitet och analysera system loggar, vars resultat granskas regelbundet.

**ID**: 1213.09 ab2system. 128-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>Övervakning omfattar privilegierade åtgärder, auktoriserad åtkomst eller obehörig åtkomst, inklusive försök att komma åt inaktiverade konton och system varningar eller-haverier.

**ID**: 1214.09 ab2system. 3456-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor ska samla in aktivitets loggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor logg profil som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive global. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Gransknings-och övervaknings system som används av organisationen stöder gransknings minskning och rapportgenerering.

**ID**: 1215.09 ab2system. 7-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics agenten ska installeras på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Den här principen granskar alla virtuella Windows-och Linux-datorer om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Automatiska system används för att granska övervaknings aktiviteter för säkerhets system (t. ex. IP-adresser/ID) och system poster per dag och identifiera och dokumentera avvikelser.

**ID**: 1216.09 ab3system. 12-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Log Analytics agenten ska installeras på Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Den här principen granskar eventuella Windows/Linux-Virtual Machine Scale Sets om Log Analytics-agenten inte är installerad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Aviseringar genereras för teknisk personal för att analysera och undersöka misstänkt aktivitet eller misstänkta överträdelser.

**ID**: 1217.09 ab3system. 3-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om agenten inte är installerad eller om den är installerad men COM-objektet AgentConfigManager. MgmtSvcCfg returnerar att det är registrerat på en annan arbets yta än det ID som anges i princip parametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>Informations systemet kan bearbeta gransknings poster automatiskt för händelser av intresse baserat på valbara kriterier.

**ID**: 1219.09 ab3system. 10-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en logg profil samlar in loggar för kategorier "Write," Delete "och" Action " |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>Övervakning omfattar inkommande och utgående kommunikation och övervakning av fil integritet.

**ID**: 1220.09 ab3system. 56-09. AB **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Administratörs-och operatörs loggar

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Organisationen ser till att korrekt loggning är aktiverat för att kunna granska administratörs aktiviteter. och granskar system administratören och operatörs loggar regelbundet.

**ID**: 1270.09 ad1system. 12-09.AD **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Ett intrångs identifierings system som hanteras utanför kontrollen av system-och nätverks administratörer används för att övervaka system-och nätverks administrations aktiviteter för efterlevnad.

**ID**: 1271.09 ad1system. 1-09.AD **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Ansvars fördelning

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Separering av uppgifter används för att begränsa risken för obehörig eller oavsiktlig ändring av information och system.

**ID**: 1229.09 c1organizational. 1-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ha detaljerad filtrering för de åtgärder som användarna kan utföra använder Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes-tjänstekluster och konfigurera relevanta Auktoriseringsprinciper. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Ingen enskild person kan komma åt, ändra eller använda informations system utan auktorisering eller identifiering.

**ID**: 1230.09 c2organizational. 1-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som "ägare, Contribute", läsare "i stället för anpassade RBAC-roller som är fel känsliga. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös gransknings-och hot modellering |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Åtkomst för personer som ansvarar för att administrera &nbsp; åtkomst kontroller begränsas till den lägsta som krävs baserat på varje användares roll och ansvar och dessa personer kan inte komma åt gransknings funktioner som är relaterade till dessa kontroller.

**ID**: 1232.09 c3organizational. 12-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för tilldelning av användar rättigheter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin tilldelning av användar rättigheter för att tillåta lokal inloggning, RDP, åtkomst från nätverket och många andra användar aktiviteter. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Säkerhets gransknings aktiviteter är oberoende av varandra.

**ID**: 1276.09 c2organizational. 2-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Roller för anpassade prenumerationer får inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade prenumerations ägare roller. |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Initieringen av en händelse skiljs från sin auktorisering för att minska risken för samverkan.

**ID**: 1277.09 c2organizational. 4-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för "säkerhets alternativ-User Account Control"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-User Account Control för-läge för administratörer, beteendet uppmaning om utökade privilegier och virtualiserade fil-och register Skriv problem. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Organisationen identifierar de uppgifter som kräver separering och definierar de autentiseringsuppgifter som krävs för att stödja separering av uppgifter. och inkompatibla uppgifter åtskiljs över flera användare för att minimera möjligheten för missbruk eller bedrägerier.

**ID**: 1278.09 c2organizational. 56-09. c- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Roller för anpassade prenumerationer får inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade prenumerations ägare roller. |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Kontroller mot skadlig kod

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Virus skydd och antispionprogram installeras, körs och uppdateras på alla slut användar enheter för att utföra regelbundna genomsökningar av systemen för att identifiera och ta bort otillåten program vara. Server miljöer som serverprogram varan för utvecklare uttryckligen rekommenderar att inte installera värdbaserade antivirus program och antispionprogram kan lösa kravet via en nätverks baserad NBMD-lösning (skadlig kod identifiering).

**ID**: 0201.09 j1organizational. 124-09. j **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera program kontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. På så sätt kan du förstärka dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler, Security Center använda Machine Learning för att analysera programmen som körs på varje dator och föreslå listan över kända säkra program. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Den här principen distribuerar ett Microsoft IaaSAntimalware-tillägg med en standard konfiguration när en virtuell dator inte har kon figurer ATS med tillägget för program mot skadlig kod. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Granska förekomsten och hälsan för en Endpoint Protection-lösning på dina virtuella datorers skalnings uppsättningar för att skydda dem mot hot och sårbarheter. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware för Azure bör konfigureras att automatiskt uppdatera skydds-signaturer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Den här principen granskar en virtuell Windows-dator som inte har kon figurer ATS med automatisk uppdatering av Microsoft Antimalware-signaturer för virus skydd. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Övervaka saknade Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection Agent övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Säkerhetskopiera

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Säkerhets kopior av information och program vara görs och test av medie-och återställnings procedurer utförs regelbundet med lämpliga intervall.

**ID**: 1616.09 l1organizational. 16-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Den här principen granskar alla Azure SQL Database med långsiktig Geo-redundant säkerhets kopiering som inte är aktive rad. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>En formell definition av säkerhets kopierings nivån som krävs för varje system är definierad och dokumenterad, inklusive hur varje system kommer att återställas, omfattningen av de data som ska avbildas, frekvensen för avbildningen och varaktigheten för kvarhållning baserat på relevanta avtals-, juridik-, reglerings-och affärs krav.

**ID**: 1617.09 l1organizational. 23-09. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Med Azure Database for MySQL kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Säkerhets kopiorna lagras på en fysiskt säker fjärrplats, på ett tillräckligt avstånd för att det ska vara rimligt att förhindra att data skadas på den primära platsen, och en rimlig fysisk och miljö kontroll görs för att säkerställa att skyddet sker på fjärrplatsen.

**ID**: 1618.09 l1organizational. 45-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Med Azure Database for PostgreSQL kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Inventerings poster för säkerhets kopiorna, inklusive innehåll och aktuell plats, bevaras.

**ID**: 1619.09 l1organizational. 7-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Med Azure Database for MariaDB kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>När säkerhets kopierings tjänsten levereras av tredje part innehåller Service avtalet det detaljerade skyddet för att kontrol lera konfidentialitet, integritet och tillgänglighet för säkerhets kopierings informationen.

**ID**: 1620.09 l1organizational. 8-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Säkerställ att Azure-Virtual Machines skyddas genom att aktivera Azure Backup. Azure Backup är en säker och kostnads effektiv data skydds lösning för Azure. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Automatiserade verktyg används för att spåra alla säkerhets kopior.

**ID**: 1621.09 l2organizational. 1-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Den här principen granskar alla Azure SQL Database med långsiktig Geo-redundant säkerhets kopiering som inte är aktive rad. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Integriteten och säkerheten för säkerhets kopiorna upprätthålls för att säkerställa framtida tillgänglighet och eventuella eventuella tillgänglighets problem med säkerhets kopiorna identifieras och minimeras i händelse av en större katastrof.

**ID**: 1622.09 l2organizational. 23-09. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Med Azure Database for MySQL kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Skyddad information säkerhets kopie ras i krypterat format för att säkerställa konfidentialitet.

**ID**: 1623.09 l2organizational. 4-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Med Azure Database for PostgreSQL kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Organisationen utför stegvisa eller differentiella säkerhets kopieringar varje dag och fullständiga säkerhets kopieringar varje vecka för att separera mediet.

**ID**: 1624.09 l3organizational. 12-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Med Azure Database for MariaDB kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Tre (3) generationer av säkerhets kopior (fullständiga plus alla relaterade säkerhets kopior eller differentiella säkerhets kopior) lagras utanför platsen och säkerhets kopior på plats och från andra platser loggas med namn, datum, tid och åtgärd.

**ID**: 1625.09 l3organizational. 34-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Säkerställ att Azure-Virtual Machines skyddas genom att aktivera Azure Backup. Azure Backup är en säker och kostnads effektiv data skydds lösning för Azure. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Organisationen ser till att en aktuell, hämtnings bar kopia av skyddad information är tillgänglig innan du flyttar servrar.

**ID**: 1626.09 l3organizational. 5-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Med Azure Database for PostgreSQL kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Organisationen testar säkerhets kopierings informationen efter varje säkerhets kopiering för att verifiera medie tillförlitlighet och informations integritet, och minst en gång om året därefter.

**ID**: 1627.09 l3organizational. 6-09. l- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Med Azure Database for MariaDB kan du välja alternativet för redundans för din databas server. Det kan ställas in på en Geo-redundant lagring där data inte bara lagras i den region där servern finns, men som också replikeras till en kopplad region för att tillhandahålla återställnings alternativ i händelse av ett regions haveri. Det går bara att konfigurera Geo-redundant lagring för säkerhets kopiering när servern skapas. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Arbets styrke medlemmarnas roller och ansvars områden i data säkerhets kopierings processen identifieras och meddelas till personalen. Du måste särskilt ta med din egen enhet (BYOD) för att kunna säkerhetskopiera organisations-och/eller klient data på sina enheter.

**ID**: 1699.09 l1organizational. 10-09. l **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Säkerställ att Azure-Virtual Machines skyddas genom att aktivera Azure Backup. Azure Backup är en säker och kostnads effektiv data skydds lösning för Azure. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Nätverks kontroller

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Organisationen övervakar för all auktoriserad och obehörig trådlös åtkomst till informations systemet och förbjuder installation av trådlösa åtkomst punkter (WAPs) om det inte uttryckligen tillåts att skriva av CIO eller dennes utsedda representant.

**ID**: 0858.09 m1organizational. 4-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alla nätverks portar bör vara begränsade för nätverks säkerhets grupper som är kopplade till den virtuella datorn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center har identifierat några av dina nätverks säkerhets gruppers inkommande regler för att vara för tillåtna. Regler för inkommande trafik ska inte tillåta åtkomst från alla eller Internet-intervall. Detta kan göra det möjligt för angripare att rikta in dig på dina resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows-datorer måste uppfylla kraven för "egenskaper för Windows-brandväggen"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin egenskaper för Windows-brandväggen för brand Väggs status, anslutningar, regel hantering och meddelanden. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Organisationen säkerställer säkerheten för information i nätverk, tillgänglighet för nätverks tjänster och informations tjänster som använder nätverket och skyddet av anslutna tjänster från obehörig åtkomst.

**ID**: 0859.09 m1organizational. 78-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Organisationen formellt hanterar utrustning i nätverket, inklusive utrustning i användar områden.

**ID**: 0860.09 m1organizational. 9-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Distribuera diagnostikinställningar för nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Den här principen distribuerar automatiskt diagnostikinställningar till nätverks säkerhets grupper. Ett lagrings konto med namnet {storagePrefixParameter} {NSGLocation} kommer att skapas automatiskt. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Den här principen granskar alla Service Bus som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>För att identifiera och autentisera enheter i lokala nätverk och/eller WAN-nätverk, inklusive trådlösa nätverk, &nbsp; använder informations systemet antingen en (i) delad känd informations lösning eller (II) en organisations verifierings lösning, det exakta valet och styrkan som är beroende av säkerhets kategoriseringen av informations systemet.

**ID**: 0861.09 m2organizational. 67-09. m **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Den här principen granskar alla App Service som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows-datorer bör uppfylla kraven för "säkerhets alternativ – nätverks åtkomst"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-nätverks åtkomst för att inkludera åtkomst för anonyma användare, lokala konton och fjärråtkomst till registret. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Organisationen ser till att informations systemen skyddar konfidentialiteten och integriteten hos överförd information, inklusive vid förberedelse av överföring och under mottagning.

**ID**: 0862.09 m2organizational. 8-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Den här principen granskar alla SQL Server som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Organisationen skapar en brand Väggs konfiguration som begränsar anslutningar mellan icke-betrodda nätverk och eventuella system komponenter i den miljö som omfattas av information. och eventuella ändringar i brand Väggs konfigurationen uppdateras i nätverks diagrammet.

**ID**: 0863.09 m2organizational. 910-09. m **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Event Hub bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar eventuella Händelsehubben som inte är konfigurerade för att använda en tjänst slut punkt för virtuella nätverk. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Användnings begränsningar och implementerings vägledning definieras för VoIP, inklusive auktorisering och övervakning av tjänsten.

**ID**: 0864.09 m2organizational. 12-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Organisationen (i) godkänner anslutningar från informations systemet till andra informations system utanför organisationen genom användning av säkerhets avtal för samtrafik eller andra formella avtal. (II) dokumenten varje anslutning, gränssnittets egenskaper, säkerhets krav och beskaffenheten hos den information som förmedlas, (III) använder en neka alla, Tillåt enligt undantags princip för att tillåta anslutningar från informations systemet till andra informations system utanför organisationen. och (IV) tillämpar en standard-Deny-regel som släpper all trafik via värdbaserade brand väggar eller port filtrerings verktyg på slut punkterna (arbets stationer, servrar osv.), förutom de tjänster och portar som uttryckligen tillåts.

**ID**: 0865.09 m2organizational. 13-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Den här principen granskar alla Key Vault som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Organisationen beskriver grupper, roller och ansvars områden för den logiska hanteringen av nätverks komponenter och säkerställer samordning av och konsekvens i elementen i nätverks infrastrukturen.

**ID**: 0866.09 m3organizational. 1516-09. m **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton bör begränsa nätverks åtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverks åtkomst till lagrings konton bör vara begränsad. Konfigurera nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. För att tillåta anslutningar från vissa Internet-eller lokala klienter kan åtkomst beviljas till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Trådlösa åtkomst punkter placeras på säkra områden och stängs av när de inte används (t. ex. nätter, helger).

**ID**: 0867.09 m3organizational. 17-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Den här principen granskar alla lagrings konton som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Organisationen skapar en brand Väggs konfiguration för att begränsa inkommande och utgående trafik till den som krävs för data miljön som omfattas.

**ID**: 0868.09 m3organizational. 18-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Filerna för routerdatorn skyddas och synkroniseras.

**ID**: 0869.09 m3organizational. 19-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Åtkomst till alla proxyservrar nekas, förutom de värdar, portar och tjänster som uttryckligen krävs.

**ID**: 0870.09 m3organizational. 20-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Auktoritativa DNS-servrar är åtskilda till interna och externa roller.

**ID**: 0871.09 m3organizational. 22-09. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Den här principen granskar alla Container Registry som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Säkerhet för Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Överenskomna tjänster som tillhandahålls av en nätverks tjänst leverantör eller hanterare hanteras formellt och övervakas för att säkerställa att de tillhandahålls på ett säkert sätt.

**ID**: 0835.09 n1organizational. 1-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Data insamlings agenten för nätverks trafik bör installeras på virtuella Windows-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center använder Microsofts beroende agent för att samla in nätverks trafik data från dina virtuella Azure-datorer för att aktivera avancerade funktioner för nätverks skydd, till exempel trafik visualisering på nätverks kartan, nätverks härdnings rekommendationer och vissa nätverks hot. |AuditIfNotExists, inaktiverat |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Virtuella datorer ska migreras till nya Azure Resource Manager-resurser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Använd nya Azure Resource Manager för dina virtuella datorer för att tillhandahålla säkerhets förbättringar, till exempel: starkare åtkomst kontroll (RBAC), bättre granskning, Azure Resource Manager baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Organisationen godkänner och dokumenterar egenskaperna för varje anslutning från ett informations system till andra informations system utanför organisationen.

**ID**: 0836.09. n2Organizational. 1-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Data insamlings agenten för nätverks trafik bör installeras på virtuella Linux-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center använder Microsofts beroende agent för att samla in nätverks trafik data från dina virtuella Azure-datorer för att aktivera avancerade funktioner för nätverks skydd, till exempel trafik visualisering på nätverks kartan, nätverks härdnings rekommendationer och vissa nätverks hot. |AuditIfNotExists, inaktiverat |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Formella avtal med externa informations system leverantörer innehåller vissa skyldigheter för säkerhet och sekretess.

**ID**: 0837.09. n2Organizational. 2-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Organisationen granskar och uppdaterar säkerhets avtalen mellan anslutningarna fort löp ande för att kontrol lera verk ställandet av säkerhets krav.

**ID**: 0885.09 n2organizational. 3-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Data insamlings agenten för nätverks trafik bör installeras på virtuella Linux-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center använder Microsofts beroende agent för att samla in nätverks trafik data från dina virtuella Azure-datorer för att aktivera avancerade funktioner för nätverks skydd, till exempel trafik visualisering på nätverks kartan, nätverks härdnings rekommendationer och vissa nätverks hot. |AuditIfNotExists, inaktiverat |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Organisationen använder och dokument i ett formellt avtal eller annat dokument, antingen i) Tillåt-alla, neka-by-Exception eller, II) neka-alla, Tillåt-by-Exception (prioriterad), princip för att tillåta att vissa informations system ansluter till externa informations system.

**ID**: 0886.09 n2organizational. 4-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Organisationen kräver externa/utlämnade tjänste leverantörer för att identifiera vilka funktioner, portar och protokoll som används för att tillhandahålla externa/utgångna tjänster.

**ID**: 0887.09 n2organizational. 5-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Data insamlings agenten för nätverks trafik bör installeras på virtuella Windows-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center använder Microsofts beroende agent för att samla in nätverks trafik data från dina virtuella Azure-datorer för att aktivera avancerade funktioner för nätverks skydd, till exempel trafik visualisering på nätverks kartan, nätverks härdnings rekommendationer och vissa nätverks hot. |AuditIfNotExists, inaktiverat |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>I kontraktet med den externa/utkontrakterade tjänste leverantören ingår den specifikation som tjänste leverantören ansvarar för att skydda skyddad information som delas.

**ID**: 0888.09 n2organizational. 6-09. n **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Hantering av flyttbara medier

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Organisationen, baserat på data klassificerings nivån, registrerar media (inklusive bärbara datorer) före användningen, placerar rimliga begränsningar för hur sådana medier används och ger en lämplig nivå av fysiskt och logiskt skydd (inklusive kryptering) för medier som innehåller information som omfattas tills den är korrekt förstörd eller sanerad.

**ID**: 0301.09 o1organizational. 123-09. o **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Organisationen skyddar och kontrollerar media som innehåller känslig information under transport utanför kontrollerade områden.

**ID**: 0302.09 o2organizational. 1-09. o- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk kryptering bör tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Digitala och icke-digitala media som kräver begränsad användning och de särskilda skydd som används för att begränsa användningen identifieras.

**ID**: 0303.09 o2organizational. 2-09. o **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ej anslutna diskar ska vara krypterade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Den här principen granskar alla frånkopplade diskar utan kryptering aktiverat. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Organisationen begränsar användningen av skrivbara flyttbara medier och personligt ägda flyttbara media i organisations system.

**ID**: 0304.09 o3organizational. 1-09. o- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Kräv kryptering för Data Lake Store-konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Den här principen säkerställer att kryptering är aktiverat på alla Data Lake Store-konton |autentiseringsregel |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom att implementera transparent datakryptering (TDE) med din egen nyckel får du ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av separering av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering av transparent datakryptering (TDE) med din egen nyckel ger ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av delning av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Information Exchange-principer och-procedurer

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Moln tjänst leverantörer använder en molnbaserad plattform för virtualiseringsplattformen och standardformat för virtualisering (t. ex. Open Virtualization Format OVF) för att säkerställa samverkan och har dokumenterat anpassade ändringar som gjorts i alla hypervisorer som används och alla lösningar specifika Virtualization-hookar som är tillgängliga för kund granskning.

**ID**: 0662.09 scsporganizational. 2-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Organisationen försäkrar sig om flera skydd innan du tillåter användning av informations system för informations utbyte.

**ID**: 0901.09 s1organizational. 1 – 09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS (Cross Origin Resource Sharing) bör inte tillåta att alla resurser har åtkomst till dina webbappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt webb program. Tillåt endast domäner som krävs för att interagera med din webbapp. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Fjärran sluten (extern) åtkomst till organisationens informations till gångar och till gång till externa informations till gångar (för vilka organisationen inte har någon kontroll) baseras på tydliga definierade allmänna villkor.

**ID**: 0902.09 s2organizational. 13-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS bör inte tillåta alla resurser att komma åt dina funktions program](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt din Function-app. Tillåt endast domäner som krävs för att interagera med din Function-app. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Organisationen upprättar allmänna villkor, som är konsekventa med en förtroende relation som har upprättats med andra organisationer som äger, driver och/eller underhåller externa informations system, vilket gör det möjligt för behöriga användare att komma åt informations systemet från externa informations system; och (II) process, lagra eller överför organisations-kontrollerad information med hjälp av externa informations system.

**ID**: 0911.09 s1organizational. 2-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS bör inte tillåta alla resurser åtkomst till din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt din API-app. Tillåt endast domäner som krävs för att interagera med din API-app. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Kryptografi används för att skydda konfidentialiteten och integriteten hos fjärråtkomstanslutningar till det interna nätverket och till externa system.

**ID**: 0912.09 s1organizational. 4-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för webb program](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Fjärrfelsökning kräver att inkommande portar öppnas i ett webb program. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Starka krypterings protokoll används för att skydda skyddad information under överföring över mindre betrodda/öppna offentliga nätverk.

**ID**: 0913.09 s1organizational. 5-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för Function-appar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i Function Apps. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Organisationen säkerställer att kraven på kommunikations skydd, inklusive säkerheten vid utbyte av information, är föremål för granskning av princip utveckling och efterlevnad.

**ID**: 0914.09 s1organizational. 6-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Fjärrfelsökning kräver att inkommande portar öppnas i API Apps. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Organisationen begränsar användningen av organisations kontrollerade flyttbara lagrings medier av behöriga personer på externa informations system.

**ID**: 0915.09 s2organizational. 2-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>Informations systemet förhindrar fjärraktivering av Collaborative Computing-enheter och ger en uttrycklig indikation på användningen av användare som fysiskt finns på enheterna.

**ID**: 0916.09 s2organizational. 4-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS (Cross Origin Resource Sharing) bör inte tillåta att alla resurser har åtkomst till dina webbappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt webb program. Tillåt endast domäner som krävs för att interagera med din webbapp. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Moln tjänst leverantörer använder säker (t. ex. icke-klartext och autentiserade) standardiserade nätverks protokoll för import och export av data och för att hantera tjänsten, och göra ett dokument till konsumenter (klienter) som beskriver relevanta Interoperabilitets-och portabilitet-standarder som ingår.

**ID**: 0960.09 scsporganizational. 1 – 09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[CORS bör inte tillåta alla resurser att komma åt dina funktions program](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt din Function-app. Tillåt endast domäner som krävs för att interagera med din Function-app. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Personalen tränas på rätt sätt i ledande principer och praxis för alla typer av informations utbyte (muntligt, papper och elektroniskt).

**ID**: 1325.09 s1organizational. 3-09. s **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Fjärrfelsökning bör inaktive ras för Function-appar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Fjärrfelsökning kräver att inkommande portar öppnas i Function Apps. Fjärrfelsökning bör inaktive ras. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Online-transaktioner

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Data som ingår i de elektroniska Commerce-och online-transaktionerna kontrol leras för att avgöra om den innehåller information som omfattas.

**ID**: 0943.09 y1organizational. 1-09. y- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Protokoll som används för att kommunicera mellan alla berörda parter skyddas med hjälp av kryptografiska metoder (t. ex. SSL).

**ID**: 0945.09 y1organizational. 3-09. y- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska Windows-datorer som inte innehåller de angivna certifikaten i den betrodda roten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Kräver att nödvändiga komponenter distribueras till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om datorns betrodda rot certifikat arkiv (cert: \ LocalMachine\Root) inte innehåller ett eller flera av de certifikat som anges av princip parametern. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Organisationen kräver att kryptering används mellan, och användningen av elektroniska signaturer av, var och en av de parter som ingår i transaktionen.

**ID**: 0946.09 y2organizational. 14-09. y- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Endast säkra anslutningar till Azure-cachen för Redis ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Granska aktivering av enbart anslutningar via SSL till Azure cache för Redis. Användningen av säkra anslutningar säkerställer autentiseringen mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-, avlyssnings-och session-kapning |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Organisationen ser till att lagringen av transaktions informationen finns utanför alla offentligt tillgängliga miljöer (t. ex. på en lagrings plattform som finns på organisationens intranät) och inte behålls och exponeras på ett lagrings medium som är direkt tillgängligt från Internet.

**ID**: 0947.09 y2organizational. 2-09. y- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Om en betrodd utfärdare används (t. ex. för att kunna utfärda och underhålla digitala signaturer och/eller digitala certifikat), integreras och bäddas säkerheten i hela hela processen för hantering av certifikat/signaturer från slut punkt till slut punkt.

**ID**: 0948.09 y2organizational. 3-09. y- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>De protokoll som används för kommunikation förbättras för att åtgärda eventuella nya säkerhets risker och de uppdaterade versionerna av protokollen antas så snart som möjligt.

**ID**: 0949.09 y2organizational. 5-09. y- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API-appen bör bara vara tillgänglig via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Funktionsapp bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Kontroll över operativa program

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Endast auktoriserade administratörer tillåts implementera godkända uppgraderingar av program vara, program och program bibliotek, baserat på affärs krav och säkerhets aspekter av versionen.

**ID**: 0605.10 h1system. 12-10. h **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade bas linjen kommer att övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows-datorer bör uppfylla kraven för säkerhets alternativ – granskning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-granskning för tvingande gransknings princip under kategori och stängas av om det inte går att logga säkerhets granskningar. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows-datorer bör uppfylla kraven för system gransknings principer-konto hantering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-konto hantering för granskning av program, säkerhet och användar grupp hantering samt andra hanterings händelser. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Program och operativ system har testats för användbarhet, säkerhet och påverkan innan produktionen.

**ID**: 0606.10 h2system. 1 – 10. h **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska sårbarheter i säkerhets konfiguration på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Organisationen använder sitt konfigurations kontroll program för att upprätthålla kontrollen över all implementerad program vara och dess system dokumentation och arkivera tidigare versioner av implementerad program vara och tillhör ande system dokumentation.

**ID**: 0607.10 h2system. 23-10. h **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera program kontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. På så sätt kan du förstärka dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler, Security Center använda Machine Learning för att analysera programmen som körs på varje dator och föreslå listan över kända säkra program. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Ändra kontroll procedurer

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Ansvariga för program system är också ansvariga för den strikta kontrollen (säkerhet) i projekt-eller support miljön och säkerställer att alla föreslagna system ändringar granskas för att kontrol lera att de inte äventyrar säkerheten för antingen systemet eller operativ miljön.

**ID**: 0635.10 k1organizational. 12-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Organisationen formellt adresser, omfattning, roller, ansvar, hanterings åtagande, samordning mellan organisatoriska entiteter och efterlevnad för konfigurations hantering (t. ex. genom principer, standarder, processer).

**ID**: 0636.10 k2organizational. 1-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Organisationen har utvecklat, dokumenterat och implementerat en konfigurations hanterings plan för informations systemet.

**ID**: 0637.10 k2organizational. 2-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Ändringar är formellt kontrollerade, dokumenterade och framtvingade för att minimera skadan av informations system.

**ID**: 0638.10 k2organizational. 34569-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Installations check listor och sårbarhets ökningar används för att verifiera konfigurationen av servrar, arbets stationer, enheter och apparater och se till att konfigurationen uppfyller minimi kraven.

**ID**: 0639.10 k2organizational. 78-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>När utvecklingen är i drift, ingår ändrings kontroll procedurer för att hantera säkerheten i avtalen och kräver särskilt att utvecklaren spårar säkerhets brister och fel lösning inom system-, komponent-, tjänst-och rapport resultat till organisations-definierad personal eller roller.

**ID**: 0640.10 k2organizational. 1012-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Organisationen använder inte automatiska uppdateringar på kritiska system.

**ID**: 0641.10 k2organizational. 11-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Organisationen utvecklar, dokumenterar och underhåller, under konfigurations kontroll, en aktuell bas linje konfiguration av informations systemet och granskar och uppdaterar bas linjen efter behov.

**ID**: 0642.10 k3organizational. 12-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Organisationen (i) upprättar och dokumenterar konfigurations inställningar för informations teknik produkter som används i informations systemet med de senaste säkerhets konfigurations bas linjerna. (II) identifierar, dokumenterar och godkänner undantag från de obligatoriska etablerade konfigurations inställningarna för enskilda komponenter baserat på explicita drifts krav. och (III) övervakar och styr ändringar av konfigurations inställningarna i enlighet med organisations principer och procedurer.

**ID**: 0643.10 k3organizational. 3-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Organisationen använder automatiserade mekanismer för att centralt hantera, tillämpa och kontrol lera konfigurations inställningar. (II) svara på obehöriga ändringar av konfigurations inställningar för nätverks-och systemsäkerhet. och (III) upprätthåller åtkomst begränsningar och granskning av tvingande åtgärder.

**ID**: 0644.10 k3organizational. 4-10. k **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för system gransknings principer-detaljerad spårning](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin system gransknings principer-detaljerad spårning för granskning av DPAPI, process skapande/avslutning, RPC-händelser och PNP-aktivitet. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Kontroll av tekniska sårbarheter

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Tekniska sårbarheter identifieras, utvärderas för risk och korrigeras i rimlig tid.

**ID**: 0709.10 m1organizational. 1-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Granskar virtuella datorer för att identifiera om de kör en lösning för sårbarhets bedömning som stöds. En kärn komponent i varje cyberhot risk-och säkerhets program är identifiering och analys av sårbarheter. Azure Security Center standard pris nivån omfattar sårbarhets skanning för dina virtuella datorer utan extra kostnad. Dessutom kan Security Center distribuera verktyget automatiskt åt dig. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska sårbarheter i säkerhets konfiguration på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade bas linjen kommer att övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Säkerhets risker i SQL-databaser bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Övervaka genomsöknings resultat och rekommendationer för sårbarhets bedömning för att åtgärda databas sårbarheter. |AuditIfNotExists, inaktiverat |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL-hanterad instans som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Granska Azure SQL-servrar som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows-datorer bör uppfylla kraven för säkerhets alternativ-Microsoft-nätverks server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-Microsoft-nätverks server för att inaktivera SMB v1-Server. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Det finns en strikt konfigurations standard för alla system-och nätverks komponenter.

**ID**: 0710.10 m2organizational. 1-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL-hanterad instans som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Ett program för teknisk sårbarhets hantering är att övervaka, utvärdera, rangordna och åtgärda sårbarheter som identifierats i system.

**ID**: 0711.10 m2organizational. 23-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Granskar virtuella datorer för att identifiera om de kör en lösning för sårbarhets bedömning som stöds. En kärn komponent i varje cyberhot risk-och säkerhets program är identifiering och analys av sårbarheter. Azure Security Center standard pris nivån omfattar sårbarhets skanning för dina virtuella datorer utan extra kostnad. Dessutom kan Security Center distribuera verktyget automatiskt åt dig. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Korrigeringsfiler testas och utvärderas innan de installeras.

**ID**: 0713.10 m2organizational. 5-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade bas linjen kommer att övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Hanterings programmet för tekniska sårbarheter utvärderas varje kvartal.

**ID**: 0714.10 m2organizational. 7-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Systemen är lämpligt härdade (t. ex. konfigurerade med endast nödvändiga och säkra tjänster, portar och protokoll aktiverade).

**ID**: 0715.10 m2organizational. 8-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Granska sårbarheter i säkerhets konfiguration på datorer där Docker är installerat och visas som rekommendationer i Azure Security Center. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Organisationen genomför en position granskning av företags säkerhet vid behov, men inte mindre än en gång inom var 365 (365) dagar, i enlighet med organisationens procedurer.

**ID**: 0716.10 m3organizational. 1-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets risker i SQL-databaser bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Övervaka genomsöknings resultat och rekommendationer för sårbarhets bedömning för att åtgärda databas sårbarheter. |AuditIfNotExists, inaktiverat |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>I sårbarhets sökverktyg ingår funktioner för att enkelt uppdatera de genomsökningar som genomsöks med information Systems sårbarhet.

**ID**: 0717.10 m3organizational. 2-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Organisationen söker efter sårbarheter i informations systemet och de program som är värd för att fastställa statusen för fel reparation varje månad (automatiskt) och igen (manuellt eller automatiskt) när nya sårbarheter som kan påverka system-och nätverks miljöer identifieras och rapporteras.

**ID**: 0718.10 m3organizational. 34-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade bas linjen kommer att övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Organisationen uppdaterar listan över informations Systems sårbarheter som genomsöks inom var trettio (30) dagar eller när nya sårbarheter identifieras och rapporteras.

**ID**: 0719.10 m3organizational. 5-10. m **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL-hanterad instans som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Verksamhets kontinuitet och riskbedömning

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Organisationen identifierar de kritiska affärs processerna som kräver affärs kontinuitet.

**ID**: 1634.12 b1organizational. 1 – 12. b **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska virtuella datorer utan haveri beredskap har kon figurer ATS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Granska virtuella datorer som inte har någon katastrof återställning konfigurerad. Om du vill veta mer om haveri beredskap går du till [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Informations säkerhets aspekter av affärs kontinuitet är (i) baserat på identifiering av händelser (eller händelse serier) som kan orsaka avbrott i organisationens kritiska affärs processer (t. ex. utrustnings fel, mänskliga fel, stöld, brand, natur katastrofer i terrorism). (II) följt av en riskbedömning för att fastställa sannolikheten och effekten av sådana avbrott, vad gäller tid, skadans omfattning och återställnings period. (III) utifrån resultaten av riskbedömningen utvecklas en strategi för affärs kontinuitet för att identifiera den övergripande metoden för affärs kontinuitet. och (IV) när denna strategi har skapats tillhandahålls bekräftelsen av hantering och en plan som skapas och godkänns för att implementera denna strategi.

**ID**: 1635.12 b1organizational. 2 – 12. b **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault hanterad HSM bör ha rensnings skyddet aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Skadlig borttagning av en Azure Key Vault hanterad HSM kan leda till permanent data förlust. En skadlig Insider i din organisation kan eventuellt ta bort och rensa Azure Key Vault hanterad HSM. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuk borttagning av Azure Key Vault hanterad HSM. Ingen i din organisation eller Microsoft kommer att kunna rensa Azure Key Vault hanterad HSM under kvarhållningsperioden för mjuk borttagning. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Rensnings skydd måste vara aktiverat för nyckel valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckel valv kan leda till permanent data förlust. En skadlig Insider i din organisation kan potentiellt ta bort och rensa nyckel valv. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuka borttagna nyckel valv. Ingen i din organisation eller Microsoft kommer att kunna rensa nyckel valven under den mjuka borttagnings perioden för kvarhållning. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Analys av affärs påverkan används för att utvärdera konsekvenserna av katastrofer, säkerhets problem, förlust av tjänster och tjänst tillgänglighet.

**ID**: 1637.12 b2organizational. 2 – 12. b **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Windows-datorer bör uppfylla kraven för "säkerhets alternativ – återställnings konsolen"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Windows-datorer ska ha de angivna grupprincip inställningarna i kategorin säkerhets alternativ-återställnings konsolen för att tillåta diskett kopiering och åtkomst till alla enheter och mappar. Den här principen kräver att gäst konfigurations kraven har distribuerats till princip tilldelnings omfånget. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Utvärderingar av affärs kontinuitet (i) genomförs årligen med fullständig medverkan från ägare av affärs resurser och processer. (II) betrakta alla affärs processer och är inte begränsade till informations till gångar, utan innehåller resultaten som är specifika för informations säkerhet. och (III) identifierar, kvantifierar och prioriterar risker mot viktiga affärs mål och kriterier som är relevanta för organisationen, inklusive kritiska resurser, påverkan av avbrott, tillåtna avbrotts tider och återställnings prioriteter.

**ID**: 1638.12 b2organizational. 345-12. b **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska virtuella datorer utan haveri beredskap har kon figurer ATS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Granska virtuella datorer som inte har någon katastrof återställning konfigurerad. Om du vill veta mer om haveri beredskap går du till [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om Azure Policy:

- [Översikt över regelefterlevnad.](../concepts/regulatory-compliance.md)
- Se [initiativ definitions strukturen](../concepts/initiative-definition-structure.md).
- Granska andra exempel i [Azure policy exempel](./index.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
