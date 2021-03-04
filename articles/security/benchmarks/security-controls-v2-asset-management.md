---
title: Azure Security benchmark v2 – till gångs hantering
description: Azure Security benchmark v2 till gångs hantering
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbeb88b6aa542666481458fde97d7c63f467fa30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051539"
---
# <a name="security-control-v2-asset-management"></a>Säkerhets kontroll v2: till gångs hantering

Till gångs hantering omfattar kontroller för att säkerställa ökad säkerhet och styrning över Azure-resurser. Detta inkluderar rekommendationer om behörigheter för säkerhets personal, säkerhets åtkomst till till gångs inventering och hantering av godkännanden för tjänster och resurser (inventering, spårning och rätt).

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa [mer i information om det inbyggda initiativet för Azure Security benchmark-efterlevnad: nätverks säkerhet](../../governance/policy/samples/azure-security-benchmark.md#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker gällande tillgångar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM – 8, PM-5 |

Se till att säkerhets teamen beviljas säkerhets läsar behörigheter i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center.

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster.

- [Översikt över säkerhetsläsarrollen](../../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../../governance/management-groups/overview.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM – 8, PM-5 |

Se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure. Säkerhetsteamet behöver den här inventeringen till att utvärdera organisationens potentiella exponering för nya risker samt som indata till löpande förbättringar av säkerheten. 

Azure Security Center Inventory-funktionen och Azure Resource Graph kan fråga efter och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.

Organisera till gångar logiskt enligt organisationens taxonomi med hjälp av taggar och andra metadata i Azure (namn, beskrivning och kategori).

- [Skapa frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM – 7, CM-8 |

Använd Azure Policy till att granska och begränsa vilka tjänster användarna kan etablera i miljön. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor till att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/index.md)

- [Skapa frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM – 7, CM-8, CM-10, CM-11 |

Upprätta eller uppdatera säkerhets principer som kan användas för att hantera livs cykel hanterings processer för potentiellt stora konsekvenser. Sådana ändringar kan till exempel avse: identitetsleverantörer och åtkomst, datakänslighet, nätverkskonfiguration och tilldelning av administrativa privilegier.

Ta bort Azure-resurser när de inte längre behövs.

- [Ta bort Azure-resursgrupp och resurs](../../azure-resource-manager/management/delete-resource-group.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Använd villkorlig åtkomst för Azure AD för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Använd endast godkända program i beräknings resurser

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Se till att endast auktoriserad program vara körs och att all obehörig program vara blockeras från att köras på Azure Virtual Machines.

Använd Azure Security Center (ASC) adaptiva program kontroller för att identifiera och generera en lista över tillåtna program. Du kan också använda ASC-anpassade program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

Använd Azure Automation Ändringsspårning och inventering för att automatisera insamlingen av inventerings information från dina virtuella Windows-och Linux-datorer. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Om du vill hämta programmets installations datum och annan information aktiverar du diagnostik på gästnivå och dirigerar Windows-händelseloggen till Log Analytics-arbetsyta.

Beroende på typen av skript kan du använda konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

Du kan också använda en lösning från tredje part för att identifiera och identifiera ej godkänd program vara.

- [Använda Azure Security Center adaptiva program kontroller](../../security-center/security-center-adaptive-application.md)

- [Förstå Azure Automation Ändringsspårning och inventering](../../automation/change-tracking/overview.md)

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
