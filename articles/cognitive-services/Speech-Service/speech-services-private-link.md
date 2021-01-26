---
title: Använda privata slut punkter med tal tjänster
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal tjänster med privata slut punkter från Azures privata länk
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 51989a9219cdbfebf833c99849dba67c939cf77a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786850"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Använda tal tjänster via en privat slut punkt

Med [Azures privata länk](../../private-link/private-link-overview.md) kan du ansluta till tjänster i Azure med hjälp av en [privat slut punkt](../../private-link/private-endpoint-overview.md). En privat slut punkt är en privat IP-adress som endast är tillgänglig inom ett särskilt [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) och undernät.

Den här artikeln förklarar hur du konfigurerar och använder privat länk och privata slut punkter med tal tjänster i Azure Cognitive Services.

> [!NOTE]
> Innan du fortsätter kan du läsa [om hur du använder virtuella nätverk med Cognitive Services](../cognitive-services-virtual-networks.md).

Den här artikeln beskriver också [hur du tar bort privata slut punkter senare, men använder fortfarande tal resursen](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Skapa ett anpassat domän namn

Privata slut punkter kräver ett [anpassat under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). Använd följande instruktioner för att skapa en för din tal resurs.

> [!WARNING]
> En tal resurs med ett anpassat domän namn aktiverat använder ett annat sätt för att interagera med tal tjänster. Du kan behöva justera program koden för båda dessa scenarier: [privat slut punkt aktive rad](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) och [ *inte* privat slut punkt aktive rad](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> När du aktiverar ett anpassat domän namn går det inte att [Ångra](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)åtgärden. Det enda sättet att gå tillbaka till det [regionala namnet](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) är att skapa en ny tal resurs.
>
> Om din tal resurs har många associerade anpassade modeller och projekt som skapats via [tal Studio](https://speech.microsoft.com/)rekommenderar vi starkt att du provar konfigurationen med en test resurs innan du ändrar resursen som används i produktionen.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill skapa ett anpassat domän namn med hjälp av Azure Portal:

1. Gå till [Azure-portalen](https://portal.azure.com/) och logga in på ditt Azure-konto.
1. Välj den nödvändiga tal resursen.
1. I **resurs hanterings** gruppen i det vänstra fönstret väljer du **nätverk**.
1. På fliken **brand väggar och virtuella nätverk** väljer du **skapa anpassat domän namn**. En ny höger panel visas med instruktioner för att skapa en unik anpassad under domän för din resurs.
1. Ange ett anpassat domän namn i panelen **skapa anpassat domän namn** . Din fullständiga anpassade domän ser ut så här: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Kom ihåg att när du har skapat ett anpassat domän namn _kan du inte_ ändra det.
    
    När du har angett ditt anpassade domän namn väljer du **Spara**.
1. När åtgärden har slutförts går du till **resurs hanterings** gruppen och väljer **nycklar och slut punkt**. Bekräfta att det nya slut punkts namnet på resursen startar på det här sättet: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa ett anpassat domän namn med hjälp av PowerShell, kontrollerar du att datorn har PowerShell version 7. x eller senare med Azure PowerShell-modul version 5.1.0 eller senare. Följ dessa steg om du vill se versioner av dessa verktyg:

1. I ett PowerShell-fönster anger du:

    `$PSVersionTable`

    Bekräfta att `PSVersion` värdet är 7. x eller senare. Om du vill uppgradera PowerShell följer du anvisningarna i [installera olika versioner av PowerShell](/powershell/scripting/install/installing-powershell).

1. I ett PowerShell-fönster anger du:

    `Get-Module -ListAvailable Az`

    Om inget visas, eller om den versionen av Azure PowerShell-modulen är tidigare än 5.1.0, följer du anvisningarna i [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps) för att uppgradera.

Innan du fortsätter kan du köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Kontrol lera att det finns ett anpassat domän namn

Kontrol lera om den anpassade domän som du vill använda är tillgänglig. Följande kod bekräftar att domänen är tillgänglig med hjälp av åtgärden [kontrol lera domän tillgänglighet](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) i Cognitive Services REST API.

> [!TIP]
> Följande kod fungerar *inte* i Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Om det önskade namnet är tillgängligt ser du ett svar som detta:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Om namnet redan är upptaget visas följande svar:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Skapa ditt anpassade domän namn

Om du vill aktivera ett anpassat domän namn för den valda tal resursen använder du cmdleten [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> När följande kod har körts kan du skapa ett anpassat domän namn för din tal resurs. Kom ihåg att det här namnet *inte kan* ändras.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Det här avsnittet kräver den senaste versionen av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Kontrol lera att det anpassade domän namnet är tillgängligt

Kontrol lera om den anpassade domän som du vill använda är kostnads fri. Använd metoden för att [kontrol lera domän tillgänglighet](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) från Cognitive Services REST API.

Kopiera följande kodblock, infoga önskat eget domän namn och spara det i filen `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Kopiera filen till din aktuella mapp eller ladda upp den till Azure Cloud Shell och kör följande kommando. Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med ditt Azure-prenumerations-ID.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Om det önskade namnet är tillgängligt ser du ett svar som detta:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Om namnet redan är upptaget visas följande svar:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Aktivera ett anpassat domän namn

Om du vill aktivera ett anpassat domän namn för den valda tal resursen använder du kommandot [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Välj den Azure-prenumeration som innehåller tal resursen. Om ditt Azure-konto bara har en aktiv prenumeration kan du hoppa över det här steget. Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med ditt Azure-prenumerations-ID.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Ange det anpassade domän namnet för den valda resursen. Ersätt exempel parameter värden med de faktiska värdena och kör följande kommando.

> [!WARNING]
> När du har slutfört körningen av följande kommando skapar du ett anpassat domän namn för din tal resurs. Kom ihåg att det här namnet *inte kan* ändras.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Aktivera privata slut punkter

Vi rekommenderar att du använder den [privata DNS-zon](../../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket med nödvändiga uppdateringar för de privata slut punkterna. Du skapar en privat DNS-zon som standard under etablerings processen. Om du använder en egen DNS-server kan du också behöva ändra DNS-konfigurationen. 

Bestäm en DNS-strategi *innan* du etablerar privata slut punkter för en produktions tal resurs. Och testa dina DNS-ändringar, särskilt om du använder en egen DNS-server.

Använd någon av följande artiklar för att skapa privata slut punkter. De här artiklarna använder en webbapp som en exempel resurs som ska aktive ras med privata slut punkter.

- [Skapa en privat slut punkt med hjälp av Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slut punkt med hjälp av Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Skapa en privat slut punkt med hjälp av Azure CLI](../../private-link/create-private-endpoint-cli.md)

Använd dessa parametrar i stället för parametrarna i den artikel som du har valt:

| Inställning             | Värde                                    |
|---------------------|------------------------------------------|
| Resurstyp       | **Microsoft. CognitiveServices/konton** |
| Resurs            | **\<your-speech-resource-name>**         |
| Målunderresurs | **konto**                              |

**DNS för privata slut punkter:** Granska allmänna principer för [DNS för privata slut punkter i Cognitive Services resurser](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Bekräfta sedan att din DNS-konfiguration fungerar korrekt genom att utföra kontrollerna som beskrivs i följande avsnitt.

### <a name="resolve-dns-from-the-virtual-network"></a>Matcha DNS från det virtuella nätverket

Den här kontrollen *krävs*.

Följ de här stegen för att testa den anpassade DNS-posten från det virtuella nätverket:

1. Logga in på en virtuell dator som finns i det virtuella nätverk som du har kopplat din privata slut punkt till. 
1. Öppna en kommando tolk i Windows eller ett bash-gränssnitt, kör `nslookup` och bekräfta att det har löst in resursens anpassade domän namn.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Bekräfta att IP-adressen matchar den privata slut punktens IP-adress.

### <a name="resolve-dns-from-other-networks"></a>Matcha DNS från andra nätverk

Utför bara den här kontrollen om du har aktiverat alternativet **alla nätverk** eller **markerade nätverk och åtkomst till privata slut punkter** i avsnittet **nätverk** i resursen. 

Om du planerar åtkomst till resursen genom att bara använda en privat slut punkt kan du hoppa över det här avsnittet.

1. Logga in på en dator som är ansluten till ett nätverk som har behörighet att komma åt resursen.
2. Öppna en kommando tolk i Windows eller bash-gränssnittet, kör `nslookup` och bekräfta att den matchar resursens anpassade domän namn.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

3. Bekräfta att IP-adressen matchar den privata slut punktens IP-adress.

> [!NOTE]
> Den matchade IP-adressen pekar på en slut punkt för en virtuell nätverks-proxy, som skickar nätverks trafiken till den privata slut punkten för Cognitive Services resursen. Beteendet skiljer sig åt för en resurs med ett anpassat domän namn men *utan* privata slut punkter. Se [det här avsnittet](#dns-configuration) för mer information.

## <a name="adjust-existing-applications-and-solutions"></a>Justera befintliga program och lösningar

En tal resurs med en anpassad domän aktive rad använder ett annat sätt för att interagera med tal tjänster. Detta gäller för en anpassad tal resurs med en anpassad domän både med och utan privata slut punkter. Informationen i det här avsnittet gäller för båda scenarierna.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Använd en tal resurs med ett anpassat domän namn och en privat slut punkt aktive rad

En tal resurs med ett anpassat domän namn och en privat slut punkt aktive rad använder ett annat sätt för att interagera med tal tjänster. I det här avsnittet beskrivs hur du använder en sådan resurs med API: er för tal tjänster och [tal-SDK](speech-sdk.md).

> [!NOTE]
> En tal resurs utan privata slut punkter men med ett anpassat domän namn aktiverat har också ett särskilt sätt att interagera med tal tjänster. Det här sättet skiljer sig från scenariot för en privat-slutpunkt-aktiverad tal resurs. Om du har en sådan resurs (till exempel om du har en resurs med privata slut punkter, men beslutat att ta bort dem), kan du läsa avsnittet [använda en tal resurs med ett anpassat domän namn och utan privata slut punkter](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Tal resurs med ett anpassat domän namn och en privat slut punkt: användning med REST API: er

Vi ska använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resurs DNS-namn (anpassad domän) för det här avsnittet.

Tal tjänster har REST-API: er för [tal till text](rest-speech-to-text.md) och [text till tal](rest-text-to-speech.md). Tänk på följande information för scenariot för privat slut punkts aktivering.

Tal till text har två REST-API: er. Varje API fungerar på ett annat sätt, använder olika slut punkter och kräver en annan metod när du använder den i scenariot för privat slut punkts aktiverat.

REST-API: er från tal till text är:
- [Tal-till-Text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), som används för [batch-avskrifter](batch-transcription.md) och [Custom Speech](custom-speech-overview.md). v 3.0 är en [efterföljande till v 2.0](./migrate-v2-to-v3.md)
- [Tal-till-text-REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), som används för online-avskrift 

Användning av tal-till-text-REST API för kort ljud och text till tal-REST API i det privata slut punkts scenariot är detsamma. Det motsvarar det [tal SDK-fall](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) som beskrivs längre fram i den här artikeln. 

Tal-till-text REST API v 3.0 använder en annan uppsättning slut punkter, så den kräver en annan metod för scenariot med privat slut punkts aktiverat.

I nästa avsnitt beskrivs båda fallen.

##### <a name="speech-to-text-rest-api-v30"></a>Tal till text REST API v 3.0

Vanligt vis använder tal resurser [Cognitive Services regionala slut punkter](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) för att kommunicera med [tal-till-text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Dessa resurser har följande namngivnings format: <p/>`{region}.api.cognitive.microsoft.com`.

Detta är en URL för exempel förfrågan:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
När du har aktiverat en anpassad domän för en tal resurs (vilket är nödvändigt för privata slut punkter) kommer den resursen att använda följande DNS-namn mönster för den grundläggande REST API slut punkten: <p/>`{your custom name}.cognitiveservices.azure.com`.

Det innebär i vårt exempel att namnet på REST API-slutpunkten är: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

URL: en för begäran måste konverteras till:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Den här URL: en ska kunna användas från det virtuella nätverket med den privata slut punkten ansluten (förutsatt att du har [rätt DNS-matchning](#resolve-dns-from-the-virtual-network)).

När du har aktiverat ett anpassat domän namn för en tal resurs ersätter du normalt värd namnet i alla begär ande-URL: er med det nya anpassade domän värd namnet. Alla andra delar av begäran (t. ex. sökvägen `/speechtotext/v3.0/transcriptions` i det tidigare exemplet) förblir desamma.

> [!TIP]
> Vissa kunder utvecklar program som använder region delen av den regionala slut punktens DNS-namn (till exempel för att skicka begäran till den tal resurs som distribueras i en viss Azure-region).
>
> En anpassad domän för en tal resurs innehåller *ingen* information om den region där resursen distribueras. Den program logik som beskrivits tidigare fungerar *inte* och måste ändras.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Tal-till-text-REST API för kort ljud-och text till tal-REST API

[Tal-till-text-REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) och [text till tal-REST API](rest-text-to-speech.md) använder två typer av slut punkter:
- [Cognitive Services regionala slut punkter](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) för att kommunicera med Cognitive Services-REST API för att hämta en autentiseringstoken
- Särskilda slut punkter för alla andra åtgärder

Den detaljerade beskrivningen av särskilda slut punkter och hur deras URL ska transformeras för en privat slut punkts aktive rad tal resurs finns i [det här underavsnittet](#general-principles) om användning med talet SDK. Samma princip som beskrivs för SDK gäller för tal-till-text REST API v 1.0 och text till tal-REST API.

Bekanta dig med materialet i underavsnittet som nämns i föregående stycke och se följande exempel. Exemplet beskriver text-till-tal-REST API. Användning av tal-till-text-REST API för kort ljud är helt likvärdig.

> [!NOTE]
> När du använder tal-till-text-REST API för kort ljud i scenarier med privata slut punkter, använder du en autentiseringstoken som [skickas via](rest-speech-to-text.md#request-headers) `Authorization` [rubriken](rest-speech-to-text.md#request-headers). Att skicka en röst prenumerations nyckel till den särskilda slut punkten via `Ocp-Apim-Subscription-Key` rubriken fungerar *inte* och kommer att generera fel 401.

**Exempel på text till tal-REST API användning**

Vi använder västra Europa som ett exempel på en Azure-region och `my-private-link-speech.cognitiveservices.azure.com` som ett exempel på tal resursens DNS-namn (anpassad domän). Det anpassade domän namnet `my-private-link-speech.cognitiveservices.azure.com` i vårt exempel tillhör tal resursen som skapats i regionen Europa, västra.

Om du vill hämta en lista över de röster som stöds i regionen gör du följande två åtgärder:

- Hämta en autentiseringstoken:
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- Hämta listan över röster genom att använda token:
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
Se mer information om föregående steg i [text till tal REST API-dokumentationen](rest-text-to-speech.md).

För den privat-slutpunkt-aktiverade tal resursen måste slut punkts-URL: er för samma åtgärd ändras. Samma sekvens kommer att se ut så här:

- Hämta en autentiseringstoken:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  Se den detaljerade förklaringen i det tidigare avsnittet om [tal-till-Text REST API v 3.0](#speech-to-text-rest-api-v30) .

- Hämta listan över röster genom att använda den hämtade token:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  Se en detaljerad förklaring i underavsnittet [allmänna principer](#general-principles) för tal-SDK.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Tal resurs med ett anpassat domän namn och en privat slut punkt: användning med talet SDK

Genom att använda tal-SDK med ett anpassat domän namn och en privat slut punkts aktive rad tal resurser måste du granska och troligt vis ändra program koden.

Vi ska använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resurs DNS-namn (anpassad domän) för det här avsnittet.

##### <a name="general-principles"></a>Allmänna principer

Normalt i SDK-scenarier (och i REST API scenarier med text till tal) använder tal resurser de dedikerade regionala slut punkterna för olika tjänst erbjudanden. DNS-namn formatet för dessa slut punkter är:

`{region}.{speech service offering}.speech.microsoft.com`

Ett exempel på ett DNS-namn är:

`westeurope.stt.speech.microsoft.com`

Alla möjliga värden för regionen (första elementet i DNS-namnet) visas i regioner som [stöds av Speech service](regions.md). I följande tabell visas möjliga värden för tal tjänst erbjudandet (andra elementet i DNS-namnet):

| DNS-namn värde | Tal tjänst erbjudande                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Anpassade kommandon](custom-commands.md)                       |
| `convai`       | [Konversations avskrift](conversation-transcription.md) |
| `s2s`          | [Talöversättning](speech-translation.md)                 |
| `stt`          | [Tal till text](speech-to-text.md)                         |
| `tts`          | [Text till tal](text-to-speech.md)                         |
| `voice`        | [Anpassad röst](how-to-custom-voice.md)                      |

Det tidigare exemplet ( `westeurope.stt.speech.microsoft.com` ) står för en slut punkt för tal till text i Västeuropa.

Slut punkter som är aktiverade för privata slut punkter och kommunicerar med tal tjänster via en speciell proxy. Därför *måste du ändra URL: en för slut punkts anslutningen*. 

En slut punkts-URL för "standard" ser ut så här: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

En privat slut punkts-URL ser ut så här: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exempel 1.** Ett program kommunicerar med hjälp av följande URL (tal igenkänning med hjälp av bas modellen för amerikansk engelska i Västeuropa):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Om du vill använda det i scenariot för privat slut punkts aktive rad när det anpassade domän namnet för tal resursen är `my-private-link-speech.cognitiveservices.azure.com` , måste du ändra URL: en så här:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Lägg märke till detaljerna:

- Värd namnet `westeurope.stt.speech.microsoft.com` har ersatts av namnet på den anpassade domän värden `my-private-link-speech.cognitiveservices.azure.com` .
- Det andra elementet i det ursprungliga DNS-namnet ( `stt` ) blir det första elementet i URL-sökvägen och föregår den ursprungliga sökvägen. Så den ursprungliga URL: en `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` blir `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exempel 2.** Ett program använder följande URL för att syntetisera tal i Västeuropa med hjälp av en anpassad röst modell:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Följande motsvarande URL använder en privat slut punkt som är aktive rad, där det anpassade domän namnet för tal resursen är `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Samma princip i exempel 1 tillämpas, men nyckel elementet den här gången är `voice` .

##### <a name="modifying-applications"></a>Ändra program

Följ dessa steg om du vill ändra koden:

1. Fastställ programmets slut punkts-URL:

   - [Aktivera loggning för ditt program](how-to-use-logging.md) och kör det för att logga aktiviteten.
   - Sök efter i logg filen `SPEECH-ConnectionUrl` . I matchande rader `value` innehåller parametern den fullständiga URL som programmet använde för att uppnå tal tjänster.

   Exempel:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   URL: en för programmet som används i det här exemplet är:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Skapa en `SpeechConfig` instans med hjälp av en fullständig slut punkts-URL:

   1. Ändra slut punkten som du nyss fastställde enligt beskrivningen i avsnittet tidigare [allmänna principer](#general-principles) .

   1. Ändra hur du skapar instansen av `SpeechConfig` . Förmodligen använder programmet något som liknar detta:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Detta fungerar inte för en privat slut punkts aktive rad tal resurs på grund av värdnamn och URL-ändringar som vi beskrivit i föregående avsnitt. Om du försöker köra ditt befintliga program utan några ändringar med hjälp av nyckeln för en privat-slutpunkt-aktiverad resurs får du ett autentiseringsfel (401).

      Om du vill göra det kan du ändra hur du instansierar `SpeechConfig` klassen och använder "från slut punkten"/"med initiering av slut punkten". Anta att vi har följande två variabler definierade:
      - `subscriptionKey` innehåller nyckeln för den privata-slutpunktbaserade tal resursen.
      - `endPoint` innehåller den fullständiga *ändrade* slut punkts-URL: en (med den typ som krävs av motsvarande programmeringsspråk). I vårt exempel ska den här variabeln innehålla:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Skapa en `SpeechConfig` instans:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> De frågeparametrar som anges i slut punkts-URI: n ändras inte, även om de har angetts av andra API: er. Om igenkännings språket exempelvis definieras i URI som frågeparameter `language=en-US` och även har angetts till `ru-RU` via motsvarande egenskap, används språk inställningen i URI: n. Det effektiva språket är sedan `en-US` .
>
> Parametrar som anges i slut punkts-URI: n har alltid företräde. Andra API: er kan åsidosätta endast parametrar som inte har angetts i slut punkts-URI: n.

Efter den här ändringen bör ditt program fungera med de tal resurser som är aktiverade för privat slut punkt. Vi arbetar på mer sömlöst stöd för privata slut punkts scenarier.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Använda en tal resurs med ett anpassat domän namn och utan privata slut punkter

I den här artikeln har vi påpekat flera gånger som aktiverar en anpassad domän för en tal resurs kan inte *ångras*. En sådan resurs kommer att använda ett annat sätt att kommunicera med tal tjänster, jämfört med de som använder [regionala slut punkts namn](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

I det här avsnittet beskrivs hur du använder en tal resurs med ett aktiverat anpassat domän namn men *utan* privata slut punkter med tal tjänsterna REST-API: er och [tal-SDK](speech-sdk.md). Detta kan vara en resurs som har använts i ett privat slut punkts scenario, men som tog bort sina privata slut punkter.

#### <a name="dns-configuration"></a>DNS-konfiguration

Kom ihåg hur ett anpassat domän-DNS-namn för den privata-slutpunktbaserade tal resursen [löses från offentliga nätverk](#resolve-dns-from-other-networks). I det här fallet pekar IP-adressen på en proxy-slutpunkt för ett virtuellt nätverk. Den här slut punkten används för att skicka nätverks trafiken till den privata slut punkts-aktiverade Cognitive Services resursen.

Men när *alla* privata slut punkter tas bort (eller direkt efter aktiveringen av det anpassade domän namnet), etableras CNAME-posten för tal resursen om. Den pekar nu på IP-adressen till motsvarande [Cognitive Services regionala slut punkt](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Då `nslookup` ser kommandots utdata att se ut så här:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Jämför den med utdata från [det här avsnittet](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Tal resurs med ett anpassat domän namn och utan privata slut punkter: användning med REST-API: er

##### <a name="speech-to-text-rest-api-v30"></a>Tal till text REST API v 3.0

Användning av tal-till-text REST API v 3.0 är helt likvärdig med fallet för de [tal resurser som är aktiverade för privata slut punkter](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Tal-till-text-REST API för kort ljud-och text till tal-REST API

I det här fallet har användningen av tal-till-text-REST API för kort ljud och användning av text till tal-REST API inga skillnader i det allmänna fallet, med ett undantag för tal-till-text-REST API för kort ljud. (Se följande anmärkning.) Du bör använda båda API: erna enligt beskrivningen i [tal-till-text-REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) [-och text till tal-REST API](rest-text-to-speech.md) -dokumentation.

> [!NOTE]
> När du använder tal-till-text-REST API för kort ljud i anpassade domän scenarier, använder du en autentiseringstoken som [skickas via](rest-speech-to-text.md#request-headers) en `Authorization` [rubrik](rest-speech-to-text.md#request-headers). Att skicka en röst prenumerations nyckel till den särskilda slut punkten via `Ocp-Apim-Subscription-Key` rubriken fungerar *inte* och kommer att generera fel 401.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Tal resurs med ett anpassat domän namn och utan privata slut punkter: användning med talet SDK

Användning av tal-SDK: n med anpassade domän aktiverade tal resurser *utan* privata slut punkter kräver granskning av och sannolika ändringar i program koden. Observera att dessa ändringar skiljer sig från fallet med en [privat slut punkts aktive rad tal resurs](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk). Vi arbetar på mer sömlöst stöd för privata slut punkter och anpassade domän scenarier.

Vi ska använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resurs DNS-namn (anpassad domän) för det här avsnittet.

I avsnittet om [privat slut punkts aktiverade tal resurser](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)förklarades hur du fastställer slut punkts-URL: en, ändrar den och gör att den fungerar via "från slut punkt"/"med slut punkt"-initiering av `SpeechConfig` klass instansen.

Men om du försöker köra samma program efter att alla privata slut punkter har tagits bort (vilket innebär att en tid för motsvarande DNS-postetablering) uppstår får du ett internt tjänst fel (404). Orsaken är att [DNS-posten](#dns-configuration) nu pekar på den regionala Cognitive Services slut punkten i stället för den virtuella Nätverksgatewayen, och att URL-sökvägar som `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` inte hittas där hittas.

Om du återställer programmet till standard-Instansieringen av `SpeechConfig` i följande kod kommer ditt program att avslutas med autentiseringsfel (401):

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>Ändra program

Följ dessa steg om du vill att programmet ska använda en tal resurs med ett anpassat domän namn och utan privata slut punkter:

1. Begär en autentiseringstoken från Cognitive Services REST API. [Den här artikeln](../authentication.md#authenticate-with-an-authentication-token) visar hur du hämtar token.

   Använd det anpassade domän namnet i slut punkts-URL: en. I vårt exempel är denna URL:
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > Du kan hitta denna URL i Azure Portal. Välj **nycklar och slut punkt** under **resurs hanterings** gruppen på din tal resurs sida.

1. Skapa en `SpeechConfig` instans med hjälp av autentiseringstoken som du fick i föregående avsnitt. Anta att vi har följande variabler definierade:

   - `token`: autentiseringstoken som hämtades i föregående avsnitt
   - `azureRegion`: namnet på tal resurs [regionen](regions.md) (exempel: `westeurope` )
   - `outError`: (endast för [mål C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) -fall)

   Skapa en `SpeechConfig` instans så här:

   ```csharp
   var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
   ```
   ```cpp
   auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
   ```
   ```java
   SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
   ```
   ```python
   import azure.cognitiveservices.speech as speechsdk
   speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
   ```
   ```objectivec
   SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
   ```
> [!NOTE]
> Anroparen måste se till att autentiseringstoken är giltig. Innan token för auktorisering upphör att gälla måste anroparen uppdatera den genom att anropa den här setter med en ny giltig token. Eftersom konfigurations värden kopieras när du skapar en ny tolk eller synthesizer, gäller inte det nya token-värdet för identifierare eller synthesizers som redan har skapats.
>
> För dessa ställer du in autentiseringstoken för den aktuella tolken eller Synthesizern för att uppdatera token. Om du inte uppdaterar token kommer tolken eller Synthesizern att upptäcka fel vid drift.

Efter den här ändringen bör ditt program fungera med tal resurser som använder ett anpassat domän namn utan privata slut punkter.

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Läs mer

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Tal till text-REST API](rest-speech-to-text.md)
* [Text till tal-REST API](rest-text-to-speech.md)