---
title: Metodtips
description: Rekommenderade metod tips för att få bättre resultat
author: ariye
ms.author: crtreasu
ms.date: 02/17/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: da3be6e3d97e50b27ded29ba017164fdbd9a0a5b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503053"
---
# <a name="best-practices"></a>Bästa praxis

Vi rekommenderar att du testar några av de här stegen för att få bästa möjliga resultat.

## <a name="ingestion"></a>Datainmatning

- Kontrol lera dimensionerna för dina fysiska objekt. Objekt ankare fungerar bäst för objekt vars minsta dimension ligger inom intervallet för den rekommenderade 1m-10 miljoner.
- Granska din 3D-modell i program vara som [**MeshLab**](https://www.meshlab.net/) för följande information.
  - Se till att 3D-modellen har ett Triangl-nät och att trianglarna på den yttre ytan utåt. Det vill säga att hörnen ska orienteras för att göra att normaln följer den högra regeln i deras orientering utåt.
  - Kontrol lera att 3D-modellen har angetts med rätt skalnings enheter avseende fysiska objekt. Enheterna måste vara en av: ***centimeter, decimeters, fötter, tum, kilo meter, meter, millimeter,*** meter.
  - Bekräfta den nominella gravitations riktningen som motsvarar objektets verkliga vertikala orientering. Om objektets lodräta/vikt är-Y, använder du ***(0,-1, 0)** _ eller _*_ (0, 0,-1) _ * * för-Z och på samma sätt för andra riktningar.
  - Kontrol lera att 3D-modellen är kodad i något av de format som stöds: `.glb` , `.gltf` ,, `.ply` `.fbx` , `.obj` .
- Vår modell konverterings tjänst kan ta lång tid att bearbeta en stor, hög LOD-modell (Level-on-detail). För att på grund av effektiviteten kan du Förbearbeta 3D-modellen för att ta bort de inre ansikten.

## <a name="detection"></a>Identifiering

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Den tillhandahållna körnings-SDK: n kräver en användar Sök region för att söka efter och identifiera fysiska objekt. Sök området kan vara en avgränsnings ruta, en sfär, en vy Frustum eller en kombination av dem. För att undvika en falsk identifiering, är det bättre att ange en Sök region som är tillräckligt stor för att kunna lägga till objektet. När du använder de tillhandahållna exempel apparna kan du stå på en sida av objektet om 2 meter bort från den närmaste ytan och starta appen.
- Innan du startar appen objekt ankare på en HoloLens 2-enhet tar du bort Hologramen i närheten av din arbets plats via dina enheters huvud inställningar via ***Inställningar->system->hologram***

  Det här steget ser till att om ett nytt objekt, t. ex. en bil, finns på samma utrymme som ett annat tidigare, eller om objektet har flyttats från mål utrymmet, kommer gamla och irrelevanta hologram inte att bevaras och skapa förvirrande visualisering för det objekt som för närvarande visas.
- När du har tagit bort Hologramen och innan du startade appen, ska du skanna objektet, till exempel en bil, genom att titta på objektet medan enheten bearbetas från cirka 1-2 meter och saktas upp runt objektet en eller två gånger.

  Det här steget säkerställer att eventuella rester av yta som har skapats på ditt space av tidigare objekt och genomsökningar uppdateras med ytorna i det aktuella målobjektet som du kommer att arbeta med. Annars kan appen se dubbla Ghost-ytor som leder till felaktig justering av 3D-modellen och tillhör ande hologram. Om du skannar objektet minskar även svars tiden för AOA, till exempel, från 30 sekunder till 5 sekunder.
- För mörka och mycket reflekterande objekt kan du behöva Skanna objektet i närmare intervall och även genom att flytta ditt huvud uppåt och nedåt och åt vänster och höger för att låta enheten se ytor från flera vinklar och flera avstånd.
- När du ser en fel objekt identifiering, till exempel orienteringen vänds eller om du inte vill att den ska vara felaktig, till exempel en vriden modell, bör du visualisera den spatiala mappningen. De felaktiga resultaten beror ofta på dålig eller ofullständig återuppbyggnad av yta. Du kan ta bort Hologramen, skanna objektet och köra objekt identifiering i appen igen.
- Den tillhandahållna körnings-SDK: n innehåller några parametrar som gör det möjligt för användarna att finjustera identifieringen, vilket visas i våra exempel appar. Standard parametrarna fungerar bra för de flesta objekt. Här följer några rekommendationer om du upptäcker att du behöver justera dem för vissa objekt:
  - Använd ett tröskelvärde för lägre beläggnings täckning om det fysiska objektet är stort, mörkt eller blankt.
  - Tillåt en liten skalnings ändring (till exempel 0,1) för stora objekt som en bil.
  - Tillåt en del avvikelse i grader mellan objektets lokala lodräta riktning och gravitation när objektet finns på en lutning.
