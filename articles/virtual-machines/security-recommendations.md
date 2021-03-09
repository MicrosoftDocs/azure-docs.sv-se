---
title: Säkerhets rekommendationer för virtuella datorer i Azure
description: Använd de här rekommendationerna för virtuella datorer i Azure för att uppfylla de säkerhets skyldigheter som beskrivs i den delade ansvars modellen och för att förbättra den övergripande säkerheten för dina distributioner.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 622ebfed8294dbcb441aa509fc4f6ba75114f28d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499364"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Säkerhets rekommendationer för virtuella datorer i Azure


Den här artikeln innehåller säkerhets rekommendationer för Azure Virtual Machines. Följ dessa rekommendationer för att uppfylla de säkerhets skyldigheter som beskrivs i vår modell för delat ansvar. Rekommendationerna hjälper dig också att förbättra den övergripande säkerheten för dina webb programs lösningar. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

En del av den här artikelns rekommendationer kan åtgärdas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen för dina resurser i Azure. Den analyserar regelbundet säkerhets läget för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan rekommenderar vi att du åtgärdar problemen. Mer information finns [i säkerhets rekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).

Allmän information om Azure Security Center finns i [Azure Security Center?](../security-center/security-center-introduction.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| När du skapar anpassade VM-avbildningar ska du använda de senaste uppdateringarna. | Innan du skapar avbildningar installerar du de senaste uppdateringarna för operativ systemet och för alla program som ska ingå i din avbildning.  | - |
| Se till att dina virtuella datorer är aktuella. | Du kan använda [uppdateringshantering](../automation/update-management/overview.md) lösning i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer i Azure. | [Ja](../security-center/asset-inventory.md) |
| Säkerhetskopiera dina virtuella datorer. | [Azure Backup](../backup/backup-overview.md) hjälper till att skydda dina program data och har minimala drifts kostnader. Program fel kan skada dina data och mänskliga fel kan leda till buggar i dina program. Azure Backup skyddar dina virtuella datorer som kör Windows och Linux. | - |
| Använd flera virtuella datorer för större återhämtning och tillgänglighet. | Om din virtuella dator kör program som måste ha hög tillgänglighet kan du använda flera virtuella datorer eller [tillgänglighets uppsättningar](./availability.md). | - |
| Anta en strategi för affärs kontinuitet och haveri beredskap (BCDR). | Med Azure Site Recovery kan du välja mellan olika alternativ som har utformats för att stödja affärs kontinuitet. Det stöder olika scenarier för replikering och redundans. Mer information finns i  [About Site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Datasäkerhet

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Kryptera operativ system diskar. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) hjälper dig att kryptera dina Windows-och Linux-IaaS VM-diskar. Utan de nödvändiga nycklarna är innehållet på krypterade diskar oläsligt. Disk kryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjliga om disken kopierades.| [Ja](../security-center/asset-inventory.md) |
| Kryptera data diskar. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) hjälper dig att kryptera dina Windows-och Linux-IaaS VM-diskar. Utan de nödvändiga nycklarna är innehållet på krypterade diskar oläsligt. Disk kryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjliga om disken kopierades.| -  |
| Begränsa installerad program vara. | Begränsa installerad program vara till det som krävs för att kunna tillämpa din lösning. Den här rikt linjen hjälper till att minska din lösnings attack yta. | - |
| Använd antivirus program eller program mot skadlig kod. | I Azure kan du använda program mot skadlig kod från säkerhets leverantörer som Microsoft, Symantec, Trend Micro och Kasper Sky. Den här program varan hjälper till att skydda dina virtuella datorer från skadliga filer, annons program och andra hot. Du kan distribuera Microsoft Antimalware baserat på dina program arbets belastningar. Microsoft Antimalware är endast tillgängligt för Windows-datorer. Använd antingen grundläggande säker eller avancerad anpassad konfiguration. Mer information finns i [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md). | - |
| Lagra nycklar och hemligheter på ett säkert sätt. | Förenkla hanteringen av dina hemligheter och nycklar genom att förse dina program ägare med ett säkert, centralt hanterat alternativ. Den här hanteringen minskar risken för angrepp eller läckage. Azure Key Vault kan lagra dina nycklar säkert i HSM: er (Hardware Security modules) som är certifierade till FIPS 140-2 nivå 2. Om du behöver använda FIPs 140,2 nivå 3 för att lagra dina nycklar och hemligheter kan du använda [Azure Dedicated HSM](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering 

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Centralisera VM-autentisering. | Du kan centralisera autentiseringen av dina virtuella Windows-och Linux-datorer med hjälp [Azure Active Directory autentisering](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Övervaka dina virtuella datorer. | Du kan använda [Azure Monitor for VMS](../azure-monitor/vm/vminsights-overview.md) för att övervaka statusen för dina virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer. Prestanda problem med en virtuell dator kan leda till avbrott i tjänsten, vilket strider mot tillgänglighetens säkerhets princip. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Begränsa åtkomsten till hanterings portar. | Angripare genomsöker offentliga moln-IP-intervall för öppna hanterings portar och försöker med "Easy" attacker som vanliga lösen ord och kända säkerhets problem. Du kan använda [just-in-Time (JIT) VM-åtkomst](../security-center/security-center-just-in-time.md) för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkla anslutningar till virtuella datorer när de behövs. | - |
| Begränsa nätverks åtkomst. | Med nätverks säkerhets grupper kan du begränsa nätverks åtkomst och kontrol lera antalet exponerade slut punkter. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Nästa steg

Kontakta din programprovider om du vill veta mer om ytterligare säkerhets krav. Mer information om hur du utvecklar säkra program finns i [dokumentationen för säker utveckling](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).