---
title: Säkerhetsfunktioner som används med virtuella Azure-datorer
titleSuffix: Azure security
description: Den här artikeln innehåller en översikt över de centrala Azure-säkerhetsfunktioner som kan användas med Azure Virtual Machines.
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
ms.openlocfilehash: 036a1d3f8cad4ba9cda03a9001cfec1bb7de86b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715045"
---
# <a name="azure-virtual-machines-security-overview"></a>Översikt över Azure Virtual Machines-säkerhet
Den här artikeln innehåller en översikt över de centrala Azure-säkerhetsfunktioner som kan användas med virtuella datorer.

Du kan använda Azure Virtual Machines för att distribuera en mängd olika data behandlings lösningar på ett flexibelt sätt. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services. Så du kan distribuera alla arbets belastningar och språk på nästan alla operativ system.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan bygga och distribuera dina program med garantier för att dina data är skyddade och säkra i mycket säkra data Center.

Med Azure kan du bygga säkerhets förbättrade och kompatibla lösningar som:

* Skydda dina virtuella datorer mot virus och skadlig kod.
* Kryptera dina känsliga data.
* Skydda nätverks trafik.
* Identifiera och identifiera hot.
* Uppfylla kraven för efterlevnad.  

## <a name="antimalware"></a>Programvara mot skadlig kod

Med Azure kan du använda program mot skadlig kod från säkerhets leverantörer som Microsoft, Symantec, Trend Micro och Kasper Sky. Den här program varan hjälper till att skydda dina virtuella datorer från skadliga filer, annons program och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtual Machines är en real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara.  Microsoft Antimalware för Azure tillhandahåller konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system.

Microsoft Antimalware för Azure är en lösning för en enskild agent för program-och klient miljöer. Den är utformad för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på dina arbets belastningar för dina program, med antingen grundläggande säkerhet eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod.

Läs mer om [Microsofts program mot skadlig kod för Azure](antimalware.md) och de viktigaste funktionerna som är tillgängliga.

