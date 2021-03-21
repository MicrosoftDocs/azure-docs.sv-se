---
title: Microsoft Identity Platform-konton & klient profiler på Android | Azure
description: En översikt över Microsoft Identity Platform-konton för Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755967"
---
# <a name="accounts--tenant-profiles-android"></a>Konton och klientorganisationsprofiler (Android)

Den här artikeln innehåller en översikt över vad som `account` finns i Microsoft Identity Platform.

API: t för Microsoft Authentication Library (MSAL) ersätter termen *användare* med termen *konto*. En orsak är att en användare (mänsklig eller program varu agent) kan ha eller kan använda flera konton. Dessa konton kan finnas i användarens egen organisation och/eller i andra organisationer som användaren är medlem i.

Ett konto i Microsoft Identity Platform består av:

- En unik identifierare.  
- En eller flera autentiseringsuppgifter som används för att demonstrera ägarskapet/kontrollen av kontot.
- En eller flera profiler som består av attribut som:
  - Bild, tilldelat namn, familje namn, titel, kontors plats
- Ett konto har en källa till en myndighet eller en post i systemet. Det här är systemet där kontot skapas och var de autentiseringsuppgifter som är kopplade till kontot lagras. I datorer med flera innehavare, till exempel Microsoft Identity Platform, är systemet av Record `tenant` var kontot skapades. Den här klienten kallas även `home tenant` .
- Konton i Microsoft Identity Platform har följande post system:
  - Azure Active Directory, inklusive Azure Active Directory B2C.
  - Microsoft-konto (Live).
- Konton från system för post utanför Microsofts identitets plattform visas i Microsoft Identity Platform, inklusive:
  - identiteter från anslutna lokala kataloger (Windows Server Active Directory)
  - externa identiteter från LinkedIn, GitHub och så vidare.
  I dessa fall har ett konto både ett ursprungs system för post och ett postsystem i Microsoft Identity Platform.
- Med Microsoft Identity Platform kan ett konto användas för att få åtkomst till resurser som tillhör flera organisationer (Azure Active Directory klienter).
  - För att kunna registrera att ett konto från ett post system (AAD-klient A) har åtkomst till en resurs i ett annat postsystem (AAD klient B) måste kontot representeras i klient organisationen där resursen definieras. Detta görs genom att skapa en lokal post för kontot från system A i system B.
  - Den här lokala posten, som är representationen av kontot, är kopplad till det ursprungliga kontot.
  - MSAL exponerar den här lokala posten som en `Tenant Profile` .
  - Klient profilen kan ha olika attribut som är lämpliga för den lokala kontexten, till exempel befattning, kontor, kontakt information osv.
- Eftersom ett konto kan finnas i en eller flera klienter kan ett konto ha fler än en profil.

> [!NOTE]
> MSAL behandlar Microsoft-konto systemet (Live, MSA) som en annan klient i Microsoft Identity Platform. Klient-ID: t för den Microsoft-konto klienten är: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram över konto översikt

![Diagram över konto översikt](./media/accounts-overview/accounts-overview.svg)

I diagrammet ovan:

- Kontot `bob@contoso.com` skapas i den lokala Windows Server-Active Directory (ursprungligt lokalt system för Record).
- Kontot `tom@live.com` skapas i Microsoft-konto klient organisationen.
- `bob@contoso.com` har åtkomst till minst en resurs i följande Azure Active Directory klienter:
  - contoso.com (moln system för Record-länkad till lokalt system för Record)
  - fabrikam.com
  - woodgrovebank.com
  - En klient profil för `bob@contoso.com` finns i var och en av dessa klienter.
- `tom@live.com` har åtkomst till resurser i följande Microsoft-klienter:
  - contoso.com
  - fabrikam.com
  - En klient profil för `tom@live.com` finns i var och en av dessa klienter.
