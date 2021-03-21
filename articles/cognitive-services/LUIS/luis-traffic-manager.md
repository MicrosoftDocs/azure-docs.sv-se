---
title: Öka slut punkts kvoten – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) ger möjlighet att öka kvoten för slut punkts förfrågningar bortom kvoten för en enskild nyckel. Detta görs genom att skapa fler nycklar för LUIS och lägga till dem i LUIS-programmet på sidan **publicera** i avsnittet **resurser och nycklar** .
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609659"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Använd Microsoft Azure Traffic Manager för att hantera slut punkts kvot för nycklar
Language Understanding (LUIS) ger möjlighet att öka kvoten för slut punkts förfrågningar bortom kvoten för en enskild nyckel. Detta görs genom att skapa fler nycklar för LUIS och lägga till dem i LUIS-programmet på sidan **publicera** i avsnittet **resurser och nycklar** .

Klient programmet måste hantera trafiken över nycklarna. LUIS gör det inte.

Den här artikeln förklarar hur du hanterar trafiken över nycklar med Azure [Traffic Manager][traffic-manager-marketing]. Du måste redan ha en utbildad och publicerad LUIS-app. Om du inte har något följer du [snabb](luis-get-started-create-app.md)starten för den inbyggda domänen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ansluta till PowerShell i Azure Portal
Öppna PowerShell-fönstret i [Azure][azure-portal] -portalen. Ikonen för PowerShell-fönstret är **>_** i det övre navigerings fältet. Genom att använda PowerShell från portalen får du den senaste PowerShell-versionen och du är autentiserad. PowerShell i portalen kräver ett [Azure Storage](https://azure.microsoft.com/services/storage/) -konto.

![Skärm bild av Azure Portal med PowerShell-fönster öppet](./media/traffic-manager/azure-portal-powershell.png)

I följande avsnitt används [Traffic Manager PowerShell-cmdletar](/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Skapa en Azure-resurs grupp med PowerShell
Innan du skapar Azure-resurserna skapar du en resurs grupp som innehåller alla resurser. Namnge resurs gruppen `luis-traffic-manager` och Använd regionen `West US` . Regionen i resurs gruppen lagrar metadata om gruppen. Det kommer inte att sakta ned resurserna om de befinner sig i en annan region.

Skapa resurs grupp med cmdleten **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Skapa LUIS-nycklar för att öka den totala slut punkts kvoten
1. I Azure Portal skapar du två **language Understanding** nycklar, en i `West US` och en i `East US` . Använd den befintliga resurs gruppen som skapades i föregående avsnitt med namnet `luis-traffic-manager` .

    ![Skärm bild av Azure Portal med två LUIS-nycklar i resurs gruppen Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. På webbplatsen för [Luis][LUIS] , i avsnittet **Hantera** , på sidan **Azure-resurser** , tilldela du nycklar till appen och publicera appen igen genom att välja knappen **publicera** på menyn längst upp till höger.

    I exempel-URL: en i kolumnen **slut punkt** används en get-begäran med slut punkts nyckeln som frågeparameter. Kopiera de två nya nycklarnas slut punkts-URL: er. De används som en del av Traffic Manager-konfigurationen senare i den här artikeln.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Hantera LUIS-slutpunkts begär anden över nycklar med Traffic Manager
Traffic Manager skapar en ny DNS-åtkomst punkt för dina slut punkter. Den fungerar inte som en gateway eller proxyserver, utan strikt på DNS-nivå. I det här exemplet ändras inte några DNS-poster. Ett DNS-bibliotek används för att kommunicera med Traffic Manager för att få rätt slut punkt för den aktuella begäran. _Varje_ begäran som är avsedd för Luis kräver först en Traffic Manager-begäran för att avgöra vilken Luis-slutpunkt som ska användas.

### <a name="polling-uses-luis-endpoint"></a>Avsökningen använder LUIS-slutpunkt
Traffic Manager avsöker slut punkterna regelbundet för att se till att slut punkten fortfarande är tillgänglig. Den Traffic Manager URL-avsökningen måste vara tillgänglig med en GET-begäran och returnera en 200. Slut punkts-URL: en på **publicerings** sidan gör detta. Eftersom varje slut punkts nyckel har en annan väg och parametrar för frågesträng behöver varje slut punkts nyckel en annan avsöknings väg. Varje gången Traffic Manager avsökningar kostar det en kvot förfrågan. Frågesträngparametern **q** för Luis-slutpunkten är den uttryck som skickas till Luis. Den här parametern, i stället för att skicka en uttryck, används för att lägga till Traffic Manager avsökning till LUIS-slutpunkt-loggen som en fel söknings teknik medan Traffic Manager konfigureras.

Eftersom varje LUIS-slutpunkt behöver sin egen sökväg behöver den sin egen Traffic Manager profil. Skapa en [ _kapslad_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) -arkitektur för att hantera profiler i profiler. En överordnad profil pekar på underordnade profiler och hanterar trafik mellan dem.

När Traffic Manager har kon figurer ATS ska du komma ihåg att ändra sökvägen till att använda parametern Logging = false frågesträng så att loggen inte fylls med avsökningen.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurera Traffic Manager med kapslade profiler
I följande avsnitt skapas två underordnade profiler, en för den östra LUIS-nyckeln och en för den västra LUIS-nyckeln. Sedan skapas en överordnad profil och de två underordnade profilerna läggs till i den överordnade profilen.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Skapa Traffic Managers profil för USA, östra med PowerShell
Det finns flera steg för att skapa en Traffic Manager-profil för östra USA: skapa profil, Lägg till slut punkt och ange slut punkt. En Traffic Manager profil kan ha många slut punkter men varje slut punkt har samma validerings Sök väg. Eftersom LUIS slut punkts-URL: er för de östra och västra prenumerationerna är olika på grund av region och slut punkts nyckel måste varje LUIS-slutpunkt vara en enda slut punkt i profilen.

1. Skapa en profil med cmdleten **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Använd följande cmdlet för att skapa profilen. Se till att ändra `appIdLuis` och `subscriptionKeyLuis` . SubscriptionKey är för den östra amerikanska LUIS-nyckeln. Om sökvägen inte är korrekt, inklusive LUIS-appens ID och slut punkts nyckel, är Traffic Manager avsökningen statusen `degraded` eftersom trafik hanteringen inte kan begära Luis-slutpunkten. Kontrol lera att värdet `q` är `traffic-manager-east` så att du kan se det här värdet i Luis slut punkts loggar.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |-Name|Luis-profil-öst|Traffic Manager namn i Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Traffic Manager metoder för routning][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom ett chattrobot eller något annat program är det chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis – DNS – öst|Detta är under domänen för tjänsten: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Avsöknings intervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Ersätt `<appIdLuis>` och `<subscriptionKeyLuis>` med dina egna värden.|

    En lyckad begäran saknar svar.

2. Lägg till östra USA-slutpunkt med cmdleten **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |– EndpointName|Luis – öst-slut punkt|Slut punkts namn som visas under profilen|
    |-TrafficManagerProfile|$eastprofile|Använd profil objekt som skapades i steg 1|
    |-Typ|ExternalEndpoints|Mer information finns i [Traffic Manager slut punkt][traffic-manager-endpoints] |
    |-Mål|eastus.api.cognitive.microsoft.com|Detta är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|platsen eastus|Slut punktens region|
    |-EndpointStatus|Enabled|Aktivera slut punkt när den skapas|

    Det framgångs bara svaret ser ut så här:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Ange östra USA-slutpunkt med cmdleten **[set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Ett lyckat svar kommer att vara samma svar som steg 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Skapa Traffic Managers profilen västra USA med PowerShell
Om du vill skapa en Traffic Manager-profil för västra USA följer du samma steg: skapa profil, Lägg till slut punkt och ange slut punkt.

1. Skapa en profil med cmdleten **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Använd följande cmdlet för att skapa profilen. Se till att ändra `appIdLuis` och `subscriptionKeyLuis` . SubscriptionKey är för den östra amerikanska LUIS-nyckeln. Om sökvägen inte är korrekt, inklusive LUIS app-ID och slut punkts nyckel, är Traffic Manager avsökningen statusen `degraded` eftersom trafik hanteringen inte kan begära Luis-slutpunkten. Kontrol lera att värdet `q` är `traffic-manager-west` så att du kan se det här värdet i Luis slut punkts loggar.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |-Name|Luis-profil-väst|Traffic Manager namn i Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Traffic Manager metoder för routning][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom ett chattrobot eller något annat program är det chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis-DNS-väst|Detta är under domänen för tjänsten: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Avsöknings intervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Ersätt `<appId>` och `<subscriptionKey>` med dina egna värden. Kom ihåg att den här slut punkts nyckeln skiljer sig från den östra slut punkts nyckeln|

    En lyckad begäran saknar svar.

2. Lägg till västra USA-slutpunkt med cmdleten **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |– EndpointName|Luis – väst-slut punkt|Slut punkts namn som visas under profilen|
    |-TrafficManagerProfile|$westprofile|Använd profil objekt som skapades i steg 1|
    |-Typ|ExternalEndpoints|Mer information finns i [Traffic Manager slut punkt][traffic-manager-endpoints] |
    |-Mål|westus.api.cognitive.microsoft.com|Detta är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|westus|Slut punktens region|
    |-EndpointStatus|Enabled|Aktivera slut punkt när den skapas|

    Det framgångs bara svaret ser ut så här:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Ange västra USA-slutpunkt med cmdleten **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Ett lyckat svar är samma svar som steg 2.

### <a name="create-parent-traffic-manager-profile"></a>Skapa överordnad Traffic Manager profil
Skapa den överordnade Traffic Manager profilen och länka två underordnade Traffic Manager profiler till den överordnade.

1. Skapa en överordnad profil med cmdleten **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |-Name|Luis-profil-överordnad|Traffic Manager namn i Azure Portal|
    |– ResourceGroupName|Luis – Traffic Manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Traffic Manager metoder för routning][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom ett chattrobot eller något annat program är det chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis-DNS-Parent|Detta är under domänen för tjänsten: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Avsöknings intervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/`|Den här sökvägen spelar ingen roll eftersom underordnade slut punkts Sök vägar används i stället.|

    En lyckad begäran saknar svar.

2. Lägg till den underordnade amerikanska USA-profilen till Parent med **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** och **NestedEndpoints** -typ

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |– EndpointName|underordnad-slut punkt-USEast|Öst-profil|
    |-TrafficManagerProfile|$parentprofile|Profil att tilldela slut punkten till|
    |-Typ|NestedEndpoints|Mer information finns i [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– TargetResourceId|$eastprofile. Identitet|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Slut punkts status efter tillägg till överordnad|
    |-EndpointLocation|platsen eastus|Resursens [region namn](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minsta antal underordnade slut punkter|

    Det framgångs bara svaret ser ut så här och innehåller den nya `child-endpoint-useast` slut punkten:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Lägg till en underordnad USA-profil till överordnad med **[Add-AzTrafficManagerEndpointConfig-](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet och **NestedEndpoints** -typ

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Konfigurations parameter|Variabel namn eller värde|Syfte|
    |--|--|--|
    |– EndpointName|underordnad-slut punkt-USWest|Västra profil|
    |-TrafficManagerProfile|$parentprofile|Profil att tilldela slut punkten till|
    |-Typ|NestedEndpoints|Mer information finns i [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |– TargetResourceId|$westprofile. Identitet|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Slut punkts status efter tillägg till överordnad|
    |-EndpointLocation|westus|Resursens [region namn](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minsta antal underordnade slut punkter|

    Det framgångs bara svaret ser ut och inkluderar både den tidigare `child-endpoint-useast` slut punkten och den nya `child-endpoint-uswest` slut punkten:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Ange slut punkter med **[set-AzTrafficManagerProfile-](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Ett lyckat svar är samma svar som steg 3.

### <a name="powershell-variables"></a>PowerShell-variabler
I föregående avsnitt skapades tre PowerShell-variabler: `$eastprofile` , `$westprofile` , `$parentprofile` . Dessa variabler används i slutet av Traffic Managers konfigurationen. Om du väljer att inte skapa variabler, eller glömt till, eller om PowerShell-fönstret har nått tids gränsen, kan du använda PowerShell-cmdleten **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** för att hämta profilen igen och tilldela den till en variabel.

Ersätt objekten i vinkelparenteser, `<>` med rätt värden för var och en av de tre profilerna du behöver.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifiera Traffic Manager fungerar
För att verifiera att Traffic Manager profiler fungerar måste profilerna ha statusen för `Online` den här statusen baserat på slut punktens avsöknings sökväg.

### <a name="view-new-profiles-in-the-azure-portal"></a>Visa nya profiler i Azure Portal
Du kan kontrol lera att alla tre profilerna har skapats genom att titta på resurserna i `luis-traffic-manager` resurs gruppen.

![Skärm bild av Azures resurs grupp Luis – Traffic Manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Kontrol lera att profil statusen är online
Traffic Manager avsöker sökvägen till varje slut punkt för att kontrol lera att den är online. Om det är online är statusen för de underordnade profilerna `Online` . Detta visas i **översikten** över varje profil.

![Skärm bild av översikt över Azure Traffic Manager profil som visar övervaknings status online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Verifiera Traffic Manager avsökningen fungerar
Ett annat sätt att verifiera att Traffic Manager-avsökningen fungerar är med LUIS slut punkts loggar. Exportera slut punkts loggen för programmet på sidan [Luis][LUIS] site Apps List. Eftersom Traffic Manager avsökningar ofta för de två slut punkterna finns det poster i loggarna även om de bara har varit några minuter. Kom ihåg att leta efter poster där frågan börjar med `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Verifiera DNS-svar från Traffic Manager fungerar
Om du vill kontrol lera att DNS-svaret returnerar en LUIS-slutpunkt ska du begära att trafiken hanterar DNS för överordnad profil med ett DNS-klientcertifikat. DNS-namnet för den överordnade profilen är `luis-dns-parent.trafficmanager.net` .

Följande Node.js kod gör en begäran för den överordnade profilen och returnerar en LUIS-slutpunkt:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Ett lyckat svar med LUIS-slutpunkten är:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Använd Traffic Manager överordnade profilen
För att kunna hantera trafik över slut punkter måste du infoga ett anrop till Traffic Manager DNS för att hitta LUIS-slutpunkten. Detta anrop görs för varje LUIS-slutpunkt-begäran och måste simulera den geografiska platsen för användaren av LUIS-klient programmet. Lägg till DNS-svarskod i mellan ditt LUIS-klientprogram och begäran till LUIS för slut punkts förutsägelsen.

## <a name="resolving-a-degraded-state"></a>Lösa ett degraderat tillstånd

Aktivera [diagnostikloggar](../../traffic-manager/traffic-manager-diagnostic-logs.md) för Traffic Manager för att se varför slut punkts status försämras.

## <a name="clean-up"></a>Rensa
Ta bort de två LUIS slut punkts nycklarna, de tre Traffic Manager-profilerna och resurs gruppen som innehöll dessa fem resurser. Detta görs från Azure Portal. Du tar bort de fem resurserna från listan med resurser. Ta sedan bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

Granska alternativ för [mellanprogram](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) i BotFramework v4 för att förstå hur den här Traffic Management-koden kan läggas till i en BotFramework-robot.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md