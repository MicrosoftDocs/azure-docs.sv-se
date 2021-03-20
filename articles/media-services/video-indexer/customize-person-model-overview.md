---
title: Anpassa en person modell i Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad som är en person modell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047046"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Anpassa en person modell i Video Indexer

Video Indexer stöder kändis-igenkänning i dina videor. Kändis igenkännings funktionen täcker cirka 1 000 000 ansikten baserat på den begärda data källan, till exempel IMDB-, Wikipedia-och Top LinkedIn-påverkan. Ansikten som inte känns igen av Video Indexer har fortfarande identifierats, men får inget namn. Kunder kan skapa anpassade person modeller och aktivera Video Indexer för att identifiera ansikten som inte känns igen som standard. Kunder kan bygga dessa person modeller genom att para ihop en persons namn med bildfiler för personens ansikte.  

Om ditt konto har olika användnings fall kan du dra nytta av att kunna skapa flera person modeller per konto. Om innehållet i ditt konto till exempel är avsett att sorteras i olika kanaler, kanske du vill skapa en separat person modell för varje kanal. 

> [!NOTE]
> Varje persons modell har stöd för upp till 1 000 000 personer och varje konto har en gräns på 50 person modeller. 

När en modell har skapats kan du använda den genom att ange modell-ID: t för en speciell person modell vid överföring/indexering eller Omindexering av en video. Träna en ny ansikte för en video, uppdaterar den anpassade modell som videon var kopplad till. 

Om du inte behöver stöd för flera person modeller tilldelar du inte ett ID för person modeller till videon när du laddar upp/indexerar eller Omindexerar. I det här fallet använder Video Indexer standard modellen för personer i ditt konto. 

Du kan använda Video Indexer webbplats för att redigera ansikten som upptäcktes i en video och för att hantera flera anpassade person modeller i ditt konto, enligt beskrivningen i avsnittet [Anpassa en person modell med hjälp av en webbplats](customize-person-model-with-website.md) . Du kan också använda API: et enligt beskrivningen i [Anpassa en person modell med hjälp av API: er](customize-person-model-with-api.md).
