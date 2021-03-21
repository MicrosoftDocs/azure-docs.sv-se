---
title: Information om efterlevnad för CIS Microsoft Azure grunderna benchmark 1.1.0
description: Information om CIS-Microsoft Azure grunder som är ett inbyggt initiativ för 1.1.0-kontroll. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 03/17/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 82ecd64ca2642b2f1f0acc9bbb5bf3836f34d8bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604768"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-110-regulatory-compliance-built-in-initiative"></a>Information om CIS-Microsoft Azure grunderna i benchmark-1.1.0, inbyggt initiativ

I följande artikel beskrivs hur den inbyggda definitionen av den Azure Policy reglerande kraven mappar till **efterlevnad av domäner** och **kontroller** i CIS Microsoft Azure grunderna för benchmark-1.1.0.
Mer information om den här standarden för efterlevnad finns i [CIS Microsoft Azure grunderna benchmark 1.1.0](https://www.cisecurity.org/benchmark/azure/). För att förstå _ägarskap_, se [Azure policy princip definition](../concepts/definition-structure.md#type) och [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).

Följande mappningar är till CIS- **Microsoft Azure grunderna benchmark 1.1.0** -kontroller. Använd navigeringen till höger om du vill gå direkt till en speciell **domän för efterlevnad**. Många av kontrollerna implementeras med en [Azure policy](../overview.md) initiativ definition. Om du vill granska den fullständiga initiativ definitionen öppnar du **princip** i Azure Portal och väljer sidan **definitioner** .
Leta sedan reda på och välj **CIS-Microsoft Azure grunderna benchmark 1.1.0** -kontroll, inbyggd initiativ definition.

Det här inbyggda initiativet distribueras som en del av [Microsoft Azure CIS-exemplet för 1.1.0-test](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../overview.md) -definitioner.
> Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som refererar till själva princip definitionerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan domäner, kontroller och Azure Policy definitioner för kompatibiliteten kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json).

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Se till att Multi-Factor Authentication har Aktiver ATS för alla privilegierade användare

**ID**: CIS Azure 1,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Se till att Multi-Factor Authentication har Aktiver ATS för alla icke-privilegierade användare

**ID**: CIS Azure 1,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Se till att det inte finns några gäst användare

**ID**: CIS Azure 1,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Externa konton med ägar behörigheter bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externa konton med ägar behörigheter bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externa konton med Läs behörighet bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externa konton med Läs privilegier bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externa konton med Skriv behörighet bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externa konton med Skriv behörighet bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Se till att inga anpassade prenumerations ägar roller skapas

**ID**: CIS Azure 1,23- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Roller för anpassade prenumerationer får inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade prenumerations ägare roller. |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Se till att standard pris nivån är vald

**ID**: CIS Azure 2,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Security Center standard pris nivå ska väljas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1181c5f-672a-477a-979a-7d58aa086233) |Standard pris nivån möjliggör hot identifiering för nätverk och virtuella datorer, vilket ger Hot information, avvikelse identifiering och beteende analys i Azure Security Center |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Se till att "automatisk etablering av övervaknings agent" är inställt på "på"

**ID**: CIS Azure 2,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Se till att ASC standard princip inställningen "övervaka system uppdateringar" inte är inaktive rad

**ID**: CIS Azure 2,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Se till att standard princip inställningen för ASC "övervaka OS-sårbarheter" inte är inaktive rad

**ID**: CIS Azure 2,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servrar som inte uppfyller den konfigurerade bas linjen kommer att övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Se till att ASC standard princip inställning "övervaka Endpoint Protection" inte är inaktive rad

**ID**: CIS Azure 2,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Övervaka saknade Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection Agent övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Kontrol lera att ASC standard princip inställningen "övervaka disk kryptering" inte är inaktive rad

**ID**: CIS Azure 2,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk kryptering bör tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Se till att standard princip inställningen för ASC "övervaka nätverks säkerhets grupper" inte är inaktive rad

**ID**: CIS Azure 2,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyserar trafik mönstren för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper som minskar risken för potentiella attacker |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Se till att ASC-standardprincip inställningen "Aktivera nästa generations brand vägg (NGFW) övervakning" inte är inaktive rad "

