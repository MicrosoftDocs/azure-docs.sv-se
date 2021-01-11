---
title: Klientens försäkrar (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Läs om stöd för signerade klienter för konfidentiella klient program i Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/30/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: f1ff679bddf2afc355516f2a04b3307d4a260a5c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063628"
---
# <a name="confidential-client-assertions"></a>Konfidentiell klient kontroll

För att bevisa sin identitet utbyter konfidentiella klient program en hemlighet med Azure AD. Hemligheten kan vara:
- En klient hemlighet (program lösen ord).
- Ett certifikat som används för att bygga en signerad försäkran som innehåller standard anspråk.

Den här hemligheten kan också vara en signerad kontroll direkt.

MSAL.NET har fyra metoder för att ange autentiseringsuppgifter eller intyg för den konfidentiella klient appen:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Även om det är möjligt att använda `WithClientAssertion()` API: et för att hämta tokens för den konfidentiella klienten rekommenderar vi inte att du använder den som standard eftersom den är mer avancerad och har utformats för att hantera mycket olika scenarier som inte är vanliga. Med hjälp av `.WithCertificate()` API: et kan MSAL.net hantera detta åt dig. Med det här API: et kan du anpassa din autentiseringsbegäran om det behövs, men den standard kontroll som skapas av `.WithCertificate()` är tillräckligt för de flesta autentiserings scenarier. Detta API kan också användas som en lösning i vissa scenarier där MSAL.NET inte kan utföra signerings åtgärden internt.

### <a name="signed-assertions"></a>Signerade kontroller

En signerad klient kontroll tar formen av en signerad JWT med nytto lasten som innehåller de autentiseringsuppgifter som krävs av Azure AD, Base64-kodat. Så här använder du den:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

De [anspråk som förväntas av Azure AD](active-directory-certificate-credentials.md) är:

Anspråkstyp | Värde | Beskrivning
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Anspråket "AUD" (Audience) identifierar mottagarna som JWT är avsett för (här Azure AD) se [RFC 7519, avsnitt 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  I det här fallet är mottagaren inloggnings Server (login.microsoftonline.com).
exp | 1601519414 | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning. Se [RFC 7519, section 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Detta gör att kontrollen kan användas tills dess, så håll den kort 5-10 minuter efter `nbf` högst.  Azure AD har ingen begränsning för den `exp` aktuella tiden. 
ISS | ClientID | Anspråket "ISS" (utfärdare) identifierar det huvud konto som utfärdade JWT, i det här fallet klient programmet.  Använd ID för GUID-program.
jti | (ett GUID) | Anspråket "JTI" (JWT ID) tillhandahåller en unik identifierare för JWT. Identifier-värdet måste tilldelas på ett sätt som garanterar att det är en försumbar sannolikhet att samma värde har tilldelats av misstag till ett annat data objekt. om programmet använder flera utfärdare, måste kollisioner förhindras mellan värden som skapas av olika utfärdare. Värdet "JTI" är en Skift läges känslig sträng. [RFC 7519, avsnitt 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning. [RFC 7519, avsnitt 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Det är lämpligt att använda den aktuella tiden. 
Build | ClientID | Anspråket "sub" (subject) identifierar ämnet för JWT, i det här fallet även ditt program. Använd samma värde som `iss` . 

Här är ett exempel på hur du kan hantverka dessa anspråk:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Så här kan du skapa en signerad klient kontroll:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternativ metod

Du kan också välja att använda [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) för att skapa en kontroll. Koden får ett mer elegant som visas i exemplet nedan:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

När du har en signerad klient kan du använda den med MSAL-API: erna som visas nedan.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` som standard skapas en signerad kontroll som innehåller de anspråk som förväntas av Azure AD plus ytterligare klient anspråk som du vill skicka. Här är ett kodfragment om hur du gör det.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Om något av anspråken i ord listan som du skickar in är samma som ett av de obligatoriska anspråken, tas det ytterligare anspråkets värde med i beräkningen. Den åsidosätter de anspråk som beräknas av MSAL.NET.

Om du vill ange egna anspråk, inklusive obligatoriska anspråk som förväntas av Azure AD, kan du skicka in `false` för- `mergeWithDefaultClaims` parametern.
