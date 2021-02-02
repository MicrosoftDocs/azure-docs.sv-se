---
title: Rikt linjer för ansvarig distribution av syntetisk röst teknik
titleSuffix: Azure Cognitive Services
description: Microsofts allmänna design rikt linjer för att använda syntetisk röst teknik. De här utvecklades i studier som Microsoft genomförde med röst personal, konsumenter, och för användare med tal sjukdomar för att vägleda utvecklingen av syntetisk röst.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 715c09ef65358b21e78cfde204b4819db0c7875d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428427"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Rikt linjer för ansvarig distribution av syntetisk röst teknik

## <a name="general-considerations-to-keep-in-mind-when-implementing-ai-systems"></a>Allmänna överväganden som du bör tänka på när du implementerar AI-system 

Den här artikeln handlar specifikt om syntetiskt tal och anpassat neurala-röst och viktiga överväganden för att använda den här tekniken ett ansvarsfullt sätt. I allmänhet finns det dock flera saker du behöver tänka på noggrant när du bestämmer hur du ska använda och implementera AI-drivna produkter och funktioner: 

* Kommer den här produkten eller funktionen att fungera bra i mitt scenario? Innan du distribuerar AI i ditt scenario ska du testa hur det fungerar med data i real tid och se till att det kan leverera den precision du behöver. 
* Är vi utrustade för att identifiera och reagera på fel? AI-drivna produkter och funktioner är inte alltid 100% korrekt, så fundera över hur du ska identifiera och svara på eventuella fel som kan uppstå. 

## <a name="general-guidelines-for-using-synthetic-voice-technology"></a>Allmänna rikt linjer för att använda syntetisk röst teknik 
Här följer Microsofts allmänna design rikt linjer för att använda syntetisk röst teknik. Dessa utvecklades i studier som Microsoft genomförde med röst personal, konsumenter, samt individer med tal sjukdomar för att vägleda utvecklingen av syntetisk röst.

För distribution av syntetisk tal teknik gäller följande rikt linjer för de flesta scenarier.

### <a name="disclose-when-the-voice-is-synthetic"></a>Lämna ut när rösten är syntetisk
Om du avmarkerar att en röst är en dator som genereras kan du inte bara minimera risken för skadliga resultat från bedrägeri, men det ökar också förtroendet i organisationen som levererar rösten. Lär dig mer om [att lämna](concepts-disclosure-guidelines.md)ut.

Microsoft kräver att kunderna avslöjar den syntetiska typen av anpassad neurala-röst till sina användare. 
* Se till att tillhandahålla tillräcklig information till mål grupperna, särskilt när rösten hos en välkänd person, som gör sitt beslut om information baserat på den person som levererar den, oavsett om de gör det eller inte.  Till exempel kan utlämnande delas av verb i början av sändningen. Mer information finns i [utlämnade mönster](concepts-disclosure-patterns.md).   
* Överväg att lämna ut information till föräldrar eller andra parter med användnings fall som är utformade för minderåriga och barn – om ditt användnings fall är avsett för minderåriga eller underordnade, måste du se till att de föräldrarna eller de juridiska webbläsarna kan förstå inlämnandet av syntetiskt medium och fatta rätt beslut för de minderåriga eller barn som ska använda upplevelsen. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Välj lämpliga röst typer för ditt scenario
Överväg noga användnings sammanhanget och de potentiella skadan som är associerade med att använda syntetisk röst. Till exempel är det inte säkert att de syntetiska rösterna med hög kvalitet är lämpliga i högrisk scenarier, t. ex. för personliga meddelanden, ekonomiska transaktioner eller komplexa situationer som kräver mänsklig anpassning eller empati. Användarna kan också ha olika förväntningar för röst typer. Till exempel, när du lyssnar på känsliga nyheter som läses av en syntetisk röst, föredrar vissa användare en mer empatisk och mänsklig läsning av nyheterna, medan andra föredrar en mer enkel färgs visning av rösten. Överväg att testa ditt program för att bättre förstå användar inställningarna.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Var transparent om funktioner och begränsningar
Det är mer troligt att användarna har fler förväntningar när de använder syntetiska röst agenter med hög kvalitet. Det innebär att om system funktionerna inte uppfyller förväntningarna kan förtroendet bli lidande och kan resultera i krångligt eller till och med skadliga upplevelser.

