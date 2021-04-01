---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "83665953"
---
Följande roller tillhandahålls för samarbete:

|Roll|Funktioner|API-åtkomst|API-behörigheter|
|--|--|--|--|
|Ägare|Alla|Autentiseringsnyckel|Alla|
|Deltagare|Alla utom möjligheten att lägga till nya medlemmar i roller|Autentiseringsnyckel|Alla utom möjligheten att lägga till nya medlemmar i roller|
|QnA Maker läsa<br>Läs|Exportera/Ladda ned<br>Test|Bearer-token|1. Hämta KB-API<br>2. Visa KB för användar-API<br>3. Hämta information om kunskaps basen<br>4. Hämta ändringar<br>Generera svar |
|QnA Maker redigerare<br>(Läs/skriv)|Exportera/Ladda ned<br>Test<br>Uppdatera KB<br>Exportera KB<br>Importera KB<br>Ersätt KB<br>Skapa kunskapsbas|Bearer-token|1. skapa KB-API<br>2. uppdatera KB-API<br>3. Ersätt KB-API<br>4. Ersätt ändringar<br>5. "träna API" [i ny tjänst modell V5]|
|Kognitiv tjänst användare<br>(Läs/skriv/publicera)|Alla|Bearer-token|Alla|