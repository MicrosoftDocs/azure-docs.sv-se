---
title: Översikt över hur du skapar Linux-avbildningar för Azure
description: Så här använder du dina virtuella Linux-avbildningar eller skapar nya avbildningar som ska användas i Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 223fdc215bc391bea5cad5d4cb9999b9d14ba570
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878790"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Skapa och skapa Linux-avbildningar i Azure

Den här översikten täcker grundläggande begrepp kring avbildningen och hur du skapar och använder Linux-avbildningar i Azure. Innan du hämtar en anpassad avbildning till Azure måste du vara medveten om vilka typer och alternativ som är tillgängliga för dig.

Den här artikeln beskriver avbildnings besluts punkterna och kraven, förklarar viktiga begrepp så att du kan följa dessa och kunna skapa egna anpassade avbildningar till din specifikation.

## <a name="difference-between-managed-disks-and-images"></a>Skillnaden mellan hanterade diskar och avbildningar


Med Azure kan du ta en virtuell hård disk till plattformen, använda den som en [hanterad disk](../faq-for-disks.md#managed-disks)eller använda som källa för en avbildning. 

Azure Managed disks är enkla virtuella hård diskar. Du kan antingen ta en befintlig virtuell hård disk och skapa en hanterad disk från den eller skapa en tom hanterad disk från grunden. Du kan skapa virtuella datorer från hanterade diskar genom att koppla disken till den virtuella datorn, men du kan bara använda en virtuell hård disk med en virtuell dator. Du kan inte ändra några OS-egenskaper. Azure försöker bara aktivera den virtuella datorn och börja använda den disken. 

Azure-avbildningar kan bestå av flera operativ system diskar och data diskar. När du använder en hanterad avbildning för att skapa en virtuell dator, gör plattformen en kopia av avbildningen och använder den för att skapa den virtuella datorn, så att hanterade avbildnings stöd återanvänder samma avbildning för flera virtuella datorer. Azure innehåller även avancerade hanterings funktioner för avbildningar, till exempel global replikering och versions hantering via [delade avbildnings galleriet](../shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Generaliserad och specialiserad

Azure erbjuder två huvud avbildnings typer, generaliserade och specialiserade. Termerna generaliserade och specialiserade är ursprungliga Windows-villkor, som migrerades i till Azure. De här typerna definierar hur plattformen ska hantera den virtuella datorn när den aktive ras. Båda typerna har fördelar och nack delar och krav. Innan du börjar måste du veta vilken avbildnings typ du behöver. Nedan sammanfattas scenarier och typ som du måste välja:

| Scenario      | Avbildningstyp  | Lagringsalternativ |
| ------------- |:-------------:| :-------------:| 
| Skapa en avbildning som kan konfigureras för användning av flera virtuella datorer och jag kan ange värd namnet, lägga till en administratörs användare och utföra andra uppgifter under den första starten. | Generaliserade | Delat avbildnings Galleri eller fristående hanterade avbildningar |
| Skapa en avbildning från en ögonblicks bild av en virtuell dator eller en säkerhets kopia | Specialiserade |Delat avbildnings Galleri eller en hanterad disk |
| Skapa snabbt en avbildning som inte behöver någon konfiguration för att skapa flera virtuella datorer |Specialiserade |Delat bildgalleri |


### <a name="generalized-images"></a>Generaliserade avbildningar

En generaliserad avbildning är en avbildning som kräver att installationen slutförs vid första starten. Vid den första starten anger du till exempel värd namnet, administratörs användaren och andra VM-/regionsspecifika konfigurationer. Detta är användbart när du vill att avbildningen ska återanvändas flera gånger och när du vill skicka in parametrar när du skapar den. Om den generaliserade avbildningen innehåller Azure-agenten kommer agenten att bearbeta parametrarna och signaler tillbaka till den plattform där den inledande konfigurationen har slutförts. Den här processen kallas för [etablering](./provisioning.md). 

Etableringen kräver att en-etablering ingår i avbildningen. Det finns två provisioner:
- [Azure Linux-Agent](../extensions/agent-linux.md)
- [init](./using-cloud-init.md)

Följande är [förutsättningar](./create-upload-generic.md) för att skapa en avbildning.


### <a name="specialized-images"></a>Specialiserade bilder
Dessa är avbildningar som är helt konfigurerade och inte kräver VM och särskilda parametrar. plattformen aktiverar bara den virtuella datorn eftersom du behöver hantera unikheten i den virtuella datorn, t. ex. Ange ett värdnamn, för att undvika DNS-konflikter i samma VNET. 

Etablerings agenter krävs inte för de här avbildningarna men du kanske vill ha funktioner för fil hantering. Du kan installera Linux-agenten, men inaktivera etablerings alternativet. Även om du inte behöver en etablerings agent måste avbildningen uppfylla [kraven](./create-upload-generic.md)  för Azure-avbildningar.


## <a name="image-storage-options"></a>Alternativ för bild lagring
När du har gjort Linux-avbildningen har du två alternativ:

- Hanterade avbildningar för enkel skapande av virtuella datorer i en utvecklings-och test miljö.
- [Delat avbildnings Galleri](../shared-image-galleries.md) för att skapa och dela bilder i stor skala.


### <a name="managed-images"></a>Hanterade avbildningar

Hanterade avbildningar kan användas för att skapa flera virtuella datorer, men de har stor mängd begränsningar. Hanterade avbildningar kan bara skapas från en generaliserad källa (VM eller VHD). De kan bara användas för att skapa virtuella datorer i samma region och de kan inte delas mellan prenumerationer och klienter.

Hanterade avbildningar kan användas för utvecklings-och test miljöer där du behöver ett par enkla generaliserade avbildningar som du kan använda inom en region och prenumeration. 

### <a name="azure-shared-image-gallery-sig"></a>Azure-Galleri för delad avbildning (SIG)

[Delade avbildnings gallerier](../shared-image-galleries.md) rekommenderas för att skapa, hantera och dela bilder i stor skala. Delade avbildnings gallerier hjälper dig att bygga struktur och organisation runt dina avbildningar.  

- Stöd för både generaliserade och specialiserade avbildningar.
- Stöd för avbildning av både generation 1 och 2 bilder.
- Global replikering av avbildningar.
- Versions hantering och gruppering av avbildningar för enklare hantering.
- Bilder med hög tillgänglighet med zon redundant lagring (ZRS) i regioner som stöder Tillgänglighetszoner. ZRS erbjuder bättre återhämtning mot zonindelade-problem.
- Dela över prenumerationer och till och med mellan Active Directory (AD)-klienter med hjälp av Azure RBAC.
- Skala dina distributioner med avbildnings repliker i varje region.

På hög nivå skapar du en SIG, och den består av:
- Bild definitioner – dessa är behållare som innehåller grupper med bilder.
- Avbildnings versioner – dessa är de faktiska bilderna



## <a name="hyper-v-generation"></a>Hyper-V-generering

Azure stöder Hyper-V generation 1 (gen1) och generation 2 (Gen2), Gen2 är den senaste generationen och erbjuder ytterligare funktioner över gen1. Till exempel: ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM). Generation 2 virtuella datorer som körs lokalt, har vissa funktioner som inte stöds i Azure än. Mer information finns i avsnittet funktioner och funktioner. Mer information finns i den här [artikeln](../generation-2.md). Skapa Gen2-avbildningar om du behöver ytterligare funktioner.

Om du fortfarande behöver skapa en egen avbildning kontrollerar du att den uppfyller [avbildnings kraven](./create-upload-generic.md)och laddar upp till Azure. Distributions särskilda krav:


- [CentOS-baserade distributioner](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES och openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar ett [delat avbildnings Galleri](tutorial-custom-images.md).