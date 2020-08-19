---
title: Versions anteckningar för Application Insights | Microsoft Docs
description: Lägg till distribution eller skapa markörer i dina Metrics Explorer-diagram i Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: e21981f134f6a78109d70b1edf6d61e204b64e12
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258411"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar på mått diagram i Application Insights

Anteckningar visar var du har distribuerat en ny version eller andra viktiga händelser. Anteckningar gör det enkelt att se om ändringarna hade någon effekt på programmets prestanda. De kan skapas automatiskt av versions systemet för [Azure pipelines](/azure/devops/pipelines/tasks/) . Du kan också skapa anteckningar för att flagga alla händelser som du vill genom att skapa dem från PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Versions anteckningar med Azure pipelines build

Versions anteckningar är en funktion i de molnbaserade Azure-pipelinen för Azure-DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installera antecknings tillägget (en tid)

Om du vill kunna skapa versions anteckningar måste du installera ett av de många Azure DevOps-tillägg som är tillgängliga i Visual Studio Marketplace.

1. Logga in på ditt [Azure DevOps](https://azure.microsoft.com/services/devops/) -projekt.
   
1. På sidan [tillägg för versions anteckningar](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) för Visual Studio Marketplace väljer du din Azure DevOps-organisation och väljer sedan **Installera** för att lägga till tillägget till din Azure DevOps-organisation.
   
   ![Välj en Azure DevOps-organisation och välj sedan installera.](./media/annotations/1-install.png)
   
Du behöver bara installera tillägget en gång för din Azure DevOps-organisation. Nu kan du konfigurera versions anteckningar för alla projekt i din organisation.

### <a name="configure-release-annotations"></a>Konfigurera versions anteckningar

Skapa en separat API-nyckel för var och en av dina mallar för Azure-pipeliner.

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna den Application Insights resurs som övervakar ditt program. Eller om du inte har en sådan, [skapar du en ny Application Insights-resurs](./app-insights-overview.md).
   
1. Öppna fliken **API-åtkomst** och kopiera **Application Insights-ID**.
   
   ![Under API-åtkomst kopierar du program-ID: t.](./media/annotations/2-app-id.png)

1. I ett separat webbläsarfönster öppnar eller skapar du den versions mall som hanterar dina distributioner av Azure-pipeline.
   
1. Välj **Lägg till aktivitet**och välj sedan aktiviteten **Application Insights versions anteckning** på menyn.
   
   ![Välj Lägg till uppgift och välj Application Insights versions anteckning.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Versions antecknings aktiviteten stöder för närvarande endast Windows-baserade agenter. den körs inte på Linux, macOS eller andra typer av agenter.
   
1. Under **program-ID**, klistra in Application Insights-ID som du kopierade från fliken **API-åtkomst** .
   
   ![Klistra in Application Insights-ID](./media/annotations/4-paste-app-id.png)
   
1. I fönstret Application Insights- **API-åtkomst** väljer du **skapa API-nyckel**. 
   
   ![På fliken API-åtkomst väljer du skapa API-nyckel.](./media/annotations/5-create-api-key.png)
   
1. I fönstret **skapa API-nyckel** anger du en beskrivning, väljer **Skriv kommentarer**och väljer sedan **generera nyckel**. Kopiera den nya nyckeln.
   
   ![I fönstret Skapa API-nyckel anger du en beskrivning, väljer Skriv kommentarer och väljer sedan generera nyckel.](./media/annotations/6-create-api-key.png)
   
1. I fönstret frigör mall på fliken **variabler** väljer du **Lägg till** för att skapa en variabel definition för den nya API-nyckeln.

1. Under **namn**anger du `ApiKey` och under **värde**klistrar du in API-nyckeln som du kopierade från fliken **API-åtkomst** .
   
   ![På fliken Azure DevOps-variabler väljer du Lägg till, namnger variabeln ApiKey och klistrar in API-nyckeln under värde.](./media/annotations/7-paste-api-key.png)
   
1. Spara mallen genom att välja **Spara** i fönstret för huvud versions mal len.


   > [!NOTE]
   > Begränsningar för API-nycklar beskrivs i [dokumentationen REST API Rate Limits](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Visa anteckningar


   > [!NOTE]
   > Versions anteckningar är för närvarande inte tillgängliga i fönstret mått i Application Insights

När du nu använder versions mal len för att distribuera en ny version skickas en anteckning till Application Insights. Anteckningarna kan visas på följande platser:

I användnings fönstret kan du också skapa versions anteckningar manuellt:

![Skärm bild av stapeldiagram med antalet användar besök som visas under en period om timmar. Versions anteckningar visas som gröna bockar ovanför diagrammet som visar hur lång tid en version inträffade](./media/annotations/usage-pane.png)

I en loggbaserade arbets bok fråga där visualiseringen visar tid längs x-axeln.

![Skärm bild av fönstret arbets böcker med Time Series log-baserad fråga med anteckningar som visas](./media/annotations/workbooks-annotations.png)

Om du vill aktivera anteckningar i din arbets bok går du till **Avancerade inställningar** och väljer **Visa anteckningar**.

![Skärm bild av menyn Avancerade inställningar med orden Visa anteckningar markerade med en bock bredvid inställningen för att aktivera den.](./media/annotations/workbook-show-annotations.png)

Välj en antecknings markör för att öppna information om versionen, inklusive begär Ande, käll kontroll gren, versions pipeline och miljö.

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade anteckningar från PowerShell
Du kan använda [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell-skriptet från GitHub för att skapa anteckningar från vilken process som helst, utan att använda Azure DevOps. 

1. Gör en lokal kopia av [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Använd stegen i föregående procedur för att hämta ditt Application Insights-ID och skapa en API-nyckel från fliken Application Insights- **API-åtkomst** .
   
1. Anropa PowerShell-skriptet med följande kod och ersätt plats hållarna för vinkel paren tes med dina värden. `-releaseProperties`Är valfria. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Du kan ändra skriptet, till exempel för att skapa kommentarer som redan har infallit.

## <a name="next-steps"></a>Nästa steg

* [Skapa arbetsuppgifter](./diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](./powershell.md)

