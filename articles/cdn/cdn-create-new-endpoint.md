---
title: Snabb start – skapa en profil och slut punkt för Azure CDN
description: Den här snabbstarten visar hur du aktiverar Azure CDN genom att skapa en ny CDN-profil och CDN-slutpunkt.
author: asudbring
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e8b3b5c28d00b4d7c81fa3e245113119ae3134ad
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555711"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt

I den här snabb starten aktiverar du Azure Content Delivery Network (CDN) genom att skapa en ny CDN-profil, som är en samling av en eller flera CDN-slutpunkter. När du har skapat en profil och en slutpunkt kan du börja leverera innehåll till dina kunder.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto med namnet *cdnstorageacct123* som du använder för ursprungs värd namnet. För att slutföra det här kravet, se [integrera ett Azure Storage-konto med Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt

När du har skapat en CDN-profil använder du den för att skapa en slut punkt.

1. På instrumentpanelen i Azure Portal väljer du den CDN-profil som du skapade. Om du inte hittar det kan du antingen öppna resurs gruppen där du skapade den eller använda Sök fältet längst upp i portalen, ange profil namnet och välja profilen från resultaten.
   
1. På sidan CDN-profil väljer du **+ slut punkt**.
   
    ![CDN-profil](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Fönstret **Lägg till slutpunkt** visas.

3. Ange följande inställnings värden:

    | Inställning | Värde |
    | ------- | ----- |
    | **Namn** | Ange *CDN-Endpoint-123* för slut punktens värdnamn. Det här namnet måste vara globalt unikt i Azure. Ange ett annat namn om det redan används. Det här namnet används för att komma åt dina cachelagrade resurser på domänen _&lt; slut &gt; punkt-Name_. azureedge.net.|
    | **Ursprungstyp** | Välj **lagring**. | 
    | **Ursprungets värdnamn** | Välj värd namnet för det Azure Storage konto som du använder i list rutan, till exempel *cdnstorageacct123.blob.Core.Windows.net*. |
    | **Sökväg till ursprung** | Lämna tomt. |
    | **Ursprungsvärdadress** | Lämna standardvärdet (vilket är värd namnet för lagrings kontot). |  
    | **Protokoll** | Använd de valda standardalternativen för **HTTP** och **HTTPS**. |
    | **Ursprungsport** | Använd standardportvärdena. | 
    | **Optimerat för** | Använd standardvalet **Allmän webbleverans**. |

    ![Fönstret Lägg till slutpunkt](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Välj **Lägg till** för att skapa den nya slutpunkten. När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
   ![CDN-slutpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Hur lång tid det tar att sprida slut punkten beror på vilken pris nivå som väljs när du skapade profilen. **Standard Akamai** slutförs vanligt vis inom en minut, **standard Microsoft** på 10 minuter och **standard** Verizon och **Premium Verizon** på upp till 30 minuter.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du en CDN-profil och en CDN-slutpunkt i en resursgrupp. Spara dessa resurser om du vill gå till [Nästa steg](#next-steps) och lära dig hur du lägger till en anpassad domän i din slutpunkt. Men om du inte tänker använda de här resurserna i framtiden kan du ta bort dem genom att ta bort resursgruppen (på så sätt undviker du ytterligare kostnader):

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal och välj sedan **CDNQuickstart-RG**.

2. Välj **ta bort resurs grupp** på sidan **resurs grupp** , ange *CDNQuickstart-RG* i text rutan och välj sedan **ta bort**. Den här åtgärden tar bort resurs gruppen, profilen och slut punkten som du skapade i den här snabb starten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Använd CDN för att hantera statiskt innehåll från en webbapp](cdn-add-to-web-app.md)
