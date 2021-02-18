---
title: Auktorisering-Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Läs mer om behörighets minskning i Threat Modeling Tool. Se en lista över potentiella hot och problemlösnings anvisningar.
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
ms.openlocfilehash: 38b147a85a26fd1e0be4f5dc6b63ae4c1331d348
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094007"
---
# <a name="security-frame-authorization--mitigations"></a>Säkerhets ram: auktorisering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Datorns förtroende gränser** | <ul><li>[Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten](#acl-restricted-access)</li><li>[Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen](#sensitive-directory)</li><li>[Kontrol lera att de distribuerade programmen körs med lägsta behörighet](#deployed-privileges)</li></ul> |
| **Webb program** | <ul><li>[Använd sekventiell steg ordning vid bearbetning av affärs logiks flöden](#sequential-logic)</li><li>[Implementera hastighets begränsning för att förhindra uppräkning](#rate-enumeration)</li><li>[Kontrol lera att rätt tillstånd är på plats och principen om lägsta behörigheten följs](#principle-least-privilege)</li><li>[Beslut om affärs logik och resurs åtkomst kan inte baseras på parametrar för inkommande begäran](#logic-request-parameters)</li><li>[Se till att innehållet och resurserna inte är enumerable eller tillgängliga via Tvingad bläddring](#enumerable-browsing)</li></ul> |
| **Databas** | <ul><li>[Se till att konton med lägst privilegier används för att ansluta till databas servern](#privileged-server)</li><li>[Implementera säkerhet på radnivå för att förhindra att klienter får åtkomst till var and ras data](#rls-tenants)</li><li>[Sysadmin-rollen bör bara ha giltiga användare](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ansluta till en molnbaserad Gateway med hjälp av minst privilegierade token](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använd en SAS-nyckel för endast skicka-behörighet för att skapa enhets-token](#sendonly-sas)</li><li>[Använd inte åtkomsttoken som ger direkt åtkomst till Händelsehubben](#access-tokens-hub)</li><li>[Anslut till Händelsehubben med SAS-nycklar som har de lägsta behörigheter som krävs](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Använd Resource tokens för att ansluta till Azure Cosmos DB närhelst det är möjligt](#resource-docdb)</li></ul> |
| **Azure Trust-gränser** | <ul><li>[Aktivera detaljerad åtkomst hantering för Azure-prenumerationen med hjälp av Azure RBAC](#grained-rbac)</li></ul> |
| **Service Fabric förtroende gränser** | <ul><li>[Begränsa klientens åtkomst till kluster åtgärder med hjälp av Azure RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs](#modeling-field)</li></ul> |
| **Dynamics CRM-Portal** | <ul><li>[Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Ge detaljerad behörighet för en serie entiteter i Azure Table Storage](#permission-entities)</li><li>[Aktivera Azure-rollbaserad åtkomst kontroll (Azure RBAC) till Azure Storage-konto med hjälp av Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera implicit upplåsning eller identifiering av rottips](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Svag klass referens i WCF](#weak-class-wcf)</li><li>[WCF – implementera Authorization-kontroll](#wcf-authz)</li></ul> |
| **Webb-API** | <ul><li>[Implementera rätt mekanism för auktorisering i ASP.NET webb-API](#authz-aspnet)</li></ul> |
| **IoT-enhet** | <ul><li>[Utför verifierings kontroller på enheten om den har stöd för olika åtgärder som kräver olika behörighets nivåer](#device-permission)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Utföra verifierings kontroller i fältet Gateway om det stöder olika åtgärder som kräver olika behörighets nivåer](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen. Detta är för att förhindra att flera användare av datorn får åtkomst till var and ras data.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Kontrol lera att de distribuerade programmen körs med lägsta behörighet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrol lera att det distribuerade programmet körs med minst behörighet. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Använd sekventiell steg ordning vid bearbetning av affärs logiks flöden

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | För att verifiera att den här fasen kördes genom av en äkta användare vill du göra det möjligt för programmet att endast bearbeta affärs logik flöden i sekventiell steg ordning, med alla steg som bearbetas på verklighetstrogen mänsklig tid, och inte bearbeta i ordning, hoppa över steg, bearbetade steg från en annan användare eller för snabbt skickade transaktioner.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementera hastighets begränsning för att förhindra uppräkning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att känsliga identifierare är slumpmässiga. Implementera CAPTCHA-kontroll på anonyma sidor. Se till att fel och undantag inte ska avslöja vissa data|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Kontrol lera att rätt tillstånd är på plats och principen om lägsta behörigheten följs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Principen innebär bara att ge ett användar konto behörighet som är nödvändig för att användarna ska fungera. En säkerhets kopierings användare behöver t. ex. inte installera program vara: säkerhets kopierings användaren har därför bara rättigheter att köra säkerhets kopierings-och säkerhets kopierings program. Alla andra behörigheter, till exempel installation av ny program vara, blockeras. Principen gäller också för en personlig dator användare som vanligt vis arbetar i ett normalt användar konto och öppnar ett skyddat, lösenordsskyddat konto (dvs. en superanvändare) endast när situationen absolut kräver det. </p><p>Den här principen kan också tillämpas på dina webb program. I stället för att bara beroende på rollbaserad autentiseringsmetoder med hjälp av sessioner, vill vi istället tilldela behörigheter till användare med hjälp av ett Database-Based-autentiseringspaket. Vi använder fortfarande sessioner för att identifiera om användaren har loggat in på rätt sätt, bara nu i stället för att tilldela den användaren en speciell roll, tilldelar han han med behörighet för att verifiera vilka åtgärder han är behörig att utföra i systemet. Dessutom är det en stor Pro av den här metoden, när en användare måste tilldelas färre behörigheter kommer dina ändringar att tillämpas i farten eftersom tilldelningen inte är beroende av den session som annars var tvungen att upphöra att gälla först.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Beslut om affärs logik och resurs åtkomst kan inte baseras på parametrar för inkommande begäran

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | När du kontrollerar om en användare är begränsad till att granska vissa data, ska åtkomst begränsningarna bearbetas på Server sidan. UserID bör lagras i en sessionsvariabel vid inloggning och ska användas för att hämta användar data från databasen |

### <a name="example"></a>Exempel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu kan en angripare inte manipulera och ändra program åtgärden eftersom identifieraren för att hämta data hanteras på Server sidan.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Se till att innehållet och resurserna inte är enumerable eller tillgängliga via Tvingad bläddring

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Känsliga statiska filer och konfigurationsfiler bör inte behållas i webb roten. För att innehåll inte måste vara offentligt bör du använda lämpliga åtkomst kontroller eller ta bort själva innehållet.</p><p>Dessutom kombineras Tvingad bläddring vanligt vis med brute force-teknik för att samla in information genom att försöka få åtkomst till så många URL: er som möjligt för att räkna upp kataloger och filer på en server. Angripare kan kontrol lera alla variationer av vanliga filer. En lösen ords fils ökning omfattar till exempel filer som psswd.txt, password.htm, Password. dat och andra variationer.</p><p>För att minimera detta bör funktioner för identifiering av Brute Force-försök inkluderas.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Se till att konton med lägst privilegier används för att ansluta till databas servern

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL-behörighet-hierarki](/sql/relational-databases/security/permissions-hierarchy-database-engine), [SQL-skydd bara objekt](/sql/relational-databases/security/securables) |
| **Steg** | Konton med minst privilegier ska användas för att ansluta till databasen. Program inloggningen bör vara begränsad i databasen och bör endast köra valda lagrade procedurer. Programmets inloggning ska inte ha någon direkt tabell åtkomst. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementera säkerhet på radnivå för att förhindra att klienter får åtkomst till var and ras data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version – V12, SQL-version – MsSQL2016 |
| **Referenser**              | [SQL Server Row-Level säkerhet (RLS)](/sql/relational-databases/security/row-level-security) |
| **Steg** | <p>Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext).</p><p>Row-Level säkerhet (RLS) fören klar utformningen och kodningen av säkerhet i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.</p><p>Logiken för åtkomst begränsning finns i databas nivån i stället för bort från data i en annan program nivå. Databas systemet tillämpar åtkomst begränsningar varje gång som data åtkomsten görs från vilken nivå som helst. Detta gör säkerhets systemet mer tillförlitligt och stabilt genom att minska säkerhets systemets Area.</p><p>|

Observera att RLS som en out-of-Box-databas-funktion bara kan användas för att SQL Server första 2016, Azure SQL Database och SQL-hanterad instans. Om RLS-funktionen är inaktive ras, bör den säkerställa att data åtkomsten är begränsad med hjälp av vyer och procedurer

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Sysadmin-rollen bör bara ha giltiga användare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL-behörighet-hierarki](/sql/relational-databases/security/permissions-hierarchy-database-engine), [SQL-skydd bara objekt](/sql/relational-databases/security/securables) |
| **Steg** | Medlemmar i den fasta Server rollen SysAdmin bör vara mycket begränsade och innehåller aldrig konton som används av program.  Kontrol lera listan med användare i rollen och ta bort eventuella onödiga konton|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Ansluta till en molnbaserad Gateway med hjälp av minst privilegierade token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Gateway-val – Azure-IoT Hub |
| **Referenser**              | [IoT Hub-Access Control](../../iot-hub/iot-hub-devguide.md) |
| **Steg** | Ge minst behörighet till olika komponenter som ansluter till en molnbaserad Gateway (IoT Hub). Vanligt exempel är – enhets hantering/etablerings komponent använder registryread/Write, händelse processor (ASA) använder tjänst anslutning. Enskilda enheter ansluter med autentiseringsuppgifter för enhet|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Använd en SAS-nyckel för endast skicka-behörighet för att skapa enhets-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över Event Hubs autentisering och säkerhets modell](../../event-hubs/authenticate-shared-access-signature.md) |
| **Steg** | En SAS-nyckel används för att generera enskilda enhets-token. Använd en SAS-nyckel för endast skicka-behörighet när du genererar enhets-token för en specifik utgivare|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Använd inte åtkomsttoken som ger direkt åtkomst till Händelsehubben

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över Event Hubs autentisering och säkerhets modell](../../event-hubs/authenticate-shared-access-signature.md) |
| **Steg** | En token som beviljar direkt åtkomst till händelsehubben ska inte ges till enheten. Om du använder en minst privilegie rad token för den enhet som ger åtkomst till en utgivare kan det hjälpa att identifiera och neka den om den är en falsk eller komprometterad enhet.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Anslut till Händelsehubben med SAS-nycklar som har de lägsta behörigheter som krävs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över Event Hubs autentisering och säkerhets modell](../../event-hubs/authenticate-shared-access-signature.md) |
| **Steg** | Ge minsta behörighet till olika Server dels program som ansluter till Event Hub. Skapa separata SAS-nycklar för varje server dels program och ange endast de behörigheter som krävs – skicka, ta emot eller hantera dem.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Använd Resource tokens för att ansluta till Cosmos DB närhelst det är möjligt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | En resurs-token är kopplad till en Azure Cosmos DB behörighets resurs och fångar upp relationen mellan användaren av en databas och den behörighet som användaren har för en specifik Azure Cosmos DB program resurs (t. ex. samling, dokument). Använd alltid en Resource-token för att få åtkomst till Azure Cosmos DB om klienten inte är betrodd med hantering av Master-eller skrivskyddade nycklar – som ett program för slutanvändare som en mobil-eller Skriv bords klient. Använd huvud nyckel eller skrivskyddade nycklar från backend-program som kan lagra nycklarna på ett säkert sätt.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-azure-rbac"></a><a id="grained-rbac"></a>Aktivera detaljerad åtkomst hantering för Azure-prenumerationen med hjälp av Azure RBAC

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Trust-gränser | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../../role-based-access-control/role-assignments-portal.md)  |
| **Steg** | Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure. Med Azure RBAC kan du endast bevilja den mängd åtkomst som användarna behöver för att utföra sina jobb.|

