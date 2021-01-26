---
title: Använd hanterad identitet med ett program
description: Använda hanterade identiteter i Azure Service Fabric program kod för att få åtkomst till Azure-tjänster.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: bec640077a17ccef822afb85fc104728aabb13b1
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791330"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Hur du utnyttjar ett Service Fabric programmets hanterade identitet för att få åtkomst till Azure-tjänster

Service Fabric program kan dra nytta av hanterade identiteter för att få åtkomst till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering. Ett program kan hämta en [åtkomsttoken som representerar](../active-directory/develop/developer-glossary.md#access-token) identiteten, som kan vara systemtilldelad eller tilldelad, och använda den som en "Bearer"-token för att autentisera sig mot en annan tjänst, även kallat en [skyddad resurs Server](../active-directory/develop/developer-glossary.md#resource-server). Token representerar den identitet som tilldelats Service Fabric programmet och kommer bara att utfärdas till Azure-resurser (inklusive SF-program) som delar identiteten. Mer detaljerad information om hanterade identiteter finns i [översikts dokumentationen för Managed Identity](../active-directory/managed-identities-azure-resources/overview.md) , samt skillnaden mellan systemtilldelade och användarspecifika identiteter. Vi kommer att referera till ett hanterat identitets aktiverat Service Fabric program som [klient program](../active-directory/develop/developer-glossary.md#client-application) i den här artikeln.

Se ett exempel program som visar hur du använder systemtilldelade och användare som tilldelats [Service Fabric program hanterade identiteter](https://github.com/Azure-Samples/service-fabric-managed-identity) med Reliable Services och behållare.

> [!IMPORTANT]
> En hanterad identitet representerar associationen mellan en Azure-resurs och ett huvud namn för tjänsten i motsvarande Azure AD-klient som är associerad med den prenumeration som innehåller resursen. I samband med Service Fabric stöds hanterade identiteter endast för program som distribueras som Azure-resurser. 

> [!IMPORTANT]
> Innan du använder den hanterade identiteten för ett Service Fabric-program måste klient programmet beviljas åtkomst till den skyddade resursen. Se listan över [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) för att kontrol lera om det finns stöd och till respektive tjänsts dokumentation för specifika steg för att ge en identitets åtkomst till resurser av intresse. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Utnyttja en hanterad identitet med Azure. Identity

Azure Identity SDK stöder nu Service Fabric. Med hjälp av Azure. Identity kan du göra det enklare att skriva kod som använder Service Fabric hanterade identiteter, eftersom det hanterar hämtning av tokens, cachelagring av tokens och serverautentisering. När du använder de flesta Azure-resurser är begreppet token dolt.

Service Fabric support finns i följande versioner för dessa språk: 
- [C# i version 1.3.0](https://www.nuget.org/packages/Azure.Identity). Se ett [C#-exempel](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python i version 1.5.0](https://pypi.org/project/azure-identity/). Se ett [python-exempel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java i version 1.2.0](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

C#-exempel på inledande autentiseringsuppgifter och använda autentiseringsuppgifterna för att hämta en hemlighet från Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Skaffa en åtkomsttoken med hjälp av REST API
I kluster som är aktiverade för hanterad identitet exponerar Service Fabric runtime en localhost-slutpunkt som program kan använda för att hämta åtkomsttoken. Slut punkten är tillgänglig på varje nod i klustret och är tillgänglig för alla entiteter på den noden. Auktoriserade anropare kan hämta åtkomsttoken genom att anropa den här slut punkten och presentera en autentiseringsnyckel. koden genereras av Service Fabric runtime för varje distinkt service code-aktivering och är kopplad till livs längden för processen som är värd för tjänst kods paketet.

Mer specifikt kommer miljön för en hanterad identitets aktive rad Service Fabric-tjänst att dirigeras med följande variabler:
- IDENTITY_ENDPOINT: den localhost-slutpunkt som motsvarar tjänstens hanterade identitet
- IDENTITY_HEADER: en unik autentiseringsnyckel som representerar tjänsten på den aktuella noden
- IDENTITY_SERVER_THUMBPRINT: tumavtryck för den hanterade identitets servern för Service Fabric

> [!IMPORTANT]
> Program koden bör ta hänsyn till värdet för miljövariabeln "IDENTITY_HEADER" som känsliga data – den bör inte loggas eller spridas på annat sätt. Autentiseringsmetoden har inget värde utanför den lokala noden, eller efter att processen som är värd för tjänsten har avbrutits, men den representerar identiteten för den Service Fabric tjänsten och bör därför behandlas med samma försiktighets åtgärder som själva åtkomsttoken.

Klienten utför följande steg för att hämta en token:
- formar en URI genom att sammanfoga den hanterade identitets slut punkten (IDENTITY_ENDPOINT värde) med API-versionen och resursen (mål gruppen) som krävs för token
- skapar en GET http (s)-begäran för angiven URI
- lägger till korrekt validerings logik för Server certifikat
- lägger till authentication code (IDENTITY_HEADER värde) som en rubrik i begäran
- skickar begäran

Ett lyckat svar innehåller en JSON-nyttolast som representerar den resulterande åtkomsttoken, samt metadata som beskriver den. Ett misslyckat svar kommer även att innehålla en förklaring av problemet. Se nedan om du vill ha mer information om fel hantering.

Åtkomsttoken cachelagras av Service Fabric på olika nivåer (nod, kluster, Resource Provider-tjänst), så ett lyckat svar innebär inte nödvändigt vis att token har utfärdats direkt som svar på användarens begäran. Token cachelagras under mindre än deras livs längd och så att ett program garanterat får en giltig token. Vi rekommenderar att program koden cachelagrar sig själv alla åtkomsttoken som den hämtar. caching-nyckeln ska innehålla (en härledning av) mål gruppen. 

Exempel förfrågan:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
där:

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slut punkten. I det här fallet en OAuth-åtkomsttoken. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Den hanterade identitets slut punkten för Service Fabric program som tillhandahålls via miljövariabeln IDENTITY_ENDPOINT. |
| `api-version` | En frågesträngparametern, som anger API-versionen för den hanterade identitets-token-tjänsten. för närvarande är det enda godkända värdet `2019-07-01-preview` och kan komma att ändras. |
| `resource` | En frågesträngparametern som anger URI för app-ID för mål resursen. Detta kommer att avspeglas som `aud` mål för den utfärdade token. I det här exemplet begärs en token för åtkomst till Azure Key Vault, vars app-ID-URI är https: \/ /Vault.Azure.net/. |
| `Secret` | Ett huvud fält för HTTP-begäran som krävs av den Service Fabric Managed Identity token-tjänsten för att Service Fabric Services ska kunna autentisera anroparen. Det här värdet tillhandahålls av SF-körningen via IDENTITY_HEADER-miljövariabeln. |


Exempel svar:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
där:

| Element | Beskrivning |
| ------- | ----------- |
| `token_type` | Typ av token; i det här fallet är en "Bearer"-åtkomsttoken, vilket innebär att presentatören ("Bearer") för denna token är det avsedda ämnet för token. |
| `access_token` | Den begärda åtkomsttoken. När du anropar en skyddad REST API, bäddas token in i `Authorization` fältet begär ande huvud som en "Bearer"-token, vilket gör att API: et kan autentisera anroparen. | 
| `expires_on` | Tidsstämpeln för förfallo datum för åtkomsttoken. representeras som antalet sekunder från "1970-01-01T0:0: 0Z UTC" och motsvarar tokens `exp` anspråk. I det här fallet upphör token att gälla den 2019-08-08T06:10:11 + 00:00 (i RFC 3339)|
| `resource` | Resursen för vilken åtkomsttoken utfärdades, som anges via frågesträngparametern `resource` ' AUD '-anspråk, motsvarar token ' '-anspråk. |


## <a name="acquiring-an-access-token-using-c"></a>Skaffa en åtkomsttoken med hjälp av C #
Ovanstående blir i C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Få åtkomst till Key Vault från ett Service Fabric program med hanterad identitet
Det här exemplet bygger på ovanstående för att demonstrera åtkomst till en hemlighet som lagras i en Key Vault med hjälp av hanterad identitet.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Felhantering
I fältet status kod i HTTP-svarshuvuden anges framgångs status för begäran. statusen "200 OK" anger att åtgärden har slutförts och svaret innehåller åtkomsttoken enligt beskrivningen ovan. Följande är en kort uppräkning av möjliga fel svar.

| Statuskod | Fel Orsak | Så här hanterar du |
| ----------- | ------------ | ------------- |
| 404 hittades inte. | Okänd verifieringsmetod, eller så har programmet inte tilldelats någon hanterad identitet. | Rätta till programinstallationen eller koden för token-hämtning. |
| 429 för många begär Anden. |  Begränsnings gränsen har nåtts, vilket införs av AAD eller SF. | Försök igen med exponentiell backoff. Se rikt linjer nedan. |
| 4xx-fel i begäran. | En eller flera av parametrarna för begäran var felaktiga. | Försök inte igen.  Granska fel informationen för mer information.  4xx-fel är design tids fel.|
| 5xx-fel från tjänsten. | Under systemet för hanterad identitet eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter en kort stund. Du kan träffa ett begränsnings villkor (429) när du försöker igen.|

Om ett fel inträffar innehåller motsvarande HTTP-svar ett JSON-objekt med fel information:

| Element | Beskrivning |
| ------- | ----------- |
| kod | Felkod. |
| correlationId | Ett korrelations-ID som kan användas för fel sökning. |
| meddelande | Utförlig beskrivning av felet. **Fel beskrivningar kan ändras när som helst. Är inte beroende av själva fel meddelandet.**|

Exempel fel:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Följande är en lista över vanliga Service Fabric fel som är speciella för hanterade identiteter:

| Kod | Meddelande | Beskrivning | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Det gick inte att hitta hemligheten i begärandehuvuden. | Autentiserings koden har inte angetts med begäran. | 
| ManagedIdentityNotFound | Hanterad identitet hittades inte för den angivna program värden. | Programmet saknar identitet eller så är autentiserings koden okänd. |
| ArgumentNullOrEmpty | Parametern Resource får inte vara null eller en tom sträng. | Resursen (Audience) tillhandahölls inte i begäran. |
| InvalidApiVersion | API-versionen stöds inte. Versionen som stöds är 2019-07-01-Preview. | Den API-version som saknas eller inte stöds anges i URI: n för begäran. |
| InternalServerError | Ett fel inträffade. | Ett fel påträffades i under systemet för hanterad identitet, eventuellt utanför Service Fabric stacken. Den mest sannolika orsaken är ett felaktigt värde som har angetts för resursen (Sök efter efterföljande '/'?) | 

## <a name="retry-guidance"></a>Vägledning för nytt försök 

Den enda felkoden som kan återförsök är vanligt vis 429 (för många begär Anden). interna Server fel/5xx fel koder kan gå att försöka igen, även om orsaken kan vara permanent. 

Begränsnings gränser gäller för det antal anrop som görs till under systemet för hanterad identitet – särskilt de överordnade beroenden (den hanterade identiteten Azure-tjänst eller Secure token service). Service Fabric cachelagrar tokens på olika nivåer i pipelinen, men med tanke på de berörda komponenternas distribuerade egenskaper, kan anroparen uppleva inkonsekventa begränsnings svar (d.v.s. begränsas av en nod/instans av ett program, men inte på en annan nod, medan en token för samma identitet begärs.) När begränsnings villkoret har angetts kan efterföljande förfrågningar från samma program Miss lyckas med HTTP-statuskod 429 (för många begär Anden) tills villkoret är avmarkerat.  

Vi rekommenderar att begär Anden som Miss lyckas på grund av begränsningen provas med en exponentiell backoff, enligt följande: 

| Anrops index | Åtgärd vid mottagande av 429 | 
| --- | --- | 
| 1 | Vänta 1 sekund och försök igen |
| 2 | Vänta 2 sekunder och försök igen |
| 3 | Vänta 4 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 5 | Vänta 16 sekunder och försök igen |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster
Se [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) för en lista över resurser som stöder Azure AD och deras respektive resurs-ID.

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
* [Utforska ett exempel program med Service Fabric hanterad identitet](https://github.com/Azure-Samples/service-fabric-managed-identity)
