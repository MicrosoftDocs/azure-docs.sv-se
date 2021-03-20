---
title: Aktiva och inaktiva händelser – Personanpassare
description: I den här artikeln beskrivs användningen av aktiva och inaktiva händelser i tjänsten personanpassa.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253657"
---
# <a name="active-and-inactive-events"></a>Aktiva och inaktiva händelser

En **aktiv** händelse är ett anrop till rangordningen där du vet att du kommer att visa resultatet för kunden och fastställa belönings poängen. Det här är standardbeteendet.

En **inaktiv** händelse är ett anrop till rangordningen där du inte är säker på om användaren kommer att se den rekommenderade åtgärden, på grund av affärs logik. På så sätt kan du ignorera händelsen så att en personlig personal inte tränas med standard belöningen. Inaktiva händelser ska inte anropa belönings-API: et.

Det är viktigt att inlärnings slingan känner till faktisk typ av händelse. En inaktiv händelse kommer inte att ha något belönings samtal. En aktiv händelse bör ha ett belönings samtal, men om API-anropet aldrig görs tillämpas standard belönings poängen. Ändra status för en händelse från inaktiv till aktiv så snart du vet att den påverkar användar upplevelsen.

## <a name="typical-active-events-scenario"></a>Scenario för vanliga aktiva händelser

När programmet anropar rang-API: et får du den åtgärd som programmet ska visa i fältet **rewardActionId** .  Från och med den tidpunkten förväntar sig ett belönings samtal med en belönings Poäng som har samma eventId. Belönings poängen används för att träna modellen för framtida Rangbaserade samtal. Om inget belönings anrop tas emot för eventId tillämpas en standard belöning. [Standard förmåner](how-to-settings.md#configure-rewards-for-the-feedback-loop) ställs in på din personanpassa resurs i Azure Portal.

## <a name="other-event-type-scenarios"></a>Andra händelse typ scenarier

I vissa fall kan programmet behöva anropa rang innan det vet om resultatet ska användas eller visas för användaren. Detta kan inträffa i situationer där exempelvis sid åter givningen av framhävda innehåll skrivs över av en marknadsförings kampanj. Om resultatet av rang anropet aldrig har använts och användaren aldrig har sett det, ska du inte skicka ett motsvarande belönings samtal.

De här scenarierna inträffar vanligt vis när:

* Du föråterger användar gränssnittet som användaren kan eller inte kan se.
* Ditt program gör en förutsägelse anpassning i vilken ranknings anrop görs med en liten real tids kontext och programmet kan eventuellt inte använda utdata.

I dessa fall använder du Personanpassare för att anropa rang, vilket begär att händelsen ska vara _inaktiv_. Personanpassaren förväntar sig inte en belöning för den här händelsen och kommer inte att tillämpa en standard belöning.

Om programmet använder informationen från rang anropet senare i din affärs logik _aktiverar_ du bara händelsen. Så snart händelsen är aktiv förväntar sig en händelse genom att göra en händelse belöning. Om inget uttryckligt anrop görs till belönings-API: et, tillämpar Personanpassaren en standard belöning.

## <a name="inactive-events"></a>Inaktiva händelser

Om du vill inaktivera utbildning för en händelse anropar du rang med hjälp av `learningEnabled = False` .

Vid inaktivitet aktive ras inlärningen implicit om du skickar en belöning för eventId eller anropar `activate` API: et för eventId.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du tar reda på belönings poängen och vilka data du bör tänka på](concept-rewards.md).
