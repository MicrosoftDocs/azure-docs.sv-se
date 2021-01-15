---
title: Konfigurera Function app-inställningar i Azure Functions
description: Lär dig hur du konfigurerar Function-appinställningar i Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 70aecc2613fbe21d34e36f9487d7ba383e140bc8
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217370"
---
# <a name="manage-your-function-app"></a>Hantera din Function-app 

I Azure Functions tillhandahåller en Function-app körnings kontexten för dina enskilda funktioner. Function-appens beteenden gäller för alla funktioner som finns i en specifik Function-app. Alla funktioner i en Function-app måste vara av samma [språk](supported-languages.md). 

Enskilda funktioner i en Function-app distribueras tillsammans och skalas tillsammans. Alla funktioner i samma Function-app delar resurser, per instans, som Function-appen skalar. 

Anslutnings strängar, miljövariabler och andra program inställningar definieras separat för varje Function-app. Alla data som måste delas mellan Functions-appar ska lagras externt i ett sparat lager.

## <a name="get-started-in-the-azure-portal"></a>Kom igång i Azure Portal

1. Börja genom att gå till [Azure Portal] och logga in på ditt Azure-konto. I Sök fältet längst upp i portalen anger du namnet på din Function-app och väljer den i listan. 

2. Under **Inställningar** i den vänstra rutan väljer du **konfiguration**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Översikt över Function-appen i Azure Portal":::

