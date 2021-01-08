---
title: Använda privata slut punkter med tal tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal tjänsten med privata slut punkter från Azures privata länk
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018538"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Använda tal tjänsten via en privat slut punkt

Med [Azures privata länk](../../private-link/private-link-overview.md) kan du ansluta till tjänster i Azure med en [privat slut punkt](../../private-link/private-endpoint-overview.md).
En privat slut punkt är en privat IP-adress som endast är tillgänglig i ett särskilt [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) och undernät.

I den här artikeln förklaras hur du konfigurerar och använder privat länk och privata slut punkter med Azure kognitiva tal tjänster.

> [!NOTE]
> I den här artikeln förklaras hur du konfigurerar och använder en privat länk med Azure kognitiva tal tjänster. Innan du fortsätter bör du gå igenom hur du [använder virtuella nätverk med Cognitive Services](../cognitive-services-virtual-networks.md).

Utför följande uppgifter för att använda en röst tjänst via en privat slut punkt:

1. [Skapa anpassat domän namn för tal resurs](#create-a-custom-domain-name)
2. [Skapa och konfigurera privata slut punkter](#enable-private-endpoints)
3. [Justera befintliga program och lösningar](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Om du vill ta bort privata slut punkter senare, men fortfarande använda tal resursen, ska du utföra de uppgifter som finns i [det här avsnittet](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Skapa ett anpassat domän namn

Privata slut punkter kräver ett [Cognitive Services anpassat under domän namn](../cognitive-services-custom-subdomains.md). Följ instruktionerna nedan för att skapa en för din tal resurs.

> [!WARNING]
> En tal resurs med anpassat domän namn aktiverat använder ett annat sätt för att interagera med tal tjänsten.
> Du måste förmodligen justera program koden för både [privat slut punkt som är aktive rad](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) och [ **inte** privat slut punkts aktiverade](#use-speech-resource-with-custom-domain-name-without-private-endpoints) scenarier.
>
> När du aktiverar ett anpassat domän namn går det inte att [**Ångra**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)åtgärden. Det enda sättet att gå tillbaka till det [regionala namnet](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) är att skapa en ny tal resurs.
>
> Om din tal resurs har många associerade anpassade modeller och projekt som skapats via [tal Studio](https://speech.microsoft.com/) rekommenderar vi **starkt** att du provar konfigurationen med en test resurs innan du ändrar resursen som används i produktionen.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill skapa ett anpassat domän namn med hjälp av Azure Portal:

1. Gå till [Azure Portal](https://portal.azure.com/) och logga in på ditt Azure-konto.
1. Välj den nödvändiga tal resursen.
1. I **resurs hanterings** gruppen i det vänstra navigerings fönstret klickar du på **nätverk**.
1. I fliken **brand väggar och virtuella nätverk** klickar du på **skapa anpassat domän namn**. En ny höger panel visas med instruktioner för att skapa en unik anpassad under domän för din resurs.
1. I panelen skapa anpassat domän namn anger du en anpassad domän namns del. Din fullständiga anpassade domän ser ut så här: `https://{your custom name}.cognitiveservices.azure.com` . 
    **När du har skapat ett anpassat domän namn _kan du inte_ ändra det! Läs varnings aviseringen igen ovan.** När du har angett ditt anpassade domän namn klickar du på **Spara**.
1. När åtgärden har slutförts klickar du på **nycklar och slut punkt** i gruppen **resurs hantering** . Bekräfta att det nya slut punkts namnet på resursen startar på det här sättet:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill skapa ett anpassat domän namn med hjälp av PowerShell, kontrollerar du att datorn har PowerShell version 7. x eller senare med Azure PowerShell-modul version 5.1.0 eller senare. Följ dessa steg om du vill se versioner av dessa verktyg:

1. I ett PowerShell-fönster skriver du:

    `$PSVersionTable`

    Bekräfta att PSVersion-värdet är större än 7. x. Uppgradera PowerShell genom att följa anvisningarna i [installera olika versioner av PowerShell](/powershell/scripting/install/installing-powershell) för att uppgradera.

1. I ett PowerShell-fönster skriver du:

    `Get-Module -ListAvailable Az`

    Om inget visas, eller om Azure PowerShell-modulens version är lägre än 5.1.0, följer du anvisningarna i [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps) för att uppgradera.

Innan du fortsätter måste `Connect-AzAccount` du köra för att skapa en anslutning till Azure.

## <a name="verify-custom-domain-name-is-available"></a>Kontrol lera att det anpassade domän namnet är tillgängligt

Kontrol lera om den anpassade domänen som du vill använda är tillgänglig. Följ dessa steg för att bekräfta att domänen är tillgänglig med hjälp av åtgärden [kontrol lera domän tillgänglighet](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) i Cognitive Services REST API.

> [!TIP]
> Koden nedan fungerar **inte** i Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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
Om det önskade namnet är tillgängligt visas ett svar som detta:
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

Vi använder [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) -cmdlet för att aktivera anpassat domän namn för den valda tal resursen.

> [!WARNING]
> När koden nedan har körts skapar du ett anpassat domän namn för din tal resurs.
> Det **går inte** att ändra det här namnet. Se mer information i **varnings** varningen ovan.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
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

- Det här avsnittet kräver den senaste versionen av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="verify-the-custom-domain-name-is-available"></a>Kontrol lera att det anpassade domän namnet är tillgängligt

Kontrol lera om den anpassade domänen som du vill använda är kostnads fri. Vi kommer att använda [kontrol lera domän tillgänglighets](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) metod från Cognitive Services REST API.

Kopiera kod blocket nedan, infoga önskat eget domän namn och spara det i filen `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Kopiera filen till din aktuella mapp eller ladda upp den till Azure Cloud Shell och kör följande kommando. (Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med ditt Azure-prenumerations-ID).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Om det önskade namnet är tillgängligt visas ett svar som detta:
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
## <a name="enable-custom-domain-name"></a>Aktivera anpassat domän namn

För att aktivera det anpassade domän namnet för den valda tal resursen använder vi kommandot [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Välj den Azure-prenumeration som innehåller tal resurs. Om ditt Azure-konto bara har en aktiv prenumeration kan du hoppa över det här steget. (Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med ditt Azure-prenumerations-ID).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Ange det anpassade domän namnet för den valda resursen. Ersätt exempel parameter värden med de faktiska värdena och kör kommandot nedan.

> [!WARNING]
> När du har genomfört kommandot nedan skapar du ett anpassat domän namn för din tal resurs. Det **går inte** att ändra det här namnet. Mer information finns i varningen ovan.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Aktivera privata slut punkter

Vi rekommenderar att du använder den [privata DNS-zon](../../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket med nödvändiga uppdateringar för de privata slut punkterna, som vi skapar som standard under etablerings processen. Men om du använder en egen DNS-server kan du också behöva ändra DNS-konfigurationen, som du ser i _DNS för privata slut punkter_ nedan. Bestäm DNS-strategin _ *före** etablering av privata slut punkter för en produktions tal resurs och testa dina DNS-ändringar, särskilt om du använder en egen DNS-server.

Använd någon av följande artiklar för att skapa privata slut punkter. Artiklarna använder en webbapp som en exempel resurs som ska aktive ras med privata slut punkter. Du kommer att använda dessa parametrar i stället för dem i artikeln:

| Inställning             | Värde                                    |
|---------------------|------------------------------------------|
| Resurstyp       | **Microsoft. CognitiveServices/konton** |
| Resurs            | **\<your-speech-resource-name>**         |
| Målunderresurs | **konto**                              |

- [Skapa en privat slutpunkt med hjälp av Azure-portalen](../../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slut punkt med hjälp av Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Skapa en privat slut punkt med Azure CLI](../../private-link/create-private-endpoint-cli.md)

**DNS för privata slut punkter:** Granska allmänna principer för [DNS för privata slut punkter i Cognitive Services resurser](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Bekräfta sedan att din DNS-konfiguration fungerar som den ska genom att utföra följande kontroller:

### <a name="resolve-dns-from-the-virtual-network"></a>Matcha DNS från det virtuella nätverket

Den här kontrollen **krävs**.

Följ dessa steg om du vill testa den anpassade DNS-posten från det virtuella nätverket.

1. Logga in på en virtuell dator som finns i det virtuella nätverk som du har kopplat din privata slut punkt till. 
1. Öppna kommando tolken i Windows eller bash-gränssnittet, kör `nslookup` och bekräfta att det har löst resurs namnet för din resurs.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Bekräfta att IP-adressen matchar den privata slut punktens IP-adress.

### <a name="resolve-dns-from-other-networks"></a>Matcha DNS från andra nätverk

Utför bara den här kontrollen om du planerar att använda din privata språk resurs för slut punkter i hybrid läge, där du har aktiverat alternativet **alla nätverk** eller **valda nätverk och åtkomst till privata slut punkter** i avsnittet **nätverk** i resursen. Om du planerar åtkomst till resursen med endast en privat slut punkt kan du hoppa över det här avsnittet.

1. Logga in på en dator som är ansluten till ett nätverk som har behörighet att komma åt resursen.
2. Öppna kommando tolken i Windows eller bash-gränssnittet, kör `nslookup` och bekräfta att det har löst resurs namnet för din resurs.

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

En tal resurs med en anpassad domän aktive rad använder ett annat sätt för att interagera med tal tjänster. Detta gäller för en anpassad domän som är aktive rad i tal resurser både med och utan privata slut punkter. Informationen i det här avsnittet gäller för båda scenarierna.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Använd tal resurs med anpassat domän namn och privat slut punkt aktive rad

En tal resurs med anpassat domän namn och privat slut punkt aktive rad använder ett annat sätt för att interagera med tal tjänster. I det här avsnittet beskrivs hur du använder en sådan resurs med Speech Services REST API och [Speech SDK](speech-sdk.md).

> [!NOTE]
> Observera att en tal resurs utan privata slut punkter, men med **anpassat domän namn** aktiverat också är ett särskilt sätt att interagera med tal tjänster, men det här sättet skiljer sig från scenariot med en aktive rad tal resurs i en privat slut punkt. Om du har en sådan resurs (anta att du hade en resurs med privata slut punkter, men beslutat att ta bort dem), se till att bekanta dig med [avsnittet](#use-speech-resource-with-custom-domain-name-without-private-endpoints)mottagare.

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Tal resurs med eget domän namn och privat slut punkt. Användning med REST API

Vi kommer att använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resursens DNS-namn (anpassad domän) för det här avsnittet.

##### <a name="note-on-speech-services-rest-api"></a>Observera om tal tjänster REST API

Tal tjänster har REST API för [tal till text](rest-speech-to-text.md) och [text till tal](rest-text-to-speech.md). Följande måste beaktas för scenariot för privat slut punkt aktiverat.

Tal till text har två olika REST-API: er. Varje API fungerar på ett annat sätt, använder olika slut punkter och kräver en annan metod när du använder enkel inloggning i scenariot för privat slut punkt aktiverat.

REST-API: er från tal till text är:
- [Tal-till-text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) används för [batch-avskrifter](batch-transcription.md) och [Custom Speech](custom-speech-overview.md). v 3.0 är en [efterföljande aktivitet för v 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [Tal-till-text-REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) används för att avskrifta online. 

Användning av tal-till-text-REST API för kort ljud-och text till tal-REST API i det privata slut punkt scenariot är detsamma och motsvarande [tal SDK-fall](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) som beskrivs längre fram i den här artikeln. 

Tal-till-text REST API v 3.0 använder en annan uppsättning slut punkter och kräver därför en annan metod för scenariot med privat slut punkt aktiverat.

Båda fallen beskrivs i nästa avsnitt.


##### <a name="speech-to-text-rest-api-v30"></a>Tal till text REST API v 3.0

Normalt använder tal resurser [Cognitive Services regionala slut punkter](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) för att kommunicera med [tal-till-text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Dessa resurser har följande namngivnings format: <p/>`{region}.api.cognitive.microsoft.com`

Detta är en URL för exempel förfrågan:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
När du har aktiverat en anpassad domän för en tal resurs (vilket krävs för privata slut punkter) kommer sådan resurs att använda följande DNS-namn mönster för den grundläggande REST API slut punkten: <p/>`{your custom name}.cognitiveservices.azure.com`

Det innebär att REST API slut punktens namn i vårt exempel är: <p/>`my-private-link-speech.cognitiveservices.azure.com`

Och URL: en för begäran ovan måste konverteras till:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Den här URL: en ska kunna användas från det virtuella nätverket med den privata slut punkten ansluten (förutsatt att du har [rätt DNS-matchning](#resolve-dns-from-the virtual-network)).

Normalt när du har aktiverat anpassat domän namn för en tal resurs ersätter du hostname i alla begär ande-URL: er med det nya anpassade domän namnet. Alla andra delar av begäran (t. ex. sökvägen `/speechtotext/v3.0/transcriptions` i exemplet ovan) förblir desamma.

> [!TIP]
> Vissa kunder utvecklar program som använder region delen av DNS-namnet för den regionala slut punkten (till exempel för att skicka begäran till den tal resurs som distribuerats i en viss Azure-region).
>
> Anpassat domän namn för tal resurs innehåller **ingen** information om den region där resursen har distribuerats. Den program logik som beskrivs ovan kommer **inte** att fungera och måste ändras.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Tal-till-text-REST API för kort ljud-och text till tal-REST API

[Tal-till-text-REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) [-och text till tal-REST API](rest-text-to-speech.md) använder två typer av slut punkter:
- [Cognitive Services regionala slut punkter](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) för att kommunicera med Cognitive Services-REST API för att hämta en autentiseringstoken
- Särskilda slut punkter för alla andra åtgärder

Den detaljerade beskrivningen av särskilda slut punkter och hur deras URL ska transformeras för en privat slut punkt som är aktive rad i [den här underavsnittet](#general-principle) i avsnittet "användning med tal-SDK" nedan. Samma princip som beskrivs för SDK gäller för tal-till-text v 1.0 och text till tal-REST API.

Bekanta dig med materialet i underavsnittet som nämns i föregående stycke och se följande exempel. (Exemplet beskriver text-till-tal-REST API; användningen av tal-till-text-REST API för kort ljud är helt likvärdig)

> [!NOTE]
> När **du använder tal-till-text-REST API för kort ljud** i scenarier med privata slut punkter, använder du en autentiseringstoken som [skickas via](rest-speech-to-text.md#request-headers) `Authorization` [sidhuvud](rest-speech-to-text.md#request-headers). Överföring av tal prenumerations nyckel till den särskilda slut punkten via `Ocp-Apim-Subscription-Key` huvud kommer **inte** att fungera och kommer att generera fel 401.

**Exempel på text till tal-REST API användning.**

Vi använder västra Europa som ett exempel på en Azure-region och `my-private-link-speech.cognitiveservices.azure.com` som ett exempel på tal resursens DNS-namn (anpassad domän). Det anpassade domän namnet `my-private-link-speech.cognitiveservices.azure.com` i vårt exempel tillhör tal resursen som skapats i regionen Europa, västra.

Om du vill hämta en lista över de röster som stöds i regionen som en behöver göra följande två åtgärder:

- Hämta autentiseringstoken:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Hämta listan över röster med hjälp av token:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Se mer information om stegen ovan i [text till tal REST API-dokumentation](rest-text-to-speech.md))

För den privata slut punkten Enabled Speech-resurs måste slut punkts-URL: er för samma åtgärd ändras. Samma sekvens kommer att se ut så här:
- Hämta autentiseringstoken via
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(se detaljerad förklaring i underavsnittet [om tal-till-text REST API v 3.0](#speech-to-text-rest-api-v30) ovan)
- Hämta listan över röster med hjälp av den hämtade token via
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(mer information [finns i avsnittet](#general-principle) "användning med tal-SDK" nedan)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Tal resurs med eget domän namn och privat slut punkt. Användning med tal-SDK

Om du använder tal-SDK med anpassat domän namn och aktiverat tal resurser för privata slut punkter måste du granska och troligt vis ändra program koden.

Vi kommer att använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resursens DNS-namn (anpassad domän) för det här avsnittet.

##### <a name="general-principle"></a>Allmän princip

Normalt i SDK-scenarier (och i text till tal-REST API scenarier) använder tal resurser dedikerade regionala slut punkter för olika tjänst erbjudanden. DNS-namn formatet för dessa slut punkter är: </p>`{region}.{speech service offering}.speech.microsoft.com`

Exempel: </p>`westeurope.stt.speech.microsoft.com`

Alla möjliga värden för regionen (första elementet i DNS-namnet) visas i den [här tabellen nedan visar det möjliga](regions.md) värdet för tal tjänst erbjudandet (andra elementet i DNS-namnet):

| DNS-namn värde | Erbjudande för tal tjänster                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Anpassade kommandon](custom-commands.md)                       |
| `convai`       | [Konversations avskrift](conversation-transcription.md) |
| `s2s`          | [Talöversättning](speech-translation.md)                 |
| `stt`          | [Tal till text](speech-to-text.md)                         |
| `tts`          | [Text till tal](text-to-speech.md)                         |
| `voice`        | [Anpassad röst](how-to-custom-voice.md)                      |

Exemplet ovan ( `westeurope.stt.speech.microsoft.com` ) står för slut punkten för tal till text i Västeuropa.

Slut punkter för privata slut punkter kommunicerar med tal tjänster via en speciell proxy och på grund av att **du måste ändra URL: erna för slut punkts anslutning**. 

En slut punkts-URL för "standard" ser ut så här: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

En privat slut punkts-URL ser ut så här: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exempel 1.** Programmet kommunicerar med hjälp av följande URL (tal igenkänning med bas modell för amerikansk engelska i Västeuropa):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Om du vill använda det i scenariot för privat slut punkt när det anpassade domän namnet för tal resursen är `my-private-link-speech.cognitiveservices.azure.com` måste du ändra URL: en så här:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Lägg märke till detaljerna:

- Värd namnet `westeurope.stt.speech.microsoft.com` har ersatts av det anpassade domän namnet `my-private-link-speech.cognitiveservices.azure.com` .
- Det andra elementet i det ursprungliga DNS-namnet ( `stt` ) blir det första elementet i URL-sökvägen och föregår den ursprungliga sökvägen. Så den ursprungliga URL: en `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` blir `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exempel 2.** I programmet används följande URL för att syntetisera tal i Västeuropa med en anpassad röst modell):
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Följande är en motsvarande URL som använder en privat slut punkt som är aktive rad där det anpassade domän namnet för tal resursen är `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Samma princip som i exempel 1 tillämpas, men nyckel elementet den här gången är `voice` .

##### <a name="modify-applications"></a>Ändra program

Följ dessa steg om du vill ändra koden:

**1. kontrol lera programmets slut punkts-URL**

- [Aktivera loggning för ditt program](how-to-use-logging.md) och kör det för att logga aktiviteten.
- Sök efter i logg filen `SPEECH-ConnectionUrl` . I matchande rader `value` innehåller parametern den fullständiga URL som programmet använde för att komma åt tal tjänsten.

Exempel:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

URL: en som används av programmet i det här exemplet är:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. skapa `SpeechConfig` en instans med fullständig slut punkts-URL**

Ändra slut punkten som du fastställde i föregående avsnitt enligt beskrivningen i [allmän princip](#general-principle) ovan.

Ändra nu hur du skapar instansen av `SpeechConfig` . De mest sannolika dagens program använder något som liknar detta:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Detta fungerar inte för en privat slut punkts aktive rad tal resurs på grund av värdnamn och URL-ändringar som vi beskrivit i föregående avsnitt. Om du försöker köra ditt befintliga program utan några ändringar med hjälp av nyckeln för en privat slut punkt som är aktive rad, får du ett autentiseringsfel (401).

Om du vill göra det kan du ändra hur du instansierar `SpeechConfig` klassen och använder "från slut punkten"/"med initiering av slut punkten". Anta att vi har följande två variabler definierade:
- `subscriptionKey` innehåller nyckeln för den privata slut punkts språks resursen
- `endPoint` innehåller den fullständiga **ändrade** slut punkts-URL: en (med den typ som krävs av det motsvarande programmeringsspråk). I vårt exempel ska den här variabeln innehålla
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Skapa sedan en `SpeechConfig` instans:
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
> De frågeparametrar som anges i slut punkts-URI: n ändras inte, även om de anges av andra API: er. Om igenkännings språket exempelvis definieras i URI som frågeparameter "Language = en-US", och också är inställt på "ru-RU" via egenskapen mottagare, används språk inställningen i URI: n och det effektiva språket är "en-US". Parametrar som anges i slut punkts-URI: n tar alltid precidence. Endast parametrar som inte har angetts i slut punkts-URI: n kan åsidosättas av andra API: er.

Efter den här ändringen bör programmet fungera med de privata aktiverade tal resurserna. Vi arbetar på mer sömlöst stöd för privata slut punkts scenario.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Använd tal resurs med eget domän namn utan privata slut punkter

I den här artikeln har vi påpekat flera gånger, att aktivera anpassad domän för en tal resurs går inte att **Ångra** och sådan resurs kommer att använda ett annat sätt att kommunicera med tal tjänster som jämförs med de "vanliga" (det vill säga de som använder [regionala slut punkts namn](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

I det här avsnittet beskrivs hur du använder en tal resurs med aktiverade anpassade domän namn men **utan** privata slut punkter med speech Services REST API och [Speech SDK](speech-sdk.md). Detta kan vara en resurs som har använts i ett privat slut punkts scenario, men som sedan hade sina privata slut punkter borttagna.

#### <a name="dns-configuration"></a>DNS-konfiguration

Kom ihåg hur ett anpassat DNS-namn för den privata slut punkten som är aktiverat för tal resursen [löses från offentliga nätverk](#resolve-dns-from-other-networks). I det här fallet har IP-adressen lösts punkter till en slut punkt för VNet-proxy som används för att skicka nätverks trafiken till den privata slut punkten som är aktive rad Cognitive Services resurs.

Men när **alla** privata slut punkter tas bort (eller direkt efter aktivering av det anpassade domän namnet), reserveras posten för tal resursen och pekar nu på IP-adressen för den kontakt [Cognitive Services regionala slut punkten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Tal resurs med eget domän namn utan privata slut punkter. Användning med REST API

##### <a name="speech-to-text-rest-api-v30"></a>Tal till text REST API v 3.0

Användning av tal-till-text REST API v 3.0 är helt likvärdig med fallet för de [tal resurser som är aktiverade för privata slut punkter](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Tal-till-text-REST API för kort ljud-och text till tal-REST API

I det här fallet är tal-till-text-REST API för kort ljud-och text till tal-REST API användning har inga skillnader i det allmänna fallet med ett undantag för tal-till-text-REST API för kort ljud (se OBS! nedan). Båda API: erna bör användas enligt beskrivningen i [tal-till-text-REST API för kort](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) [text och text till tal REST API-](rest-text-to-speech.md) dokumentation.

> [!NOTE]
> När **du använder tal-till-text-REST API för kort ljud** i anpassade domän scenarier, använder du en autentiseringstoken som [skickas via](rest-speech-to-text.md#request-headers) `Authorization` [huvud](rest-speech-to-text.md#request-headers). Överföring av tal prenumerations nyckel till den särskilda slut punkten via `Ocp-Apim-Subscription-Key` huvud kommer **inte** att fungera och kommer att generera fel 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Tal resurs med eget domän namn utan privata slut punkter. Användning med tal-SDK

Genom att använda tal-SDK med anpassat domän namn aktiverat tal resurser **utan** privata slut punkter måste du granska och troligt vis ändra program koden. Observera att dessa ändringar är **olika** jämfört med en [privat slut punkt som är aktive rad tal resurs](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Vi arbetar på mer sömlöst stöd för privata slut punkter/anpassat domän scenario.

Vi kommer att använda `my-private-link-speech.cognitiveservices.azure.com` som exempel på tal resursens DNS-namn (anpassad domän) för det här avsnittet.

I avsnittet om [inaktiverade tal resurser för privat slut punkt](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) har vi förklarat hur du avgör slut punktens URL som används, ändrar den och gör den till att fungera via "från slut punkt"/"med slut punkt"-initiering av `SpeechConfig` klass instans.

Men om du försöker köra samma program efter att alla privata slut punkter har tagits bort (vilket gör det möjligt för den ometablering av DNS-post) får du ett internt tjänst fel (404). Orsaken är [DNS-posten](#dns-configuration) som nu pekar på den regionala Cognitive Services slut punkten i stället för VNet-proxyn, och URL-sökvägar som `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` inte hittas där, och därför kan felet "hittades inte" visas (404).

Om du "återställer" ditt program till instansieringen "standard" av `SpeechConfig` i formatet för
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
programmet kommer att avslutas med autentiseringsfel (401).

##### <a name="modifying-applications"></a>Ändra program

Följ dessa steg om du vill att programmet ska använda en tal resurs med ett anpassat domän namn och utan privata slut punkter:

**1. begär token för auktorisering från Cognitive Services REST API**

[Den här artikeln](../authentication.md#authenticate-with-an-authentication-token) visar hur du hämtar token med hjälp av Cognitive Services REST API.

Använd det anpassade domän namnet i slut punkts-URL: en, i vårt exempel är URL: en:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Du hittar denna URL i Azure Portal. På din tal resurs sida väljer du **nycklar och slut punkt** under under **resurs hanterings** gruppen.

**2. skapa en `SpeechConfig` instans med hjälp av metoden "från autentiseringstoken"/"med autentiseringstoken".**

Skapa en `SpeechConfig` instans med hjälp av autentiseringstoken som du fick i föregående avsnitt. Anta att vi har följande variabler definierade:

- `token`: autentiseringstoken som hämtades i föregående avsnitt
- `azureRegion`: namnet på tal resurs [regionen](regions.md) (exempel: `westeurope` )
- `outError`: (endast för [mål C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) -fall)

Skapa sedan en `SpeechConfig` instans:

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
> Anroparen måste se till att autentiseringstoken är giltig.
> Innan token för auktorisering upphör att gälla måste anroparen uppdatera den genom att anropa den här setter med en ny giltig token.
> När konfigurations värden kopieras när en ny tolk eller synthesizer skapas, gäller inte det nya token-värdet för identifierare eller synthesizers som redan har skapats.
> För dessa ställer du in autentiseringstoken för den aktuella tolken eller Synthesizern för att uppdatera token.
> Om du inte uppdaterar token kommer tolken eller Synthesizern att upptäcka fel vid drift.

Efter den här ändringen bör programmet fungera med tal resurser som använder ett anpassat domän namn utan privata slut punkter.

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Mer information

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [REST API för tal-till-text](rest-speech-to-text.md)
* [Text-till-tal (REST API)](rest-text-to-speech.md)
