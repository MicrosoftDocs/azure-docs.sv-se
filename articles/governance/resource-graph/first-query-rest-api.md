---
title: 'Snabb start: din första REST API fråga'
description: I den här snabb starten följer du stegen för att anropa resurs diagram slut punkten för REST API och kör din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920079"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Snabb start: kör din första resurs diagram fråga med hjälp av REST API

Det första steget till att använda Azure Resource Graph med REST API är att kontrol lera att du har ett verktyg för att anropa REST-API: er tillgängliga. Den här snabb starten vägleder dig genom processen att köra en fråga och hämta resultaten genom att anropa Azure Resource Graph REST API-slutpunkten.

I slutet av den här processen har du verktyg för att anropa REST API slut punkter och köra din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Komma igång med REST API

Om du inte är bekant med REST API börjar du med att gå igenom [referensen för Azure REST API](/rest/api/azure/) så att du förstår hur REST-API:et fungerar, särskilt begärande-URI och begärandetext. I den här artikeln används dessa begrepp för att tillhandahålla anvisningar för att arbeta med Azures resurs diagram och förutsätter att du arbetar med kunskaper om dem. Verktyg som [ARMClient](https://github.com/projectkudu/ARMClient) och andra kan hantera auktorisering automatiskt och rekommenderas för nybörjare.

Information om Azure Resource Graph-specifikationer finns i [Azure Resource graph REST API](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>REST API och PowerShell

Om du inte redan har ett verktyg för att göra REST API-anrop kan du använda PowerShell för dessa anvisningar. Följande kod exempel hämtar ett huvud för autentisering med Azure. Generera ett autentiseringshuvud, även kallat en **ägartoken**, och ange REST API-URI:n som du vill ansluta till med valfria parametrar eller en **begärandetext**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Ersätt `{subscriptionId}` i `$restUri` variabeln för att få information om din prenumeration.
`$response`Variabeln innehåller resultatet av `Invoke-RestMethod` cmdleten, som kan parsas med cmdlet: ar, till exempel [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json). Om REST API-tjänstslutpunkten förväntar sig en **begärandetext** lägger du till en JSON-formaterad variabel till `-Body`-parametern för `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Med REST API verktyg som har lagts till i den miljö du väljer, är det dags att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

I begär ande texten för varje REST API-anrop finns en variabel som du måste ersätta med ditt eget värde:

- `{subscriptionID}` – Ersätt med ditt prenumerations-ID

1. Kör din första Azure Resource Graph-fråga med hjälp av REST API och `resources` slut punkten:

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Begärandetext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Eftersom den här frågan inte tillhandahåller en sorterings modifierare, till exempel `order by` att köra den här frågan flera gånger, är det troligt att en annan uppsättning resurser skapas per begäran.

1. Uppdatera anropet till `resouces` slut punkten och ändra **frågan** till `order by` egenskapen **namn** :

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Begärandetext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Uppdatera anropet till `resources` slut punkten och ändra **frågan** till först `order by` egenskapen **namn** och sedan `limit` till de fem främsta resultaten:

   - REST API-URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Begärandetext

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

Fler exempel på REST API-anrop för Azure Resource Graph finns i [rest-exemplen för Azure Resource Graph](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Rensa resurser

REST API har inga bibliotek eller moduler att avinstallera. Om du har installerat ett verktyg som _ARMClient_ eller _Postman_ för att göra anropen och inte längre behöver det kan du avinstallera verktyget nu.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du anropat resurs diagrammets REST API slut punkt och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
