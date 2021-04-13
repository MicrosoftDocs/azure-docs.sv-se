---
title: Använda hanterade identiteter på en virtuell dator för att hämta åtkomsttoken – Azure AD
description: Stegvisa instruktioner och exempel för att använda hanterade identiteter för Azure-resurser på en virtuell dator för att hämta en OAuth-åtkomsttoken.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee7739d9dbfd34190dc1e856b98fdd21be15743
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364948"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Så här använder du hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att hämta en åtkomsttoken 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

Den här artikeln innehåller olika kod- och skriptexempel för tokenförvärv, samt vägledning om viktiga ämnen som hantering av förfallodatum för token och HTTP-fel. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell i den här artikeln måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - All exempelkod/skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser. Använd funktionen "Anslut" för den virtuella datorn i Azure Portal för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i Konfigurera hanterade identiteter för [Azure-resurser](qs-configure-portal-windows-vm.md)på en virtuell dator med hjälp av Azure Portal eller någon av variantartiklarna (med PowerShell, CLI, en mall eller ett Azure SDK). 

> [!IMPORTANT]
> - Säkerhetsgränsen för hanterade identiteter för Azure-resurser är den resurs som den används på. All kod/skript som körs på en virtuell dator kan begära och hämta token för alla hanterade identiteter som är tillgängliga på den. 

## <a name="overview"></a>Översikt

