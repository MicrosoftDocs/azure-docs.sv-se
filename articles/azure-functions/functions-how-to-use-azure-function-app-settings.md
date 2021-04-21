---
title: Konfigurera inställningar för funktionsappen i Azure Functions
description: Lär dig hur du konfigurerar inställningar för funktionsappen i Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: ed87a5a744defb15d4a898aeabdce5267b7431fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775663"
---
# <a name="manage-your-function-app"></a>Hantera funktionsappen 

I Azure Functions en funktionsapp körningskontext för dina enskilda funktioner. Funktionsappsbeteenden gäller för alla funktioner som finns i en viss funktionsapp. Alla funktioner i en funktionsapp måste ha samma [språk.](supported-languages.md) 

Enskilda funktioner i en funktionsapp distribueras tillsammans och skalas tillsammans. Alla funktioner i samma funktionsapp delar resurser, per instans, allt eftersom funktionsappen skalar. 

Anslutningssträngar, miljövariabler och andra programinställningar definieras separat för varje funktionsapp. Alla data som måste delas mellan funktionsappar ska lagras externt i ett bestående arkiv.

## <a name="get-started-in-the-azure-portal"></a>Kom igång i Azure Portal

1. Börja genom att gå till [Azure Portal] och logga in på ditt Azure-konto. I sökfältet högst upp i portalen anger du namnet på funktionsappen och väljer den i listan. 

2. Under **Inställningar** i det vänstra fönstret väljer du **Konfiguration.**

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Översikt över funktionsappen i Azure Portal":::

