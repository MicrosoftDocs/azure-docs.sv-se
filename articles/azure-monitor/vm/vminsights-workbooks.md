---
title: Skapa interaktiva rapporter i Azure Monitor for VMs med arbetsböcker
description: Förenkla komplex rapportering med fördefinierade och anpassade parameterstyrda arbets böcker för Azure Monitor for VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 43cdb9de111bdea5486e49a56d58d38279b685c7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625013"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Skapa interaktiva rapporter i Azure Monitor for VMs med arbetsböcker

Arbets böcker kombinerar text, [logg frågor](/azure/data-explorer/kusto/query/), mått och parametrar till omfattande interaktiva rapporter. Arbetsböcker kan redigeras av andra teammedlemmar som har åtkomst till samma Azure-resurser.

Arbets böcker är användbara för scenarier som:

* Utforska användningen av den virtuella datorn när du inte känner till mått på intresse i förväg: processor användning, disk utrymme, minne, nätverks beroenden osv. Till skillnad från andra verktyg för användnings analys kan du med arbets böcker kombinera flera olika typer av visualiseringar och analyser, vilket gör dem fantastiska för den här typen av kostnads fri utforskning.
* Vi förklarar ditt team hur en nyligen etablerad virtuell dator presterar genom att visa mått för nyckel räknare och andra logg händelser.
* Dela resultatet av ett experiment med att ändra storlek på den virtuella datorn med andra medlemmar i din grupp. Du kan förklara målen för experimentet med text, och sedan Visa alla användnings mått och analys frågor som används för att utvärdera experimentet, tillsammans med tydliga anrop för anrop för om varje mått var över eller under mål.
* Rapporterar effekten av ett avbrott i användningen av din virtuella dator, kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.

I följande tabell sammanfattas de arbets böcker som Azure Monitor for VMs innehåller för att komma igång.

| Arbetsbok | Description | Omfång |
|----------|-------------|-------|
| Prestanda | Innehåller en anpassningsbar version av vår översta N-lista och diagram-vy i en enda arbets bok som utnyttjar alla Log Analytics prestanda räknare som du har aktiverat.| I skala |
| Prestandaräknare | En översta N-diagramvy över en stor uppsättning prestanda räknare. | I skala |
| Anslutningar | Anslutningar är en djupgående vy över inkommande och utgående anslutningar från dina övervakade virtuella datorer. | I skala |
| Aktiva portar | Innehåller en lista över de processer som har bundits till portarna på de övervakade virtuella datorerna och deras aktivitet inom den valda tids ramen. | I skala |
| Öppna portar | Visar antalet portar som är öppna på de övervakade virtuella datorerna och information om de öppna portarna. | I skala |
| Misslyckade anslutningar | Visa antalet misslyckade anslutningar på de övervakade virtuella datorerna, fel trenden och om procent andelen fel ökar med tiden. | I skala |
| Säkerhet och granskning | En analys av TCP/IP-trafik som rapporterar om övergripande anslutningar, skadliga anslutningar, där IP-slutpunkterna finns globalt.  Om du vill aktivera alla funktioner måste du aktivera säkerhets identifiering. | I skala |
| TCP-trafik | En rangordnad rapport för dina övervakade virtuella datorer och deras skickade, mottagna och totala nätverks trafik i ett rutnät och visas som en trend linje. | I skala |
| Jämförelse av trafik | Med den här arbets boken kan du jämföra trender för nätverks trafik för en enskild dator eller en grupp av datorer. | I skala |
| Prestanda | Ger en anpassningsbar version av vår prestanda vy som utnyttjar alla Log Analytics prestanda räknare som du har aktiverat. | Enstaka virtuell dator | 
| Anslutningar | Anslutningar är en djupgående vy över inkommande och utgående anslutningar från den virtuella datorn. | Enstaka virtuell dator |
 
## <a name="creating-a-new-workbook"></a>Skapa en ny arbets bok

En arbets bok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text och inmatade kontroller. Vi börjar med att öppna en mall och gå igenom hur du skapar en anpassad arbets bok för att bättre förstå arbets böcker. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Virtual Machines**.

3. Välj en virtuell dator i listan.

4. På sidan virtuell dator i avsnittet **övervakning** väljer du **insikter**.

