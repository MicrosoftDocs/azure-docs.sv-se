---
title: Felsöka Azure Monitor Metric-diagram
description: Felsöka problem med att skapa, anpassa eller tolka mått diagram
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 7c3af0865282475ded0172d18aecad1dfb61721b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814269"
---
# <a name="troubleshooting-metrics-charts"></a>Felsöka måttdiagram

Använd den här artikeln om du stöter på problem med att skapa, anpassa eller tolka diagram i Azure Metrics Explorer. Om du är nybörjare på måtten kan du läsa mer om att [komma igång med Metrics Explorer](metrics-getting-started.md) och [avancerade funktioner i Metrics Explorer](metrics-charts.md). Du kan också se [exempel](metric-chart-samples.md) på de konfigurerade mått diagram.

## <a name="chart-shows-no-data"></a>Diagrammet visar inga data

Ibland kanske diagrammen inte visar några data när du har valt rätt resurser och mått. Detta kan bero på flera av följande orsaker:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights-resursprovidern är inte registrerad för din prenumeration

*Microsoft.Insights*-resursprovidern måste vara registrerad för din prenumeration för att du ska kunna utforska mått. I många fall registreras den automatiskt (det vill säga när du har konfigurerat en aviseringsregel, anpassat diagnostikinställningar för resurser eller konfigurerat en regel för automatisk skalning). Om Microsoft. Insights-resurs leverantören inte är registrerad, måste du registrera den manuellt genom att följa stegen som beskrivs i [Azures resurs leverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md).

**Lösning:** Öppna **prenumerationer**, fliken **Resource providers** och kontrol lera att *Microsoft. Insights* har registrerats för din prenumeration.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Du har inte tillräckliga åtkomsträttigheter för resursen

