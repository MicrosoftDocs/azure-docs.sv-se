---
title: Röst assistenter i Windows – översikt
titleSuffix: Azure Cognitive Services
description: En översikt över röst assistenterna i Windows, inklusive funktioner och utvecklings resurser tillgängliga.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024763"
---
# <a name="voice-assistants-on-windows"></a>Röst assistenter i Windows

I Windows 10 version 2004 och senare kan röst assistent program dra nytta av Windows ConversationalAgent-API: er för att få en fullständig röst aktive rad assistent.

## <a name="voice-assistant-features"></a>Röst assistent funktioner

Voice agent-program kan aktive ras med ett talade nyckelord för att möjliggöra en kostnads fri, röst driven upplevelse. Röst aktivering fungerar när programmet stängs och när skärmen är låst.

Dessutom tillhandahåller Windows en uppsättning sekretess inställningar för röst aktivering som ger användare kontroll över röst aktivering och över lås aktivering för varje app-basis.

Efter röst aktiveringen kommer Windows att hantera flera aktiva agenter korrekt och meddela varje röst assistent om de är avbrutna eller inaktiverade. Detta gör att program kan hantera avbrott och andra händelser i flera agenter på rätt sätt.

## <a name="how-does-voice-activation-work"></a>Hur fungerar röst aktiveringen?

Agent aktiverings körningen (AAR) är den pågående processen i Windows som hanterar program aktivering på ett talade nyckelord eller knapp tryckning. Den börjar med Windows så länge det finns minst ett program på systemet som är registrerat hos systemet. Program interagerar med AAR via ConversationalAgent-API: erna i Windows SDK.

När användaren pratar med ett nyckelord meddelar program-eller maskin varu nyckelordet spotter på systemet AAR att ett nyckelord har identifierats, vilket ger ett nyckelords-ID. AAR skickar i sin tur en begäran till BackgroundService för att starta programmet med motsvarande program-ID.

### <a name="registration"></a>Registrering

Första gången ett röst aktiverat program körs registreras dess app-ID och nyckelords information via ConversationalAgent-API: erna. AAR registrerar alla konfigurationer i den globala mappningen med spotter för maskinvaru-eller program varu nyckelord i systemet, så att de kan identifiera programmets nyckelord. Programmet registreras även [med bakgrunds tjänsten](/windows/uwp/launch-resume/register-a-background-task).

Observera att det innebär att ett program inte kan aktive ras av Voice förrän det har körts en gång och registreringen har kunnat slutföras.

### <a name="receiving-an-activation"></a>Ta emot en aktivering

När begäran tas emot från AAR startar bakgrunds tjänsten programmet. Programmet tar emot en signal genom OnBackgroundActivated livs cykel metod i `App.xaml.cs` med ett unikt händelse argument. Det här argumentet anger att programmet har Aktiver ATS av AAR och att det ska starta nyckelords verifieringen.

Om programmet har verifierat nyckelordet kan det göra en begäran som visas i förgrunden. När den här begäran lyckas visar programmet gränssnitt och fortsätter att interagera med användaren.

AAR signalerar fortfarande aktiva program när deras nyckelord talas. I stället för att signalera genom livs cykel metoden i `App.xaml.cs` , signalerar den genom en händelse i ConversationalAgent-API: erna.

### <a name="keyword-verification"></a>Nyckelords verifiering

Nyckelordet spotter som utlöser programmet för start har uppnått låg strömförbrukning genom att förenkla nyckelords modellen. På så sätt kan nyckelordet spotter vara "Always On" utan hög effekt, men det innebär också att nyckelordet spotter troligen har ett stort antal "falskt accepterar" där det identifierar ett nyckelord även om inget nyckelord har talas. Detta är orsaken till att röst aktiverings systemet startar programmet i bakgrunden: om du vill ge programmet en chans att verifiera att nyckelordet har talat innan du avbryter användarens aktuella session. AAR sparar ljudet efter några sekunder innan nyckelordet var Spotted och gör det tillgängligt för programmet. Programmet kan använda detta för att köra en mer tillförlitlig nyckelords spotter på samma ljud.

## <a name="next-steps"></a>Nästa steg

- **Läs rikt linjerna för design:** Våra [design rikt linjer](windows-voice-assistants-best-practices.md) utformar det viktiga arbetet som krävs för att tillhandahålla bästa möjliga upplevelse för röst aktivering i Windows 10.
- **Besök sidan komma igång:** Börja [här](how-to-windows-voice-assistants-get-started.md) för att börja implementera röst assistenter i Windows, från att ställa in utvecklings miljön genom en introduktion till implementerings guide.
- **Prova appen exempel**: om du vill uppleva dessa funktioner själv går du till [exempel sidan UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) och följer anvisningarna för att hämta exempel klienten som kör.