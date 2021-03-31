---
title: Jakt funktioner i Azure Sentinel | Microsoft Docs
description: Använd Azure Sentinels inbyggda jakt frågor som hjälper dig att ställa rätt frågor för att hitta problem i dina data.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576765"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Söker efter hot med Azure Sentinel

Om du är en utredare som vill vara proaktiv om att söka efter säkerhetshot, kan du söka efter säkerhetshot i Azure Sentinel i din organisations data källor. Men dina system-och säkerhets apparater genererar berg data som kan vara svåra att parsa och filtrera i meningsfulla händelser. För att hjälpa säkerhetsanalytikerna att titta proaktivt för nya avvikelser som inte identifierats av dina säkerhetsappar, kan du med Azure Sentinel-de inbyggda jakt frågorna ställa rätt frågor till att hitta problem i de data som du redan har i nätverket. 

En inbyggd fråga ger till exempel information om de vanligaste processerna som körs i din infrastruktur – du vill inte ha en avisering om varje gång de körs, men de kan vara helt innocentde, men du kanske vill ta en titt på frågan om det finns något som är ovanligt. 



Med Azure Sentinel-jakt kan du dra nytta av följande funktioner:

- Inbyggda frågor: för att komma igång innehåller en start sida förinstallerade fråge exempel utformade för att komma igång och bekanta dig med tabellerna och frågespråket. Dessa inbyggda jakt frågor utvecklas av Microsofts säkerhets forskare på en kontinuerlig basis, nya frågor och fin justering av befintliga frågor för att ge dig en start punkt för att söka efter nya identifieringar och ta reda på var du ska starta jakt efter nya attacker. 

- Kraftfullt frågespråk med IntelliSense: byggt ovanpå ett frågespråk som ger dig den flexibilitet du behöver för att ta jakt till nästa nivå.

- Skapa egna bok märken: under jakt processen kan du komma över matchningar eller resultat, instrument paneler eller aktiviteter som ser ovanliga eller misstänkta. Använd bok märkes funktionen för att markera dessa objekt så att du kan komma tillbaka till dem i framtiden. Med bok märken kan du Spara poster för senare, som ska användas för att skapa en incident för undersökning. Mer information om bok märken finns [i använda bok märken i jakt](hunting.md).
- Använd antecknings böcker för att automatisera undersökningen: bärbara datorer är till exempel steg-för-steg-spel böcker som du kan bygga för att gå igenom stegen i en undersökning och söka.  Antecknings böcker kapslar in alla jakt steg i en återanvändbar Spelbok som kan delas med andra i din organisation. 
- Fråga lagrade data: data är tillgängliga i tabeller som du kan fråga efter. Du kan till exempel skapa en fråga om process skapande, DNS-händelser och många andra händelse typer.

- Länkar till community: utnyttja kraften i den större communityn för att hitta fler frågor och data källor.
 
## <a name="get-started-hunting"></a>Kom igång med jakt

1. Klicka på **jakt** i Azure Sentinel-portalen.
  ![Azure Sentinel startar jakt](media/tutorial-hunting/hunting-start.png)

2. När du öppnar sidan **jakt** visas alla jakt frågor i en enda tabell. I tabellen visas alla frågor som har skrivits av Microsofts team med säkerhetsanalytiker och ytterligare frågor som du har skapat eller ändrat. Varje fråga innehåller en beskrivning av vad den söker efter och vilken typ av data som den körs på. De här mallarna grupperas efter deras olika taktiker – ikonerna till höger klassificerar typ av hot, till exempel initial åtkomst, persistence och exfiltrering. Du kan filtrera dessa jakt-fråge mallar med något av fälten. Du kan spara alla frågor till dina favoriter. Genom att spara en fråga till dina favoriter körs frågan automatiskt varje gång **jakt** sidan nås. Du kan skapa en egen jakt fråga eller klona och anpassa en befintlig jakt-frågegrupp. 
 
2. Klicka på **Kör fråga** på informations sidan för jakt för att köra en fråga utan att lämna sidan jakt.  Antalet matchningar visas i tabellen. Granska listan över jakt frågor och deras matchningar. Kolla in vilket stadium i Kill-kedjan som matchningen är associerad med.

3. Utför en snabb genom gång av den underliggande frågan i rutan Frågedetaljer eller klicka på **Visa frågeresultat** för att öppna frågan i Log Analytics. Granska matchningarna för frågan längst ned.

