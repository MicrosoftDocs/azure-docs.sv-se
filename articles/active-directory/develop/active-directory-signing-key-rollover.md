---
title: Signering av nyckel förnyelse i Microsoft Identity Platform
description: Den här artikeln beskriver metod tips för förnyelse av signerings nyckel för Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: a8c9a15761a4b37dfcf5ba7cc4cf046390092145
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672153"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Signering av nyckel förnyelse i Microsoft Identity Platform
Den här artikeln beskriver vad du behöver veta om de offentliga nycklar som används av Microsoft Identity Platform för att signera säkerhetstoken. Det är viktigt att notera att dessa nycklar är i regelbunden följd och att de i nödfall kan överföras direkt. Alla program som använder Microsoft Identity Platform bör kunna hantera nyckel förnyelse processen program mässigt. Fortsätt att läsa för att förstå hur nycklarna fungerar, hur du bedömer effekten av överrullningen till ditt program och hur du uppdaterar programmet eller upprättar en periodisk manuell förnyelse process för att hantera nyckel förnyelse vid behov.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Översikt över signerings nycklar i Microsoft Identity Platform
Microsoft Identity Platform använder kryptering med offentliga nycklar som bygger på bransch standarder för att upprätta förtroende mellan sig och de program som använder den. I praktiska termer fungerar detta på följande sätt: Microsoft Identity Platform använder en signerings nyckel som består av ett offentligt och privat nyckel par. När en användare loggar in till ett program som använder Microsoft Identity Platform för autentisering, skapar Microsoft Identity Platform en säkerhetstoken som innehåller information om användaren. Den här token signeras av Microsoft Identity Platform med sin privata nyckel innan den skickas tillbaka till programmet. För att verifiera att token är giltig och härstammar från Microsoft Identity Platform, måste programmet verifiera token signaturen med hjälp av de offentliga nycklar som exponeras av Microsoft Identity Platform som finns i klient organisationens [OpenID Connect Discovery-dokument](https://openid.net/specs/openid-connect-discovery-1_0.html) eller SAML/WS-utfodras [Federation Metadata Document](../azuread-dev/azure-ad-federation-metadata.md).

Av säkerhets synpunkt kan Microsoft Identity Platforms signerings nyckel slås samman regelbundet och, i händelse av en nöd situation, kunna överföras omedelbart. Det finns ingen angiven eller garanterad tid mellan dessa nyckel kast – alla program som integreras med Microsoft Identity Platform bör förberedas för att hantera en nyckel förnyelse händelse oavsett hur ofta den kan inträffa. Om det inte är det och ditt program försöker använda en nyckel som har gått ut för att verifiera signaturen på en token, kommer inloggnings förfrågan att Miss lyckas.  Att kontrol lera var 24: e timme för uppdateringar är ett bra tillvägagångs sätt, med en begränsning (en gång var femte minut högst) omedelbart uppdateringar av nyckel dokumentet om en token påträffas med en okänd nyckel identifierare. 

Det finns alltid fler än en giltig nyckel som är tillgänglig i OpenID Connect Discovery-dokumentet och federationsmetadata. Ditt program bör förberedas för användning av alla nycklar och alla nycklar som anges i dokumentet, eftersom en nyckel kan ställas in snart, en annan kan ersätta den och så vidare.  Antalet nycklar som finns kan ändras över tid baserat på den interna arkitekturen hos Microsoft Identity Platform när vi stöder nya plattformar, nya moln eller nya autentiseringsprotokoll. Varken ordningen på nycklarna i JSON-svaret eller i vilken ordning de exponerades bör anses vara meaninful i din app. 

Program som bara har stöd för en enda signerings nyckel eller de som kräver manuella uppdateringar av signerings nycklarna är i själva fall mindre säkra och tillförlitliga.  De bör uppdateras för att använda [standard bibliotek](reference-v2-libraries.md) för att säkerställa att de alltid använder uppdaterade signerings nycklar, bland annat bästa praxis. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Så här bedömer du om ditt program kommer att påverkas och vad du ska göra
Hur ditt program hanterar nyckel förnyelse beror på variabler som typ av program eller vilka identitets protokoll och bibliotek som användes. I avsnitten nedan bedöms om de vanligaste typerna av program påverkas av nyckel förnyelsen och ger vägledning om hur du uppdaterar programmet så att det stöder automatisk förnyelse eller manuellt uppdaterar nyckeln.

* [Interna klient program som har åtkomst till resurser](#nativeclient)
* [Webb program/API: er som har åtkomst till resurser](#webclient)
* [Webb program/API: er som skyddar resurser och bygger på Azure App Services](#appservices)
* [Webb program/API: er som skyddar resurser med .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellan program](#owin)
* [Webb program/API: er som skyddar resurser med .NET Core OpenID Connect eller JwtBearerAuthentication mellan program](#owincore)
* [Webb program/API: er som skyddar resurser med Node.js Passport-Azure-AD-modul](#passport)
* [Webb program/API: er som skyddar resurser och som skapats med Visual Studio 2015 eller senare](#vs2015)
* [Webb program som skyddar resurser och som skapats med Visual Studio 2013](#vs2013)
* Webb-API: er som skyddar resurser och som skapats med Visual Studio 2013
* [Webb program som skyddar resurser och som skapats med Visual Studio 2012](#vs2012)
* [Webb program som skyddar resurser och som skapats med Visual Studio 2010, 2008 o med hjälp av Windows Identity Foundation](#vs2010)
* [Webb program/API: er som skyddar resurser med andra bibliotek eller manuellt implementerar något av de protokoll som stöds](#other)

Den här vägledningen gäller **inte** för:

* Program som har lagts till från Azure AD-programgalleriet (inklusive anpassade) har separat vägledning för signering av nycklar. [Mer information.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Lokala program som publiceras via programproxy behöver inte oroa dig för signerings nycklar.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Interna klient program som har åtkomst till resurser
Program som bara kommer åt resurser (dvs. Microsoft Graph, nyckel valv, Outlook-API och andra Microsoft API: er (Microsoft API: er) kan vanligt vis bara hämta en token och skicka den till resurs ägaren. Med tanke på att de inte skyddar några resurser, kontrollerar de inte token och behöver därför inte kontrol lera att de är korrekt signerade.

Interna klient program, oavsett om de är Station ära eller mobila, omfattas av den här kategorin och påverkas därför inte av förnyelsen.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webb program/API: er som har åtkomst till resurser
Program som bara kommer åt resurser (dvs. Microsoft Graph, nyckel valv, Outlook-API och andra Microsoft API: er (Microsoft API: er) kan vanligt vis bara hämta en token och skicka den till resurs ägaren. Med tanke på att de inte skyddar några resurser, kontrollerar de inte token och behöver därför inte kontrol lera att de är korrekt signerade.

Webb program och webb-API: er som använder app-only-flödet (klientautentiseringsuppgifter/klient certifikat) för att begära token tillhör den här kategorin och påverkas därför inte av förnyelsen.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webb program/API: er som skyddar resurser och bygger på Azure App Services
Azure App Services-funktionen för autentisering/auktorisering (EasyAuth) har redan den logik som krävs för att hantera förnyelse av nycklar automatiskt.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webb program/API: er som skyddar resurser med .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellan program
Om ditt program använder .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellan program, har den redan den logik som krävs för att hantera nyckel förnyelse automatiskt.

Du kan bekräfta att ditt program använder något av dessa genom att söka efter något av följande kodfragment i programmets Startup.cs-eller Startup.Auth.cs-filer.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webb program/API: er som skyddar resurser med .NET Core OpenID Connect eller JwtBearerAuthentication mellan program
Om ditt program använder .NET Core OWIN OpenID Connect eller JwtBearerAuthentication mellan program, har den redan den logik som krävs för att hantera nyckel förnyelse automatiskt.

Du kan bekräfta att ditt program använder något av dessa genom att söka efter något av följande kodfragment i programmets Startup.cs eller Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webb program/API: er som skyddar resurser med Node.js Passport-Azure-AD-modul
Om programmet använder Node.js Passport-AD-modulen har den redan den logik som krävs för att hantera förnyelse av nycklar automatiskt.

Du kan bekräfta att ditt program Passport-AD genom att söka efter följande kodfragment i programmets app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webb program/API: er som skyddar resurser och som skapats med Visual Studio 2015 eller senare
Om ditt program har skapats med hjälp av en mall för webb program i Visual Studio 2015 eller senare och du har valt **arbets-eller skol konton** från menyn **ändra autentisering** , har det redan den logik som krävs för att hantera förnyelse av nycklar automatiskt. Den här logiken är inbäddad i OWIN OpenID Connect-mellanprogram, hämtar och cachelagrar nycklarna från OpenID Connect Discovery-dokumentet och uppdaterar dem regelbundet.

Om du har lagt till autentisering till din lösning manuellt kanske programmet saknar nödvändig logik för nyckel förnyelse. Du måste skriva den själv eller följa stegen i [webb program/API: er med hjälp av andra bibliotek eller manuellt implementera något av de protokoll som stöds](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webb program som skyddar resurser och som skapats med Visual Studio 2013
Om ditt program har skapats med hjälp av en mall för webb program i Visual Studio 2013 och du har valt **organisations konton** från menyn **ändra autentisering** , har det redan den logik som krävs för att hantera förnyelse av nycklar automatiskt. Den här logiken lagrar organisationens unika identifierare och information om signerings nyckeln i två databas tabeller som är kopplade till projektet. Du kan hitta anslutnings strängen för databasen i projektets Web.config-fil.

Om du har lagt till autentisering till din lösning manuellt kanske programmet saknar nödvändig logik för nyckel förnyelse. Du måste skriva den själv eller följa stegen i [webb program/API: er med hjälp av andra bibliotek eller manuellt implementera något av de protokoll som stöds.](#other).

Med följande steg kan du kontrol lera att logiken fungerar korrekt i ditt program.

1. Öppna lösningen i Visual Studio 2013 och klicka sedan på fliken **Server Explorer** i det högra fönstret.
2. Expandera **data anslutningar**, **DefaultConnection** och sedan **tabeller**. Leta upp tabellen **IssuingAuthorityKeys** , högerklicka på den och klicka sedan på **Visa tabell data**.
3. Det finns minst en rad i tabellen **IssuingAuthorityKeys** som motsvarar värdet för tumavtrycket för nyckeln. Ta bort alla rader i tabellen.
4. Högerklicka på tabellen **klienter** och klicka sedan på **Visa tabell data**.
5. Det finns minst en rad i tabellen **klienter** som motsvarar en unik identifierare för katalog klient organisationen. Ta bort alla rader i tabellen. Om du inte tar bort raderna i både tabellen **Tenants** och **IssuingAuthorityKeys** , får du ett fel vid körning.
6. Skapa och kör programmet. När du har loggat in på ditt konto kan du stoppa programmet.
7. Gå tillbaka till **Server Explorer** och titta på värdena i tabellen **IssuingAuthorityKeys** och **klient organisationer** . Du märker att de fyllts i automatiskt med lämplig information från federationsmetadata.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webb-API: er som skyddar resurser och som skapats med Visual Studio 2013
Om du har skapat ett webb-API-program i Visual Studio 2013 med hjälp av webb-API-mallen och sedan valt **organisations konton** från menyn **ändra autentisering** , har du redan den nödvändiga logiken i programmet.

Om du har konfigurerat autentisering manuellt följer du anvisningarna nedan och lär dig hur du konfigurerar ditt webb-API för att automatiskt uppdatera dess nyckelinformation.

Följande kodfragment visar hur du hämtar de senaste nycklarna från federationsmetadata och använder sedan [JWT token-hanteraren](/previous-versions/dotnet/framework/security/json-web-token-handler) för att validera token. Kodfragmentet förutsätter att du använder din egen caching-mekanism för att spara nyckeln för att validera framtida token från Microsoft Identity Platform, oavsett om det är en databas, en konfigurations fil eller någon annan stans.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webb program som skyddar resurser och som skapats med Visual Studio 2012
Om ditt program har skapats i Visual Studio 2012 använde du förmodligen verktyget identitets-och åtkomst verktyg för att konfigurera ditt program. Det är också troligt att du använder [VINR (verifiera utfärdarens namn register)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). VINR ansvarar för att underhålla information om betrodda identitets leverantörer (Microsoft Identity Platform) och de nycklar som används för att validera token som utfärdats av dem. VINR gör det också enkelt att automatiskt uppdatera nyckelinformation som lagras i en Web.config-fil genom att hämta det senaste dokumentet för federationsmetadata som är associerat med din katalog, kontrol lera om konfigurationen är inaktuell med det senaste dokumentet och uppdatera programmet så att det använder den nya nyckeln vid behov.

Om du har skapat programmet med något av kod exemplen eller genom gångs dokumentationen från Microsoft ingår nyckel förnyelse logiken redan i projektet. Du ser att koden nedan redan finns i ditt projekt. Om programmet inte redan har den här logiken följer du stegen nedan för att lägga till den och kontrol lera att den fungerar som den ska.

1. I **Solution Explorer** lägger du till en referens till sammansättningen **system. IdentityModel** för lämpligt projekt.
2. Öppna filen **Global.asax.cs** och Lägg till följande med hjälp av direktiv:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Lägg till följande metod i **Global.asax.cs** -filen:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Anropa metoden **RefreshValidationSettings ()** i metoden **Application_Start ()** i **Global.asax.cs** som visas:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

När du har följt de här stegen kommer programmets Web.config att uppdateras med den senaste informationen från dokumentet för federationsmetadata, inklusive de senaste nycklarna. Den här uppdateringen sker varje gång din programpool återanvänds i IIS. som standard är IIS inställd på att återvinna program var 29: e timme.

Följ stegen nedan för att kontrol lera att logiken för nyckel förnyelse fungerar.

1. När du har kontrollerat att programmet använder koden ovan öppnar du **Web.config** -filen och navigerar till **\<issuerNameRegistry>** blocket och letar efter följande rader:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. I **\<add thumbprint="">** inställningen ändrar du tumavtrycket genom att ersätta alla bokstäver med ett annat. Spara filen **Web.config**.
3. Skapa programmet och kör det. Om du kan slutföra inloggnings processen uppdaterar programmet nyckeln genom att ladda ned den information som krävs från katalogens dokument för federationsmetadata. Om du har problem med att logga in, se till att ändringarna i programmet är korrekta genom att läsa [lägga till Sign-On i ditt webb program med hjälp av Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) -artikeln eller ladda ned och inspektera följande kod exempel: [flera innehavares moln program för Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webb program skyddar resurser och har skapats med Visual Studio 2008 eller 2010 och Windows Identity Foundation (WIF) v 1.0 för .NET 3,5
Om du har byggt ett program på WIF v 1.0 finns det ingen funktion för att automatiskt uppdatera programmets konfiguration för att använda en ny nyckel.

* *Enklaste sättet* Använd FedUtil-verktyget som ingår i WIF SDK, som kan hämta det senaste Metadatadokumentet och uppdatera konfigurationen.
* Uppdatera ditt program till .NET 4,5, som innehåller den senaste versionen av WIF som finns i systemets namnrymd. Du kan sedan använda [verifiering av utfärdarens namn register (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) för att utföra automatiska uppdateringar av programmets konfiguration.
* Utför en manuell förnyelse enligt anvisningarna i slutet av det här vägledning dokumentet.

Instruktioner för att använda FedUtil för att uppdatera konfigurationen:

1. Kontrol lera att WIF v 1.0 SDK är installerat på utvecklings datorn för Visual Studio 2008 eller 2010. Du kan [Ladda ned det här](https://www.microsoft.com/en-us/download/details.aspx?id=4451) om du inte har installerat det än.
2. Öppna lösningen i Visual Studio och högerklicka sedan på tillämpligt projekt och välj **Uppdatera federationsmetadata**. Om det här alternativet inte är tillgängligt är FedUtil och/eller WIF v 1.0 SDK inte installerat.
3. I prompten väljer du **Uppdatera** för att börja uppdatera federationsmetadata. Om du har åtkomst till den server miljö där programmet finns kan du välja att använda FedUtil för [Automatisk uppdatering av metadata](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10)).
4. Slutför uppdaterings processen genom att klicka på **Slutför** .

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webb program/API: er som skyddar resurser med andra bibliotek eller manuellt implementerar något av de protokoll som stöds
Om du använder ett annat bibliotek eller manuellt implementerat något av de protokoll som stöds måste du granska biblioteket eller din implementering för att säkerställa att nyckeln hämtas från antingen OpenID Connect Discovery-dokumentet eller federationsmetadata. Ett sätt att söka efter detta är att göra en sökning i din kod eller bibliotekets kod för anrop till antingen OpenID identifierings dokument eller federationsmetadata.

Om nyckeln lagras någonstans eller hårdkodad i ditt program kan du hämta nyckeln manuellt och uppdatera den genom att utföra en manuell förnyelse enligt anvisningarna i slutet av det här väglednings dokumentet. **Vi rekommenderar starkt att du förbättrar ditt program så att det stöder automatisk överrullning** med någon av de metoder som beskrivs i den här artikeln för att undvika framtida avbrott och omkostnader om Microsoft Identity Platform ökar sin rollover-takt eller har en nöd utökning.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Så här testar du programmet för att avgöra om det kommer att påverkas
Du kan kontrol lera om ditt program stöder automatisk nyckel förnyelse genom att ladda ned skripten och följa instruktionerna i [den här GitHub-lagringsplatsen.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Utföra en manuell förnyelse om programmet inte stöder automatisk förnyelse
Om programmet **inte** stöder automatisk förnyelse måste du upprätta en process som regelbundet övervakar signerings nycklarna i Microsoft Identity Platform och utför en manuell förnyelse. [Den här GitHub-lagringsplatsen](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) innehåller skript och instruktioner för hur du gör detta.