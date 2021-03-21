---
title: 'Azure AD Connect: sömlöst enskilt Sign-On – hur det fungerar | Microsoft Docs'
description: Den här artikeln beskriver hur Azure Active Directory sömlösa en Sign-On-funktion fungerar.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369653"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory sömlös enkel inloggning: tekniskt djup

Den här artikeln innehåller teknisk information om hur funktionen Azure Active Directory sömlös enkel Sign-On (sömlös SSO) fungerar.

## <a name="how-does-seamless-sso-work"></a>Hur fungerar sömlös enkel inloggning?

Det här avsnittet innehåller tre delar:

1. Installationen av sömlös SSO-funktion.
2. Hur en enskild användares inloggnings transaktion i en webbläsare fungerar med sömlös SSO.
3. Hur en enskild användares inloggnings transaktion på en intern klient fungerar med sömlös SSO.

### <a name="how-does-set-up-work"></a>Hur fungerar konfiguration?

Sömlös SSO aktive ras med Azure AD Connect som visas [här](how-to-connect-sso-quick-start.md). När funktionen aktive ras inträffar följande steg:

- Ett dator konto ( `AZUREADSSOACC` ) skapas i din lokala Active Directory (AD) i varje AD-skog som du synkroniserar med Azure AD (med Azure AD Connect).
- Dessutom skapas ett antal SPN-namn (Service Principal Name) som ska användas under inloggnings processen i Azure AD.
- Dator kontots Kerberos-dekrypterings nyckel delas på ett säkert sätt med Azure AD. Om det finns flera AD-skogar kommer varje dator konto ha sin egen unika Kerberos-dekrypterings nyckel.

>[!IMPORTANT]
> `AZUREADSSOACC`Dator kontot måste vara starkt skyddat av säkerhets skäl. Endast domän administratörer bör kunna hantera dator kontot. Se till att Kerberos-delegering på dator kontot är inaktiverat och att inget annat konto i Active Directory har Delegerings behörighet för `AZUREADSSOACC` dator kontot. Lagra dator kontot i en organisationsenhet (OU) där de är säkra från oavsiktliga borttagningar och där endast domän administratörer har åtkomst. Krypterings nyckeln för Kerberos på dator kontot bör också behandlas som känslig. Vi rekommenderar starkt att du [rullar igenom Kerberos-dekrypterings nyckeln](how-to-connect-sso-faq.md) för `AZUREADSSOACC` dator kontot minst var 30: e dag.

>[!IMPORTANT]
> Sömlös SSO stöder AES256_HMAC_SHA1, AES128_HMAC_SHA1 och RC4_HMAC_MD5 krypterings typer för Kerberos. Det rekommenderas att krypterings typen för AzureADSSOAcc $-kontot har angetts till AES256_HMAC_SHA1, eller en av AES-typerna vs. RC4 för ytterligare säkerhet. Krypterings typen lagras i attributet msDS-SupportedEncryptionTypes för kontot i din Active Directory.  Om krypterings typen för AzureADSSOAcc $-kontot har angetts till RC4_HMAC_MD5 och du vill ändra den till en av AES-krypterings typerna, se till att du först rullar över Kerberos-dekrypterings nyckeln för AzureADSSOAcc $-kontot enligt beskrivningen i [FAQ-dokumentet](how-to-connect-sso-faq.md) under den aktuella frågan, annars sker ingen sömlös SSO.

När installationen är klar fungerar sömlös enkel inloggning på samma sätt som andra inloggningar som använder integrerad Windows-autentisering (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hur loggar jag in i en webbläsare med sömlös enkel inloggning?

Inloggnings flödet i en webbläsare är följande:

1. Användaren försöker få åtkomst till ett webb program (till exempel Outlook Web App – https://outlook.office365.com/owa/) från en domänansluten företags enhet i företags nätverket.
2. Om användaren inte redan är inloggad omdirigeras användaren till inloggnings sidan för Azure AD.
3. Användaren skriver in sitt användar namn på inloggnings sidan för Azure AD.

   >[!NOTE]
   >Steg 2 & 3 hoppas över för [vissa program](./how-to-connect-sso-faq.md).

4. Med hjälp av Java Script i bakgrunden utmanar Azure AD webbläsaren, via ett 401 obehörigt svar, för att tillhandahålla en Kerberos-biljett.
5. Webbläsaren begär i sin tur en biljett från Active Directory för `AZUREADSSOACC` dator kontot (som representerar Azure AD).
6. Active Directory söker efter dator kontot och returnerar en Kerberos-biljett till webbläsaren som krypteras med dator kontots hemlighet.
7. Webbläsaren vidarebefordrar Kerberos-biljetten som den erhöll från Active Directory till Azure AD.
8. Azure AD dekrypterar Kerberos-biljetten, som innehåller identiteten för användaren som är inloggad på företags enheten, med hjälp av den tidigare delade nyckeln.
9. Efter utvärderingen returnerar Azure AD antingen en token tillbaka till programmet eller ber användaren att utföra ytterligare Korrektur, t. ex. Multi-Factor Authentication.
10. Om användar inloggningen lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och de steg som ingår.

![Flöde för sömlös enkel inloggning – webbapp](./media/how-to-connect-sso-how-it-works/sso2.png)

Sömlös SSO är Opportunistic, vilket innebär att om det Miss lyckas återgår inloggnings upplevelsen till sitt normala beteende, d.v.s. användaren måste ange sitt lösen ord för att logga in.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hur loggar jag in på en intern klient med sömlös enkel inloggning?

Inloggnings flödet på en ursprunglig klient är följande:

1. Användaren försöker komma åt ett internt program (till exempel Outlook-klienten) från en domänansluten företags enhet i företags nätverket.
2. Om användaren inte redan är inloggad hämtar det interna programmet användar namnet för användaren från enhetens Windows-session.
3. Appen skickar användar namnet till Azure AD och hämtar klient organisationens WS-Trust MEX-slutpunkt. Den här WS-Trust slut punkten används exklusivt av funktionen sömlös SSO och är inte en allmän implementering av WS-Trust-protokollet på Azure AD.
4. Appen frågar sedan WS-Trust MEX-slutpunkten för att se om den integrerade autentiseringens slut punkt är tillgänglig. Den integrerade autentiseringens slut punkt används exklusivt av sömlös SSO-funktionen.
5. Om steg 4 lyckas utfärdas en Kerberos-utmaning.
6. Om appen kan hämta Kerberos-biljetten vidarebefordras den till Azure ADs integrerade autentiserings slut punkt.
7. Azure AD dekrypterar Kerberos-biljetten och verifierar den.
8. Azure AD signerar användaren i och utfärdar en SAML-token till appen.
9. Appen skickar sedan SAML-token till Azure ADs OAuth2 token-slutpunkt.
10. Azure AD validerar SAML-token och utfärdar till appen en åtkomsttoken och en uppdateringstoken för den angivna resursen och en ID-token.
11. Användaren får åtkomst till appens resurs.

Följande diagram illustrerar alla komponenter och de steg som ingår.

![Sömlöst enkel inloggnings program flöde](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD sömlös SSO.
- [**Vanliga frågor**](how-to-connect-sso-faq.md) och svar på vanliga frågor.
- [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.