4.    Klicka på raden och välj **Lägg till bok märke** för att lägga till de rader som ska undersökas – du kan göra detta för allt som ser misstänkt ut. 

5. Gå sedan tillbaka till sidan för huvud **jakt** och klicka på fliken **bok märken** för att se alla misstänkta aktiviteter. 

6. Välj ett bok märke och klicka sedan på **Undersök** för att öppna gransknings upplevelsen. Du kan filtrera bok märkena. Om du till exempel undersöker en kampanj kan du skapa en tagg för kampanjen och sedan filtrera alla bok märken baserat på kampanjen.

1. När du har identifierat vilken jakt fråga som ger hög värdes insikter om eventuella attacker kan du också skapa anpassade identifierings regler baserat på din fråga och få fram de insikter som aviseringar till dina säkerhets incident svarare.

 

## <a name="query-language"></a>Frågespråk 

Jakt i Azure Sentinel baseras på Kusto frågespråk. Mer information om frågespråket och operatörer som stöds finns i [frågespråk](../azure-monitor/logs/get-started-queries.md).

## <a name="public-hunting-query-github-repository"></a>Offentlig jakt fråga GitHub-lagringsplats

Kolla in [jakt-lagringsplatsen](https://github.com/Azure/Orion). Delta och Använd exempel frågor som delas av våra kunder.

 

## <a name="sample-query"></a>Exempelfråga

En typisk fråga börjar med ett tabell namn följt av en serie operatorer avgränsade med \| .

I exemplet ovan börjar du med tabell namnet SecurityEvent och lägger till skickas-element vid behov.

1. Definiera ett tids filter om du bara vill granska poster från de senaste sju dagarna.

2. Lägg till ett filter i frågan för att endast visa händelse-ID 4688.

3. Lägg till ett filter i frågan på kommando raden för att endast innehålla instanser av cscript.exe.

4. Projicera bara de kolumner som du är intresse rad av att utforska och begränsa resultatet till 1000 och klicka på **Kör fråga**.
5. Klicka på den gröna triangeln och kör frågan. Du kan testa frågan och köra den för att söka efter avvikande beteende.

## <a name="useful-operators"></a>Användbara operatorer

Frågespråket är kraftfullt och har många tillgängliga operatorer, men vissa användbara operatörer visas här:

**WHERE** – filtrera en tabell till delmängd av rader som uppfyller ett predikat.

**sammanfatta** – skapa en tabell som sammanställer innehållet i Indatakällan.

**Sammanfoga rader** med två tabeller för att skapa en ny tabell genom att matcha värdena för de angivna kolumnerna från varje tabell.

**Count** – returnera antalet poster i den angivna Indataposten.

 returnera de första N posterna sorterade efter de angivna kolumnerna.

**Limit** -returnera upp till det angivna antalet rader.

**projekt** – Markera de kolumner som du vill inkludera, byta namn på eller släppa och infoga nya beräknade kolumner.

**utöka** – skapa beräknade kolumner och Lägg till dem i resultat uppsättningen.

**makeset** – returnera en dynamisk (JSON) matris av uppsättningen distinkta värden som uttryck tar i gruppen

**hitta** – Hitta rader som matchar ett predikat i en uppsättning tabeller.

## <a name="save-a-query"></a>Spara en fråga

Du kan skapa eller ändra en fråga och spara den som en egen fråga eller dela den med användare som tillhör samma klient organisation.

   ![Spara fråga](./media/tutorial-hunting/save-query.png)

Skapa en ny jakt fråga:

1. Klicka på **ny fråga** och välj **Spara**.
2. Fyll i alla tomma fält och välj **Spara**.

   ![Ny fråga](./media/tutorial-hunting/new-query.png)

Klona och ändra en befintlig jakt fråga:

1. Välj frågan jakt i den tabell som du vill ändra.
2. Välj ellipsen (...) på raden i frågan som du vill ändra och välj **klonings fråga**.

   ![klona fråga](./media/tutorial-hunting/clone-query.png)
 

3. Ändra frågan och välj **skapa**.

   ![anpassad fråga](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kör en jakt undersökning med Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:


- [Använd antecknings böcker för att köra automatiserade jakt kampanjer](notebooks.md)
- [Använd bok märken för att spara intressant information under jakt](bookmarks.md)