### <a name="provide-optional-human-support"></a>Ge tillvals personal support
I tvetydiga, transaktions scenarier (till exempel ett Call Support Center) kan användarna inte alltid lita på en dator agent för att svara på lämpliga förfrågningar. Stöd för mänsklig personal kan vara nödvändigt i dessa fall, oberoende av den realistiska kvaliteten på systemets röst eller funktion.

## <a name="considerations-for-voice-talent"></a>Överväganden för röst personal
När du arbetar med röst personal, till exempel röst aktörer, för att skapa syntetiska röster, gäller rikt linjerna nedan.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Få ett meningsfullt godkännande från röst personal
Röst kunskap bör ha kontroll över sin röst modell (hur och var den ska användas) och kompenseras för användning. Microsoft kräver att anpassade röst kunder erhåller uttryckligt skriftligt tillstånd från sina röst personal för att skapa en syntetisk röst och dess avtal med röst kunskap, vilket är varaktighet, användning och eventuella innehålls begränsningar.  Om du skapar en syntetisk röst av en välkänd person bör du tillhandahålla ett sätt för personen bakom rösten att redigera eller godkänna innehållet.

Vissa röst kunskap är inte medvetna om den potentiella skadliga användningen av tekniken och bör vara sammanutbildad av systemets ägare om funktionerna i tekniken. Microsoft kräver att kunder delar Microsofts information [om röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent) med röst-personal direkt eller via röst personals auktoriserade ombud som beskriver hur syntetiska röster utvecklas och arbetar tillsammans med text till tal tjänster.

## <a name="considerations-for-those-with-speech-disorders"></a>Att tänka på med tal sjukdomar
När du arbetar med individer med tal sjukdomar, för att skapa eller distribuera syntetisk röst teknik gäller följande rikt linjer.

### <a name="provide-guidelines-to-establish-contracts"></a>Ange rikt linjer för att upprätta kontrakt
Ange rikt linjer för att skapa avtal med personer som använder syntetisk röst för att få hjälp med att tala. Kontraktet bör överväga att ange de parter som äger rösten, varaktigheten för användning, villkor för ägarskaps överföring, procedurer för att ta bort röst teckensnittet och hur du förhindrar obehörig åtkomst. Du kan också aktivera den avtalade överföringen av röst teckensnitts ägande efter dödsfall till familje medlemmar om den personen har fått behörighet.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konto för inkonsekvenser i tal mönster
För enskilda användare med tal sjukdomar som registrerar sina egna röst teckensnitt kan inkonsekvenser i deras tal mönster (slurring eller oförmåga att uttala vissa ord) komplicera inspelnings processen. I dessa fall bör syntetisk röst teknik och inspelnings sessioner hantera dem (det vill säga ge raster och ytterligare antal inspelnings sessioner).

### <a name="allow-modification-over-time"></a>Tillåt ändring över tid
Individer med tal sjukdomar vill göra uppdateringar av sin syntetiska röst för att återspegla ålders fördelning (till exempel ett barn som når puberty). Individer kan också ha stilistiska inställningar som förändras över tid, och det kan vara bra att göra ändringar i bredd, dekor färg eller andra röst egenskaper.


## <a name="reference-docs"></a>Referens dokument

* [Utlämnande av röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Översikt över hantera](concepts-gating-overview.md)
* [Lämna ut](concepts-disclosure-guidelines.md)
* [Design mönster för utlämnande](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Nästa steg

* [Utlämnande av röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Lämna ut](concepts-disclosure-guidelines.md)
* [Design mönster för utlämnande](concepts-disclosure-patterns.md)