5. Välj fliken **prestanda** eller **kartor** på sidan VM Insights och välj sedan **Visa arbets böcker** från länken på sidan. Välj **gå till Galleri** i list rutan.

    ![Skärm bild av list rutan för arbets boken](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Detta startar arbets boks galleriet med ett antal färdiga arbets böcker som hjälper dig att komma igång.

7. Skapa en ny arbets bok genom att välja **ny**.

    ![Skärm bild av arbets boks Galleri](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Redigera avsnitt i arbetsbok

Arbets böcker har två lägen: **redigerings läge** och **läsläge**. När en ny arbets bok startas öppnas den i **redigerings läge**. Det visar allt innehåll i arbets boken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** visar en förenklad vy för rapport format. Med läsläge kan du sammanställa komplexiteten som ingick i att skapa en rapport samtidigt som den underliggande Mechanics bara har några klickningar borta när det behövs för att ändra.

![Skärm bild av avsnittet Virtual Machines arbets bok i Azure Monitor visar en ny arbets bok i redigerings läge med redigerings kontroller markerade.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. När du är klar med redigeringen av ett avsnitt klickar du på **klar redigering** i det nedre vänstra hörnet i avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt klickar du på ikonen **klona det här avsnittet** . Att skapa duplicerade avsnitt är ett bra sätt att iterera på en fråga utan att förlora tidigare iterationer.

3. Om du vill flytta upp ett avsnitt i en arbets bok klickar du på ikonen **Flytta upp** eller **Flytta ned** .

4. Om du vill ta bort ett avsnitt permanent klickar du på ikonen **ta bort** .

## <a name="adding-text-and-markdown-sections"></a>Lägga till text och Markdown-avsnitt

Genom att lägga till rubriker, förklaringar och kommentarer i dina arbets böcker kan du omvandla en uppsättning tabeller och diagram till en rad olika. Text avsnitt i arbets böcker stöder [markdown-syntaxen](https://daringfireball.net/projects/markdown/) för textformatering, t. ex. rubrik, fet, kursiv stil och punkt listor.

Om du vill lägga till ett text avsnitt i din arbets bok använder du knappen **Lägg till text** längst ned i arbets boken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Lägga till frågeavsnitt

![Avsnittet fråga i arbets böcker](media/vminsights-workbooks/005-workbook-query-section.png)

Om du vill lägga till en fråga i din arbets bok använder du knappen **Lägg till fråga** längst ned i arbets boken eller längst ned i ett avsnitt.

Fråge avsnitt är mycket flexibla och kan användas för att besvara frågor som:

* Hur var min processor användning under samma tids period som en ökning av nätverks trafiken?
* Vad var trenden i tillgängligt disk utrymme under den senaste månaden?
* Hur många nätverks anslutnings fel har min VM-upplevelse under de senaste två veckorna? 

Du är inte bara begränsad till att fråga från kontexten för den virtuella dator som du startade arbets boken från. Du kan fråga över flera virtuella datorer, samt Log Analytics arbets ytor, så länge du har åtkomst behörighet till dessa resurser.

Ta med data från andra Log Analytics arbets ytor eller från en speciell Application Insights-app med hjälp av **arbetsyte** -ID. Mer information om frågor över flera resurser finns i den [officiella vägledningen](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Avancerade inställningar för analys fråga

Varje avsnitt har sina egna avancerade inställningar, som är tillgängliga via avsnittet Inställningar ![ arbets boks redigerings ](media/vminsights-workbooks/006-settings.png) ikon som finns till höger om knappen **Lägg till parametrar** .

![Skärm bild av dialog rutan Avancerade inställningar i avsnittet Virtual Machines arbets bok i Azure Monitor. Ikonen som öppnar dialog rutan är markerad.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Anpassad bredd**    | Gör ett objekt till en godtycklig storlek, så att du kan passa många objekt på en enda rad så att du bättre kan organisera dina diagram och tabeller i interaktiva interaktiva rapporter.  |
| **Villkorligt synlig** | Ange om du vill dölja steg baserat på en parameter i läsläge. |
| **Exportera en parameter**| Tillåt en markerad rad i rutnätet eller diagrammet för att få senare steg att ändra värden eller bli synlig.  |
| **Visa fråga när du inte redigerar** | Visar frågan ovanför diagrammet eller tabellen även i läsläge.
| **Visa knappen Öppna i Analytics när du inte redigerar** | Lägger till den blå analys ikonen i det högra hörnet i diagrammet för att tillåta åtkomst till ett klick.|

De flesta av dessa inställningar är ganska intuitiva, men för att förstå **export av en parameter** är det bättre att granska en arbets bok som använder den här funktionen.

En av de färdiga arbets böckerna – **TCP-trafik** ger information om anslutnings mått från en virtuell dator.

Det första avsnittet i arbets boken baseras på data från logg frågor. Det andra avsnittet baseras också på data från loggdata, men om du markerar en rad i den första tabellen uppdateras diagrammets innehåll interaktivt:

![Skärm bild av avsnittet Virtual Machines i Azure Monitor visar en fördefinierad TCP-trafik för arbets boken.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Beteendet är möjligt genom att använda **när ett objekt har valts, exportera en parameter** avancerade inställningar, som är aktiverade i tabellens logg fråga.

![Skärm bild av dialog rutan Avancerade inställningar för en Virtual Machines arbets bok med alternativet "Exportera en parameter" när ett objekt har marker ATS.](media/vminsights-workbooks/009-settings-export.png)

Den andra logg frågan använder sedan de exporterade värdena när en rad väljs för att skapa en uppsättning värden som sedan används av avsnittets rubrik och diagram. Om ingen rad har marker ATS döljs avsnitts rubriken och diagram. 

Den dolda parametern i det andra avsnittet använder till exempel följande referens från den rad som valts i rutnätet:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Lägga till måttavsnitt

Mått avsnitt ger dig fullständig åtkomst till att införliva Azure Monitor Mät data i dina interaktiva rapporter. I Azure Monitor for VMs innehåller de färdiga arbets böckerna vanligt vis analytiska frågedata i stället för Mät data.  Du kan välja att skapa arbets böcker med mät data, så att du kan dra full nytta av det bästa av båda funktionerna på ett och samma ställe. Du kan också hämta mått data från resurser i någon av de prenumerationer som du har åtkomst till.

Här är ett exempel på data för virtuella datorer som hämtas till en arbets bok för att tillhandahålla en rutnäts visualisering av processor prestanda:

![Skärm bild av avsnittet mått i en virtuell dator arbets bok i Azure Monitor. CPU-prestanda för varje virtuell dator visas grafiskt.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Lägga till parameteravsnitt

Med arbets boks parametrar kan du ändra värden i arbets boken utan att behöva redigera fråge-eller text avsnitten manuellt. Detta tar bort behovet av att förstå det underliggande Analytics-frågespråket och utökar den potentiella mål gruppen för arbets boksbaserade rapporter.

Värdena för parametrarna ersätts i frågor, text eller andra parameter avsnitt genom att ange namnet på parametern inom klammerparenteser, t. ex ``{parameterName}`` .. Parameter namn är begränsade till liknande regler som JavaScript-identifierare, alfabetiska tecken eller under streck, följt av alfanumeriska tecken eller under streck. Till exempel tillåts **a1** , men **1a** tillåts inte.

Parametrar är linjära, med början från början av en arbets bok och flödar ned till senare steg.  Parametrar som deklarerats senare i en arbets bok kan åsidosätta parametrar som har deklarerats tidigare. Detta gör det också möjligt att använda parametrar som använder frågor för att komma åt värdena från parametrarna som definierats tidigare. I själva steget för en parameter är parametrarna också linjära, vänster till höger, där parametrar till höger kan vara beroende av en parameter som har deklarerats tidigare i samma steg.
 
Det finns fyra olika typer av parametrar som stöds för närvarande:

|                  |      |
| ---------------- |:-----|
| **Text**    | Gör att användaren kan redigera en text ruta och du kan också ange en fråga för att fylla i standardvärdet. |
| **Listmeny** | Tillåter användaren att välja från en uppsättning värden. |
| **Intervall väljare**| Gör att användaren kan välja från en fördefinierad uppsättning tidsintervalls värden eller välja från ett anpassat tidsintervall.|
| **Resurs väljare** | Gör att användaren kan välja bland de resurser som har valts för arbets boken.|

### <a name="using-a-text-parameter"></a>Använda en text parameter

Värdet som en användare skriver i text rutan ersätts direkt i frågan, utan några undantag eller citat tecken. Om värdet du behöver är en sträng ska frågan ha citat tecken runt parametern (t. ex. **{parameter}**).

Med text parametern kan du använda värdet i en text ruta var som helst. Det kan vara ett tabell namn, ett kolumn namn, ett funktions namn, en operator osv.  Text parameter typen har inställningen **Hämta standardvärde från Analytics-frågan**, vilket gör att arbets bokens författare kan använda en fråga för att fylla i standardvärdet för text rutan.

När du använder standardvärdet från en logg fråga används bara det första värdet på den första raden (rad 0, kolumn 0) som standardvärdet. Vi rekommenderar därför att du begränsar frågan så att den returnerar bara en rad och en kolumn. Andra data som returneras av frågan ignoreras. 

Vilket värde som frågan returnerar ersätts direkt utan att det visas några undantag eller citat tecken. Om frågan inte returnerar några rader är resultatet av parametern antingen en tom sträng (om parametern inte krävs) eller odefinierad (om parametern krävs).

### <a name="using-a-drop-down"></a>Använda en listruta

Med den nedrullningsbara parameter typen kan du skapa en List Rute kontroll, så att du kan välja ett eller flera värden.

List rutan fylls i av en logg fråga eller JSON. Om frågan returnerar en kolumn, är värdena i den kolumnen både värdet och etiketten i den nedrullningsbara kontrollen. Om frågan returnerar två kolumner är den första kolumnen värdet och den andra kolumnen är den etikett som visas i list rutan. Om frågan returnerar tre kolumner används den tredje kolumnen för att ange standard valet i list rutan. Den här kolumnen kan vara vilken typ som helst, men det enklaste är att använda bool-eller numeric-typer, där 0 är falskt och 1 är sant.

Om kolumnen är en sträng typ anses null/tom sträng vara falskt och andra värden betraktas som sanna. För List rutor med enstaka val används det första värdet med ett sant värde som standard val.  För List rutor med flera val används alla värden med ett sant värde som standard vald uppsättning. Objekten i list rutan visas i vilken ordning frågan returnerade rader. 

Nu ska vi titta på de parametrar som finns i rapporten Översikt över anslutningar. Klicka på Redigera-symbolen bredvid **riktning**.

![Skärm bild av avsnittet för att lägga till och redigera rapport parametrar i Azure Monitor. Redigerings ikonen för parametern Direction är markerad.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Då startas meny alternativet **Redigera parameter** .

![Skärm bild av dialog rutan Redigera parameter. Parameter namnet är riktningen, parameter typen är List rutan och hämta data från JSON är markerat.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Med JSON kan du generera en godtycklig tabell som är ifylld med innehåll. Följande JSON genererar till exempel två värden i list rutan:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Ett mer tillämpligt exempel är att använda en listruta för att välja från en uppsättning prestanda räknare efter namn:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Frågan visar resultat enligt följande:

![Listruta för prestanda räknare](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

List rutor är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Intervall parametrar

Även om du kan skapa en egen anpassad tids intervall parameter via den nedrullningsbara parameter typen, kan du också använda parameter typen out-of-box för tidsintervallen om du inte behöver samma grad av flexibilitet. 

Parameter typer för tidsintervall har 15 standard intervall som går från fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta val av anpassat tidsintervall, vilket gör att rapportens operatör kan välja explicita start-och stopp värden för tidsintervallet.

### <a name="resource-picker"></a>Resurs väljare

Parameter typen resurs väljare ger dig möjlighet att begränsa rapporten till vissa typer av resurser. Ett exempel på en fördefinierad arbets bok som utnyttjar resurs väljar typen är arbets boken **prestanda** .

![Listruta för arbets ytor](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbets böcker sparas i en Log Analytics arbets yta eller en virtuell dator resurs, beroende på hur du kommer åt galleriet för arbets böcker. Arbets boken kan sparas i **Mina rapporter** avsnittet som är privat för dig eller i avsnittet **delade rapporter** som är tillgänglig för alla som har åtkomst till resursen. Om du vill visa alla arbets böcker i resursen klickar du på knappen **Öppna** i åtgärds fältet.

Så här delar du en arbets bok som för närvarande finns i **Mina rapporter**:

1. Klicka på **Öppna** i åtgärds fältet
2. Klicka på "..." bredvid den arbets bok som du vill dela
3. Klicka på **Flytta till delade rapporter**.

Om du vill dela en arbets bok med en länk eller via e-post, klickar du på **dela** i åtgärds fältet. Tänk på att mottagare av länken behöver åtkomst till den här resursen i Azure Portal för att kunna visa arbets boken. För att göra redigeringar behöver mottagarna minst deltagar behörigheter för resursen.

Fästa en länk till en arbets bok på en Azure-instrumentpanel:

1. Klicka på **Öppna** i åtgärds fältet
2. Klicka på "..." bredvid den arbets bok som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="next-steps"></a>Nästa steg

- Information om hur du identifierar begränsningar och övergripande prestanda för virtuella datorer finns i [Visa prestanda](vminsights-performance.md)för virtuella Azure-datorer.

- Information om identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).