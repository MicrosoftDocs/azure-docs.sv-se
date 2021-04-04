---
title: Röst assistenter i rikt linjer för Windows-design
titleSuffix: Azure Cognitive Services
description: Rikt linjer för bästa praxis när du utformar en röst agent upplevelse.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92165153"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Design assistents upplevelser för Windows 10

Röst assistenter som utvecklats på Windows 10 måste implementera rikt linjerna för användar upplevelse nedan för att ge bästa möjliga upplevelse för röst aktivering i Windows 10. Det här dokumentet hjälper utvecklare att förstå det viktiga arbete som krävs för att en röst assistent ska kunna integreras med Windows 10-gränssnittet.

## <a name="contents"></a>Innehåll

- [Översikt över vyer för röst aktivering som stöds i Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Krav Sammanfattning](#requirements-summary)
- [Metod tips för bra lyssnings upplevelser](#best-practices-for-good-listening-experiences)
- [Design Guide för röst aktivering i appar](#design-guidance-for-in-app-voice-activation)
- [Design vägledning för röst aktivering över lås](#design-guidance-for-voice-activation-above-lock)
- [Design vägledning för för hands versionen av röst aktivering](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Översikt över vyer för röst aktivering som stöds i Windows 10

Windows 10 härleder en aktiverings upplevelse för kund kontexten baserat på enhetens kontext. Följande sammanfattnings tabell är en övergripande översikt över de olika tillgängliga vyerna när skärmen är på.

| Visa (tillgänglighet) | Enhets kontext | Kund mål | Visas när | Design behov |
| --- | --- | --- | --- | --- |
| **In-app (19H1)** | Under låset har assistenten fokus | Interagera med assistant-appen | Assistenten bearbetar begäran i appen | Visa lyssnings upplevelse i appens appar |
| **Över lås (19H2)** | Över lås, oautentiserad | Interagera med assistenten, men från ett avstånd | Systemet är låst och assistenten begär aktivering | Hel skärms visuella objekt för användar gränssnittet i långt fält. Implementera principer för att inte blockera upplåsning. |
| **För hands version av röst aktivering (20H1)** | Under låset har assistenten inte fokus | Interagera med assistenten, men på ett mindre påträngande sätt | Systemet är under lås-och assistent begär Anden om bakgrunds aktivering | Minimal arbets yta. Ändra storlek på eller inaktivera vyn huvud program efter behov. |

## <a name="requirements-summary"></a>Krav Sammanfattning

Minimal ansträngning krävs för att komma åt de olika upplevelserna. Assistenter måste dock implementera rätt design rikt linjer för varje vy. I den här tabellen nedan visas en check lista över de krav som måste följas.

| **Vyn röst aktivering** | **Sammanfattning av assistent krav** |
| --- | --- |
| **In-app** | <ul><li>Bearbeta begäran i-app</li><li>Innehåller GRÄNSSNITTs indikatorer för lyssnings tillstånd</li><li>GRÄNSSNITTs anpassningar som ändring av fönster storlek</li></ul> |
| **Över lås** | <ul><li>Identifiera lås tillstånd och aktivering av begäran</li><li>Ge inte alltid beständig UX som blockerar åtkomsten till Windows Lås skärmen</li><li>Tillhandahålla hel skärms visualisering och en röst för första upplevelse</li><li>Följ anvisningarna nedan</li><li>Följ sekretess-och säkerhets aspekter nedan</li></ul> |
| **För hands version av röst aktivering** | <ul><li>Identifiera upplåsnings tillstånd och begär bakgrunds aktivering</li><li>Rita minimalt lyssnar-UX i förhands gransknings fönstret</li><li>Rita ett Stäng X i det övre högra hörnet och Stäng av och stoppa direkt uppspelning av ljud när de trycks ned</li><li>Ändra storlek på eller lämna ut till huvudassistentens app-vy efter behov för att ge svar</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Metod tips för bra lyssnings upplevelser

Assistenter bör skapa en lyssnande upplevelse för att ge kritisk feedback så att kunden kan förstå status för assistenten. Nedan följer några möjliga tillstånd att tänka på när du skapar en assistent upplevelse. Detta är endast möjliga förslag, inte obligatorisk vägledning.

- Assistenten är tillgänglig för talindata-ingångar
- Assistenten håller på att aktive ras (antingen ett nyckelord eller en mikrofon knapp tryckning)
- Assistenten strömmar aktivt ljud till assistent molnet
- Assistenten är redo för kunden att börja tala
- Assistenten hör att orden
- Assistenten förstår att kunden är klar med att prata
- Assistenten bearbetar och förbereder ett svar
- Assistenten svarar

Även om tillstånden ändras snabbt är det värt att fundera över att tillhandahålla UX för tillstånd, eftersom varaktigheten är varierande i Windows-eko systemet. Visuell feedback samt korta ljud-CHIMES eller chirps, som även kallas &quot; earcons &quot; , kan vara en del av lösningen. På samma sätt är visuella kort kopplade till ljud beskrivningar för lämpliga svars alternativ.

## <a name="design-guidance-for-in-app-voice-activation"></a>Design Guide för röst aktivering i appar

När assistent appen har fokus är kund avsikten tydlig att interagera med appen, så alla röst aktiverings upplevelser bör hanteras av huvudappens vy. Den här vyn kan ändra storlek på kunden. I resten av det här dokumentet används det konkreta exemplet på en ekonomi tjänst assistent som heter Contoso för att hjälpa till att förklara interaktions gränssnittet. I det här och efterföljande diagram, vad kunden säger visas i tecknade tal bubblor till vänster med assistent svar i tecknade bubblor till höger.

**Vyn app. Initialt tillstånd när röst aktivering börjar:** 
 ![ skärm bild som visar appen contoso ekonomi assistenten öppen till standard arbets ytan. En tecknings prat bubbla till höger står "contoso".](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Vyn app. Efter lyckad röst aktivering börjar lyssnings upplevelsen:** ![ skärm bild av röst assistenten i Windows när röst assistenten lyssnar](media/voice-assistants/windows_voice_assistant/listening.png)

**Vyn app. Alla svar finns kvar i appens upplevelse.** ![ Skärm bild av röst assistenten i Windows som assistent svar](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Design vägledning för röst aktivering över lås

Assistenter som bygger på Windows röst aktiverings plattform är tillgängliga med 19H2 och är tillgängliga för svar över låset.

### <a name="customer-opt-in"></a>Kund deltagande

Röst aktivering över låset är alltid inaktive rad som standard. Kunderna väljer bland Windows-inställningarna>sekretess>röst aktivering. Information om övervakning och uppstart av den här inställningen finns i [hand boken för låsnings implementering](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Inte en byte på Lås skärmen

Även om aviseringar eller andra standard program för att låsa upp skärmen fortfarande är tillgängliga för assistenten, så definierar Windows Lås skärmen alltid den inledande kund upplevelsen tills en röst aktivering sker. När röst aktiveringen har identifierats visas assistentens app tillfälligt ovanför Lås skärmen. Om du vill undvika kundens förvirring, när aktivt ovan lås, måste assistentens app aldrig Visa användar gränssnittet för att begära någon typ av autentiseringsuppgifter eller logga in.

![Skärm bild av en Windows Lås skärm](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Låsnings upplevelse efter röst aktivering

När skärmen är aktive rad är assistent appen full skärm utan namn List ovanför Lås skärmen. Större visuella objekt och starka röst beskrivningar med starkt röst – primärt gränssnitt tillåter att kunden är för långt borta för att läsa användar gränssnittet eller ha händerna upptagen med en annan (icke-PC) aktivitet.

När skärmen förblir inaktive rad kan assistent appen spela upp en Earcon för att visa att assistenten är aktive rad och ger en enda röst upplevelse.

![Skärm bild av röst assistenten över lås](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Principer för inlösta

Assistenten måste implementera den inlösta vägledningen i det här avsnittet för att göra det enklare för kunder att logga in nästa gången de vill använda sina Windows-datorer. Nedan visas särskilda krav som assistenten måste implementera:

- **Alla assistent arbets ytor som visas ovan måste innehålla ett X** högst upp till höger som stänger av assistenten.
- **Om du trycker på valfri tangent måste du också stänga av appen assistent**. Tangent bords indata är en traditionell lås app-signal som kunden vill logga in på. Det innebär att alla tangent bord/text-inmatare inte ska dirigeras till appen. I stället bör appen stängas av när tangent bords indata identifieras, så att kunden enkelt kan logga in på sina enheter.
- **Om skärmen går ut måste appen stängas av.** Detta säkerställer att inloggnings skärmen är klar och väntar på nästa gången kunden använder sin dator.
- Om appen används &quot; &quot; kan det fortsätta att vara kvar över låset. &quot;används för att &quot; Ange indata eller utdata. Till exempel när en musik eller video strömmas, kan appen fortsätta att vara över låset. &quot;Följ &quot; de här stegen i och andra dialog steg för multiaktivering är tillåtna för att hålla appen över låset.
- **Implementerings information om hur du stänger programmet** finns [i hand boken för låsnings implementering](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Skärm bild som visar den ovan säkra vyn i appen contoso ekonomi assistent.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Skärm bild av ett skriv bord som visar Windows Lås skärmen.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Sekretess &amp; säkerhets aspekter över lås

Många datorer är bärbara men inte alltid inom kundens räckvidd. De kan vara en kort plats på hotell utrymmen, flyg Plans platser eller arbets ytor där andra personer har fysisk åtkomst. Om assistenter som aktive ras ovan inte är förberedda kan de bli föremål för klassen med så kallade &quot; [Evil Maid](https://en.wikipedia.org/wiki/Evil_maid_attack) - &quot; attacker.

Assistenterna bör därför följa rikt linjerna i det här avsnittet för att hålla dig säker. Interaktionen ovanför låset sker när Windows-användaren inte är autentiserad. Det innebär att i allmänhet **bör inström till assistenten även behandlas som oautentiserad**.

- Assistenter bör **implementera en lista över tillåtna kunskaper för att identifiera kunskaper som bekräftas och är säkra** att komma åt ovanför låset.
- Tekniker-ID-tekniker kan spela en roll för att minska riskerna, men högtalar-ID är inte en lämplig ersättning för Windows-autentisering.
- Listan över tillåtna kunskaper bör överväga tre olika typer av åtgärder eller kunskaper:

| **Åtgärds klass** | **Beskrivning** | **Exempel (inte en fullständig lista)** |
| --- | --- | --- |
| Säker utan autentisering | Generell användnings information eller grundläggande app-kommando och kontroll | &quot;Vilken tid är det? &quot; , kan &quot; du spela upp nästa spår&quot; |
| Säkert med högtalar-ID | Personifiering av risk, som avslöjar personlig information. | &quot;Vad&#39;s nästa avtalade tid? &quot; , &quot; Granska min shopping lista &quot; , &quot; besvara samtalet&quot; |
| Endast säker efter Windows-autentisering | Hög risk åtgärder som en angripare kan använda för att skada kunden | &quot;Köp fler mat varor &quot; , &quot; ta bort mitt (viktigt) avtalad tid &quot; , &quot; Skicka ett (medelvärde) textmeddelande &quot; , &quot; starta en (post) webb sida&quot; |

För contoso är allmän information kring offentlig information säker utan autentisering. Kundspecifik information, till exempel antalet ägda resurser är förmodligen säkert med högtalar-ID. Köp-eller försäljnings lager bör dock aldrig tillåtas utan Windows-autentisering.

För att ytterligare skydda upplevelsen, **Weblinks eller andra app-to-app-lanseringar blockeras alltid av Windows tills kunden loggar in.** Som en sista utväg förbehåller Microsoft rätten att ta bort ett program från listan över tillåtna aktiverade assistenter om ett allvarligt säkerhets problem inte åtgärdas inom rimlig tid.

## <a name="design-guidance-for-voice-activation-preview"></a>Design vägledning för för hands versionen av röst aktivering

Under låset, när assistent appen _inte_ har fokus, ger Windows ett mindre påträngande röst aktiverings gränssnitt för att hålla kunden i flöde. Detta gäller särskilt för falska aktiveringar som skulle vara mycket störande om de startade hela appen. Core-idén är att varje assistent har en annan start i gränssnittet, ikonen för assistenten i aktivitets fältet. När begäran om bakgrunds aktivering inträffar visas en liten vy ovanför assistent ikonen i aktivitets fältet. Assistenter bör ge en liten lyssnings upplevelse på den här arbets ytan. När du har bearbetat förfrågningarna kan assistenterna välja att ändra storlek på den här vyn för att visa ett kontext svar eller för att lämna in huvudappens vy för att visa större, mer detaljerade visuella objekt.

- För att se till att förhands granskningen inte har någon namn List, så att **assistenten måste rita ett X högst upp till höger så att kunderna kan stänga vyn.** Information om hur du [stänger programmet](windows-voice-assistants-implementation-guide.md#closing-the-application) för specifika API: er som ska anropas när knappen Stäng trycks ned.
- För att stödja förhands visning av röst aktivering kan assistenter bjuda in kunder att fästa assistenten i aktivitets fältet under första körningen.

**För hands version av röst aktivering: initialt tillstånd**

Contoso-assistenten har ett start i aktivitets fältet: deras virvel, cirkulära ikon.

![Skärm bild av röst assistenten i Windows som en ikon före aktivering i aktivitets fältet](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**När aktiveringen fortskrider** begär assistenten bakgrunds aktivering. Assistenten har fått ett litet förhands gransknings fönster (standard bredden 408 och height: 248). Om röst aktiveringen på Server sidan avgör att signalen var falsk positivt, kan den här vyn ignoreras för minimalt avbrott.

![Skärm bild av röst assistenten i Windows i kompakt vy vid verifiering av aktivering](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**När den slutliga aktiveringen bekräftas**, presenterar assistenten sitt lyssnings gränssnitt. Assistenten måste alltid rita ett Stäng X längst upp till höger i förhands granskningen av röst aktiveringen.

![Skärm bild av röst assistenten i Windows-lyssning i kompakt vy](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**Snabb svar** kan visas i förhands granskningen av röst aktiveringen. En TryResizeView gör det möjligt för assistenter att begära olika storlekar.

![Skärm bild av röst assistenten i Windows-svar i kompakt vy](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Lämna ut**. Assistenten kan när som helst komma över till huvudappens vy för att ge mer information, dialog eller svar som kräver mer skärm utrymme. I avsnittet [över gång från komprimera vy till fullständig vy](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) finns information om implementering.

![Skärm bilder av röst assistenten i Windows före och efter expandering av den kompakta vyn](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att utveckla din röst assistent](how-to-windows-voice-assistants-get-started.md)