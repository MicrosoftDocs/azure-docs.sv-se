---
title: Designa Azure Functions för identiska ingångar
description: Skapa Azure Functions som ska idempotenta
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74226862"
---
# <a name="designing-azure-functions-for-identical-input"></a>Designa Azure Functions för identiska ingångar

Verkligheten för händelse driven och meddelandebaserade arkitektur kräver att du accepterar identiska begär Anden samtidigt som data integriteten och system stabiliteten bevaras.

För att illustrera, kan du titta på en knapp för höjd samtal. När du trycker på knappen tänds den och en hiss skickas till golvet. En stund senare kan någon annan ansluta dig till dig i lobbyn. Den här personen är på gång och trycker på den lysande knappen en gång i taget. Du kommer tillbaka och Chuckle till dig själv när du är påmind om att kommandot för att anropa en hiss är idempotenta.

Genom att trycka på en uttrycks samtals knapp en andra, tredje eller fjärde tiden har du inte någon betydelse för det slutliga resultatet. När du trycker på knappen skickas hissen till din våning, oavsett antalet gånger. Idempotenta system, t. ex. hissen, resulterar i samma resultat oavsett hur många gånger identiska kommandon utfärdas.

När det gäller att skapa program bör du överväga följande scenarier:

- Vad händer om inventerings kontroll programmet försöker ta bort samma produkt mer än en gång?
- Hur fungerar ditt personal program om det finns fler än en förfrågan om att skapa en medarbetar post för samma person?
- Var går pengarna till om din bank app får 100-begäran om att göra samma åter kallelse?

Det finns många kontexter där förfrågningar till en funktion kan ta emot identiska kommandon. Några situationer är:

- Principer för återförsök skickar samma begäran flera gånger
- Cachelagrade kommandon som spelas upp i programmet
- Program fel som skickar flera identiska begär Anden

För att skydda data integriteten och system hälsan innehåller ett idempotenta-program logik som kan innehålla följande beteenden:

- Verifierar att det finns data innan du försöker köra en borttagning
- Kontrollerar om det finns data som redan finns innan du försöker köra en åtgärd för att skapa
- Synkronisera logik som skapar eventuell konsekvens i data
- Samtidighets kontroller
- Dubblettidentifiering
- Verifiering av data aktualitet
- Skydds logik för att verifiera indata

Slutligen idempotens uppnås genom att säkerställa att en specifik åtgärd är möjlig och utförs bara en gång.
