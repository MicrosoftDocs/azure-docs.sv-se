---
title: Åter givnings funktioner
description: Standard Azure Batch-funktioner används för att köra åter givning av arbets belastningar och appar. Batch innehåller vissa funktioner som stöder åter givning av arbets belastningar.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496345"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch åter givnings funktioner

Standard Azure Batch-funktioner används för att köra åter givning av arbets belastningar och program. Batch innehåller också vissa funktioner som stöder åter givning av arbets belastningar.

En översikt över batch-begrepp, inklusive pooler, jobb och aktiviteter, finns i [den här artikeln](./batch-service-workflow-features.md).

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Batch-pooler med anpassade VM-avbildningar och standard program licensiering

Precis som med andra arbets belastningar och typer av program kan en anpassad VM-avbildning skapas med nödvändiga åter givnings program och plugin-program. Den anpassade VM-avbildningen placeras i det [delade avbildnings galleriet](../virtual-machines/shared-image-galleries.md) och [kan användas för att skapa batch-pooler](batch-sig-images.md).

Kommando rads strängarna för aktiviteten måste referera till de program och sökvägar som används när du skapar den anpassade virtuella dator avbildningen.

De flesta åter givnings program kräver licenser som hämtats från en licens Server. Om det finns en befintlig lokal licens Server måste både poolen och licens servern finnas i samma [virtuella nätverk](../virtual-network/virtual-networks-overview.md). Det är också möjligt att köra en licens server på en virtuell Azure-dator, med batch-poolen och den virtuella licens servern som finns på samma virtuella nätverk.

## <a name="batch-pools-using-rendering-vm-images"></a>Batch-pooler som använder rendering av VM-avbildningar

