---
title: 'Konfigurera daemon-appar som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er (app-konfiguration)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 3c52d4d80fd3c77cff5e335967fc9d109212ce29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578438"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app som anropar webb-API: er – kod konfiguration

Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er.

## <a name="microsoft-libraries-supporting-daemon-apps"></a>Microsoft-bibliotek som stöder daemon-appar

Följande Microsoft-bibliotek stöder daemon-appar:

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="configure-the-authority"></a>Konfigurera utfärdaren

Daemon-program använder program behörigheter i stället för delegerade behörigheter. Deras konto typer som stöds får inte vara ett konto i någon organisations katalog eller någon personlig Microsoft-konto (till exempel Skype, Xbox, Outlook.com). Det finns ingen innehavaradministratör för att bevilja medgivande till ett daemon-program för ett personligt Microsoft-konto. Du måste välja *konton i min organisation* eller *konton i alla organisationer*.

Den auktoritet som anges i program konfigurationen ska vara klient organisation (som anger ett klient-ID eller ett domän namn som är kopplat till din organisation).

Även om du vill tillhandahålla ett verktyg för flera innehavare bör du använda ett klient-ID eller domän namn, **inte** `common` eller `organizations` med det här flödet, eftersom tjänsten inte kan härleda vilken klient som ska användas.

## <a name="configure-and-instantiate-the-application"></a>Konfigurera och instansiera programmet

I MSAL-bibliotek skickas klientens autentiseringsuppgifter (hemlighet eller certifikat) som en parameter för den konfidentiella klient program konstruktionen.

> [!IMPORTANT]
> Även om ditt program är ett konsol program som körs som en tjänst, om det är ett daemon-program, måste det vara ett konfidentiellt klient program.

### <a name="configuration-file"></a>Konfigurationsfil

Konfigurations filen definierar:

- Moln instansen och klient-ID: t tillsammans utgör *utfärdaren*.
- Det klient-ID som du fick från program registreringen.
- Antingen en klient hemlighet eller ett certifikat.

# <a name="net"></a>[.NET](#tab/dotnet)

Här är ett exempel på hur du definierar konfigurationen i en [*appsettings.jsi*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) filen. Det här exemplet hämtas från kod exemplet för [.net Core Console daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) på GitHub.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Du anger antingen en `ClientSecret` eller en `CertificateName` . De här inställningarna är exklusiva.

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Konfigurations parametrar för [Node.js daemon-exemplet](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console/) finns i en *. miljö* -fil:

```Text 
# Credentials
TENANT_ID=Enter_the_Tenant_Info_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

# <a name="python"></a>[Python](#tab/python)

När du skapar en konfidentiell klient med klient hemligheter är [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) i konfigurations filen i [python-bakgrunds](https://github.com/Azure-Samples/ms-identity-python-daemon) exemplet följande:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

När du skapar en konfidentiell klient med certifikat, är [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) i konfigurations filen i [python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -exemplet följande:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

---

### <a name="instantiate-the-msal-application"></a>Instansiera MSAL-programmet

Om du vill instansiera MSAL-programmet lägger du till, refererar till eller importerar MSAL-paketet (beroende på språket).

Konstruktion skiljer sig beroende på om du använder klient hemligheter eller certifikat (eller som ett avancerat scenario, signerade intyg).

#### <a name="reference-the-package"></a>Referera till paketet

Referera till MSAL-paketet i program koden.

# <a name="net"></a>[.NET](#tab/dotnet)

Lägg till [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paketet i programmet och Lägg sedan till ett `using` direktiv i din kod för att referera till det.

I MSAL.NET representeras det konfidentiella klient programmet av `IConfidentialClientApplication` gränssnittet.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installera bara paketen genom `npm install` att köra i mappen där *package.js* filen finns. Importera sedan **msal-Node-** paket:

```JavaScript 
const msal = require('@azure/msal-node');
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instansiera det konfidentiella klient programmet med en klient hemlighet

Här är koden för att instansiera det konfidentiella klient programmet med en klient hemlighet:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

`Authority`Är en sammanfogning av moln instansen och klient-ID: t, till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com` eller `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd` . I *appsettings.jspå* filen som visas i avsnittet [konfigurations fil](#configuration-file) representeras de av respektive `Instance` `Tenant` värden.

I kod exemplet togs föregående kodfragment från, `Authority` är en egenskap i  [AuthenticationConfig](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70) -klassen och definieras som sådant:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript

const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instansiera det konfidentiella klient programmet med ett klient certifikat

Här är koden för att bygga ett program med ett certifikat:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```
# <a name="java"></a>[Java](#tab/java)

I MSAL Java finns det två byggprogram för att instansiera det konfidentiella klient programmet med certifikat:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

eller

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Exempel programmet implementerar inte initiering med certifikat för tillfället.

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Avancerat scenario: instansiera det konfidentiella klient programmet med klient kontroll

# <a name="net"></a>[.NET](#tab/dotnet)

I stället för en klient hemlighet eller ett certifikat kan det konfidentiella klient programmet också bevisa sin identitet genom att använda klientens kontroll.

MSAL.NET har två metoder för att tillhandahålla signerade kontroller till appen konfidentiell klient:

- `.WithClientAssertion()`
- `.WithClientClaims()`

`WithClientAssertion`Ange ett signerat JWT när du använder. Det här avancerade scenariot beskrivs i [klient kontroll](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

När du använder `WithClientClaims` kommer MSAL.net att skapa en signerad kontroll som innehåller de anspråk som förväntas av Azure AD, plus ytterligare klient anspråk som du vill skicka.
Den här koden visar hur du gör:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Mer information finns i [klient kontroll](msal-net-client-assertions.md).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Exempel programmet implementerar inte initiering med intyg för tillfället.

# <a name="python"></a>[Python](#tab/python)

I MSAL python kan du ange klient anspråk genom att använda anspråken som ska signeras av den `ConfidentialClientApplication` privata nyckeln.

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Mer information finns i referens dokumentationen för MSAL python för [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

---

## <a name="next-steps"></a>Nästa steg

# <a name="net"></a>[.NET](#tab/dotnet)

Gå vidare till nästa artikel i det här scenariot och [Hämta en token för appen](./scenario-daemon-acquire-token.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Gå vidare till nästa artikel i det här scenariot och [Hämta en token för appen](./scenario-daemon-acquire-token.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Gå vidare till nästa artikel i det här scenariot och [Hämta en token för appen](./scenario-daemon-acquire-token.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Gå vidare till nästa artikel i det här scenariot och [Hämta en token för appen](./scenario-daemon-acquire-token.md?tabs=python).

---
