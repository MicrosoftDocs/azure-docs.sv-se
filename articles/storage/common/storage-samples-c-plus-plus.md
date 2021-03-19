---
title: Azure Storage exempel med C++ | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Identifiera exempel på att komma igång med blobbar, köer, tabeller och filer med hjälp av C++-bibliotek för lagrings klienter.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 158f908dfd52a3365d19f65eb00faf1787893af5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92379054"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>Azure Storage exempel som använder V12 C++-klient bibliotek

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

> [!NOTE]
> De här exemplen använder det senaste Azure Storage C++ V12-biblioteket.

## <a name="blob-samples"></a>BLOB-exempel

:::row:::
   :::column:::
        [Autentisera med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [Skapa en blobcontainer](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Hämta en BLOB-klient](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Ladda upp en blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Ange metadata för en BLOB](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [Hämta BLOB-egenskaper](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [Ladda ned en blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 exempel

:::row:::
   :::column:::
        [Skapa en tjänst klient med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [Skapa en fil system klient med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Skapa ett filsystem](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Skapa en katalog](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Skapa en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [Lägga till data i en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Rensa fildata](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [Läs en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Lista alla fil system](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [Ta bort fil system](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files exempel

:::row:::
    :::column:::
        [Skapa en resurs klient med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [Skapa en filresurs](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Hämta en fil klient](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [Ladda upp en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Ange metadata för en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [Hämta filegenskaper](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [Ladda ned en fil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Exempel bibliotek för Azure-kod

Om du vill visa fullständiga C++-exempel bibliotek går du till:

* [Kod exempel för Azure-Blob](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
* [Azure Data Lake kod exempel](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/sample)
* [Azure Files kod exempel](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/sample)

Du kan bläddra och klona GitHub-lagringsplatsen för varje bibliotek.

## <a name="getting-started-guides"></a>Kom igång-guider

Kolla in följande guider om du behöver instruktioner om hur du installerar och kommer igång med Azure Storage klient bibliotek.

* [Snabb start: Azure Blob Storage-bibliotek V12 – C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>Nästa steg

Information om exempel för andra språk:

* .NET: [Azure Storage exempel med .net](storage-samples-dotnet.md)
* Java: [Azure Storage exempel med Java](storage-samples-java.md)
* Python: [Azure Storage exempel med python](storage-samples-python.md)
* Java Script/Node.js: [Azure Storage exempel med hjälp av Java Script](storage-samples-javascript.md)
* Alla andra språk: [Azure Storage exempel](storage-samples.md)
