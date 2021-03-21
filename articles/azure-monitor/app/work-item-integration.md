---
title: Integration av arbets objekt (för hands version) – Application Insights
description: Lär dig hur du skapar arbets objekt i GitHub eller Azure DevOps med Application Insights data som är inbäddade i dem.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731926"
---
# <a name="work-item-integration-preview"></a>Integration av arbets objekt (för hands version)

Med funktionerna för integration av arbets objekt kan du enkelt skapa arbets objekt i GitHub eller Azure DevOps som har relevanta Application Insights data inbäddade i dem.

> [!IMPORTANT]
> Integration av arbets objekt är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Skapa och konfigurera en mall för arbets objekt

1. Om du vill skapa en mall för arbets objekt går du till Application Insights resurs och till vänster under *Konfigurera* Välj **arbets objekt** klickar du på **skapa en ny mall**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Skärm bild av fliken arbets uppgifter med skapa en ny mall vald." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Du kan också skapa en mall för arbets objekt från fliken transaktions information från slut punkt till slut punkt, om det inte finns någon mall. Välj en händelse och gå till höger och välj **skapa ett arbets objekt** och **börja sedan med en mall för arbets böcker**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Skärm bild av fliken transaktions information från slut punkt till slut punkt med skapa en arbets uppgift, börja med en arbetsboksmall vald." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. När du har valt **skapa en ny mall** kan du välja spårnings system, namnge din arbets bok, länka till det valda spårnings systemet och välja en region för lagring av mallen (Standardvärdet är den region som Application Insights-resursen finns i). URL-parametrarna är standard-URL: en för din lagrings plats, till exempel `https://github.com/myusername/reponame` eller `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Skärm bild av skapa en ny mall för arbets objekt arbets uppgift.":::

## <a name="create-a-work-item"></a>Skapa ett arbets objekt

 Du kan komma åt din nya mall från valfri slut punkt till slut punkts transaktions information som du kan komma åt från prestanda, problem, tillgänglighet eller andra flikar.

1. Om du vill skapa ett arbets objekt går du till information från slut punkt till slut punkt, väljer en händelse och väljer sedan **skapa arbets** objekt och väljer mallen för arbets objekt.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Skärm bild av fliken transaktions information från slut punkt till slut punkt med skapa arbets uppgift vald." lightbox="./media/work-item-integration/create-work-item.png":::

1. En ny flik i webbläsaren öppnas med ditt val spårnings system. I Azure DevOps kan du skapa en bugg eller uppgift och i GitHub kan du skapa ett nytt problem i din lagrings plats. Ett nytt arbets objekt skapas automatiskt med sammanhangsbaserad information från Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Skärm bild av automatiskt skapade GitHub-problem" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Skärm bild av automatisk skapande av bugg i Azure-DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Redigera en mall

Om du vill redigera din mall går du till fliken **arbets uppgifter** under *Konfigurera* och väljer Penn ikonen bredvid den arbets bok som du vill uppdatera.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Skärm bild av fliken arbets objekt med ikonen Redigera penna vald.":::

Välj Redigera ![ Redigera-ikonen ](./media/work-item-integration/edit-icon.png) längst upp för att börja redigera mallen. Mallar för arbets objekt baseras på [Azure Monitor arbets böcker](../visualize/workbooks-overview.md). Informationen om arbets objekt genereras med hjälp av nyckelordet frågespråk. Du kan ändra frågorna för att lägga till mer Sammanhangs beroende för ditt team. När du är klar med redigeringen sparar du arbets boken genom att välja ![ ikonen Spara ikon spara ](./media/work-item-integration/save-icon.png) i det översta verktygsfältet.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Skärm bild av arbets mal len arbets objekt mal len i redigerings läge." lightbox="./media/work-item-integration/edit-workbook.png":::

Du kan skapa mer än en arbets uppgifts konfiguration och ha en anpassad arbets bok för att uppfylla varje scenario. Arbets böckerna kan också distribueras genom Azure Resource Manager att säkerställa standard implementeringar i dina miljöer.