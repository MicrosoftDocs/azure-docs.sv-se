---
title: Alternativ för utvecklarportalen med egen värd
titleSuffix: Azure API Management
description: Lär dig mer om alternativa metoder som du kan använda när du själv är värd för en utvecklarportal i Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741869"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Alternativa metoder för utvecklarportalen med egen värd

Det finns flera alternativa metoder som du kan utforska när du själv [är värd för en utvecklarportal:](developer-portal-self-host.md)

* Använd produktionsbyggen av designern och utgivaren.

* Använd en Azure-funktionsapp för att publicera din portal.

* Front the files of your portal with a Content Delivery Network (CDN) to reduce page loading times.

Den här artikeln innehåller information om var och en av dessa metoder. 

Om du inte redan har gjort det kan du konfigurera en [lokal miljö för](developer-portal-self-host.md#step-1-set-up-local-environment) den senaste versionen av utvecklarportalen.

## <a name="build-for-production"></a>Skapa för produktion

Om du vill vara värd för utvecklingsmiljön i portalen online i samarbetssyfte kan du använda produktionsbyggen av designern och utgivaren. Produktionsbyggen paketera filerna, exkludera källmappningar osv.

Skapa ett paket i `./dist/designer` katalogen genom att köra kommandot :

```sh
npm run build-designer
```

Resultatet är ett ensidesprogram, så du kan fortfarande distribuera det till en statisk webbvärd, till exempel Azure Blob Storage statiska webbplatsen.

På samma sätt placerar du en kompilerad och optimerad utgivare i `./dist/publisher` mappen :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Använda funktionsappen för att publicera portalen

Kör publiceringssteget i molnet som ett alternativ till att köra det lokalt.

Om du vill implementera publicering med en Azure-funktionsapp behöver du följande:

- [Skapa en Azure-funktion](../azure-functions/functions-create-first-azure-function.md). Funktionen måste vara en JavaScript-språkfunktion.
- Installera Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Steg 1: Konfigurera lagring av utdata

Ladda upp innehållet direkt till värdwebbplatsen ("$web" container för utdatalagring) i stället för en lokal mapp. Konfigurera den här ändringen i `./src/config.publish.json` filen:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Steg 2: Skapa och distribuera funktionsappen

Det finns ett exempel på en HTTP-utlösarfunktion i `./examples` mappen . Kör följande kommando för att skapa `./dist/function` den och placera den i :

```sh
npm run build-function
```

Logga sedan in på Azure CLI och distribuera det:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

När den har distribuerats kan du anropa den med ett HTTP-anrop:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Värdtjänster och CDN

I en utvecklarportal på egen värd föreslår vi att du använder ett Azure [Storage-konto](developer-portal-self-host.md) som värd för din webbplats. Du kan dock publicera filerna via valfri lösning, inklusive tjänster från värdleverantörer.

Du kan också fronta filerna med en Content Delivery Network (CDN) för att minska sidinläsningstiderna. Vi rekommenderar att du [använder Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
