---
title: Kom igång med Azure Data Lake Storage Gen1 – PowerShell | Microsoft Docs
description: Använd Azure PowerShell för att skapa ett Azure Data Lake Storage Gen1 konto och utföra grundläggande åtgärder.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103738"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Kom igång med Azure Data Lake Storage Gen1 med Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig hur du använder Azure PowerShell för att skapa ett Azure Data Lake Storage Gen1 konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Storage Gen1 finns i [Översikt över data Lake Storage gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="authentication"></a>Autentisering

Den här artikeln använder en enklare metod för autentisering med Data Lake Storage Gen1 där du uppmanas att ange dina autentiseringsuppgifter för Azure-kontot. Åtkomst nivån för Data Lake Storage Gen1 konto och fil system styrs sedan av åtkomst nivån för den inloggade användaren. Det finns dock andra metoder att autentisera med Data Lake Storage Gen1, som är autentisering med slutanvändare eller tjänst-till-tjänst-autentisering. Instruktioner och mer information om hur du autentiserar finns i [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md) eller [Tjänst-till-tjänst-autentisering](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

1. Öppna ett nytt Windows PowerShell-fönster på skrivbordet. Ange följande kodfragment för att logga in på ditt Azure-konto, ange prenumerationen och registrera Data Lake Storage Gen1-providern. När du uppmanas att logga in kontrollerar du att du loggar in som en av prenumerations administratörerna/ägaren:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Ett Data Lake Storage Gen1 konto är associerat med en Azure-resurs grupp. Börja med att skapa en resurs grupp.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Skapa en Azure-resurs grupp](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Skapa en Azure-resursgrupp")

1. Skapa ett Data Lake Storage Gen1-konto. Namnet du anger får bara innehålla gemener och siffror.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Skapa ett Data Lake Storage Gen1-konto](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Skapa ett Data Lake Storage Gen1-konto")

1. Kontrollera att kontot har skapats.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    Utdata för cmdleten ska vara **Sant**.

## <a name="create-directory-structures"></a>Skapa katalog strukturer

Du kan skapa kataloger under ditt Data Lake Storage Gen1 konto för att hantera och lagra data.

1. Ange en rotkatalog.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Skapa en ny katalog som kallas **mynewdirectory** under den angivna roten.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Kontrollera att den nya katalogen har skapats.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Utdata bör visas så som på följande skärmbild:

    ![Verifiera katalog](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verifiera katalog")

## <a name="upload-data"></a>Ladda upp data

Du kan ladda upp data till Data Lake Storage Gen1 direkt på rotnivå eller till en katalog som du har skapat i kontot. Kodavsnitten i det här avsnittet visar hur du kan ladda upp exempeldata till katalogen (**mynewdirectory**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den i en lokal katalog på datorn, till exempel C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Byta namn på, ladda ned och ta bort data

Om du vill byta namn på en fil, använder du följande kommando:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Om du vill hämta en fil, använder du följande kommando:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Om du vill ta bort en fil, använder du följande kommando:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

När du uppmanas, trycker du på **Y** för att ta bort objektet. Om du har fler än en fil att ta bort, kan du ange alla sökvägar avgränsade med kommatecken.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Ta bort ditt konto

Använd följande kommando för att ta bort ditt Data Lake Storage Gen1-konto.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg

* [Vägledning för prestanda justering för att använda PowerShell med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Använd Azure Data Lake Storage Gen1 för Big data-krav](data-lake-store-data-scenarios.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)