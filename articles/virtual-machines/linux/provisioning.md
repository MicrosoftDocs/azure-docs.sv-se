---
title: Översikt över Linux-etablering
description: Översikt över hur du kan ta med dina virtuella Linux-avbildningar eller skapa nya avbildningar som ska användas i Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563227"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM-etablering
När du skapar en virtuell dator från en generaliserad avbildning (delat avbildnings Galleri eller hanterad avbildning) gör kontroll planet att du kan skapa en virtuell dator och skicka parametrar och inställningar till den virtuella datorn. Detta kallas VM- *etablering*. Under etableringen gör plattformen nödvändig virtuell dator skapa parameter värden (hostname, användar namn, lösen ord, SSH-nycklar, customData) som är tillgängliga för den virtuella datorn när den startas. 

En etablerings agent bakade i avbildningen kommer att vara Interface med plattformen, ansluta upp till flera oberoende etablerings gränssnitt), ange egenskaperna och signalen till den plattform som har slutförts. 

Etablerings agenterna kan vara [Azure Linux-agenten](../extensions/agent-linux.md)eller [Cloud-Init](./using-cloud-init.md). Följande är [förutsättningar](create-upload-generic.md) för att skapa generaliserade avbildningar.

Etablerings agenterna, ger stöd för alla godkända [Azure Linux-distributioner](./endorsed-distros.md)och du hittar de godkända distribution-avbildningarna i många fall kommer att levereras med både Cloud-Init och Linux-agenten. På så sätt kan du använda Cloud-Init för att hantera etableringen. sedan ger Linux-agenten stöd för att hantera Azure- [tillägg](../extensions/features-windows.md). Genom att tillhandahålla stöd för tillägg innebär det att den virtuella datorn har rätt att stödja ytterligare Azure-tjänster, till exempel återställning av lösen ord för virtuella datorer, Azure-övervakning, Azure Backup, Azure Disk Encryption osv.

När etableringen har slutförts körs Cloud-Init på varje start. Cloud-Init övervakar ändringar i den virtuella datorn, t. ex. nätverks ändringar, montering och formatering av den tillfälliga disken och start av Linux-agenten. Linux-agenten körs kontinuerligt på servern, som söker efter ett mål tillstånd (ny konfiguration) från Azure-plattformen, så när du installerar tillägg kommer agenten att kunna bearbeta dem.

Även om det finns två etablerings agenter bör Cloud-Init vara den etablerings agent du väljer och Linux-agenten bör installeras för tilläggs stöd. På så sätt kan du utnyttja plattforms optimeringar, och du kan inaktivera/ta bort Linux-agenten för mer information om hur du skapar avbildningar utan agenten och hur du tar bort den genom att läsa den här [dokumentationen](disable-provisioning.md).

Om du har en Linux-kernel som inte stöder att köra någon av agenterna, men vill ange några av egenskaperna för virtuella datorer, till exempel hostname, customData, användar namn, lösen ord, SSH-nycklar, diskuterar i det här dokumentet hur du kan [skapa generaliserade avbildningar utan agent](no-agent.md)och uppfyller plattforms kraven.


## <a name="provisioning-agent-responsibilities"></a>Agent ansvar för etablering

**Avbildnings etablering**
  
- Skapa ett användar konto
- Konfigurera SSH-autentiseringstyper
- Distribution av offentliga SSH-nycklar och nyckel par
- Ange värd namnet
- Publicera värd namnet på plattforms-DNS
- Rapportering av finger avtryck för SSH-värdnamnet till plattformen
- Resurs disk hantering
- Formatera och montera resurs disken
- Förbrukning och bearbetning `customData`
 
**Nätverk**
  
- Hanterar vägar för att förbättra kompatibilitet med plattforms-DHCP-servrar
- Säkerställer stabiliteten för nätverks gränssnittets namn

**Kernel**
  
- Konfigurerar virtuell NUMA (inaktivera för kernel-<`2.6.37` )
- Använder Hyper-V-entropi för `/dev/random`
- Konfigurerar SCSI-tidsgräns för rot enheten (som kan vara fjärran sluten)

**Diagnostik**
  
- Omdirigering av konsol till den seriella porten

## <a name="communication"></a>Kommunikation
Informations flödet från plattformen till agenten sker via två kanaler:

- En ansluten DVD-skiva för IaaS-distributioner. DVD: n innehåller en OVF-kompatibel konfigurations fil som innehåller all etablerings information, förutom de faktiska SSH-nyckelvärdena.
- En TCP-slutpunkt visar en REST API som används för att hämta distribution och konfiguration av topologi.


## <a name="azure-provisioning-agent-requirements"></a>Krav för Azure Provisioning-agenten
Linux-agenten och Cloud-Init är beroende av vissa system paket för att kunna fungera korrekt:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Filsystemverktyg: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Lösen ords verktyg: chpasswd, sudo
- Verktyg för text bearbetning: sed, grep
- Nätverks verktyg: IP-Route
- Kernel-stöd för att montera UDF-filsystem.

## <a name="next-steps"></a>Nästa steg

Om du behöver kan du [inaktivera etableringen och ta bort Linux-agenten](disable-provisioning.md).
