---
title: Vad är rums analys?
titleSuffix: Azure Cognitive Services
description: Det här dokumentet beskriver de grundläggande begreppen och funktionerna i en Visuellt innehåll rums analys behållare.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284550"
---
# <a name="what-is-spatial-analysis"></a>Vad är rums analys?

Tjänsten för spatial analys hjälper organisationer att maximera värdet av deras fysiska utrymmen genom att förstå personernas rörelser och närvaro inom ett angivet ställe. Det gör att du kan mata in video från CCTV eller övervaknings kameror, köra AI-åtgärder för att extrahera insikter från video strömmarna och generera händelser som ska användas av andra system. Med indata från en kamera ström kan en AI-åtgärd göra saker som att räkna antalet personer som anger ett utrymme eller mäter efterlevnad med rikt linjer för ansikts mask och sociala distancing.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Vad läget gör

Kärn funktionerna för rums analys är allt byggt på en pipeline som inleder video, identifierar personer i videon, spårar människor när de rör sig över tid och genererar händelser när människor interagerar med intresse områden.

## <a name="spatial-analysis-features"></a>Funktioner för rums analys

| Funktion | Definition |
|------|------------|
| **Person identifiering** | Den här komponenten besvarar frågan "var är personerna i den här bilden?" Den söker efter personer i en bild och skickar en avgränsnings ruta som anger var personen finns till personer som spårar komponenten. |
| **Personer som spårar** | Den här komponenten ansluter person identifieringar över tid då människor rör sig framför en kamera. Den använder temporal logik om hur användare vanligt vis flyttar och grundläggande information om användarnas övergripande utseende. Det spårar inte personer över flera kameror. Om en person lämnar fältet i vyn längre än cirka en minut och sedan anger om kameravy, kommer systemet att se detta som en ny person. Personer som spårar kan inte unikt identifiera individer i kameror. Den använder inte ansikts igenkänning eller gång spårning. |
| **Identifiering av ansikts mask** | Den här komponenten identifierar platsen för en persons ansikte i kamerans visnings område och identifierar förekomsten av en ansikts mask. AI-åtgärden genomsöker bilder från video. När en ansikte identifieras ger tjänsten en avgränsnings ruta runt om. Genom att använda funktioner för objekt identifiering identifieras förekomsten av ansikts masker i markerings rutan. Identifiering av ansikts mask innebär inte att skilja en ansikte från en annan ansikte, förutsäga eller klassificera ansikts attribut eller att utföra ansikts igenkänning. |
| **Intresse region** | Det här är en användardefinierad zon eller en användardefinierad linje i indata-videons ram. När en person interagerar med den här regionen på videon genererar systemet en händelse. Till exempel definieras en rad i videon för PersonCrossingLine-åtgärden. När en person korsar raden skapas en händelse. |
| **Händelse** | En händelse är primär utdata från rums analys. Varje åtgärd genererar en viss händelse antingen med jämna mellanrum (till exempel en gång per minut) eller varje gång en viss utlösare sker. Händelsen innehåller information om vad som hände i indata-videon men som inte innehåller några bilder eller video. PeopleCount-åtgärden kan till exempel generera en händelse som innehåller det uppdaterade antalet varje gång antalet personer ändras (utlösare) eller en gång i minuten (med jämna mellanrum). |

## <a name="get-started"></a>Kom igång

### <a name="public-preview-gating"></a>Hantera för offentlig för hands version

För att säkerställa rums analys används för scenarier som den har utformats för, vi gör den här tekniken tillgänglig för kunder genom en program process. För att få åtkomst till avstånds analys måste du börja med att fylla i vårt insugnings formulär online. [Börja ditt program här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Till gång till den offentliga för hands versionen av spatial analys omfattas av Microsofts enda val som baseras på våra berättiganderegler, först konsumentsajter process och tillgänglighet för att stödja ett begränsat antal kunder under den här för hands versionen. I offentlig för hands version letar vi efter kunder som har en betydande relation med Microsoft, är intresserade av att arbeta med oss i de rekommenderade användnings fallen och ytterligare scenarier som håller våra ansvariga AI-åtaganden.

### <a name="follow-a-quickstart"></a>Följ en snabb start

När du har beviljat åtkomst till rums analys följer du [snabb](spatial-analysis-container.md) starten för att konfigurera behållaren och börja analysera video.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Ansvarig användning av teknik för rums analys

Information om hur du använder ett ansvarsfullt sätt för rums analys finns i [Oh-anteckningen](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Microsofts Oh-kommentarer är avsedda att hjälpa dig att förstå hur vår AI-teknik fungerar, vilka alternativ som system ägare kan göra för att påverka systemets prestanda och hur viktigt det är att tänka på hela systemet, inklusive teknik, människor och miljö.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: rums analys behållare](spatial-analysis-container.md)' ' ' ' ' ' '