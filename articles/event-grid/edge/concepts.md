---
title: Begrepp – Azure Event Grid IoT Edge | Microsoft Docs
description: Begrepp i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682387"
---
# <a name="event-grid-concepts"></a>Event Grid-begrepp

I den här artikeln beskrivs huvud begreppen i Azure Event Grid.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som fullständigt beskriver något som hände i systemet. Varje händelse har gemensam information, t. ex. Händelsens källa, tid då händelsen ägde rum och unik identifierare. Varje händelse har också en speciell information som endast är relevant för den speciella typen av händelse. Stöd för en händelse av en storlek på upp till 1 MB är för närvarande en för hands version.

De egenskaper som ingår i en händelse finns i [Azure Event Grid händelse schema](event-schemas.md).

## <a name="publishers"></a>Utgivare

En utgivare är användaren eller organisationen som bestämmer sig för att skicka händelser till Event Grid. Du kan publicera händelser från ditt eget program.

## <a name="event-sources"></a>Händelsekällor

En händelse källa är den plats där händelsen inträffar. Varje händelsekälla är relaterat till en eller flera händelsetyper. Till exempel är Azure Storage händelsekällan för händelser som skapas i blobbar. Ditt program är händelsekällan för anpassade händelser som du definierar. Händelsekällor ansvarar för att skicka händelser till Event Grid.

## <a name="topics"></a>Ämnen

Avsnittet Event Grid innehåller en slut punkt där källan skickar händelser. Avsnittet Event Grid skapas i utgivaren och avgör om en händelse källa behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser, bestämmer prenumeranter vilka ämnen som ska prenumerera på.

När du designar ditt program är du flexibelt att bestämma hur många ämnen som ska skapas. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelse prenumeranter kan filtrera efter de händelse typer som de vill ha.

Se [REST API dokumentation](api.md) om hur du hanterar ämnen i Event Grid.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration visar Event Grid vilka händelser på ett ämne som du är intresse rad av att ta emot. När du skapar prenumerationen anger du en slut punkt för att hantera händelsen. Du kan filtrera de händelser som skickas till slut punkten. 

Se [REST API dokumentation](api.md) om hur du hanterar prenumerationer i Event Grid.

## <a name="event-handlers"></a>Händelsehanterare

Från ett Event Grid perspektiv är en händelse hanterare den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Event Grid stöder flera typer av hanterare. Du kan använda en Azure-tjänst som stöds eller din egen webhook som hanterare. Beroende på typen av hanterare Event Grid följande olika mekanismer för att garantera att händelsen levereras. Om mål händelse hanteraren är en HTTP-webbhook görs ett nytt försök att utföra händelsen tills hanteraren returnerar status koden `200 – OK` . För Edge Hub, om händelsen levereras utan undantag, betraktas den som lyckad.

## <a name="security"></a>Säkerhet

Event Grid tillhandahåller säkerhet för att prenumerera på ämnen och publicera ämnen. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

## <a name="event-delivery"></a>Händelseleverans

Om Event Grid inte kan bekräfta att en händelse har mottagits av prenumerantens slut punkt, kommer den att leverera om händelsen. Mer information finns i [Event Grid meddelande leverans och försök igen](delivery-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne måste händelser alltid publiceras i en matris. I scenarier med lågt data flöde har matrisen bara ett värde. För användnings fall av stora volymer rekommenderar vi att du grupperar flera händelser per publicering för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB. Varje händelse ska fortfarande inte vara större än 1 MB (för hands version).