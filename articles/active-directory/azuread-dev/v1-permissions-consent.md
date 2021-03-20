---
title: Behörigheter i Azure Active Directory | Microsoft docs
description: Lär dig mer om behörigheter i Azure Active Directory och hur du använder dem.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 2b85115d905cb6a7eb7c6aed64a4834425d2f1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366402"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Behörigheter och medgivande i Azure Active Directory v 1.0-slutpunkten

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) använder sig av behörigheter för både OAuth-flöden och OIDC-flöden (OpenID Connect). När appen tar emot en åtkomsttoken från Azure AD innehåller åtkomsttoken anspråk som beskriver de behörigheter som appen har för en viss resurs.

*Behörigheter*, som även kallas *omfång*, gör auktoriseringen enkla för resursen eftersom resursen bara behöver kontrol lera att token innehåller rätt behörighet för det API som appen anropar.

## <a name="types-of-permissions"></a>Typer av behörigheter

Azure AD definierar två typer av behörigheter:

* **Delegerade behörigheter** – Används av appar som har en inloggad användare. För dessa appar godkänner antingen användaren eller administratören de behörigheter som appen begär, och appen delegeras behörighet att agera som den inloggade användaren när ett API anropas. Beroende på API: t kanske användaren inte kan samtycka till API: et direkt och behöver i stället [en administratör för att tillhandahålla "administrativt medgivande"](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Programbehörigheter** – Används av appar som körs utan någon inloggad användare, t.ex, appar som körs som en bakgrundstjänst eller daemon. Program behörigheter kan bara [skickas till av administratörer](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) eftersom de vanligt vis är kraftfulla och ger åtkomst till data över användar gränser eller data som annars skulle vara begränsade till administratörer. Användare som definieras som ägare av resurs programmet (t. ex. API: et som publicerar behörigheterna) tillåts också att ge program behörigheter för de API: er som de äger.

Gällande behörigheter är de behörigheter som din app har vid begäranden till en API. 

* För delegerade behörigheter blir de behörigheter som gäller för din app lägsta möjliga skärningspunkt för de delegerade behörigheter som appen har beviljats (via godkännande) och de behörigheter som den för tillfället inloggade användaren har. Din app kan aldrig ha fler behörigheter än den inloggade användaren. Inom organisationer kan behörigheter för den inloggade användaren fastställas med en princip eller av medlemskap i en eller flera administratörsroller. Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../roles/permissions-reference.md).
    Anta exempelvis att din app har beviljats den delegerade behörigheten `User.ReadWrite.All` i Microsoft Graph. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör, kommer din app att kunna uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte har någon administratörsroll, kommer appen endast kunna uppdatera profilen för den inloggade användaren. Den kommer inte att kunna uppdatera profilerna för andra användare i organisationen, eftersom den användare som den har behörighet att agera på uppdrag åt inte har den behörigheten.
* För programbehörigheter är de gällande behörigheterna för din app den fullständiga behörighetsnivå som behörigheten ger. En app som exempelvis har programbehörigheten `User.ReadWrite.All` kan uppdatera profilen för alla användare i organisationen.

## <a name="permission-attributes"></a>Behörighetsattribut
Behörigheter i Azure AD har ett antal egenskaper som hjälper användare, administratörer eller apputvecklare att fatta välgrundade beslut om vad behörigheten ger åtkomst till.

> [!NOTE]
> Du kan se de behörigheter som ett Azure AD-program eller ett huvudnamn för tjänsten har med Azure Portal eller PowerShell. Testa det här skriptet för att se de behörigheter som görs tillgängliga av Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Egenskapsnamn | Beskrivning | Exempel |
| --- | --- | --- |
| `ID` | Ett GUID-värde som ger en unik identifiering av behörigheten. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Anger om den här behörigheten är tillgänglig för användning. | true |
| `Type` | Anger om den här behörigheten kräver ett godkännande av användaren eller administratören. | User |
| `AdminConsentDescription` | En beskrivning som visas för administratörer vid administratörsmedgivanden | Tillåter att appen läser e-post i användarnas postlådor. |
| `AdminConsentDisplayName` | Ett eget namn som visas för administratörer vid administratörsmedgivanden. | Läsa användarnas e-post |
| `UserConsentDescription` | En beskrivning som visas för användarna vid ett användargodkännande. |  Tillåter att appen läser e-post i din postlåda. |
| `UserConsentDisplayName` | Ett eget namn som visas för användare vid ett användargodkännande. | Läsa din e-post |
| `Value` | Den sträng som används för att identifiera behörigheten vid OAuth 2.0-auktorisering av flöden. `Value` kan även kombineras med app-ID:ts URI-sträng för att bilda ett fullständigt behörighetsnamn. | `Mail.Read` |

