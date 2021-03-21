---
title: Komma igång-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Lär dig hur du kommer igång med Threat Modeling Tool. Skapa ett diagram, identifiera hot, minimera hot och verifiera varje minskning.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94515735"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Komma igång med Threat Modeling Tool

Microsoft Threat Modeling Tool 2018 släpptes som GA i september 2018 som ett kostnads fritt **[klick att ladda ned](https://aka.ms/threatmodelingtool)**. Med funktionen ändra leverans kan vi skicka de senaste förbättringarna och fel korrigeringarna till kunderna varje gången de öppnar verktyget, vilket gör det lättare att underhålla och använda.
Den här artikeln tar dig igenom processen för att komma igång med Microsoft SDL Threat Modeling-metoden och visar hur du använder verktyget för att utveckla fantastiska hot modeller som ett stamnät till din säkerhets process.

Den här artikeln bygger på befintliga kunskaper om den här metoden för SDL Threat-modellering. En snabb genom gång finns i **[webb program för hot modellering](/previous-versions/msp-n-p/ff648006(v=pandp.10))** och en arkiverad version av avlästa **[säkerhets brister med hjälp av](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** artikeln gå till MSDN-artikeln Publicerad i 2006.

För att snabbt sammanfatta, inbegriper metoden att skapa ett diagram, identifiera hot, minimera dem och verifiera varje åtgärd. Här är ett diagram som markerar den här processen:

![SDL-process](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Startar processen för hot modellering

När du startar Threat Modeling Tool ser du några saker som visas på bilden:

![Tom Start sida](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sektionen hot modell

| Komponent                                   | Information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Knappen feedback, förslag och problem** | Tar dig till **[MSDN-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** för alla objekt i sdl. Det ger dig möjlighet att läsa igenom vad andra användare gör, tillsammans med lösningar och rekommendationer. Om du fortfarande inte kan hitta det du söker, e-post tmtextsupport@microsoft.com för vårt support team för att hjälpa dig                                                                                                                            |
| **Skapa en modell**                          | Öppnar en tom arbets yta där du kan rita ditt diagram. Se till att välja vilken mall du vill använda för din modell                                                                                                                                                                                                                                                                                                                                                                       |
| **Mall för nya modeller**                 | Du måste välja vilken mall som ska användas innan du skapar en modell. Vår huvudsakliga mall är Azure Threat Model-mallen som innehåller Azure-/regionsspecifika stenciler, hot och lösningar. För allmänna modeller väljer du SDL TM Knowledge Base på den nedrullningsbara menyn. Vill du skapa en egen mall eller skicka en ny för alla användare? Kolla ut sidan med vår GitHub för **[mall](https://github.com/Microsoft/threat-modeling-templates)** för att lära dig mer                              |
| **Öppna en modell**                            | <p>Öppnar tidigare sparade hot modeller. Funktionen nyligen öppnade modeller är bra om du behöver öppna dina senaste filer. När du hovrar över markeringen hittar du två sätt att öppna modeller:</p><p><ul><li>Öppna från den här datorn – ett klassiskt sätt att öppna en fil med hjälp av lokal lagring</li><li>Öppna från OneDrive – team kan använda mappar i OneDrive för att spara och dela alla sina hot modeller på en enda plats för att öka produktiviteten och samarbetet</li></ul></p> |
| **Kom igång-guide**                   | Öppnar **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** huvud sida                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Mall-avsnitt

| Komponent               | Information                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skapa ny mall** | Öppnar en tom mall som du kan bygga vidare på. Om du inte har omfattande kunskaper om att skapa mallar från grunden, rekommenderar vi att du bygger utifrån befintliga |
| **Öppna mall**       | Öppnar befintliga mallar så att du kan göra ändringar i                                                                                                              |

Threat Modeling Tools teamet arbetar ständigt för att förbättra verktygets funktioner och erfarenhet. Några mindre ändringar kan ske under året, men alla större ändringar kräver omskrivning i guiden. Se ofta till att du får de senaste meddelandena.

## <a name="building-a-model"></a>Skapa en modell

I det här avsnittet följer vi:

- Cristina (en utvecklare)
- Ricardo (en program hanterare) och
- Ashish (en testare)

De går igenom processen med att utveckla sin första hot modell.

> Ricardo: Hi-Cristina, jag arbetade med diagrammet för hot modellen och ville se till att vi fick information direkt. Kan du hjälpa mig att se den?
> Cristina: absolut. Låt oss ta en titt.
> Ricardo öppnar verktyget och delar skärmen med Cristina.

![Basic Threat Model](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, ser enkelt ut, men kan du gå igenom det?
> Ricardo: kontrol lera! Här är en uppdelning:
> - Vår mänskliga användare ritas som en yttre enhet – en fyrkant
> - De skickar kommandon till vår webb server – cirkeln
> - Webb servern konsulterar en databas (två parallella linjer)

Det Ricardo som precis visade Cristina är ett DFD-kort för **[data flödes diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Threat Modeling Tool gör det möjligt för användare att ange förtroende gränser, som anges av de röda streckade linjerna, för att visa var olika entiteter är i kontroll. IT-administratörer behöver till exempel ett Active Directory system för autentisering, så att Active Directory utanför kontrollen.

> Cristina: ser bra ut. Vad är det för hot?
> Ricardo: Låt mig visa dig.

## <a name="analyzing-threats"></a>Analysera hot

När han klickar på vyn analysvy från meny alternativet ikon (fil med förstorings glas), tas han eller hon till en lista över genererade hot som Threat Modeling Tool som finns baserat på standard mal len, som använder SDL-metoden **[kliv (förfalskning, manipulering, avslöjande av information, förskjutning, Denial of service och höjning av privilegier)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Idén är att program varan finns under en förutsägbar uppsättning hot som kan hittas med dessa 6 kategorier.

Den här metoden är som att skydda ditt hus genom att se till att varje dörr och fönster har en låsnings funktion innan du lägger till ett larm system eller jaga efter tjuven.

![Grundläggande hot](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo börjar genom att välja det första objektet i listan. Det här är vad som händer:

Först förbättras interaktionen mellan de två stencilerna

![Skärm bild som visar två stenciler och den böjda pilen ansluter dem på en kraftigare linje.](./media/threat-modeling-tool-getting-started/interaction.png)

För det andra visas ytterligare information om hotet i hotet Fönstret Egenskaper

![Skärm bild som visar hot Fönstret Egenskaper, som innehåller rubrik, kategori, beskrivning, interaktion och prioritet.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Det genererade hotet hjälper honom att förstå potentiella design fel. Med kategoriseringen kliv får han en uppfattning om potentiella angrepps vektorer, medan den ytterligare beskrivningen säger exakt vad som är fel, tillsammans med potentiella sätt att minimera den. Han kan använda redigerbara fält för att skriva anteckningar i motiverings informationen eller ändra prioritets klassificeringar beroende på organisationens fel fält.

Azure-mallar har ytterligare information som hjälper användarna att förstå inte bara vad som är fel, utan också hur du kan åtgärda det genom att lägga till beskrivningar, exempel och hyperlänkar till Azure-baserad dokumentation.

Beskrivningen görs i betydelsen av att lägga till en autentiseringsmekanism för att hindra användare från att bli falska, vilket avslöjar det första hotet att arbeta på. Några minuter i diskussionen med Cristina, de förstår vikten av att implementera åtkomst kontroll och roller. Ricardo har fyllt i några snabba anteckningar för att se till att dessa implementerades.

När Ricardo gick in i hoten under information som utlämnas, hade han en åtkomst kontroll plan som krävde vissa skrivskyddade konton för granskning och rapportgenerering. Han har undrat om detta bör vara ett nytt hot, men lösningarna är desamma, så att han antecknade hotet i enlighet därmed.
Han trodde också att information utlämnar en bit mer och påbörjade att de säkerhetskopierade banden behövde kryptering, ett jobb för drift teamet.

Hot som inte är tillämpliga på designen på grund av befintliga begränsningar eller säkerhets garantier kan ändras till "inte tillämpligt" från List rutan status. Det finns tre andra alternativ: Ej startad – standard val, behöver undersökas – används för att följa upp objekt och åtgärder som har åtgärd ATS – när de har fungerat helt.

## <a name="reports--sharing"></a>Rapporter & delning

När Ricardo går igenom listan med Cristina och lägger till viktiga anteckningar, begränsningar/motivering, prioritets-och status ändringar, väljer han Reports-> skapa fullständig rapport-> Spara rapport, som skriver ut en bra rapport som han kan gå igenom med kollegor för att säkerställa att rätt säkerhets arbete implementeras.

![Skärm bild som visar en representativ Threat Modeling-rapport.](./media/threat-modeling-tool-feature-overview/report.png)

Om Ricardo vill dela filen i stället kan han enkelt göra det genom att spara i organisationens OneDrive-konto. När han gör det kan han kopiera dokument länken och dela den med sina kollegor. 

## <a name="threat-modeling-meetings"></a>Möten för Threat Modeling

När Ricardo skickat sin hot modell till sin kollega med hjälp av OneDrive, Ashish, testaren, var underwhelmed. Verkade som Ricardo och Cristina missade ett fåtal viktiga hörn, vilket kan vara lätt att avslöja. Hans skepticism är ett komplement till hot modeller.

I det här scenariot, efter att Ashish vidtogs över hot modellen, kallades den för två Threat Modeling-möten: ett möte att synkronisera i processen och gå igenom diagrammen och sedan ett andra möte för hot granskning och utloggning.

I det första mötet tillbringade Ashish 10 minuter med alla genom processen för SDL Threat Modeling. Sedan kan han eller hon Hämta diagrammet för hot modellen och börja förklara det i detalj. En viktig komponent som saknas har identifierats inom fem minuter.

Några minuter senare, Ashish och Ricardo fick en utökad diskussion om hur webb servern byggdes. Det var inte det bästa sättet för ett möte att gå vidare, men alla har slutligen enats om att identifiera den avvikelsen tidigt var att spara tid i framtiden.

I det andra mötet har teamet gick genom hoten, diskuterat några sätt att åtgärda dem och signerat av hot modellen. De kontrollerade dokumentet i käll kontrollen och fortsatte med utveckling.

## <a name="thinking-about-assets"></a>Att tänka på till gångar

Vissa läsare som har hot modell kan observera att vi inte har talat om till gångar alls. Vi har upptäckt att många program varu tekniker förstår sin program vara bättre än att förstå begreppet till gångar och vilka till gångar en angripare kan vara intresse rad av.

Om du ska kunna använda hot modellen ett hus kan du börja med att tänka på din familj, oersättliga bilder eller värdefull grafik. Kanske kan du börja med att fundera över vem som kan bryta i och det aktuella säkerhets systemet. Eller så kanske du börjar genom att ta hänsyn till de fysiska funktionerna, t. ex. poolen eller den främre Porch. Detta är detsamma som att tänka på till gångar, angripare eller program design. Någon av dessa tre metoder fungerar.

Metoden för hot modellering som vi har presenterat här är betydligt enklare än vad Microsoft har gjort tidigare. Vi har upptäckt att program utformnings metoden fungerar bra för många team. Vi hoppas att inkludera ditt.

## <a name="next-steps"></a>Nästa steg

Skicka frågor, kommentarer och problem till tmtextsupport@microsoft.com . **[Hämta](https://aka.ms/threatmodelingtool)** Threat Modeling Tool för att komma igång.