I Azure kontrol leras åtkomsten till mått av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Du måste vara medlem i någon av grupperna [Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Övervakningsdeltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor), eller [Deltagare](../../role-based-access-control/built-in-roles.md#contributor) för att kunna utforska mått för resurser.

**Lösning:** Kontrol lera att du har behörighet för den resurs som du utforskar måtten från.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Resursen genererade inga mått under det valda tidsintervallet

Vissa resurser genererar inte mått regelbundet. Exempelvis samlar inte Azure in mått för stoppade virtuella datorer. Andra resurser kanske bara genererar mått vid särskilda händelser. Till exempel kräver ett mått som visar bearbetningstiden för en transaktion att minst en transaktion skett. Om det inte finns några transaktioner i det valda tidsintervallet, kommer diagrammet givetvis att vara tomt. Och även om de flesta måtten i Azure samlas in varje minut, finns det några som samlas in mer sällan. I dokumentationen för mått finns mer information om det mått som du vill utforska.

**Lösning:** Ändra tiden för diagrammet till ett bredare intervall. Du kan börja med de senaste 30 dagarna genom att använda en större tids kornig het (eller förlita dig på alternativet "automatisk tids kornig het").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Du har valt ett tidsintervall som är längre än 30 dagar

[De flesta mått i Azure lagras i 93 dagar](data-platform-metrics.md#retention-of-metrics). Dock kan du högst fråga efter data för 30 dagar i ett enskilt diagram. Den här begränsningen gäller inte för [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Lösning:** Om du ser ett tomt diagram eller om diagrammet bara visar en del av mått data, kontrollerar du att skillnaden mellan start-och slutdatumen i tids väljaren inte överskrider intervallet med 30 dagar.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Alla mått låg utanför det låsta y-axelintervallet

Genom att [låsa gränserna för y-axeln i diagrammet](metrics-charts.md#lock-boundaries-of-chart-y-axis) kan du oavsiktligt göra så att diagramlinjen inte visas i diagrammets visningsområdet. Om y-axeln exempelvis är låst till ett intervall mellan 0 % och 50 % och måttet har ett konstant värde på 100 %, återges linjen alltid utanför det synliga området, vilket gör att diagrammet verkar vara tomt.

**Lösning:** Kontrol lera att diagrammets y-Axels gränser inte är låsta utanför intervallet för mått värden. Om gränserna för y-axeln är låsta kan du tillfälligt återställa dem så att måttvärdena inte faller utanför diagramintervallet. Vi avråder dig från att låsa y-axelintervallet med automatisk kornighet för diagram med aggregeringar som **summa**, **min** och **max** eftersom deras värden ändras med kornigheten när webbläsarfönstret eller skärmupplösningen ändras. Om du ändrar kornigheten kan det göra att du inte ser något i visningsområdet i diagrammet.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Du tittar på ett gäst operativ system mått men har inte aktiverat Azure Diagnostic-tillägget

Insamling av mått för **gästoperativsystem** kräver att Azure Diagnostics-tillägget konfigureras eller aktiveras via panelen **Diagnostikinställningar** för din resurs.

**Lösning:** Om Azure-diagnostik tillägget är aktiverat men du fortfarande inte kan se dina mått, följer du stegen som beskrivs i [fel söknings guiden för Azure-diagnostik-tillägg](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Se även fel söknings stegen för [det går inte att välja namn område och mått för gäst operativ system](#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Meddelandet "fel vid hämtning av data" på instrument panelen

Det här problemet kan inträffa när instrumentpanelen har skapats med ett mått som senare blivit inaktuellt och tagits bort från Azure. Kontrol lera att det är fallet genom att öppna fliken **mått** i resursen och kontrol lera tillgängliga mått i mått väljaren. Om måttet inte visas, betyder det att det har tagits bort från Azure. När ett mätvärde har blivit inaktuellt och tagits bort finns det normalt ett nytt bättre mått som ger liknande information om resurshälsan.

**Lösning:** Uppdatera panelen för att återställa genom att välja ett alternativt mått för diagrammet på instrument panelen. Du kan [granska en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>En streckad linje visas i diagrammet

Azure Metrics-diagram använder streckat linje format för att indikera att det finns ett saknat värde (även kallat "null-värde") mellan två kända tids kornig data punkter. Om du till exempel har valt tids kornig het på 1 minut men måttet rapporterades som 07:26, 07:27, 07:29 och 07:30 (Observera ett minut intervall mellan andra och tredje data punkter), ansluter en streckad linje till 07:27 och 07:29 och en heldragen linje ansluter alla andra data punkter. Den streckade linjen hamnar nedåt till noll när måttet använder **Count** och **Sum** -aggregering. För de **AVG**-, **min** -eller **Max** agg regeringar ansluter den streckade linjen två nära kända data punkter. Och om data saknas längst till höger eller längst till vänster i diagrammet expanderas den streckade linjen i riktningen mot datapunkten som saknas.
  ![Skärm bild som visar hur när data saknas på den högra eller vänstra sidan av diagrammet, expanderas den streckade linjen till riktningen för den saknade data punkten.](./media/metrics-troubleshoot/dashed-line.png)

**Lösning:** Det här beteendet är avsiktligt. Detta är användbart för att identifiera datapunkter som saknas. Linje diagrammet är ett överlägset alternativ för att visualisera trender i mått med hög densitet, men det kan vara svårt att tolka Mät värdena med null-optimerade värden, särskilt när samrelaterade värden med tids kornig het är viktigt. Den streckade linjen gör det enklare att läsa dessa diagram, men om diagrammet fortfarande är svårtolkat bör du överväga att visa måtten i en annan typ av diagram. Ett spridt ritat diagram för samma mått visar tydligt varje tids kornig het genom att bara visualisera en punkt när det finns ett värde och hoppa över data punkten när värdet saknas: ![ skärm bild som markerar meny alternativet punkt diagram.](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > Om du ändå föredrar ett linjediagram för dina mått kan det vara enklare att utvärdera tidskornigheten genom att föra musen över diagrammet så att datapunkten under muspekaren markeras.

## <a name="chart-shows-unexpected-drop-in-values"></a>Diagrammet visar ett oväntat värdefall

Detta är ofta en feltolkning av diagrammets data. Summor eller antal kan falla när de senaste minuterna visas i diagrammet eftersom Azure inte har fått eller hunnit bearbeta de senaste måttdatapunkterna än. Beroende på tjänst tar det ett par minuter att bearbeta måtten. För diagram som visar ett nyligen tidsintervall med en precision på 1 eller 5 minuter blir en minskning av värdet under de senaste minuterna tydligare: ![ skärm bild som visar ett släpp värde under de senaste minuterna.](./media/metrics-troubleshoot/unexpected-dip.png)

**Lösning:** Det här beteendet är avsiktligt. Vi tror att det är bäst att visa data så fort de blir tillgängliga även om de är *partiella* eller *ofullständiga*. På så sätt kan du dra viktiga slutsatser snabbare och börja undersöka direkt. Om du till exempel ser delvärdet X för ett mått som visar antalet fel, betyder det att minst X fel inträffade under en viss minut. Du kan börja undersöka problemet direkt, i stället för att vänta och se det exakta antalet fel som inträffade under minuten, vilket kanske inte är så viktigt. Diagrammet uppdateras när vi får hela uppsättningen data, men då kan det också visa nya ofullständiga datapunkter från senare minuter.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Det går inte att välja namn område och mått för gäst operativ system

Virtuella datorer och VM-skalningsuppsättningar har två måttkategorier: Mått av typen **Virtuell värddator** som samlas in av Azure-värdmiljön, och mått av typen **Gästoperativsystem (klassiskt)** som samlas in av [övervakningsagenten](agents-overview.md) som körs på dina virtuella datorer. Du installerar övervakningsagenten genom att aktivera [Azure Diagnostics-tillägget](diagnostics-extension-overview.md).

Som standard lagras mått för gästoperativsystem i ett Azure Storage-konto, som du väljer från fliken **Diagnostikinställningar** för din resurs. Om inga mått för gästoperativsystem samlas in eller om Metrics Explorer inte kan komma åt dem visas bara namnrymden för **Virtuell värddator**-måttet:

![bild på mått](./media/metrics-troubleshoot/vm.png)

**Lösning:** Om du inte ser namn området och måtten för **gäst operativ systemet (klassisk)** i Metrics Explorer:

1. Bekräfta att [Azure Diagnostics-tillägget](diagnostics-extension-overview.md) är aktiverat och konfigurerat att samla in mått.
    > [!WARNING]
    > Du kan inte använda [Log Analytics-agenten](agents-overview.md#log-analytics-agent) (kallas även Microsoft Monitoring Agent eller MMA) för att skicka **Gästoperativsystem** till ett lagringskonto.

1. Se till att **Microsoft. Insights** Resource provider har [registrerats för din prenumeration](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Kontrollera att lagringskontot inte skyddas av brandväggen. Azure Portal behöver åtkomst till lagringskontot för att kunna hämta måttdata och rita diagrammen.

1. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrol lera att måtten flödar till lagrings kontot. Om måtten inte samlas in följer du anvisningarna i [felsökningsguiden för Azure Diagnostics-tillägget](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om att komma igång med Metric Explorer](metrics-getting-started.md)
* [Lär dig mer om avancerade funktioner i Metric Explorer](metrics-charts.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Visa exempel på konfigurerade diagram](metric-chart-samples.md)
