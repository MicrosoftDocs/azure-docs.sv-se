---
title: Använda Azure Storage Explorer med Azure Data Lake Storage Gen2
description: Använd Azure Storage Explorer för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652283"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd Azure Storage Explorer för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att skapa och hantera kataloger och filer i lagrings konton med hierarkiskt namn område (HNS) aktiverat.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](../common/storage-account-create.md) anvisningarna för att skapa en.

- Azure Storage Explorer installerat på den lokala datorn. Information om hur du installerar Azure Storage Explorer för Windows, Macintosh och Linux finns i [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Storage Explorer använder sig av båda [slut punkterna](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) BLOB (blob) & data Lake Storage Gen2 (DFS) när du arbetar med Azure Data Lake Storage Gen2. Om åtkomst till Azure Data Lake Storage Gen2 har kon figurer ATS med hjälp av privata slut punkter, se till att två privata slut punkter skapas för lagrings kontot: en med den underordnade underresursen `blob` och den andra med mål under resursen `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Logga in på Storage Explorer

När du först startar Storage Explorer visas fönstret **Microsoft Azure Storage Explorer – anslut**. Storage Explorer erbjuder flera sätt att ansluta till lagringskonton, men för närvarande stöds bara ett sätt för att hantera ACL: er.

|Uppgift|Syfte|
|---|---|
|Lägga till ett Azure-konto | Omdirigerar dig till din organisations inloggnings sida för att autentisera dig för Azure. Det här är för närvarande den enda autentiseringsmetod som stöds om du vill hantera och ange ACL: er.|
|Använda en anslutningssträng eller en signatur-URI för delad åtkomst | Kan användas för direkt åtkomst till en container eller ett lagringskonto med en SAS-token eller en delad anslutningssträng. |
|Använda lagringskontots namn och nyckel| Använd lagringskontonamnet och nyckeln för ditt lagringskonto för att ansluta till Azure Storage.|

Välj **Lägg till ett Azure-konto** och klicka på **Logga in..**. Logga in på ditt Azure-konto genom att följa anvisningarna på skärmen.

![Skärm bild som visar Microsoft Azure Storage Explorer och markerar alternativet Lägg till ett Azure-konto och knappen Logga in.](media/storage-quickstart-blobs-storage-explorer/connect.png)

När anslutningen är klar läses Azure Storage Explorer in med fliken **Utforskaren** öppen. Den här vyn ger dig insikt om alla dina Azure Storage-konton samt lokal lagring som kon figurer ATS via [Azurite Storage-emulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -konton eller [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) miljöer.

![Fönstret Microsoft Azure Storage Explorer – anslut](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Skapa en container

En behållare innehåller kataloger och filer. Om du vill skapa ett expanderar du det lagrings konto som du skapade i steget Fortsätt. Välj **BLOB-behållare**, högerklicka på och välj **skapa BLOB-behållare**. Ange namnet på din behållare. Se avsnittet [skapa en behållare](storage-quickstart-blobs-dotnet.md#create-a-container) för en lista över regler och begränsningar för namn containrar. När du är klar trycker du på **RETUR** för att skapa behållaren. När behållaren har skapats visas den under mappen **BLOB containers** för det valda lagrings kontot.

![Microsoft Azure Storage Explorer – skapa en behållare](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Skapa en katalog

Om du vill skapa en katalog väljer du den behållare som du skapade i steget för att fortsätta. I menyfliksområdet container väljer du knappen **ny mapp** . Ange namnet på din katalog. När du är klar trycker du på **RETUR** för att skapa katalogen. När katalogen har skapats visas den i redigerings fönstret.

![Microsoft Azure Storage Explorer – skapa en katalog](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Ladda upp blobar till katalogen

I menyfliksområdet katalog väljer du knappen **överför** . Den här åtgärden ger dig möjlighet att ladda upp en mapp eller fil.

Välj de filer eller mappar du vill ladda upp.

![Microsoft Azure Storage Explorer – ladda upp en blob](media/data-lake-storage-explorer/upload-file.png)

När du väljer **OK** sätts de valda filerna i kö för uppladdning och varje fil laddas upp. När uppladdningen är klar visas resultatet i fönstret **Activities** (Aktiviteter).

## <a name="view-blobs-in-a-directory"></a>Visa blobar i en katalog

I programmet **Azure Storage Explorer** väljer du en katalog under ett lagringskonto. I huvudrutan visas en lista över blobarna i den valda katalogen.

![Microsoft Azure Storage Explorer – lista med blobar i en katalog](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Om du vill hämta filer med hjälp av **Azure Storage Explorer** väljer du **Ladda ned** från menyfliksområdet när en fil har valts. En fildialogruta öppnas och där du kan ange ett filnamn. Klicka på **Spara** för att starta nedladdningen av en fil till den lokala platsen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar fil-och katalog behörigheter genom att ange åtkomst kontrol listor (ACL: er)

> [!div class="nextstepaction"]
> [Använd Azure Storage Explorer för att hantera ACL: er i Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
