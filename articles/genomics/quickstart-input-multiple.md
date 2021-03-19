---
title: Skicka ett arbets flöde med flera indata
titleSuffix: Microsoft Genomics
description: Den här artikeln visar hur du skickar ett arbets flöde till Microsoft Genomics tjänsten om indatafilen är flera FASTQ-eller BAM-filer från samma exempel.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b426015906a8e17674123c0c3ad2fccb9c43798f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "72248574"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Skicka ett arbetsflöde med flera indata från samma prov

Den här artikeln visar hur du skickar ett arbets flöde till Microsoft Genomics tjänsten om indatafilen är flera FASTQ-eller BAM-filer som **kommer från samma exempel**. Om du exempelvis körde **samma prov** i flera banor i sekvensen, kunde resulterande utdata bli ett par med FASTQ-filer för varje bana. I stället för att sammanfoga FASTQ-filerna innan inpassning och identifiering av varianter, kan du direkt skicka alla dessa indata till `msgen`-klienten. Utdata från `msgen`-klienten kan vara en **enkel uppsättning** av filer, inklusive en .bam-, .bai- eller .vcf-fil. 

Kom ihåg att du **inte** kan skicka in blandade FASTQ- och BAM-filer. Dessutom kan du **inte** skicka flera FASTQ- eller BAM-filer från flera personer. 

I den här artikeln förutsätts det att du redan har installerat och kört `msgen`-klienten och att du vet hur du använder Azure Storage. Om du har skickat ett arbets flöde med hjälp av de tillhandahållna exempel data, är du redo att fortsätta med den här artikeln. 


## <a name="multiple-bam-files"></a>Flera BAM-filer

### <a name="upload-your-input-files-to-azure-storage"></a>Ladda upp dina indatafiler till Azure Storage
Vi antar att du har flera BAM-filer som indata, *reads.bam*, *additional_reads.bam* och *yet_more_reads.bam*, och att du har laddat upp dem till ditt lagringskonto *myaccount* i Azure. Du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 

Du kan skicka flera BAM-filer genom att skicka deras namn till argumentet --input-blob-name-1. Observera att alla filer måste komma från samma prov, men deras inbördes ordning är inte viktig. I följande avsnitt finns exempel på inskickade filer från kommandoraden i Windows, i Unix och med en konfigurationsfil. Radbrytningar har lagts till för tydlighetens skull:


För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
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
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Flera, parade FASTQ-filer

### <a name="upload-your-input-files-to-azure-storage"></a>Ladda upp dina indatafiler till Azure Storage
Vi antar att du har flera parade FASTQ-filer som indata, *reads_1.fq.gz* och *reads_2.fq.gz*, *additional_reads_1.fq.gz* och *additional_ reads_2.fq.GZ* och *yet_more_reads_1.fq.gz* och *yet_more_reads_2.fq.gz*. Du har laddat upp dem till ditt lagringskonto *myaccount* i Azure och du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 

Parade FASTQ-filer måste komma från samma prov, men de måste också behandlas tillsammans.  Ordningen på filnamnen är viktig när de skickas som argument till--input-blob-name-1 och --input-blob-name-2. 

I följande avsnitt finns exempel på inskickade filer från kommandoraden i Windows, i Unix och med en konfigurationsfil. Radbrytningar har lagts till för tydlighetens skull:


För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Om du föredrar att använda en konfigurationsfil skulle den innehålla följande:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp flera BAM-filer eller parade FASTQ-filer till Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Ytterligare information om att skicka arbetsflöden och andra kommandon som du kan använda med tjänsten Microsoft Genomics finns i [Vanliga frågor och svar](frequently-asked-questions-genomics.md). 