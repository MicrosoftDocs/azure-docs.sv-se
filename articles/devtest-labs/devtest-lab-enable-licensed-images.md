---
title: Aktivera en licensierad avbildning i ditt labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du aktiverar en licensierad avbildning i Azure DevTest Labs att använda Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91276364"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Aktivera en licensierad avbildning i labbet i Azure DevTest Labs

I Azure DevTest Labs är en licensierad avbildning en licensierad avbildning som innehåller villkor – vanligt vis från en tredje part – som måste godkännas innan avbildningen kan nås av användare i labbet. I följande avsnitt beskrivs hur du arbetar med licensierade bilder så att de kan användas för att skapa virtuella datorer.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Avgöra om en licensierad avbildning är tillgänglig för användare
Det första steget för att tillåta användare att skapa virtuella datorer från en licensierad avbildning är att se till att villkoren har godkänts för den licensierade avbildningen. Följande steg visar hur du kan visa en licensierad avbildnings erbjudande status och, om det behövs, att godkänna dess allmänna villkor.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du önskat labb.  

1. I den vänstra panelen under **Inställningar** väljer du **konfiguration och principer**.

1. Välj **Marketplace-avbildningar** i den vänstra panelen under **virtuella dator baser**. 

    ![Meny alternativ för Marketplace-bilder](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    En lista över alla tillgängliga Marketplace-avbildningar visas, inklusive **erbjudande status** för varje bild.

    ![Lista över Marketplace-bilder som visar erbjudande status för varje bild](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    En licensierad bild visar en erbjudande status för 
    
    - **Accepterade villkor:** den licensierade avbildningen är tillgänglig för användare att skapa virtuella datorer. 
    - **Villkors granskning krävs:** den licensierade avbildningen är för närvarande inte tillgänglig för användare. Villkoren för licensen måste godkännas innan labb användare kan använda den för att skapa virtuella datorer. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Göra en licensierad avbildning tillgänglig för labb användare
För att se till att en licensierad avbildning är tillgänglig för labb användare måste en labb ägare med administratörs behörighet först godkänna de allmänna villkoren för den licensierade avbildningen. Att aktivera program distribution för den prenumeration som är associerad med en licensierad avbildning accepterar automatiskt de juridiska villkoren och sekretess policyn för avbildningen. Att [arbeta med Marketplace-avbildningar på Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) ger ytterligare information om programmerings distribution av Marketplace-avbildningar.

Du kan aktivera program distribution för en licensierad avbildning genom att följa dessa steg:

1. I [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)går du till listan över Marketplace- **avbildningar**.

1. Identifiera en licensierad avbildning som du vill att användarna ska ha åtkomst till men vars villkor inte har accepterats. Du kan till exempel se en Data Science Virtual Machine som visar statusen **godkänd** eller **villkors granskning som behövs**.

    ![Skärm bild som visar Data Science Virtual Machine bilder med erbjudande status "av" villkor accepterade "och" villkors granskning krävs "](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Virtuella datorer för data vetenskap är avbildningar av virtuella Azure-datorer, förinstallerade, konfigurerade och testade med flera populära verktyg som ofta används för data analys, Machine Learning och AI-utbildning. [Introduktion till Azure Data Science Virtual Machine för Linux och Windows](../machine-learning/data-science-virtual-machine/overview.md) innehåller en fantastisk information om dsvm.
   >
   >

1. I kolumnen **erbjudande status** för avbildningen väljer du **villkors granskning krävs**.

1. I fönstret Konfigurera programprogram distribution väljer du **Aktivera**.

    ![Fönstret Konfigurera program mässig distribution](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Du kan se flera prenumerationer som anges i fönstret Konfigurera programdistribution. Se till att du aktiverar program mässig distribution enbart för den avsedda prenumerationen.
   >
   >


1. Välj **Spara**. 

    I listan över Marketplace-avbildningar visar bilden nu **villkor som godkänts** och är tillgänglig för användare att skapa virtuella datorer.

> [!NOTE]
> Användare kan skapa en anpassad avbildning från en licensierad avbildning. Mer information finns i [skapa en anpassad avbildning från en VHD-fil](devtest-lab-create-template.md) .
>
>


## <a name="related-blog-posts"></a>Relaterade blogg inlägg

- [Anpassade bilder eller formler?](./devtest-lab-faq.md#blog-post)
- [Kopiera anpassade bilder mellan Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning från en virtuell dator](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Skapa en anpassad avbildning från en VHD-fil](devtest-lab-create-template.md)
- [Lägg till en virtuell dator i labbet](devtest-lab-add-vm.md)
