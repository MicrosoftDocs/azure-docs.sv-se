---
title: Information om regelefterlevnad för CIS Microsoft Azure Foundations Benchmark 1.3.0
description: Information om det inbyggda initiativet CIS Microsoft Azure Foundations Benchmark 1.3.0 Regulatory Compliance. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 14a93281eb93bee1bf6ecd15da8811088a692c29
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865629"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Information om DET inbyggda initiativet CIS Microsoft Azure Foundations Benchmark 1.3.0 Regulatory Compliance

Följande artikel beskriver hur den inbyggda initiativdefinitionen Azure Policy  regelefterlevnad  mappar till efterlevnadsdomäner och kontroller i CIS Microsoft Azure Foundations Benchmark 1.3.0.
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). Information om _ägarskap_ finns [i Azure Policy principdefinition](../concepts/definition-structure.md#type) [och Delat ansvar i molnet.](../../../security/fundamentals/shared-responsibility.md)

Följande mappningar är till **CIS-kontrollerna Microsoft Azure Foundations Benchmark 1.3.0.** Använd navigeringen till höger för att gå direkt till en specifik **efterlevnadsdomän.** Många av kontrollerna implementeras med en [Azure Policy](../overview.md) initiativdefinition. Om du vill granska den fullständiga **initiativdefinitionen** öppnar du Princip i Azure Portal och väljer **sidan** Definitioner.
Leta sedan upp och välj **den inbyggda initiativdefinitionen CIS Microsoft Azure Foundations Benchmark v1.3.0** Regulatory Compliance.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure Policy](../overview.md) definitioner.
> Dessa principer kan hjälpa dig [att utvärdera efterlevnaden](../how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte någon en-till-en-matchning eller fullständig matchning mellan en kontroll och en eller flera principer. Därför **refererar Kompatibel i** Azure Policy endast till själva principdefinitionerna. Detta säkerställer inte att du är helt kompatibel med alla krav för en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte hanteras av några Azure Policy definitioner just nu. Därför är efterlevnad i Azure Policy endast en partiell vy över din övergripande efterlevnadsstatus. Associationerna mellan efterlevnadsdomäner, kontroller och Azure Policy för den här efterlevnadsstandarden kan ändras med tiden. Om du vill visa ändringshistoriken kan du gå [till GitHub Commit History (Genomförandehistorik för GitHub).](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json)

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Se till att multifaktorautentisering är aktiverat för alla privilegierade användare

**ID:** CIS Azure 1.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med ägarbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Se till att multifaktorautentisering är aktiverat för alla icke-privilegierade användare

**ID:** CIS Azure 1.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med läsbehörighet för att förhindra intrång i konton eller resurser. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Se till att gästanvändare granskas varje månad

**ID:** CIS Azure 1.3-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Externa konton med ägarbehörighet ska tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externa konton med ägarbehörighet bör tas bort från prenumerationen för att förhindra oövervakad åtkomst. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externa konton med läsbehörighet ska tas bort från din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externa konton med läsbehörighet bör tas bort från prenumerationen för att förhindra oövervakad åtkomst. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externa konton med skrivbehörighet ska tas bort från prenumerationen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externa konton med skrivbehörighet bör tas bort från prenumerationen för att förhindra oövervakad åtkomst. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Se till att inga anpassade prenumerationsägaresroller skapas

**ID:** CIS Azure 1.21-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anpassade prenumerationsägaresroller bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade ägarroller för prenumerationer. |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Kontrollera att Azure Defender är inställt på På för servrar

**ID:** CIS Azure 2.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för servrar ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender för servrar ger skydd mot hot i realtid för serverarbetsbelastningar och genererar härdningsrekommendationer samt aviseringar om misstänkta aktiviteter. |AuditIfNotExists, inaktiverad |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Kontrollera att Azure Defender är inställt på På för App Service

**ID:** CIS Azure 2.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för App Service ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender för App Service utnyttjar molnets skala och den synlighet som Azure har som molnleverantör för att övervaka vanliga webbappattacker. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Kontrollera att Azure Defender är inställt på På för Azure SQL databasservrar

**ID:** CIS Azure 2.3-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för Azure SQL Database-servrar ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender för SQL innehåller funktioner för att visa och åtgärda potentiella säkerhetsrisker i databasen, identifiera avvikande aktiviteter som kan tyda på hot mot SQL-databaser samt identifiera och klassificera känsliga data. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Kontrollera att Azure Defender är inställt på På för SQL-servrar på datorer

**ID:** CIS Azure 2.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för SQL-servrar på datorer ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender för SQL innehåller funktioner för att visa och åtgärda potentiella säkerhetsrisker i databasen, identifiera avvikande aktiviteter som kan tyda på hot mot SQL-databaser samt identifiera och klassificera känsliga data. |AuditIfNotExists, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Se till Azure Defender är inställt på På för Lagring

**ID:** CIS Azure 2.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för Storage ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender för Storage ger identifieringar av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. |AuditIfNotExists, inaktiverad |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Se till Azure Defender är inställt på På för Kubernetes

**ID:** CIS Azure 2.6 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för Kubernetes ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender för Kubernetes ger skydd mot hot i realtid för containermiljöer och genererar aviseringar för misstänkta aktiviteter. |AuditIfNotExists, inaktiverad |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Se till Azure Defender är inställt på På för containerregister

**ID:** CIS Azure 2.7-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för containerregister ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender för containerregister ger sårbarhetsgenomsökning av alla avbildningar som har dragits under de senaste 30 dagarna, push-skickas till registret eller importeras och visar detaljerade resultat per bild. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Kontrollera att Azure Defender är inställt på På för Key Vault

**ID:** CIS Azure 2.8-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender för Key Vault ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender för Key Vault ger ett extra skydds- och säkerhetsintelligenslager genom att upptäcka ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Kontrollera att "Automatisk etablering av övervakningsagenten" är inställt på "På"

**ID:** CIS Azure 2.11-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatisk etablering av Log Analytics-agenten ska vara aktiverat i din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Om du vill övervaka säkerhetsrisker och hot samlar Azure Security Center data från dina virtuella Azure-datorer. Data samlas in av Log Analytics-agenten, tidigare kallad Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till Log Analytics-arbetsytan för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Se till att "Ytterligare e-postadresser" har konfigurerats med en e-postadress för säkerhetskontakt

**ID:** CIS Azure 2.13 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Prenumerationer bör ha en kontakt-e-postadress för säkerhetsproblem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |För att säkerställa att relevanta personer i din organisation meddelas när det finns en potentiell säkerhetsöverträdelse i en av dina prenumerationer, ställer du in en säkerhetskontakt för att ta emot e-postaviseringar från Security Center. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Kontrollera att "Meddela om aviseringar med följande allvarlighetsgrad" är inställt på "Hög"

**ID:** CIS Azure 2.14-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[E-postavisering för aviseringar med hög allvarlighetsgrad ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |För att se till att relevanta personer i din organisation meddelas när det finns en potentiell säkerhetsöverträdelse i en av dina prenumerationer aktiverar du e-postaviseringar för aviseringar med hög allvarlighetsgrad i Security Center. |AuditIfNotExists, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Lagringskonton

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Kontrollera att "Säker överföring krävs" är inställt på "Aktiverad"

**ID:** CIS Azure 3.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Säker överföring till lagringskonton ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Granska krav för säker överföring i ditt lagringskonto. Säker överföring är ett alternativ som tvingar ditt lagringskonto att endast godkänna begäranden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data under överföring från attacker på nätverksnivå, till exempel man-in-the-middle, avlyssning och sessionskapning |Granska, Neka, Inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Kontrollera att "Offentlig åtkomstnivå" är inställt på Privat för blobcontainrar

**ID:** CIS Azure 3.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Offentlig åtkomst till lagringskonto ska inte vara tillåtet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig läsåtkomst till containrar och blobar i Azure Storage ett praktiskt sätt att dela data men kan medför säkerhetsrisker. För att förhindra dataintrång som orsakas av oönskad anonym åtkomst rekommenderar Microsoft att du förhindrar offentlig åtkomst till ett lagringskonto om ditt scenario inte kräver det. |audit, deny, disabled |[2.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Se till att standardregeln för nätverksåtkomst för lagringskonton är inställd på neka

**ID:** CIS Azure 3.6-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör begränsa nätverksåtkomsten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Nätverksåtkomst till lagringskonton bör begränsas. Konfigurera nätverksregler så att endast program från tillåtna nätverk kan komma åt lagringskontot. För att tillåta anslutningar från specifika Internetklienter eller lokala klienter kan åtkomst beviljas till trafik från specifika virtuella Azure-nätverk eller till IP-adressintervall för offentliga Internet |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Lagringskonton bör begränsa nätverksåtkomsten med hjälp av regler för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Skydda dina lagringskonton mot potentiella hot med hjälp av regler för virtuellt nätverk som en prioriterad metod i stället för IP-baserad filtrering. Om du inaktiverar IP-baserad filtrering kan offentliga IP-adresser inte komma åt dina lagringskonton. |Granska, Neka, Inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Kontrollera att "Betrodda Microsoft-tjänster" är aktiverat för åtkomst till lagringskonto

**ID:** CIS Azure 3.7 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör tillåta åtkomst från betrodda Microsoft-tjänster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Vissa Microsoft-tjänster som interagerar med lagringskonton fungerar från nätverk som inte kan beviljas åtkomst via nätverksregler. För att hjälpa den här typen av tjänst att fungera som avsett kan du tillåta Microsoft-tjänster att kringgå nätverksregler. Dessa tjänster använder sedan stark autentisering för att få åtkomst till lagringskontot. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Se till att lagring för kritiska data krypteras med kund hanterad nyckel

**ID:** CIS Azure 3.9-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonton bör använda kund hanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Skydda ditt lagringskonto med större flexibilitet med hjälp av kund hanterade nycklar. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Användning av kund hanterade nycklar ger ytterligare funktioner för att styra rotationen av nyckelkrypteringsnyckeln eller att radera data kryptografiskt. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Database Services

### <a name="ensure-that-auditing-is-set-to-on"></a>Kontrollera att "Granskning" är inställt på "På"

**ID:** CIS Azure 4.1.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granskning på SQL Server ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på SQL Server bör aktiveras för att spåra databasaktiviteter i alla databaser på servern och spara dem i en granskningslogg. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Kontrollera att "Datakryptering" är inställt på "På" på en SQL Database

**ID:** CIS Azure 4.1.2 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[transparent datakryptering på SQL-databaser ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparent datakryptering ska aktiveras för att skydda vilodata och uppfylla efterlevnadskrav |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Se till att kvarhållningen av granskning är längre än 90 dagar

**ID:** CIS Azure 4.1.3 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servrar med granskning till lagringskontots mål bör konfigureras med 90 dagars kvarhållning eller högre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |I incidentundersökningssyfte rekommenderar vi att du ställer in datalagringen för SQL Server granskning till lagringskontots mål till minst 90 dagar. Bekräfta att du uppfyller de nödvändiga kvarhållningsreglerna för de regioner där du arbetar. Detta krävs ibland för efterlevnad av regelstandarder. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Kontrollera att Advanced Threat Protection (ATP) på en SQL-server är inställt på "Aktiverad"

**ID:** CIS Azure 4.2.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Avancerad datasäkerhet ska vara aktiverat på SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Granska varje SQL Managed Instance utan avancerad datasäkerhet. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Avancerad datasäkerhet ska vara aktiverat på DINA SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Granska SQL-servrar utan Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Se till att Sårbarhetsbedömning (VA) är aktiverat på en SQL-server genom att ange ett lagringskonto

**ID:** CIS Azure 4.2.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sårbarhetsbedömning bör aktiveras på SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Granska varje SQL Managed Instance som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella säkerhetsrisker i databasen. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Sårbarhetsbedömning bör aktiveras på SQL-servrarna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Granska Azure SQL servrar som inte har återkommande genomsökningar för sårbarhetsbedömning aktiverade. Sårbarhetsbedömning kan identifiera, spåra och hjälpa dig att åtgärda potentiella sårbarheter i databasen. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Se till att VA-inställningen Skicka genomsökningsrapporter till har konfigurerats för en SQL-server

**ID:** CIS Azure 4.2.4 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inställningarna för sårbarhetsbedömning för SQL Server bör innehålla en e-postadress för att ta emot genomsökningsrapporter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Kontrollera att en e-postadress har angetts för fältet "Skicka genomsökningsrapporter till" i inställningarna för Sårbarhetsbedömning. Den här e-postadressen tar emot sammanfattning av genomsökningsresultat efter att en regelbunden genomsökning körs på SQL-servrar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Kontrollera att "Framtvinga SSL-anslutning" är inställt på "AKTIVERAT" för PostgreSQL-databasserver

**ID:** CIS Azure 4.3.1 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Kontrollera att "Framtvinga SSL-anslutning" är inställt på "ENABLED" för MySQL-databasservern

**ID:** CIS Azure 4.3.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot man-in-the-middle-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen kräver att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Kontrollera att serverparametern "log_checkpoints" är inställd på "ON" för PostgreSQL-databasservern

**ID:** CIS Azure 4.3.3 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Loggkontrollpunkter ska vara aktiverade för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan log_checkpoints inställningen aktiverad. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Kontrollera att serverparametern "log_connections" är inställd på "ON" för PostgreSQL-databasserver

**ID:** CIS Azure 4.3.4 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logganslutningar ska vara aktiverade för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan log_connections inställningen aktiverad. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Kontrollera att serverparametern "log_disconnections" är inställd på "ON" för PostgreSQL-databasserver

**ID:** CIS Azure 4.3.5 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Frånkopplingar ska loggas för PostgreSQL-databasservrar.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan log_disconnections aktiverat. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Kontrollera att serverparametern "connection_throttling" är inställd på "ON" för PostgreSQL-databasserver

**ID:** CIS Azure 4.3.6 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Anslutningsbegränsning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Den här principen hjälper till att granska alla PostgreSQL-databaser i din miljö utan att anslutningsbegränsning är aktiverad. Den här inställningen aktiverar tillfällig anslutningsbegränsning per IP vid för många felaktiga lösenordsinloggningar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Kontrollera att Azure Active Directory administratör har konfigurerats

**ID:** CIS Azure 4.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[En Azure Active Directory-administratör ska etableras för SQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Granska etableringen av en Azure Active Directory för din SQL-server för att aktivera Azure AD-autentisering. Azure AD-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Se till att SQL-serverns TDE-skydd är krypterat med kund hanterad nyckel

**ID:** CIS Azure 4.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-hanterade instanser bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Genom transparent datakryptering (TDE) med din egen nyckel får du ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en HSM-backad extern tjänst och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL-servrar bör använda kund hanterade nycklar för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementering transparent datakryptering (TDE) med din egen nyckel ger ökad transparens och kontroll över TDE-skyddet, ökad säkerhet med en HSM-backad extern tjänst och befordran av uppdelning av uppgifter. Den här rekommendationen gäller för organisationer med ett relaterat efterlevnadskrav. |AuditIfNotExists, inaktiverad |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Loggning och övervakning

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Se till att lagringscontainern som lagrar aktivitetsloggarna inte är offentligt tillgänglig

**ID:** CIS Azure 5.1.3 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Offentlig åtkomst till lagringskonto ska inte vara tillåtet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonym offentlig läsåtkomst till containrar och blobar i Azure Storage ett praktiskt sätt att dela data men kan ge säkerhetsrisker. För att förhindra dataintrång som orsakas av oönskad anonym åtkomst rekommenderar Microsoft att du förhindrar offentlig åtkomst till ett lagringskonto om inte ditt scenario kräver det. |audit, deny, disabled |[2.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Se till att lagringskontot som innehåller containern med aktivitetsloggar är krypterat med BYOK (Använd din egen nyckel)

**ID:** CIS Azure 5.1.4 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Lagringskonto som innehåller containern med aktivitetsloggar måste krypteras med BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Den här principen granskar om lagringskontot som innehåller containern med aktivitetsloggar krypteras med BYOK. Principen fungerar bara om lagringskontot finns i samma prenumeration som aktivitetsloggar. Mer information Azure Storage kryptering i vila finns [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) här.  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Kontrollera att loggning för Azure KeyVault är aktiverad

**ID:** CIS Azure 5.1.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Resursloggar i Key Vault ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Kontrollera att aktivitetsloggaviseringen finns för att skapa principtilldelning

**ID:** CIS Azure 5.2.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika principåtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Den här principen granskar specifika principåtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Kontrollera att aktivitetsloggaviseringen finns för borttagning av principtilldelning

**ID:** CIS Azure 5.2.2 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika principåtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Den här principen granskar specifika principåtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Kontrollera att aktivitetsloggaviseringen finns för Skapa eller Uppdatera nätverkssäkerhetsgrupp

**ID:** CIS Azure 5.2.3 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Kontrollera att aktivitetsloggaviseringen finns för Ta bort nätverkssäkerhetsgrupp

**ID:** CIS Azure 5.2.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Kontrollera att aktivitetsloggaviseringen finns för regel för att skapa eller uppdatera nätverkssäkerhetsgrupp

**ID:** CIS Azure 5.2.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Kontrollera att aktivitetsloggaviseringen finns för regeln Ta bort nätverkssäkerhetsgrupp

**ID:** CIS Azure 5.2.6 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Kontrollera att aktivitetsloggaviseringen finns för säkerhetslösningen Skapa eller Uppdatera

**ID:** CIS Azure 5.2.7 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika säkerhetsåtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar specifika säkerhetsåtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Kontrollera att aktivitetsloggaviseringen finns för Ta bort säkerhetslösning

**ID:** CIS Azure 5.2.8 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[En aktivitetsloggavisering bör finnas för specifika säkerhetsåtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Den här principen granskar specifika säkerhetsåtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Kontrollera att aktivitetsloggaviseringen finns för skapa eller uppdatera eller ta bort SQL Server brandväggsregel

**ID:** CIS Azure 5.2.9-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Det bör finnas en aktivitetsloggavisering för specifika administrativa åtgärder](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Den här principen granskar specifika administrativa åtgärder utan konfigurerade aktivitetsloggaviseringar. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Kontrollera att Diagnostikloggar är aktiverade för alla tjänster som stöder det.

**ID:** CIS Azure 5.3-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnostikloggar i App Services ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Granska aktivering av diagnostikloggar i appen. På så sätt kan du återskapa aktivitetsloggar i undersökningssyfte om en säkerhetsincident inträffar eller om nätverket har komprometterats |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Resursloggar i Azure Data Lake Store ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Batch-konton ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Data Lake Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i händelsehubben ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i IoT Hub ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Key Vault ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Logic Apps ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Söktjänster ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, inaktiverad |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Resursloggar i Virtual Machine Scale Sets ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Vi rekommenderar att du aktiverar loggar så att aktivitetsloggen kan återskapas när utredningar krävs i händelse av en incident eller en komprometterad händelse. |AuditIfNotExists, inaktiverad |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Nätverk

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Kontrollera att RDP-åtkomsten är begränsad från Internet

**ID:** CIS Azure 6.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[RDP-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Den här principen granskar alla nätverkssäkerhetsregel som tillåter RDP-åtkomst från Internet |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Kontrollera att SSH-åtkomsten är begränsad från Internet

**ID:** CIS Azure 6.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SSH-åtkomst från Internet ska blockeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Den här principen granskar alla nätverkssäkerhetsregel som tillåter SSH-åtkomst från Internet |Granska, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Kontrollera att Network Watcher är "Aktiverad"

**ID:** CIS Azure 6.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher ska vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Med övervakning på scenarionivå kan du diagnostisera problem i en vy på nätverksnivå från början till slut. Verktyg för nätverksdiagnostik och visualisering som är Network Watcher hjälper dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Se Virtual Machines använder Managed Disks

**ID:** CIS Azure 7.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska virtuella datorer som inte använder hanterade diskar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Den här principen granskar virtuella datorer som inte använder hanterade diskar |Revision |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Se till att OS- och datadiskar krypteras med CMK

**ID:** CIS Azure 7.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diskkryptering ska tillämpas på virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuella datorer utan aktiverad diskkryptering övervakas av Azure Security Center som rekommendationer. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Kontrollera att "Ejanslutna diskar" är krypterade med CMK

**ID:** CIS Azure 7.3 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Icke-frånkopplade diskar ska krypteras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Den här principen granskar alla icke-frånkopplade diskar utan att kryptering har aktiverats. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Kontrollera att endast godkända tillägg har installerats

**ID:** CIS Azure 7.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endast godkända VM-tillägg ska installeras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Den här principen styr tillägg för virtuella datorer som inte godkänns. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Kontrollera att de senaste os-korrigeringarna för alla Virtual Machines tillämpas

**ID:** CIS Azure 7.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systemuppdateringar ska ha installerats på dina datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Se till att slutpunktsskydd för alla Virtual Machines är installerat

**ID:** CIS Azure 7.6-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Övervaka saknat Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection-agent övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Andra säkerhetsöverväganden

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Kontrollera att förfallodatumet har angetts för alla nycklar

**ID:** CIS Azure 8.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault ska ha ett förfallodatum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Kryptografiska nycklar ska ha ett definierat förfallodatum och inte vara permanenta. Nycklar som är giltiga för alltid ger en potentiell angripare mer tid att kompromettera nyckeln. Det är en rekommenderad säkerhetspraxis att ange förfallodatum för kryptografiska nycklar. |Granska, Neka, Inaktiverad |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Kontrollera att förfallodatumet är inställt på alla hemligheter

**ID:** CIS Azure 8.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault hemligheter ska ha ett förfallodatum](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Hemligheter ska ha ett definierat förfallodatum och inte vara permanenta. Hemligheter som är giltiga för alltid ger en potentiell angripare mer tid att kompromettera dem. Det är en rekommenderad säkerhetspraxis att ange förfallodatum för hemligheter. |Granska, Neka, Inaktiverad |[1.0.1-förhandsversion](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Kontrollera att nyckelvalvet kan återställas

**ID:** CIS Azure 8.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rensningsskydd ska vara aktiverat för nyckelvalv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Skadlig borttagning av ett nyckelvalv kan leda till permanent dataförlust. En illvillig insider i din organisation kan potentiellt ta bort och rensa nyckelvalv. Rensningsskydd skyddar dig mot insiderattacker genom att framtvinga en obligatorisk kvarhållningsperiod för mjukt borttagna nyckelvalv. Ingen i din organisation eller Microsoft kommer att kunna rensa dina nyckelvalv under kvarhållningsperioden för mjuk borttagning. |Granska, Neka, Inaktiverad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Aktivera rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Services

**ID:** CIS Azure 8.5 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Om du vill ge detaljerad filtrering av de åtgärder som användare kan utföra kan du använda Role-Based Access Control (RBAC) för att hantera behörigheter i Kubernetes Service-kluster och konfigurera relevanta auktoriseringsprinciper. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Kontrollera App Service autentisering är inställt på Azure App Service

**ID:** CIS Azure 9.1-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Autentisering ska vara aktiverat i API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-begäranden når API-appen eller autentisera dem som har token innan de når API-appen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Autentisering ska vara aktiverat i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-begäranden når funktionsappen eller autentisera dem som har token innan de når funktionsappen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Autentisering ska vara aktiverat i webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service autentisering är en funktion som kan förhindra att anonyma HTTP-begäranden når webbappen eller autentisera dem som har token innan de når webbappen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Se till att webbappen omdirigerar all HTTP-trafik till HTTPS Azure App Service

**ID:** CIS Azure 9.2-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Webbprogram bör endast vara tillgängligt via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Användning av HTTPS garanterar server-/tjänstautentisering och skyddar data under överföring från avlyssningsattacker på nätverksnivå. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Se till att webbappen använder den senaste versionen av TLS-kryptering

**ID:** CIS Azure 9.3-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Den senaste TLS-versionen ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Den senaste TLS-versionen ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uppgradera till den senaste TLS-versionen |AuditIfNotExists, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Kontrollera att webbappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"

**ID:** CIS Azure 9.4-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att API-appen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Med klientcertifikat kan appen begära ett certifikat för inkommande begäranden. Endast klienter som har ett giltigt certifikat kan nå appen. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Kontrollera att WEBBappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Med klientcertifikat kan appen begära ett certifikat för inkommande begäranden. Endast klienter som har ett giltigt certifikat kan nå appen. |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Funktionsappar ska ha "Klientcertifikat (inkommande klientcertifikat)" aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Med klientcertifikat kan appen begära ett certifikat för inkommande begäranden. Endast klienter med giltiga certifikat kommer att kunna nå appen. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Kontrollera att Registrera med Azure Active Directory är aktiverat på App Service

**ID:** CIS Azure 9.5-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Hanterad identitet ska användas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Använda en hanterad identitet för förbättrad autentiseringssäkerhet |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Hanterad identitet ska användas i din funktionsapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Använda en hanterad identitet för förbättrad autentiseringssäkerhet |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Hanterad identitet ska användas i din webbapp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Använda en hanterad identitet för förbättrad autentiseringssäkerhet |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Kontrollera att "PHP-versionen" är den senaste, om den används för att köra webbappen

**ID:** CIS Azure 9.6-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att PHP-versionen är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Med jämna mellanrum släpps nyare versioner för PHP-programvara antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste PHP-versionen för API-appar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Kontrollera att "PHP-versionen" är den senaste, om den används som en del av WEBBappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Med jämna mellanrum släpps nyare versioner för PHP-programvara antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste PHP-versionen för webbappar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Kontrollera att "Python-versionen" är den senaste, om den används för att köra webbappen

**ID:** CIS Azure 9.7 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att "Python-versionen" är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Med jämna mellanrum släpps nyare versioner för Python-programvara, antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Python-versionen för API-appar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Kontrollera att "Python-versionen" är den senaste, om den används som en del av funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Med jämna mellanrum släpps nyare versioner för Python-programvara antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Python-versionen för funktionsappar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. För närvarande gäller den här principen endast för Linux-webbappar. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Kontrollera att "Python-versionen" är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Med jämna mellanrum släpps nyare versioner för Python-programvara antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Python-versionen för webbappar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. För närvarande gäller den här principen endast för Linux-webbappar. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Kontrollera att "Java-versionen" är den senaste, om den används för att köra webbappen

**ID:** CIS Azure 9.8-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att "Java-versionen" är den senaste, om den används som en del av API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Med jämna mellanrum släpps nyare versioner för Java antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Python-versionen för API-appar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Kontrollera att "Java-versionen" är den senaste, om den används som en del av funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Med jämna mellanrum släpps nyare versioner för Java-programvara, antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Java-versionen för funktionsappar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Kontrollera att "Java-versionen" är den senaste, om den används som en del av webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Med jämna mellanrum släpps nyare versioner för Java-programvara, antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Vi rekommenderar att du använder den senaste Java-versionen för webbappar för att kunna dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den senaste versionen. Den här principen gäller för närvarande endast för Linux-webbappar. |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Kontrollera att HTTP-versionen är den senaste, om den används för att köra webbappen

**ID:** CIS Azure 9.9 **Ägarskap:** Kund

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kontrollera att HTTP-versionen är den senaste, om den används för att köra API-appen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Med jämna mellanrum släpps nyare versioner för HTTP antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Genom att använda den senaste HTTP-versionen för webbappar kan du dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den nyare versionen. För närvarande gäller den här principen endast för Linux-webbappar. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Kontrollera att HTTP-versionen är den senaste, om den används för att köra funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Med jämna mellanrum släpps nyare versioner för HTTP antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Genom att använda den senaste HTTP-versionen för webbappar kan du dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den nyare versionen. För närvarande gäller den här principen endast för Linux-webbappar. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Kontrollera att HTTP-versionen är den senaste om den används för att köra webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Med jämna mellanrum släpps nyare versioner för HTTP antingen på grund av säkerhetsbrister eller för att inkludera ytterligare funktioner. Genom att använda den senaste HTTP-versionen för webbappar kan du dra nytta av eventuella säkerhetskorrigeringar och/eller nya funktioner i den nyare versionen. För närvarande gäller den här principen endast för Linux-webbappar. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Se till att FTP-distributioner är inaktiverade

**ID:** CIS Azure 9.10-ägarskap: Kund 

|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[FTPS ska endast krävas i din API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Aktivera FTPS-framtvingande för förbättrad säkerhet |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS ska endast krävas i funktionsappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Aktivera FTPS-framtvingande för förbättrad säkerhet |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS måste vara obligatoriskt i webbappen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Aktivera FTPS-framtvingande för förbättrad säkerhet |AuditIfNotExists, inaktiverad |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Tillgängligheten för Azure Policy definitioner kan variera Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om Azure Policy:

- [Översikt över regelefterlevnad.](../concepts/regulatory-compliance.md)
- Se [initiativdefinitionsstrukturen](../concepts/initiative-definition-structure.md).
- Granska andra exempel på [Azure Policy exempel](./index.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig hur [du åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
