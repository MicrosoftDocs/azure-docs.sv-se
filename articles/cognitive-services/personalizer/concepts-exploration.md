---
title: Utforskning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Med utforskningen kan du fortsätta att leverera bättre resultat, även om användar beteendet ändras. Att välja en utforsknings inställning är ett affärs beslut om den andel av användar interaktioner som kan utforskas med, för att förbättra modellen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87128156"
---
# <a name="exploration-and-exploitation"></a>Utforskning och utnyttjande

Med utforskningen kan du fortsätta att leverera bättre resultat, även om användar beteendet ändras.

När en Personanpassare får ett rang anrop, returnerar den en RewardActionID som antingen:
* Använder utnyttjande för att matcha det mest sannolika användar beteendet baserat på den aktuella Machine Learning-modellen.
* Använder utforskning, som inte matchar den åtgärd som har högst sannolikhet i rangordningen.

I en personanpassare används en algoritm som heter *Epsilon girig* för att utforska. 

## <a name="choosing-an-exploration-setting"></a>Välja en utforsknings inställning

Du konfigurerar den procent andel av trafiken som ska användas för utforskning på Azure Portalens **konfigurations** sida för personanpassaren. Den här inställningen anger procent andelen rang anrop som utför utforskning. 

Personanpassaren bestämmer om du vill utforska eller utnyttja sannolikheten för varje rang anrop. Detta skiljer sig från beteendet i vissa A/B-ramverk som låser en behandling på vissa användar-ID: n.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Metod tips för att välja en utforsknings inställning

Att välja en utforsknings inställning är ett affärs beslut om den andel av användar interaktioner som kan utforskas med, för att förbättra modellen. 

En inställning på noll kommer att negera många av fördelarna med en Personanpassare. Med den här inställningen använder Personanpassaren inga användar interaktioner för att upptäcka bättre användar interaktioner. Detta leder till modell stagnation, drift, och slutligen lägre prestanda.

En inställning som är för hög, kommer att negera fördelarna med inlärning från användar beteendet. Att ställa in det på 100% innebär en konstant slumpmässig het och alla inlärda beteenden från användarna påverkar inte resultatet.

Det är viktigt att inte ändra program beteendet beroende på om du ser om en Personanpassare utforskar eller utnyttjar. Detta leder till inlärnings fördomar som i slut ändan skulle minska den potentiella prestandan.

## <a name="next-steps"></a>Nästa steg

[Kunskapsförmedling](concepts-reinforcement-learning.md) 