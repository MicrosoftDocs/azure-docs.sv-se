---
title: Linux-distributioner som har godkänts på Azure
description: Lär dig mer om Linux på Azure-godkända distributioner, inklusive rikt linjer för Ubuntu, CentOS, Oracle och SUSE.
services: virtual-machines
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 01/03/2021
ms.author: guybo
ms.openlocfilehash: 091a06fd608763ac7265670733890e93bf71dde1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547415"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Signerade Linux-distributioner på Azure

Partner tillhandahåller linux-avbildningar på Azure Marketplace. Microsoft arbetar med olika Linux-communities för att lägga till ännu fler varianter i den godkända distributions listan. För distributioner som inte är tillgängliga från Marketplace kan du alltid ta med din egen Linux genom att följa rikt linjerna i [skapa och ladda upp en virtuell hård disk som innehåller Linux-operativsystemet](./create-upload-generic.md).

## <a name="supported-distributions-and-versions"></a>Distributioner och versioner som stöds

I följande tabell visas de Linux-distributioner och-versioner som stöds i Azure. Mer information finns i [stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).

LIS-drivrutinerna (Linux Integration Services) för Hyper-V och Azure är kernel-moduler som Microsoft bidrar direkt till i den överordnade Linux-kärnan. Vissa LIS-drivrutiner är inbyggda i distributionens kernel som standard. Äldre distributioner som baseras på RHEL-/CentOS (Red Hat Enterprise) är tillgängliga som separat hämtning på [Linux Integration Services Version 4,2 för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Mer information finns i [krav för linux-kernel](create-upload-generic.md#linux-kernel-requirements).

Azure Linux-agenten är redan förinstallerad på Azure Marketplace-avbildningar och är vanligt vis tillgänglig från distributionens paket lagrings plats. Du hittar käll koden på [GitHub](https://github.com/azure/walinuxagent).

| Distribution | Version | Drivrutiner | Agent |
| --- | --- | --- | --- |
| CentOS av falsk Wave-programvara |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [Lis-hämtning](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: i kernel |Paket: i [lagrings platsen](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> Kärnan är [slut på livs längd](https://coreos.com/os/eol/) den 26 maj 2020. |Inte längre tillgänglig | | |
| Debian av credativ |8. x, 9. x, 10. x |I kernel |Paket: i lagrings platsen under "waagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Flatcar container Linux av Kinvolk| Pro, stabil, beta| I kernel | WA – Linux-agenten har redan installerats i/usr/share/OEM/bin/waagent |
| Oracle Linux av Oracle |6.x, 7.x, 8.x |I kernel |Paket: i lagrings platsen under "WALinuxAgent" <br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat Enterprise Linux av Red Hat](../workloads/redhat/overview.md) |6.x, 7.x, 8.x |I kernel |Paket: i lagrings platsen under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise med SUSE |SLES/SLES för SAP 11. x, 12. x, 15. x <br/> [Bild livs cykel för SUSE offentlig moln bild](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |I kernel |Paketfilerna<p> för 11 i [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagrings platsen<br>för 12 som ingår i modulen "offentligt moln" under "python-Azure-agent"<br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE av SUSE |openSUSE Leap 15.x |I kernel |Paket: i [molnet: tools](https://build.opensuse.org/project/show/Cloud:Tools) lagrings platsen under "python-Azure-agent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu efter kanoniskt |Ubuntu Server och Pro. 16. x, 18. x, 20. x<p>Information om utökat stöd för Ubuntu 12,04 och 14,04 hittar du här: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm). |I kernel |Paket: i lagrings platsen under "walinuxagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Takt för avbildnings uppdatering

Azure kräver att utgivare av de signerade Linux-distributionerna regelbundet uppdaterar sina avbildningar på Azure Marketplace med de senaste korrigeringarna och säkerhets korrigeringarna, på ett kvartal eller snabbare takt. Uppdaterade avbildningar på Marketplace är automatiskt tillgängliga för kunder som nya versioner av en avbildnings-SKU. Mer information om hur du hittar Linux-avbildningar: [hitta virtuella Linux-avbildningar på Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Azure-justerade kärnor

Azure fungerar nära olika godkända Linux-distributioner för att optimera de bilder som de har publicerat på Azure Marketplace. En aspekt av det här samarbetet är utvecklingen av "justerade" Linux-kerneler som är optimerade för Azure-plattformen och levereras som komponenter som stöds fullt ut av Linux-distributionen. Azure-Tuned kernels innehåller nya funktioner och prestanda förbättringar, och går snabbare (kvartals vis) takt jämfört med de standard-eller generiska kärnor som är tillgängliga från distributionen.

I de flesta fall kommer du att se dessa kernels förinstallerade på standard avbildningarna i Azure Marketplace, så att kunderna omedelbart får fördelarna med dessa optimerade kärnor. Mer information om dessa Azure-Tuned-kernels finns i följande länkar:

- [CentOS Azure-Tuned kernel – tillgängligt via CentOS Virtualization SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud kernel – tillgängligt med Debian 10-och Debian 9 "backports"-avbildningen på Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-Tuned-kernel](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure-Tuned-kernel](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar container Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS

Kärnan har schemalagts att vara [i slutet av livs längden](https://coreos.com/os/eol/) den 26 maj 2020.
Microsoft har två (2) kanaler för migrering av Core-användare.

- Flatcar av Kinvolk (se posten "Flatcar container Linux by Kinvolk".)
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (kunder måste ladda upp sin egen avbildning. Här är [dokumentationen om migreringen](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/).

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

credativ är ett oberoende konsult-och tjänste företag som specialiserar sig på utveckling och implementering av yrkes lösningar med hjälp av kostnads fri program vara. Som ledande specialister med öppen källkod har credativ internationell igenkänning med många IT-avdelningar som använder sin support. Tillsammans med Microsoft förbereder credativ Debian-avbildningar. Avbildningarna är särskilt utformade för att köras på Azure och kan enkelt hanteras via plattformen. credativ kommer också att ha stöd för långsiktigt underhåll och uppdatering av Debian-avbildningarna för Azure via Support Center för öppen källkod.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk är företaget bakom Flatcar container Linux, och fortsätter med den ursprungliga kärnan i en minimal, oföränderlig och automatisk uppdatering av grund för program i behållare. Som en minimal distribution innehåller Flatcar bara de paket som krävs för att distribuera behållare. Det oföränderliga fil systemet garanterar konsekvens och säkerhet, medan funktionerna för automatisk uppdatering gör att du alltid är uppdaterad med de senaste säkerhets korrigeringarna. 

Flatcar container Linux säkerhets kopie ras av Kinvolks globala team av Linux-och container Technology-experter som erbjuder en extra kommersiell support prenumeration som omfattar svar, säkerhet och tekniska aviseringar dygnet runt, och exklusiva Azure-optimerade avbildningar inklusive en långsiktig support kanal.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle-strategin är att erbjuda en bred portfölj med lösningar för offentliga och privata moln. Strategin ger kunderna valmöjligheter och flexibilitet i hur de distribuerar Oracle-programvara i Oracle-moln och andra moln. Oracle: s partnerskap med Microsoft gör det möjligt för kunder att distribuera Oracle-program i Microsofts offentliga och privata moln med förtroende för certifiering och support från Oracle.  Oracles åtagande och investering i offentliga och privata moln lösningar i Oracle är oförändrade.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Världens ledande leverantör av lösningar med öppen källkod, Red Hat hjälper mer än 90% av Fortune 500-företag att lösa affärs utmaningar, anpassa sina IT-och affärs strategier och förbereda för framtida teknik. Red Hat uppnår detta genom att tillhandahålla säkra lösningar via en öppen affärs modell och en prisvärd, förutsägbar prenumerations modell.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure är en beprövad plattform som ger överlägsen tillförlitlighet och säkerhet för molnbaserad data behandling. SUSEs mångsidiga Linux-plattform integreras sömlöst med Azure Cloud Services för att leverera en lätt hanterbar moln miljö. Med fler än 9 200 certifierade program från fler än 1 800 oberoende program varu leverantörer för SUSE Linux Enterprise Server, säkerställer SUSE att arbets belastningar som körs i data centret kan distribueras på ett säkert sätt i Azure.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kanonisk teknik och öppen community styrnings enhet Ubuntu lyckades i klient-, server-och molnbaserad data behandling, som innehåller privata moln tjänster för konsumenter. Kanoniskt innehåll för en enhetlig, kostnads fri plattform i Ubuntu, från telefon till moln, tillhandahåller en serie sammanhängande gränssnitt för telefon, surfplatta, TV och skriv bord. Den här synen gör Ubuntu det första valet för olika institutioner från offentliga moln leverantörer till de som tillverkar konsument elektronik och en favorit i olika Technologists.

Med utvecklare och teknik Center runt om i världen är kanoniskt unikt placerad till partner med maskin varu tillverkare, innehålls leverantörer och programutvecklare för att få Ubuntu-lösningar till marknaden för datorer, servrar och handhållna enheter.
