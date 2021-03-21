---
title: Kommunikations säkerhet för Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Lär dig mer om att undvika kommunikations säkerhetshot som exponeras i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517537"
---
# <a name="security-frame-communication-security--mitigations"></a>Säkerhets ram: kommunikations säkerhet | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Skydda kommunikation till Event Hub med SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Kontrol lera behörigheter för tjänst kontot och kontrol lera att de anpassade tjänsterna eller ASP.NET-sidorna respekterar CRM-säkerheten](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Använd Data Management Gateway när du ansluter lokala SQL Server till Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identitets Server** | <ul><li>[Se till att all trafik till identitets servern är över HTTPS-anslutning](#identity-https)</li></ul> |
| **Webb program** | <ul><li>[Verifiera X. 509-certifikat som används för att autentisera SSL-, TLS-och DTLS-anslutningar](#x509-ssltls)</li><li>[Konfigurera TLS/SSL-certifikat för anpassad domän i Azure App Service](#ssl-appservice)</li><li>[Tvinga all trafik att Azure App Service över HTTPS-anslutning](#appservice-https)</li><li>[Aktivera HTTP Strict Transport Security (HSTS)](#http-hsts)</li></ul> |
| **Databas** | <ul><li>[Kontrol lera kryptering av SQL Server-anslutning och certifikat validering](#sqlserver-validation)</li><li>[Framtvinga krypterad kommunikation med SQL Server](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att kommunikationen med Azure Storage är över HTTPS](#comm-storage)</li><li>[Verifiera MD5-hash efter nedladdning av BLOB om HTTPS inte kan aktive ras](#md5-https)</li><li>[Använd SMB 3,0-kompatibel klient för att säkerställa data kryptering under överföring till Azure-filresurser](#smb-shares)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera certifikats fäste](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Aktivera HTTPS – säker transport kanal](#https-transport)</li><li>[WCF: Ange skydds nivå för meddelande säkerhet till EncryptAndSign](#message-protection)</li><li>[WCF: Använd ett konto med minst privilegier för att köra WCF-tjänsten](#least-account-wcf)</li></ul> |
| **Webb-API** | <ul><li>[Tvinga all trafik till webb-API: er över HTTPS-anslutning](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Se till att kommunikationen med Azure cache för Redis är över TLS](#redis-ssl)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Skydda enhet till fält Gateway-kommunikation](#device-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Skydda enhet till Cloud Gateway-kommunikation med SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Skydda kommunikation till Event Hub med SSL/TLS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över Event Hubs autentisering och säkerhets modell](../../event-hubs/authenticate-shared-access-signature.md) |
| **Steg** | Skydda AMQP eller HTTP-anslutningar till Händelsehubben med SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Kontrol lera behörigheter för tjänst kontot och kontrol lera att de anpassade tjänsterna eller ASP.NET-sidorna respekterar CRM-säkerheten

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrol lera behörigheter för tjänst kontot och kontrol lera att de anpassade tjänsterna eller ASP.NET-sidorna respekterar CRM-säkerheten |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Använd Data Management Gateway när du ansluter lokala SQL Server till Azure Data Factory

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Data Factory | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Länkade tjänst typer – Azure och lokalt |
| **Referenser**              |[Flytta data mellan lokala platser och Azure Data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), [Data Management Gateway](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Steg** | <p>Verktyget Data Management Gateway (DMG) krävs för att ansluta till data källor som skyddas bakom Corpnet eller en brand vägg.</p><ol><li>Genom att låsa datorn isolerar du DMG-verktyget och förhindrar att program inte skadar eller snooping på data käll datorn. Otillräcklig. de senaste uppdateringarna måste installeras, aktivera minsta antal portar som krävs, granskning av kontrollerade konton, granskning aktive rad, disk kryptering aktiverat osv.)</li><li>Data Gateway-nyckeln måste roteras med jämna mellanrum eller när DMG tjänst kontots lösen ord förnyas</li><li>Data överföring via länk tjänsten måste vara krypterad</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Se till att all trafik till identitets servern är över HTTPS-anslutning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3 – nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3-distribution](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | Som standard kräver IdentityServer att alla inkommande anslutningar kommer att komma över HTTPS. Det är absolut nödvändigt att kommunikation med IdentityServer görs via säkra transporter. Det finns vissa distributions scenarier som TLS-avlastning där detta krav kan vara avslappnad. Mer information finns på distributions sidan för identitets servern i referenserna. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verifiera X. 509-certifikat som används för att autentisera SSL-, TLS-och DTLS-anslutningar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Program som använder SSL, TLS eller DTLS måste kontrol lera X. 509-certifikaten för de entiteter som de ansluter till. Detta inkluderar verifiering av certifikaten för:</p><ul><li>Domännamn</li><li>Giltighets datum (både start-och utgångs datum)</li><li>Återkallnings status</li><li>Användning (till exempel Server-autentisering för servrar, klientautentisering för klienter)</li><li>Förtroende kedja. Certifikat måste vara kedja till en rot certifikat utfärdare (CA) som är betrodd av plattformen eller som uttryckligen konfigureras av administratören</li><li>Nyckel längden för certifikatets offentliga nyckel måste vara >2048 bitar</li><li>Hash-algoritmen måste vara SHA256 och högre |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurera TLS/SSL-certifikat för anpassad domän i Azure App Service

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | [Aktivera HTTPS för en app i Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Steg** | Som standard aktiverar Azure HTTPS för varje app med ett certifikat med jokertecken för domänen *. azurewebsites.net. Precis som alla domäner med jokertecken är det dock inte lika säkert som att använda en anpassad domän med egna certifikat, [Se](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Vi rekommenderar att du aktiverar TLS för den anpassade domänen som den distribuerade appen kommer att få åtkomst till via|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Tvinga all trafik att Azure App Service över HTTPS-anslutning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | [Använd HTTPS på Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Steg** | <p>Även om Azure redan aktiverar HTTPS för Azure App Services med ett jokertecken för domänen *. azurewebsites.net, tillämpar den inte HTTPS. Besökare kanske fortfarande har åtkomst till appen via HTTP, som kan kompromettera appens säkerhet och därför måste HTTPS vara påtvingad uttryckligen. ASP.NET MVC-program bör använda [RequireHttps-filtret](/dotnet/api/system.web.mvc.requirehttpsattribute) som tvingar en oskyddad http-begäran att skickas på nytt via https.</p><p>Alternativt kan URL-modulen för att skriva om, som ingår i Azure App Service användas för att genomdriva HTTPS. Med URL-modulen för skrivning kan utvecklare definiera regler som tillämpas på inkommande begär Anden innan begär Anden skickas till ditt program. Regler för URL-omskrivning definieras i en web.config fil som lagras i programmets rot</p>|

### <a name="example"></a>Exempel
I följande exempel finns en grundläggande URL-omskrivning regel som tvingar all inkommande trafik att använda https
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Den här regeln fungerar genom att returnera HTTP-statuskod 301 (permanent omdirigering) när användaren begär en sida med HTTP. 301 omdirigerar begäran till samma URL som den begärda användaren, men ersätter HTTP-delen av begäran med HTTPS. `HTTP://contoso.com`Omdirigera till exempel till `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Aktivera HTTP Strict Transport Security (HSTS)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [OWASP HTTP Strict Transport Security lathund blad](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Steg** | <p>HTTP Strict Transport Security (HSTS) är en valbar säkerhets förbättring som anges av ett webb program med hjälp av ett särskilt svars huvud. När en webbläsare som stöds får den här rubriken att webbläsaren hindrar all kommunikation från att skickas via HTTP till den angivna domänen och kommer istället att skicka all kommunikation via HTTPS. Det förhindrar även HTTPS att klicka via prompter i webbläsare.</p><p>För att implementera HSTS måste följande svars huvud konfigureras för en webbplats globalt, antingen i kod eller i konfig. Strikt-transport-säkerhet: Max-Age = 300; includeSubDomains HSTS åtgärdar följande hot:</p><ul><li>Användar bok märken eller manuellt typer `https://example.com` och är beroende av en man-in-the-Middle-angripare: HSTS omdirigerar automatiskt HTTP-begäranden till https för mål domänen</li><li>Webb program som är avsedda att vara rent HTTPS oavsiktligt innehåller HTTP-länkar eller som hanterar innehåll över HTTP: HSTS omdirigerar automatiskt HTTP-begäranden till HTTPS för mål domänen</li><li>En man-in-the-Middle-angripare försöker avlyssna trafik från en skadelidande-användare med ett ogiltigt certifikat och hoppas att användaren accepterar det felaktiga certifikatet: HSTS tillåter inte att en användare åsidosätter det ogiltiga certifikat meddelandet</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Kontrol lera kryptering av SQL Server-anslutning och certifikat validering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure  |
| **Attribut**              | SQL-version – V12 |
| **Referenser**              | [Metod tips för att skriva säkra anslutnings strängar för SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Steg** | <p>All kommunikation mellan SQL Database och ett klient program krypteras med hjälp av Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL). SQL Database stöder inte okrypterade anslutningar. Om du vill validera certifikat med program kod eller verktyg kan du uttryckligen begära en krypterad anslutning och inte lita på Server certifikaten. Om din program kod eller dina verktyg inte begär en krypterad anslutning får de fortfarande krypterade anslutningar</p><p>De kan dock inte verifiera Server certifikaten och kommer därför att vara utsatta för "man i de mittersta" angrepp. Om du vill validera certifikat med ADO.NET program kod anger `Encrypt=True` och `TrustServerCertificate=False` i databas anslutnings strängen. Om du vill verifiera certifikat via SQL Server Management Studio öppnar du dialog rutan Anslut till server. Klicka på kryptera anslutning på fliken anslutnings egenskaper</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Framtvinga krypterad kommunikation med SQL Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | OnPrem |
| **Attribut**              | SQL-version – MsSQL2016, SQL-version – MsSQL2012, SQL-version-MsSQL2014 |
| **Referenser**              | [Aktivera krypterade anslutningar till databasmotorn](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Steg** | Att aktivera TLS-kryptering ökar säkerheten för data som överförs mellan nätverk mellan instanser av SQL Server och program. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Se till att kommunikationen med Azure Storage är över HTTPS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Azure Storage Transport-Level kryptering – använda HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Steg** | För att säkerställa säkerheten för Azure Storage data överföring använder du alltid HTTPS-protokollet när du anropar REST-API: erna eller använder objekt i lagring. Signaturer för delad åtkomst, som kan användas för att delegera åtkomst till Azure Storage objekt, innehåller också ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, vilket säkerställer att vem skickar ut länkar med SAS-token använder rätt protokoll.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Verifiera MD5-hash efter nedladdning av BLOB om HTTPS inte kan aktive ras

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | StorageType-BLOB |
| **Referenser**              | [Översikt över Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Steg** | <p>Windows Azure Blob Service tillhandahåller mekanismer för att säkerställa data integriteten både på program-och transport lager. Om du av någon anledning behöver använda HTTP i stället för HTTPS och du arbetar med block blobbar, kan du använda MD5-kontroll för att kontrol lera integriteten för de blobbar som överförs</p><p>Detta hjälper till att skydda mot fel på nätverks-/transport nivå, men inte nödvändigt vis med mellanliggande attacker. Om du kan använda HTTPS, som tillhandahåller säkerhet på transport nivå, är det redundant och onödigt att använda MD5-kontrollen.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Använd SMB 3,0-kompatibel klient för att säkerställa data kryptering under överföring till Azure-filresurser

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | StorageType – fil |
| **Referenser**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB-stöd för Windows-klienter](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Steg** | Azure File Storage stöder HTTPS när du använder REST API, men används ofta ofta som en SMB-filresurs som är ansluten till en virtuell dator. SMB 2,1 stöder inte kryptering, så anslutningar tillåts bara inom samma region i Azure. SMB 3,0 stöder dock kryptering och kan användas med Windows Server 2012 R2, Windows 8, Windows 8,1 och Windows 10, vilket ger åtkomst över flera regioner och till och med till gång på Skriv bordet. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementera certifikats fäste

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, Windows Phone |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Certifikat och offentlig nyckel fäste](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Steg** | <p>Certifikat sättning skyddar mot MITM-attacker (man-in-the-Middle). Att fästa är en process för att associera en värd med det förväntade X509-certifikatet eller den offentliga nyckeln. När ett certifikat eller en offentlig nyckel är känd eller visas för en värd, är certifikatet eller den offentliga nyckeln kopplad till värden. </p><p>När en angripare försöker utföra TLS MITM-attack, under TLS-handskakning, kommer nyckeln från angriparens server att skilja sig från det fästa Certifikatets nyckel, och begäran kommer att tas bort, vilket förhindrar att MITM certifikat fästning kan uppnås genom att implementera ServicePointManagers `ServerCertificateValidationCallback` ombud.</p>|

### <a name="example"></a>Exempel
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Aktivera HTTPS – säker transport kanal

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Steg** | Program konfigurationen bör se till att HTTPS används för all åtkomst till känslig information.<ul><li>**Förklaring:** Om ett program hanterar känslig information och inte använder kryptering på meddelande nivå, bör det bara tillåtas att kommunicera via en krypterad transport kanal.</li><li>**Rekommendationer:** Se till att HTTP-transport är inaktive rad och Aktivera HTTPS-transport i stället. Ersätt till exempel `<httpTransport/>` `<httpsTransport/>` taggen with. Förlita dig inte på en nätverks konfiguration (brand vägg) för att garantera att programmet bara kan nås via en säker kanal. Från en Philosophical-plats bör programmet inte vara beroende av nätverket för dess säkerhet.</li></ul><p>Från en praktisk vy kan de personer som ansvarar för att skydda nätverket inte alltid följa säkerhets kraven för programmet när de utvecklas.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Ange skydds nivå för meddelande säkerhet till EncryptAndSign

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Steg** | <ul><li>**Förklaring:** När skydds nivån har angetts till "ingen" inaktive ras meddelande skyddet. Konfidentialitet och integritet uppnås med lämplig inställnings nivå.</li><li>**REKOMMENDERADE**<ul><li>När `Mode=None` -inaktiverar meddelande skydd</li><li>När `Mode=Sign` -tecken, men inte krypterar meddelandet, ska användas när data integriteten är viktig</li><li>När `Mode=EncryptAndSign` -signerar och krypterar meddelandet</li></ul></li></ul><p>Överväg att inaktivera kryptering och bara signera ditt meddelande när du bara behöver validera integriteten för informationen utan problem med sekretess. Detta kan vara användbart för drift-eller tjänste kontrakt där du behöver validera den ursprungliga avsändaren men inga känsliga data överförs. När du minskar skydds nivån bör du vara noga med att meddelandet inte innehåller några personliga data.</p>|

### <a name="example"></a>Exempel
Genom att konfigurera tjänsten och åtgärden för att endast signera meddelandet visas följande exempel. Service kontrakt exempel på `ProtectionLevel.Sign` : följande är ett exempel på hur du använder ProtectionLevel. Sign på service kontrakts nivån: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exempel
Exempel på åtgärds kontrakt `ProtectionLevel.Sign` (för detaljerad kontroll): följande är ett exempel på hur `ProtectionLevel.Sign` du använder på OperationContract nivå:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Använd ett konto med minst privilegier för att köra WCF-tjänsten

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Steg** | <ul><li>**Förklaring:** Kör inte WCF-tjänster under administratörs-eller hög behörighets konto. om tjänster komprometteras leder det till hög påverkan.</li><li>**Rekommendationer:** Använd ett konto med minst privilegier för att vara värd för WCF-tjänsten eftersom det minskar ditt programs attack yta och minskar risken för angrepp om du angrips. Om tjänst kontot kräver ytterligare åtkomst behörighet för infrastruktur resurser som MSMQ, händelse loggen, prestanda räknarna och fil systemet, bör lämpliga behörigheter ges till dessa resurser så att WCF-tjänsten kan köras utan problem.</li></ul><p>Om tjänsten behöver åtkomst till vissa resurser för den ursprungliga anroparen, använder du personifiering och delegering för att flöda anroparens identitet för en underordnad verifierings kontroll. I ett utvecklings scenario använder du det lokala nätverks tjänst kontot, som är ett särskilt inbyggt konto som har lägre privilegier. I ett produktions scenario skapar du ett anpassat domän tjänst konto med minst privilegier.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Tvinga all trafik till webb-API: er över HTTPS-anslutning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Tvinga SSL i en webb-API-kontroll](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Steg** | Om ett program har både en HTTPS och en HTTP-bindning kan klienter fortfarande använda HTTP för att komma åt platsen. Undvik detta genom att använda ett åtgärds filter för att säkerställa att förfrågningar till skyddade API: er alltid är över HTTPS.|

### <a name="example"></a>Exempel 
Följande kod visar ett webb-API-autentiseringsschema som kontrollerar TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Lägg till det här filtret till alla webb-API-åtgärder som kräver TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Se till att kommunikationen med Azure cache för Redis är över TLS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Cache for Redis | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Stöd för Azure Redis TLS](../../azure-cache-for-redis/cache-faq.md) |
| **Steg** | Redis-servern har inte stöd för TLS från rutan, men Azure cache för Redis gör. Om du ansluter till Azure cache för Redis och klienten stöder TLS, t. ex. StackExchange. Redis, ska du använda TLS. Som standard är icke-TLS-port inaktive rad för ny Azure-cache för Redis-instanser. Se till att de säkra standardvärdena inte ändras om det inte finns något beroende av TLS-stöd för Redis-klienter. |

Observera att Redis har utformats för att få åtkomst till betrodda klienter i betrodda miljöer. Det innebär vanligt vis att det inte är en bra idé att exponera Redis-instansen direkt till Internet eller, i allmänhet, till en miljö där obetrodda klienter kan få direkt åtkomst till Redis TCP-port eller UNIX-socket. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Skydda enhet till fält Gateway-kommunikation

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | För IP-baserade enheter kan kommunikations protokollet normalt kapslas in i en SSL/TLS-kanal för att skydda data under överföringen. För andra protokoll som inte stöder SSL/TLS undersöker du om det finns säkra versioner av protokollet som tillhandahåller säkerhet på transport-eller meddelande skiktet. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Skydda enhet till Cloud Gateway-kommunikation med SSL/TLS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Välj kommunikations protokoll](../../iot-hub/iot-hub-devguide.md) |
| **Steg** | Skydda HTTP/AMQP-eller MQTT-protokoll med SSL/TLS. |