---
title: API HTTP-svars koder – LUIS
titleSuffix: Azure Cognitive Services
description: 'Ta reda på vilka HTTP-svars koder som returneras från LUIS-redigering och slut punkts-API: er'
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.openlocfilehash: 46a7fff86d3a8c4539b77a0f271179d68e7a26d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541687"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Vanliga API-svars koder och deras innebörd

API: erna för [redigering](https://go.microsoft.com/fwlink/?linkid=2092087) och [slut punkter](https://go.microsoft.com/fwlink/?linkid=2092356) returnerar http-svars koder. När svarsmeddelanden innehåller information som är speciell för en begäran, är HTTP-svarets status kod allmänt.

## <a name="common-status-codes"></a>Vanliga status koder
I följande tabell visas några av de vanligaste HTTP-svars status koderna för [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087) -och [slut punkts](https://go.microsoft.com/fwlink/?linkid=2092356) -API: erna:

|Kod|API|Förklaring|
|:--|--|--|
|400|Redigering, slut punkt|parametrarna för begäran är felaktiga eftersom nödvändiga parametrar saknas, är felaktiga eller för stora|
|400|Redigering, slut punkt|frågans brödtext är felaktig, vilket innebär att JSON saknas, är felaktig eller för stor|
|401|Redigering|prenumerations nyckel för Använd slut punkt i stället för redigerings nyckel|
|401|Redigering, slut punkt|ogiltig, felaktig eller tom nyckel|
|401|Redigering, slut punkt| nyckeln matchar inte regionen|
|401|Redigering|du är inte ägare eller medarbetare|
|401|Redigering|Ogiltig ordning för API-anrop|
|403|Redigering, slut punkt|Total kvot gräns för månatlig nyckel har överskridits|
|409|Slutpunkt|programmet läses fortfarande in|
|410|Slutpunkt|programmet måste omtränas och publiceras om|
|414|Slutpunkt|frågan överskrider max tecken gränsen|
|429|Redigering, slut punkt|Hastighets begränsningen har överskridits (begär Anden per sekund)|

## <a name="next-steps"></a>Nästa steg

* REST API [redigering](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) och [slut punkts](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentation
