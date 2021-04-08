---
title: Använd hanterade identiteter på en virtuell dator för att få åtkomst-token – Azure AD
description: Steg-för-steg-instruktioner och exempel för att använda hanterade identiteter för Azure-resurser på en virtuell dator för att skaffa en OAuth-åtkomsttoken.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541f76ad825f492679530902c571096ca4b01902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726239"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att hämta en åtkomsttoken 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

Den här artikeln innehåller olika kod-och skript exempel för hämtning av token, samt vägledning för viktiga ämnen som hantering av token för förfallo datum och HTTP-fel. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell exemplen i den här artikeln måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - All exempel kod/skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser. Använd den virtuella datorn "Connect"-funktionen i Azure Portal för att fjärrans luta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md)eller en av variant-artiklarna (med POWERSHELL, CLI, mall eller Azure SDK). 

> [!IMPORTANT]
> - Säkerhets gränserna för hanterade identiteter för Azure-resurser är den resurs som används. All kod/alla skript som körs på en virtuell dator kan begära och hämta token för alla hanterade identiteter som är tillgängliga på den. 

## <a name="overview"></a>Översikt

Ett klient program kan begära hanterade identiteter för Azure-resurser [endast app-åtkomsttoken](../develop/developer-glossary.md#access-token) för åtkomst till en specifik resurs. Token [baseras på hanterade identiteter för Azure Resources-tjänstens huvud namn](overview.md#managed-identity-types). Därför behöver inte klienten registrera sig för att få en åtkomsttoken under sitt eget tjänst huvud namn. Token är lämplig för användning som en Bearer-token i [tjänst-till-tjänst-anrop som kräver klientautentiseringsuppgifter](../develop/v2-oauth2-client-creds-grant-flow.md).

| Länk | Beskrivning |
| -------------- | -------------------- |
| [Hämta en token med HTTP](#get-a-token-using-http) | Protokoll information för hanterade identiteter för Azure-resurser token-slutpunkt |
| [Hämta en token med hjälp av Microsoft. Azure. Services. AppAuthentication-biblioteket för .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exempel på hur du använder Microsoft. Azure. Services. AppAuthentication-biblioteket från en .NET-klient
| [Hämta en token med C #](#get-a-token-using-c) | Exempel på användning av hanterade identiteter för Azure-resurser REST-slutpunkt från en C#-klient |
| [Hämta en token med Java](#get-a-token-using-java) | Exempel på användning av hanterade identiteter för Azure-resurser REST-slutpunkt från en Java-klient |
| [Hämta en token med go](#get-a-token-using-go) | Exempel på användning av hanterade identiteter för Azure-resurser REST-slutpunkt från en Go-klient |
| [Hämta en token med hjälp av Azure PowerShell](#get-a-token-using-azure-powershell) | Exempel på användning av hanterade identiteter för Azure-resurser REST-slutpunkt från en PowerShell-klient |
| [Hämta en token med hjälp av sväng](#get-a-token-using-curl) | Exempel på användning av hanterade identiteter för Azure-resurser REST-slutpunkt från en bash/en spiral klient |
| Hantera cachelagring av token | Vägledning för att hantera utgångna åtkomsttoken |
| [Felhantering](#error-handling) | Vägledning för hantering av HTTP-fel som returneras från hanterade identiteter för Azure-resurser token-slutpunkt |
| [Resurs-ID för Azure-tjänster](#resource-ids-for-azure-services) | Var du får resurs-ID: n för Azure-tjänster som stöds |

## <a name="get-a-token-using-http"></a>Hämta en token med HTTP 

Det grundläggande gränssnittet för att förvärva en åtkomsttoken baseras på REST, vilket gör det tillgängligt för alla klient program som körs på den virtuella datorn och som kan göra HTTP REST-anrop. Detta liknar programmerings modellen för Azure AD, förutom att klienten använder en slut punkt på den virtuella datorn (jämfört med en Azure AD-slutpunkt).

Exempel förfrågan med hjälp av Azure Instance Metadata Service (IMDS)-slut punkten *(rekommenderas)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slut punkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Hanterade identiteter för Azures resursers slut punkt för Instance Metadata Service. |
| `api-version`  | En frågesträngparametern som anger API-versionen för IMDS-slutpunkten. Använd en API-version `2018-02-01` eller mer. |
| `resource` | En frågesträngparametern som anger URI för app-ID för mål resursen. Den visas också i `aud` (Audience)-anspråket för Utfärdad token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en app-ID-URI för `https://management.azure.com/` . |
| `Metadata` | Ett huvud fält för HTTP-begäran som krävs av hanterade identiteter för Azure-resurser som ett skydd mot Server sidans förfalsknings attack (SSRF). Värdet måste anges till "true", i gemener. |
| `object_id` | Valfritt En frågesträngparametern, som anger object_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|
| `client_id` | Valfritt En frågesträngparametern, som anger client_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|
| `mi_res_id` | Valfritt En frågesträngparametern, som anger mi_res_id (Azure-resurs-ID) för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter. |

Exempel förfrågan som använder hanterade identiteter för VM-tillägget för virtuella Azure *-resurser (planerat för utfasning i januari 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slut punkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | Hanterade identiteter för Azure-resursers slut punkt, där 50342 är standard porten och kan konfigureras. |
| `resource` | En frågesträngparametern som anger URI för app-ID för mål resursen. Den visas också i `aud` (Audience)-anspråket för Utfärdad token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en app-ID-URI för `https://management.azure.com/` . |
| `Metadata` | Ett huvud fält för HTTP-begäran som krävs av hanterade identiteter för Azure-resurser som ett skydd mot Server sidans förfalsknings attack (SSRF). Värdet måste anges till "true", i gemener.|
| `object_id` | Valfritt En frågesträngparametern, som anger object_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|
| `client_id` | Valfritt En frågesträngparametern, som anger client_id för den hanterade identitet som du vill ha token för. Krävs om den virtuella datorn har flera användare tilldelade hanterade identiteter.|

Exempel svar:

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
| `access_token` | Den begärda åtkomsttoken. När du anropar en skyddad REST API, bäddas token in i `Authorization` fältet begär ande huvud som en "Bearer"-token, vilket gör att API: et kan autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antalet sekunder som åtkomst-token fortsätter att vara giltig, innan det går ut, från tid för utfärdande. Tiden för utfärdande kan hittas i token- `iat` anspråk. |
| `expires_on` | TimeSpan när åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `exp` anspråk). |
| `not_before` | TimeSpan när åtkomsttoken börjar gälla och kan godkännas. Datumet visas som antalet sekunder från "1970-01-01T0:0: 0Z UTC" (motsvarar token: s `nbf` anspråk). |
| `resource` | Resursen som åtkomsttoken begärdes för, som matchar `resource` frågesträngparametern för begäran. |
| `token_type` | Typen av token, som är en "Bearer"-åtkomsttoken, vilket innebär att resursen kan ge till gång till denna token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Hämta en token med hjälp av Microsoft. Azure. Services. AppAuthentication-biblioteket för .NET

För .NET-program och-funktioner är det enklaste sättet att arbeta med hanterade identiteter för Azure-resurser via Microsoft. Azure. Services. AppAuthentication-paketet. Med det här biblioteket kan du också testa din kod lokalt på din utvecklings dator med ditt användar konto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory integrerad autentisering. Mer information om lokala utvecklings alternativ med det här biblioteket finns i [referens för Microsoft. Azure. Services. AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication). Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -och [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paketen i programmet.

2.  Lägg till följande kod i programmet:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Mer information om Microsoft. Azure. Services. AppAuthentication och de åtgärder den visar finns i [referensen Microsoft. Azure. Services. AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) och [app service och nyckel valvet med hanterade identiteter för Azure-resurser .net-exempel](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

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

Använd det här [JSON-biblioteket](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) för att hämta en token med Java.

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

## <a name="get-a-token-using-go"></a>Hämta en token med go

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

## <a name="get-a-token-using-azure-powershell"></a>Hämta en token med hjälp av Azure PowerShell

Följande exempel visar hur du använder hanterade identiteter för Azure-resurser REST-slutpunkt från en PowerShell-klient till:

1. Hämta en åtkomsttoken.
2. Använd åtkomsttoken för att anropa ett Azure Resource Manager REST API och hämta information om den virtuella datorn. Se till att du ersätter ditt prenumerations-ID, resurs gruppens namn och namnet på den virtuella datorn för `<SUBSCRIPTION-ID>` , `<RESOURCE-GROUP>` `<VM-NAME>` respektive.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exempel på hur du tolkar åtkomsttoken från svaret:
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

## <a name="get-a-token-using-curl"></a>Hämta en token med hjälp av sväng

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exempel på hur du tolkar åtkomsttoken från svaret:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Cachelagring av token

Medan de hanterade identiteterna för Azure Resources-undersystemet används (IMDS/Managed Identities for Azure Resources VM), så rekommenderar vi även att implementera cachelagring av token i din kod. Därför bör du förbereda för scenarier där resursen indikerar att token har upphört att gälla. 

Anrop till Azure AD skapas endast på tråd:
- Cachemissar uppstår på grund av att det inte finns någon token i hanterade identiteter för Azure-resursers under systemets cache
- den cachelagrade token har upphört att gälla

## <a name="error-handling"></a>Felhantering

Slut punkten för hanterade identiteter för Azure-resurser signalerar fel via statusfältet i HTTP-svarsmeddelandet, antingen som 4xx eller 5xx fel:

| Statuskod | Fel Orsak | Så här hanterar du |
| ----------- | ------------ | ------------- |
| 404 hittades inte. | IMDS-slutpunkten uppdateras. | Försök igen med exponentiell backoff. Se rikt linjer nedan. |
| 429 för många begär Anden. |  IMDS begränsning har nåtts. | Försök igen med exponentiell backoff. Se rikt linjer nedan. |
| 4xx-fel i begäran. | En eller flera av parametrarna för begäran var felaktiga. | Försök inte igen.  Granska fel informationen för mer information.  4xx-fel är design tids fel.|
| 5xx tillfälligt fel från tjänsten. | Hanterade identiteter för under systemet för Azure-resurser eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter att ha väntat i minst en sekund.  Om du försöker igen för snabbt eller för ofta kan IMDS och/eller Azure AD returnera ett frekvens gräns fel (429).|
| timeout | IMDS-slutpunkten uppdateras. | Försök igen med exponentiell backoff. Se rikt linjer nedan. |

Om ett fel inträffar innehåller motsvarande HTTP-svar-text JSON fel information:

| Element | Beskrivning |
| ------- | ----------- |
| fel   | Fel-ID. |
| error_description | Utförlig beskrivning av felet. **Fel beskrivningar kan ändras när som helst. Skriv inte kod som förgreningar baserat på värden i fel beskrivningen.**|

### <a name="http-response-reference"></a>Referens för HTTP-svar

I det här avsnittet dokumenteras möjliga fel svar. Statusen "200 OK" är ett lyckat svar och åtkomsttoken finns i JSON för svars texten i access_token-elementet.

| Statuskod | Fel | Felbeskrivning | Lösning |
| ----------- | ----- | ----------------- | -------- |
| 400 – Felaktig begäran | invalid_resource | AADSTS50001: programmet med namnet *\<URI\>* hittades inte i klient organisationen med namnet *\<TENANT-ID\>* . Detta kan inträffa om programmet inte har installerats av administratören för klientorganisationen, eller om det har godkänts av någon användare i klientorganisationen. Du kanske har skickat din autentiseringsbegäran till fel klient. \ | (Endast Linux) |
| 400 – Felaktig begäran | bad_request_102 | Obligatoriskt metadata-huvud har inte angetts | Antingen `Metadata` saknas fältet begär ande huvud från din förfrågan eller så är det felaktigt formaterat. Värdet måste anges som `true` , i gemener/versaler. Se "exempel förfrågan" i föregående REST-avsnitt för ett exempel.|
| 401 – Ej behörig | unknown_source | Okänd källa *\<URI\>* | Verifiera att HTTP GET-begärans URI är korrekt formaterad. `scheme:host/resource-path`Delen måste anges som `http://localhost:50342/oauth2/token` . Se "exempel förfrågan" i föregående REST-avsnitt för ett exempel.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parameter värde, innehåller en parameter mer än en gång eller är i övrigt felaktig. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära en åtkomsttoken med den här metoden. | Orsakas av en begäran som inte använde lokal loopback för att anropa tillägget, eller på en virtuell dator som inte har hanterade identiteter för Azure-resurser korrekt konfigurerade. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfigurationen. |
|           | access_denied | Resurs ägaren eller auktoriseringsservern nekade begäran. |  |
|           | unsupported_response_type | Auktoriseringsservern har inte stöd för att hämta en åtkomsttoken med den här metoden. |  |
|           | invalid_scope | Det begärda omfånget är ogiltigt, okänt eller felaktigt. |  |
| 500 Internt serverfel | okänd | Det gick inte att hämta token från Active Directory. Mer information finns i loggarna i *\<file path\>* | Kontrol lera att hanterade identiteter för Azure-resurser har Aktiver ATS på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfigurationen.<br><br>Kontrol lera också att HTTP GET-begärande-URI: n är korrekt formaterad, särskilt resurs-URI: n som anges i frågesträngen. Se "exempel förfrågan" i föregående REST-avsnitt för ett exempel, eller [Azure-tjänster som stöder Azure AD-autentisering](./services-support-managed-identities.md) för en lista över tjänster och deras respektive resurs-ID.

## <a name="retry-guidance"></a>Vägledning för nytt försök 

Vi rekommenderar att du försöker igen om du får en felkod på 404, 429 eller 5xx (se [fel hantering](#error-handling) ovan).

Begränsnings gränser gäller för antalet anrop som görs till IMDS-slutpunkten. När tröskelvärdet för begränsning har överskridits begränsar IMDS-slutpunkten alla ytterligare begär Anden medan begränsningen gäller. Under den här perioden returnerar IMDS-slutpunkten HTTP-statuskod 429 ("för många begär Anden") och begär Anden Miss fungerar. 

För återförsök rekommenderar vi följande strategi: 

| **Återförsöksstrategi** | **Inställningar** | **Värden** | **Så här fungerar det** |
| --- | --- | --- | --- |
|ExponentialBackoff |Antal återförsök<br />Min. backoff<br />Max. backoff<br />Deltabackoff<br />Första snabba återförsöket |5<br />0 sek.<br />60 sek.<br />2 sek.<br />falskt |Försök 1 – 0 sek. fördröjning<br />Försök 2 – ~2 sek. fördröjning<br />Försök 3 – ~6 sek. fördröjning<br />Försök 4 – ~14 sek. fördröjning<br />Försök 5 – ~30 sek. fördröjning |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](./services-support-managed-identities.md) för en lista över resurser som stöder Azure AD och som har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID.


## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera hanterade identiteter för Azure-resurser på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md).