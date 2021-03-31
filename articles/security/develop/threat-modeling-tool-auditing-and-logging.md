---
title: Granskning och loggning – Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Lär dig mer om gransknings-och loggnings minskning i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
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
ms.openlocfilehash: 9d3f3ca7b5d4516c2ad5dc9cb19a2eaed0a8a4a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518285"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Säkerhets ram: granskning och loggning | Åtgärder 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifiera känsliga entiteter i din lösning och implementera ändrings granskning](#sensitive-entities)</li></ul> |
| **Webb program** | <ul><li>[Kontrol lera att granskning och loggning tillämpas på programmet](#auditing)</li><li>[Se till att log rotation och separation är på plats](#log-rotation)</li><li>[Kontrol lera att programmet inte loggar känsliga användar data](#log-sensitive-data)</li><li>[Se till att gransknings-och loggfilerna har begränsad åtkomst](#log-restricted-access)</li><li>[Se till att användar hanterings händelser loggas](#user-management)</li><li>[Se till att systemet har inbyggda skydd mot missbruk](#inbuilt-defenses)</li><li>[Aktivera diagnostikloggning för Web Apps i Azure App Service](#diagnostics-logging)</li></ul> |
| **Databas** | <ul><li>[Kontrol lera att inloggnings granskning har Aktiver ATS på SQL Server](#identify-sensitive-entities)</li><li>[Aktivera hot identifiering i Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Använd Azure-lagringsanalys för att granska åtkomst till Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementera tillräcklig loggning](#sufficient-logging)</li><li>[Implementera tillräcklig hantering av gransknings problem](#audit-failure-handling)</li></ul> |
| **Webb-API** | <ul><li>[Se till att granskning och loggning tillämpas på webb-API](#logging-web-api)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Se till att lämplig granskning och loggning tillämpas på Field Gateway](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att lämplig granskning och loggning tillämpas på Cloud Gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identifiera känsliga entiteter i din lösning och implementera ändrings granskning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | Identifiera entiteter i din lösning som innehåller känsliga data och implementera ändrings granskning på dessa entiteter och fält |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Kontrol lera att granskning och loggning tillämpas på programmet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | Aktivera granskning och loggning på alla komponenter. Gransknings loggar ska avbilda användar kontext. Identifiera alla viktiga händelser och logga dessa händelser. Implementera centraliserad loggning |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Se till att log rotation och separation är på plats

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Log rotation är en automatiserad process som används vid system administration i vilken daterade loggfiler arkiveras. Servrar som kör stora program loggar ofta varje begäran: i slutet av Mass loggar är logg rotation ett sätt att begränsa den totala storleken på loggarna samtidigt som du tillåter analys av de senaste händelserna. </p><p>Logg separering innebär i princip att du måste lagra dina loggfiler på en annan partition än när ditt operativ system/program körs för att avstyra en denial of service-attack eller nedgradering av ditt programs prestanda</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Kontrol lera att programmet inte loggar känsliga användar data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontrol lera att du inte loggar känsliga data som en användare skickar till din webbplats. Sök efter avsiktlig loggning och sido effekter som orsakas av design problem. Exempel på känsliga data är:</p><ul><li>Användarautentiseringsuppgifter</li><li>Person nummer eller annan identifierings information</li><li>Kreditkorts nummer eller annan finansiell information</li><li>Hälso information</li><li>Privata nycklar eller andra data som kan användas för att dekryptera krypterad information</li><li>System-eller program information som kan användas för att på ett effektivare sätt attackera programmet</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Se till att gransknings-och loggfilerna har begränsad åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontrol lera att åtkomst behörigheterna till loggfilerna har angetts korrekt. Program konton bör ha skrivskyddad åtkomst och operatörer och support personal bör ha skrivskyddad åtkomst vid behov.</p><p>Administratörs konton är de enda konton som bör ha fullständig åtkomst. Kontrol lera Windows ACL för loggfiler för att säkerställa att de är korrekt begränsade:</p><ul><li>Program konton ska ha skrivskyddad åtkomst</li><li>Operatörer och support personal bör ha skrivskyddad åtkomst vid behov</li><li>Administratörer är de enda konton som ska ha fullständig åtkomst</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Se till att användar hanterings händelser loggas

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontrol lera att programmet övervakar användar hanterings händelser, till exempel lyckade och misslyckade användar inloggningar, återställning av lösen ord, lösen ords ändringar, konto utelåsning, användar registrering. Detta hjälper till att identifiera och reagera på potentiellt misstänkt beteende. Det gör också att du kan samla in drift data. till exempel för att spåra vem som har åtkomst till programmet</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Se till att systemet har inbyggda skydd mot missbruk

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontroller bör finnas på plats som ger upphov till säkerhets undantag i händelse av missbruk av program. T. ex., om verifiering av autentisering är på plats och en angripare försöker mata in skadlig kod som inte matchar regex, kan ett säkerhets undantag genereras, vilket kan vara en indikation på system missbruk</p><p>Vi rekommenderar till exempel att du har loggat över säkerhets undantag och åtgärder som vidtas för följande problem:</p><ul><li>Indatavalidering</li><li>CSRF-överträdelser</li><li>Brute Force (övre gräns för antalet begär Anden per användare per resurs)</li><li>Fil överförings fel</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Aktivera diagnostikloggning för Web Apps i Azure App Service

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Azure har inbyggd diagnostik som hjälper till med fel sökning av en App Service-webbapp. Den gäller även för API-appar och mobilappar. App Service Web Apps innehåller diagnostiska funktioner för loggning av information från både webb servern och webb programmet.</p><p>Dessa delas logiskt upp i Web Server Diagnostics och Application Diagnostics</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Kontrol lera att inloggnings granskning har Aktiver ATS på SQL Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Konfigurera inloggningsgranskning](/sql/ssms/configure-login-auditing-sql-server-management-studio) |
| **Steg** | <p>Granskning av databas Server inloggning måste vara aktiverat för att kunna identifiera/bekräfta angrepp vid lösen ords gissning. Det är viktigt att fånga misslyckade inloggnings försök. Att samla både lyckade och misslyckade inloggnings försök ger ytterligare förmån under kriminal tekniska-undersökningar</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Aktivera hot identifiering i Azure SQL

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure |
| **Attribut**              | SQL-version – V12 |
| **Referenser**              | [Kom igång med SQL Database hot identifiering](../../azure-sql/database/threat-detection-configure.md)|
| **Steg** |<p>Hot identifiering identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen. Det ger ett nytt säkerhets lager som gör det möjligt för kunder att upptäcka och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter.</p><p>Användare kan utforska misstänkta händelser med Azure SQL Database granskning för att avgöra om de kommer från ett försök att komma åt, avslöja eller utnyttja data i databasen.</p><p>Hot identifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhets expert eller hantera avancerade säkerhets övervaknings system</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Använd Azure-lagringsanalys för att granska åtkomst till Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [Använda Lagringsanalys för att övervaka Authorization-typ](../../storage/blobs/security-recommendations.md#loggingmonitoring) |
| **Steg** | <p>För varje lagrings konto kan det vara möjligt för Azure-lagringsanalys att utföra loggnings-och lagrings statistik data. Lagrings analys loggarna innehåller viktig information, till exempel autentiseringsmetoden som används av någon vid åtkomst till lagring.</p><p>Detta kan vara användbart om du nära skyddar åtkomsten till lagringen. I Blob Storage kan du till exempel ange att alla behållare ska vara privata och implementera användningen av en SAS-tjänst i alla program. Sedan kan du kontrol lera loggarna regelbundet för att se om dina blobbar nås med hjälp av lagrings konto nycklarna, vilket kan tyda på en säkerhets överträdelse eller om Blobbarna är offentliga men inte bör vara det.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementera tillräcklig loggning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Steg** | <p>Bristen på ett korrekt gransknings spår efter en säkerhets incident kan försvåra kriminal tekniska-åtgärder. Windows Communication Foundation (WCF) ger möjlighet att logga lyckade och/eller misslyckade autentiseringsförsök.</p><p>Loggning av misslyckade autentiseringsförsök kan varna administratörer för potentiella angrepp av Brute Force. På samma sätt kan loggning av lyckade autentiseringsfel ge en bra Gransknings logg när ett legitimt konto komprometteras. Aktivera WCF-funktionen för säkerhets granskning av tjänster |

### <a name="example"></a>Exempel
Följande är ett exempel på en konfiguration med granskning aktiverat
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementera tillräcklig hantering av gransknings problem

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Steg** | <p>Den utvecklade lösningen har kon figurer ATS för att inte generera ett undantag när det inte går att skriva till en Gransknings logg. Om WCF har kon figurer ATS för att inte utlösa ett undantag när det inte går att skriva till en Gransknings logg, kommer programmet inte att meddelas om felet och granskning av kritiska säkerhets händelser kanske inte inträffar.</p>|

### <a name="example"></a>Exempel
`<behavior/>`Elementet i WCF-konfigurationsfilen nedan instruerar WCF att inte meddela programmet när WCF inte kan skriva till en Gransknings logg.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Konfigurera WCF för att meddela programmet när det inte går att skriva till en Gransknings logg. Programmet bör ha ett alternativt meddelande schema för att varna den organisation som gransknings historiken inte bevaras. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Se till att granskning och loggning tillämpas på webb-API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Aktivera granskning och inloggning på webb-API: er. Gransknings loggar ska avbilda användar kontext. Identifiera alla viktiga händelser och logga dessa händelser. Implementera centraliserad loggning |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Se till att lämplig granskning och loggning tillämpas på Field Gateway

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>När flera enheter ansluter till en fält-Gateway kontrollerar du att anslutnings försöken och autentiseringen (lyckades eller misslyckades) för enskilda enheter loggas och underhålls på fält-gatewayen.</p><p>I de fall där Field Gateway upprätthåller IoT Hub autentiseringsuppgifter för enskilda enheter bör du kontrol lera att granskningen utförs när autentiseringsuppgifterna hämtas. Utveckla en process för att regelbundet överföra loggarna till Azure IoT Hub/Storage för långsiktig kvarhållning.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Se till att lämplig granskning och loggning tillämpas på Cloud Gateway

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Introduktion till IoT Hub åtgärds övervakning](../../iot-hub/iot-hub-operations-monitoring.md) |
| **Steg** | <p>Design för att samla in och lagra gransknings data som samlas in via IoT Hub Operations Monitoring. Aktivera följande övervaknings kategorier:</p><ul><li>Enhets identitets åtgärder</li><li>Kommunikation från enhet till moln</li><li>Kommunikation från moln till enhet</li><li>Anslutningar</li><li>Fil överföringar</li></ul>|