Du kan navigera till allt du behöver för att hantera din Function-app från översikts sidan, i synnerhet **[program inställningar](#settings)** och **[plattforms funktioner](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Arbeta med program inställningar

Program inställningar kan hanteras från [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) och med hjälp av [Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) och [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Du kan också hantera program inställningar från [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) och från [Visual Studio](functions-develop-vs.md#function-app-settings). 

De här inställningarna lagras krypterade. Mer information finns i [program inställningar säkerhet](security-concepts.md#application-settings).

# <a name="portal"></a>[Portal](#tab/portal)

Fliken **program inställningar** innehåller inställningar som används av din Function-app. Du måste välja **Visa värden** om du vill se värdena i portalen. Om du vill lägga till en inställning i portalen väljer du **ny program inställning** och lägger till det nya nyckel/värde-paret.

![Function app-inställningar i Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list)Kommandot returnerar de befintliga program inställningarna, som i följande exempel:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)Kommandot lägger till eller uppdaterar en program inställning. I följande exempel skapas en inställning med en nyckel med namnet `CUSTOM_FUNCTION_APP_SETTING` och värdet `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting)Cmdleten returnerar de befintliga program inställningarna, som i följande exempel: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Kommandot lägger till eller uppdaterar en program inställning. I följande exempel skapas en inställning med en nyckel med namnet `CUSTOM_FUNCTION_APP_SETTING` och värdet `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Använd program inställningar

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

När du utvecklar en Function-app lokalt måste du upprätthålla lokala kopior av dessa värden i local.settings.jsi projekt filen. Mer information finns i [filen med lokala inställningar](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Typ av värd plan

När du skapar en Function-app skapar du också en App Service värd plan där appen körs. En plan kan ha en eller flera Function-appar. Funktioner, skalning och prissättning för dina funktioner beror på vilken typ av plan du har. Mer information finns på sidan med [Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/).

Du kan bestämma vilken typ av plan som används av din Function-app från Azure Portal eller genom att använda Azure CLI-eller Azure PowerShell-API: er. 

Följande värden anger plan typen:

| Plantyp | Portalen | Azure CLI/PowerShell |
| --- | --- | --- |
| [Förbrukning](consumption-plan.md) | **Förbrukning** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedikerad (App Service)](dedicated-plan.md) | Önskade | Önskade |

# <a name="portal"></a>[Portal](#tab/portal)

För att avgöra vilken typ av plan som används av din Function-app, se **App Service plan** på fliken **Översikt** för function-appen i [Azure Portal](https://portal.azure.com). Om du vill se pris nivån väljer du namnet på **app Services planen** och väljer sedan **Egenskaper** i det vänstra fönstret.

![Visa skalnings plan i portalen](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Kör följande Azure CLI-kommando för att få din värd Plans typ:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

I föregående exempel ersätter `<RESOURCE_GROUP>` och `<FUNCTION_APP_NAME>` med namn på resurs grupp och funktion, respektive. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör följande Azure PowerShell-kommando för att få din värd Plans typ:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
I föregående exempel ersätter `<RESOURCE_GROUP>` och `<FUNCTION_APP_NAME>` med namn på resurs grupp och funktion, respektive. 

---


## <a name="platform-features"></a>Plattforms funktioner

Function-appar körs i, och underhålls av, Azure App Service plattformen. Därför har dina funktions appar till gång till de flesta av funktionerna i Azures centrala webb värd plattform. I den vänstra rutan får du till gång till många funktioner i den App Service-plattform som du kan använda i dina Function-appar. 

> [!NOTE]
> Alla App Service funktioner är inte tillgängliga när en Function-App körs på förbruknings värd planen.

Resten av den här artikeln fokuserar på följande App Service funktioner i Azure Portal som är användbara för funktioner:

+ [App Service redigerare](#editor)
+ [Konsol](#console)
+ [Avancerade verktyg (kudu)](#kudu)
+ [Distributionsalternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)

Mer information om hur du arbetar med App Service inställningar finns i [konfigurera Azure App Service inställningar](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>App Service redigerare

![App Service redigeraren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Services redigeraren är en avancerad i-Portal-redigerare som du kan använda för att ändra JSON-konfigurationsfiler och kommandofiler. Om du väljer det här alternativet startas en separat flik i webbläsaren med en grundläggande redigerare. På så sätt kan du integrera med git-lagringsplatsen, köra och felsöka kod och ändra programmets inställningar. Den här redigeraren ger en förbättrad utvecklings miljö för dina funktioner jämfört med den inbyggda funktions redigeraren.  

Vi rekommenderar att du tänker utveckla dina funktioner på den lokala datorn. När du utvecklar lokalt och publicerar till Azure är dina projektfiler skrivskyddade i portalen. Mer information finns i [kod-och test Azure Functions lokalt](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konsol

![Function app-konsol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsolen i-portalen är ett idealiskt utvecklingsverktyg när du föredrar att interagera med din Function-app från kommando raden. Vanliga kommandon är att skapa och navigera mellan kataloger och filer, samt att köra kommandofiler och skript. 

När du utvecklar lokalt rekommenderar vi att du använder [Azure Functions Core tools](functions-run-local.md) och [Azure CLI].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Avancerade verktyg (kudu)

![Konfigurera kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De avancerade verktygen för App Service (kallas även kudu) ger till gång till avancerade administrativa funktioner i din Function-app. Från kudu kan du hantera system information, appinställningar, miljövariabler, plats tillägg, HTTP-rubriker och servervariabler. Du kan också starta **kudu** genom att bläddra till SCM-slutpunkten för din Function-app, t. ex. `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Distributionscenter

När du använder en käll kontroll lösning för att utveckla och underhålla din funktions kod kan du skapa och distribuera från käll kontroll i Deployment Center. Ditt projekt skapas och distribueras till Azure när du gör uppdateringar. Mer information finns [i distributions tekniker i Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin resource sharing (CORS)

För att förhindra körning av skadlig kod på klienten blockerar moderna webbläsare förfrågningar från webb program till resurser som körs i en separat domän. Med [resurs delning mellan ursprung (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) kan en `Access-Control-Allow-Origin` rubrik deklarera vilka ursprung som får anropa slut punkter i din Function-app.

#### <a name="portal"></a>Portalen

När du konfigurerar listan över **tillåtna ursprung** för din Function-app `Access-Control-Allow-Origin` läggs rubriken automatiskt till i alla svar från http-slutpunkter i din Function-app. 

![Konfigurera CORS-listan för Function-appen](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

När jokertecknet ( `*` ) används ignoreras alla andra domäner. 

Använd [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) kommandot för att lägga till en domän i listan över tillåtna ursprung. I följande exempel läggs contoso.com-domänen till:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Använd [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) kommandot för att visa en lista över aktuella tillåtna ursprung.

### <a name="authentication"></a><a name="auth"></a>Autentisering

![Konfigurera autentisering för en Function-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

När funktioner använder en HTTP-utlösare kan du kräva att anrop först ska autentiseras. App Service stöder Azure Active Directory autentisering och inloggning med sociala leverantörer, till exempel Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar vissa autentiseringsproviders finns i [Översikt över Azure App Service autentisering](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Nästa steg

+ [Konfigurera Azure App Service inställningar](../app-service/configure-common.md)
+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure-portalen]: https://portal.azure.com
