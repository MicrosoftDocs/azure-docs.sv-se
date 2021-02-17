---
title: Azure Monitor visualiseringar i arbets boks träd
description: Lär dig mer om alla visualiseringar i Azure Monitor arbets bok träd.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625234"
---
# <a name="tree-visualizations"></a>Träd visualiseringar

Arbets böcker har stöd för hierarkiska vyer via träd rutnät. Trees tillåter att vissa rader kan utökas till nästa nivå för att öka detalj nivån.

Exemplet nedan visar hälso mått för behållare (storleken på arbets minnet) visualiserade som ett träd rutnät. Noder på den översta nivån här är Azure Kubernetes service-noder (AKS), nästa nivå är poddar och den slutgiltiga nivån är behållare. Observera att du fortfarande kan formatera dina kolumner som i ett rutnät (termisk karta, ikoner, länk). Den underliggande data källan i det här fallet är en Log Analytics arbets yta med AKS-loggar.

[![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Lägga till ett träd rutnät
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Välj **Lägg till** och *Lägg till fråga* för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Ange visualiseringen till **rutnät**
6. Välj knappen **kolumn inställningar** för att öppna fönstret inställningar
7. I avsnittet **träd/gruppera efter inställningar** längst ned anger du:
    * Träd typ: `Parent/Child`
    * ID-fält: `Id`
    * Överordnat ID-fält: `ParentId`
    * Visa expanderingen på: `Name`
    * Markera kryss rutan *expandera den översta nivån i trädet* .
8. I avsnittet _kolumner_ högst upp anger du:
    * _ID_ – kolumn åter givning: `Hidden`
    * _Överordnat ID_ -kolumn åter givning: `Hidden`
    * _Begär Anden_ -kolumn åter givning: `Bar` , färg: `Blue` , minimalt värde: `0`
9. Välj knappen **Spara och Stäng** längst ned i fönstret.

[![Skärm bild som visar sammanfattnings vyn för panelen med ovanstående fråga och inställningar.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Träd inställningar

| Inställning | Förklaring |
|:------------- |:-------------|
| `Id Field` | Unikt ID för varje rad i rutnätet. |
| `Parent Id Field` | ID: t för den överordnade raden för den aktuella raden. |
| `Show the expander on` | Den kolumn där träd expanderingen ska visas. Det är vanligt att träd rutnät döljer sitt ID och ett överordnat ID-fält eftersom de inte är särskilt läsbara. I stället visas expanderingen i ett fält med ett mer läsbart värde, t. ex. namnet på entiteten. |
| `Expand the top level of the tree` | Om det här alternativet är markerat expanderas träd rutnätet på den översta nivån. Användbart om du vill visa mer information som standard. |

## <a name="grouping-in-a-grid"></a>Gruppera i ett rutnät

Med gruppering kan du bygga hierarkiska vyer som liknar dem ovan med betydligt enklare frågor. Du förlorar sammansättning på de inre noderna i trädet, men det är acceptabelt för vissa scenarier. Använd *Group by* för att skapa träd vyer när den underliggande resultat uppsättningen inte kan omvandlas till ett lämpligt kostnads fritt formulär, till exempel: avisering, hälso tillstånd och mått data.

## <a name="adding-a-tree-using-grouping"></a>Lägga till ett träd med hjälp av gruppering

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Välj **Lägg till** och *Lägg till fråga* för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Ange visualiseringen till *Grid*.
2. Välj **knappen kolumn inställningar** för att öppna fönstret inställningar.
3. I avsnittet *träd/gruppera efter inställningar* längst ned anger du:
    * Träd typ: `Group By`
    * Gruppera efter: `App`
    * Sedan efter: `None`
    * Markera kryss rutan *expandera den översta nivån i trädet* .
4. I avsnittet *kolumner* högst upp anger du:
    * Åter givning av *app* -kolumn:`Hidden`
    * *Begär Anden* -kolumn åter givning: `Bar` , färg: `Blue` , minimalt värde: `0`
5. Välj knappen **Spara och Stäng** längst ned i fönstret.

[![Skärm bild som visar skapandet av en träd visualisering i arbets böcker](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [graf i arbets böcker](workbooks-graph-visualizations.md).
* Lär dig hur du skapar en [panel i arbets böcker](workbooks-tile-visualizations.md).
