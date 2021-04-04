---
title: Hantera Azure-filresurser med hjälp av Azure Storage Explorer
description: I den här snabbstarten lär du dig hur du använder Azure Storage Explorer till att hantera Azure Files.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629792"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Snabbstart: Skapa och hantera Azure-filresurser med Azure Storage Explorer
Den här guiden vägleder dig igenom grunderna i att arbeta med [Azure-filresurser](storage-files-introduction.md) med Azure Storage Explorer. Azure-filresurser är precis som andra filresurser men lagras i molnet och täcks av Azure-plattformen. Azure-filresurser stöder SMB-protokollet som är branschstandard och möjliggör fildelning på olika datorer, program och instanser. 

Azure Storage Explorer är ett populärt klientverktyg som finns för Windows, macOS och Linux. Du kan använda Storage Explorer för att hantera Azure-filresurser och andra lagringsresurser.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För den här snabbstarten krävs det att Storage Explorer är installerat. Hämta och installera det genom att gå till [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Du kan inte skapa nya resurser med Storage Explorer. can't use Storage Explorer to create new resources. Skapa lagringskontot i [Azure-portalen](https://portal.azure.com/) för den här demon. 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Ansluta Storage Explorer till Azure-resurser
När du först startar Storage Explorer visas fönstret **Microsoft Azure Storage Explorer – anslut**. I Storage Explorer finns flera olika sätt att ansluta till lagringskonton: 

- **Logga in med Azure-kontot**: Du kan logga in med autentiseringsuppgifterna för din organisation eller ditt Microsoft-konto. 
- **Anslut till ett specifikt lagringskontot med en anslutningssträng eller SAS-token**: En anslutningssträng är en speciell sträng som innehåller ett lagringskontonamn och en lagringskontonyckel/SAS-token. Med denna token får Storage Explorer direktåtkomst till lagringskontot (istället för att bara se alla lagringskonton på Azure-kontot). Mer information om anslutningssträngar finns i [Konfigurera anslutningssträngar för Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Ansluta till ett specifikt lagringskonto med ett lagringskontonamn och nyckel**: Anslut till Azure Storage genom att använda lagringskontonamnet och nyckeln till ditt lagringskonto.

I den här snabbstarten loggar vi för enkelhetens skull in med ditt Azure-konto. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto.

![En skärmbild av fönstret Microsoft Azure Storage Explorer – anslut](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Skapa en filresurs
Så här skapar du din första Azure-filresurs inom lagringskontot `storageacct<random number>`:

1. Expandera det lagringskonto du har skapat.
2. Högerklicka på **Filresurser** och välj **Skapa filresurs**.  
    ![En skärmbild av filresursmappen och snabbmenyn](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. För filresursen anger du *myshare* och trycker på Retur.

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck (men får inte inledas med bindestreck). Fullständig information om namngivning av fil resurser och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

När filresursen har skapats öppnas en flik för filresursen i den högra rutan. 

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB i [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Alternativt kan du arbeta med din Azure-filresurs genom att använda Azure Storage Explorer. Fördelen med att använda Azure Storage Explorer istället för att montera filresursen med SMB är att alla förfrågningar som görs med Azure Storage Explorer görs med fil-REST-API:n. Du kan använda fil-REST-API:n för att skapa, ändra och ta bort filer och kataloger på klienter som inte har SMB-åtkomst.

### <a name="create-a-directory"></a>Skapa en katalog
Om du lägger till en katalog får du en hierarkisk struktur där du kan hantera din filresurs. Du kan skapa flera nivåer i din katalog. Men du måste se till att de överordnade katalogerna finns måste du skapa underkataloger. För sökvägen myDirectory/mySubDirectory måste du t.ex. först skapa katalogen *myDirectory*. Därefter kan du skapa *mySubDirectory*. 

1. Klicka på knappen **Ny mapp** på toppmenyn på filresursens flik. Fönstret **Skapa ny katalog**.
    ![En skärmbild av knappen Ny mapp i sitt sammanhang](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. För katalognamnet anger du *myDirectory* och väljer **OK**. 

Katalogen *myDirectory* visas på fliken för filresursen *myshare*.

### <a name="upload-a-file"></a>Ladda upp en fil 
Du kan överföra en fil från din lokala dator till den nya katalogen i filresursen. Du kan ladda upp en hel mapp eller en enda fil.

1. I menyn högst upp väljer du **Ladda upp**. Den här åtgärden ger dig möjlighet att ladda upp en mapp eller fil.
2. Välj **Överför fil** och markera den fil på din lokala dator som ska överföras.
3. Skriv *myDirectory* i **Överför till en katalog** och klicka sedan på **Överför**. 

När du är klar visas filen i fönstret *myDirectory*.

### <a name="download-a-file"></a>Ladda ned en fil
Om du vill ladda ned en kopia av en fil från din filresurs högerklickar du på filen och väljer därefter **Ladda ned**. Ange var du vill placera filen på den lokala datorn och klicka sedan på **Spara**.

Hämtningsförloppet visas i rutan **Aktiviteter** längst ned i fönstret.

## <a name="clean-up-resources"></a>Rensa resurser
Du kan inte ta bort resurser med Storage Explorer. Om du vill rensa från den här snabbstarten kan du använda [Azure-portalen](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)