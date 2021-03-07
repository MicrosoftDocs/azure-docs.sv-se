---
title: Lär dig mer om Azure Image Builder (för hands version)
description: Lär dig mer om Azure Image Builder för virtuella datorer i Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 0e72c35af1f1990527b0154d2ba47a45d3f8b8c9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425637"
---
# <a name="preview-azure-image-builder-overview"></a>För hands version: översikt över Azure Image Builder

Med standardiserade avbildningar av virtuella datorer kan organisationer migrera till molnet och säkerställa konsekvens i distributionerna. Bilder innehåller vanligt vis fördefinierade säkerhets-och konfigurations inställningar och nödvändig program vara. Att konfigurera din egen avbildnings pipeline kräver tid, infrastruktur och konfiguration, men med Azure VM Image Builder kan du bara ange en konfiguration som beskriver din avbildning, skicka den till tjänsten och avbildningen skapas och distribueras.
 
Med Azures avbildnings verktyg för virtuella datorer (Azure Image Builder) kan du starta med en Windows-eller Linux-baserad Azure Marketplace-avbildning, befintliga anpassade avbildningar och börja lägga till dina egna anpassningar. Eftersom Image Builder bygger på [HashiCorp Packer](https://packer.io/) kommer du att se vissa likheter, men har fördelen med en hanterad tjänst. Du kan också ange var du vill att dina avbildningar ska finnas i [Azures Galleri för delad avbildning](shared-image-galleries.md), som en hanterad avbildning eller en virtuell hård disk.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Förhandsgranskningsfunktioner

I för hands versionen stöds dessa funktioner:

- Skapa bas linje avbildningar som innehåller dina lägsta säkerhets-och företags konfigurationer och gör det möjligt för avdelningar att anpassa dem ytterligare.
- Integrering av kärn program, så att virtuella datorer kan ta på arbets belastningar när de har skapats, eller lägga till konfigurationer som stöder Windows virtuella Skriv bords avbildningar.
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

## <a name="how-it-works"></a>Så här fungerar det

Image Builder för Azure VM är en fullständigt hanterad Azure-tjänst som kan nås av en Azure-adressresurs. Ange en konfiguration för tjänsten som anger käll avbildningen, anpassningen som ska utföras och var den nya avbildningen ska distribueras till. i diagrammet nedan visas ett arbets flöde med hög nivå:

![Konceptuell ritning i Azure Image Builder-processen som visar källorna (Windows/Linux), anpassningar (Shell, PowerShell, omstart av Windows & Update, lägga till filer) och global distribution med Azures delade avbildnings Galleri](./media/image-builder-overview/image-builder-flow.png)

Konfiguration av mallar kan skickas med PowerShell, AZ CLI, ARM-mallar och med Azure VM Image Builder DevOps-uppgiften när du skickar den till tjänsten skapar vi en resurs för bildmallar. När resursen avbildnings mal len har skapats visas en mellanlagringsplats som skapats i din prenumeration i formatet: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Den mellanliggande resurs gruppen innehåller filer och skript som refereras till i filen, skalet, PowerShell-anpassningen i egenskapen ScriptURI.

För att du ska kunna köra den version du anropar `Run` , kommer tjänsten att distribuera ytterligare resurser för bygget, till exempel en virtuell dator, ett nätverk, en disk, ett nätverkskort osv. Om du skapar en avbildning utan att använda ett befintligt VNET-avbildnings verktyg för att distribuera en offentlig IP-adress och en NSG, ansluter tjänsten till den virtuella datorns build med SSH eller WinRM. Om du väljer ett befintligt virtuellt nätverk kommer tjänsten att distribueras med hjälp av en privat Azure-länk, och det krävs ingen offentlig IP-adress. mer information om Image Builder-nätverk finns i [informationen](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking).

När versionen har slutförts tas alla resurser bort, förutom den mellanlagringsplatsen och lagrings kontot för att ta bort dessa, tar du bort bildmallens resurs, eller så kan du lämna dem där för att köra versionen igen.

Det finns flera exempel och steg-för-steg-guider i den här dokumentationen, som refererar till konfigurationsfiler och lösningar i [Azure Image Builder GitHub-lagringsplatsen](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Flytta support
Resursen för bildmallar är oföränderlig och innehåller länkar till resurser och mellanlagrings resurs gruppen, och därför stöder inte resurs typen att flyttas. Om du vill flytta en resurs för bildmallar, se till att du har en kopia av konfigurations mal len (extrahera den befintliga konfigurationen från resursen om du inte redan har den), skapa en ny avbildnings mal len resurs i den nya resurs gruppen med ett nytt namn och ta bort den tidigare bildmallens resurs. 

## <a name="permissions"></a>Behörigheter
När du registrerar dig för (AIB) ger detta behörigheten AIB-tjänst för att skapa, hantera och ta bort en resurs grupp för mellanlagring (IT_ *) och har behörighet att lägga till resurser i den, vilket krävs för avbildnings versionen. Detta görs av ett AIB tjänst huvud namn (SPN) som görs tillgängligt i prenumerationen vid en lyckad registrering.

Om du vill tillåta att Azure VM Image Builder distribuerar avbildningar till antingen de hanterade avbildningarna eller till ett delat avbildnings Galleri måste du skapa en Azure User-tilldelad identitet som har behörighet att läsa och skriva bilder. Om du använder Azure Storage måste du ha behörighet att läsa privata och offentliga behållare.

Behörigheter förklaras i detalj för [PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-powershell)och [AZ CLI](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-cli).

## <a name="costs"></a>Kostnader
Du kommer att ådra dig några beräknings-, nätverks-och lagrings kostnader när du skapar, skapar och lagrar avbildningar med Azure Image Builder. Dessa kostnader liknar kostnaderna för att skapa anpassade avbildningar manuellt. För resurserna debiteras du enligt dina Azure-priser. 

Under processen för att skapa avbildningar laddas filerna ned och lagras i `IT_<DestinationResourceGroup>_<TemplateName>` resurs gruppen, vilket innebär en liten lagrings kostnad. Om du inte vill behålla dessa raderar du **avbildnings mal len** efter att avbildningen har byggts.
 
Image Builder skapar en virtuell dator med en D1v2 VM-storlek, och lagrings utrymme och nätverk som krävs för den virtuella datorn. Dessa resurser kommer att vara sist under Bygg processen och tas bort när avbildnings verktyget har skapat avbildningen. 
 
Azure Image Builder distribuerar avbildningen till dina valda regioner, vilket kan innebära att det tar betalt för nätverket.

## <a name="hyper-v-generation"></a>Hyper-V-generering
Image Builder har för närvarande endast stöd för att skapa Hyper-V generation (gen1) 1-avbildningar i Azure-galleriet för delad avbildning (SIG) eller hanterad avbildning. 
 
## <a name="next-steps"></a>Nästa steg 
 
Om du vill prova Azure Image Builder kan du läsa artikeln om att skapa [Linux](./linux/image-builder.md) -eller [Windows](./windows/image-builder.md) -avbildningar.