Du kan navigera till allt du behöver för att hantera funktionsappen från översiktssidan, särskilt **[programinställningarna och](#settings)** **[plattformsfunktionerna](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Arbeta med programinställningar

Programinställningar kan hanteras från [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) och med hjälp av Azure [CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) och [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Du kan också hantera programinställningar från [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) och [från Visual Studio](functions-develop-vs.md#function-app-settings). 

De här inställningarna lagras krypterade. Mer information finns i Säkerhet [för programinställningar.](security-concepts.md#application-settings)

# <a name="portal"></a>[Portal](#tab/portal)

Information om hur du hittar programinställningarna [finns i Kom igång i Azure Portal](#get-started-in-the-azure-portal). 

Fliken **Programinställningar** innehåller inställningar som används av funktionsappen. Du måste välja **Visa värden** för att se värdena i portalen. Om du vill lägga till en inställning i portalen väljer **du Ny programinställning** och lägger till det nya nyckel/värde-paret.

![Funktionsappinställningar i Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Kommandot [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) returnerar de befintliga programinställningarna, som i följande exempel:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Kommandot [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) lägger till eller uppdaterar en programinställning. I följande exempel skapas en inställning med en nyckel med `CUSTOM_FUNCTION_APP_SETTING` namnet och värdet `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting)Cmdleten returnerar de befintliga programinställningarna, som i följande exempel: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Kommandot [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) lägger till eller uppdaterar en programinställning. I följande exempel skapas en inställning med en nyckel med `CUSTOM_FUNCTION_APP_SETTING` namnet och värdet `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Använda programinställningar

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

När du utvecklar en funktionsapp lokalt måste du underhålla lokala kopior av dessa värden i local.settings.jspå projektfilen. Mer information finns i [Filen Med lokala inställningar.](functions-run-local.md#local-settings-file)

## <a name="hosting-plan-type"></a>Typ av värdplan

När du skapar en funktionsapp skapar du även en värdplan där appen körs. En plan kan ha en eller flera funktionsappar. Funktionernas funktioner, skalning och priser beror på typen av plan. Mer information finns i [Azure Functions värdalternativ.](functions-scale.md)

Du kan bestämma vilken typ av plan som används av funktionsappen från Azure Portal eller med hjälp av Azure CLI eller Azure PowerShell API:er. 

Följande värden anger plantypen:

| Plantyp | Portalen | Azure CLI/PowerShell |
| --- | --- | --- |
| [Förbrukning](consumption-plan.md) | **Förbrukning** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedikerad (App Service)](dedicated-plan.md) | Olika | Olika |

# <a name="portal"></a>[Portal](#tab/portal)

Information om vilken typ av plan som används av **funktionsappen finns App Service plan** på fliken Översikt för funktionsappen i [Azure Portal](https://portal.azure.com).  Om du vill se prisnivån väljer du namnet på **App Service plan** och väljer **sedan Egenskaper** i den vänstra rutan.

![Visa skalningsplan i portalen](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Kör följande Azure CLI-kommando för att få din värdplantyp:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

I föregående exempel ersätter `<RESOURCE_GROUP>` och med namnen på `<FUNCTION_APP_NAME>` resursgruppen och funktionsappen. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör följande kommando Azure PowerShell för att hämta typen av värdplan:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
I föregående exempel ersätter `<RESOURCE_GROUP>` och med namnen på `<FUNCTION_APP_NAME>` resursgruppen och funktionsappen. 

---

## <a name="plan-migration"></a>Planera migrering

Du kan använda Azure CLI-kommandon för att migrera en funktionsapp mellan en förbrukningsplan och en Premium-plan i Windows. De specifika kommandona beror på migreringens riktning. Direktmigrering till en dedikerad plan (App Service) stöds inte för närvarande.

Den här migreringen stöds inte i Linux.

### <a name="consumption-to-premium"></a>Förbrukning till Premium

Använd följande procedur för att migrera från en förbrukningsplan till en Premium-plan i Windows:

1. Kör följande kommando för att skapa en ny App Service plan (Elastic Premium) i samma region och resursgrupp som din befintliga funktionsapp.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Kör följande kommando för att migrera den befintliga funktionsappen till den nya Premium-planen

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Om du inte längre behöver din tidigare förbrukningsfunktionsappplan tar du bort den ursprungliga planen för funktionsappen när du har bekräftat att du har migrerat till den nya. Kör följande kommando för att hämta en lista över alla förbrukningsplaner i resursgruppen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Du kan ta bort planen på ett säkert sätt med noll platser, vilket är den som du har migrerat från.

1. Kör följande kommando för att ta bort förbrukningsplanen som du migrerade från.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium till förbrukning

Använd följande procedur för att migrera från en Premium-plan till en förbrukningsplan i Windows:

1. Kör följande kommando för att skapa en ny funktionsapp (Förbrukning) i samma region och resursgrupp som din befintliga funktionsapp. Det här kommandot skapar också en ny förbrukningsplan där funktionsappen körs.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Kör följande kommando för att migrera den befintliga funktionsappen till den nya förbrukningsplanen.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Ta bort funktionsappen som du skapade i steg 1 eftersom du bara behöver den plan som skapades för att köra den befintliga funktionsappen.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Om du inte längre behöver din tidigare Premium-funktionsappplan tar du bort den ursprungliga planen för funktionsappen när du har bekräftat att du har migrerat till den nya. Observera att om planen inte tas bort debiteras du fortfarande för Premium-planen. Kör följande kommando för att hämta en lista över alla Premium-planer i resursgruppen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Kör följande kommando för att ta bort premiumplanen som du migrerade från.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Plattformsfunktioner

Funktionsappar körs i och underhålls av den Azure App Service plattformen. Därför har dina funktionsappar åtkomst till de flesta av funktionerna i Azures grundläggande webbvärdplattform. I det vänstra fönstret får du åtkomst till de många funktionerna i App Service plattformen som du kan använda i dina funktionsappar. 

> [!NOTE]
> Alla funktioner App Service inte tillgängliga när en funktionsapp körs på värdplanen Förbrukning.

Resten av den här artikeln fokuserar på följande App Service funktioner i Azure Portal som är användbara för Functions:

+ [App Service redigeringsprogram](#editor)
+ [Konsol](#console)
+ [Avancerade verktyg (Kudu)](#kudu)
+ [Distributionsalternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)

Mer information om hur du arbetar med App Service finns i [Konfigurera Azure App Service inställningar.](../app-service/configure-common.md)

### <a name="app-service-editor"></a><a name="editor"></a>App Service redigeringsprogram

![Redigeringsredigeraren App Service den](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Redigeringsredigeraren App Service avancerad i portalen som du kan använda för att ändra JSON-konfigurationsfiler och kodfiler. Om du väljer det här alternativet startas en separat webbläsarflik med en grundläggande redigerare. På så sätt kan du integrera med Git-lagringsplatsen, köra och felsöka kod och ändra inställningarna för funktionsappen. Den här redigeraren tillhandahåller en förbättrad utvecklingsmiljö för dina funktioner jämfört med den inbyggda funktionsredigeraren.  

Vi rekommenderar att du utvecklar dina funktioner på din lokala dator. När du utvecklar lokalt och publicerar till Azure är dina projektfiler skrivskyddade i portalen. Mer information finns i [Koda och testa Azure Functions lokalt.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Konsol

![Funktionsappkonsol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsolen i portalen är ett perfekt utvecklarverktyg när du föredrar att interagera med funktionsappen från kommandoraden. Vanliga kommandon är katalog- och filskapande och navigering, samt körning av batchfiler och skript. 

När du utvecklar lokalt rekommenderar vi att du [använder Azure Functions Core Tools](functions-run-local.md) och [Azure CLI.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Avancerade verktyg (Kudu)

![Konfigurera Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De avancerade verktygen för App Service (även kallat Kudu) ger åtkomst till avancerade administrativa funktioner i din funktionsapp. Från Kudu hanterar du systeminformation, appinställningar, miljövariabler, webbplatstillägg, HTTP-huvuden och servervariabler. Du kan också starta **Kudu genom** att bläddra till SCM-slutpunkten för din funktionsapp, till exempel `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Distributionscenter

När du använder en källkontrolllösning för att utveckla och underhålla din funktionskod kan du med Distributionscenter skapa och distribuera från källkontrollen. Projektet byggs och distribueras till Azure när du gör uppdateringar. Mer information finns i [Distributionstekniker i Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin resource sharing (CORS)

För att förhindra körning av skadlig kod på klienten blockerar moderna webbläsare begäranden från webbprogram till resurser som körs i en separat domän. [Resursdelning för korsande ursprung (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) gör att ett huvud kan deklarera vilka ursprung som `Access-Control-Allow-Origin` tillåts anropa slutpunkter i funktionsappen.

#### <a name="portal"></a>Portalen

När du konfigurerar **listan Tillåtna ursprung** för funktionsappen läggs rubriken automatiskt till i alla svar `Access-Control-Allow-Origin` från HTTP-slutpunkter i funktionsappen. 

![Konfigurera funktionsappens CORS-lista](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

När jokertecknet ( `*` ) används ignoreras alla andra domäner. 

Använd kommandot [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add) för att lägga till en domän i listan över tillåtna ursprung. I följande exempel läggs contoso.com domän:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Använd kommandot [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show) för att visa en lista över de aktuella tillåtna ursprungen.

### <a name="authentication"></a><a name="auth"></a>Autentisering

![Konfigurera autentisering för en funktionsapp](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

När funktioner använder en HTTP-utlösare kan du kräva att anrop först autentiseras. App Service har Azure Active Directory för autentisering och inloggning med sociala providers som Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar specifika [autentiseringsproviders finns i Azure App Service översikt över autentisering.](../app-service/overview-authentication-authorization.md) 


## <a name="next-steps"></a>Nästa steg

+ [Konfigurera Azure App Service inställningar](../app-service/configure-common.md)
+ [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure-portalen]: https://portal.azure.com
