---
title: Välj en domän för ett Custom Vision projekt – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du väljer en domän för projektet i Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 0dbd6ea13069b72e6bca5c065af92568a5c7cdb8
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844928"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Välj en domän för ett Custom Vision-projekt

På fliken Inställningar i Custom Vision projektet kan du välja en domän för projektet. Välj den domän som är närmast ditt scenario. Om du använder Custom Vision via ett klient bibliotek eller REST API måste du ange ett domän-ID när du skapar projektet. Du kan hämta en lista över domän-ID: n med [Hämta domäner](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)eller använda tabellen nedan.

## <a name="image-classification"></a>Bildklassificering

|Domain|Syfte|
|---|---|
|__Allmänt__| Optimerad för ett brett utbud av bild klassificerings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen. IDENTITET `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Kost__|Optimerad för foton av rätter på samma sätt som du ser dem på en restaurang meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker använder du livsmedels domänen. IDENTITET `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Landmärken__|Optimerad för identifierbara landmärken, både naturliga och artificiella. Den här domänen fungerar bäst när landmärket är tydligt synligt i fotografiet. Den här domänen fungerar även om landmärken är något som är något som står framför det. IDENTITET `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Detaljhandeln__|Optimerad för avbildningar som finns i en shopping katalog eller shopping webbplats. Om du vill att hög precision ska klassificeras mellan dresses, Pants och skjortor använder du den här domänen. IDENTITET `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Komprimerade domäner__| Optimerad för begränsningar i real tids klassificering av gräns enheter.|

## <a name="object-detection"></a>Objektidentifiering

|Domain|Syfte|
|---|---|
|__Allmänt__| Optimerad för ett brett utbud av objekt identifierings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen. IDENTITET `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Logotyp__|Optimerad för att hitta varumärkes logo typer i bilder. IDENTITET `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produkter på hyllor__|Optimerad för att identifiera och klassificera produkter på hyllor. IDENTITET `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Komprimerade domäner__| Optimerad för begränsningar av objekt identifiering i real tid på gräns enheter.|

## <a name="compact-domains"></a>Komprimerade domäner

Modeller som genereras av komprimerade domäner kan exporteras för att köras lokalt. I Custom Vision 3,4-API för offentlig för hands version kan du hämta en lista över de exporter bara plattformarna för komprimerade domäner genom att anropa GetDomains-API: et.

Modell prestanda varierar beroende på vald domän. I tabellen nedan rapporterar vi modell storlek och härlednings tid för Intel Desktop-processor och NVidia GPU \[ 1 \] . De här talen omfattar inte förbehandling och postprocessing tid.

|Uppgift|Domain|ID|Modellstorlek|CPU-härlednings tid|GPU-härlednings tid|
|---|---|---|---|---|---|
|Klassificering|Allmän (kompakt)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|5 MB|13 MS|5 MS|
|Objektidentifiering|Allmän (kompakt)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Objektidentifiering|Allmän (kompakt) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>__Allmän (komprimera)__ domän för objekt identifiering kräver speciell postprocessing-logik. Mer information finns i ett exempel skript i det exporterade zip-paketet. Om du behöver en modell utan postprocessing Logic använder du __General (Compact) [S1]__.

>[!IMPORTANT]
>Det finns ingen garanti för att de exporterade modellerna ger exakt samma resultat som förutsägelse-API: et i molnet. Mindre skillnader i den plattform som körs eller för bearbetning av Förbearbetning kan orsaka större skillnader i modellens utdata. Information om för bearbetnings logiken finns i [det här dokumentet](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5 – 2690 CPU och NVIDIA Tesla M60
