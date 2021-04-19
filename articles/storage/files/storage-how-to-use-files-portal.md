---
title: Snabbstart för hantering av Azure-filresurser med Azure-portalen
description: Se hur du skapar och hanterar Azure-filresurser i Azure Portal. Skapa ett lagringskonto, skapa en Azure-filresurs och använd din Azure-filresurs.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717880"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Snabbstart: Skapa och hantera Azure-filresurser med Azure-portalen 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows, Linux och macOS. Den här guiden vägleder dig igenom grunderna i att arbeta med Azure-filresurser med [Azure Portal](https://portal.azure.com/).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Skapa en Azure-filresurs:

1. Välj lagringskonto från instrumentpanelen.
1. På sidan för lagringskontot i avsnittet **Tjänster** väljer du **Filer**.
    
    ![En skärmbild av lagringsavsnittet för lagringskontot. välj filresurser.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. Klicka på Filresurs på menyn längst **upp på** sidan **Filtjänst.** Sidan **Ny filresurs** rullas ned.
1. I **Namn** skriver *du myshare*, anger en quoate och **lämnar Transaction optimized (Transaktionsoptimerad)** markerad för **Tiers (Nivåer).**
1. Välj **Skapa** för att skapa Azure-filresursen.

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har tre metoder för att arbeta med filer och mappar i din Azure-filresurs [Server Message Block: SMB-protokollet (branschstandard),](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)NFS-protokollet (network file system) (förhandsversion) och [fil-REST-protokollet](/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Använda en Azure-filresurs via Azure-portalen
Alla begäranden som görs via Azure Portal görs med REST API-filen så att du kan skapa, ändra och ta bort filer och kataloger på klienter utan SMB-åtkomst. Det är möjligt att arbeta direkt med fil-REST-protokollet (dvs. att själv skapa REST HTTP-anrop för hand), men det vanligaste sättet (utöver att använda Azure Portal) att använda fil-REST-protokollet är att använda [Azure PowerShell-modulen](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)eller ett Azure Storage SDK. Alla ger en bra omser runt fil-REST-protokollet i val av skript-/programmeringsspråk. 

Vi förväntar oss att de flesta användare av Azure Files kommer att vilja arbeta med sin Azure-filresurs via SMB-protokollet, eftersom det gör att de kan använda befintliga program och verktyg som de förväntar sig att kunna använda, men det finns flera orsaker till varför det är fördelaktigt att använda fil-REST API i stället för SMB, till exempel:

- Du måste göra en snabb ändring av Azure-filresursen i farten, till exempel via en bärbar dator utan SMB-åtkomst, en surfplatta eller en mobil enhet.
- Du måste köra ett skript eller program från en klient som inte kan montera en SMB-resurs, till exempel lokala klienter, som inte har port 445 avblockerad.
- Du utnyttjar serverlösa resurser såsom [Azure Functions](../../azure-functions/functions-overview.md). 

I följande exempel visas hur du använder Azure-portalen till att ändra din Azure-filresurs med fil-REST-protokollet. 

Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB i [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också arbeta med din Azure-filresurs med Azure Portal. 

#### <a name="create-a-directory"></a>Skapa en katalog
Skapa en ny katalog med namnet *myDirectory* vid roten på Azure-filresursen:

1. På sidan **Filtjänst** väljer du filresursen **myshare**. Då öppnas sidan för filresursen.
1. På menyn överst på sidan väljer du **+ Lägg till katalog**. Sidan **Ny katalog** rullas ned.
1. Skriv *myDirectory* och klicka sedan på **OK**.

#### <a name="upload-a-file"></a>Ladda upp en fil 
För att ladda upp en fil, måste du först skapa eller välja en fil som ska laddas upp. Du kan göra detta på det sätt du känner är lämpligt. När du har valt den fil som du vill ladda upp:

1. Klicka på katalogen **myDirectory**. Panelen **myDirectory** öppnas.
1. Klicka på **Ladda upp** på den översta menyn. Panelen **Ladda upp filer** öppnas.  
    
    ![En skärmbild av panelen ladda upp filer](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Klicka på mappikonen för att öppna ett fönster för att bläddra bland dina lokala filer. 
1. Välj en fil och klicka sedan på **Öppna**. 
1. På sidan **Ladda upp filer**, kontrollera filnamnet och klicka sedan på **Ladda upp**.
1. När du är klar ska filen visas i listan på sidan **myDirectory**.

#### <a name="download-a-file"></a>Ladda ned en fil
Du kan ladda ned en kopia av filen du överförde genom att högerklicka på filen. När du klickar på knappen ladda ner, beror den exakta upplevelsen på det operativsystem och den webbläsare du använder.

## <a name="clean-up-resources"></a>Rensa resurser
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)