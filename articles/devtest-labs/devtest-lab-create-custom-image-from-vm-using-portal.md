---
title: Skapa en Azure DevTest Labs anpassad avbildning från en virtuell dator | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning i Azure DevTest Labs från en etablerad virtuell dator med hjälp av Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87277035"
---
# <a name="create-a-custom-image-from-a-vm"></a>Skapa en anpassad avbildning från en virtuell dator

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Du kan skapa en anpassad avbildning från en etablerad virtuell dator och sedan använda den anpassade avbildningen för att skapa identiska virtuella datorer. Följande steg visar hur du skapar en anpassad avbildning från en virtuell dator:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du önskat labb.  

1. I Labbets huvud fönster väljer du **mina virtuella datorer**.
 
1. I fönstret **mina virtuella datorer** väljer du den virtuella dator som du vill skapa den anpassade avbildningen från.

1. I fönstret hantering av virtuell dator väljer du **Skapa anpassad avbildning** under **åtgärder**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Meny alternativet Skapa anpassad avbildning":::
1. I fönstret **anpassad avbildning** anger du ett namn och en beskrivning för din anpassade avbildning. Den här informationen visas i listan över baser när du skapar en virtuell dator. Den anpassade avbildningen innehåller OS-disken och alla data diskar som är anslutna till den virtuella datorn.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Sidan Skapa anpassad avbildning":::
1. Välj om Sysprep kördes på den virtuella datorn. Om Sysprep inte kördes på den virtuella datorn anger du om du vill att Sysprep ska köras på den virtuella datorn när den anpassade avbildningen skapas.
1. Välj **OK** när du är färdig för att skapa den anpassade avbildningen.

    Efter några minuter skapas den anpassade avbildningen och lagras i Labbets lagrings konto. När en labb användare vill skapa en ny virtuell dator är avbildningen tillgänglig i listan med bas avbildningar.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="anpassad avbildning tillgänglig i listan med bas avbildningar":::

## <a name="related-blog-posts"></a>Relaterade blogg inlägg

- [Anpassade bilder eller formler?](./devtest-lab-faq.md#blog-post)
- [Kopiera anpassade bilder mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)