## <a name="types-of-consent"></a>Typer av godkännande

Program i Azure AD förlitar sig på godkännanden för att få åtkomst till resurser eller API:er som krävs. Det finns ett antal typer av godkännanden som din app kan behöva ha för att fungera. Om du definierar behörigheter behöver du också känna till hur användarna får tillgång till din app eller API.

* **Statiskt användargodkännande** – Sker automatiskt under [OAuth 2.0-auktoriseringsflödet](v1-protocols-oauth-code.md#request-an-authorization-code) när du anger den resurs som din app vill interagera med. För ett statiskt användargodkännande måste din app redan ha angett alla behörigheter som behövs i appens konfiguration i Azure Portal. Om användaren (eller administratören efter behov) inte har gett sitt godkännande för den här appen, kommer Azure AD be användaren om detta nu. 

    Läs mer om hur du registrerar en Azure AD-app som begär åtkomst till en statisk uppsättning API:er.
* **Dynamiskt användargodkännande** – Är en funktion i v2 Azure AD-appmodellen. I det här scenariot begär appen en uppsättning behörigheter som krävs i [OAuth 2.0-auktoriseringen av flödet för v2-appar](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent). Om användarna inte har godkänt detta, uppmanas de att godkänna nu. [Mer information om dynamiskt godkännande](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Ett dynamiskt godkännande kan vara praktiskt, men det är en utmaning för behörigheter som kräver administratörsmedgivande eftersom man inte känner till dessa behörigheter vid godkännandet. Om du behöver administratörs privilegier eller om din app använder dynamiskt medgivande måste du registrera alla behörigheter i Azure Portal (inte bara den delmängd behörigheter som kräver administratörs medgivande). Detta gör det möjligt för klient administratörer att godkänna för alla användares räkning.
  
* **Administratörsmedgivande** – Krävs när din app behöver ha åtkomst till vissa högpriviligierade behörigheter. Administratörsmedgivande säkerställer att administratörer har vissa ytterligare kontroller innan de godkänner appar, eller att användare får åtkomst till högprivilegierade data från organisationen. [Mer information om hur du ger ett administratörsmedgivande](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Bästa praxis

### <a name="client-best-practices"></a>Metodtips för klienter

- Begär endast behörigheter som din app behöver. Appar med för många behörigheter riskerar att exponera användardata om de komprometteras.
- Välj mellan delegerade behörigheter och programbehörigheter som baseras på det scenario som din app stödjer.
    - Använd alltid delegerade behörigheter om anropet görs för en användares räkning.
    - Använd bara programbehörigheter om appen är icke-interaktiv och inte gör anrop för en viss användares räkning. Programbehörigheter är högprivilegierade och bör endast användas när det är absolut nödvändigt.
- Vid användning av en app som baseras på v2.0-slutpunkten ska du alltid ställa in de statiska behörigheterna (de som angetts i programregistreringen) till att vara supermängden till de dynamiska behörigheter som du begär vid körning (de som anges i koden och skickas som frågeparametrar i din auktoriseringsbegäran) så att scenarier som administratörsmedgivande fungerar korrekt.

### <a name="resourceapi-best-practices"></a>Metodtips för resurs/API

- Resurser som gör API:er tillgängliga bör definiera behörigheter som är specifika för de data eller de åtgärder som de skyddar. Genom att följa det här metodtipset säkerställer du att klienterna inte får behörighet att komma åt data som de inte behöver och att användarna är väl informerade om vilka data de godkänner.
- Resurserna ska tydligt definiera behörigheterna `Read` och `ReadWrite` separat.
- Resurserna ska markera alla behörigheter som ger åtkomst till data över användarens gränser som `Admin`-behörigheter.
- Resurser bör följa namngivningsmönstret `Subject.Permission[.Modifier]`, där:
  - `Subject` motsvarar den typ av data som är tillgänglig
  - `Permission` motsvarar åtgärden som en användare kan utföra på dessa data
  - `Modifier` används för att beskriva specialiseringar för en annan behörighet
    
    Exempel:
  - Mail.Read – Tillåter att användarna läser e-post.
  - Mail.ReadWrite – Tillåter att användarna läser eller skriver e-post.
  - Mail.ReadWrite.All – Ger en administratör eller användare åtkomst till all e-post i organisationen.