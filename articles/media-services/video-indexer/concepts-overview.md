---
title: Video Indexer koncept – Azure
titleSuffix: Azure Media Services Video Indexer
description: Den här artikeln ger en kort översikt över Azure Media Services Video Indexer terminologi och begrepp.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98634238"
---
# <a name="video-indexer-concepts"></a>Video Indexer begrepp

Den här artikeln ger en kort översikt över Azure Media Services Video Indexer terminologi och begrepp.

## <a name="audiovideocombined-insights"></a>Ljud/video/kombinerade insikter

När du laddar upp videor till Video Indexer, analyserar den både visuella objekt och ljud genom att köra olika AI-modeller. När Video Indexer analyserar videon extraheras de insikter som extraheras av AI-modellerna. Mer information finns i [Översikt](video-indexer-overview.md).

## <a name="confidence-scores"></a>Förtroende Poäng

Förtroende poängen innebär att du är säker på att det är en insikt. Det är ett tal mellan 0,0 och 1,0. Ju högre poäng, desto större besvars noggrannhet. Exempel: 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Innehålls moderator

Använd text-och visualiserings modeller för att hålla dina användare skyddade från olämpligt innehåll och kontrol lera att innehållet som du publicerar matchar din organisations värden. Du kan automatiskt blockera vissa videor eller Varna dina användare om innehållet. Mer information finns i [insikter: visualisering av visuella objekt och text innehåll](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Delar   

Block är avsedda att göra det enklare att gå igenom data. Blocken kan till exempel delas in baserat på när talare ändras eller det förekommer en lång paus.  

## <a name="project-and-editor"></a>Projekt och redigerare

På [video Indexer](https://www.videoindexer.ai/) webbplats kan du använda videons djup insikter för att: hitta rätt medie innehåll, leta upp de delar som du är intresse rad av och Använd resultatet för att skapa ett helt nytt projekt. När projektet har skapats kan projektet renderas och hämtas från Video Indexer och användas i dina egna redigerings program eller i efterföljande arbets flöden.

Vissa scenarier där den här funktionen kan vara användbar är: 

* Skapa film markeringar för släp vagnar.
* Använda gamla klipp av videor i nyhets brev.
* Skapa kortare innehåll för sociala medier.

Mer information finns i [använda Editor för att skapa projekt](use-editor-create-project.md).

## <a name="keyframes"></a>Nyckel rutor

Video Indexer väljer den eller de ramar som bäst representerar varje bild. Nyckel rutor är de representativa bild rutor som valts från hela videon baserat på de egenskaper som är markerade (till exempel kontrast och stabilhet). Mer information finns i [scener, bilder och nyckel bild rutor](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervall jämfört med justerat tidsintervall   

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spelnings listan. Eftersom du kan skapa en spelnings lista från olika rader med olika videoklipp kan du ta en timmes video och använda bara 1 rad från den, till exempel 10:00-10:15. I så fall har du en spelnings lista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15. 

## <a name="widgets"></a>Widgets

Video Indexer stöder inbäddning av widgetar i dina appar. Mer information finns i [bädda in video Indexer widgetar i dina appar](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Sammanfattande insikter  

Sammanfattade insikter innehåller en sammanställd vy av data: ansikten, ämnen, känslor. I stället för att gå över varje tusentals tidsintervall och kontrol lera vilka ansikten som finns, innehåller de sammanfattande insikterna alla ansikten och för var och en, de tidsintervall som visas i och% av tiden som visas.  

## <a name="next-steps"></a>Nästa steg

- [översikt](video-indexer-overview.md)
- [Insikter](video-indexer-output-json-v2.md)
