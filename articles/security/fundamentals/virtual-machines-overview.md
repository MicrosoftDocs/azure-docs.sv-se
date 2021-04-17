---
title: Säkerhetsfunktioner som används med virtuella Azure-datorer
titleSuffix: Azure security
description: Den här artikeln innehåller en översikt över de viktigaste säkerhetsfunktionerna i Azure som kan användas med Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 60f67ea618746c9f2b0cd65a9fbc7fb2b5fbfe86
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520011"
---
# <a name="azure-virtual-machines-security-overview"></a>Säkerhetsöversikt Virtual Machines Azure Virtual Machines
Den här artikeln innehåller en översikt över de grundläggande Azure-säkerhetsfunktionerna som kan användas med virtuella datorer.

Du kan använda Azure Virtual Machines för att distribuera en mängd olika beräkningslösningar på ett flexibelt sätt. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services. Det gör att du kan distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan skapa och distribuera dina program med garantier att dina data är skyddade och säkra i mycket säkra datacenter.

Med Azure kan du skapa säkerhetsför förbättrade, kompatibla lösningar som:

* Skydda dina virtuella datorer mot virus och skadlig kod.
* Kryptera känsliga data.
* Skydda nätverkstrafik.
* Identifiera och identifiera hot.
* Uppfylla efterlevnadskrav.  

## <a name="antimalware"></a>Programvara mot skadlig kod

Med Azure kan du använda program mot skadlig programvara från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Fel. Den här programvaran hjälper till att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtual Machines är en funktion för realtidsskydd som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara.  Microsoft Antimalware för Azure innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själv eller köras på dina Azure-system.

Microsoft Antimalware för Azure är en lösning med en agent för program- och klientmiljöer. Det är utformat för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på behoven för dina programarbetsbelastningar, med antingen grundläggande säker som standard eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod.

Läs mer om [Microsoft Antimalware för Azure](antimalware.md) och de grundläggande funktionerna som är tillgängliga.

