---
title: Villkorlig åtkomst för B2B-samarbets användare – Azure AD
description: Lär dig hur du tillämpar Multi-Factor Authentication-principer för Azure Active Directory B2B-användare.
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
ms.openlocfilehash: 42b3c3d4d474c61cbe472b4122ac2f80f218bf8d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797275"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Villkorlig åtkomst för B2B-samarbets användare

Den här artikeln beskriver hur organisationer kan omfånget av principer för villkorlig åtkomst (CA) för B2B-gäst användare för att få åtkomst till sina resurser.
>[!NOTE]
>Detta autentiserings-eller auktoriseringsfel är lite annorlunda för gäst användare än för befintliga användare av identitets leverantören (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Autentiseringsschema för B2B-gäst användare från en extern katalog

Följande diagram illustrerar flödet: ![ bilden visar autentiseringsscheman för B2B-gäst användare från en extern katalog](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Steg | Description |
|--------------|-----------------------|
| 1. | B2B-gäst användaren begär åtkomst till en resurs. Resursen omdirigerar användaren till resurs klienten, en betrodd IdP.|
| 2. | Resurs klienten identifierar användaren som extern och omdirigerar användaren till B2B-gäst användarens IdP. Användaren utför primär autentisering i IdP.
| 3. | B2B-gäst användarens IdP utfärdar en token till användaren. Användaren omdirigeras tillbaka till resurs klienten med token. Resurs klienten verifierar token och utvärderar sedan användaren mot sina CA-principer. Resurs klienten kan till exempel kräva att användaren utför Azure Active Directory (AD) Multi-Factor Authentication.
| 4. | När alla CA-principer för resurs klienten är uppfyllda utfärdar resurs klienten sin egen token och omdirigerar användaren till resursen.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Autentiseringsschema för B2B-gäst användare med ett lösen ord

Följande diagram illustrerar flödet: ![ bilden visar autentiseringsscheman för B2B-gäst användare med ett lösen ord](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Steg | Description |
|--------------|-----------------------|
| 1. |Användaren begär åtkomst till en resurs i en annan klient organisation. Resursen omdirigerar användaren till resurs klienten, en betrodd IdP.|
| 2. | Resurs klienten identifierar användaren som en [extern e-postanvändare för eng ång slö sen ord (eng ång slö sen ord)](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) och skickar ett e-postmeddelande med eng ång slö sen ord till användaren.|
| 3. | Användaren hämtar eng ång slö sen ord och skickar koden. Resurs klienten utvärderar användaren mot sina CA-principer.
| 4. | När alla CA-principer är uppfyllda utfärdar resurs klienten en token och omdirigerar användaren till resursen. |

>[!NOTE]
>Om användaren kommer från en extern resurs klient organisation är det inte möjligt för B2B-gäst användarens IdP CA-principer att utvärderas. Från och med idag gäller endast resurs innehavarens certifikat utfärdare för dess gäster.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD-Multi-Factor Authentication för B2B-användare

Organisationer kan använda flera Azure AD Multi-Factor Authentication-principer för sina B2B-gäst användare. Dessa principer kan tillämpas på klient-, app-eller individuell användar nivå på samma sätt som de är aktiverade för heltids anställda och medlemmar i organisationen.
Resurs klienten är alltid ansvarig för Azure AD Multi-Factor Authentication för användare, även om gäst användarens organisation har Multi-Factor Authentication funktioner. Här är ett exempel –

1. En administratörs-eller informations anställd i ett företag som heter Fabrikam bjuder in användare från ett annat företag som heter Contoso för att använda sitt program.

2. Sparbanken i Fabrikam har kon figurer ATS för att kräva Azure AD-Multi-Factor Authentication för åtkomst.

3. När B2B-gäst användaren från contoso försöker komma åt Sparbanken i Fabrikam-klienten uppmanas de att slutföra Azure AD Multi-Factor Authentication-utmaningen.

4. Gäst användaren kan sedan konfigurera Azure AD-Multi-Factor Authentication med Fabrikam och välja alternativen.

5. Det här scenariot fungerar för alla identiteter – Azure AD eller personal Microsoft-konto (MSA). Om användaren i Contoso exempelvis autentiserar sig med sociala-ID.

6. Fabrikam måste ha tillräckliga Premium Azure AD-licenser som stöder Azure AD Multi-Factor Authentication. Användaren från Contoso använder sedan den här licensen från fabrikam. Information om B2B-licensiering finns i [fakturerings modell för externa Azure AD-identiteter](https://docs.microsoft.com/azure/active-directory/external-identities/external-identities-pricing) .

>[!NOTE]
>Azure AD Multi-Factor Authentication görs på resurs innehavaren för att säkerställa förutsägbarhet.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Konfigurera Azure AD-Multi-Factor Authentication för B2B-användare

Titta på den här videon om du vill konfigurera Azure AD Multi-Factor Authentication för B2B-samarbets användare:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-användare Azure AD Multi-Factor Authentication för erbjudandet om inlösen

Titta på den här videon om du vill veta mer om Azure AD Multi-Factor Authentication inlösen-upplevelsen:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Azure AD Multi-Factor Authentication återställning för B2B-användare

Nu är följande PowerShell-cmdletar tillgängliga för att korrekturläsa B2B-gäst användare:

1. Anslut till Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Hämta alla användare med korrektur metoder

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Här är ett exempel:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Återställ Azure AD-Multi-Factor Authentication-metoden för en särskild användare för att kräva att B2B-samarbets användaren ställer in några språk metoder igen. 
   Här är ett exempel:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Villkorlig åtkomst för B2B-användare

Det finns olika faktorer som påverkar CA-principer för B2B-gäst användare.

### <a name="device-based-conditional-access"></a>Enhetsbaserad villkorlig åtkomst

I CA finns ett alternativ för att kräva att en användares [enhet är kompatibel eller hybrid Azure AD-ansluten](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#device-state-preview). B2B-gäst användare kan bara uppfylla efterlevnad om resurs klienten kan hantera sin enhet. Enheter kan inte hanteras av mer än en organisation i taget. B2B-gäst användare kan inte uppfylla hybrid Azure AD-anslutning eftersom de inte har något lokalt AD-konto. Endast om gäst användarens enhet är ohanterad kan de registrera eller registrera sina enheter i resurs klienten och sedan göra enheten kompatibel. Användaren kan sedan uppfylla kontrollen bevilja behörighet.

>[!Note]
>Vi rekommenderar inte att du kräver en hanterad enhet för externa användare.

### <a name="mobile-application-management-policies"></a>Principer för hantering av mobilprogram

Certifikat utfärdaren tilldelar kontroller, till exempel **Kräv godkända klient program** och kräver att enhets **skydds principer** måste registreras i klienten. De här kontrollerna kan endast tillämpas på [iOS-och Android-enheter](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#device-platforms). Ingen av dessa kontroller kan dock tillämpas på B2B-gäst användare om användarens enhet redan hanteras av en annan organisation. Det går inte att registrera en mobil enhet i mer än en klient i taget. Om den mobila enheten hanteras av en annan organisation kommer användaren att blockeras. Endast om gäst användarens enhet är ohanterad kan de registrera sina enheter i resurs klienten. Användaren kan sedan uppfylla kontrollen bevilja behörighet.  

>[!NOTE]
>Vi rekommenderar inte att du kräver en app Protection-princip för externa användare.

### <a name="location-based-conditional-access"></a>Plats-baserad villkorlig åtkomst

Den [platsbaserade principen](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#locations) som baseras på IP-intervall kan tillämpas om den bjudande organisationen kan skapa ett betrott IP-adressintervall som definierar deras partner organisationer.

Principer kan också tillämpas baserat på **geografiska platser**.

### <a name="risk-based-conditional-access"></a>Riskbaserad villkorlig åtkomst

[Principen för inloggnings risker](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#sign-in-risk) tillämpas om B2B-gäst användaren uppfyller tilldelnings kontrollen. En organisation kan till exempel kräva Azure AD Multi-Factor Authentication för medelhög eller hög inloggnings risk. Om en användare inte tidigare har registrerats för Azure AD Multi-Factor Authentication i resurs klienten kommer användaren att blockeras. Detta görs för att förhindra att obehöriga användare registrerar sina egna Azure AD Multi-Factor Authentication-autentiseringsuppgifter i händelse av att de äventyrar en legitim användares lösen ord.

Det går dock inte att lösa [användar risk principen](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#user-risk) i resurs klienten. Om du till exempel behöver en lösen ords ändring för gäst användare med hög risk blockeras de på grund av möjligheten att återställa lösen ord i resurs katalogen.

### <a name="conditional-access-client-apps-condition"></a>Villkor för klient program för villkorlig åtkomst

[Klient program villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps) fungerar likadant för B2B-gäst användare som de gör för andra typer av användare. Du kan till exempel förhindra att gäst användare använder bakåtkompatibla autentiseringsprotokoll.

### <a name="conditional-access-session-controls"></a>Kontroller för villkorlig åtkomst-session

[Sessionsnycklar](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) fungerar likadant för B2B-gäst användare som de gör för andra typer av användare.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b)
- [Identitetsskydd och B2B-användare](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-b2b)
- [Prissättning för Externa identiteter](https://azure.microsoft.com/pricing/details/active-directory/)
- [Vanliga frågor och svar](https://docs.microsoft.com/azure/active-directory/external-identities/faq)

