---
title: Konfigurera program inställningar för Azures statiska Web Apps
description: Lär dig hur du konfigurerar program inställningar för Azures statiska Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219190"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Konfigurera program inställningar för för hands versionen av Azure statisk Web Apps

Program inställningarna innehåller konfigurations inställningar för värden som kan ändras, t. ex. databas anslutnings strängar. Genom att lägga till program inställningar kan du ändra konfigurations ingången till din app, utan att behöva ändra program koden.

Program inställningarna är:

- Krypterat i vila
- Kopieras till [mellanlagrings](review-publish-pull-requests.md) -och produktions miljöer

Program inställningarna kallas ibland miljövariabler.

> [!IMPORTANT]
> Program inställningarna som beskrivs i den här artikeln gäller endast Server dels-API: et för en statisk Azure-webbapp.
>
> Information om hur du använder miljövariabler med webb programmet för klient delen finns i dokumenten för [Java Script Framework](#javascript-frameworks-and-libraries) eller den [statiska webbplats generatorn](#static-site-generators).

## <a name="prerequisites"></a>Förutsättningar

- Ett statiskt Azure Web Apps-program
- [Azure CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Typer av program inställningar

Det finns vanligt vis två aspekter på ett statiskt Azure Web Apps-program. Det första är webb programmet eller det statiska innehållet som representeras av HTML, CSS, Java Script och avbildningar. Det andra är Server dels-API: et, som drivs av ett Azure Functions-program.

Den här artikeln visar hur du hanterar program inställningar för Server dels-API: et i Azure Functions.

Program inställningarna som beskrivs i den här artikeln kan inte användas eller refereras till i statiska webb program. Många frontend-ramverk och statiska webbplats generatorer gör det dock möjligt att använda miljövariabler under utvecklingen. Vid Bygg tiden ersätts dessa variabler med sina värden i den genererade HTML-koden eller Java Script. Eftersom data i HTML och Java Script lätt kan identifieras av besökare, vill du undvika att placera känslig information i klient programmet. Inställningar som innehåller känsliga data finns bäst i API-delen av ditt program.

Information om hur du använder miljövariabler med ditt JavaScript-ramverk eller bibliotek finns i följande artiklar för mer information.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript-ramverk och bibliotek

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Statiska webbplats generatorer

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Om API app-inställningar

API: er i Azures statiska Web Apps drivs av Azure Functions, vilket gör att du kan definiera program inställningar i _local.settings.jsi_ filen. Den här filen definierar program inställningar i `Values` konfigurationens egenskap.

Följande exempel _local.settings.jspå_ visar hur du lägger till ett värde för `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Inställningar som definieras i `Values` egenskapen kan refereras från kod som miljövariabler som är tillgängliga från `process.env` objektet.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json`Filen spåras inte av GitHub-lagringsplatsen eftersom känslig information, t. ex. databas anslutnings strängar, ofta ingår i filen. Eftersom de lokala inställningarna är kvar på datorn måste du ladda upp dina inställningar manuellt till Azure.

I allmänhet görs det sällan att ladda upp dina inställningar och det krävs inte i varje version.

## <a name="uploading-application-settings"></a>Laddar upp program inställningar

Du kan konfigurera program inställningar via Azure Portal eller med Azure CLI.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Azure Portal tillhandahåller ett gränssnitt för att skapa, uppdatera och ta bort program inställningar.

1. Navigera till [Azure Portal](https://portal.azure.com).

1. I Sök fältet söker du efter och väljer **statisk Web Apps**.

1. Klicka på **konfigurations** alternativet på sido panelen.

1. Välj den miljö som du vill tillämpa program inställningarna på. Mellanlagrings miljöer skapas automatiskt när en pull-begäran genereras och befordras till produktion när pull-begäran har slagits samman. Du kan ange program inställningar per miljö.

1. Klicka på **knappen Lägg** till för att lägga till en ny app-inställning.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Konfigurations vy för Azure statisk Web Apps":::

1. Ange ett **namn** och ett **värde**.

1. Klicka på **OK**.

1. Klicka på **Spara**.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Du kan använda `az rest` kommandot för att utföra Mass uppladdning av dina inställningar till Azure. Kommandot accepterar program inställningar som JSON-objekt i en överordnad egenskap som kallas `properties` .

Det enklaste sättet att skapa en JSON-fil med lämpliga värden är att skapa en modifierad version av _local.settings.jsi_ filen.

1. Lägg till följande post i din _. gitignore_ -fil för att se till att den nya filen med känsliga data inte visas offentligt.

   ```bash
   local.settings*.json
   ```

2. Skapa sedan en kopia av din _local.settings.jspå_ filen och ge den namnet _local.settings.properties.jspå_.

3. I den nya filen tar du bort alla andra data från filen utom program inställningarna och byter namn `Values` till `properties` .

   Filen bör nu se ut ungefär som i följande exempel:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLI-kommandot kräver ett antal värden som är speciella för ditt konto för att köra uppladdningen. I _översikts_ fönstret för den statiska Web Apps resursen har du åtkomst till följande information:

1. Statiskt plats namn
2. Namn på resursgrupp
3. Prenumerations-ID:t

:::image type="content" source="media/application-settings/overview.png" alt-text="Översikt över Azures statiska Web Apps":::

4. Kör följande kommando från en terminal eller kommando rad. Se till att ersätta plats hållarna för `<YOUR_STATIC_SITE_NAME>` , `<YOUR_RESOURCE_GROUP_NAME>` och `<YOUR_SUBSCRIPTION_ID>` med dina värden från _översikts_ fönstret.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Filen local.settings.properties.jspå måste finnas i samma katalog som kommandot körs på. Den här filen kan likna vad du vill. Namnet är inte signifikant.

### <a name="view-application-settings-with-the-azure-cli"></a>Visa program inställningar med Azure CLI

Program inställningar är tillgängliga för visning via Azure CLI.

- Kör följande kommando från en terminal eller kommando rad. Se till att ersätta plats hållarna `<YOUR_SUBSCRIPTION_ID>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_STATIC_SITE_NAME>` med dina värden.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera lokal utveckling](local-development.md)
