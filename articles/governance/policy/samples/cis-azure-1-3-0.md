---
title: Information om efterlevnad för CIS Microsoft Azure grunderna benchmark 1.3.0
description: Information om CIS-Microsoft Azure grunder som är ett inbyggt initiativ för 1.3.0-kontroll. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 03/10/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 1d7883a6e7bb8d5732676906895cae06cbe8e12b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632330"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Information om CIS-Microsoft Azure grunderna i benchmark-1.3.0, inbyggt initiativ

I följande artikel beskrivs hur den inbyggda definitionen av den Azure Policy reglerande kraven mappar till **efterlevnad av domäner** och **kontroller** i CIS Microsoft Azure grunderna för benchmark-1.3.0.
Mer information om den här standarden för efterlevnad finns i [CIS Microsoft Azure grunderna benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). För att förstå _ägarskap_, se [Azure policy princip definition](../concepts/definition-structure.md#type) och [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).

Följande mappningar är till CIS- **Microsoft Azure grunderna benchmark 1.3.0** -kontroller. Använd navigeringen till höger om du vill gå direkt till en speciell **domän för efterlevnad**. Många av kontrollerna implementeras med en [Azure policy](../overview.md) initiativ definition. Om du vill granska den fullständiga initiativ definitionen öppnar du **princip** i Azure Portal och väljer sidan **definitioner** .
Leta sedan reda på och välj **CIS-Microsoft Azure grunderna benchmark 1.3.0** -kontroll, inbyggd initiativ definition.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../overview.md) -definitioner.
> Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som refererar till själva princip definitionerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan domäner, kontroller och Azure Policy definitioner för kompatibiliteten kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Se till att Multi-Factor Authentication har Aktiver ATS för alla privilegierade användare

**ID**: CIS Azure 1,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med ägar behörigheter för att förhindra att konton eller resurser överlappar varandra. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Se till att Multi-Factor Authentication har Aktiver ATS för alla icke-privilegierade användare

**ID**: CIS Azure 1,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Läs behörighet för att förhindra en överträdelse av konton eller resurser. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Se till att gäst användare granskas varje månad

**ID**: CIS Azure 1,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Externa konton med ägar behörigheter bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externa konton med ägar behörigheter bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externa konton med Läs behörighet bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externa konton med Läs privilegier bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externa konton med Skriv behörighet bör tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externa konton med Skriv behörighet bör tas bort från din prenumeration för att förhindra oövervakad åtkomst. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Se till att inga anpassade prenumerations ägar roller skapas

**ID**: CIS Azure 1,21- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Roller för anpassade prenumerationer får inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade prenumerations ägare roller. |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Se till att Azure Defender är inställt på på för servrar

**ID**: CIS Azure 2,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för-servrar måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender för-servrar ger skydd mot hot i real tid för Server arbets belastningar och genererar bättre rekommendationer samt aviseringar om misstänkta aktiviteter. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Se till att Azure Defender är inställt på på för App Service

**ID**: CIS Azure 2,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för App Service ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender för App Service utnyttjar molnets skala och den synlighet som Azure har som en moln leverantör, för att övervaka vanliga attacker från webb program. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Se till att Azure Defender är inställt på på för Azure SQL Database-servrar

**ID**: CIS Azure 2,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för Azure SQL Database-servrar måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender för SQL innehåller funktioner för Visa och åtgärder för att minska risken för databas sårbarheter, identifiera avvikande aktiviteter som kan tyda på hot mot SQL-databaser och upptäcka och klassificera känsliga data. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Se till att Azure Defender är inställt på på för SQL-servrar på datorer

**ID**: CIS Azure 2,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för SQL-servrar på datorer ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender för SQL innehåller funktioner för Visa och åtgärder för att minska risken för databas sårbarheter, identifiera avvikande aktiviteter som kan tyda på hot mot SQL-databaser och upptäcka och klassificera känsliga data. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Kontrol lera att Azure Defender är inställt på på för lagring

**ID**: CIS Azure 2,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för lagring ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender för lagring ger upptäckt av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Se till att Azure Defender är inställt på på för Kubernetes

**ID**: CIS Azure 2,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för Kubernetes ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender för Kubernetes tillhandahåller skydd i real tid för miljöer i behållare och genererar aviseringar för misstänkta aktiviteter. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Se till att Azure Defender är inställt på på för behållar register