Läs mer om program mot skadlig programvara för att skydda dina virtuella datorer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Så här installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Windows-dator](/previous-versions/azure/virtual-machines/extensions/trend)
* [Installera och konfigurera Symantec Endpoint Protection på en virtuell Windows-dator](../../virtual-machines/extensions/symantec.md)
* [Säkerhetslösningar i Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

För ännu kraftfullare skydd kan du överväga att [använda Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Med Windows Defender ATP får du:

* [Minskning av attackytan](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Nästa generations skydd](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Slutpunktsskydd och svar](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatiserad undersökning och reparation](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Säkerhetspoäng](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Avancerad jakt](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Hantering och API:er](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Läs mer:

* [Kom igång med WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Översikt över WDATP-funktioner](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul

Förbättrad nyckelsäkerhet kan förbättra krypterings- och autentiseringsskydd. Du kan förenkla hanteringen och säkerheten för dina kritiska hemligheter och nycklar genom att lagra dem i Azure Key Vault.

Key Vault alternativet att lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) som är certifierade för FIPS 140-2 Level 2-standarder. Dina SQL Server krypteringsnycklar för säkerhetskopiering eller [transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) kan lagras i Key Vault med nycklar eller hemligheter från dina program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault blogg](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Diskkryptering för virtuell dator

Azure Disk Encryption är en ny funktion för kryptering av virtuella Windows- och Linux-datordiskar. Azure Disk Encryption använder branschstandardfunktionen [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) i Windows och [dm-crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volymkryptering för operativsystemet och datadiskarna.

Lösningen är integrerad med Azure Key Vault som hjälper dig att kontrollera och hantera diskkrypteringsnycklar och hemligheter i nyckelvalvsprenumerationen. Det säkerställer att alla data på de virtuella datordiskarna krypteras i vila i Azure Storage.

Läs mer:

* [Azure Disk Encryption för virtuella IaaS-datorer](./azure-disk-encryption-vms-vmss.md)
* [Snabbstart: Kryptera en virtuell Windows IaaS-dator med Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator

Azure Backup är en skalbar lösning som skyddar dina programdata utan kapitalinvestering och minimala driftskostnader. Programfel kan skada dina data, och mänskliga fel kan introducera buggar i dina program. Med Azure Backup skyddas dina virtuella datorer som kör Windows och Linux.

Läs mer:

* [Vad är Azure Backup?](../../backup/backup-overview.md)
* [Vanliga frågor och svar Azure Backup om Azure Backup tjänsten](../../backup/backup-azure-backup-faq.yml)

## <a name="azure-site-recovery"></a>Azure Site Recovery

En viktig del av din organisations BCDR-strategi är att räkna ut hur företagets arbetsbelastningar och appar ska fortsätta att köras vid planerade och oplanerade avbrott. Azure Site Recovery hjälper till att samordna replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats blir ur drift.

Site Recovery:

* **Förenklar din BCDR-strategi:** Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbetsbelastningar och appar från en enda plats. Site Recovery dirigerar replikering och redundans, men fångar inte upp dina programdata eller har någon information om dem.
* **Ger flexibel replikering:** Med Site Recovery kan du replikera arbetsbelastningar som körs på virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows-/Linux-servrar.
* **Stöder redundans och återställning:** Site Recovery tillhandahåller redundanstestning för att stödja programåterställningstest utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter redundans kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundära datacenter:** Du kan replikera till en sekundär lokal plats eller till Azure. Att använda Azure som mål för haveriberedskap eliminerar kostnaden och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Integrerar med befintlig BCDR-teknik:** Site Recovery med andra appars BCDR-funktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server av företagets arbetsbelastningar. Detta inkluderar inbyggt stöd för SQL Server Always On för att hantera redundans för tillgänglighetsgrupper.

Läs mer:

* [Vad är Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Hur fungerar Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Vilka arbetsbelastningar skyddas av Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuellt nätverk

Virtuella datorer behöver nätverksanslutning. För att stödja det kravet kräver Azure att virtuella datorer är anslutna till ett virtuellt Azure-nätverk.

Ett virtuellt Azure-nätverk är en logisk konstruktion som bygger på den fysiska Azure-nätverks fabricen. Varje logiskt virtuellt Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. Den här isoleringen säkerställer att nätverkstrafiken i dina distributioner inte är tillgänglig för andra Microsoft Azure kunder.

Läs mer:

* [Översikt över nätverkssäkerhet i Azure](network-overview.md)
* [Virtual Network översikt](../../virtual-network/virtual-networks-overview.md)
* [Nätverksfunktioner och partnerskap för företagsscenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering och rapportering av säkerhetsprinciper

Azure Security Center hjälper dig att förebygga, identifiera och reagera på hot. Security Center ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering för alla dina Azure-prenumerationer. Den hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med ett brett ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Tillhandahålla [säkerhetsrekommendationer](../../security-center/security-center-recommendations.md) för de virtuella datorerna. Exempel på rekommendationer är: tillämpa systemuppdateringar, konfigurera ACL-slutpunkter, aktivera program mot skadlig kod, aktivera nätverkssäkerhetsgrupper och tillämpa diskkryptering.
* Övervaka tillståndet för dina virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-introduction.md)
* [Azure Security Center vanliga frågor och svar](../../security-center/faq-general.md)
* [Azure Security Center planering och drift](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Efterlevnad

Azure Virtual Machines är certifierat för FISMA, FedRAMP, HIPAA, PCI DSS Level 1 och andra viktiga efterlevnadsprogram. Den här certifieringen gör det enklare för dina egna Azure-program att uppfylla efterlevnadskraven och för ditt företag att hantera en mängd olika nationella och internationella regelkrav.

Läs mer:

* [Microsoft Trust Center: Efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Betrott moln: Microsoft Azure säkerhet, sekretess och efterlevnad](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Konfidentiell databehandling

Konfidentiell databehandling är inte tekniskt sett en del av säkerheten för virtuella datorer, men avsnittet om säkerhet för virtuella datorer hör till det högre ämnet "beräkningssäkerhet". Konfidentiell databehandling tillhör kategorin "beräkningssäkerhet".

Konfidentiell databehandling säkerställer att när data är "i klarfigur", vilket krävs för effektiv bearbetning, skyddas data i en betrodd körningsmiljö (VERT– även kallat enklav), ett exempel som visas i  https://en.wikipedia.org/wiki/Trusted_execution_environment bilden nedan.  

TE:er ser till att det inte finns något sätt att visa data eller åtgärder utifrån, även med ett felsökningsfel. De ser till och med till att endast auktoriserad kod har behörighet att komma åt data. Om koden ändras eller manipuleras nekas åtgärderna och miljön inaktiveras. THEAR tillämpar dessa skydd under hela körningen av kod i den.

Läs mer:

* [Introduktion till konfidentiell databehandling i Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Konfidentiell databehandling i Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om metodtips](iaas.md) för säkerhet för virtuella datorer och operativsystem.