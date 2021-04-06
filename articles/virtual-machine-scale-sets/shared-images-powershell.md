---
title: Använd delade VM-avbildningar för att skapa en skalnings uppsättning i Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att skapa delade VM-avbildningar som ska användas för distribution av skalnings uppsättningar för virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3ba826de47255143c8adefe4424448d0b80ba105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678346"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Skapa och Använd delade avbildningar för skalnings uppsättningar för virtuella datorer med Azure PowerShell

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Tjänsten Shared Image Gallery fören klar anpassningen av anpassade bilder i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner, inom en AAD-klient. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar logiskt. 

Galleriet är en resurs på den översta nivån som ger fullständig Azure-rollbaserad åtkomst kontroll (Azure RBAC). Avbildningar kan vara versioner och du kan välja att replikera varje avbildnings version till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder. 

Funktionen för delad bild galleri har flera resurs typer. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Du måste ha en befintlig hanterad avbildning för att kunna slutföra exemplet i den här artikeln. Du kan följa [Självstudier: skapa och använda en anpassad avbildning för skalnings uppsättningar för virtuella datorer med Azure PowerShell](tutorial-use-custom-image-powershell.md) för att skapa en om det behövs. Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

När du arbetar genom artikeln ersätter du resurs gruppen och VM-namnen där det behövs.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Nästa steg

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade avbildnings gallerier finns i [översikten](../virtual-machines/shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](../virtual-machines/troubleshooting-shared-images.md).