---
title: Azure Monitor arbets boks grupper
description: Hur du använder grupper i Azure Monitor arbets böcker.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b36dec79e5d60d97c11b9f8c74790527e1bd19fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731365"
---
# <a name="how-to-use-groups-in-workbooks"></a>Använda grupper i arbets böcker

Med ett grupp objekt i en arbets bok kan du logiskt gruppera en uppsättning steg i en arbets bok.

Grupper i arbets böcker är användbara för flera saker:

- Layout
  - I scenarier där du vill att objekt ska ordnas lodrätt kan du skapa en grupp med objekt som alla staplar upp och ange att gruppens format ska vara en procents ATS bredd i stället för att ange procent bredden för alla enskilda objekt.
- Synlighet
  - I scenarier där du vill att många objekt ska dölja/visas tillsammans kan du ange synligheten för hela gruppen med objekt, i stället för att ställa in Synlighets inställningar för varje enskilt objekt. Detta kan vara användbart i mallar som använder flikar, eftersom du kan använda en grupp som innehåll på fliken, och hela gruppen kan döljas/visas baserat på en parameter som angetts av den valda fliken.
- Prestanda
  - I de fall där du har en mycket stor mall med många avsnitt eller flikar kan du konvertera varje avsnitt till en egen underordnad mall och använda grupper för att läsa in alla undermallar i mallen på den översta nivån. Innehållet i undermallarna läses inte in eller körs förrän användaren gör grupperna synliga. Lär dig mer om [hur du delar upp en stor mall i många mallar](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Använda grupper

Om du vill lägga till en grupp i din arbets bok väljer du *Lägg till* och sedan *Lägg till grupp*.

![Välj Lägg till och sedan grupp.](./media/workbooks-groups/add-group.png)

Nedan visas en skärm bild av en grupp i läsläge med två objekt i: ett text objekt och ett frågespråk.  

![Grupp i läsläge.](./media/workbooks-groups/groups-view.png)

När du redigerar arbets boken kan du se att dessa två objekt i själva verket är inuti ett grupp objekt:

![Grupp i redigerings läge. ](./media/workbooks-groups/groups-edit.png)

I skärm bilden ovan är gruppen i redigerings läge, som visar att den innehåller två objekt (inuti det streckade ytan). Varje steg kan vara i redigerings-eller läs läge, oberoende av varandra. Till exempel är text steget i redigerings läge medan Frågeuttrycket är i Läs läge.

## <a name="scoping"></a>Omfångsdefinierande

För närvarande behandlas en grupp som en ny omfattning i arbets boken. Alla parametrar som skapas i gruppen syns bara inuti gruppen. Detta gäller även för sammanfogning, men du kan bara se data i gruppen eller på den överordnade nivån.

## <a name="group-types"></a>Grupp typer

Med objektet arbets grupps grupp kan du lägga till en grupp med objekt i en arbets bok. Som författare till en arbets bok anger du vilken typ av grupp den ska vara. Det finns två typer av grupper:

- Editable
  - Med gruppen i arbets boken kan du lägga till, ta bort eller redigera innehållet i objekten i gruppen. Detta används vanligt vis för layout-och Synlighets syfte.
- Från mall
  - Gruppen i arbets boken hämtas från innehållet i en annan mall efter dess ID. Innehållet i mallen läses in och sammanfogas i arbets boken vid körning. I redigerings läge kan du inte ändra något av innehållet i gruppen eftersom de bara läses in igen från mallen nästa gång objektet läses in.  

## <a name="load-types"></a>Inläsnings typer

Det finns flera olika sätt som innehållet i en grupp kan läsas in på. Som författare till en arbets bok får du ange när och hur innehållet i en grupp ska läsas in.

### <a name="lazy-the-default"></a>Lazy (standard)

Gruppen kommer bara att läsas in när objektet visas. Detta gör att en grupp kan användas av flik-objekt. Om du aldrig väljer fliken blir gruppen aldrig synlig, vilket innebär att innehållet inte läses in.

För grupper som skapats från en mall hämtas inte innehållet i mallen och objekten i gruppen skapas inte förrän gruppen blir synlig. Användaren ser förlopps rotations rutor för hela gruppen när innehållet hämtas.

### <a name="explicit"></a>Explicit

I det här läget visas en knapp där gruppen skulle vara, och inget innehåll hämtas eller skapas förrän användaren klickar på knappen för att läsa in innehållet. Detta är användbart i scenarier där innehållet kan vara dyrt att beräkna eller använda sällan. Författaren kan ange vilken text som ska visas på knappen.

Nedan visas en skärm bild av explicita inläsnings inställningar som visar en konfigurerad "Läs mer"-knapp.

![Explicita inläsnings inställningar](./media/workbooks-groups/groups-explicitly-loaded.png)

Gruppen innan den läses in i arbets boken:

![Explicit grupp innan den läses in i arbets boken](./media/workbooks-groups/groups-explicitly-loaded-before.png)

Gruppen efter att ha lästs in i arbets boken:

![Explicit grupp efter att ha lästs in i en arbets bok](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Always

I det här läget läses innehållet i gruppen alltid in och skapas så snart arbets boken läses in. Detta används oftast när du använder en grupp endast för layout, där innehållet alltid visas.

## <a name="using-templates-inside-a-group"></a>Använda mallar i en grupp

När en grupp har kon figurer ATS för att läsas in från en mall läses innehållet in Lazy som standard och läses bara in när gruppen är synlig.

När en mall läses in i en grupp försöker arbets boken sammanfoga alla parametrar som har deklarerats i mallen som läses in med parametrar som redan finns i gruppen. Alla parametrar som redan finns i arbets boken med identiska namn kommer att sammanfogas från den mall som läses in. Om alla parametrar i ett parameter steg sammanfogas, försvinner hela parameter steget.

### <a name="example-1-all-parameters-have-identical-names"></a>Exempel 1: alla parametrar har identiska namn

Överväg en mall som har två parametrar överst.

- `TimeRange` – en tids intervalls parameter.
- `Filter` – en text parameter.

![Redigera parameter objekt: "Top-Level-parametrar"](./media/workbooks-groups/groups-top-level-params.png)

Sedan läser ett grupp objekt en andra mall som har sina egna två parametrar och ett text steg, där parametrarna får samma namn:

![Redigera ett parameter objekt för en andra mall](./media/workbooks-groups/groups-merged-away.png)

När den andra mallen läses in i gruppen sammanfogas de duplicerade parametrarna. Eftersom alla parametrar sammanfogas, sammanfogas även det inre parameter steget ut, vilket resulterar i att gruppen bara innehåller text steget.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Exempel 2: en parameter har ett identiskt namn

Överväg en grupps mall som har två parametrar överst.

- `TimeRange` – en tids intervalls parameter.
- `FilterB` – en text parameter, Observera att den inte `Filter` liknar den översta mallen.

![Redigera ett grupp objekt med resultatet av parametrar som kopplas bort](./media/workbooks-groups/groups-wont-merge-away.png)

När gruppens item's-mall läses in, `TimeRange` kommer parametern att slås samman från gruppen. Sedan kommer arbets boken att ha de första parametrarna steg för `TimeRange` och `Filter` , och gruppens parameter steg omfattar bara `FilterB`

![resultat av parametrar som inte ska sammanfogas](./media/workbooks-groups/groups-wont-merge-away-result.png)

Om den inlästa mallen hade funnits `TimeRange` och `Filter` (i stället för `FilterB` ), skulle den resulterande arbets boken ha ett parameter steg och en grupp med endast det text steg som återstår.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Så här delar du upp en stor mall i flera mallar

För att förbättra prestandan, är det fördelaktigt att dela upp en stor mall i flera mindre mallar som läser in visst innehåll i Lazy eller på begäran av användaren. Detta gör den första inläsningen snabbare eftersom mallen på den översta nivån kan vara mycket mindre.

När du delar upp en mall i delar behöver du effektivt dela upp mallen i flera mallar (undermallar) som alla arbetar var för sig. Så om den översta mallen har en `TimeRange` parameter som andra steg använder, behöver undermallen även ha ett parameter steg som definierar en parameter med det exakta namnet. Detta gör att undermallarna fungerar separat och låter dem läsas in i större mallar i grupper.

Så här omvandlar du en större mall till flera undermallar:

1.  Skapa en ny tom grupp längst upp i arbets boken, efter de delade parametrarna. Den nya gruppen blir till sist en underordnad mall.
2. Skapa en kopia av steget delade parametrar och Använd sedan *Flytta till grupp* för att flytta kopian till den grupp som skapades i steg 1. Med det här parameter steget kan undermallen fungera oberoende av den yttre mallen och kopplas samman när den läses in i den yttre mallen.
    > [!NOTE]
    > Undermallen behöver inte vara tekniskt tvungen att ha de här parametrarna som sammanslagna om du inte vill att de underordnade mallarna ska synas. Det innebär dock att det blir mycket svårt att redigera eller felsöka om du behöver göra det senare.

3. Flytta varje objekt i arbets boken som ska finnas i undermallen till den grupp som skapades i steg 1.
4. Om de enskilda stegen som flyttades i steg 3 hade villkorlig synlighet, blir det synligt för den yttre gruppen (som används i flikar). Ta bort dem från objekten i gruppen och Lägg till den Synlighets inställningen i själva gruppen. Spara här för att undvika att förlora ändringar och/eller exportera och spara en kopia av JSON-innehållet.
5. Om du vill att gruppen ska läsas in från en mall kan du använda verktygs knappen *Redigera* i gruppen. Då öppnas bara innehållet i den gruppen som en arbets bok i ett nytt fönster. Du kan sedan spara den efter behov och stänga vyn arbets bok (Stäng inte webbläsaren, bara den vyn för att gå tillbaka till föregående arbets bok som du redigerade).
6. Du kan sedan ändra det grupp steg som ska läsas in från mallen och ange fältet Mall-ID till den arbets bok/mall som du skapade i steg 5. För att arbeta med arbets boks-ID: n måste källan vara ett delat arbets boks resurs-ID. Tryck på *load* och innehållet i gruppen kommer nu att läsas in från den under mallen i stället för att sparas i den här yttre arbets boken.

## <a name="next-steps"></a>Nästa steg
- [Översikt över arbets böcker](./workbooks-overview.md)
- [Använda JSONPath med arbets böcker](workbooks-jsonpath.md)