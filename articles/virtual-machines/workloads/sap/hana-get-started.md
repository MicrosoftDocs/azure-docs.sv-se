---
title: Installation av SAP HANA på virtuella Azure-datorer | Microsoft Docs
description: Guide för installation av SAP HANA på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: f2a62cb08fcce6597f02c080231f5e1808794054
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509968"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installation av SAP HANA på virtuella Azure-datorer
## <a name="introduction"></a>Introduktion
Den här guiden hjälper dig att peka på rätt resurser för att distribuera HANA på virtuella Azure-datorer. Den här guiden leder dig till dokumentations resurser som du måste kontrol lera innan du installerar SAP HANA på en virtuell Azure-dator. Så att du kan utföra rätt steg för att avsluta med en konfiguration som stöds av SAP HANA på virtuella Azure-datorer.  

> [!NOTE]
> I den här guiden beskrivs distributioner av SAP HANA till virtuella Azure-datorer. Information om hur du distribuerar SAP HANA till HANA-stora instanser finns i [så här installerar och konfigurerar du SAP HANA (stora instanser) i Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Krav
Den här guiden förutsätter också att du är bekant med:
* SAP HANA-och SAP-NetWeaver och hur de installeras lokalt.
* Installera och använda SAP HANA-och SAP-programinstanser på Azure.
* Begreppen och procedurerna som beskrivs i:
   * Planera för SAP-distribution på Azure, som innehåller Azure Virtual Network planera och Azure Storage användning. Se [SAP NetWeaver i Azure Virtual Machines-planering och implementerings guide](./planning-guide.md)
   * Distributions principer och sätt att distribuera virtuella datorer i Azure. Se [Azure Virtual Machines Deployment for SAP](./deployment-guide.md)
   * Koncept med hög tillgänglighet för SAP HANA som dokumenterats i [SAP HANA hög tillgänglighet för virtuella Azure-datorer](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Steg för steg innan du distribuerar
I det här avsnittet visas de olika stegen som du måste utföra innan du börjar med att installera SAP HANA på en virtuell Azure-dator. Ordningen räknas upp och så ska den följas av som räknas upp:

1. Det går inte att använda alla distributions scenarier i Azure. Därför bör du kontrol lera dokument- [SAP-arbetsbelastningen i scenarier med virtuella Azure-datorer som stöds](./sap-planning-supported-configurations.md) för scenariot som du har i åtanke med din SAP HANA-distribution. Om scenariot inte finns med i listan, måste du anta att det inte har testats och därför inte stöds
2. Förutsatt att du har en bra idé om ditt minnes krav för din SAP HANA-distribution måste du hitta en anpassning av Azure VM. Inte alla virtuella datorer som är certifierade för SAP NetWeaver, enligt beskrivningen i [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533), SAP HANA certifieras. Källan till sanningen för SAP HANA certifierade virtuella Azure-datorer är webbplatsen [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Enheterna som börjar med **S** är Hana-enheter med [stora instanser](./hana-overview-architecture.md) och inte virtuella Azure-datorer.
3. Olika typer av virtuella Azure-datorer har olika lägsta operativ system versioner för SUSE Linux eller Red Hat Linux. På webbplatsen [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)måste du klicka på en post i listan över SAP HANA certifierade enheter för att få detaljerade data om enheten. Förutom den som stöds av HANA-arbetsbelastningen visas de OS-versioner som stöds med dessa enheter för SAP HANA
4. När det gäller operativ system versioner måste du överväga vissa minimala kernel-versioner. Dessa minimi versioner dokumenteras i följande SAP-support anteckningar:
    - [Stöd för SAP-support #2814271 SAP HANA säkerhets kopiering Miss lyckas på Azure med fel kontroll fel](https://launchpad.support.sap.com/#/notes/2814271)
    - [Stöd för SAP #2753418 potentiell prestanda försämring på grund av timer-återställning](https://launchpad.support.sap.com/#/notes/2753418)
    - [Stöd för SAP #2791572 prestanda försämring på grund av saknat stöd för VDSO för Hyper-V i Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Baserat på den OS-version som stöds för den valda typen av virtuell dator måste du kontrol lera om den önskade SAP HANA versionen stöds av den operativ system versionen. Läs [stöd för SAP-support #2235581](https://launchpad.support.sap.com/#/notes/2235581) för en support mat ris med SAP HANA utgåvor med de olika operativ system versionerna.
5. Som du kanske har hittat en giltig kombination av typ av virtuell Azure-dator, operativ Systems utgåva och SAP HANA utgåva måste du kontrol lera i produkt tillgänglighets matrisen för SAP. I tillgänglighets mat ris för SAP kan du ta reda på om den SAP-produkt som du vill köra mot SAP HANA-databasen stöds.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Steg för steg-distribution av virtuella datorer och gäst operativ system
I den här fasen måste du gå igenom de steg som distribuerar VM: erna för att installera HANA och slutligen optimera det valda operativ systemet efter installationen.

1. Välj bas avbildningen från Azure-galleriet. Om du vill bygga en egen operativ system avbildning för SAP HANA måste du känna till alla de olika paket som krävs för en lyckad SAP HANA-installation. Annars rekommenderas det att använda SUSE-och Red Hat-avbildningar för SAP eller SAP HANA utanför Azures avbildnings Galleri. De här avbildningarna innehåller de paket som krävs för en lyckad HANA-installation. Baserat på ditt support avtal med operativ system leverantören måste du välja en avbildning där du använder din egen licens. Eller så väljer du en OS-avbildning som innehåller stöd
2. Om du väljer en gäst operativ system avbildning som kräver att du använder din egen licens måste du registrera operativ system avbildningen med din prenumeration, så att du kan hämta och använda de senaste korrigeringarna. Det här steget kommer att kräva offentlig Internet åtkomst. Om du inte konfigurerar din privata instans av, till exempel en SMT-server i Azure.
3. Bestäm nätverks konfigurationen för den virtuella datorn. Du kan läsa mer information i dokumentet [SAP HANA infrastruktur konfiguration och åtgärder på Azure](./hana-vm-operations.md). Tänk på att det inte finns några kvoter för nätverks data flöde som du kan tilldela till virtuella nätverkskort i Azure. Detta innebär att det enda syftet med att dirigera trafik genom olika virtuella nätverkskort baseras på säkerhets överväganden. Vi litar på att du kan hitta ett support Bart problem mellan komplexiteten i trafik dirigeringen genom flera virtuella nätverkskort och de krav som upprätthålls av säkerhets aspekterna.
3. Tillämpa de senaste korrigeringarna på operativ systemet när den virtuella datorn har distribuerats och registrerats. Registrerat antingen med din egen prenumeration. Eller om du väljer en avbildning som innehåller stöd för operativ system måste den virtuella datorn ha åtkomst till korrigerings filen redan. 
4. Använd de optimeringar som krävs för SAP HANA. Dessa optimeringar visas i följande SAP-support anteckningar:

    - [SAP support NOTE #2694118-Red Hat Enterprise Linux HA-tillägg på Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP support NOTE #1984787-SUSE LINUX Enterprise Server 12: installations information](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Support anteckning för SAP #2578899-SUSE Linux Enterprise Server 15: installations kommentar](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP support NOTE #2002167-Red Hat Enterprise Linux 7. x: installation och uppgradering](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support NOTE #2292690-SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP support NOTE #2772999-Red Hat Enterprise Linux 8. x: installation och konfiguration](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP support NOTE #2777782-SAP HANA DB: rekommenderade OS-inställningar för RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP support NOTE #2455582 – Linux: köra SAP-program som kompileras med GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Stöd för SAP-support #2382421 – optimera nätverks konfigurationen på HANA-och OS-nivå](https://launchpad.support.sap.com/#/notes/2382421)

1. Välj Azure Storage-typ för SAP HANA. I det här steget måste du bestämma lagrings-layouten för SAP HANA installationen. Du kommer att använda antingen anslutna Azure-diskar eller interna Azure NFS-resurser. De Azure Storage-typer som eller stöds och kombinationer av olika typer av Azure-lagring som kan användas, dokumenteras i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](./hana-vm-operations-storage.md). Ta de konfigurationer som dokumenteras som start punkt. För icke-produktionssystem kanske du kan konfigurera lägre data flöde eller IOPS. I produktions syfte kan du behöva konfigurera en lite mer data flöde och IOPS.
2. Se till att du har konfigurerat [Azure-Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) för dina volymer som innehåller DBMS-transaktions loggarna eller gör om loggar när du använder virtuella datorer i M-eller Mv2-serien. Tänk på begränsningarna för Skrivningsaccelerator som dokumenterade.
2. Kontrol lera om [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som distribueras.

> [!NOTE]
> Alla kommandon i de olika SAP-finjustera-profilerna eller enligt beskrivningen i anteckningar kan kanske köras på Azure. Kommandon som kan manipulera energi läget för virtuella datorer returnerar vanligt vis ett fel eftersom det inte går att ändra energi läget för den underliggande Azure-värd maskin varan.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Stegvisa förberedelser som är speciella för Azure Virtual Machines
En av Azure-information är installationen av ett Azure VM-tillägg som levererar övervaknings data för SAP-värd agenten. Information om installationen av det här övervaknings tillägget dokumenteras i:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) diskuterar SAP Enhanced Monitoring med virtuella Linux-datorer i Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) diskuterar information om SAPOSCOL i Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) diskuterar nyckel övervaknings mått för SAP på Microsoft Azure
-  [Azure Virtual Machines-distribution för SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA installation
När de virtuella Azure-datorerna har distribuerats och operativ systemen är registrerade och konfigurerade kan du installera SAP HANA enligt SAP-installationen. För att komma igång med den här dokumentationen börjar du med den här SAP-webbplatsen [Hana-resurser](https://www.sap.com/products/hana/implementation/resources.html)

För SAP HANA skalbara konfigurationer med direkta anslutna diskar i Azure Premium Storage eller Ultra disk läser du de information som finns i dokumentet [SAP HANA infrastruktur konfiguration och åtgärder på Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Ytterligare resurser för SAP HANA säkerhets kopiering
Information om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer finns i:
* [Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA Azure Backup på filnivå](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Nästa steg
Läs dokumentationen:

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](./hana-vm-operations.md)
- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](./hana-vm-operations-storage.md)
