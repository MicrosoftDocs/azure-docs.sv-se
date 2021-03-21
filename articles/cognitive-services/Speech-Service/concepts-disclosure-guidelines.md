---
title: Design rikt linjer för avslöjande
titleSuffix: Azure Cognitive Services
description: Introduktion till utformnings rikt linjer och bedömning av utdelnings nivå.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 472d55f79033d60c4f40e60b55e0f7fc2ea4517e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716660"
---
# <a name="disclosure-design-guidelines"></a>Utlämnande av designriktlinjer
Lär dig att bygga och upprätthålla förtroende med kunder genom att vara transparent om din röst upplevelses syntetiska natur.

## <a name="what-is-disclosure"></a>Vad är avslöjande?

Utlämnande av information är ett sätt att låta personer veta att de&#39;att interagera med eller lyssna på en röst som skapats syntetiskt.

## <a name="why-is-disclosure-necessary"></a>Varför är det nödvändigt att lämna ut information?

Behovet av att lämna ut de syntetiska ursprungen för en dator-genererad röst är relativt nytt. Tidigare var det uppenbart att en dator genererats, vilket innebär att ingen skulle råka ut för en riktig person. Varje dag ökar emellertid realism av syntetiska röster och de blir allt mer särskiljbar från mänskliga röster.

## <a name="goals"></a>Mål
Detta är principerna som du bör tänka på när du utformar syntetiska röst upplevelser:

**Förstärka förtroende**
<br>Design med avsikt att inte Turing testet utan att försämra upplevelsen. Låt användarna vara på det faktum att de interagerar med en syntetisk röst och att de kan samar beta smidigt med upplevelsen.

**Anpassa till användnings kontext**
<br>Förstå när, var och hur dina användare interagerar med den syntetiska rösten för att ange rätt typ av avslöjande vid rätt tidpunkt.

**Ange tydliga förväntningar**
<br>Tillåt användare att enkelt upptäcka och förstå funktionerna i agenten. Erbjud möjligheter att lära sig mer om syntetisk röst teknik på begäran.

**Använd ett problem**
<br>Använd en stunds krasch för att förstärka agentens funktioner.

## <a name="how-to-use-this-guide"></a>Så här använder du den här guiden

Den här guiden hjälper dig att avgöra vilka mönster för inlämning som passar bäst för din syntetiska röst upplevelse. Vi erbjuder sedan exempel på hur och när de ska användas. Var och en av dessa mönster är utformad för att maximera transparensen med användare om syntetiskt tal och ha värdet sant för den mänskliga designen.

Med tanke på den stora delen av design vägledningen om röst upplevelser fokuserar vi här specifikt på:

1. [**Bedömning av avslöjande**](#disclosure-assessment): en process för att fastställa vilken typ av utlämnande som rekommenderas för din syntetiska röst upplevelse

2. [**Så här lämnar du**](concepts-disclosure-patterns.md): exempel på inlämnings mönster som kan tillämpas på din syntetiska röst upplevelse

3. [**Vid utlämnande**](concepts-disclosure-patterns.md#when-to-disclose): optimalt moment för att avslöjas under användar resan

## <a name="disclosure-assessment"></a>Bedömning av avslöjande
Överväg att dina användare&#39; förväntningar på en interaktion och i vilken kontext de kommer att uppleva rösten. Om kontexten gör det tydligt att en syntetisk röst &quot; talar, &quot; kan utlämnande av information vara minimalt, momentant eller till och med onödigt. De huvudsakliga typerna av kontext som påverkar inlämnade uppgifter är personens typ, scenario typ och exponerings nivå. Den hjälper också till att överväga vem som kan lyssna.

### <a name="understand-context"></a>Att förstå kontexten

Använd det här kalkyl bladet för att fastställa kontexten för den syntetiska röst upplevelsen. Du kommer att använda detta i nästa steg där du bestämmer din utdelnings nivå.

|                                    | Användnings kontext                                                                                                                                                                                                                                                                                                                                                       | Potentiella risker & utmaningar                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. persona typ**               | **Om något av följande gäller kan din person passa i kategorin "Human-liknande persona":**<br><br><ul><li> Persona återspeglar en riktig mänsklig person oavsett om det är en fiktiv representation eller inte. (t. ex. fotografi eller en dator-genererad åter givning av en riktig person)<br><br><li> Den syntetiska rösten baseras på rösten i en mycket identifierbar verklig person (t. ex. kändis, politisk figur) | De mer mänskliga representationerna som du ger dig, desto mer sannolikt är en användare att associera den med en riktig person, eller låta dem tro att innehållet talas av en riktig person i stället för att genereras av datorn. </ul>                                                                                                                                                                      |
| **2. scenario typ**            | **Om något av följande gäller passar din röst upplevelse under kategorin "känslig":**<br><br><ul><li> Hämtar eller visar personlig information från användaren <br><br> <li> Sändnings tid för känsliga nyheter/information (t. ex. larm avisering)<br><br><li> Syfte att hjälpa riktiga personer att kommunicera med varandra (t. ex. läser personliga e-postmeddelanden/texter)<br><br> <li> Tillhandahåller medicinsk/hälso hjälp </ul>            | Användningen av syntetisk röst är kanske inte lämplig eller tillförlitlig för de personer som använder den när ämnen är relaterade till känsliga, personliga eller brådskande frågor. De kan också förväntas ha samma nivå av empati och sammanhangsbaserad medvetenhet som en verklig mänsklig. |
| **3. exponerings nivå** |**Din röst miljö passar förmodligen under kategorin "hög" om:** <br><br><ul><li>Användaren hör eller interagerar med den syntetiska rösten ofta eller under en lång tids period </ul>                                                                                                                                                                             | Vikten av att vara transparent och skapa förtroende med användare är ännu högre när du skapar långsiktiga relationer.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Ange utdelnings nivå

Använd följande diagram för att avgöra om din syntetiska röst miljö kräver hög eller låg information baserat på din användnings kontext.

  ![Diagram över bedömning av avslöjande](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Se även

* [Utlämnande av designmönster](concepts-disclosure-patterns.md)
* [Utlämnande av röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Rikt linjer för ansvarig distribution av syntetisk röst teknik](concepts-guidelines-responsible-deployment-synthetic.md)