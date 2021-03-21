---
title: Känsliga data – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Läs mer om känslig data minskning i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0bcbe35fc6d9f104325bec8a3404ad57a6376cf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518132"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Säkerhets ram: känsliga data | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Datorns förtroende gränser** | <ul><li>[Se till att binärfilerna är fördunklade om de innehåller känslig information](#binaries-info)</li><li>[Överväg att använda krypterat fil system (EFS) används för att skydda konfidentiella användarspecifika data](#efs-user)</li><li>[Se till att känsliga data som lagras av programmet på fil systemet är krypterade](#filesystem)</li></ul> | 
| **Webb program** | <ul><li>[Se till att känsligt innehåll inte cachelagras i webbläsaren](#cache-browser)</li><li>[Kryptera avsnitt i webbappens konfigurationsfiler som innehåller känsliga data](#encrypt-data)</li><li>[Inaktivera explicit HTML-attributet autocomplete i känsliga formulär och indata](#autocomplete-input)</li><li>[Se till att känsliga data som visas på användar skärmen är maskerade](#data-mask)</li></ul> | 
| **Databas** | <ul><li>[Implementera dynamisk data maskning för att begränsa känsliga data exponeringar som inte är privilegierade användare](#dynamic-users)</li><li>[Se till att lösen ord lagras i saltat hash-format](#salted-hash)</li><li>[Se till att känsliga data i databas kolumner är krypterade](#db-encrypted)</li><li>[Se till att kryptering på databas nivå (TDE) är aktiverat](#tde-enabled)</li><li>[Se till att databas säkerhets kopiorna är krypterade](#backup)</li></ul> | 
| **Webb-API** | <ul><li>[Se till att känsliga data som är relevanta för webb-API: n inte lagras i webbläsarens lagring](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Kryptera känsliga data som lagras i Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM Trust-gränser** | <ul><li>[Använd Azure Disk Encryption för att kryptera diskar som används av Virtual Machines](#disk-vm)</li></ul> | 
| **Service Fabric förtroende gränser** | <ul><li>[Kryptera hemligheter i Service Fabric program](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Utför säkerhets modellering och Använd affär Senheter/team där det behövs](#modeling-teams)</li><li>[Minimera åtkomst till delnings funktionen på kritiska entiteter](#entities)</li><li>[Utbilda användare om risker som är kopplade till funktionerna i Dynamics CRM-resursen och de bästa säkerhets metoderna](#good-practices)</li><li>[Ta med en regel för utvecklings standarder som beskriver hur du visar konfigurations information i undantags hanteringen](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Använda Azure Storage Service Encryption (SSE) för vilande data (för hands version)](#sse-preview)</li><li>[Använd Client-Side kryptering för att lagra känsliga data i Azure Storage](#client-storage)</li></ul> | 
| **Mobil klient** | <ul><li>[Kryptera känsliga eller PII-data som skrivits för lokal lagring av telefoner](#pii-phones)</li><li>[Obfuscate skapade binärfiler innan de distribueras till slutanvändare](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ange clientCredentialType till certifikat eller Windows](#cert)</li><li>[WCF-säkerhets läge är inte aktiverat](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Se till att binärfilerna är fördunklade om de innehåller känslig information

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att binärfilerna är fördunklade om de innehåller känslig information, till exempel affärs hemligheter, känslig affärs logik som inte ska ångras. Detta är att stoppa omvända konstruktioner av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Överväg att använda krypterat fil system (EFS) används för att skydda konfidentiella användarspecifika data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Överväg att använda krypterat fil system (EFS) används för att skydda konfidentiella användarspecifik information från angripare med fysisk åtkomst till datorn. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Se till att känsliga data som lagras av programmet på fil systemet är krypterade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att känsliga data som lagras av programmet på fil systemet är krypterade (t. ex. med DPAPI), om EFS inte kan tillämpas |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Se till att känsligt innehåll inte cachelagras i webbläsaren

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generiska, webb formulär, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Webbläsare kan lagra information i syfte att cachelagra och historik. Dessa cachelagrade filer lagras i en mapp, t. ex. mappen Temporary Internet Files, i Internet Explorer. När dessa sidor refereras igen, visar webbläsaren dem från cachen. Om känslig information visas för användaren (till exempel adress, kreditkorts information, person nummer eller användar namn) kan den här informationen lagras i webbläsarens cacheminne och därför kan hämtas genom att undersöka webbläsarens cacheminne eller genom att helt enkelt trycka på webbläsarens "bak"-knapp. Ange värdet "No-Store" för Cache-Control svar för alla sidor. |

### <a name="example"></a>Exempel
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-store" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Exempel
Detta kan implementeras via ett filter. Följande exempel kan användas: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Kryptera avsnitt i webbappens konfigurationsfiler som innehåller känsliga data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Gör så [här: kryptera konfigurations avsnitt i ASP.NET 2,0 med DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Ange en skyddad Konfigurationsprovider](/previous-versions/68ze1hb2(v=vs.140)) [med hjälp av Azure Key Vault för att skydda program hemligheter](/azure/architecture/multitenant-identity/web-api) |
| **Steg** | Konfigurationsfiler som Web.config, appsettings.jspå används ofta för att lagra känslig information, inklusive användar namn, lösen ord, databas anslutnings strängar och krypterings nycklar. Om du inte skyddar den här informationen är ditt program sårbart för angripare eller skadliga användare som erhåller känslig information, till exempel användar namn och lösen ord för kontot, databas namn och Server namn. Med hjälp av distributions typen (Azure/on-lokal) krypterar du de känsliga delarna av config-filerna med DPAPI eller tjänster som Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Inaktivera explicit HTML-attributet autocomplete i känsliga formulär och indata

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN: Autoavsluta-attribut](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [använda Autoavsluta i HTML](/previous-versions/windows/internet-explorer/ie-developer/), [sårbarhet för HTML-säkerhetsspråkning](/security-updates/SecurityBulletins/2010/ms10-071), [Autoavsluta., igen?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Steg** | Attributet Autoavsluta anger om ett formulär ska ha Autoavsluta eller inte. När autocomplete är aktiverat Slutför webbläsaren automatiskt värden baserat på värden som användaren har angett tidigare. Om till exempel ett nytt namn och lösen ord anges i ett formulär och formuläret skickas, frågar webbläsaren om lösen ordet ska sparas. Därefter när formuläret visas fylls namnet och lösen ordet i automatiskt eller slutförs när namnet anges. En angripare med lokal åtkomst kan hämta lösen ordet för klartext från webbläsarens cacheminne. Som standard är automatisk komplettering aktiverat och måste inaktive ras explicit. |

### <a name="example"></a>Exempel
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Se till att känsliga data som visas på användar skärmen är maskerade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Känsliga data, till exempel lösen ord, kreditkorts nummer, SSN osv. bör maskeras när de visas på skärmen. Detta är att förhindra obehöriga personer från att komma åt data (t. ex. axel-och lösen ord, support personal som visar SSN-nummer för användare). Se till att dessa data element inte är synliga i oformaterad text och maskeras på lämpligt sätt. Detta måste vara försiktig när du accepterar dem som inmatade (t. ex.. Indatatyp = "lösen ord") och Visa tillbaka på skärmen (t. ex. visar bara de sista 4 siffrorna i kreditkorts numret). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementera dynamisk data maskning för att begränsa känsliga data exponeringar som inte är privilegierade användare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version – V12, SQL-version – MsSQL2016 |
| **Referenser**              | [Dynamisk datamaskering](/sql/relational-databases/security/dynamic-data-masking) |
| **Steg** | Syftet med dynamisk datamaskering är att begränsa exponeringen av känsliga data, vilket förhindrar användare som inte ska ha åtkomst till data från att visas. Dynamisk data maskning syftar inte till att hindra databas användare från att ansluta direkt till databasen och köra omfattande frågor som visar delar av känsliga data. Dynamisk data maskning kompletterar andra SQL Server säkerhetsfunktioner (granskning, kryptering och säkerhet på radnivå...) och vi rekommenderar att du använder den här funktionen tillsammans med dem för att bättre skydda känsliga data i databasen. Observera att den här funktionen endast stöds av SQL Server som börjar med 2016 och Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Se till att lösen ord lagras i saltat hash-format

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lösen ords hash med .NET-API för kryptering](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Steg** | Lösen ord bör inte lagras i databaserna för anpassade användar arkiv. Lösen ords-hashar ska lagras med salt värden i stället. Se till att saltet för användaren alltid är unikt och att du använder b-crypt, s-Encryption eller PBKDF2 innan du lagrar lösen ordet, med ett minsta antal arbets faktorer i 150 000-slingor för att eliminera risken för brute framtvingande.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Se till att känsliga data i databas kolumner är krypterade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | SQL-version – alla |
| **Referenser**              | [Kryptera känsliga data i SQL Server](/previous-versions/sql/sql-server-2008-r2/ff848751(v=sql.105)), [så här: kryptera en data kolumn i SQL Server](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), [kryptera efter certifikat](/sql/t-sql/functions/encryptbycert-transact-sql) |
| **Steg** | Känsliga data som kreditkorts nummer måste vara krypterade i-databasen. Data kan krypteras med hjälp av kryptering på kolumn nivå eller en program funktion med hjälp av krypterings funktionerna. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Se till att kryptering på databas nivå (TDE) är aktiverat

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Förstå SQL Server transparent datakryptering (TDE)](/previous-versions/sql/sql-server-2008-r2/bb934049(v=sql.105)) |
| **Steg** | Transparent datakryptering-funktionen (TDE) i SQL Server hjälper till att kryptera känsliga data i en databas och skydda nycklar som används för att kryptera data med ett certifikat. Detta förhindrar att någon utan nycklarna använder data. TDE skyddar data i vila, vilket innebär data-och loggfilerna. Det ger möjlighet att följa många lagar, förordningar och rikt linjer som har upprättats i olika branscher. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Se till att databas säkerhets kopiorna är krypterade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version – V12, SQL-version – MsSQL2014 |
| **Referenser**              | [Säkerhets kopierings kryptering i SQL Database](/sql/relational-databases/backup-restore/backup-encryption) |
| **Steg** | SQL Server kan kryptera data när du skapar en säkerhets kopia. Genom att ange krypteringsalgoritmen och Krypteraren (ett certifikat eller asymmetrisk nyckel) när du skapar en säkerhets kopia kan du skapa en krypterad säkerhets kopierings fil. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Se till att känsliga data som är relevanta för webb-API: n inte lagras i webbläsarens lagring

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **Attribut**              | Identitetsprovider-ADFS, identitetsprovider – Azure AD |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>I vissa implementeringar lagras känsliga artefakter som är relevanta för webb-API: s autentisering i webbläsarens lokala lagring. T. ex. Azure AD-autentisering som ADAL. idtoken, ADAL. nonce. idtoken, ADAL. Access. token. Key, ADAL. token. Keys, ADAL. State. Login, ADAL. session. State, ADAL. förfallo datum. nyckel osv.</p><p>Alla dessa artefakter är tillgängliga även efter att utloggning eller webbläsare har stängts. Om en angripare får åtkomst till dessa artefakter kan han/hon återanvända dem för att få åtkomst till de skyddade resurserna (API: erna). Se till att alla känsliga artefakter relaterade till webb-API: n inte lagras i webbläsarens lagrings utrymme. I de fall där lagring på klient sidan inte kan undvikas (t. ex. en enda sida program (SPA) som utnyttjar implicita OpenIdConnect/OAuth-flöden behöver lagra åtkomsttoken lokalt), Använd lagrings alternativ med inte beständighet. till exempel föredra SessionStorage till LocalStorage.</p>| 

### <a name="example"></a>Exempel
JavaScript-kodfragmentet nedan är från ett anpassat autentiseringspaket som lagrar autentiserings artefakter i lokal lagring. Sådana implementeringar bör undvikas. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Kryptera känsliga data som lagras i Cosmos DB

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kryptera känsliga data på program nivå innan du lagrar i dokument databasen eller lagra känsliga data i andra lagrings lösningar som Azure Storage eller Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Använd Azure Disk Encryption för att kryptera diskar som används av Virtual Machines

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure IaaS VM Trust-gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Använda Azure Disk Encryption för att kryptera diskar som används av dina virtuella datorer](../../storage/blobs/security-recommendations.md#data-protection) |
| **Steg** | <p>Azure Disk Encryption är en ny funktion som för närvarande är en för hands version. Med den här funktionen kan du kryptera de OS-diskar och data diskar som används av en virtuell IaaS-dator. För Windows krypteras enheterna med hjälp av teknik för BitLocker-kryptering enligt bransch standard. För Linux krypteras diskarna med DM-Crypt-teknik. Detta är integrerat med Azure Key Vault så att du kan styra och hantera disk krypterings nycklarna. Azure Disk Encryption-lösningen stöder följande tre kund krypterings scenarier:</p><ul><li>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från kundens krypterade VHD-filer och de krypterings nycklar som anges av kunden, som lagras i Azure Key Vault.</li><li>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från Azure Marketplace.</li><li>Aktivera kryptering på befintliga virtuella IaaS-datorer som redan körs i Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Kryptera hemligheter i Service Fabric program

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure |
| **Referenser**              | [Hantera hemligheter i Service Fabric program](../../service-fabric/service-fabric-application-secret-management.md) |
| **Steg** | Hemligheter kan vara vilken känslig information som helst, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text. Använd Azure Key Vault för att hantera nycklar och hemligheter i Service Fabric-program. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Utför säkerhets modellering och Använd affär Senheter/team där det behövs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhets modellering och Använd affär Senheter/team där det behövs |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimera åtkomst till delnings funktionen på kritiska entiteter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Minimera åtkomst till delnings funktionen på kritiska entiteter |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Utbilda användare om risker som är kopplade till funktionerna i Dynamics CRM-resursen och de bästa säkerhets metoderna

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utbilda användare om risker som är kopplade till funktionerna i Dynamics CRM-resursen och de bästa säkerhets metoderna |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Ta med en regel för utvecklings standarder som beskriver hur du visar konfigurations information i undantags hanteringen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Ta med en regel för utvecklings standarder som beskriver hur du visar konfigurations information i undantags hantering utanför utvecklingen. Testa för detta som en del av kod granskning eller periodisk kontroll.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Använda Azure Storage Service Encryption (SSE) för vilande data (för hands version)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | StorageType-BLOB |
| **Referenser**              | [Azure Storage tjänst kryptering för vilande data (för hands version)](../../storage/common/storage-service-encryption.md) |
| **Steg** | <p>Med hjälp av Azure Storage Service Encryption (SSE) för data i vila kan du skydda och skydda dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Kryptering, dekryptering och nyckel hantering är helt transparent för användare. SSE gäller endast för block-blobar, Page blobbar och bifogade blobbar. Andra typer av data, inklusive tabeller, köer och filer, kommer inte att krypteras.</p><p>Arbets flöde för kryptering och dekryptering:</p><ul><li>Kunden aktiverar kryptering på lagrings kontot</li><li>När kunden skriver nya data (Lägg till BLOB, skicka block, skicka sida osv.) till Blob Storage. varje skrivning krypteras med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga</li><li>När kunden behöver åtkomst till data (Hämta BLOB osv.) dekrypteras data automatiskt innan användaren återgår till användaren</li><li>Om kryptering är inaktiverat är nya skrivningar inte längre krypterade och befintliga krypterade data förblir krypterade tills de skrivs om av användaren. När kryptering är aktiverat krypteras skrivning till Blob Storage. Data statusen ändras inte när användaren växlas mellan aktivering/inaktive ring av kryptering för lagrings kontot</li><li>Alla krypterings nycklar lagras, krypteras och hanteras av Microsoft</li></ul><p>Observera att för närvarande hanteras nycklar som används för kryptering av Microsoft. Microsoft genererar nycklarna ursprungligen och hanterar den säkra lagringen av nycklar samt den regelbundna rotationen enligt definitionen i den interna Microsoft-principen. I framtiden får kunderna möjlighet att hantera sina egna >krypterings nycklar och ange en sökväg för migrering från Microsoft-hanterade nycklar till Kundhanterade nycklar.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Använd Client-Side kryptering för att lagra känsliga data i Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md), [Självstudier: kryptera och dekryptera blobbar i Microsoft Azure Storage använda Azure Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md), [lagra data på ett säkert sätt i Azure Blob Storage med Azure Encryption-tillägg](/archive/blogs/partnercatalystteam/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions) |
| **Steg** | <p>Azure Storage klient bibliotek för .NET NuGet-paket stöder kryptering av data i klient program innan överföring till Azure Storage, och dekryptering av data vid hämtning till klienten. Biblioteket stöder även integrering med Azure Key Vault för hantering av nycklar för lagringskonton. Här är en kort beskrivning av hur kryptering på klient sidan fungerar:</p><ul><li>Azure Storage klient-SDK: n genererar en innehålls krypterings nyckel (CEK), som är en symmetrisk nyckel som används i ett tillfälle</li><li>Kund information krypteras med den här CEK</li><li>CEK omsluts sedan (krypteras) med nyckel krypterings nyckeln (KEK). KEK identifieras av en nyckel identifierare och kan vara ett asymmetriskt nyckel par eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Själva lagrings klienten har aldrig åtkomst till KEK. Den anropar bara den nyckel brytnings algoritm som tillhandahålls av Key Vault. Kunder kan välja att använda anpassade providers för nyckel brytningar/avradbyte om de vill</li><li>Krypterade data överförs sedan till Azure Storages tjänsten. Kontrol lera länkarna i avsnittet referenser för implementerings information på låg nivå.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Kryptera känsliga eller PII-data som skrivits för lokal lagring av telefoner

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, Xamarin  |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Hantera inställningar och funktioner på dina enheter med Microsoft Intune principer](/mem/intune/configuration/), [nyckel Ring valet](https://components.xamarin.com/view/square.valet) |
| **Steg** | <p>Om programmet skriver känslig information som användarens PII (e-post, telefonnummer, förnamn, efter namn, preferenser osv.) -på mobilens fil system bör den krypteras innan du skriver till det lokala fil systemet. Om programmet är ett företags program kan du utforska möjligheten att publicera program med hjälp av Windows Intune.</p>|

### <a name="example"></a>Exempel
Intune kan konfigureras med följande säkerhets principer för att skydda känsliga data: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exempel
Om programmet inte är ett företags program använder du plattforms oberoende nyckel lagring, nyckel kedjor för att lagra krypterings nycklar som använder vilken kryptografisk åtgärd som kan utföras i fil systemet. Följande kodfragment visar hur du kommer åt nyckeln från nyckel ringen med Xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Obfuscate skapade binärfiler innan de distribueras till slutanvändare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Krypto-döljande för .net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Steg** | Genererade binärfiler (sammansättningar inom APK) bör vara fördunklade för att stoppa omvända konstruktioner av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Ange clientCredentialType till certifikat eller Windows

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Steg** | Om du använder en UsernameToken med ett lösen ord i klartext över en okrypterad kanal exponeras lösen ordet för angripare som kan avhjälpa SOAP-meddelanden. Tjänst leverantörer som använder UsernameToken kan acceptera lösen ord som skickas i klartext. Om du skickar lösen ord i klartext över en okrypterad kanal kan du exponera autentiseringsuppgifterna för angripare som kan sniffa SOAP-meddelandet. | 

### <a name="example"></a>Exempel
Följande konfiguration för WCF-tjänstprovider använder UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ange clientCredentialType till certifikat eller Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF-säkerhets läge är inte aktiverat

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmän, .NET Framework 3 |
| **Attribut**              | Säkerhets läge – transport, säkerhets läge – meddelande |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [grunderna i WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Steg** | Ingen transport eller meddelande säkerhet har definierats. Program som skickar meddelanden utan transport-eller meddelande säkerhet kan inte garantera integritet eller konfidentialitet för meddelanden. När en WCF-säkerhetsbindning har angetts till ingen, inaktive ras både transport-och meddelande säkerhet. |

### <a name="example"></a>Exempel
Följande konfiguration ställer in säkerhets läget på ingen. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Exempel
Säkerhets läge för alla tjänst bindningar det finns fem möjliga säkerhets lägen: 
* Inga. Inaktiverar säkerhet. 
* Källtransportadr. Använder transport säkerhet för ömsesidig autentisering och meddelande skydd. 
* Meddelande. Använder meddelande säkerhet för ömsesidig autentisering och meddelande skydd. 
* Dubbelrikta. Gör att du kan ange inställningar för säkerhet på transport-och meddelande nivå (endast MSMQ stöder detta). 
* TransportWithMessageCredential. Autentiseringsuppgifterna skickas med meddelandet och meddelande skyddet och serverautentisering tillhandahålls av transport lagret. 
* TransportCredentialOnly. Klientautentiseringsuppgifterna skickas med transport lagret och inget meddelande skydd tillämpas. Använd transport-och meddelande säkerhet för att skydda integriteten och konfidentialiteten hos meddelanden. Konfigurationen nedan instruerar tjänsten att använda transport säkerhet med meddelande uppgifter.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```