Ett klientprogram kan begära hanterade identiteter för [app-endast åtkomsttoken för Azure-resurser](../develop/developer-glossary.md#access-token) för åtkomst till en viss resurs. Token baseras på [hanterade identiteter för Tjänstens huvudnamn för Azure-resurser.](overview.md#managed-identity-types) Därför behöver klienten inte registrera sig själv för att hämta en åtkomsttoken under sitt eget huvudnamn för tjänsten. Token är lämplig för användning som en bearer-token i [tjänst-till-tjänst-anrop som kräver klientautentiseringsuppgifter.](../develop/v2-oauth2-client-creds-grant-flow.md)

| Länk | Beskrivning |
| -------------- | -------------------- |
| [Hämta en token med HTTP](#get-a-token-using-http) | Protokollinformation för hanterade identiteter för tokenslutpunkt för Azure-resurser |
| [Hämta en token med hjälp av Microsoft.Azure.Services.AppAuthentication-biblioteket för .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exempel på hur du använder biblioteket Microsoft.Azure.Services.AppAuthentication från en .NET-klient
| [Hämta en token med C #](#get-a-token-using-c) | Exempel på hur du använder hanterade identiteter för REST-slutpunkter för Azure-resurser från en C#-klient |
| [Hämta en token med Java](#get-a-token-using-java) | Exempel på hur du använder hanterade identiteter för REST-slutpunkten för Azure-resurser från en Java-klient |
| [Hämta en token med Go](#get-a-token-using-go) | Exempel på hur du använder hanterade identiteter för REST-slutpunkter för Azure-resurser från en Go-klient |
| [Hämta en token med Azure PowerShell](#get-a-token-using-azure-powershell) | Exempel på hur du använder hanterade identiteter för REST-slutpunkter för Azure-resurser från en PowerShell-klient |
| [Hämta en token med CURL](#get-a-token-using-curl) | Exempel på hur du använder hanterade identiteter för REST-slutpunkter för Azure-resurser från en Bash/CURL-klient |
| Hantera cachelagring av token | Vägledning för hantering av utgångna åtkomsttoken |
| [Felhantering](#error-handling) | Vägledning för hantering av HTTP-fel som returneras från hanterade identiteter för tokenslutpunkter för Azure-resurser |
| [Resurs-ID:er för Azure-tjänster](#resource-ids-for-azure-services) | Var du kan hämta resurs-ID:er för Azure-tjänster som stöds |

## <a name="get-a-token-using-http"></a>Hämta en token med HTTP 

Det grundläggande gränssnittet för att hämta en åtkomsttoken baseras på REST, vilket gör det tillgängligt för alla klientprogram som körs på den virtuella datorn som kan göra HTTP REST-anrop. Detta liknar Azure AD-programmeringsmodellen, förutom att klienten använder en slutpunkt på den virtuella datorn (jämfört med en Azure AD-slutpunkt).

Exempelbegäran med hjälp av slutpunkten för Azure Instance Metadata Service (IMDS) *(rekommenderas):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slutpunkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Slutpunkten för hanterade identiteter för Azure-resurser för Instance Metadata Service. |
| `api-version`  | En frågesträngsparameter som anger API-versionen för IMDS-slutpunkten. Använd API-version `2018-02-01` eller senare. |
| `resource` | En frågesträngsparameter som anger app-ID-URI för målresursen. Den visas också i `aud` anspråket (målgrupp) för den utfärdade token. I det här exemplet begärs en token för Azure Resource Manager, som har app-ID-URI:n `https://management.azure.com/` . |
| `Metadata` | Ett huvudfält för HTTP-begäran som krävs av hanterade identiteter för Azure-resurser som en åtgärd mot SSRF-attacker (Server Side Request Forgery). Det här värdet måste anges till "true", i gemener. |
| `object_id` | (Valfritt) En frågesträngsparameter som anger object_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användar tilldelade hanterade identiteter.|
| `client_id` | (Valfritt) En frågesträngsparameter som anger client_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användar tilldelade hanterade identiteter.|
| `mi_res_id` | (Valfritt) En frågesträngsparameter som anger mi_res_id (Azure Resource ID) för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användar tilldelade hanterade identiteter. |

Exempelsvar:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomsttoken. När en skyddad REST API anropas bäddas token in i fältet för begärandehuvud som en `Authorization` "bearer"-token, så att API:et kan autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antalet sekunder som åtkomsttoken fortsätter att vara giltigt innan den upphör att gälla från tidpunkten för utfärdandet. Tid för utfärdande finns i tokens `iat` anspråk. |
| `expires_on` | Tidsspann när åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `exp` anspråk). |
| `not_before` | Det tidsspann då åtkomsttoken har verkställs och kan godkännas. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `nbf` anspråk). |
| `resource` | Resursen som åtkomsttoken begärdes för, som matchar `resource` frågesträngparametern för begäran. |
| `token_type` | Typen av token, som är en åtkomsttoken "Bearer", vilket innebär att resursen kan ge åtkomst till den här tokens bearer. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Hämta en token med hjälp av Microsoft.Azure.Services.AppAuthentication-biblioteket för .NET

För .NET-program och funktioner är det enklaste sättet att arbeta med hanterade identiteter för Azure-resurser via paketet Microsoft.Azure.Services.AppAuthentication. Med det här biblioteket kan du också testa koden lokalt på utvecklingsdatorn med ditt användarkonto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory-integrerad autentisering. Mer information om lokala utvecklingsalternativ med det här biblioteket finns i referensen [Microsoft.Azure.Services.AppAuthentication.](/dotnet/api/overview/azure/service-to-service-authentication) Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till NuGet-paketen [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) i ditt program.

2.  Lägg till följande kod i ditt program:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Mer information om Microsoft.Azure.Services.AppAuthentication och de åtgärder som exponeras finns i Referens för [Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) och [i .NET-exemplet App Service och KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)med hanterade identiteter för Azure-resurser.

## <a name="get-a-token-using-c"></a>Hämta en token med C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Hämta en token med Java

Använd det här [JSON-biblioteket](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) för att hämta en token med hjälp av Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Hämta en token med Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Hämta en token med Azure PowerShell

Följande exempel visar hur du använder de hanterade identiteterna för REST-slutpunkten för Azure-resurser från en PowerShell-klient för att:

1. Hämta en åtkomsttoken.
2. Använd åtkomsttoken för att anropa en Azure Resource Manager REST API och hämta information om den virtuella datorn. Se till att ersätta ditt prenumerations-ID, resursgruppens namn och namnet på den `<SUBSCRIPTION-ID>` virtuella datorn för respektive , och `<RESOURCE-GROUP>` `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exempel på hur du parsar åtkomsttoken från svaret:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Hämta en token med CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exempel på hur du parsar åtkomsttoken från svaret:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Cachelagring av token

Även om de hanterade identiteterna för Azure-resursundersystemet cachelagrar token, rekommenderar vi också att du implementerar tokencachelagring i koden. Därför bör du förbereda dig för scenarier där resursen anger att token har upphört att gälla. 

On-the-wire-anrop till Azure AD-resultat endast när:

- Cachemiss inträffar på grund av att det inte finns någon token i de hanterade identiteterna för undersystemcachen för Azure-resurser.
- Den cachelagrade token har upphört att gälla.

## <a name="error-handling"></a>Felhantering

Hanterade identiteter för Azure-resursslutpunkter signalerar fel via statuskodfältet i HTTP-svarsmeddelandehuvudet som antingen 4xx- eller 5xx-fel:

| Statuskod | Felorsak | Så här hanterar du |
| ----------- | ------------ | ------------- |
| 404 Hittades inte. | IMDS-slutpunkten uppdateras. | Försök igen med exponentiell backoff. Se vägledningen nedan. |
| 429 För många begäranden. |  IMDS-begränsningsgränsen har nåtts. | Försök igen med exponentiell backoff. Se vägledningen nedan. |
| 4xx Fel i begäran. | En eller flera av begärandeparametrarna var felaktiga. | Försök inte igen.  Granska felinformationen för mer information.  4xx-fel är designtidsfel.|
| 5xx Tillfälligt fel från tjänsten. | Hanterade identiteter för azure-resursers undersystem eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen när du har väntat i minst en sekund.  Om du försöker igen för snabbt eller för ofta kan IMDS och/eller Azure AD returnera ett fel om frekvensbegränsning (429).|
| timeout | IMDS-slutpunkten uppdateras. | Försök igen med exponentiell backoff. Se vägledningen nedan. |

Om ett fel inträffar innehåller motsvarande HTTP-svarstext JSON med felinformationen:

| Element | Beskrivning |
| ------- | ----------- |
| fel   | Felidentifierare. |
| error_description | Utförlig beskrivning av felet. **Felbeskrivningar kan ändras när som helst. Skriv inte kod som förgrenar baserat på värden i felbeskrivningen.**|

### <a name="http-response-reference"></a>HTTP-svarsreferens

I det här avsnittet dokumenteras möjliga felsvar. Statusen "200 OK" är ett lyckat svar och åtkomsttoken finns i svarstextens JSON i access_token-elementet.

| Statuskod | Fel | Felbeskrivning | Lösning |
| ----------- | ----- | ----------------- | -------- |
| 400 – Felaktig begäran | invalid_resource | AADSTS50001: Programmet med *\<URI\>* namnet hittades inte i klientorganisationen med namnet *\<TENANT-ID\>* . Detta kan inträffa om programmet inte har installerats av administratören för klientorganisationen, eller om det har godkänts av någon användare i klientorganisationen. Du kanske har skickat din autentiseringsbegäran till fel klientorganisation.\ | (endast Linux) |
| 400 – Felaktig begäran | bad_request_102 | Nödvändigt metadatahuvud har inte angetts | Antingen `Metadata` saknas fältet för begärandehuvudet i begäran eller är felaktigt formaterat. Värdet måste anges som `true` , i gemener. Ett exempel finns i "Exempelbegäran" i föregående REST-avsnitt.|
| 401 – Ej behörig | unknown_source | Okänd källa *\<URI\>* | Kontrollera att din URI för HTTP GET-begäran är korrekt formaterad. Delen `scheme:host/resource-path` måste anges som `http://localhost:50342/oauth2/token` . Ett exempel finns i "Exempelbegäran" i föregående REST-avsnitt.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parametervärde, innehåller en parameter mer än en gång eller är felaktig på annat sätt. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära en åtkomsttoken med den här metoden. | Orsakas av en begäran på en virtuell dator som inte har hanterade identiteter för Azure-resurser korrekt konfigurerade. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp Azure Portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med konfiguration av virtuella datorer. |
|           | access_denied | Resursägaren eller auktoriseringsservern nekade begäran. |  |
|           | unsupported_response_type | Auktoriseringsservern stöder inte att hämta en åtkomsttoken med den här metoden. |  |
|           | invalid_scope | Det begärda omfånget är ogiltigt, okänt eller felaktigt. |  |
| 500 Internt serverfel | okänd | Det gick inte att hämta token från Active Directory. Mer information finns i loggar in *\<file path\>* | Kontrollera att hanterade identiteter för Azure-resurser har aktiverats på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp Azure Portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med konfiguration av virtuella datorer.<br><br>Kontrollera också att URI:en för HTTP GET-begäran är korrekt formaterad, särskilt resurs-URI:en som anges i frågesträngen. Se "Exempelbegäran" i föregående REST-avsnitt för ett exempel eller Azure-tjänster som stöder [Azure AD-autentisering](./services-support-managed-identities.md) för en lista över tjänster och deras respektive resurs-ID: er.

## <a name="retry-guidance"></a>Vägledning för återförsök 

Vi rekommenderar att du försöker igen om du får felkoden 404, 429 eller 5xx (se [Felhantering](#error-handling) ovan).

Begränsningsgränser gäller för antalet anrop till IMDS-slutpunkten. När tröskelvärdet för begränsning överskrids begränsar IMDS-slutpunkten eventuella ytterligare begäranden när begränsningen gäller. Under den här perioden returnerar IMDS-slutpunkten HTTP-statuskod 429 ("För många begäranden") och begärandena misslyckas. 

För återförsök rekommenderar vi följande strategi: 

| **Återförsöksstrategi** | **Inställningar** | **Värden** | **Så här fungerar det** |
| --- | --- | --- | --- |
|ExponentialBackoff |Antal återförsök<br />Min. backoff<br />Max. backoff<br />Deltabackoff<br />Första snabba återförsöket |5<br />0 sek.<br />60 sek.<br />2 sek.<br />falskt |Försök 1 – 0 sek. fördröjning<br />Försök 2 – ~2 sek. fördröjning<br />Försök 3 – ~6 sek. fördröjning<br />Försök 4 – ~14 sek. fördröjning<br />Försök 5 – ~30 sek. fördröjning |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

En [lista över resurser](./services-support-managed-identities.md) som stöder Azure AD och som har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID finns i Azure-tjänster som stöder Azure AD-autentisering.


## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell Azure-dator finns i Konfigurera hanterade identiteter för Azure-resurser på en [virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md).