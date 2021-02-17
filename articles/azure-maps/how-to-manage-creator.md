---
title: Hantera Microsoft Azure Maps Creator (förhands granskning)
description: I den här artikeln får du lära dig hur du hanterar Microsoft Azure Maps Creator (för hands version).
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559151"
---
# <a name="manage-azure-maps-creator-preview"></a>Hantera Azure Maps skapare (förhands granskning) 

> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Maps Creator kan du skapa privat indata från kartan. Med hjälp av Azure Maps API och modulen kartor kan du utveckla interaktiva och dynamiska kart webb program. För närvarande är Creator endast tillgängligt i USA med hjälp av pris nivån S1.

Den här artikeln tar dig igenom stegen för att skapa och ta bort en skapare-resurs i ett Azure Maps konto.

## <a name="create-creator-preview-resource"></a>Skapa skapare (förhands granskning) resurs

1. Logga in på [Azure-portalen](https://portal.azure.com)

2. Välj ditt Azure Maps-konto. Om du inte kan se ditt Azure Maps konto under de **senaste resurserna** går du till Azure Portal-menyn. Välj **Alla resurser**. Sök efter och välj ditt Azure Maps-konto.

    ![Azure Maps portalens start sida](./media/how-to-manage-creator/select-maps-account.png)

3. När du är på sidan Azure Maps konto går du till **översikts** alternativet under **skapare**. Välj  **skapa**  för att skapa en Azure Maps skapare-resurs.

    ![Sidan Skapa Azure Maps skapare](./media/how-to-manage-creator/creator-blade-settings.png)

4. Ange namn och plats för din skapare resurs. För närvarande stöds endast skapare i USA. Välj **Granska + skapa**.

   ![Sidan Ange skapare konto information](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Granska inställningarna och välj **skapa**.

    ![Sidan Bekräfta inställningar för skapare av konto](./media/how-to-manage-creator/creator-create-dialog.png)

6. När distributionen är klar visas en sida med ett lyckat eller ett fel meddelande.

   ![Status sida för resurs distribution](./media/how-to-manage-creator/creator-resource-created.png)

7. Välj **Gå till resurs**. Sidan skapare resurs visning visar status för din skapare-resurs och den valda demografiska regionen.

    ![Sidan status för skapare](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >På sidan skapare resurs kan du gå tillbaka till det Azure Maps konto som den tillhör genom att välja Azure Maps konto.

## <a name="delete-creator-preview-resource"></a>Ta bort skapare (förhands granskning) resurs

Om du vill ta bort skapare-resursen går du till ditt Azure Maps-konto. Välj **Översikt** under **skapare**. Välj knappen **Ta bort**.

>[!WARNING]
>När du tar bort skapare-resursen för ditt Azure Maps-konto tar du även bort data uppsättningarna, tilesets och funktionen statesets som skapats med hjälp av Creator Services.

![Sidan skapare med knappen Ta bort](./media/how-to-manage-creator/creator-delete.png)

Välj knappen **ta bort** och skriv namnet på din skapare för att bekräfta borttagningen. När resursen har tagits bort visas en bekräftelse sida, som i bilden nedan:

![Sidan skapare med bekräftelse på borttagning](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Autentisering

Skapare (förhands granskning) ärver inställningar för Azure Maps Access Control (IAM). Alla API-anrop för data åtkomst måste skickas med regler för autentisering och auktorisering.

Skapare användnings data ingår i Azure Maps användnings diagram och aktivitets loggen.  Mer information finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Åtkomst till Creator-tjänster

Creator Services (för hands version) och tjänster som använder data som finns i skapare (till exempel rendering service), är tillgängliga på en geografisk URL. Den geografiska URL: en bestäms av den plats som valdes när den skapas. Om Creator till exempel skapas på den USA geografiska platsen, måste alla anrop till konverterings tjänsten skickas till `us.atlas.microsoft.com/conversion/convert` .

Alla data som importeras till skaparen bör också överföras till samma geografiska plats som skaparen-resursen. Om skapare till exempel är etablerad i USA, ska alla rå data överföras via `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Nästa steg

Introduktion till Creator Services (för hands version) för inlednings mappning:

> [!div class="nextstepaction"]
> [Data uppladdning](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Data konvertering](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Data uppsättning](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Funktions tillstånds uppsättning](creator-indoor-maps.md#feature-statesets)

Lär dig hur du använder Creator Services (för hands version) för att återge inaktuella kartor i ditt program:

> [!div class="nextstepaction"]
> [Själv studie kurs om Azure Maps Creator](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamisk formatering för inomhus-karta](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Använda modulen inomhus Maps](how-to-use-indoor-module.md)