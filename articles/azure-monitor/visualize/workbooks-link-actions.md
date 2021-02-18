---
title: Länk åtgärder för Azure Monitor arbets böcker
description: Så här använder du länk åtgärder i Azure Monitor arbets böcker
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622877"
---
# <a name="link-actions"></a>Länka åtgärder

Länk åtgärder kan nås via arbets boks länk steg eller med kolumn inställningar för [rutnät](../visualize/workbooks-grid-visualizations.md), [rubriker](../visualize/workbooks-tile-visualizations.md)eller [grafer](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Allmänna länk åtgärder

| Länk åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `Generic Details` | Visar rad värden i en Sammanhangs visning för egenskaps rutnät. |
| `Cell Details` | Visar cellvärdet i en Sammanhangs visning för egenskaps rutnät. Användbart när cellen innehåller en dynamisk typ med information (t. ex. JSON med begär ande egenskaper som plats, roll instans osv.). |
| `Url` | Cellens värde förväntas vara en giltig http-URL, och cellen är en länk som öppnar den URL: en på en ny flik.|

## <a name="application-insights"></a>Application Insights

| Länk åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `Custom Event Details` | Öppnar Application Insights Sök information med det anpassade händelse-ID: t ( `itemId` ) i cellen. |
| `* Details` | Liknar anpassad händelse information, förutom för beroenden, undantag, sid visningar, begär Anden och spår. |
| `Custom Event User Flows` | Öppnar den Application Insights Användarflödens upplevelsen som är pivoterad på det anpassade händelse namnet i cellen. |
| `* User Flows` | Precis som med anpassade händelse Användarflöden utom undantag, sidvyer och begär Anden. |
| `User Timeline` | Öppnar användarens tids linje med användar-ID ( `user_Id` ) i cellen. |
| `Session Timeline` | Öppnar den Application Insights Sök upplevelsen för värdet i cellen (du kan till exempel söka efter texten "ABC" där ABC är värdet i cellen). |

`*` anger ett jokertecken för tabellen ovan

## <a name="azure-resource"></a>Azure-resurs

| Länk åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `ARM Deployment` | Distribuera en Azure Resource Manager-mall.  När det här alternativet är markerat visas ytterligare fält som låter författaren konfigurera vilken Azure Resource Manager mall som ska öppnas, parametrar för mallen osv. [se Azure Resource Manager distributions länk inställningar](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Skapar en aviserings regel för en resurs.  |
| `Custom View` | Öppnar en anpassad vy. När det här alternativet är markerat visas ytterligare fält som låter författaren konfigurera visnings tillägget, visnings namnet och alla parametrar som används för att öppna vyn. [Se anpassad vy](#custom-view-link-settings). |
| `Metrics` | Öppnar vyn mått.  |
| `Resource overview` | Öppna resursens vy i portalen baserat på värdet resurs-ID i cellen. Författaren kan också ange ett `submenu` värde som öppnar ett särskilt meny alternativ i vyn resurs. |
| `Workbook (template)` | Öppna en mall för arbets böcker.  När det här alternativet är markerat visas ytterligare fält som låter författaren konfigurera vilken mall som ska öppnas osv.  |

## <a name="link-settings"></a>Länk inställningar

När du använder länk åter givning är följande inställningar tillgängliga:

![Skärm bild av länk inställningar](./media/workbooks-link-actions/link-settings.png)

| Inställning | Förklaring |
|:------------- |:-------------|
| `View to open` | Tillåter att författaren väljer en av de åtgärder som räknats upp ovan. |
| `Menu item` | Om "resurs översikt" är markerat är det här meny alternativet i resurs översikten för att öppna. Detta kan användas för att öppna aviseringar eller aktivitets loggar i stället för "Översikt" för resursen. Meny alternativ värden är olika för varje Azure `Resourcetype` .|
| `Link label` | Om det här värdet anges visas det här värdet i rutnäts kolumnen. Om det här värdet inte anges visas cellens värde. Om du vill att ett annat värde ska visas, t. ex. en termisk karta eller ikon, ska du inte använda `Link` åter givningen, Använd i stället lämplig åter givning och välja `Make this item a link` alternativet. |
| `Open link in Context Blade` | Om det här alternativet anges öppnas länken som en popup "context"-vy till höger i fönstret i stället för att öppnas som en fullständig vy. |

När du använder `Make this item a link` alternativet är följande inställningar tillgängliga:

| Inställning | Förklaring |
|:------------- |:-------------|
| `Link value comes from` | När du visar en cell som en renderare med en länk anger det här fältet var länken "länk" som ska användas i länken kommer från, så att författaren kan välja från en listruta i de andra kolumnerna i rutnätet. Cellen kan till exempel vara ett termisk karta värde, men du vill att länken ska öppna resurs översikten för resurs-ID: t på raden. I så fall skulle du ange värdet för länken som kommer från `Resource Id` fältet.
| `View to open` | samma som ovan. |
| `Menu item` | samma som ovan. |
| `Open link in Context Blade` | samma som ovan. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Inställningar för Azure Resource Manager distributions länk

Om den valda länk typen är `ARM Deployment` författaren måste du ange ytterligare inställningar för att öppna en Azure Resource Manager-distribution. Det finns två huvudsakliga flikar för konfigurationer.

### <a name="template-settings"></a>Mal lin ställningar

I det här avsnittet definieras var mallen ska hämtas och vilka parametrar som används för att köra Azure Resource Manager-distributionen.

| Källa | Förklaring |
|:------------- |:-------------|
|`Resource group id comes from` | Resurs-ID: t används för att hantera distribuerade resurser. Prenumerationen används för att hantera distribuerade resurser och kostnader. Resurs grupperna används som mappar för att ordna och hantera alla dina resurser. Om det här värdet inte anges kommer distributionen att Miss förväntas. Välj mellan `Cell` , `Column` , `Static Value` eller `Parameter` i [länk källor](#link-sources).|
|`ARM template URI from` | URI: n till själva Azure Resource Manager mallen. Mall-URI: n måste vara tillgänglig för de användare som ska distribuera mallen. Välj mellan `Cell` , `Column` , `Parameter` eller `Static Value`  i [länk källor](#link-sources). För starter tar du en titt på våra [snabb starts mallar](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Det här avsnittet definierar de mallparametrar som används för den mall-URI som definierats ovan. Dessa parametrar används för att distribuera mallen på körnings sidan. Rutnätet innehåller knappen expandera verktygsfält som hjälper dig att fylla i parametrarna med hjälp av namnen som definierats i mall-URI: n och ange den till statiska tomma värden. Det här alternativet kan endast användas när det inte finns några parametrar i rutnätet och mall-URI har angetts. Det nedre avsnittet är en förhands granskning av hur parameter resultatet ser ut. Välj Uppdatera för att uppdatera förhands granskningen med aktuella ändringar. Parametrar är vanligt vis värden, medan referenser är något som kan peka på nyckel valvs hemligheter som användaren har åtkomst till. <br/><br/> **Blad begränsning i Template Viewer** – återger inte referens parametrar korrekt och visas som null/Value, och användarna kommer därför inte att kunna distribuera referens parametrar från fliken mallar.|

![Skärm bild av Azure Resource Manager mal lin ställningar](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX-inställningar

I det här avsnittet konfigureras vad användarna ska se innan de kör Azure Resource Manager-distributionen.

| Källa | Förklaring |
|:------------- |:-------------|
|`Title from` | Rubrik som används i körnings vyn. Välj mellan `Cell` , `Column` , `Parameter` eller `Static Value` i [länk källor](#link-sources).|
|`Description from` | Detta är den markdown-text som används för att ge en användbar Beskrivning av användare när de vill distribuera mallen. Välj mellan `Cell` , `Column` , `Parameter` eller `Static Value`  i [länk källor](#link-sources). <br/><br/> **Obs:** Om `Static Value` är markerat visas en flerradig text ruta. I den här text rutan kan du lösa parametrar med hjälp av `{paramName}` . Du kan också behandla kolumner som parametrar genom att lägga till `_column` efter kolumn namnet som `{columnName_column}` . I exempel bilden nedan kan vi referera till kolumnen genom att `VMName` skriva `{VMName_column}` . Värdet efter kolonet är parameter- [Formatter](../visualize/workbooks-parameters.md#parameter-options), i det här fallet är det `value` .|
|`Run button text from` | Etikett som används på knappen Kör (kör) för att distribuera Azure Resource Manager-mallen. Detta är vad användarna väljer för att börja Distribuera Azure Resource Manager-mallen.|

![Skärm bild av Azure Resource Manager UX-inställningar](./media/workbooks-link-actions/ux-settings.png)

När de här konfigurationerna har ställts in kommer den att öppnas i vyn med det UX som beskrivs i UX-inställningarna när användarna väljer länken. Om användaren väljer `Run button text from` att distribuera en Azure Resource Manager-mall med hjälp av värdena från [mal lin ställningar](#template-settings). Visa mall öppnar fliken för mallens visnings program för användaren för att undersöka mallen och parametrarna innan du distribuerar.

![Skärm bild av vyn kör Azure Resource Manager](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Länk inställningar för anpassad vy

Använd det här för att öppna anpassade vyer i Azure Portal. Verifiera all konfiguration och alla inställningar. Felaktiga värden kommer att orsaka fel i portalen eller att inte öppna vyerna på rätt sätt. Det finns två sätt att konfigurera inställningarna via `Form` eller `URL` .

> [!NOTE]
> Det går inte att öppna vyer med en meny i en Sammanhangs flik. Om en vy med en meny har kon figurer ATS för att öppnas i en Sammanhangs flik visas inte fliken kontext när länken är markerad.

### <a name="form"></a>Formulär

| Källa | Förklaring |
|:------------- |:-------------|
|`Extension name` | Namnet på det tillägg som är värd för vyns namn.|
|`View name` | Namnet på vyn som ska öppnas.|

#### <a name="view-inputs"></a>Visa indata

Det finns två typer av indata, rutnät och JSON. Använd `Grid` för TABB-indata för enkel nyckel och värde eller Välj `JSON` om du vill ange en KAPSLAd JSON-indata.

- Rutnät
    - `Parameter Name`: Namnet på Indataparametern för vyn.
    - `Parameter Comes From`: Där värdet för parametern View ska hämtas från. Välj mellan `Cell` , `Column` , `Parameter` eller `Static Value` i [länk källor](#link-sources).
    > [!NOTE]
    > Om `Static Value` är markerat kan parametrarna lösas med hjälp av hakparenteser-länken `{paramName}` i text rutan. Kolumner kan behandlas som parameter kolumner genom `_column` att de läggs till efter kolumn namnet som `{columnName_column}` .

    - `Parameter Value`: beroende på `Parameter Comes From` visas detta en listruta med tillgängliga parametrar, kolumner eller ett statiskt värde.

    ![Skärm bild av inställningen Redigera kolumn Visa inställningar för anpassad vy från formuläret.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Ange inmatade flikar i ett JSON-format i redigeraren. Precis som det `Grid` kan finnas referenser till läge, parametrar och kolumner med hjälp av `{paramName}` parametrar, och `{columnName_column}` för kolumner. Om du väljer visas `Show JSON Sample` förväntade utdata för alla matchade parametrar och kolumner användare för Visa indata.

    ![Skärm bild som visar öppna Inställningar för anpassad vy med Visa ininformation på JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Klistra in en portal-URL som innehåller tillägget, namnet på vyn och eventuella indata som behövs för att öppna vyn. När du `Initialize Settings` har valt så fylls den `Form` för författaren för att lägga till/ändra/ta bort någon av de indata som visas.

![Skärm bild som visar kolumn inställning Visa inställningar för anpassad vy från URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Länk inställningar för arbets bok (mall)

Om den valda länk typen är `Workbook (Template)` , måste författaren ange ytterligare inställningar för att öppna rätt arbetsboksmall. Inställningarna nedan innehåller alternativ för hur rutnätet ska hitta lämpligt värde för varje inställning.

| Inställning | Förklaring |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Detta är resurs-ID för den Azure-resurs som "äger" arbets boken. Vanligt vis är det en Application Insights resurs eller en Log Analytics arbets yta. Inuti Azure Monitor kan det också vara en litteral sträng `"Azure Monitor"` . När arbets boken har sparats är det här som arbets boken ska länkas till. |
| `Workbook resources` | En matris med Azure-resurs-ID: n som anger standard resursen som används i arbets boken. Om den mall som öppnas exempelvis visar mått för virtuella datorer, skulle värdena här vara resurs-ID: n för virtuella datorer.  Många gånger är ägaren och resurserna inställda på samma inställningar. |
| `Template Id` | Ange ID för den mall som ska öppnas. Om det här är en community-mall från galleriet (det vanligaste fallet) ska du ge sökvägen till mallen med `Community-` , till exempel `Community-Workbooks/Performance/Apdex` för `Workbooks/Performance/Apdex` mallen. Om det här är en länk till en sparad arbets bok/mall är det det fullständiga Azure-resurs-ID: t för objektet. |
| `Workbook Type` | Ange vilken typ av arbetsboksmall-mall som ska öppnas. De vanligaste fallen använder `Default` `Workbook` alternativet eller för att använda värdet i den aktuella arbets boken. |
| `Gallery Type` | Detta anger vilken Galleri typ som ska visas i vyn "Galleri" för mallen som öppnas. De vanligaste fallen använder `Default` `Workbook` alternativet eller för att använda värdet i den aktuella arbets boken. |
|`Location comes from` | Plats fältet ska anges om du öppnar en specifik arbets boks resurs. Om ingen plats anges är det mycket långsammare att hitta arbets bokens innehåll. Om du känner till platsen anger du den. Om du inte känner till platsen eller öppnar en mall som inte har någon angiven plats lämnar du fältet som "standard".|
|`Pass specific parameters to template` | Välj om du vill skicka vissa parametrar till mallen. Om det här alternativet har valts skickas bara de angivna parametrarna till mallen, annars skickas alla parametrar i den aktuella arbets boken till mallen och i så fall måste parameter *namnen* vara samma i båda arbets böckerna för att parametervärdet ska fungera.|
|`Workbook Template Parameters` | Det här avsnittet definierar de parametrar som skickas till mål mal len. Namnet måste stämma överens med namnet på parametern i mål mal len. Välj värde från `Cell` , `Column` , `Parameter` , och `Static Value` . Namn och värde får inte vara tomt för att skicka parametern till mål mal len.|

För var och en av inställningarna ovan måste författaren välja var värdet i den länkade arbets boken kommer från. Se [länk källor](#link-sources)

När arbets boks länken öppnas skickas alla värden som kon figurer ATS från inställningarna ovan till vyn ny arbets bok.

![Skärm bild av inställningar för arbets boks länkar](./media/workbooks-link-actions/workbook-link-settings.png)

![Skärm bild av inställningar för parametrar för arbets boks mal len](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Länka källor

| Källa | Förklaring |
|:------------- |:-------------|
| `Cell` | Detta kommer att använda värdet i den cellen i rutnätet som länk värde |
| `Column` | När det här alternativet är markerat visas ett annat fält så att författaren kan välja en annan kolumn i rutnätet.  Värdet för den kolumnen för raden kommer att användas i länk svärdet. Detta används ofta för att aktivera varje rad i ett rutnät för att öppna en annan mall, genom `Template Id` att ange fältet till `column` eller för att öppna samma arbetsboksmall för olika resurser, om `Workbook resources` fältet är inställt på en kolumn som innehåller ett Azure-resurs-ID |
| `Parameter` | När det här alternativet är markerat visas ett annat fält som låter författaren välja en parameter. Värdet för den parametern används för värdet när användaren klickar på länken |
| `Static value` | När det här alternativet är markerat visas ett annat fält för att tillåta författar typen i ett statiskt värde som ska användas i den länkade arbets boken. Detta används vanligt vis när alla rader i rutnätet ska använda samma värde för ett fält. |
| `Step` | Använd det värde som angetts i det aktuella steget i arbets boken. Det här är vanligt i fråge-och mått steg för att ställa in arbets boks resurser i den länkade arbets boken för de som används *i steget fråga/mått*, inte den aktuella arbets boken |
| `Workbook` | Använd det värde som angetts i den aktuella arbets boken. |
| `Default` | Använd standardvärdet som ska användas om inget värde har angetts. Detta är vanligt för Galleri typ, där standard galleriet skulle anges av typen av ägar resurs |

## <a name="next-steps"></a>Nästa steg

- [Kontrol lera](../visualize/workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
- Lär dig hur du använder [grupper i arbets böcker](../visualize/workbooks-groups.md).