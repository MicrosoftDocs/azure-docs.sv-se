---
title: Skicka ett arbets flöde med hjälp av BAM-fildata
titleSuffix: Microsoft Genomics
description: Den här artikeln visar hur du skickar ett arbets flöde till Microsoft Genomics tjänsten om indatafilen är en enskild BAM-fil.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 5145aa0ffdc4095f178a214f63433e5bcece83b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "72249168"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Skicka ett arbetsflöde med en BAM-fil som indata

Den här artikeln visar hur du skickar ett arbets flöde till Microsoft Genomics tjänsten om indatafilen är en enskild BAM-fil. I det här avsnittet förutsätts det att du redan har installerat och kört `msgen`-klienten och att du vet hur du använder Azure Storage. Om du har skickat ett arbets flöde med hjälp av de tillhandahållna exempel data, är du redo att fortsätta med den här artikeln. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Förberedelse: Överför BAM-filen till Azure Storage
Vi antar att du har en enskild BAM-fil, *reads.bam*, och du har laddat upp den till ditt lagringskonto *myaccount* i Azure som **https://<span></span>myaccount.BLOB.Core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>**. Du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 


Här är den minsta möjliga uppsättningen argument som du måste ange för `msgen`-klienten. Radbrytningar har lagts till för tydlighetens skull:

För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


För Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Om du föredrar att använda en konfigurationsfil skulle den innehålla följande:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp en BAM-fil i Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Ytterligare information om att skicka arbetsflöden och andra kommandon som du kan använda med tjänsten Microsoft Genomics finns i [vanliga frågor och svar](frequently-asked-questions-genomics.md). 