- Information om Tom och Bob i andra klient organisationer kan skilja sig från den i posten i systemet. De kan variera beroende på attribut som befattning, kontors plats och så vidare. De kan vara medlemmar i grupper och/eller roller inom varje organisation (Azure Active Directory klient). Vi refererar till den här informationen som bob@contoso.com klient organisations profil.

I diagrammet bob@contoso.com och tom@live.com har åtkomst till resurser i olika Azure Active Directory klienter. Mer information finns i [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md).

## <a name="accounts-and-single-sign-on-sso"></a>Konton och enkel inloggning (SSO)

MSAL token cache lagrar en *enskild uppdateringstoken* per konto. Uppdaterings-token kan användas för att tyst begära åtkomsttoken från flera klient organisationer för Microsoft Identity Platform. När en Broker installeras på en enhet hanteras kontot av Service Broker och enkel inloggning med hela enheten blir möjligt.

> [!IMPORTANT]
> Verksamhets-till-konsument-kontot (B2C) och Refresh-token skiljer sig från resten av Microsoft Identity Platform. Mer information finns i [B2C-principer &-konton](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Konto identifierare

MSAL-kontots ID är inte ett konto objekt-ID. Den är inte avsedd att parsas och/eller förlita sig på att förmedla något annat än unikhet i Microsoft Identity Platform.

För att vara kompatibel med Azure AD Authentication Library (ADAL) och för att under lätta migrering från ADAL till MSAL kan MSAL söka efter konton med hjälp av en giltig identifierare för det konto som är tillgängligt i MSAL cache.  Följande kan till exempel alltid hämta samma konto objekt för tom@live.com eftersom varje identifierare är giltig:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Åtkomst till anspråk om ett konto

Förutom att begära en åtkomsttoken begär MSAL även alltid en ID-token från varje klient. Detta sker genom att alltid begära följande omfång:

- OpenID
- profil

ID-token innehåller en lista över anspråk. `Claims` är namn/värde-par för kontot och används för att utföra begäran.

Som tidigare nämnts kan varje klient där ett konto finns lagra annan information om kontot, inklusive men inte begränsat till attribut som: befattning, kontors plats och så vidare.

Även om ett konto kan vara medlem eller gäst i flera organisationer, frågar MSAL inte efter en tjänst för att hämta en lista över de klienter som kontot är medlem i. I stället skapar MSAL en lista över klienter som kontot finns i, till följd av token-begäranden som har gjorts.

De anspråk som exponeras för kontot är alltid anspråk från/{Authority} "hem klient" för ett konto. Om kontot inte har använts för att begära en token för sin hem klient kan MSAL inte tillhandahålla anspråk via objektet konto.  Exempel:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Om du vill se en lista över anspråk som är tillgängliga från objektet konto, referera till [anspråk i ett id_token](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> Om du vill lägga till ytterligare anspråk i id_token kan du läsa mer i dokumentationen för valfria anspråk i [How to: tillhandahålla valfria anspråk till din Azure AD-App](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Åtkomst till klient profil anspråk

För att få åtkomst till anspråk om ett konto som de visas i andra klienter måste du först omvandla ditt konto objekt till `IMultiTenantAccount` . Alla konton kan vara flera klienter, men antalet klient profiler som är tillgängliga via MSAL baseras på vilka innehavare du har begärt token från att använda det aktuella kontot.  Exempel:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-principer & konton

Uppdaterade token för ett konto delas inte mellan B2C-principer. Det går därför inte att använda enkel inloggning med token. Detta innebär inte att enkel inloggning inte är möjlig. Det innebär att enkel inloggning måste använda en interaktiv upplevelse där en cookie är tillgänglig för att aktivera enkel inloggning.

Det innebär också att om du hämtar token med olika B2C-principer, innebär det också att de behandlas som separata konton, var och en med sin egen identifierare. Om du vill använda ett konto för att begära en token med `acquireTokenSilent` måste du välja kontot i listan över konton som matchar den princip som du använder med Tokenbegäran. Exempel:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```