Lär dig mer om program mot skadlig kod för att hjälpa till att skydda dina virtuella datorer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Så här installerar och konfigurerar du Trend Micro djupgående säkerhet som en tjänst på en virtuell Windows-dator](/previous-versions/azure/virtual-machines/extensions/trend)
* [Så här installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator](../../virtual-machines/extensions/symantec.md)
* [Säkerhetslösningar på Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Överväg att använda [Windows Defender Avancerat skydd](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)för ännu mer kraftfullt skydd. Med Windows Defender ATP får du:

* [Minskning av attack ytan](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Nästa generations skydd](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection och-svar](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatiserad undersökning och reparation](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Säkerhetspoäng](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Avancerad jakt](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Hantering och API: er](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Läs mer:

* [Kom igång med WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Översikt över WDATP-funktioner](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modul för maskin varu säkerhet

Förbättrad nyckel säkerhet kan förbättra krypterings-och autentiserings skydd. Du kan förenkla hanteringen och säkerheten för viktiga hemligheter och nycklar genom att lagra dem i Azure Key Vault.

Key Vault ger dig möjlighet att lagra dina nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 på nivå 2. Dina SQL Server krypterings nycklar för säkerhets kopiering eller [transparent data kryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) kan lagras i Key Vault med alla nycklar och hemligheter från dina program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault blogg](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Disk kryptering för virtuell dator

Azure Disk Encryption är en ny funktion för att kryptera dina Windows-och Linux-diskar för virtuella datorer. Azure Disk Encryption använder den branschstandardiserade [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volym kryptering för operativ systemet och data diskarna.

Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. Det säkerställer att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage.

Läs mer:

* [Azure Disk Encryption för virtuella IaaS-datorer](./azure-disk-encryption-vms-vmss.md)
* [Snabbstart: Kryptera en virtuell Windows IaaS-dator med Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Säkerhets kopiering av virtuell dator

Azure Backup är en skalbar lösning som hjälper dig att skydda dina program data utan kapital investering och minimala drifts kostnader. Program fel kan skada dina data och mänskliga fel kan leda till buggar i dina program. Med Azure Backup skyddas dina virtuella datorer som kör Windows och Linux.

Läs mer:

* [Vad är Azure Backup?](../../backup/backup-overview.md)
* [Vanliga frågor och svar om Azure Backup-tjänsten](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

En viktig del av din organisations BCDR strategi är att ta reda på hur du håller företags arbets belastningar och appar som körs när planerade och oplanerade avbrott uppstår. Azure Site Recovery hjälper till att dirigera replikering, redundans och återställning av arbets belastningar och appar så att de är tillgängliga från en annan plats om din primära plats slutar fungera.

Site Recovery:

* **Fören klar din BCDR-strategi**: Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbets belastningar och appar från en enda plats. Site Recovery dirigerar replikering och redundans men fångar inte dina program data eller har ingen information om den.
* **Ger flexibel replikering**: genom att använda Site Recovery kan du replikera arbets belastningar som körs på virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows-och Linux-servrar.
* **Stöder redundans och återställning**: Site Recovery tillhandahåller redundanstest som stöder haveri beredskap utan att påverka produktions miljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter redundansväxlingen kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundära Data Center**: du kan replikera till en sekundär lokal plats eller till Azure. Om du använder Azure som mål för haveri beredskap elimineras kostnaden och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* Är **integrerat med befintliga BCDR-tekniker**: Site Recovery partner med andra PROGRAMs BCDR-funktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server backend-delen av företags arbets belastningar. Detta inkluderar inbyggt stöd för SQL Server Always on för att hantera redundans för tillgänglighets grupper.

Läs mer:

* [Vad är Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Hur fungerar Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Vilka arbets belastningar skyddas av Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuellt nätverk

Virtuella datorer behöver nätverks anslutning. För att stödja detta krav kräver Azure att virtuella datorer är anslutna till ett virtuellt Azure-nätverk.

Ett virtuellt Azure-nätverk är en logisk konstruktion som byggts ovanpå den fysiska Azures nätverks infrastruktur resurs. Varje logiskt virtuellt Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. Den här isoleringen hjälper till att säkerställa att nätverks trafiken i dina distributioner inte är tillgänglig för andra Microsoft Azure kunder.

Läs mer:

* [Översikt över nätverkssäkerhet i Azure](network-overview.md)
* [Översikt över Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Nätverksfunktioner och partnerskap för företags scenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering och rapportering av säkerhets principer

Azure Security Center hjälper dig att förebygga, identifiera och reagera på hot. Security Center ger ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer. Den hjälper till att upptäcka hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Tillhandahålla [säkerhets rekommendationer](../../security-center/security-center-recommendations.md) för de virtuella datorerna. Exempel på rekommendationer är: tillämpa system uppdateringar, konfigurera ACL-slutpunkter, aktivera program mot skadlig kod, aktivera nätverks säkerhets grupper och Använd disk kryptering.
* Övervaka statusen för dina virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-introduction.md)
* [Vanliga frågor och svar om Azure Security Center](../../security-center/faq-general.md)
* [Azure Security Center planering och åtgärder](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Efterlevnad

Azure Virtual Machines är certifierat för FISMA, FedRAMP, HIPAA, PCI DSS nivå 1 och andra viktiga compliance-program. Med den här certifieringen blir det enklare för dina egna Azure-program att uppfylla kraven på efterlevnad och för din verksamhet att hantera en mängd olika nationella och internationella myndighets krav.

Läs mer:

* [Microsoft säkerhets Center: efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Betrott moln: Microsoft Azure säkerhet, sekretess och efterlevnad](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Konfidentiell databehandling

Även om konfidentiella data behandling inte är tekniskt en del av säkerheten för virtuella datorer, ingår avsnittet om säkerhet på den virtuella datorn på den högre nivån av "Compute"-säkerhet. Konfidentiell data behandling ingår i kategorin "Compute"-säkerhet.

Konfidentiell bearbetning garanterar att när data är "i klartext", vilket krävs för effektiv bearbetning, skyddas data i en betrodd körnings miljö  https://en.wikipedia.org/wiki/Trusted_execution_environment (tee, även kallat en enklaven), ett exempel på som visas i bilden nedan.  

TEEs se till att det inte finns något sätt att visa data eller åtgärderna inuti utanför, även med en fel sökare. De säkerställer även att endast auktoriserad kod tillåts komma åt data. Om koden ändras eller manipuleras, nekas åtgärderna och miljön inaktive rad. TEE tillämpar dessa skydd i hela körningen av kod i den.

Läs mer:

* [Introduktion till Azures konfidentiella data behandling](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Konfidentiell databehandling i Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [rekommenderade säkerhets metoder](iaas.md) för virtuella datorer och operativ system.