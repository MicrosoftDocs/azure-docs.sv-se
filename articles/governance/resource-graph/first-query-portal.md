---
title: 'Snabbstart: Din första portalfråga'
description: I den här snabbstarten följer du stegen för att köra din första fråga från Azure Portal med Azure Resource Graph Explorer.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533087"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Snabbstart: Kör din första Resource Graph med hjälp av Azure Resource Graph Explorer

Kraften hos Azure Resource Graph är tillgänglig direkt i Azure Portal via Azure Resource Graph Explorer. Resource Graph Explorer innehåller användbar information om de Azure Resource Manager resurstyper och egenskaper som du kan köra frågor mot. Resource Graph Explorer tillhandahåller också ett rent gränssnitt för att arbeta med flera frågor, utvärdera resultaten och även konvertera resultatet av vissa frågor till ett diagram som kan fästas på en Azure-instrumentpanel.

I slutet av den här snabbstarten har du använt Azure Portal och Resource Graph Explorer för att köra din första Resource Graph-fråga och fäst resultatet på en instrumentpanel.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Öppna den [Azure Portal](https://portal.azure.com) för att hitta och använda Resource Graph Explorer genom att följa dessa steg för att köra din Resource Graph fråga:

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Resource Graph Explorer**.

1. I delen **Fråga 1** i fönstret anger du frågan och `Resources | project name, type | limit 5` väljer Kör **fråga**.

   > [!NOTE]
   > Eftersom det här frågeexempel inte ger någon sorteringsmodifierare som , kommer körning av frågan flera gånger troligen att ge en annan `order by` uppsättning resurser per begäran.

1. Granska frågesvaret på **fliken** Resultat. Välj fliken **Meddelanden** för att se information om frågan, inklusive antalet resultat och frågans varaktighet. Eventuella fel visas under den här fliken.

1. Uppdatera frågan till egenskapen `order by` **Name:** `Resources | project name, type | limit 5 | order by name asc` . Välj sedan Kör **fråga**.

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommandoordningen begränsar först frågeresultatet och beställer dem sedan.

1. Uppdatera frågan till att först `order by` egenskapen **Name** (Namn) och sedan till de fem `limit` främsta resultaten: `Resources | project name, type | order by name asc | limit 5` . Välj sedan **Kör fråga**.

När den sista frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultaten som returneras konsekventa och sorterade efter egenskapen **Namn,** men fortfarande begränsade till de fem främsta resultaten.

### <a name="schema-browser"></a>Schemawebbläsare

Schemawebbläsaren finns i den vänstra rutan i Resource Graph Explorer. Den här listan över  resurser visar alla resurstyper för Azure-resurser som både stöds av Azure Resource Graph och som finns i en klientorganisation som du har åtkomst till. Om du expanderar en resurstyp eller underegenskaper visas underordnade egenskaper som kan användas för att skapa en Resource Graph fråga.

Om du väljer resurstyp `where type =="<resource type>"` placerar du i frågerutan. Om du väljer en av de underordnade egenskaperna läggs `where <propertyName> == "INSERT_VALUE_HERE"` den till i frågerutan.
Schemawebbläsaren är ett bra sätt att identifiera egenskaper för användning i frågor. Ersätt INSERT VALUE _\_ HERE med \_ ditt_ eget värde, justera frågan med villkor, operatorer och funktioner för att uppnå dina avsedda resultat.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Skapa ett diagram från Resource Graph frågan

När du har kört föregående  fråga visas ett meddelande om att "resultatuppsättningen inte är kompatibel med en cirkeldiagramvisualisering" om du väljer fliken Diagram. Frågor som listar resultat kan inte göras till ett diagram, men frågor som anger antal resurser kan göra det. Med [exempelfrågan – Räkna virtuella datorer efter OS-typ](./samples/starter.md#count-os)ska vi skapa en visualisering från Resource Graph frågan.

1. I delen **Fråga 1** i fönstret anger du följande fråga och väljer **Kör fråga.**

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Välj fliken **Resultat** och observera att svaret för den här frågan innehåller antal.

1. Välj **fliken** Diagram. Frågan resulterar nu i visualiseringar. Ändra typen från Välj _diagramtyp... till antingen_ _Stapeldiagram eller_ _Ringdiagram för_ att experimentera med de tillgängliga visualiseringsalternativen.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fästa frågevisualiseringen på en instrumentpanel

När du har resultat från en fråga som kan visualiseras kan datavisualiseringen sedan fästas på en av dina instrumentpaneler. Följ dessa steg när du har kört den föregående frågan:

1. Välj **Spara** och ange namnet "Virtuella datorer efter OS-typ". Välj sedan **Spara** längst ned i den högra rutan.

1. Välj **Kör fråga** för att köra frågan igen nu när den har sparats.

1. På fliken **Diagram** väljer du en datavisualisering. Välj sedan **Fäst på instrumentpanelen**.

1. Välj antingen det portalmeddelande som visas eller välj **Instrumentpanel** i det vänstra fönstret.

Frågan är nu tillgänglig på instrumentpanelen med rubriken på panelen som matchar frågenamnet. Om frågan inte sparats när den fästes får den namnet "Fråga 1" i stället.

Frågan och resulterande datavisualisering körs och uppdateras varje gång instrumentpanelen läses in, vilket ger dynamiska insikter i realtid till din Azure-miljö direkt i arbetsflödet.

> [!NOTE]
> Frågor som resulterar i en lista kan också fästas på instrumentpanelen. Funktionen är inte begränsad till datavisualiseringar av frågor.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importera exempel Resource Graph Explorer-instrumentpaneler

Om du vill ge exempel Resource Graph frågor och hur Resource Graph Explorer kan användas för att förbättra ditt Azure Portal arbetsflöde kan du prova dessa exempelinstrumentpaneler.

- [Resource Graph Explorer – Exempel på instrumentpanel #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Exempelbild för exempelinstrumentpanelens #1" lightbox="./media/arge-sample1-large.png":::

- [Resource Graph Explorer – Exempel på instrumentpanel #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Exempelbild för exempelinstrumentpanelens #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> Antal och diagram i exemplet på skärmbilder på instrumentpanelen ovan varierar beroende på din Azure-miljö.

1. Välj och ladda ned exempelinstrumentpanelen som du vill utvärdera.

1. I Azure Portal väljer du **Instrumentpanel** i den vänstra rutan.

1. Välj **Ladda upp** och leta sedan upp och välj den nedladdade exempelinstrumentpanelen. Välj sedan **Öppna.**

Den importerade instrumentpanelen visas automatiskt. Eftersom den nu finns i Azure Portal kan du utforska och göra ändringar efter behov eller skapa nya instrumentpaneler från exemplet för att dela med dina team. Mer information om hur du arbetar med instrumentpaneler finns [i Skapa och dela instrumentpaneler i Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph från din Azure Portal miljö kan du göra det med följande steg:

1. Välj **Instrumentpanel** i det vänstra fönstret.

1. I listrutan för instrumentpanelen väljer du exemplet på Resource Graph instrumentpanel som du vill ta bort.

1. Välj **Ta** bort på instrumentpanelens meny överst på instrumentpanelen och välj **Ok för** att bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du använt Azure Resource Graph Explorer för att köra din första fråga och tittat på instrumentpanelsexempel som drivs av Resource Graph. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
