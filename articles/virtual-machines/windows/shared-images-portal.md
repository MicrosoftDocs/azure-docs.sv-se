---
title: Skapa ett galleri för Azure-delad avbildning med hjälp av portalen
description: Lär dig hur du använder Azure Portal för att skapa och dela avbildningar av virtuella datorer.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e28a1412fa417b9f15ab7a6f79305d07ca6d9fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556102"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Skapa ett galleri för Azure-delad avbildning med hjälp av portalen

Ett [delat bild galleri](../shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att starta distributions åtgärder som till exempel för att läsa in program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner, inom en AAD-klient. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar logiskt. 

Galleriet är en resurs på den översta nivån som ger fullständig Azure-rollbaserad åtkomst kontroll (Azure RBAC). Avbildningar kan vara versioner och du kan välja att replikera varje avbildnings version till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen för delad bild galleri har flera resurs typer. Vi kommer att använda eller skapa dessa i den här artikeln:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Skapa VM:ar

Nu kan du skapa en eller flera nya virtuella datorer. I det här exemplet skapas en virtuell dator med namnet *myVM* i *myResourceGroup* i data centret *USA, östra* .

1. Gå till din avbildnings definition. Du kan använda resurs filtret för att visa alla tillgängliga avbildnings definitioner.
1. På sidan för din avbildnings definition väljer du **Skapa virtuell dator** på menyn längst upp på sidan.
1. För **resurs grupp** väljer du **Skapa ny** och skriver *myResourceGroup* som namn.
1. Skriv *myVM* i **namn på virtuell dator**.
1. För **region** väljer du *östra USA*.
1. För **tillgänglighets alternativ** lämnar du standardvärdet *ingen redundans krävs*.
1. Värdet för **bild** fylls automatiskt med `latest` bild versionen om du startade från sidan för avbildnings definitionen.
1. I **storlek** väljer du en storlek på virtuell dator i listan över tillgängliga storlekar och väljer sedan **Välj**.
1. Om avbildningen generaliseras måste du ange ett användar namn, till exempel *azureuser* och ett lösen ord, under **Administratörs kontot**. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](faq.md#what-are-the-password-requirements-when-creating-a-vm). Om din avbildning var specialiserad kommer fälten användar namn och lösen ord att tonas ut eftersom användar namnet och lösen ordet för den virtuella käll datorn används.
1. Om du vill tillåta fjärråtkomst till den virtuella datorn under **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** i list rutan. Om du inte vill tillåta fjärråtkomst till den virtuella datorn lämnar du **inget** markerat för **offentliga inkommande portar**.
1. När du är färdig väljer du knappen **Granska + skapa** längst ned på sidan.
1. När den virtuella datorn har godkänts för validering väljer du **skapa** längst ned på sidan för att starta distributionen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

Om du vill ta bort enskilda resurser måste du ta bort dem i omvänd ordning. Om du till exempel vill ta bort en avbildnings definition måste du ta bort alla avbildnings versioner som skapats från avbildningen.

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade avbildnings gallerier finns i [översikten](../shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](../troubleshooting-shared-images.md).