**ID**: CIS Azure 2,9- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverks säkerhets grupper (NSG). Läs mer om hur du styr trafik med NSG: er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Undernät ska associeras med en nätverks säkerhets grupp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Skydda ditt undernät mot potentiella hot genom att begränsa åtkomsten till det med en nätverks säkerhets grupp (NSG). NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till ditt undernät. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Se till att ASC standard princip inställningen "övervaka sårbarhets bedömning" inte är inaktive rad

**ID**: CIS Azure 2,10- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Granskar virtuella datorer för att identifiera om de kör en lösning för sårbarhets bedömning som stöds. En kärn komponent i varje cyberhot risk-och säkerhets program är identifiering och analys av sårbarheter. Azure Security Center standard pris nivån omfattar sårbarhets skanning för dina virtuella datorer utan extra kostnad. Dessutom kan Security Center distribuera verktyget automatiskt åt dig. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Se till att standard princip inställningen för ASC övervaka JIT-nätverksanslutning inte är inaktive rad

**ID**: CIS Azure 2,12- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig åtkomst till nätverks åtkomst (JIT) kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Se till att standard princip inställningen för ASC "övervaka adaptiv program vit listning" inte är inaktive rad

**ID**: CIS Azure 2,13- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Aktivera program kontroller för att definiera listan över kända säkra program som körs på dina datorer och varna dig när andra program körs. På så sätt kan du förstärka dina datorer mot skadlig kod. För att förenkla processen med att konfigurera och underhålla dina regler, Security Center använda Machine Learning för att analysera programmen som körs på varje dator och föreslå listan över kända säkra program. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Se till att standard princip inställningen för ASC övervaka SQL-granskning är inaktive rad

**ID**: CIS Azure 2,14- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granskning på SQL Server måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på din SQL Server ska vara aktive rad för att spåra databas aktiviteter i alla databaser på servern och spara dem i en Gransknings logg. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Se till att standard princip inställningen för ASC "övervaka SQL-kryptering" inte är inaktive rad

**ID**: CIS Azure 2,15- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Kontrol lera att e-postmeddelandena för säkerhets kontakter har angetts

**ID**: CIS Azure 2,16- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |För att se till att de relevanta personerna i din organisation meddelas när det finns en potentiell säkerhets överträdelse i en av dina prenumerationer, ange en säkerhets kontakt för att ta emot e-postaviseringar från Security Center. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Se till att "skicka e-postmeddelande om aviseringar med hög allvarlighets grad" är inställt på "on"