> [!IMPORTANT]
> Avbildningarna för rendering av virtuella datorer och licenser för betalning för användning är [inaktuella och kommer att dras tillbaka den 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Om du vill använda batch för rendering ska du använda [en anpassad VM-avbildning och standard program licensiering.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Återge programinstallation

En Azure Marketplace-avbildning av en avbildning av virtuella datorer kan anges i konfigurationen av poolen om bara de förinstallerade programmen måste användas.

Det finns en Windows-avbildning och en CentOS-avbildning.  På [Azure Marketplace](https://azuremarketplace.microsoft.com)hittar du VM-avbildningarna genom att söka efter "batch-rendering".

Ett exempel på en pool konfiguration finns i [själv studie kursen för Azure CLI-åter givning](./tutorial-rendering-cli.md).  Azure Portal och Batch Explorer ger GUI-verktyg för att välja en avbildning av avbildning av avbildning när du skapar en pool.  Om du använder ett batch-API anger du följande egenskaps värden för [ImageReference](/rest/api/batchservice/pool/add#imagereference) när du skapar en pool:

| Publisher | Erbjudande | Sku | Version |
|---------|---------|---------|--------|
| batch | rendering – centos73 | Render | senaste |
| batch | rendering – windows2016 | Render | senaste |

Andra alternativ är tillgängliga om ytterligare program krävs på de virtuella datorerna i poolen:

* En anpassad avbildning från det delade avbildnings galleriet:
  * Med det här alternativet kan du konfigurera din virtuella dator med de exakta program och specifika versioner du behöver. Mer information finns i [skapa en pool med det delade avbildnings galleriet](batch-sig-images.md). Autodesk och kaos Group har ändrat Arnold och V-Ray för att verifiera mot en Azure Batch-klientlicensieringstjänsten. Se till att du har versioner av de här programmen med detta stöd, annars fungerar inte betala per användning-licensiering. Aktuella versioner av Maya eller 3DS Max kräver ingen licens Server vid körning av konsol lös läge (i batch-/kommando rads läge). Kontakta Azure-supporten om du inte är säker på hur du kan fortsätta med det här alternativet.
* [Programpaket](./batch-application-packages.md):
  * Paketera programfilerna med en eller flera ZIP-filer, ladda upp via Azure Portal och ange paketet i konfigurationen för poolen. När virtuella pooler skapas laddas ZIP-filerna ned och filerna extraheras.
* Resursfiler:
  * Programfiler laddas upp till Azure Blob Storage och du anger fil referenser i [Start aktiviteten för poolen](/rest/api/batchservice/pool/add#starttask). När virtuella pooler skapas laddas resursfiler ned till varje virtuell dator.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Betala för användnings licensiering för förinstallerade program

De program som ska användas och har en licens avgift måste anges i konfiguration av poolen.

* Ange `applicationLicenses` egenskapen när du [skapar en pool](/rest/api/batchservice/pool/add#request-body).  Följande värden kan anges i sträng mat ris-"Vray", "Arnold", "3dsmax", "Maya".
* När du anger ett eller flera program läggs kostnaden för dessa program till i kostnaden för de virtuella datorerna.  Program priserna visas på [sidan Azure Batch priser](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Om du i stället ansluter till en licens Server för att använda åter givnings programmen ska du inte ange `applicationLicenses` egenskapen.

Du kan använda Azure Portal eller Batch Explorer för att välja program och Visa program priserna.

Om ett försök görs att använda ett program, men programmet inte har angetts i `applicationLicenses` egenskapen för konfigurationen av poolen eller inte når någon licens Server, Miss lyckas program körningen med ett licens fel och en slutkod som inte är noll.

### <a name="environment-variables-for-pre-installed-applications"></a>Miljövariabler för förinstallerade program

Om du vill kunna skapa kommando raden för att återge aktiviteter måste installations platsen för program varan för rendering av program anges.  Systemmiljövariabler har skapats på virtuella Azure Marketplace-avbildningar, som kan användas i stället för att ange faktiska sökvägar.  De här miljövariablerna är förutom de [standard-batch-miljövariabler](./batch-compute-node-environment-variables.md) som skapas för varje aktivitet.

|Program|Körbar program fil|Miljö variabel|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Kaos Group V-Ray fristående|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Arnold 2020-kommando rad|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure VM-familjer

Precis som med andra arbets belastningar varierar kraven på program systemet och prestanda kraven varierar för jobb och projekt.  En stor mängd olika VM-familjer är tillgängliga i Azure beroende på dina krav – lägsta kostnad, bästa pris/prestanda, bästa prestanda och så vidare.
Vissa åter givnings program, till exempel Arnold, är CPU-baserade. andra som V-Ray-och blends-cykler kan använda processorer och/eller GPU: er.
En beskrivning av tillgängliga VM-familjer och VM-storlekar [finns i VM-typer och storlekar](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Lågprioriterade virtuella datorer

Precis som med andra arbets belastningar kan virtuella datorer med låg prioritet användas i batch-pooler för åter givning.  Virtuella datorer med låg prioritet utför samma som vanliga dedikerade virtuella datorer, men använder överskott i Azure-kapacitet och är tillgängliga för en stor rabatt.  Kompromissen med att använda virtuella datorer med låg prioritet är att de virtuella datorerna kanske inte är tillgängliga för tilldelning eller kan avbrytas när som helst, beroende på tillgänglig kapacitet. Av den anledningen kommer virtuella datorer med låg prioritet inte att vara lämpliga för alla åter givnings jobb. Om det till exempel tar flera timmar att rendera bilder, är det troligt att åter givningen av dessa avbildningar avbryts och startas om på grund av att de virtuella datorerna inte skulle vara acceptabla.

Mer information om egenskaperna för virtuella datorer med låg prioritet och de olika sätten att konfigurera dem med hjälp av batch finns i [använda virtuella datorer med låg prioritet med batch](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>Jobb och uppgifter

Inget stöd för åter givning krävs för jobb och uppgifter.  Huvud konfigurations objekt är aktivitets kommando raden som måste referera till det program som krävs.
När de virtuella Azure Marketplace-avbildningarna används är det bästa sättet att använda miljövariablerna för att ange sökvägen och programmets körbara fil.

## <a name="next-steps"></a>Nästa steg

Exempel på batch-åter givning testar de två självstudierna:

* [Rendering med hjälp av Azure CLI](./tutorial-rendering-cli.md)
* [Rendering med hjälp av Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)