---
title: Använda innehållstypen JSON för nyckelvärden
titleSuffix: Azure App Configuration
description: Lär dig hur du använder JSON-innehålls typ för nyckel värden
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 19de46bc87b72ada221c63e36e87d0545304d344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122161"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Utnyttjar innehålls typ för att lagra JSON-nyckel värden i app-konfiguration

Data lagras i appens konfiguration som nyckel värden, där värden behandlas som sträng typ som standard. Du kan dock ange en anpassad typ genom att använda den innehålls typs egenskap som är kopplad till varje nyckel värde, så att du kan bevara den ursprungliga typen av data eller låta programmet beter sig på olika sätt baserat på innehålls typ.


## <a name="overview"></a>Översikt

I app-konfigurationen kan du använda JSON-medietyp som innehålls typ för dina nyckel värden till att utnyttja fördelarna som:
- **Enklare data hantering**: att hantera nyckel värden, som matriser, blir mycket enklare i Azure Portal.
- **Förbättrad data export**: primitiva typer, matriser och JSON-objekt kommer att bevaras under data exporten.
- **Inbyggt stöd med app Configuration Provider**: nyckel värden med JSON Content-Type fungerar bra när de förbrukas av apparnas konfigurations leverantörs bibliotek i dina program.

#### <a name="valid-json-content-type"></a>Giltig JSON-innehålls typ

Medie typer som definieras [här](https://www.iana.org/assignments/media-types/media-types.xhtml)kan tilldelas den innehålls typ som är associerad med varje nyckel värde.
En medietyp består av en typ och en undertyp. Om typen är `application` och under typen (eller suffixet) är `json` , betraktas medie typen som en giltig JSON-innehålls typ.
Några exempel på giltiga JSON-innehålls typer är:

- application/json
- program/aktivitet + JSON
- Application/VND. foobar + JSON; charset = utf-8

#### <a name="valid-json-values"></a>Giltiga JSON-värden

När ett nyckel värde har JSON-innehåll, måste värdet vara i ett giltigt JSON-format för att klienter ska kunna bearbeta det korrekt. Annars kan klienterna Miss lyckas eller återgå och behandla dem som sträng format.
Några exempel på giltiga JSON-värden är:

- "John Berg"
- 723
- falskt
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"mål": {"standard": true, "level": "information"}}}

> [!NOTE]
> För resten av den här artikeln kallas alla nyckel värden i app-konfigurationen som har en giltig JSON-innehållstyp och ett giltigt JSON-värde som **JSON-nyckel värde**. 

I den här självstudien får du lära dig att:
> [!div class="checklist"]
> * Skapa JSON-nyckel-värden i app-konfigurationen.
> * Importera JSON-nyckel – värden från en JSON-fil.
> * Exportera JSON-nyckel-värden till en JSON-fil.
> * Använda JSON-nyckel värden i dina program.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- I den här självstudien krävs version 2.10.0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Skapa JSON-nyckel-värden i appens konfiguration

JSON-nyckel – värden kan skapas med hjälp av Azure Portal, Azure CLI eller genom att importera från en JSON-fil. I det här avsnittet hittar du instruktioner om hur du skapar samma JSON-nyckel värden med alla tre metoderna.

### <a name="create-json-key-values-using-azure-portal"></a>Skapa JSON-nyckel – värden med Azure Portal

Bläddra till appens konfigurations Arkiv och välj **konfigurations Utforskaren**  >  **skapa**  >  **nyckel-värde** för att lägga till följande nyckel/värde-par:

| Tangent | Värde | Innehållstyp |
|---|---|---|
| Inställningar: BackgroundColor | Växt | application/json |
| Inställningar: FontSize | 24 | application/json |
| Inställningar: UseDefaultRouting | falskt | application/json |
| Inställningar: BlockedUsers | null | application/json |
| Inställningar: ReleaseDate | "2020-08-04T12:34:56.789 Z" | application/json |
| Inställningar: RolloutPercentage | [25, 50, 75100] | application/json |
| Inställningar: loggning | {"Test": {"level": "Felsök"}, "Prod": {"level": "varning"}} | application/json |

Lämna **etiketten** Tom och välj **Använd**.