**ID**: CIS Azure 2,18- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[E-postavisering om aviseringar med hög allvarlighets grad ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |För att se till att de relevanta personerna i din organisation meddelas när det finns en potentiell säkerhets överträdelse i en av dina prenumerationer, aktivera e-postmeddelanden för aviseringar med hög allvarlighets grad i Security Center. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Se till att "skicka e-post även till prenumerations ägare" är inställt på "på"

**ID**: CIS Azure 2,19- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[E-postmeddelande till Prenumerationens ägare för aviseringar med hög allvarlighets grad måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |För att se till att dina prenumerations ägare meddelas när det finns en potentiell säkerhets överträdelse i prenumerationen anger du e-postaviseringar till prenumerations ägare för aviseringar med hög allvarlighets grad i Security Center. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Lagringskonton

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Se till att "säker överföring krävs" är inställt på "Enabled"

**ID**: CIS Azure 3,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Se till att "offentlig åtkomst nivå" är inställt på privat för BLOB-behållare

**ID**: CIS Azure 3,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontots offentliga åtkomst ska inte tillåtas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data men kan innebära säkerhets risker. För att förhindra data intrång orsakade av oönskad anonym åtkomst rekommenderar Microsoft att förhindra offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. |granska, neka, inaktive rad |[2.0.1-för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Se till att standard regeln för nätverks åtkomst för lagrings konton är inställd på neka

**ID**: CIS Azure 3,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton bör begränsa nätverks åtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverks åtkomst till lagrings konton bör vara begränsad. Konfigurera nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. För att tillåta anslutningar från vissa Internet-eller lokala klienter kan åtkomst beviljas till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Se till att betrodda Microsoft-tjänster är aktiverat för åtkomst till lagrings kontot

**ID**: CIS Azure 3,8- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Vissa Microsoft-tjänster som samverkar med lagrings konton fungerar från nätverk som inte kan beviljas åtkomst via nätverks regler. För att hjälpa den här typen av tjänst att fungera som avsedd, tillåter du att uppsättningen betrodda Microsoft-tjänster kringgår nätverks reglerna. Dessa tjänster använder sedan stark autentisering för att komma åt lagrings kontot. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Databas tjänster

### <a name="ensure-that-auditing-is-set-to-on"></a>Se till att "granskning" är inställt på "on"

**ID**: CIS Azure 4,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granskning på SQL Server måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på din SQL Server ska vara aktive rad för att spåra databas aktiviteter i alla databaser på servern och spara dem i en Gransknings logg. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Kontrol lera att AuditActionGroups i gransknings principen för en SQL Server är korrekt inställd

**ID**: CIS Azure 4,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inställningarna för SQL-granskning ska ha Action-Groups kon figurer ATS för att samla in kritiska aktiviteter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Egenskapen AuditActionsAndGroups ska innehålla minst SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP för att säkerställa en grundlig gransknings loggning |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Kontrol lera att kvarhållning av granskning är större än 90 dagar

**ID**: CIS Azure 4,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL-servrar bör behålla gransknings data i minst 90 dagar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |I händelse av incidenter rekommenderar vi att du ställer in data kvarhållning för dina SQL Servers gransknings data till minst 90 dagar. Bekräfta att du uppfyller de nödvändiga reglerna för kvarhållning för de regioner där du arbetar. Detta krävs ibland för efterlevnad av regel standarder. |AuditIfNotExists, inaktiverat |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Kontrol lera att "avancerad data säkerhet" på en SQL-Server är inställt på "på"

**ID**: CIS Azure 4,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Avancerad data säkerhet ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Granska varje SQL-hanterad instans utan avancerad data säkerhet. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Granska SQL-servrar utan avancerad data säkerhet |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Kontrol lera att Azure Active Directory admin har kon figurer ATS

**ID**: CIS Azure 4,8- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[En Azure Active Directory administratör bör tillhandahållas för SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Granska etablering av en Azure Active Directory administratör för SQL Server för att aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Se till att data kryptering är inställt på on på en SQL Database

**ID**: CIS Azure 4,9- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Se till att SQL Servers TDE-skydd krypteras med BYOK (Använd din egen nyckel)

**ID**: CIS Azure 4,10- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom att implementera transparent datakryptering (TDE) med din egen nyckel får du ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av separering av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering av transparent datakryptering (TDE) med din egen nyckel ger ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av delning av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Se till att tvinga SSL-anslutning är inställt på ENABLEd för MySQL-databasserver

**ID**: CIS Azure 4,11- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_checkpoints" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4,12- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logg kontroll punkter ska vara aktiverade för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_checkpoints inställningen aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Se till att tvinga SSL-anslutning är inställt på ENABLEd för PostgreSQL Database Server

**ID**: CIS Azure 4,13- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_connections" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4,14- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logg anslutningar ska vara aktiverade för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_connections inställningen aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_disconnections" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4,15- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Från kopplingar ska loggas för PostgreSQL-databas servrar.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_disconnections aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "connection_throttling" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4,17- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anslutnings begränsning ska vara aktiverat för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan anslutnings begränsning aktive rad. Den här inställningen aktiverar tillfälligt anslutnings begränsning per IP för alltför många ogiltiga lösen ords inloggnings fel. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Loggning och övervakning

### <a name="ensure-that-a-log-profile-exists"></a>Kontrol lera att det finns en logg profil

**ID**: CIS Azure 5.1.1- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure-prenumerationer ska ha en logg profil för aktivitets loggen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Den här principen säkerställer om en logg profil är aktive rad för att exportera aktivitets loggar. Den granskar om det inte finns någon logg profil som har skapats för att exportera loggarna antingen till ett lagrings konto eller till en händelsehubben. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Kontrol lera att kvarhållning av aktivitets logg har angetts till 365 dagar eller mer

**ID**: CIS Azure 5.1.2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aktivitets loggen ska behållas i minst ett år](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Den här principen granskar aktivitets loggen om kvarhållning inte har angetts för 365 dagar eller för alltid (kvarhållning dagar har angetts till 0). |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Se till att gransknings profilen fångar alla aktiviteter

**ID**: CIS Azure 5.1.3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en logg profil samlar in loggar för kategorier "Write," Delete "och" Action " |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Se till att logg profilen fångar in aktivitets loggar för alla regioner, inklusive global

**ID**: CIS Azure 5.1.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor ska samla in aktivitets loggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor logg profil som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive global. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Se till att lagrings behållaren som lagrar aktivitets loggarna inte är offentligt tillgänglig

**ID**: CIS Azure 5.1.5 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontots offentliga åtkomst ska inte tillåtas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data men kan innebära säkerhets risker. För att förhindra data intrång orsakade av oönskad anonym åtkomst rekommenderar Microsoft att förhindra offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. |granska, neka, inaktive rad |[2.0.1-för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Se till att lagrings kontot som innehåller behållaren med aktivitets loggar är krypterat med BYOK (Använd din egen nyckel)

**ID**: CIS Azure 5.1.6 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontot som innehåller behållaren med aktivitets loggar måste vara krypterat med BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Den här principen granskar om det lagrings konto som innehåller behållaren med aktivitets loggar krypteras med BYOK. Principen fungerar bara om lagrings kontot är i samma prenumeration som aktivitets loggarna efter design. Mer information om Azure Storage kryptering i vila finns här [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Se till att loggning för Azure-valv är aktiverat

**ID**: CIS Azure 5.1.7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Azure Key Vault hanterad HSM måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Om du vill återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras, kan du granska genom att aktivera resurs loggar på hanterade HSM: er. Följ anvisningarna här: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Key Vault ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Se till att aktivitets logg aviseringen finns för att skapa princip tilldelning

**ID**: CIS Azure 5.2.1 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa princip åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Den här principen granskar vissa princip åtgärder utan att någon aktivitets logg avisering har kon figurer ATS. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Kontrol lera att det finns en aktivitets logg avisering för att skapa eller uppdatera nätverks säkerhets gruppen

**ID**: CIS Azure 5.2.2 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Se till att aktivitets logg aviseringen finns för ta bort nätverks säkerhets grupp

**ID**: CIS Azure 5.2.3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för regeln skapa eller uppdatera nätverks säkerhets grupp

**ID**: CIS Azure 5.2.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för regeln ta bort nätverks säkerhets grupp

**ID**: CIS Azure 5.2.5 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Se till att aktivitets logg aviseringen finns för säkerhets lösningen skapa eller uppdatera

**ID**: CIS Azure 5.2.6 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar vissa säkerhets åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Se till att aktivitets logg aviseringen finns för borttagning av säkerhetslösning

**ID**: CIS Azure 5.2.7 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar vissa säkerhets åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för att skapa eller uppdatera eller ta bort SQL Server brand Väggs regel

**ID**: CIS Azure 5.2.8 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Se till att aktivitets logg aviseringen finns för säkerhets princip för uppdatering

**ID**: CIS Azure 5.2.9 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar vissa säkerhets åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Nätverk

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Se till att RDP-åtkomsten är begränsad från Internet

**ID**: CIS Azure 6,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[RDP-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Den här principen granskar eventuella nätverks säkerhets regler som tillåter RDP-åtkomst från Internet |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Se till att SSH-åtkomsten är begränsad från Internet

**ID**: CIS Azure 6,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SSH-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Den här principen granskar eventuella nätverks säkerhets regler som tillåter SSH-åtkomst från Internet |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Se till att Network Watcher är aktive rad

**ID**: CIS Azure 6,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Kontrol lera att OS-disken är krypterad

**ID**: CIS Azure 7,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk kryptering bör tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Kontrol lera att data diskar är krypterade

**ID**: CIS Azure 7,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk kryptering bör tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Kontrol lera att "ej anslutna diskar" är krypterade

**ID**: CIS Azure 7,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ej anslutna diskar ska vara krypterade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Den här principen granskar alla frånkopplade diskar utan kryptering aktiverat. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Se till att endast godkända tillägg är installerade

**ID**: CIS Azure 7,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Endast godkända VM-tillägg ska installeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Den här principen styr de tillägg för virtuella datorer som inte är godkända. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Se till att de senaste OS-korrigeringarna för alla Virtual Machines tillämpas

**ID**: CIS Azure 7,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Se till att Endpoint Protection för alla Virtual Machines har installerats

**ID**: CIS Azure 7,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Övervaka saknade Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection Agent övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Andra säkerhets aspekter

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Se till att förfallo datumet anges för alla nycklar

**ID**: CIS Azure 8,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault nycklar ska ha ett utgångs datum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Krypterings nycklar ska ha ett definierat förfallo datum och inte vara permanent. Nycklar som är giltiga för alltid ger en potentiell angripare mer tid att kompromettera nyckeln. Vi rekommenderar att du anger förfallo datum för kryptografiska nycklar. |Granska, neka, inaktive rad |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Se till att förfallo datumet har angetts för alla hemligheter

**ID**: CIS Azure 8,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault hemligheter bör ha ett utgångs datum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Hemligheter bör ha ett definierat förfallo datum och inte vara permanent. Hemligheter som är giltiga för alltid ger en potentiell angripare mer tid att kompromissa med dem. Vi rekommenderar att du anger förfallo datum för hemligheter. |Granska, neka, inaktive rad |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Se till att nyckel valvet är återställnings Bart

**ID**: CIS Azure 8,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Key Vault hanterad HSM bör ha rensnings skyddet aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Skadlig borttagning av en Azure Key Vault hanterad HSM kan leda till permanent data förlust. En skadlig Insider i din organisation kan eventuellt ta bort och rensa Azure Key Vault hanterad HSM. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuk borttagning av Azure Key Vault hanterad HSM. Ingen i din organisation eller Microsoft kommer att kunna rensa Azure Key Vault hanterad HSM under kvarhållningsperioden för mjuk borttagning. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Rensnings skydd måste vara aktiverat för nyckel valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckel valv kan leda till permanent data förlust. En skadlig Insider i din organisation kan potentiellt ta bort och rensa nyckel valv. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuka borttagna nyckel valv. Ingen i din organisation eller Microsoft kommer att kunna rensa nyckel valven under den mjuka borttagnings perioden för kvarhållning. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services

**ID**: CIS Azure 8,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ha detaljerad filtrering för de åtgärder som användarna kan utföra använder Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes-tjänstekluster och konfigurera relevanta Auktoriseringsprinciper. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Se till att App Service autentisering är inställt på Azure App Service

**ID**: CIS Azure 9,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Autentisering ska vara aktiverat i API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når API-appen eller autentisera dem som har tokens innan de når API-appen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Autentisering ska aktive ras i din Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når Function-appen eller autentiserar de som har tokens innan de når Function-appen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Autentisering ska vara aktiverat på din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når webbappen eller autentisera dem som har tokens innan de når webbappen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Se till att webbapp omdirigerar all HTTP-trafik till HTTPS i Azure App Service

**ID**: CIS Azure 9,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Se till att webb programmet använder den senaste versionen av TLS-kryptering

**ID**: CIS Azure 9,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Se till att webbapp har "klient certifikat (inkommande klient certifikat)" inställd på "på"

**ID**: CIS Azure 9,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att API-appen har klient certifikat (inkommande klient certifikat) inställd på](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Funktions appar ska ha "klient certifikat (inkommande klient certifikat)" aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter med giltiga certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Kontrol lera att register med Azure Active Directory är aktiverat på App Service

**ID**: CIS Azure 9,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterad identitet ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Hanterad identitet ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Hanterad identitet ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att PHP-version är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att PHP-version är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Nyare versioner släpps med jämna mellanrum för PHP-programvara antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Du rekommenderas att använda den senaste PHP-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Se till att PHP-version är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Nyare versioner släpps med jämna mellanrum för PHP-programvara antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste PHP-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att "python-version" är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,8- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att "python-version" är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Se till att "python-version" är den senaste, om den används som en del av Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Att använda den senaste python-versionen för Function-appar rekommenderas för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Se till att "python-version" är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att Java-version är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,9- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att Java-version är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Nyare versioner släpps med jämna mellanrum för Java antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Se till att Java-version är den senaste, om den används som en del av Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Nyare versioner släpps med jämna mellanrum för Java-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste Java-versionen för Function Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Se till att Java-version är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Nyare versioner släpps med jämna mellanrum för Java-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste Java-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att "HTTP-version" är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,10- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att "HTTP-version" är den senaste, om den används för att köra API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Se till att "HTTP-version" är den senaste, om den används för att köra Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Se till att "HTTP-version" är den senaste, om den används för att köra webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om Azure Policy:

- [Översikt över regelefterlevnad.](../concepts/regulatory-compliance.md)
- Se [initiativ definitions strukturen](../concepts/initiative-definition-structure.md).
- Granska andra exempel i [Azure policy exempel](./index.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
