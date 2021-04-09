---
title: Autentisering – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Lär dig mer om att undvika autentisering i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94832538"
---
# <a name="security-frame-authentication--mitigations"></a>Säkerhets ram: autentisering | Åtgärder

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webb program**    | <ul><li>[Överväg att använda en standardmekanism för autentisering för att autentisera till webb program](#standard-authn-web-app)</li><li>[Program måste hantera misslyckade verifierings scenarier på ett säkert sätt](#handle-failed-authn)</li><li>[Aktivera steg-till-eller anpassad autentisering](#step-up-adaptive-authn)</li><li>[Se till att de administrativa gränssnitten är korrekt låsta](#admin-interface-lockdown)</li><li>[Implementera glömt lösen ords funktioner på ett säkert sätt](#forgot-pword-fxn)</li><li>[Kontrol lera att lösen ordet och konto principen implementeras](#pword-account-policy)</li><li>[Implementera kontroller för att förhindra uppräkning av användar namn](#controls-username-enum)</li></ul> |
| **Databas** | <ul><li>[Använd Windows-autentisering när det är möjligt för att ansluta till SQL Server](#win-authn-sql)</li><li>[När det är möjligt använder Azure Active Directory autentisering för att ansluta till SQL Database](#aad-authn-sql)</li><li>[När SQL-autentiseringsläge används, se till att konto-och lösen ords principen tillämpas på SQL Server](#authn-account-pword)</li><li>[Använd inte SQL-autentisering i inneslutna databaser](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använd autentiseringsuppgifter för autentisering per enhet med SaS-token](#authn-sas-tokens)</li></ul> |
| **Azure Trust-gränser** | <ul><li>[Aktivera Azure AD Multi-Factor Authentication för Azure-administratörer](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric förtroende gränser** | <ul><li>[Begränsa anonym åtkomst till Service Fabric kluster](#anon-access-cluster)</li><li>[Se till att Service Fabric klient-till-nod-certifikat skiljer sig från nod-till-nod-certifikat](#fabric-cn-nn)</li><li>[Använd AAD för att autentisera klienter för Service Fabric-kluster](#aad-client-fabric)</li><li>[Se till att Service Fabric-certifikat hämtas från en godkänd certifikat utfärdare (CA)](#fabric-cert-ca)</li></ul> |
| **Identitets Server** | <ul><li>[Använd standard scenarier för autentisering som stöds av identitets servern](#standard-authn-id)</li><li>[Åsidosätt standardvärdet för Identity Server-token med ett skalbart alternativ](#override-token)</li></ul> |
| **Datorns förtroende gränser** | <ul><li>[Kontrol lera att de distribuerade programmets binärfiler är digitalt signerade](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Aktivera autentisering vid anslutning till MSMQ-köer i WCF](#msmq-queues)</li><li>[WCF – Ställ inte in meddelande-clientCredentialType på none](#message-none)</li><li>[WCF – Ställ inte in transport-clientCredentialType till none](#transport-none)</li></ul> |
| **Webb-API** | <ul><li>[Se till att standard teknikerna för autentisering används för att säkra webb-API: er](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Använd standard scenarier för autentisering som stöds av Azure Active Directory](#authn-aad)</li><li>[Åsidosätt standardvärdet för ADAL token med ett skalbart alternativ](#adal-scalable)</li><li>[Se till att TokenReplayCache används för att förhindra uppspelning av ADAL-autentiseringstoken](#tokenreplaycache-adal)</li><li>[Använd ADAL-bibliotek för att hantera Tokenbegäran från OAuth2-klienter till AAD (eller lokalt AD)](#adal-oauth2)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Autentisera enheter som ansluter till fält-gatewayen](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att enheter som ansluter till en moln-Gateway autentiseras](#authn-devices-cloud)</li><li>[Använd autentiseringsuppgifter för autentisering per enhet](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att endast nödvändiga behållare och blobbar får anonym Läs behörighet](#req-containers-anon)</li><li>[Bevilja begränsad åtkomst till objekt i Azure Storage med SAS eller SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Överväg att använda en standardmekanism för autentisering för att autentisera till webb program

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligt vis via autentiseringsuppgifter, till exempel användar namn och lösen ord. Det finns flera tillgängliga autentiseringsprotokoll som kan beaktas. En del av dem visas nedan:</p><ul><li>Klient certifikat</li><li>Windows-baserad</li><li>Formulär som baseras</li><li>Federation-ADFS</li><li>Federation – Azure AD</li><li>Federations identitets Server</li></ul><p>Överväg att använda en standardmekanism för autentisering för att identifiera käll processen</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Program måste hantera misslyckade verifierings scenarier på ett säkert sätt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Program som uttryckligen autentiserar användare måste hantera misslyckade verifierings scenarier på ett säkert sätt. Autentiseringsmekanismen måste:</p><ul><li>Neka åtkomst till privilegierade resurser när autentiseringen Miss lyckas</li><li>Visa ett allmänt fel meddelande efter misslyckad autentisering och åtkomst nekas</li></ul><p>Testa för:</p><ul><li>Skydd av privilegierade resurser efter misslyckade inloggningar</li><li>Ett allmänt fel meddelande visas vid misslyckad autentisering och åtkomst nekade händelse (er)</li><li>Konton har inaktiverats efter ett alltför stort antal misslyckade försök</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Aktivera steg-till-eller anpassad autentisering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Kontrol lera att programmet har ytterligare behörighet (till exempel Stega eller adaptiv autentisering) via Multi-Factor Authentication, till exempel att skicka eng ång slö sen ord i SMS, e-post eller fråga om omautentisering, så att användaren får åtkomst till känslig information. Den här regeln gäller även för att göra kritiska ändringar i ett konto eller en åtgärd</p><p>Det innebär också att anpassningen av autentisering måste implementeras på ett sådant sätt att programmet korrekt tillämpar Sammanhangs beroende behörighet, så att otillåten modifiering tillåts inte i exempel av parameter manipulation</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Se till att de administrativa gränssnitten är korrekt låsta

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | Den första lösningen är att endast bevilja åtkomst från ett visst käll-IP-adressintervall till det administrativa gränssnittet. Om lösningen inte skulle vara möjlig än rekommenderar vi alltid att genomdriva en stegvis eller anpassningsbar autentisering för inloggning i det administrativa gränssnittet |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Implementera glömt lösen ords funktioner på ett säkert sätt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Det första är att kontrol lera att glömt lösen ord och andra återställnings Sök vägar skickar en länk som innehåller en tidsbegränsad aktiveringsbegäran i stället för själva lösen ordet. Ytterligare autentisering baserat på mjuka token (t. ex. SMS-token, inbyggda mobila program osv.) kan också krävas innan länken skickas över. För det andra bör du inte låsa användarens konto medan processen för att få ett nytt lösen ord pågår.</p><p>Detta kan leda till en denial of service-attack när en angripare bestämmer sig för att avsiktligt låsa ut användare med en automatisk attack. När den nya förfrågningen för begäran har ställts in, bör meddelandet du visar generaliseras för att förhindra uppräkning av användar namn. Den fjärde tillåter alltid att gamla lösen ord används och implementerar en princip för starka lösen ord.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Kontrol lera att lösen ordet och konto principen implementeras

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Lösen ord och konto principer i enlighet med organisations principen och bästa praxis bör implementeras.</p><p>För att skydda mot brute-force och Dictionary-baserad gissning: principen för starka lösen ord måste implementeras för att säkerställa att användarna skapar ett komplext lösen ord (t. ex. 12 tecken, minimilängd, alfanumeriska tecken och specialtecken).</p><p>Principer för konto utelåsning kan implementeras på följande sätt:</p><ul><li>**Mjukt lås ut:** Detta kan vara ett användbart alternativ för att skydda dina användare mot brute force-attacker. När användaren till exempel anger fel lösen ord tre gånger kan programmet låsa kontot för en minut för att sakta ned processen att tvinga sitt lösen ord att göra det mindre lönsamt för angriparen att gå vidare. Om du skulle implementera hårda motåtgärder för att låsa upp i det här exemplet skulle du få en "DoS" genom att permanent låsa ut konton. Alternativt kan programmet generera ett eng ång slö sen ord (ett lösen ord) och skicka det out-of-band (via e-post, SMS osv.) till användaren. En annan metod kan vara att implementera CAPTCHA när ett tröskel antal misslyckade försök har nåtts.</li><li>**Hård låsning:** Den här typen av utelåsning bör tillämpas när du identifierar en användare som angriper ditt program och räknar dem genom att permanent låsa upp sitt konto tills ett svars team har tid att göra sina data utredning. Efter den här processen kan du välja att ge användaren tillbaka sitt konto eller vidta ytterligare juridiska åtgärder mot dem. Den här typen av metod förhindrar att angriparen ytterligare tränger in ditt program och din infrastruktur.</li></ul><p>Kontrol lera att alla nycklar och lösen ord är utbytbara och att de genereras eller ersätts efter installations tiden för att skydda mot attacker som är standard och förutsägbara konton.</p><p>Om programmet måste generera lösen ord automatiskt måste du se till att de genererade lösen orden är slumpmässiga och har hög entropi.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementera kontroller för att förhindra uppräkning av användar namn

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla fel meddelanden bör generaliseras för att förhindra uppräkning av användar namn. Ibland kan du ibland undvika att information läcker i funktioner som t. ex. en registrerings sida. Här behöver du använda Rate-Limiting-metoder som CAPTCHA för att förhindra ett automatiskt angrepp av en angripare. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Använd Windows-autentisering när det är möjligt för att ansluta till SQL Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | OnPrem |
| **Attribut**              | SQL-version – alla |
| **Referenser**              | [SQL Server-Välj ett autentiseringsläge](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Steg** | Windows-autentisering använder Kerberos-säkerhetsprotokoll, tillhandahåller lösen ords princip tillämpning med avseende på komplexitets verifiering för starka lösen ord, ger stöd för konto utelåsning och har stöd för lösen ordets giltighets tid.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>När det är möjligt använder Azure Active Directory autentisering för att ansluta till SQL Database

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | SQL Azure |
| **Attribut**              | SQL-version – V12 |
| **Referenser**              | [Ansluta till SQL Database med Azure Active Directory autentisering](../../azure-sql/database/authentication-aad-overview.md) |
| **Steg** | **Lägsta version:** Azure SQL Database V12 krävs för att Azure SQL Database ska kunna använda AAD-autentisering mot Microsoft-katalogen |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>När SQL-autentiseringsläge används, se till att konto-och lösen ords principen tillämpas på SQL Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL Server lösen ords princip](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Steg** | När du använder SQL Server autentisering skapas inloggningar i SQL Server som inte baseras på Windows-användarkonton. Både användar namnet och lösen ordet skapas med hjälp av SQL Server och lagras i SQL Server. SQL Server kan använda mekanismer för lösen ords principer i Windows. Den kan använda samma komplexitets-och förfallo principer som används i Windows för lösen ord som används i SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Använd inte SQL-autentisering i inneslutna databaser

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | OnPrem, SQL Azure |
| **Attribut**              | SQL-version – MSSQL2012, SQL-version – V12 |
| **Referenser**              | [Rekommenderade säkerhets metoder med inneslutna databaser](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Steg** | Avsaknad av en Tvingad lösen ords princip kan öka sannolikheten för att en svag autentiseringsuppgift upprättas i en innesluten databas. Utnyttja Windows-autentisering. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Använd autentiseringsuppgifter för autentisering per enhet med SaS-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över Event Hubs autentisering och säkerhets modell](../../event-hubs/authenticate-shared-access-signature.md) |
| **Steg** | <p>Event Hubs säkerhets modellen baseras på en kombination av SAS-token (signatur för delad åtkomst) och händelse utgivare. Utgivar namnet representerar den DeviceID som tar emot token. På så sätt kan du associera de token som genereras med respektive enheter.</p><p>Alla meddelanden är taggade med upphovs man på tjänst sidan som gör det möjligt att identifiera inbetalnings försök med ursprung i nytto Last. När du autentiserar enheter genererar du en SaS-token per enhet som är begränsad till en unik utgivare.</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Aktivera Azure AD Multi-Factor Authentication för Azure-administratörer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Trust-gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Vad är Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Steg** | <p>Multi-Factor Authentication (MFA) är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhets lager för användar inloggningar och transaktioner. Det fungerar genom att kräva två eller flera av följande verifierings metoder:</p><ul><li>Något som du känner till (vanligt vis ett lösen ord)</li><li>Något du har (en betrodd enhet som inte enkelt dupliceras, till exempel en telefon)</li><li>Något som du är (biometrik)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Begränsa anonym åtkomst till Service Fabric kluster

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure  |
| **Referenser**              | [Service Fabric kluster säkerhets scenarier](../../service-fabric/service-fabric-cluster-security.md) |
| **Steg** | <p>Kluster bör alltid skyddas för att förhindra att obehöriga användare ansluter till klustret, särskilt när de har produktions arbets belastningar som körs på den.</p><p>När du skapar ett Service Fabric-kluster ser du till att säkerhets läget är inställt på "säker" och konfigurerar det obligatoriska X. 509-servercertifikatet. Om du skapar ett "osäker" kluster kan alla anonyma användare ansluta till det om det exponerar hanterings slut punkter för det offentliga Internet.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Se till att Service Fabric klient-till-nod-certifikat skiljer sig från nod-till-nod-certifikat

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure, oberoende miljö |
| **Referenser**              | [Service Fabric säkerhet på klient-till-nod-certifikat](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security), [Anslut till ett säkert kluster med klient certifikat](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Steg** | <p>Säkerhet för klient-till-nod-certifikat konfigureras när klustret skapas, antingen via Azure Portal, Resource Manager-mallar eller en fristående JSON-mall genom att ange ett administratörs klient certifikat och/eller ett användar klient certifikat.</p><p>Administratörs klienten och användar klient certifikaten som du anger bör skilja sig från de primära och sekundära certifikat som du anger för nod-till-nod-säkerhet.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Använd AAD för att autentisera klienter för Service Fabric-kluster

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure |
| **Referenser**              | [Kluster säkerhets scenarier – säkerhets rekommendationer](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Steg** | Kluster som körs på Azure kan också skydda åtkomsten till hanterings slut punkter med hjälp av Azure Active Directory (AAD), förutom klient certifikat. För Azure-kluster rekommenderar vi att du använder AAD-säkerhet för att autentisera klienter och certifikat för nod-till-nod-säkerhet.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Se till att Service Fabric-certifikat hämtas från en godkänd certifikat utfärdare (CA)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Miljö – Azure |
| **Referenser**              | [X. 509-certifikat och Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Steg** | <p>Service Fabric använder X. 509-Server certifikat för att autentisera noder och klienter.</p><p>Några viktiga saker att tänka på när du använder certifikat i Service Fabric:</p><ul><li>Certifikat som används i kluster som kör produktions arbets belastningar bör skapas med en korrekt konfigurerad Windows Server-certifikatutfärdare eller hämtas från en godkänd certifikat utfärdare (CA). Certifikat utfärdaren kan vara en godkänd extern certifikat utfärdare eller en korrekt hanterad intern offentlig nyckel infrastruktur (PKI)</li><li>Använd aldrig temporära eller test certifikat i produktion som skapas med verktyg som MakeCert.exe</li><li>Du kan använda ett självsignerat certifikat, men bör bara göra det för test kluster och inte i produktion</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Använd standard scenarier för autentisering som stöds av identitets servern

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3 – stor bild](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Steg** | <p>Nedan visas de vanligaste interaktionerna som stöds av identitets servern:</p><ul><li>Webbläsare kommunicerar med webb program</li><li>Webb program kommunicerar med webb-API: er (ibland egna, ibland för en användares räkning)</li><li>Webbläsarbaserade program kommunicerar med webb-API: er</li><li>Interna program kommunicerar med webb-API: er</li><li>Serverbaserade program kommunicerar med webb-API: er</li><li>Webb-API: er kommunicerar med webb-API: er (ibland egna, ibland för en användares räkning)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Åsidosätt standardvärdet för Identity Server-token med ett skalbart alternativ

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identity Server-distribution-cachelagring](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | <p>IdentityServer har en enkel inbyggd cache i minnet. Även om detta är användbart för små skalade appar, skalar den inte för program på mellan nivå och Server del på grund av följande orsaker:</p><ul><li>De här programmen används av många användare på samma gång. Att spara alla åtkomsttoken i samma Arkiv skapar isolerings problem och presenterar utmaningar när de körs i stor skala: många användare, var och en med så många tokens som de resurser som appen har åtkomst till för deras räkning, kan innebära enorma siffror och mycket dyra söknings åtgärder</li><li>Dessa program distribueras vanligt vis på distribuerade topologier, där flera noder måste ha åtkomst till samma cacheminne</li><li>Cachelagrade token måste överleva process återkallning och inaktive ring</li><li>För alla ovanstående orsaker, medan du implementerar webbappar, rekommenderar vi att du åsidosätter standardvärdet för identitets serverns token-cache med ett skalbart alternativ, till exempel Azure cache för Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Kontrol lera att de distribuerade programmets binärfiler är digitalt signerade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser |
| **SDL-fas**               | Distribution |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att det distribuerade programmets binärfiler är digitalt signerade så att integriteten för binärfilerna kan verifieras|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Aktivera autentisering vid anslutning till MSMQ-köer i WCF

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Steg** | Det gick inte att aktivera autentisering vid anslutning till MSMQ-köer, en angripare kan skicka meddelanden anonymt till kön för bearbetning. Om autentisering inte används för att ansluta till en MSMQ-kö som används för att leverera ett meddelande till ett annat program, kan en angripare skicka ett anonymt meddelande som är skadligt.|

### <a name="example"></a>Exempel
`<netMsmqBinding/>`Elementet i WCF-konfigurationsfilen nedan instruerar WCF att inaktivera autentisering vid anslutning till en MSMQ-kö för meddelande leverans.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurera MSMQ för att kräva Windows-domän eller certifikatautentisering hela tiden för inkommande eller utgående meddelanden.

### <a name="example"></a>Exempel
`<netMsmqBinding/>`Elementet i WCF-konfigurationsfilen nedan instruerar WCF att aktivera certifikatautentisering vid anslutning till en MSMQ-kö. Klienten autentiseras med hjälp av X. 509-certifikat. Klient certifikatet måste finnas i certifikat arkivet på servern.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF – Ställ inte in meddelande-clientCredentialType på none

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Typ av klient behörighet – ingen |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Steg** | Avsaknad av autentisering innebär att alla kan komma åt den här tjänsten. En tjänst som inte autentiserar sina klienter ger åtkomst till alla användare. Konfigurera programmet att autentisera mot klientautentiseringsuppgifter. Detta kan göras genom att ange meddelandets clientCredentialType till Windows eller certifikat. |

### <a name="example"></a>Exempel
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF – Ställ inte in transport-clientCredentialType till none

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmän, .NET Framework 3 |
| **Attribut**              | Typ av klient behörighet – ingen |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Steg** | Avsaknad av autentisering innebär att alla kan komma åt den här tjänsten. En tjänst som inte autentiserar sina klienter ger alla användare åtkomst till sina funktioner. Konfigurera programmet att autentisera mot klientautentiseringsuppgifter. Detta kan göras genom att ange transport clientCredentialType till Windows eller certifikat. |

### <a name="example"></a>Exempel
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Se till att standard teknikerna för autentisering används för att säkra webb-API: er

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Autentisering och auktorisering i ASP.net webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [external Authentication Services med ASP.net Web API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Steg** | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligt vis via autentiseringsuppgifter, till exempel användar namn och lösen ord. Det finns flera tillgängliga autentiseringsprotokoll som kan beaktas. En del av dem visas nedan:</p><ul><li>Klient certifikat</li><li>Windows-baserad</li><li>Formulär som baseras</li><li>Federation-ADFS</li><li>Federation – Azure AD</li><li>Federations identitets Server</li></ul><p>Länkar i avsnittet referenser innehåller information på låg nivå om hur vart och ett av autentiseringsmetoderna kan implementeras för att skydda ett webb-API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Använd standard scenarier för autentisering som stöds av Azure Active Directory

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Autentiserings scenarier för Azure AD](../../active-directory/develop/authentication-vs-authorization.md), [Azure Active Directory kod exempel](../../active-directory/azuread-dev/sample-v1-code.md), [Azure Active Directory Developer ' s guide](../../active-directory/develop/index.yml) |
| **Steg** | <p>Azure Active Directory (Azure AD) fören klar autentiseringen för utvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect. Nedan visas de fem primära program scenarier som stöds av Azure AD:</p><ul><li>Webbläsare till webb program: en användare måste logga in på ett webb program som skyddas av Azure AD</li><li>SPA (Single Page Application): en användare måste logga in på ett enda sid program som skyddas av Azure AD</li><li>Inbyggt program till webb-API: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att få resurser från ett webb-API som skyddas av Azure AD</li><li>Webb program till webb-API: ett webb program måste hämta resurser från ett webb-API som skyddas av Azure AD</li><li>Daemon-eller serverprogram till webb-API: ett daemon-program eller ett serverprogram utan webb användar gränssnitt behöver få resurser från ett webb-API som skyddas av Azure AD</li></ul><p>Se länkarna i avsnittet referenser för implementerings information på låg nivå</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Åsidosätt standardvärdet för ADAL token med ett skalbart alternativ

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webb program](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications), [med hjälp av Redis som ADAL token cache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Steg** | <p>Standardcachen som ADAL (Active Directory-autentiseringsbibliotek) använder är en minnes intern cache som förlitar sig på en statisk lagring, tillgänglig i hela processen. Även om det här fungerar för interna program, skalar det inte för program på mellan nivå och Server delar av följande orsaker:</p><ul><li>De här programmen används av många användare på samma gång. Att spara alla åtkomsttoken i samma Arkiv skapar isolerings problem och presenterar utmaningar när de körs i stor skala: många användare, var och en med så många tokens som de resurser som appen har åtkomst till för deras räkning, kan innebära enorma siffror och mycket dyra söknings åtgärder</li><li>Dessa program distribueras vanligt vis på distribuerade topologier, där flera noder måste ha åtkomst till samma cacheminne</li><li>Cachelagrade token måste överleva process återkallning och inaktive ring</li></ul><p>För alla ovanstående orsaker, medan du implementerar webbappar, rekommenderas att åsidosätta standardvärdet för ADAL token med ett skalbart alternativ, till exempel Azure cache för Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Se till att TokenReplayCache används för att förhindra uppspelning av ADAL-autentiseringstoken

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webb program](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **Steg** | <p>Med egenskapen TokenReplayCache kan utvecklare definiera en cache för cachelagring av token, en lagrings plats som kan användas för att spara tokens för att verifiera att ingen token kan användas mer än en gång.</p><p>Detta är ett mått mot en gemensam attack, aptly anropade attacken för token-uppspelning: en angripare som fångar upp token som skickas vid inloggning kan försöka skicka den till appen igen ("spela upp" den) för att upprätta en ny session. T. ex., i OIDC-kod för kod beviljande, efter lyckad användarautentisering, görs en begäran till "/signin-OIDC"-slut punkten för den förlitande parten med parametrarna "id_token", "Code" och "State".</p><p>Den förlitande parten verifierar denna begäran och upprättar en ny session. Om en angripare fångar denna begäran och spelar upp den, kan han/hon upprätta en lyckad session och falska användare. Förekomsten av nonce i OpenID Connect kan begränsas, men inte helt eliminera de omständigheter som angreppet kan utföras i. För att skydda sina program kan utvecklare tillhandahålla en implementering av ITokenReplayCache och tilldela en instans till TokenReplayCache.</p>|

### <a name="example"></a>Exempel
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Exempel
Här är en exempel implementering av ITokenReplayCache-gränssnittet. (Du måste anpassa och implementera det Project-/regionsspecifika cache-ramverket för cachelagring)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Den implementerade cachen måste refereras till i OIDC alternativ via egenskapen "TokenValidationParameters" på följande sätt.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Observera att du kan testa effektiviteten för den här konfigurationen, logga in i det lokala OIDC-skyddade programmet och avbilda begäran till `"/signin-oidc"` slut punkten i Fiddler. När skyddet inte är på plats kan du spela upp den här begäran i Fiddler för att ange en ny cookie för session. När begäran spelas upp när TokenReplayCache-skyddet har lagts till, kommer programmet att utlösa ett undantag på följande sätt: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Använd ADAL-bibliotek för att hantera Tokenbegäran från OAuth2-klienter till AAD (eller lokalt AD)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Steg** | <p>Azure AD Authentication Library (ADAL) gör det möjligt för utvecklare av klient program att enkelt autentisera användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.</p><p>ADAL har många funktioner som gör autentisering enklare för utvecklare, till exempel asynkron support, ett konfigurerbart token-cache som lagrar åtkomsttoken och uppdateringstoken, automatisk uppdatering av token när en åtkomsttoken upphör att gälla och en uppdateringstoken är tillgänglig.</p><p>Genom att hantera det mesta av komplexiteten kan ADAL hjälpa utvecklare att fokusera på affärs logik i sina program och enkelt skydda resurser utan att vara expert på säkerhet. Separata bibliotek är tillgängliga för .NET, Java Script (klient och Node.js), python, iOS, Android och Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Autentisera enheter som ansluter till fält-gatewayen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att varje enhet autentiseras av fält-gatewayen innan du accepterar data från dem och innan du under lätta kommunikationen med moln-gatewayen. Kontrol lera också att enheterna ansluter med autentiseringsuppgifter per enhet så att enskilda enheter kan identifieras unikt.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Se till att enheter som ansluter till en moln-Gateway autentiseras

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Generisk, C#, Node.JS,  |
| **Attribut**              | Ej tillämpligt, Gateway-val – Azure IoT Hub |
| **Referenser**              | Ej tillämpligt, [Azure IoT Hub med .net](../../iot-hub/quickstart-send-telemetry-dotnet.md), [komma igång med IoT Hub och Node JS](../../iot-hub/quickstart-send-telemetry-node.md), vilket [skyddar IoT med SAS och certifikat](../../iot-hub/iot-hub-devguide-security.md), [git-lagringsplats](https://github.com/Azure/azure-iot-sdks/) |
| **Steg** | <ul><li>**Allmänt:** Autentisera enheten med hjälp av Transport Layer Security (TLS) eller IPSec. Infrastrukturen bör ha stöd för användning av i förväg delad nyckel (PSK) på de enheter som inte kan hantera fullständig asymmetrisk kryptering. Utnyttja Azure AD, OAuth.</li><li>**C#:** När du skapar en DeviceClient-instans skapar metoden Create som standard en DeviceClient-instans som använder AMQP-protokollet för att kommunicera med IoT Hub. Om du vill använda HTTPS-protokollet använder du åsidosättningen av Create-metoden som gör att du kan ange protokollet. Om du använder HTTPS-protokollet bör du också lägga till `Microsoft.AspNet.WebApi.Client` NuGet-paketet i projektet för att inkludera `System.Net.Http.Formatting` namn området.</li></ul>|

### <a name="example"></a>Exempel
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Exempel
**Node.JS: autentisering**
#### <a name="symmetric-key"></a>Symmetrisk nyckel
* Skapa en IoT-hubb på Azure
* Skapa en post i enhets identitets registret
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Skapa en simulerad enhet
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS-token
* Skapas internt när symmetrisk nyckel används men vi kan generera och använda det explicit
* Definiera ett protokoll: `var Http = require('azure-iot-device-http').Http;`
* Skapa en SAS-token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Anslut med SAS-token:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Certifikat
* Generera ett självsignerat X509-certifikat med valfritt verktyg som OpenSSL för att generera ett. cert-och. Key-filer för att lagra certifikatet respektive nyckel
* Etablera en enhet som accepterar säker anslutning med hjälp av certifikat.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Ansluta en enhet med ett certifikat
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Använd autentiseringsuppgifter för autentisering per enhet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway  |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Gateway-val – Azure-IoT Hub |
| **Referenser**              | [Azure IoT Hub säkerhetstoken](../../iot-hub/iot-hub-devguide-security.md) |
| **Steg** | Använd autentiseringsuppgifter för autentisering per enhet med SaS-token baserat på enhets nyckel eller klient certifikat, i stället för principer för delad åtkomst på IoT Hub nivå. Detta förhindrar åter användning av autentiseringstoken för en enhet eller fält-Gateway med en annan |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Se till att endast nödvändiga behållare och blobbar får anonym Läs behörighet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | StorageType-BLOB |
| **Referenser**              | [Hantera anonym Läs åtkomst till behållare och blobbar](../../storage/blobs/anonymous-read-access-configure.md), [signaturer för delad åtkomst, del 1: förstå SAS-modellen](../../storage/common/storage-sas-overview.md) |
| **Steg** | <p>Som standard kan en behållare och alla blobbar i den endast nås av ägare av lagrings kontot. För att ge anonyma användare Läs behörighet till en behållare och dess blobbar, kan en ange behållar behörighet att tillåta offentlig åtkomst. Anonyma användare kan läsa blobbar i en offentligt tillgänglig behållare utan att autentisera begäran.</p><p>Behållare innehåller följande alternativ för att hantera container åtkomst:</p><ul><li>Fullständig offentlig Läs behörighet: behållare och BLOB-data kan läsas via anonym begäran. Klienter kan räkna upp blobar i behållaren via anonym begäran, men kan inte räkna upp behållare i lagrings kontot.</li><li>Offentlig Läs behörighet för blobbar: BLOB-data i den här behållaren kan läsas via anonym begäran, men behållar data är inte tillgängliga. Klienter kan inte räkna upp blobar i behållaren via anonym begäran</li><li>Ingen offentlig Läs behörighet: behållare och BLOB-data kan bara läsas av konto ägaren</li></ul><p>Anonym åtkomst är bäst för scenarier där vissa blobbar alltid ska vara tillgängliga för anonym Läs åtkomst. En bättre och mer detaljerad kontroll är att skapa en signatur för delad åtkomst, som gör att du kan delegera begränsad åtkomst med olika behörigheter och under ett angivet tidsintervall. Se till att behållare och blobbar som potentiellt kan innehålla känsliga data inte ges anonym åtkomst av misstag</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Bevilja begränsad åtkomst till objekt i Azure Storage med SAS eller SAP

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage |
| **SDL-fas**               | Skapa |
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [Signaturer för delad åtkomst, del 1: förstå SAS-modellen](../../storage/common/storage-sas-overview.md), [signaturer för delad åtkomst, del 2: skapa och använda en SAS med Blob Storage](../../storage/common/storage-sas-overview.md), [så här delegerar du åtkomst till objekt i ditt konto med hjälp av signaturer för delad åtkomst och lagrade åtkomst principer](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Steg** | <p>Att använda en signatur för delad åtkomst (SAS) är ett kraftfullt sätt att ge begränsad åtkomst till objekt i ett lagrings konto till andra klienter, utan att behöva exponera konto åtkomst nyckel. SAS är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med SAS måste klienten bara skicka in SAS till lämplig konstruktor eller metod.</p><p>Du kan använda en SAS när du vill ge åtkomst till resurser i ditt lagrings konto till en klient som inte är betrodd med konto nyckeln. Dina lagrings konto nycklar innehåller både en primär och sekundär nyckel, som båda ger administrativ åtkomst till ditt konto och alla resurser i den. Genom att exponera någon av dina konto nycklar öppnas ditt konto för möjligheten att använda skadlig eller försumlig användning. Signaturer för delad åtkomst är ett säkert alternativ som gör det möjligt för andra klienter att läsa, skriva och ta bort data i ditt lagrings konto enligt de behörigheter som du har beviljat, och utan att behöva konto nyckeln.</p><p>Om du har en logisk uppsättning parametrar som liknar varje gång, är det en bättre idé att använda en princip för lagrade åtkomst principer (SAP). Eftersom användning av en SAS som härletts från en lagrad åtkomst princip ger dig möjlighet att återkalla SAS omedelbart, är det rekommenderade bästa praxis att alltid använda lagrade åtkomst principer när det är möjligt.</p>|