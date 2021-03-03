---
title: Lär dig mer om Azure Image Builder (för hands version)
description: Lär dig mer om Azure Image Builder för virtuella datorer i Azure.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.reviewer: cynthn
ms.openlocfilehash: 1c70edfc3bad2be70d26c71736ca06fcc4a8dcdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672504"
---
# <a name="preview-azure-image-builder-overview"></a>För hands version: översikt över Azure Image Builder

Med standardiserade avbildningar av virtuella datorer kan organisationer migrera till molnet och säkerställa konsekvens i distributionerna. Bilder innehåller vanligt vis fördefinierade säkerhets-och konfigurations inställningar och nödvändig program vara. Att konfigurera din egen avbildnings pipeline kräver tid, infrastruktur och konfiguration, men med Azure VM Image Builder får du bara en enkel konfiguration som beskriver avbildningen, skickar den till tjänsten och avbildningen skapas och distribueras.
 
Med Azures avbildnings verktyg för virtuella datorer (Azure Image Builder) kan du starta med en Windows-eller Linux-baserad Azure Marketplace-avbildning, befintliga anpassade avbildningar eller Red Hat Enterprise Linux (RHEL) ISO och börja lägga till dina egna anpassningar. Eftersom Image Builder bygger på [HashiCorp Packer](https://packer.io/), kan du också importera befintliga paket i Shell-Provisioning-skripten. Du kan också ange var du vill att dina avbildningar ska finnas i [Azures Galleri för delad avbildning](shared-image-galleries.md), som en hanterad avbildning eller en virtuell hård disk.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Förhandsgranskningsfunktioner

I för hands versionen stöds dessa funktioner:

- Skapandet av de gyllene bas linje avbildningarna, som innehåller din minsta säkerhets-och företags konfiguration, och gör det möjligt för avdelningar att anpassa dem ytterligare efter deras behov.
- Korrigering av befintliga avbildningar gör att du kontinuerligt kan korrigera befintliga anpassade avbildningar i Image Builder.
- Anslut avbildnings byggare till dina befintliga virtuella nätverk så att du kan ansluta till befintliga konfigurations servrar (DSC, chef, Puppet osv.), fil resurser eller andra flyttbara servrar/tjänster.
- Integrering med Azures delade avbildnings Galleri, gör att du kan distribuera, version och skala avbildningar globalt och ger dig ett avbildnings hanterings system.
- Integrering med befintliga avbildningar skapar pipeliner, anropar bara Image Builder från din pipeline eller Använd den enkla för hands versionen av DevOps-aktiviteten i Image Builder.
- Migrera en befintlig avbildnings anpassnings pipeline till Azure. Använd dina befintliga skript, kommandon och processer för att anpassa avbildningar.
- Skapa avbildningar i VHD-format för att stödja Azure Stack.
 

## <a name="regions"></a>Regioner
Azure Image Builder-tjänsten är tillgänglig för för hands version i dessa regioner. Avbildningar kan distribueras utanför dessa regioner.
- East US
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra

## <a name="os-support"></a>OS-stöd
AIB kommer att ha stöd för Azure Marketplace Base OS-avbildningar:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

Stöd för RHEL ISO stöds inte längre.

## <a name="how-it-works"></a>Så här fungerar det

Azure Image Builder är en fullständigt hanterad Azure-tjänst som kan nås av en Azure-adressresurs. Azure Image Builder-processen har tre huvud delar: källa, anpassa och distribuera, dessa representeras i en mall. Diagrammet nedan visar komponenterna, med några av deras egenskaper. 
 

**Image Builder-process** 

![Konceptuell ritning i Azure Image Builder-processen](./media/image-builder-overview/image-builder-process.png)

1. Skapa avbildnings mal len som en. JSON-fil. Den här. JSON-filen innehåller information om avbildningens källa, anpassningar och distribution. Det finns flera exempel i [Azure Image Builder GitHub-lagringsplatsen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Skicka den till tjänsten. då skapas en avbildnings mal len artefakt i den resurs grupp som du anger. I bakgrunden kommer Image Builder att ladda ned käll avbildningen eller ISO och skript efter behov. De lagras i en separat resurs grupp som skapas automatiskt i din prenumeration i formatet: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. När du har skapat avbildnings mal len kan du skapa avbildningen. I Background Image Builder används mallen och källfilerna för att skapa en virtuell dator (standard storlek: Standard_D1_v2), nätverk, offentlig IP, NSG och lagring i \<DestinationResourceGroup> resurs gruppen IT_ _ _ \<TemplateName> .
1. Som en del av avbildningen distribuerar Image Builder avbildningen enligt mallen och tar sedan bort de ytterligare resurserna i den IT_ \<DestinationResourceGroup> _ \<TemplateName> resurs grupp som skapades för processen.


## <a name="permissions"></a>Behörigheter
När du registrerar dig för (AIB) ger detta behörigheten AIB-tjänst för att skapa, hantera och ta bort en resurs grupp för mellanlagring (IT_ *) och har behörighet att lägga till resurser i den, vilket krävs för avbildnings versionen. Detta görs av ett AIB tjänst huvud namn (SPN) som görs tillgängligt i prenumerationen vid en lyckad registrering.

Om du vill tillåta att Azure VM Image Builder distribuerar avbildningar till antingen de hanterade avbildningarna eller till ett delat avbildnings Galleri måste du skapa en Azure User-tilldelad identitet som har behörighet att läsa och skriva bilder. Om du använder Azure Storage måste du ha behörighet att läsa privata behållare.

Från början måste du följa anvisningarna för att [skapa en Azure-tilldelad identitets](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) dokumentation om hur du skapar en identitet.

När du har identiteten som du behöver för att ge IT-behörighet kan du använda en anpassad roll definition i Azure och sedan tilldela den användare som tilldelats den hanterade identiteten att använda den anpassade roll definitionen.

Behörigheter förklaras i detalj [här](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)och exemplen visar hur detta implementeras.

> [!Note]
> Tidigare med AIB använder du AIB SPN och ger SPN-behörighet till avbildnings resurs grupperna. Vi går vidare från den här modellen, så att du kan använda framtida funktioner. Från 26 maj 2020 kan Image Builder inte ta emot mallar som inte har någon tilldelad identitet, befintliga mallar måste skickas igen till tjänsten med en [användar identitet](./linux/image-builder-json.md). Exemplen här visar redan hur du kan skapa en användardefinierad identitet och lägga till dem i en mall. Mer information finns i den här [dokumentationen](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) om den här ändringen och uppdateringar.

## <a name="costs"></a>Kostnader
Du kommer att ådra dig några beräknings-, nätverks-och lagrings kostnader när du skapar, skapar och lagrar avbildningar med Azure Image Builder. Dessa kostnader liknar kostnaderna för att skapa anpassade avbildningar manuellt. För resurserna debiteras du enligt dina Azure-priser. 

Under processen för att skapa avbildningar laddas filerna ned och lagras i `IT_<DestinationResourceGroup>_<TemplateName>` resurs gruppen, vilket innebär en liten lagrings kostnad. Om du inte vill behålla dessa raderar du **avbildnings mal len** efter att avbildningen har byggts.
 
Image Builder skapar en virtuell dator med en D1v2 VM-storlek, och lagrings utrymme och nätverk som krävs för den virtuella datorn. Dessa resurser kommer att vara sist under Bygg processen och tas bort när avbildnings verktyget har skapat avbildningen. 
 
Azure Image Builder distribuerar avbildningen till dina valda regioner, vilket kan innebära att det tar betalt för nätverket.

## <a name="hyper-v-generation"></a>Hyper-V-generering
Image Builder har för närvarande endast stöd för att skapa Hyper-V generation (gen1) 1-avbildningar i Azure-galleriet för delad avbildning (SIG) eller hanterad avbildning. Om du vill skapa Gen2-avbildningar måste du använda en käll Gen2 avbildning och distribuera till VHD. Därefter måste du skapa en hanterad avbildning från den virtuella hård disken och mata in den i SIG som en Gen2-avbildning.
 
## <a name="next-steps"></a>Nästa steg 
 
Om du vill prova Azure Image Builder kan du läsa artikeln om att skapa [Linux](./linux/image-builder.md) -eller [Windows](./windows/image-builder.md) -avbildningar.

