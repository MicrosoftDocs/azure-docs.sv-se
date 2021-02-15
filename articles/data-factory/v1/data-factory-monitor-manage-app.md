---
title: Övervaka och hantera data pipelines – Azure
description: Lär dig hur du använder appen för övervakning och hantering för att övervaka och hantera Azure-datafabriker och pipeliner.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: be11f742eb99d25cb0e41a3dd0888d62417837dd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380380"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Övervaka och hantera Azure Data Factory pipelines med hjälp av appen övervakning och hantering
> [!div class="op_single_selector"]
> * [Använda Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Använda övervaknings-och hanterings program](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [övervaka och hantera data Factory pipeliner i](../monitor-visually.md).

Den här artikeln beskriver hur du använder appen övervakning och hantering för att övervaka, hantera och felsöka Data Factory pipelines. Du kan komma igång med att använda programmet genom att titta på följande video:

> [!NOTE]
> Användar gränssnittet som visas i videon kanske inte exakt matchar det du ser i portalen. Det är något äldre, men koncepten är oförändrade. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Starta övervaknings-och hanterings appen
Starta appen övervakare och hantering genom att klicka på panelen **övervaka & hantera** på bladet **Data Factory** för din data fabrik.

![Panelen övervakning på Data Factory start sida](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Du bör se appen övervakning och hantering öppen i ett separat fönster.  

![Övervaknings- och hanteringsapp](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Om du ser att webbläsaren har fastnat på "auktoriserar..." avmarkerar du kryss rutan **blockera cookies från tredje part** , eller låter den vara markerad, skapar ett undantag för **login.microsoftonline.com** och försöker sedan öppna appen igen.


I listan aktivitets fönster i mitten av fönstret visas ett aktivitets fönster för varje körning av en aktivitet. Om du till exempel har schemalagt aktiviteten för att köra per timme i fem timmar visas fem aktivitets fönster som är kopplade till fem data sektorer. Om du inte ser aktivitets fönster i listan längst ned gör du följande:
 
- Uppdatera **Start** -och **slut tid** filtren överst för att matcha start-och slut tiderna för din pipeline och klicka sedan på knappen **tillämpa** .  
- Aktivitets listan i Windows uppdateras inte automatiskt. Klicka på knappen **Uppdatera** i verktygsfältet i listan **aktivitets fönster** .  

Om du inte har ett Data Factory-program för att testa de här stegen med, gör du självstudierna: [Kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Förstå övervaknings-och hanterings appen
Det finns tre flikar till vänster: **Resursläsaren**, **övervaka vyer** och **aviseringar**. Den första fliken (**Resursläsaren**) är markerad som standard.

### <a name="resource-explorer"></a>Resursläsaren
Du ser följande:

* Vyn Resursläsaren **träd** i det vänstra fönstret.
* **Diagramvyn** överst i fönstret i mitten.
* Listan **aktivitets fönster** längst ned i fönstret i mitten.
* Fliken **Egenskaper**, **aktivitets fönstret Utforskaren** och fliken **skript** i den högra rutan.

I Resursläsaren visas alla resurser (pipelines, data uppsättningar, länkade tjänster) i data fabriken i en trädvy. När du väljer ett objekt i Resursläsaren:

* Den associerade Data Factory entiteten är markerad i diagramvyn.
* [Associerade aktivitets fönster](data-factory-scheduling-and-execution.md) markeras i listan aktivitets fönster längst ned.  
* Egenskaperna för det valda objektet visas i Fönstret Egenskaper i den högra rutan.
* JSON-definitionen för det valda objektet visas, om tillämpligt. Till exempel: en länkad tjänst, en data uppsättning eller en pipeline.

![Resursläsaren](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

I artikeln om [schemaläggning och körning](data-factory-scheduling-and-execution.md) finns detaljerad information om aktivitets fönster.

### <a name="diagram-view"></a>Diagramvy
Diagramvyn för en data fabrik ger ett fönster med glas för att övervaka och hantera en data fabrik och dess till gångar. När du väljer en Data Factory entitet (data uppsättning/pipeline) i diagramvyn:

* Data Factory-entiteten väljs i trädvyn.
* De associerade aktivitets Fönstren markeras i listan aktivitets fönster.
* Egenskaperna för det valda objektet visas i Fönstret Egenskaper.

När pipelinen är aktive rad (inte i pausat läge) visas den med en grön linje:

![Pipeline körs](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Du kan pausa, återuppta eller avsluta en pipeline genom att välja den i diagramvyn och använda knapparna i kommando fältet.

![Pausa/återuppta i kommando fältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Det finns tre kommando fälts knappar för pipelinen i diagramvyn. Du kan använda den andra knappen för att pausa pipelinen. Om du pausar avbryts inte de aktiviteter som körs för tillfället och de kan fortsätta att slutföras. Den tredje knappen pausar pipelinen och avslutar dess befintliga körnings aktiviteter. Den första knappen återupptar pipelinen. När din pipeline har pausats ändras färgen på pipelinen. En pausad pipeline ser till exempel ut i följande bild: 

![Pipelinen har pausats](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Du kan välja två eller fler pipeliner genom att använda Ctrl-tangenten. Du kan använda kommando fälts knapparna för att pausa/återuppta flera pipeliner i taget.

Du kan också högerklicka på en pipeline och välja alternativ för att pausa, återuppta eller avsluta en pipeline. 

![Snabb meny för pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klicka på alternativet **Öppna pipeline** om du vill visa alla aktiviteter i pipelinen. 

![Menyn Öppna pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

I vyn öppen pipeline visas alla aktiviteter i pipelinen. I det här exemplet finns det bara en aktivitet: kopierings aktivitet. 

![Öppen pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Om du vill gå tillbaka till den föregående vyn klickar du på data fabriks namnet på menyn för dynamiska länkar överst.

När du väljer en data uppsättning för utdata i pipeline-vyn eller när du flyttar musen över data uppsättningen för utdata, visas popup-fönstret för aktivitets Fönstren för den data uppsättningen.

![Popup-fönster för aktivitets fönster](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Du kan klicka på ett aktivitets fönster om du vill se information om det i fönstret **Egenskaper** i den högra rutan.

![Egenskaper för aktivitets fönster](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

I den högra rutan växlar du till fliken **aktivitets fönstrets Utforskare** för att se mer information.

![Skärm bild som visar hur du kommer åt fliken aktivitets fönstret Utforskaren.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Du ser också **matchade variabler** för varje körnings försök för en aktivitet i avsnittet **försök** .

![Matchade variabler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Växla till fliken **skript** för att se definitionen av JSON-skriptet för det valda objektet.   

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Du kan se aktivitets fönster på tre platser:

* Popup-fönstret aktivitets fönster i vyn diagram (i mitten).
* Aktivitets fönstrets Utforskare i den högra rutan.
* Listan aktivitets fönster i det nedre fönstret.

I fönstret aktivitets fönster i popup-fönstret och aktivitets fönstret i Windows kan du bläddra till föregående vecka och nästa vecka med hjälp av vänster och höger pilar.

![Aktivitets fönster i Utforskaren vänster-/högerpil](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Längst ned i diagramvyn visas följande knappar: zooma in, zooma ut, zooma för att passa, zooma 100%, Lås layout. Med knappen **Lås layout** kan du inte flytta tabeller och pipeliner oavsiktligt i diagramvyn. Den är på som standard. Du kan inaktivera det och flytta entiteter runt i diagrammet. När du inaktiverar det kan du använda den sista knappen för att automatiskt placera tabeller och pipeliner. Du kan också zooma in eller ut med hjälp av mus hjulet.

![Zoomnings kommandon för diagramvyn](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista med aktivitetsfönster
Listan aktivitets fönster längst ned i fönstret i mitten visar alla aktivitets fönster för den data uppsättning som du valde i Resursläsaren eller diagramvyn. Som standard är listan i fallande ordning, vilket innebär att du ser det senaste aktivitets fönstret överst.

![Lista med aktivitetsfönster](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Den här listan uppdateras inte automatiskt, så Använd knappen Uppdatera i verktygsfältet för att uppdatera den manuellt.  

Aktivitets fönster kan ha någon av följande statusar:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substatus</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime</td><td>Det går inte att köra tids perioden för aktivitets fönstret.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>De överordnade beroendena är inte klara.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräknings resurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alla aktivitets instanser är upptagna för att köra andra aktivitets fönster.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten har pausats och det går inte att köra aktivitets Fönstren förrän den har återupptagits.</td>
</tr>
<tr>
<td>Försök igen</td><td>Det görs ett nytt försök att köra aktiviteten.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen har inte startat ännu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Verifieringen väntar på att göras om.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Pågår</td><td>Verifiera</td><td>Verifiering pågår.</td>
</tr>
<td>-</td>
<td>Aktivitets fönstret bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>Stängningsåtgärd</td><td>Aktivitets körningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Aktivitets fönstret avbröts av en användar åtgärd.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Det gick inte att skapa eller verifiera aktivitets fönstret.</td>
</tr>
<td>Redo</td><td>-</td><td>Aktivitets fönstret är klart för användning.</td>
</tr>
<tr>
<td>Överhoppad</td><td>-</td><td>Aktivitets fönstret behandlades inte.</td>
</tr>
<tr>
<td>Inget</td><td>-</td><td>Ett aktivitets fönster som används för att finnas med en annan status, men som har återställts.</td>
</tr>
</table>


När du klickar på ett aktivitets fönster i listan visas information om det i **aktivitets Utforskaren** eller fönstret **Egenskaper** till höger.

![Skärm bild som visar hur du visar information om ett aktivitets fönster.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Uppdatera aktivitets fönster
Informationen uppdateras inte automatiskt, så Använd uppdaterings knappen (den andra knappen) i kommando fältet för att uppdatera aktivitets listan i Windows manuellt.  

### <a name="properties-window"></a>Fönstret Egenskaper
Fönstret Egenskaper visas i den högra rutan i övervaknings-och hanterings appen.

![Fönstret Egenskaper](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Den visar egenskaper för det objekt som du har valt i listan Resursläsaren (trädvy), diagramvyn eller aktivitets fönster.

### <a name="activity-window-explorer"></a>Aktivitets fönstrets Utforskare
Fönstret **aktivitets fönstret Utforskaren** visas i det högra fönstret i övervaknings-och hanterings appen. Den visar information om aktivitets fönstret som du valde i popup-fönstret aktivitets fönster eller i listan aktivitets fönster.

![Aktivitets fönstrets Utforskare](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Du kan växla till ett annat aktivitets fönster genom att klicka på det i vyn Kalender längst upp. Du kan också använda Vänsterpil/HÖGERPIL längst upp för att se aktivitets fönster från föregående vecka eller nästa vecka.

Du kan använda verktygsfälts knapparna i det nedre fönstret för att köra om aktivitets fönstret eller uppdatera informationen i fönstret.

### <a name="script"></a>Skript
Du kan använda fliken **skript** för att Visa JSON-definitionen för den valda data Factory entiteten (länkad tjänst, data uppsättning eller pipeline).

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Använda systemvyer
Övervaknings-och hanterings appen innehåller förskapade systemvyer (**senaste aktivitets fönster**, **misslyckad aktivitets fönster**, pågående **aktivitets** fönster) som gör att du kan visa senaste/misslyckade aktivitets Fönstren för din data fabrik.

Växla till fliken **övervakande vyer** till vänster genom att klicka på den.

![Fliken övervaka vyer](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

För närvarande finns det tre systemvyer som stöds. Välj ett alternativ för att visa senaste aktivitets fönster, misslyckad aktivitets fönster eller pågående aktivitets fönster i listan aktivitets fönster (längst ned i fönstret i mitten).

När du väljer **Windows alternativet senaste aktivitet** visas alla senaste aktivitets fönster i fallande ordning efter **senaste försök**.

Du kan använda vyn för **misslyckad aktivitet i Windows** för att se alla misslyckade aktivitets fönster i listan. Välj ett fönster för misslyckad aktivitet i listan om du vill visa information om det i fönstret **Egenskaper** eller i **fönstret aktivitets fönster Utforskaren**. Du kan också hämta loggar för ett fönster för misslyckad aktivitet.

## <a name="sort-and-filter-activity-windows"></a>Sortera och filtrera aktivitets fönster
Ändra inställningarna för **Start tid** och **slut tid** i kommando fältet för att filtrera aktivitets fönster. När du har ändrat start-och slut tid klickar du på knappen bredvid slut tiden för att uppdatera listan aktivitets Windows.

![Start-och slut tider](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> För närvarande är alla tider i UTC-format i övervaknings-och hanterings appen.
>
>

I **listan aktivitets fönster** klickar du på namnet på en kolumn (till exempel: status).

![Kolumn meny för aktivitets lista i Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Du kan göra följande:

* Sortera i stigande ordning.
* Sortera i fallande ordning.
* Filtrera efter ett eller flera värden (klart, väntar och så vidare).

När du anger ett filter i en kolumn visas filter knappen som är aktive rad för den kolumnen, vilket indikerar att värdena i kolumnen är filtrerade värden.

![Filtrera efter en kolumn i aktivitets fönster listan](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Du kan använda samma popup-fönster för att rensa filter. Om du vill rensa alla filter för listan aktivitets fönster klickar du på knappen Rensa filter i kommando fältet.

![Rensa alla filter för aktivitets listan i Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Utföra batch-åtgärder
### <a name="rerun-selected-activity-windows"></a>Kör om valt aktivitets fönster
Välj ett aktivitets fönster, klicka på nedpilen för det första kommando fältet och välj Kör om   /  **Kör igen med Uppström i pipeline**. När du väljer alternativet **Kör om med uppströms i pipeline, kör** den även om alla överordnade aktivitets fönster.
    ![Kör ett aktivitets fönster igen](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Du kan också välja flera aktivitets fönster i listan och köra om dem på samma tidpunkt. Du kanske vill filtrera aktivitets fönster baserat på status (till exempel: **misslyckades**)--och sedan köra Fönstren för misslyckad aktivitet igen efter att ha åtgärdat problemet som gör att aktivitets Fönstren Miss lyckas. I följande avsnitt finns information om filtrering av aktivitets fönster i listan.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/återuppta flera pipelines
Du kan välja två eller fler pipeliner genom att använda Ctrl-tangenten. Du kan använda kommando fälts knapparna (som är markerade i den röda rektangeln i följande bild) för att pausa/återuppta dem.

![Pausa/återuppta i kommando fältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
