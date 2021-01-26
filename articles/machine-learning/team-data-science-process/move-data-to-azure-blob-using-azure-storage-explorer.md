---
title: Flytta Blob Storage-data med Azure Storage Explorer-Team data science process
description: Lär dig hur du använder Azure Storage Explorer för att ladda upp och ladda ned data från Azure Blob Storage.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788917"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytta data till och från Azure Blob Storage med Azure Storage Explorer
Azure Storage Explorer är ett kostnads fritt verktyg från Microsoft som gör att du kan arbeta med Azure Storage data på Windows, macOS och Linux. I det här avsnittet beskrivs hur du använder det för att ladda upp och ladda ned data från Azure Blob Storage. Verktyget kan laddas ned från [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder en virtuell dator som har kon figurer ATS med skripten som tillhandahålls av [data science Virtual Machines i Azure](../data-science-virtual-machine/overview.md), är Azure Storage Explorer redan installerad på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md) och [Azure Blob service](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagrings konto och motsvarande lagrings nyckel för det kontot. Innan du laddar upp/laddar ned data måste du känna till Azure Storage kontots namn och konto nyckel. 

* Information om hur du konfigurerar en Azure-prenumeration finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagrings konto och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-account-create.md). Anteckna åtkomst nyckeln för ditt lagrings konto eftersom du behöver den här nyckeln för att ansluta till kontot med Azure Storage Explorer-verktyget.
* Verktyget Azure Storage Explorer kan laddas ned från [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Acceptera standardinställningarna under installationen.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Använda Azure Storage Explorer
Följande steg beskriver hur du laddar upp/laddar ned data med Azure Storage Explorer. 

1. Starta Microsoft Azure Storage Explorer.
2. För att öppna **inloggningen till ditt konto...** -guide, Välj ikonen för **Azure konto inställningar** och **Lägg sedan till ett konto** och ange autentiseringsuppgifter. 
![Lägg till ett Azure Storage konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Öppna guiden Anslut till **Azure Storage** genom att välja ikonen **Anslut till Azure Storage** . ![Klicka på Anslut till Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Ange åtkomst nyckeln från Azure Storage-kontot i guiden **Anslut till Azure Storage** och klicka sedan på **Nästa**. ![Ange åtkomst nyckel från Azure Storage konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Ange lagrings kontots namn i rutan **konto namn** och välj sedan **Nästa**. ![Bifoga extern lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Det tillagda lagrings kontot bör nu visas. Om du vill skapa en BLOB-behållare i ett lagrings konto högerklickar du på noden **BLOB-behållare** i kontot, väljer **skapa BLOB-behållare** och anger ett namn.
7. Om du vill överföra data till en behållare väljer du mål behållaren och klickar på knappen **överför** .
![Lagringskonton](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicka på **..** . till höger om rutan **filer** , Välj en eller flera filer som ska laddas upp från fil systemet och klicka på **överför** för att påbörja överföring av filerna. ![ Ladda upp filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Hämta data genom att välja bloben i motsvarande behållare för att ladda ned och klicka på **Hämta**. ![Ladda ned filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
