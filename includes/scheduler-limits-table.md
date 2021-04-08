---
title: ta med fil
description: ta med fil
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75392427"
---
I följande tabell beskrivs var och en av de viktigaste kvoterna, gränserna, standarderna och begränsningarna i Azure Scheduler.

| Resurs | Gräns Beskrivning |
| -------- | ----------------- |
| **Jobb storlek** | Den maximala jobb storleken är 16 000. Om en åtgärd för att införa eller en uppdatering resulterar i en jobb storlek som är större än den här gränsen returneras en status kod på 400 felaktig status. | 
| **Jobb samlingar** | Det maximala antalet jobb samlingar per Azure-prenumeration är 200 000. | 
| **Jobb per samling** | Som standard är det maximala antalet jobb fem jobb i en kostnads fri jobb samling och 50 jobb i en standard jobb samling. Du kan ändra det maximala antalet jobb för en jobb samling. Alla jobb i en jobb samling är begränsade till värdet som angetts för jobb samlingen. Om du försöker skapa fler jobb än den maximala jobb kvoten Miss lyckas begäran med en status kod för 409-konflikt. | 
| **Tid till start tid** | Max "tid för start tid" är 18 månader. |
| **Upprepnings intervall** | Det maximala upprepnings intervallet är 18 månader. | 
| **Frekvens** | Som standard är den maximala frekvens kvoten en timme i en kostnads fri jobb samling och en minut i en standard jobb samling. <p>Du kan göra den maximala frekvensen för en jobb samling lägre än max värdet. Alla jobb i jobb samlingen är begränsade till värdet som angetts för jobb samlingen. Om du försöker skapa ett jobb med en högre frekvens än den maximala frekvensen för jobb samlingen, Miss lyckas begäran med en status kod för 409-konflikt. | 
| **Storlek på brödtext** | Den maximala text storleken för en begäran är 8 192 tecken. |
| **URL-storlek för begäran** | Den maximala storleken för en fråge-URL är 2 048 tecken. |
| **Antal huvuden** | Det maximala antalet huvuden är 50 sidhuvud. | 
| **Sammanställd huvud storlek** | Den maximala sammanlagda huvud storleken är 4 096 tecken. |
| **Standardvärde** | Timeout för begäran är statisk, det vill säga inte kan konfigureras. och är 60 sekunder för HTTP-åtgärder. För längre drift åtgärder följer du de HTTP-asynkrona protokollen. Returnera till exempel en 202 omedelbart men fortsätt arbeta i bakgrunden. | 
| **Jobbhistorik** | Det maximala svars innehållet som lagras i jobb historiken är 2 048 byte. |
| **Kvarhållning av jobb historik** | Jobb historiken bevaras i upp till två månader eller upp till de senaste 1 000-körningarna. | 
| **Slutförd och felaktig kvarhållning av jobb** | Slutförda och felaktiga jobb sparas i 60 dagar. |
||| 