## <a name="restrict-clients-access-to-cluster-operations-using-service-fabric-rbac"></a><a id="cluster-rbac"></a>Begränsa klientens åtkomst till kluster åtgärder med Service Fabric RBAC

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure |
| **Referenser**              | [Service Fabric rollbaserad åtkomst kontroll för Service Fabric klienter](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **Steg** | <p>Azure Service Fabric har stöd för två olika åtkomst kontroll typer för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomst kontroll kan kluster administratören begränsa åtkomsten till vissa kluster åtgärder för olika grupper av användare, vilket gör klustret säkrare.</p><p>Administratörer har fullständig åtkomst till hanterings funktioner (inklusive Läs-och skriv funktioner). Användare har som standard endast Läs behörighet till hanterings funktioner (till exempel fråge funktioner) och möjligheten att lösa program och tjänster.</p><p>Du anger de två klient rollerna (administratör och klient) när klustret skapas genom att tillhandahålla separata certifikat för var och en.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM-Portal | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Ge detaljerad behörighet för en serie entiteter i Azure Table Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | StorageType – tabell |
| **Referenser**              | [Så här delegerar du åtkomst till objekt i ditt Azure Storage-konto med hjälp av SAS](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Steg** | I vissa affärs scenarier kan Azure Table Storage krävas för att lagra känsliga data som består till olika parter. T. ex. känsliga data som rör olika länder/regioner. I sådana fall kan SAS-signaturer skapas genom att ange nyckel intervall för partition och rad, så att en användare kan komma åt data som är specifika för ett visst land/en viss region.| 

## <a name="enable-azure-role-based-access-control-azure-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Aktivera Azure-rollbaserad åtkomst kontroll (Azure RBAC) till Azure Storage-konto med hjälp av Azure Resource Manager

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Skydda ditt lagrings konto med rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../storage/blobs/security-recommendations.md) |
| **Steg** | <p>När du skapar ett nytt lagrings konto väljer du en distributions modell av klassisk eller Azure Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter bara all-eller-ingen åtkomst till prenumerationen, och i sin tur är lagrings kontot.</p><p>Med Azure Resource Manager-modellen ska du lagra lagrings kontot i en resurs grupp och kontrol lera åtkomsten till hanterings planet för det angivna lagrings kontot med hjälp av Azure Active Directory. Du kan till exempel ge vissa användare möjlighet att komma åt lagrings konto nycklarna, medan andra användare kan visa information om lagrings kontot, men inte åtkomst till lagrings konto nycklarna.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementera implicit upplåsning eller identifiering av rottips

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Programmet bör skydda sina egna konfigurations-och användar data om telefonen är rotad eller jailbrokad bruten. Rot-/jailbrokad-brytning innebär otillåten åtkomst, vilket normala användare inte gör på sina egna telefoner. Därför bör programmet ha implicit identifierings logik vid program Start, för att identifiera om telefonen har rotats.</p><p>Identifierings logiken kan bara komma åt filer som vanligt vis bara rot användare kan komma åt, till exempel:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Om programmet kan komma åt någon av dessa filer anger det att programmet körs som rot användare.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Svag klass referens i WCF

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Systemet använder en svag klass referens, vilket kan göra det möjligt för en angripare att köra oauktoriserad kod. Programmet refererar till en användardefinierad klass som inte har identifierats unikt. När .NET läser in den svag identifierade klassen söker CLR-typ inläsaren efter klassen på följande platser i angiven ordning:</p><ol><li>Om sammansättningen av typen är känd söker inläsaren igenom konfigurations filens omdirigerings platser, GAC, den aktuella sammansättningen med hjälp av konfigurations information och programmets bas katalog</li><li>Om sammansättningen är okänd söker inläsaren efter den aktuella sammansättningen, mscorlib och den plats som returneras av händelse hanteraren för TypeResolve</li><li>Den här CLR-sökordningen kan ändras med krokar, till exempel mekanismen för typ vidarebefordran och händelsen AppDomain. TypeResolve</li></ol><p>Om en angripare utnyttjar CLR-sökordningen genom att skapa en alternativ klass med samma namn och placera den på en alternativ plats som CLR kommer att läsa in först, kommer CLR att oavsiktligt köra angriparen-kod som anges</p>|

### <a name="example"></a>Exempel
`<behaviorExtensions/>`Elementet i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en anpassad beteende klass i ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Att använda fullständigt kvalificerade (starka) namn identifierar unikt en typ och ökar säkerheten i systemet ytterligare. Använd fullständigt kvalificerade sammansättnings namn när du registrerar typer i machine.config och app.config filer.

### <a name="example"></a>Exempel
`<behaviorExtensions/>`Elementet i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en starkt refererad anpassad beteende klass till ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF – implementera Authorization-kontroll

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Den här tjänsten använder inte en Authorization-kontroll. När en klient anropar en viss WCF-tjänst tillhandahåller WCF olika auktoriserings scheman som kontrollerar att anroparen har behörighet att köra tjänst metoden på servern. Om verifierings kontroller inte är aktiverade för WCF-tjänster kan en autentiserad användare få behörighets eskalering.</p>|

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att inte kontrol lera klientens autentiseringsnivå när tjänsten körs:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Använd ett schema för auktorisering av tjänsten för att kontrol lera att anroparen för tjänst metoden har behörighet att göra det. WCF tillhandahåller två lägen och tillåter definitionen av ett anpassat Authorization-schema. UseWindowsGroups-läget använder Windows-roller och användare och UseAspNetRoles-läget använder en ASP.NET-rollprovider, till exempel SQL Server, för att autentisera.

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att kontrol lera att klienten är en del av gruppen Administratörer innan du kör Lägg till tjänsten:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Tjänsten deklareras sedan som följande:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementera rätt mekanism för auktorisering i ASP.NET webb-API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, MVC5 |
| **Attribut**              | Ej tillämpligt, identitetsprovider-ADFS, identitetsprovider – Azure AD |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Steg** | <p>Roll information för program användarna kan härledas från Azure AD eller ADFS-anspråk om programmet förlitar sig på dem som identitets leverantör eller själva programmet kan tillhandahålla det. I något av dessa fall ska implementeringen av anpassad auktorisering validera användar Rolls informationen.</p><p>Roll information för program användarna kan härledas från Azure AD eller ADFS-anspråk om programmet förlitar sig på dem som identitets leverantör eller själva programmet kan tillhandahålla det. I något av dessa fall ska implementeringen av anpassad auktorisering validera användar Rolls informationen.</p>

### <a name="example"></a>Exempel
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Alla styrenheter och åtgärds metoder som måste skyddas måste anges i ovanstående attribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Utför verifierings kontroller på enheten om den har stöd för olika åtgärder som kräver olika behörighets nivåer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Enheten ska auktorisera anroparen för att kontrol lera om anroparen har de behörigheter som krävs för att utföra den begärda åtgärden. T. ex. kan du säga att enheten är ett smart dörr Lås som kan övervakas från molnet, samt att det ger funktioner som fjärrlåser dörren.</p><p>Smart dörr låset tillhandahåller en enda låsning av funktioner när någon fysiskt kommer nära dörren med ett kort. I det här fallet bör implementeringen av fjärrkommandot och kontrollen göras på ett sådant sätt att den inte tillhandahåller några funktioner för att låsa upp dörren eftersom moln-gatewayen inte har behörighet att skicka ett kommando för att låsa upp dörren.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Utföra verifierings kontroller i fältet Gateway om det stöder olika åtgärder som kräver olika behörighets nivåer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Fält-gatewayen ska auktorisera anroparen för att kontrol lera om anroparen har de behörigheter som krävs för att utföra den begärda åtgärden. Till exempel bör det finnas olika behörigheter för ett administratörs användar gränssnitt/API som används för att konfigurera en fält-Gateway v/s-enheter som ansluter till den.|