**ID**: CIS Azure 2,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för behållar register ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender för behållar register tillhandahåller sårbarhets genomsökning av alla avbildningar som hämtats under de senaste 30 dagarna, som skickas till registret eller importeras, och visar detaljerade resultat per bild. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Se till att Azure Defender är inställt på på för Key Vault

**ID**: CIS Azure 2,8- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Defender för Key Vault ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender för Key Vault ger ett ytterligare skydds lager och säkerhets information genom att identifiera ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. |AuditIfNotExists, inaktiverat |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Se till att "automatisk etablering av övervaknings agent" är inställt på "på"

**ID**: CIS Azure 2,11- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Se till att "ytterligare e-postadresser" är konfigurerat med ett e-postmeddelande om säkerhets kontakt

**ID**: CIS Azure 2,13- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |För att se till att de relevanta personerna i din organisation meddelas när det finns en potentiell säkerhets överträdelse i en av dina prenumerationer, ange en säkerhets kontakt för att ta emot e-postaviseringar från Security Center. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Se till att "meddela om aviseringar med följande allvarlighets grad" är inställt på "hög"

**ID**: CIS Azure 2,14- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[E-postavisering om aviseringar med hög allvarlighets grad ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |För att se till att de relevanta personerna i din organisation meddelas när det finns en potentiell säkerhets överträdelse i en av dina prenumerationer, aktivera e-postmeddelanden för aviseringar med hög allvarlighets grad i Security Center. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Lagringskonton

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Se till att "säker överföring krävs" är inställt på "Enabled"

**ID**: CIS Azure 3,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Se till att "offentlig åtkomst nivå" är inställt på privat för BLOB-behållare

**ID**: CIS Azure 3,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontots offentliga åtkomst ska inte tillåtas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data men kan innebära säkerhets risker. För att förhindra data intrång orsakade av oönskad anonym åtkomst rekommenderar Microsoft att förhindra offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. |granska, neka, inaktive rad |[2.0.1-för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Se till att standard regeln för nätverks åtkomst för lagrings konton är inställd på neka

**ID**: CIS Azure 3,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton bör begränsa nätverks åtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverks åtkomst till lagrings konton bör vara begränsad. Konfigurera nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. För att tillåta anslutningar från vissa Internet-eller lokala klienter kan åtkomst beviljas till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Lagrings konton bör begränsa nätverks åtkomsten med hjälp av regler för virtuella nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Skydda dina lagrings konton mot potentiella hot med hjälp av virtuella nätverks regler som önskad metod i stället för IP-baserad filtrering. Om du inaktiverar IP-baserad filtrering hindras offentliga IP-adresser från att komma åt dina lagrings konton. |Granska, neka, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Se till att betrodda Microsoft-tjänster är aktiverat för åtkomst till lagrings kontot

**ID**: CIS Azure 3,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Vissa Microsoft-tjänster som samverkar med lagrings konton fungerar från nätverk som inte kan beviljas åtkomst via nätverks regler. För att hjälpa den här typen av tjänst att fungera som avsedd, tillåter du att uppsättningen betrodda Microsoft-tjänster kringgår nätverks reglerna. Dessa tjänster använder sedan stark autentisering för att komma åt lagrings kontot. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Kontrol lera att lagringen för viktiga data är krypterad med kundhanterad nyckel

**ID**: CIS Azure 3,9- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings konton ska använda kundhanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Skydda ditt lagrings konto med större flexibilitet med Kundhanterade nycklar. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Att använda Kundhanterade nycklar ger ytterligare funktioner för att kontrol lera rotationen av nyckel krypterings nyckeln eller att rensa data kryptografiskt. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Databas tjänster

### <a name="ensure-that-auditing-is-set-to-on"></a>Se till att "granskning" är inställt på "on"

**ID**: CIS Azure 4.1.1- **ägande**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granskning på SQL Server måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på din SQL Server ska vara aktive rad för att spåra databas aktiviteter i alla databaser på servern och spara dem i en Gransknings logg. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Se till att data kryptering är inställt på on på en SQL Database

**ID**: CIS Azure 4.1.2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Kontrol lera att kvarhållning av granskning är större än 90 dagar

**ID**: CIS Azure 4.1.3 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL-servrar bör behålla gransknings data i minst 90 dagar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |I händelse av incidenter rekommenderar vi att du ställer in data kvarhållning för dina SQL Servers gransknings data till minst 90 dagar. Bekräfta att du uppfyller de nödvändiga reglerna för kvarhållning för de regioner där du arbetar. Detta krävs ibland för efterlevnad av regel standarder. |AuditIfNotExists, inaktiverat |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Se till att Avancerat skydd (ATP) på en SQL-Server är inställt på ' Enabled '

**ID**: CIS Azure 4.2.1 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Avancerad data säkerhet ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Granska varje SQL-hanterad instans utan avancerad data säkerhet. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Granska SQL-servrar utan avancerad data säkerhet |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Se till att sårbarhets bedömning (VA) är aktiverat på en SQL-Server genom att ange ett lagrings konto

**ID**: CIS Azure 4.2.2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL-hanterad instans som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Granska Azure SQL-servrar som inte har återkommande genomsökningar aktiverade för sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Kontrol lera att VA-inställningen Skicka skannings rapporter till har kon figurer ATS för en SQL-Server

**ID**: CIS Azure 4.2.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Se till att en e-postadress har angetts för fältet Skicka skannings rapporter till i inställningarna för sårbarhets bedömning. Den här e-postadressen får en sammanfattning av Sök resultatet när en regelbunden sökning körs på SQL-servrar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Se till att tvinga SSL-anslutning är inställt på ENABLEd för PostgreSQL Database Server

**ID**: CIS Azure 4.3.1 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Se till att tvinga SSL-anslutning är inställt på ENABLEd för MySQL-databasserver

**ID**: CIS Azure 4.3.2 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_checkpoints" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4.3.3 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logg kontroll punkter ska vara aktiverade för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_checkpoints inställningen aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_connections" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4.3.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Logg anslutningar ska vara aktiverade för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_connections inställningen aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "log_disconnections" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure 4.3.5 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Från kopplingar ska loggas för PostgreSQL-databas servrar.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att log_disconnections aktive rad. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Se till att Server parametern "connection_throttling" är inställd på "ON" för PostgreSQL-databasserver

**ID**: CIS Azure- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Anslutnings begränsning ska vara aktiverat för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan anslutnings begränsning aktive rad. Den här inställningen aktiverar tillfälligt anslutnings begränsning per IP för alltför många ogiltiga lösen ords inloggnings fel. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Kontrol lera att Azure Active Directory admin har kon figurer ATS

**ID**: CIS Azure 4,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[En Azure Active Directory administratör bör tillhandahållas för SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Granska etablering av en Azure Active Directory administratör för SQL Server för att aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Se till att SQL Servers TDE-skydd krypteras med kundhanterad nyckel

**ID**: CIS Azure 4,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom att implementera transparent datakryptering (TDE) med din egen nyckel får du ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av separering av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering av transparent datakryptering (TDE) med din egen nyckel ger ökad insyn och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av delning av uppgifter. Den här rekommendationen gäller organisationer med ett relaterat krav på efterlevnad. |AuditIfNotExists, inaktiverat |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Loggning och övervakning

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Se till att lagrings behållaren som lagrar aktivitets loggarna inte är offentligt tillgänglig

**ID**: CIS Azure 5.1.3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontots offentliga åtkomst ska inte tillåtas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data men kan innebära säkerhets risker. För att förhindra data intrång orsakade av oönskad anonym åtkomst rekommenderar Microsoft att förhindra offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. |granska, neka, inaktive rad |[2.0.1-för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Se till att lagrings kontot som innehåller behållaren med aktivitets loggar är krypterat med BYOK (Använd din egen nyckel)

**ID**: CIS Azure 5.1.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Lagrings kontot som innehåller behållaren med aktivitets loggar måste vara krypterat med BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Den här principen granskar om det lagrings konto som innehåller behållaren med aktivitets loggar krypteras med BYOK. Principen fungerar bara om lagrings kontot är i samma prenumeration som aktivitets loggarna efter design. Mer information om Azure Storage kryptering i vila finns här [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Se till att loggning för Azure-valv är aktiverat

**ID**: CIS Azure 5.1.5 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Resurs loggar i Key Vault ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Se till att aktivitets logg aviseringen finns för att skapa princip tilldelning

**ID**: CIS Azure 5.2.1 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa princip åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Den här principen granskar vissa princip åtgärder utan att någon aktivitets logg avisering har kon figurer ATS. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Se till att aktivitets logg aviseringen finns för borttagning av princip tilldelning

**ID**: CIS Azure 5.2.2 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa princip åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Den här principen granskar vissa princip åtgärder utan att någon aktivitets logg avisering har kon figurer ATS. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Kontrol lera att det finns en aktivitets logg avisering för att skapa eller uppdatera nätverks säkerhets gruppen

**ID**: CIS Azure 5.2.3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Se till att aktivitets logg aviseringen finns för ta bort nätverks säkerhets grupp

**ID**: CIS Azure 5.2.4 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för regeln skapa eller uppdatera nätverks säkerhets grupp

**ID**: CIS Azure 5.2.5 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för regeln ta bort nätverks säkerhets grupp

**ID**: CIS Azure 5.2.6 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Se till att aktivitets logg aviseringen finns för säkerhets lösningen skapa eller uppdatera

**ID**: CIS Azure 5.2.7 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar vissa säkerhets åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Se till att aktivitets logg aviseringen finns för borttagning av säkerhetslösning

**ID**: CIS Azure 5.2.8 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar vissa säkerhets åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Kontrol lera att det finns en aktivitets logg avisering för att skapa eller uppdatera eller ta bort SQL Server brand Väggs regel

**ID**: CIS Azure 5.2.9 **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar vissa administrativa åtgärder utan att några aktivitets logg aviseringar har kon figurer ATS. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Se till att diagnostikloggar är aktiverade för alla tjänster som stöder det.

**ID**: CIS Azure 5,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Diagnostikloggar i App Services ska vara aktive rad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Granska aktivering av diagnostikloggar i appen. På så sätt kan du återskapa aktivitets spårningar i utrednings syfte om en säkerhets incident inträffar eller nätverket komprometteras |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Resurs loggar i Azure Data Lake Store ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Azure Stream Analytics ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i batch-konton måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Data Lake Analytics ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Händelsehubben måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i IoT Hub ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Key Vault ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte när en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Logic Apps ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Sök tjänster måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Service Bus ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Resurs loggar i Virtual Machine Scale Sets ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Vi rekommenderar att du aktiverar loggar så att aktivitets spårningen kan återskapas när undersökningar krävs vid en incident eller en kompromiss. |AuditIfNotExists, inaktiverat |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Nätverk

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Se till att RDP-åtkomsten är begränsad från Internet

**ID**: CIS Azure 6,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[RDP-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Den här principen granskar eventuella nätverks säkerhets regler som tillåter RDP-åtkomst från Internet |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Se till att SSH-åtkomsten är begränsad från Internet

**ID**: CIS Azure 6,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SSH-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Den här principen granskar eventuella nätverks säkerhets regler som tillåter SSH-åtkomst från Internet |Granskning, inaktive rad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Se till att Network Watcher är aktive rad

**ID**: CIS Azure 6,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Se till att Virtual Machines använder Managed Disks

**ID**: CIS Azure 7,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granska virtuella datorer som inte använder hanterade diskar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Den här principen granskar virtuella datorer som inte använder hanterade diskar |händelse |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Kontrol lera att diskarna OS och data är krypterade med CMK

**ID**: CIS Azure 7,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Disk kryptering bör tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Se till att "icke anslutna diskar" är krypterade med CMK

**ID**: CIS Azure 7,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ej anslutna diskar ska vara krypterade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Den här principen granskar alla frånkopplade diskar utan kryptering aktiverat. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Se till att endast godkända tillägg är installerade

**ID**: CIS Azure 7,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Endast godkända VM-tillägg ska installeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Den här principen styr de tillägg för virtuella datorer som inte är godkända. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Se till att de senaste OS-korrigeringarna för alla Virtual Machines tillämpas

**ID**: CIS Azure 7,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Se till att Endpoint Protection för alla Virtual Machines har installerats

**ID**: CIS Azure 7,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Övervaka saknade Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection Agent övervakas med Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Andra säkerhets aspekter

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Se till att förfallo datumet anges för alla nycklar

**ID**: CIS Azure 8,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault nycklar ska ha ett utgångs datum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Krypterings nycklar ska ha ett definierat förfallo datum och inte vara permanent. Nycklar som är giltiga för alltid ger en potentiell angripare mer tid att kompromettera nyckeln. Vi rekommenderar att du anger förfallo datum för kryptografiska nycklar. |Granska, neka, inaktive rad |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Se till att förfallo datumet har angetts för alla hemligheter

**ID**: CIS Azure 8,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault hemligheter bör ha ett utgångs datum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Hemligheter bör ha ett definierat förfallo datum och inte vara permanent. Hemligheter som är giltiga för alltid ger en potentiell angripare mer tid att kompromissa med dem. Vi rekommenderar att du anger förfallo datum för hemligheter. |Granska, neka, inaktive rad |[1.0.1 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Se till att nyckel valvet är återställnings Bart

**ID**: CIS Azure 8,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rensnings skydd måste vara aktiverat för nyckel valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckel valv kan leda till permanent data förlust. En skadlig Insider i din organisation kan potentiellt ta bort och rensa nyckel valv. Rensnings skyddet skyddar dig från insider attacker genom att tvinga fram en obligatorisk kvarhållningsperiod för mjuka borttagna nyckel valv. Ingen i din organisation eller Microsoft kommer att kunna rensa nyckel valven under den mjuka borttagnings perioden för kvarhållning. |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services

**ID**: CIS Azure 8,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ha detaljerad filtrering för de åtgärder som användarna kan utföra använder Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes-tjänstekluster och konfigurera relevanta Auktoriseringsprinciper. |Granskning, inaktive rad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Se till att App Service autentisering är inställt på Azure App Service

**ID**: CIS Azure 9,1- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Autentisering ska vara aktiverat i API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når API-appen eller autentisera dem som har tokens innan de når API-appen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Autentisering ska aktive ras i din Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når Function-appen eller autentiserar de som har tokens innan de når Function-appen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Autentisering ska vara aktiverat på din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-förfrågningar når webbappen eller autentisera dem som har tokens innan de når webbappen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Se till att webbapp omdirigerar all HTTP-trafik till HTTPS i Azure App Service

**ID**: CIS Azure 9,2- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Webb program bör endast vara tillgängliga via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Se till att webb programmet använder den senaste versionen av TLS-kryptering

**ID**: CIS Azure 9,3- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Se till att webbapp har "klient certifikat (inkommande klient certifikat)" inställd på "på"

**ID**: CIS Azure 9,4- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att API-appen har klient certifikat (inkommande klient certifikat) inställd på](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter som har ett giltigt certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Funktions appar ska ha "klient certifikat (inkommande klient certifikat)" aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Klient certifikat gör att appen kan begära ett certifikat för inkommande begär Anden. Endast klienter med giltiga certifikat kommer att kunna komma åt appen. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Kontrol lera att register med Azure Active Directory är aktiverat på App Service

**ID**: CIS Azure 9,5- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Hanterad identitet ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Hanterad identitet ska användas i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Hanterad identitet ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Använd en hanterad identitet för utökad autentisering |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att PHP-version är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,6- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att PHP-version är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Nyare versioner släpps med jämna mellanrum för PHP-programvara antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Du rekommenderas att använda den senaste PHP-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Se till att PHP-version är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Nyare versioner släpps med jämna mellanrum för PHP-programvara antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste PHP-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att "python-version" är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,7- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att "python-version" är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Se till att "python-version" är den senaste, om den används som en del av Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Att använda den senaste python-versionen för Function-appar rekommenderas för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Se till att "python-version" är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Nyare versioner släpps med jämna mellanrum för python-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att Java-version är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,8- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att Java-version är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Nyare versioner släpps med jämna mellanrum för Java antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste python-versionen för API Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Se till att Java-version är den senaste, om den används som en del av Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Nyare versioner släpps med jämna mellanrum för Java-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste Java-versionen för Function Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Se till att Java-version är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Nyare versioner släpps med jämna mellanrum för Java-program, antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Det rekommenderas att du använder den senaste Java-versionen för Web Apps för att dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Se till att "HTTP-version" är den senaste, om den används för att köra webbappen

**ID**: CIS Azure 9,9- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Se till att "HTTP-version" är den senaste, om den används för att köra API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Se till att "HTTP-version" är den senaste, om den används för att köra Function-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Se till att "HTTP-version" är den senaste, om den används för att köra webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Nyare versioner släpps med jämna mellanrum för HTTP antingen på grund av säkerhets brister eller för att inkludera ytterligare funktioner. Med den senaste HTTP-versionen för Web Apps kan du dra nytta av säkerhets korrigeringar, om det finns några, och/eller nya funktioner i den nyare versionen. Den här principen gäller för närvarande bara för Linux-webbappar. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Se till att FTP-distributioner är inaktiverade

**ID**: CIS Azure 9,10- **ägarskap**: kund

|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[FTPS ska bara krävas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Aktivera FTPS-tvång för förbättrad säkerhet |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS måste anges i Funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Aktivera FTPS-tvång för förbättrad säkerhet |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS måste anges i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Aktivera FTPS-tvång för förbättrad säkerhet |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om Azure Policy:

- [Översikt över regelefterlevnad.](../concepts/regulatory-compliance.md)
- Se [initiativ definitions strukturen](../concepts/initiative-definition-structure.md).
- Granska andra exempel i [Azure policy exempel](./index.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
