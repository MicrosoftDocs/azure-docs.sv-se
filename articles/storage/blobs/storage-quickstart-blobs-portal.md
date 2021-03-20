---
title: Snabb start – skapa en blob med Azure Portal
titleSuffix: Azure Storage
description: I den här snabbstarten använder du Azure-portalen i objektlagring (Blob). Sedan använder du Azure-portalen och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523339"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av Azure-portalen

I den här snabbstarten får du lära dig att använda [Azure Portal](https://portal.azure.com/) för att skapa en container i Azure Storage, samt ladda upp och ladda ned blockblobar i den containern.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Skapa en container

Följ dessa steg för att skapa en container i Azure Portal:

1. Navigera till ditt nya lagringskonto i Azure Portal.
2. I den vänstra menyn för lagrings kontot, bläddrar du till avsnittet **BLOB service** och väljer **behållare**.
3. Välj knappen **+ Container**.
4. Ange ett namn för den nya containern. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Ställ in nivån för allmän åtkomst till containern. Standardnivån är **Privat (ingen anonym åtkomst)**.
6. Klicka på **OK** för att skapa containern.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Skärmbild som visar hur du skapar en container i Azure Portal":::

## <a name="upload-a-block-blob"></a>Ladda upp en blockblob

Blockblobar består av datablock som har satts samman för att bilda en blob. De flesta scenarier med bloblagring använder blockblobar. Blockblobar är utmärkta för att lagra text eller binära data i molnet, t.ex. filer, avbildningar och videor. Den här snabbstarten visar hur du arbetar med blockblobar.

Följ de här stegen för att ladda upp en blockblob till den nya containern i Azure Portal:

1. Navigera till den container som du skapade i föregående avsnitt i Azure Portal.
1. Välj containern för att visa en lista över blobar som den innehåller. Den här behållaren är ny, så den kommer ännu inte att innehålla några blobbar.
1. Välj knappen **överför** för att öppna bladet överför och bläddra i det lokala fil systemet för att hitta en fil som ska laddas upp som en Block-Blob. Du kan också expandera avsnittet **Avancerat** om du vill konfigurera andra inställningar för överförings åtgärden.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Skärm bild som visar hur du laddar upp en BLOB från den lokala enheten via Azure Portal":::

1. Välj knappen **överför** för att överföra blobben.
1. Ladda upp så många blobar du vill på det här sättet. Du kan se att de nya blobarna nu visas i listan i containern.

## <a name="download-a-block-blob"></a>Ladda ned en blockblob

Du kan ladda ned en blockblob som ska visas i webbläsaren eller spara den i det lokala filsystemet. Följ de här stegen om du vill ladda ned en blockblob:

1. Gå till listan över blobar som du överförde i föregående avsnitt.
1. Högerklicka på den blob som du vill ladda ned och välj **Ladda ned**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Skärmbild som visar hur du laddar ned en blob i Azure Portal":::

## <a name="delete-a-block-blob"></a>Ta bort en Block-Blob

Följ dessa steg om du vill ta bort en eller flera blobbar i Azure Portal:

1. I Azure Portal navigerar du till behållaren.
1. Visa listan över blobbar i behållaren.
1. Använd kryss rutan för att välja en eller flera blobbar från listan.
1. Klicka på knappen **ta bort** för att ta bort de valda Blobbarna.
1. I dialog rutan bekräftar du borttagningen och anger om du även vill ta bort BLOB-ögonblicksbilder.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Skärm bild som visar hur du tar bort blobbar från Azure Portal":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du skapade i den här snabb starten kan du helt enkelt ta bort behållaren. Alla blobar i containern tas också bort.

Ta bort containern:

1. Gå till listan med containrar i ditt lagringskonto i Azure Portal.
1. Välj den container som ska tas bort.
1. Välj knappen **Mer** (**...**) och välj **Ta bort**.
1. Bekräfta att du vill ta bort containern.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en behållare och laddar upp en blob med Azure Portal. Om du vill lära dig mer om att arbeta med Blob Storage från en webbapp fortsätter du till en självstudie som visar hur du laddar upp avbildningar till ett lagrings konto.

> [!div class="nextstepaction"]
> [Självstudie: Ladda upp bilddata i molnet med Azure Storage](storage-upload-process-images.md)