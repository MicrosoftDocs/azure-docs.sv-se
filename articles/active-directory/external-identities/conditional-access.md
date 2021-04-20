---
title: Villkorlig åtkomst för B2B-samarbetsanvändare – Azure AD
description: Lär dig hur du tillämpar principer för multifaktorautentisering för Azure Active Directory B2B-användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739556"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för B2B-samarbetsanvändare

Den här artikeln beskriver hur organisationer kan begränsa principer för villkorsstyrd åtkomst (CA) för B2B-gästanvändare så att de kan komma åt sina resurser.
>[!NOTE]
>Det här autentiserings- eller auktoriseringsflödet är lite annorlunda för gästanvändare än för de befintliga användarna av den identitetsprovidern (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Autentiseringsflöde för B2B-gästanvändare från en extern katalog

Följande diagram illustrerar flödet: bilden visar ![ autentiseringsflödet för B2B-gästanvändare från en extern katalog](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Steg | Description |
|--------------|-----------------------|
| 1. | B2B-gästanvändaren begär åtkomst till en resurs. Resursen omdirigerar användaren till dess resursklientorganisation, en betrodd IdP.|
| 2. | Resursklientorganisationen identifierar användaren som extern och omdirigerar användaren till B2B-gästanvändarens IdP. Användaren utför primär autentisering i IdP:n.
| 3. | B2B-gästanvändarens IdP utfärdar en token till användaren. Användaren omdirigeras tillbaka till resursklientorganisationen med token. Resursklientorganisationen verifierar token och utvärderar sedan användaren mot dess CA-principer. Resursklientorganisationen kan till exempel kräva att användaren utför Azure Active Directory (AD) Multi-Factor Authentication.
| 4. | När alla principer för resursklientorganisations-CA är uppfyllda utfärdar resursklientorganisationen sin egen token och omdirigerar användaren till dess resurs.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Autentiseringsflöde för B2B-gästanvändare med ett lösenord

Följande diagram illustrerar flödet: bilden visar ![ autentiseringsflödet för B2B-gästanvändare med ett lösenord](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Steg | Description |
|--------------|-----------------------|
| 1. |Användaren begär åtkomst till en resurs i en annan klientorganisation. Resursen omdirigerar användaren till dess resursklientorganisation, en betrodd IdP.|
| 2. | Resursklientorganisationen identifierar användaren som en extern e-postanvändare med engångslösenord [(OTP)](./one-time-passcode.md) och skickar ett e-postmeddelande med engångslösenordet till användaren.|
| 3. | Användaren hämtar engångslösenordet och skickar koden. Resursklientorganisationen utvärderar användaren mot dess CA-principer.
| 4. | När alla PRINCIPER för certifikatutfärdaren har uppfyllts utfärdar resursklienten en token och omdirigerar användaren till dess resurs. |

>[!NOTE]
>Om användaren kommer från en extern resursklientorganisation är det inte möjligt att även B2B-gästanvändarens IdP CA-principer utvärderas. Från och med idag gäller endast resursklientorganisationens CA-principer för dess gäster.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD Multi-Factor Authentication för B2B-användare

Organisationer kan tillämpa flera Azure AD Multi-Factor Authentication-principer för sina B2B-gästanvändare. Dessa principer kan tillämpas på klient-, app- eller enskild användarnivå på samma sätt som de aktiveras för heltidsanställda och medlemmar i organisationen.
Resursklientorganisationen ansvarar alltid för Azure AD Multi-Factor Authentication för användare, även om gästanvändarens organisation har multifaktorautentiseringsfunktioner. Här är ett exempel:

1. En administratör eller informationsarbetare på ett företag som heter Fabrikam bjuder in användare från ett annat företag som heter Contoso att använda sitt program Woodgrove.

2. Woodgrove-appen i Fabrikam är konfigurerad för att kräva Azure AD Multi-Factor Authentication vid åtkomst.

3. När B2B-gästanvändaren från Contoso försöker komma åt Woodgrove i Fabrikam-klientorganisationen uppmanas de att slutföra Azure AD Multi-Factor Authentication-utmaningen.

4. Gästanvändaren kan sedan konfigurera sin Azure AD Multi-Factor Authentication med Fabrikam och välja alternativen.

5. Det här scenariot fungerar för alla identiteter – Azure AD eller personligt Microsoft-konto (MSA). Till exempel om användaren i Contoso autentiseras med hjälp av ett socialt ID.

6. Fabrikam måste ha tillräckligt med Premium Azure AD-licenser som stöder Azure AD Multi-Factor Authentication. Användaren från Contoso använder sedan den här licensen från Fabrikam. Se [faktureringsmodellen för externa Azure AD-identiteter](./external-identities-pricing.md) för information om B2B-licensieringen.

>[!NOTE]
>Azure AD Multi-Factor Authentication görs vid resurstilldelningen för att säkerställa förutsägbarhet. När gästanvändaren loggar in visas inloggningssidan för resursklientorganisationen i bakgrunden och en egen inloggningssida för startklientorganisationen och företagslogotypen i förgrunden.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Konfigurera Azure AD Multi-Factor Authentication för B2B-användare

Om du vill konfigurera Azure AD Multi-Factor Authentication för B2B-samarbetsanvändare kan du titta på den här videon:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-användare Azure AD Multi-Factor Authentication för inlösning av erbjudande

Om du vill veta mer om inlösningsupplevelsen för Azure AD Multi-Factor Authentication kan du titta på den här videon:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Återställning av Azure AD Multi-Factor Authentication för B2B-användare

Nu är följande PowerShell-cmdlets tillgängliga för att bevisa B2B-gästanvändare:

1. Anslut till Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Hämta alla användare med proof up-metoder

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Här är ett exempel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Återställ Azure AD Multi-Factor Authentication-metoden för en specifik användare så att B2B-samarbetsanvändaren måste konfigurera korrekturmetoder igen. 
   Här är ett exempel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Villkorlig åtkomst för B2B-användare

Det finns olika faktorer som påverkar CA-principer för B2B-gästanvändare.

### <a name="device-based-conditional-access"></a>Enhetsbaserad villkorlig åtkomst

I CERTIFIKATUTFÄRDARE finns det ett alternativ för att kräva att en användares enhet är [kompatibel eller Hybrid Azure AD-ansluten.](../conditional-access/concept-conditional-access-conditions.md#device-state-preview) B2B-gästanvändare kan bara uppfylla efterlevnadskrav om resursklientorganisationen kan hantera sin enhet. Enheter kan inte hanteras av mer än en organisation i taget. B2B-gästanvändare kan inte uppfylla Hybrid Azure AD-anslutning eftersom de inte har något lokalt AD-konto. Endast om gästanvändarens enhet är ohanterad kan användaren registrera eller registrera sin enhet i resursklientorganisationen och sedan göra enheten kompatibel. Användaren kan sedan uppfylla beviljandekontrollen.

>[!Note]
>Vi rekommenderar inte att du kräver en hanterad enhet för externa användare.

### <a name="mobile-application-management-policies"></a>Principer för hantering av mobilprogram

Ca-beviljandekontroller som **Kräv godkända klientappar** **och Kräv appskyddsprinciper** kräver att enheten registreras i klientorganisationen. Dessa kontroller kan endast tillämpas på [iOS- och Android-enheter.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) Ingen av dessa kontroller kan dock tillämpas på B2B-gästanvändare om användarens enhet redan hanteras av en annan organisation. En mobil enhet kan inte registreras i mer än en klientorganisation åt gången. Om den mobila enheten hanteras av en annan organisation blockeras användaren. Endast om gästanvändarens enhet är ohanterad kan användaren registrera sin enhet i resursklientorganisationen. Användaren kan sedan uppfylla beviljandekontrollen.  

>[!NOTE]
>Vi rekommenderar inte att du kräver en appskyddsprincip för externa användare.

### <a name="location-based-conditional-access"></a>Platsbaserad villkorlig åtkomst

Den platsbaserade principen som baseras på IP-intervall kan tillämpas om den inbjudande organisationen kan skapa ett [betrott IP-adressintervall](../conditional-access/concept-conditional-access-conditions.md#locations) som definierar deras partnerorganisationer.

Principer kan också tillämpas baserat på **geografiska platser**.

### <a name="risk-based-conditional-access"></a>Riskbaserad villkorlig åtkomst

Principen [för inloggningsrisk](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) tillämpas om B2B-gästanvändaren uppfyller beviljandekontrollen. En organisation kan till exempel kräva Azure AD Multi-Factor Authentication för medelhög eller hög inloggningsrisk. Men om en användare inte tidigare har registrerats för Azure AD Multi-Factor Authentication i resursklientorganisationen blockeras användaren. Detta görs för att förhindra att obehöriga användare registrerar sina egna autentiseringsuppgifter för Azure AD Multi-Factor Authentication om de skulle kompromettera en legitim användares lösenord.

Principen [för användarrisk kan](../conditional-access/concept-conditional-access-conditions.md#user-risk) dock inte lösas i resursklientorganisationen. Om du till exempel kräver en lösenordsändring för gästanvändare med hög risk blockeras de på grund av att det inte går att återställa lösenord i resurskatalogen.

### <a name="conditional-access-client-apps-condition"></a>Villkor för klientappar för villkorsstyrd åtkomst

[Klientappvillkor](../conditional-access/concept-conditional-access-conditions.md#client-apps) fungerar på samma sätt för B2B-gästanvändare som för andra typer av användare. Du kan till exempel förhindra att gästanvändare använder äldre autentiseringsprotokoll.

### <a name="conditional-access-session-controls"></a>Sessionskontroller för villkorsstyrd åtkomst

[Sessionskontroller](../conditional-access/concept-conditional-access-session.md) fungerar på samma sätt för B2B-gästanvändare som för andra typer av användare.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](./what-is-b2b.md)
- [Identitetsskydd och B2B-användare](../identity-protection/concept-identity-protection-b2b.md)
- [Prissättning för Externa identiteter](https://azure.microsoft.com/pricing/details/active-directory/)
- [Vanliga frågor och svar](./faq.md)
