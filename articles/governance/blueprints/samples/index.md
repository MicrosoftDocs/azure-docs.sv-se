---
title: Index för skissexempel
description: Index över efterlevnads- och standardexempel för distribution av miljöer, principer och ramverket för molnimplementering med Azure Blueprints.
ms.date: 02/08/2020
ms.topic: sample
ms.openlocfilehash: c64bc02c1dea4e2c53d4ff195add2ac7fa57874f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370962"
---
# <a name="azure-blueprints-samples"></a>Azure Blueprints-exempel

Följande tabell innehåller länkar till exempel för Azure Blueprints. Alla exempel håller produktionskvalitet och du kan börja distribuera dem redan idag för att uppfylla dina olika efterlevnadsbehov.

## <a name="standards-based-blueprint-samples"></a>Standardbaserade skissexempel

| Exempel | Beskrivning |
|---------|---------|
| [Den australiska regeringens ISM PROTECTED-kontroller](./ism-protected/index.md) | Tillhandahåller styrningsriktlinjer för efterlevnad av den australiska regeringens ISM PROTECTED-kontroller. |
| [Benchmark för Azure-säkerhet](./azure-security-benchmark.md) | Innehåller riktlinjer för efterlevnad av [Azure Security Benchmark](../../../security/benchmarks/overview.md). |
| [Azure Security benchmark Foundation](./azure-security-benchmark-foundation/index.md) | Distribuerar och konfigurerar Azure Security benchmark Foundation. |
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Tillhandahåller riktlinjer för överensstämmelse med Kanadas PBMM (Protected B, Medium Integrity, Medium Availability). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1-1-0.md)| Innehåller en uppsättning principer som hjälper dig att följa CIS Microsoft Azure Foundations Benchmark-rekommendationerna. |
| [DoD:s effektnivå 4](./dod-impact-level-4/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa DoD:s effektnivå 4. |
| [DoD:s effektnivå 5](./dod-impact-level-5/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa DoD:s effektnivå 5. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa FedRAMP Moderate. |
| [FedRAMP High](./fedramp-h/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa FedRAMP High. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Tillhandahåller guardrails för efterlevnad med IRS 1075.|
| [ISO 27001](./iso-27001-2013.md) | Tillhandahåller efterlevnadsriktlinjer för ISO 27001. |
| [ISO 27001 Delade tjänster](./iso27001-shared/index.md) | Tillhandahåller en uppsättning kompatibla infrastrukturmönster och principriktlinjer som hjälper dig med attesteringen för ISO 27001. |
| [ISO 27001 App Service-miljön/SQL Database-arbetsbelastning](./iso27001-ase-sql-workload/index.md) | Ger mer infrastruktur till ISO 27001-skissen för [delade tjänster](./iso27001-shared/index.md) . |
| [Media](./media/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa Media MPAA. |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | Tillhandahåller guardrails för att följa NIST SP 800-53 R4. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | Tillhandahåller guardrails för kompatibilitet med NIST SP 800-171 R2. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Innehåller en uppsättning principer som hjälp för efterlevnad av PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Hjälper till med SWIFT CSP-CSCF v2020-efterlevnad. |
| [Styrning av UK OFFICIAL och UK NHS](./ukofficial/index.md) | Tillhandahåller en uppsättning kompatibla infrastruktursmönster och principriktlinjer som hjälper dig med attesteringen för UK OFFICIAL och UK NHS. |
| [CAF Foundation](./caf-foundation/index.md) | Innehåller en uppsättning kontroller som hjälper dig att hantera din molnegendom i enlighet med [Microsoft Cloud adoption Framework för Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [CAF Migrate-landningszonen](./caf-migrate-landing-zone/index.md) | Innehåller en uppsättning kontroller som hjälper dig att förbereda migrering av din första arbetsbelastning samt hantera din molnegendom i enlighet med [Microsoft Cloud adoption Framework för Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Exempelstrategi

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="Diagram över var skissexemplen passar för arkitekturens komplexitet jämfört med krav på efterlevnad." border="false":::
   Beskriver ett koordinatsystem där arkitekturens komplexitet är på X-axeln och krav på efterlevnad på Y-axeln.  När du ökar komplexitets kraven och kraven på efterlevnaden bör du införa standard skiss exempel från portalen som anges i region E.  För kunder som kommer igång med Azure använder vi Cloud adoption Framework (C A F) baserade bas-och landnings zon ritningar som anges av region A och B.  Det återstående utrymmet som är tilldelat till anpassade modeller som skapats av kunder är partner för regionerna C, D och F. :::image-end:::

Skisserna för CAF Foundation och CAF Migrate-landningszonen förutsätter att kunden förbereder en befintlig, ren enstaka prenumeration för migrering av lokala tillgångar och arbetsbelastningar till Azure.
(Region A och B i bilden).  

Det finns en möjlighet att iterera genom exempelskisserna och leta efter mönster för anpassningar som en kund använder. Det finns även möjligheten att proaktivt hantera ritningar som är branschspecifika, till exempel finansiella tjänster och näthandel (övre delen av region B). På ett liknande sätt tänker vi oss skapande av skisser för komplexa arkitektoniska överväganden såsom flera prenumerationer, hög tillgänglighet, resurser mellan flera regioner samt kunder som implementerar kontroller över flera prenumerationer och resurser (region C och D).

Det finns exempelskisser som rör det kundscenario där efterlevnadskraven är höga och den arkitektoniska komplexiteten är hög (region E i bilden). Region F i bilden är ett som är en åtgärd som utförs av kunder och partners som använder exempel ritningarna och anpassar dem efter deras unika behov.

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).
