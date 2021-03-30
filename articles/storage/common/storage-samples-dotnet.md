---
title: Azure Storage exempel med hjälp av .NET | Microsoft Docs
description: Visa, ladda ned och kör exempel kod och program för Azure Storage. Identifiera exempel på att komma igång med blobbar, köer, tabeller och filer med hjälp av klient biblioteken för .NET-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 760fedd94ef682b183eede1215186de973e9a216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359789"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Azure Storage exempel med V12 .NET-klient bibliotek

Följande tabell innehåller en översikt över våra exempel lager och scenarier som beskrivs i varje exempel. Klicka på länkarna om du vill visa motsvarande exempel kod i GitHub.

> [!NOTE]
> De här exemplen använder det senaste Azure Storage .NET V12-biblioteket. För äldre V11-kod, se [Azure Blob Storage-exempel för .net](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) i GitHub-lagringsplatsen.

## <a name="blob-samples"></a>BLOB-exempel

### <a name="authentication"></a>Autentisering

:::row:::
   :::column span="":::
      [Autentisera med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Autentisera med autentiseringsuppgifter för delad nyckel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Autentisera med Azure-identitet](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Autentisera med hjälp av en Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Få anonym åtkomst till en offentlig BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Batchbearbetning

:::row:::
   :::column span="":::
      [Ta bort flera blobbar i en begäran](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Ange flera BLOB-åtkomst nivåer i en begäran](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Detaljerad kontroll i en batch-begäran](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Fånga fel från en misslyckad under åtgärd](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Ladda upp en fil till en BLOB](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Ladda ned en blob till en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ladda ned en avbildning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Lista alla blobbar i en behållare](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Felsökning
:::row:::
   :::column span="2":::
      [Utlösa ett återställnings Bart fel med hjälp av en behållar klient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 exempel

### <a name="authentication"></a>Autentisering

:::row:::
   :::column span="":::
      [Få anonym åtkomst till en offentlig fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Autentisera med autentiseringsuppgifter för delad nyckel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Autentisera med hjälp av signaturen för delad åtkomst (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Autentisera med hjälp av en Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Filsystem
:::row:::
   :::column span="":::
      [Skapa en fil med hjälp av en fil system klient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Hämta egenskaper för en fil och en katalog](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Byta namn på en fil och en katalog](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Katalog

:::row:::
   :::column span="":::
      [Skapa en katalog](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Skapa en fil med en katalog klient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Lista kataloger](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Bläddra bland filer och kataloger](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Fil
:::row:::
   :::column span="":::
      [Ladda upp en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Ladda upp genom att lägga till i en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ladda ned en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ange och hämta en fil åtkomst kontrol lista](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Ange och hämta behörigheter för en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Felsökning

:::row:::
   :::column span="2":::
      [Utlös ett återställnings Bart fel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files exempel

### <a name="authentication"></a>Autentisering

:::row:::
   :::column span="":::
      [Autentisera med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Autentisera med autentiseringsuppgifter för delad nyckel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Autentisera med hjälp av signaturen för delad åtkomst (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Filresurser

:::row:::
   :::column span="":::
      [Skapa en resurs och ladda upp en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Ladda ned en fil](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Bläddra bland filer och kataloger](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Felsökning

:::row:::
   :::column span="2":::
      [Utlösa ett återställnings Bart fel med en resurs klient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Köa exempel

### <a name="authentication"></a>Autentisering

:::row:::
   :::column span="":::
      [Autentisera med hjälp av Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Autentisera med hjälp av en anslutnings sträng](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Autentisera med autentiseringsuppgifter för delad nyckel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Autentisera med hjälp av signaturen för delad åtkomst (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Autentisera med hjälp av en Active Directory-token](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kö

:::row:::
   :::column span="2":::
      [Skapa en kö och Lägg till ett meddelande](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Meddelande

:::row:::
   :::column span="":::
      [Ta emot och bearbeta meddelanden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Titta på meddelanden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Ta emot meddelanden och tids gräns för uppdaterings synlighet](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Felsökning 
:::row:::
   :::column span="2":::
      [Utlösa ett återställnings Bart fel med en Queue-klient](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tabell exempel (V11)

:::row:::
   :::column span="":::
      [Skapa tabell](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Ta bort entitet/tabell](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Infoga/sammanfoga/Ersätt entitet](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Fråga entiteter](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Frågetabeller](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tabell-ACL/egenskaper](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Uppdatera entitet](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Exempel bibliotek för Azure-kod

Om du vill visa fullständiga .NET-exempel bibliotek går du till:

* [Kod exempel för Azure-Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Azure Data Lake kod exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Azure Files kod exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Kod exempel för Azure-kö](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Du kan bläddra och klona GitHub-lagringsplatsen för varje bibliotek.

## <a name="getting-started-guides"></a>Kom igång-guider

Kolla in följande guider om du behöver instruktioner om hur du installerar och kommer igång med Azure Storage klient bibliotek.

* [Komma igång med Azure Blob service i .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Komma igång med Azure Queue Service i .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Komma igång med Azure Table service i .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Komma igång med Azure File Service i .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

Information om exempel för andra språk:

* Java: [Azure Storage exempel med Java](storage-samples-java.md)
* Python: [Azure Storage exempel med python](storage-samples-python.md)
* Java Script/Node.js: [Azure Storage exempel med hjälp av Java Script](storage-samples-javascript.md)
* C++: [Azure Storage exempel med hjälp av C++](storage-samples-c-plus-plus.md)
* Alla andra språk: [Azure Storage exempel](storage-samples.md)
