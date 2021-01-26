---
title: Använd ett delat avbildnings galleri i Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb konto för att använda ett delat bild galleri så att en användare kan dela en avbildning med andra och en annan användare kan använda avbildningen för att skapa en mall för virtuella datorer i labbet.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 984cb4c47099928ffab327895a728cbe8a8f9604
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791398"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använd ett delat avbildnings galleri i Azure Lab Services
Den här artikeln visar hur lärare och labb administratörer kan spara en mall för virtuella dator avbildningar i ett [delat bild galleri](../virtual-machines/shared-image-galleries.md) så att de kan användas av andra för att skapa labb. 

> [!IMPORTANT]
> När du använder ett delat bild galleri Azure Lab Services stöder bara bilder med mindre än 128 GB OS-disk utrymme. Avbildningar med mer än 128 GB disk utrymme eller flera diskar visas inte i listan över avbildningar av virtuella datorer under labb skapandet.

## <a name="scenarios"></a>Scenarier
Här är några av de scenarier som stöds av den här funktionen: 

- En labb konto administratör bifogar ett delat avbildnings galleri till labb kontot och laddar upp en bild till galleriet för delad avbildning utanför kontexten för ett labb. Sedan kan Lab-skapare använda avbildningen från galleriet för delad avbildning för att skapa labb labb. 
- Ett labb konto administratör bifogar ett delat avbildnings galleri till labb kontot. En labb skapare (instruktör) sparar den anpassade avbildningen av sitt labb labb i det delade avbildnings galleriet. Sedan kan andra labb skapare välja den här avbildningen från galleriet för delade avbildningar för att skapa en mall för sina labb. 

    När en bild sparas i ett delat bild galleri, Azure Lab Services replikerar den sparade avbildningen till andra regioner som är tillgängliga i samma [geografi](https://azure.microsoft.com/global-infrastructure/geographies/). Det garanterar att avbildningen är tillgänglig för labb som skapats i andra regioner i samma geografi. Att spara bilder i ett delat avbildnings Galleri debiteras ytterligare en kostnad, vilket innefattar kostnad för alla replikerade avbildningar. Kostnaden är separat från Azure Lab Services användnings kostnad. Mer information om priser för delade avbildnings gallerier finns i avsnittet [Shared Image Gallery – fakturering]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Krav
- Skapa ett galleri för delad avbildning med hjälp av antingen [Azure PowerShell](../virtual-machines/shared-images-powershell.md) eller [Azure CLI](../virtual-machines/shared-images-cli.md).
- Du har kopplat det delade avbildnings galleriet till labb kontot. Stegvisa instruktioner finns i [så här ansluter du eller kopplar från delade avbildnings galleriet](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en bild i galleriet för delade avbildningar
När ett delat avbildnings Galleri har kopplats kan en labb konto administratör eller en lärare Spara en bild i det delade bild galleriet så att den kan återanvändas av andra lärare. 

1. På **mall** -sidan för labbet väljer du **Exportera till delat avbildnings Galleri** i verktygsfältet.

    ![Knappen Spara bild](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. I dialog rutan **Exportera till delad avbildnings Galleri** anger **du ett namn för avbildningen** och väljer sedan **Exportera**. 

    ![Dialog rutan exportera till delat bild galleri](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Du kan se förloppet för den här åtgärden på **mallsidan** . Den här åtgärden kan ta en stund. 

    ![Export pågår](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. När export åtgärden lyckas visas följande meddelande:

    ![Export slutförd](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    När du har sparat avbildningen i det delade bild galleriet kan du använda den bilden från galleriet när du skapar ett nytt labb. Du kan också ladda upp en avbildning till galleriet för delade avbildningar utanför kontexten för ett labb. Mer information finns i [Översikt över Shared Image Gallery](../virtual-machines/shared-images-powershell.md). 

    > [!IMPORTANT]
    > När du [sparar en mall bild av ett labb](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) i Azure Lab Services till ett delat bild galleri laddas bilden upp till galleriet som en **specialiserad bild**. [Specialiserade avbildningar](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) behåller datorspecifik information och användar profiler. Du kan fortfarande ladda upp en generaliserad avbildning direkt till galleriet utanför Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använd en avbildning från galleriet för delad avbildning
En lärare kan välja en anpassad avbildning som är tillgänglig i galleriet för delade avbildningar för mallen när nya labb skapas.

![Använd avbildningen av en virtuell dator från galleriet](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Du kan skapa en mall för virtuella datorer baserat på både **generaliserade** och **specialiserade** avbildningar i Azure Lab Services. 


## <a name="next-steps"></a>Nästa steg
Mer information om delade avbildnings gallerier finns i avsnittet [Shared Image Gallery](../virtual-machines/shared-image-galleries.md).