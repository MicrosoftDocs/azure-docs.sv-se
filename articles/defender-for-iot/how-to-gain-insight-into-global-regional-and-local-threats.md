---
title: Få insikter om globala, regionala och lokala hot
description: Få insikt i globala, regionala och lokala hot genom att använda webbplats kartan i den lokala hanterings konsolen.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784126"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Få insikter om globala, regionala och lokala hot

Webbplats kartan i den lokala hanterings konsolen hjälper dig att uppnå fullständig säkerhets täckning genom att dela upp nätverket i geografiska och logiska segment som återspeglar din affärstopologi:

- **Geografisk anläggnings nivå**: en plats visar ett antal enheter grupperade efter en geografisk plats som visas på kartan. Som standard tillhandahåller Azure Defender för IoT en världs karta. Du uppdaterar kartan så att den återspeglar organisationens eller affärs strukturen. Du kan till exempel använda en karta som återspeglar platser i ett angivet land, stad eller industriellt campus. När plats färgen ändras på kartan ger den SOC-teamet en indikation på den kritiska system statusen i anläggningen.

  Kartan är interaktiv och gör det möjligt att öppna varje webbplats och Delve till den här platsens information.

- **Globalt logiskt lager**: en affär senhet är ett sätt att dela upp ditt företag i logiska segment enligt specifika branscher. När du gör detta visas din affärstopologi på kartan.

  Till exempel kan ett globalt företag som innehåller glas fabriker, plast fabriker och bil fabriker hanteras som tre olika affär senheter. En fysisk plats som finns i Toronto innehåller tre olika produktions linjer för glas, en produktions linje för plast och en produktions linje för Truck-motor. På så sätt har den här webbplatsen företrädare för alla tre affär senheterna.

- **Geografisk region nivå**: skapa regioner för att dela upp ett globalt företag i geografiska regioner. Företaget som vi benämnt kan till exempel använda regionerna Nordamerika, Västeuropa och östra Europa. Nordamerika har fabriker från alla tre affär senheterna. Västeuropa har bil fabriker och glas fabriker och östra Europa har endast plast fabriker.

- **Lokal logisk segment nivå**: en zon är ett logiskt segment inom en plats som definierar, till exempel ett funktionellt område eller en produktions linje. Genom att arbeta med zoner kan du tvinga säkerhets principer som är relevanta för zon definitionen. Till exempel kan en webbplats som innehåller fem produktions rader segmenteras i fem zoner.

- **Lokal visnings nivå**: en lokal vy av en enskild sensor installation ger inblick i drift-och säkerhets statusen för anslutna enheter.

## <a name="work-with-site-map-views"></a>Arbeta med vyer för webbplats översikt

Den lokala hanterings konsolen innehåller en övergripande vy över ditt industriella nätverk i en Sammanhangs beroende karta. Den allmänna kart visningen visar den globala kartan över din organisation med den geografiska platsen för varje plats.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Skärm bild av vyn Enterprise Map.":::

### <a name="color-coded-map-views"></a>Färgkodade kart visningar

**Grönt**: antalet säkerhets händelser är under det tröskelvärde som Defender för IoT har definierats för systemet. Ingen åtgärd krävs.

**Gul**: antalet säkerhets händelser är lika med det tröskelvärde som Defender för IoT har definierats för systemet. Överväg att undersöka händelserna.  

**Röd**: antalet säkerhets händelser överskrider det tröskelvärde som Defender för IoT har definierat för systemet. Vidta omedelbara åtgärder.

### <a name="risk-level-map-views"></a>Kart vyer på risk nivå

**Riskbedömning**: i vyn riskbedömning visas information om webbplats risker. Risk information hjälper dig att prioritera minskning och bygga en väg karta för att planera säkerhets förbättringar.

**Incident svar**: få en centraliserad vy över alla aviseringar som inte har godkänts på varje plats i företaget. Du kan öka detalj nivån och hantera aviseringar som identifierats på en angiven plats.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Skärm bild av Enterprise Map-vyn med incident svar.":::

**Skadlig aktivitet**: om skadlig kod upptäcks visas platsen i rött. Det innebär att du bör vidta omedelbara åtgärder.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Skärm bild av vyn Enterprise Map med skadlig aktivitet.":::

**Drift aviseringar**: den här kart visningen för olika system är en bättre förståelse för vilket system som kan uppleva drifts incidenter, till exempel PLC stoppas, uppladdning av inbyggd program vara och program uppladdning.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Skärm bild av vyn Enterprise Map med drift aviseringar.":::

Så här väljer du en Map-vy:

1. Välj **standardvy** från kartan.
2. Välj en vy.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Skärm bild av vyn för plats kartans standardvy.":::

## <a name="update-the-site-map-image"></a>Uppdatera plats kartans bild

Defender for IoT tillhandahåller en världs standard karta. Du kan ändra den så att den återspeglar din organisation: en land karta eller en stads karta. 

Ersätta kartan:

1. I den vänstra rutan väljer du **Systeminställningar**.

2. Välj **ändra webbplats kartan** och överför bild filen för att ersätta standard kartan.

## <a name="next-step"></a>Nästa steg

[Visa aviseringar](how-to-view-alerts.md)