### <a name="create-json-key-values-using-azure-cli"></a>Skapa JSON-nyckel – värden med Azure CLI

Följande kommandon skapar JSON-nyckel värden i appens konfigurations arkiv. Ersätt `<appconfig_name>` med namnet på appens konfigurations arkiv.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Om du använder Azure CLI eller Azure Cloud Shell för att skapa JSON-nyckel värden måste det angivna värdet vara en Escaped JSON-sträng.

### <a name="import-json-key-values-from-a-file"></a>Importera JSON-nyckel – värden från en fil

Skapa en JSON-fil `Import.json` med namnet med följande innehåll och importera som nyckel värden till appens konfiguration:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth`Argumentet används för att förenkla hierarkiska data från en fil till nyckel/värde-par. I den här självstudien anges djupet för att demonstrera att du även kan lagra JSON-objekt som värden i app-konfigurationen. Om inget djup anges, kommer JSON-objekt att förenklas till den djupaste nivån som standard.

De JSON-nyckel värden som du har skapat bör se ut så här i appens konfiguration:

![Konfigurations arkiv som innehåller JSON-nyckel värden](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Exportera JSON-nyckel-värden till en fil

En av de största fördelarna med att använda JSON-nyckel värden är möjligheten att bevara den ursprungliga data typen för dina värden vid export. Om ett nyckel värde i app-konfigurationen saknar JSON-innehåll, behandlas värdet som sträng. 

Tänk på följande nyckel värden utan JSON-innehålls typ:

| Tangent | Värde | Innehållstyp |
|---|---|---|
| Inställningar: FontSize | 24 | |
| Inställningar: UseDefaultRouting | falskt | |

När du exporterar dessa nyckel värden till en JSON-fil, exporteras värdena som strängar:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Men när du exporterar JSON-nyckel värden till en fil behåller alla värden sina ursprungliga data typer. Du verifierar detta genom att exportera nyckel värden från din app-konfiguration till en JSON-fil. Du ser att den exporterade filen har samma innehåll som `Import.json` filen du importerade tidigare.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Om appens konfigurations Arkiv har några nyckel värden utan JSON-innehållstyp, exporteras de också till samma fil i sträng format. Om du bara vill exportera JSON-nyckel-värden tilldelar du en unik etikett eller ett prefix till JSON-nyckel värden och använder etikett-eller prefix filtrering under exporten.


## <a name="consuming-json-key-values-in-applications"></a>Använda JSON-nyckel värden i program

Det enklaste sättet att använda JSON-nyckel-värden i ditt program är via bibliotek för Provider för app-konfiguration. Med Provider-biblioteken behöver du inte implementera särskild hantering av JSON-nyckel värden i ditt program. De kommer att tolkas och konverteras så att de matchar programmets interna konfiguration.

Om du till exempel har följande nyckel-värde i appens konfiguration:

| Tangent | Värde | Innehållstyp |
|---|---|---|
| Inställningar | {"FontSize": 24, "UseDefaultRouting": false} | application/json |

.NET-program konfigurationen kommer att ha följande nyckel värden:

| Tangent | Värde |
|---|---|
| Inställningar: FontSize | 24 |
| Inställningar: UseDefaultRouting | falskt |

Du kan komma åt de nya nycklarna direkt eller så kan du välja att [binda konfigurations värden till instanser av .net-objekt](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern).


> [!Important]
> Inbyggt stöd för JSON-nyckel-värden finns i .NET-Konfigurationsprovider version 4.0.0 (eller senare). Mer information finns i avsnittet [*Nästa steg*](#next-steps) .

Om du använder SDK eller REST API för att läsa nyckel värden från App-konfigurationen, baserat på innehålls typ, är ditt program ansvarigt för att parsa värdet för JSON-nyckel-värde.


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du arbetar med JSON-nyckel värden i appens konfigurations Arkiv, skapar du ett program för att konsumera dessa nyckel värden:

* [ASP.NET Core snabb start](./quickstart-aspnet-core-app.md)
    * Förutsättning: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 eller senare.

* [Snabb start för .NET Core](./quickstart-dotnet-core-app.md)
    * Förutsättning: [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) -paket v 4.0.0 eller senare.
