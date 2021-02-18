---
title: Översikt över rums analys
titleSuffix: Azure Cognitive Services
description: Det här dokumentet beskriver de grundläggande begreppen och funktionerna i en Visuellt innehåll rums analys behållare.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575369"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Översikt över Visuellt innehåll rums analys

Visuellt innehåll rums analys är en ny funktion i Azure Cognitive Services Visuellt innehåll som hjälper organisationer att maximera värdet på deras fysiska utrymmen genom att förstå personernas rörelser och närvaro inom ett angivet ställe. Det gör att du kan mata in video från CCTV eller övervaknings kameror, köra AI-åtgärder för att extrahera insikter från video strömmarna och generera händelser som ska användas av andra system. Med indata från en kamera ström kan en AI-åtgärd göra saker som att räkna antalet personer som anger ett utrymme eller mäter efterlevnad med rikt linjer för ansikts mask och sociala distancing.

## <a name="the-basics-of-spatial-analysis"></a>Grunderna för rums analys

Idag är kärn åtgärderna för rums analys allt byggt på en pipeline som inleder video, identifierar personer i videon, spårar människor när de rör sig över tid och genererar händelser när människor interagerar med intresse områden.

## <a name="spatial-analysis-terms"></a>Villkor för rums analys

| Period | Definition |
|------|------------|
| Person identifiering | Den här komponenten besvarar frågan "var är personerna i den här bilden"? Den hittar människor i en bild och skickar en avgränsnings ruta som visar var personen är ansvarig för personer som spårar komponenten. |
| Personer som spårar | Den här komponenten ansluter person identifieringar över tid då de flyttar runt en kamera. Den använder temporal logik om hur användare vanligt vis flyttar och grundläggande information om det övergripande utseendet på dem. Det spårar inte personer över flera kameror. Om en person finns i fältet från en kamera i mer än cirka en minut och sedan anger om kameravy, kommer systemet att se detta som en ny person. Personer som spårar kan inte unikt identifiera individer i kameror. Den använder inte ansikts igenkänning eller gång spårning. |
| Identifiering av ansikts mask | Den här komponenten identifierar platsen för en persons ansikte i kamerans visnings område och identifierar förekomsten av en ansikts mask. För att göra det genomsöker AI-åtgärden bilder från video. När en ansikte identifieras ger tjänsten en avgränsnings ruta runt om. Genom att använda funktioner för objekt identifiering identifieras förekomsten av ansikts masker i markerings rutan. Identifiering av ansikts mask innebär inte att skilja en ansikte från en annan ansikte, förutsäga eller klassificera ansikts attribut eller att utföra ansikts igenkänning. |
| Intresse region | Detta är en zon eller linje som definierats i Indataporten som en del av konfigurationen. När en person interagerar med regionen i videon genererar systemet en händelse. Till exempel definieras en rad i videon för PersonCrossingLine-åtgärden. När en person korsar raden skapas en händelse. |
| Händelse | En händelse är primär utdata från rums analys. Varje åtgärd genererar en speciell händelse antingen med jämna mellanrum (t. ex. en gång per minut) eller när en viss utlösare sker. Händelsen innehåller information om vad som hände i indata-videon men som inte innehåller några bilder eller video. PeopleCount-åtgärden kan till exempel generera en händelse som innehåller det uppdaterade antalet varje gång antalet personer ändras (utlösare) eller en gång i minuten (med jämna mellanrum). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Ansvarig användning av teknik för rums analys

Information om hur du använder ett ansvarsfullt sätt för rums analys finns i [Oh-anteckningen](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Microsofts Oh-kommentarer är avsedda att hjälpa dig att förstå hur vår AI-teknik fungerar, vilka alternativ som system ägare kan göra för att påverka systemets prestanda och hur viktigt det är att tänka på hela systemet, inklusive teknik, människor och miljö.

## <a name="spatial-analysis-gating-for-public-preview"></a>Hantera för spatial analys för offentlig för hands version

För att säkerställa rums analys används för scenarier som den har utformats för, vi gör den här tekniken tillgänglig för kunder genom en program process. För att få åtkomst till avstånds analys måste du börja med att fylla i vårt insugnings formulär online. [Börja ditt program här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Till gång till den offentliga för hands versionen av spatial analys omfattas av Microsofts enda val som baseras på våra berättiganderegler, först konsumentsajter process och tillgänglighet för att stödja ett begränsat antal kunder under den här för hands versionen. I offentlig för hands version letar vi efter kunder som har en betydande relation med Microsoft, är intresserade av att arbeta med oss i de rekommenderade användnings fallen och ytterligare scenarier som är i drift med våra ansvariga AI-åtaganden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med rums analys behållare](spatial-analysis-container.md)