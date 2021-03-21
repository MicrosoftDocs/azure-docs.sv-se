---
title: Animerat typsnitts identifiering med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder animerad identifiering av bokstäver med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854758"
---
# <a name="animated-character-detection-preview"></a>Identifiering av animerade karaktärer (förhandsversion)

Azure Media Services Video Indexer stöder identifiering, gruppering och igenkänning av tecken i animerat innehåll via integrering med [Cognitive Services anpassad vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Den här funktionen är tillgänglig både via portalen och via API: et.

När du har laddat upp en animerad video med en speciell modell för animering Video Indexer extraherar nyckel rutor, identifierar animerade tecken i dessa ramar, grupper liknande tecken och väljer det bästa exemplet. Sedan skickar den grupperade tecken till Custom Vision som identifierar tecken baserade på de modeller som den tränade på. 

Innan du börjar träna din modell identifieras tecknen namelessly. När du lägger till namn och tränar modellen identifierar Video Indexer tecknen och namnger dem därefter.

## <a name="flow-diagram"></a>Flödesdiagram

Följande diagram visar flödet av den animerade processen för att identifiera animering.

![Flödesdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konton

Olika funktions uppsättningar är tillgängliga beroende på vilken typ av Video Indexer konto som används. Information om hur du ansluter ditt konto till Azure finns i [skapa ett video Indexer-konto som är anslutet till Azure](connect-to-azure.md).

* Utvärderings konto: Video Indexer använder ett internt Custom Vision konto för att skapa en modell och ansluta den till ditt Video Indexer-konto. 
* Betalt konto: du ansluter ditt Custom Vision-konto till ditt Video Indexer-konto (om du inte redan har ett konto måste du först skapa ett konto).

### <a name="trial-vs-paid"></a>Utvärdering kontra betald

|Funktioner|Utvärdering|Betald|
|---|---|---|
|Custom Vision konto|Hanteras i bakgrunden med Video Indexer. |Ditt Custom Vision-konto är anslutet till Video Indexer.|
|Antal modeller för animering|En|Upp till 100 modeller per konto (Custom Vision begränsning).|
|Träna modellen|Video Indexer tågen modellen för nya tecken ytterligare exempel på befintliga tecken.|Konto ägaren tågen modellen när de är redo att göra ändringar.|
|Avancerade alternativ i Custom Vision|Ingen åtkomst till Custom Vision-portalen.|Du kan justera modellerna själv i Custom Vision-portalen.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Använda animerat teckensnitts identifiering med Portal och API

Mer information finns i [använda animerat teckensnitts identifiering med Portal och API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Begränsningar

* För närvarande stöds inte funktionen "animation Identification" i East-Asia region.
* Tecken som verkar vara små eller långt i videon kanske inte identifieras korrekt om videons kvalitet är låg.
* Rekommendationen är att använda en modell per uppsättning animerade tecken (till exempel per animerad serie).

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)