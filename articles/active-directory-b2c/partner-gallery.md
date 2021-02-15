---
title: ISV partner Galleri för Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar med våra ISV-partner för att skräddarsy din slut användar upplevelse efter dina behov. Vårt partner nätverk utökar våra lösnings funktioner; Aktivera MFA, säker kundautentisering, rollbaserad åtkomst kontroll; bekämpa bedrägerier genom att bevisa för identitets verifiering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1ee632e3aaae7b858ab43b45f6e72aff8d1fb77
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361776"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV-partner

Vårt ISV partner Network utökar våra lösnings funktioner som hjälper dig att bygga sömlösa slut användar upplevelser. Med Azure AD B2C kan du integrera med ISV-partners för att aktivera Multi-Factor Authentication-metoder (MFA), utföra rollbaserad åtkomst kontroll, aktivera identitets verifiering och språk kontroll, förbättra säkerheten med bot-identifiering och bedrägeri skydd och uppfylla betalnings tjänsternas direktiv 2 (PSD2) Secure Custom Authentication (SCA)-krav. Använd våra detaljerade exempel genom gångar för att lära dig hur du integrerar appar med ISV-partners.

>[!NOTE]
>[Azure Active Directory B2C Community-webbplatsen på GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) innehåller också exempel på anpassade principer från communityn.

## <a name="identity-verification-and-proofing"></a>Identitets verifiering och-korrektur

Med Azure AD B2C partner kan kunder aktivera identitets verifiering och att deras slutanvändare har behörighet att tillåta konto registrering eller åtkomst. Identitets verifiering och språk kontroll kan kontrol lera dokument, kunskapsbaserade uppgifter och Live.

Ett arkitektur diagram med hög nivå förklarar flödet.

![Diagram som visar flödet för identitets bevisning](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft samarbetar med följande ISV: er för identitets verifiering och språk kontroll.

| ISV-partner | Beskrivning och integrations genom gångar |
|:-------------------------|:--------------|
|![Skärm bild av en Experian:-logotyp.](./media/partner-gallery/experian-logo.png) | [Experian:](./partner-experian.md) är en identitets verifiering och en kontroll leverantör som utför riskbedömningar baserat på användarattribut för att förhindra bedrägerier. |
|![Skärm bild av en IDology-logotyp.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) är en identitets verifiering och en språkprovider med ID-verifierings lösningar, bedrägeri förebyggande lösningar, lösningar för efterlevnad och andra.|
|![Skärm bild av en Jumio-logotyp.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) är en tjänst för ID-verifiering som möjliggör verifiering av AUTOMATISERAt ID i real tid, vilket skyddar kund information. |
| ![Skärm bild av en LexisNexis-logotyp.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) är en profilerings-och identitets verifierings leverantör som verifierar användar identifiering och ger omfattande riskbedömning baserat på användarens enhet. |
| ![Skärm bild av en Onfido-logotyp](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) är ett dokument-ID och en verifierings lösning för ansikts biometrik som gör det möjligt för företag att möta sina *kunskaper om kund* -och identitets krav i real tid.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA och lösenordsbaserad autentisering

Microsoft samarbetar med följande ISV: er för MFA-och lösenordsbaserad autentisering.

| ISV-partner | Beskrivning och integrations genom gångar |
|:-------------------------|:--------------|
| ![Skärm bild av en hypr-logotyp](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) är en lösenordsskyddad autentiseringsprovider som ersätter lösen ord med krypteringar med offentliga nycklar som eliminerar bedrägerier, nätfiske och åter användning av autentiseringsuppgifter. |
| ![Skärm bild av en itsme-logotyp](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) är en eiDAS-kompatibel (Electronic Identification, Authentication and Trust Services) Digital ID-lösning som gör att användarna kan logga in på ett säkert sätt utan kort läsare, lösen ord, tvåfaktorautentisering och flera PIN-koder. |
|![Skärm bild av en Keyless-logotyp.](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) är en lösenordsskyddad autentiseringsprovider som ger autentisering i form av en bio metrisk genomsökning i ansikts form och eliminerar bedrägerier, nätfiske och åter användning av autentiseringsuppgifter.
| ![Skärm bild av en Nevis-logotyp](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) möjliggör lösenordsautentisering utan lösen ord och ger en mobil-och helt anpassad slut användar upplevelse med Nevis-åtkomst-appen för stark kundautentisering och för att uppfylla PSD2 transaktions krav. |
| ![Skärm bild av en trusona-logotyp](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) -integreringen hjälper dig att logga in på ett säkert sätt och möjliggör autentisering med lösen ords avsökning, MFA och digital License. |
| ![Skärm bild av en Twilio-logotyp.](./media/partner-gallery/twilio-logo.png) | [Twilio verifiera app](./partner-twilio.md) tillhandahåller flera lösningar för att aktivera MFA via SMS eng ång slö sen ord (eng ång slö sen ord), tidsbaserat eng ång slö sen ord (TOTP mobilapp) och push-meddelanden och för att uppfylla SCA-kraven för PSD2. |
| ![Skärm bild av en typingDNA-logotyp](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) möjliggör stark kundautentisering genom att analysera en användares Skriv mönster. Det hjälper företag att aktivera en tyst MFA och uppfylla SCA-kraven för PSD2. |
| ![Skärm bild av en whoiam-logotyp](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) är ett BRIMS-program som gör det möjligt för organisationer att verifiera användar basen med röst, SMS och e-post. |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll 
 
Microsoft samarbetar med följande ISV: er för rollbaserad åtkomst kontroll.

| ISV-partner | Beskrivning och integrations genom gångar |
|:-------------------------|:--------------|
| ![Skärm bild av en n8identity-logotyp](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) är en plattform för identitets-som-tjänst-styrning som tillhandahåller lösningar för att hantera migrering av kund konton och kund tjänst begär Anden (CSR) som körs på Microsoft Azure. |
| ![Skärm bild av en Saviynt-logotyp](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) Cloud – ursprunglig plattform befordrar bättre säkerhet, efterlevnad och styrning via intelligent analys och integrering av program vara för att effektivisera IT-modernisering. |

## <a name="security"></a>Säkerhet

Microsoft samarbetar med följande ISV: er för säkerhet.

| ISV-partner | Beskrivning och integrations genom gångar |
|:-------------------------|:--------------|
| ![Skärm bild av en Arkose Lab-logotyp](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) är en provider för bedrägeri förebyggande lösningar som hjälper organisationer att skydda mot bot-attacker, konto upptagnings attacker och bedrägligt konto öppningar. |
| ![Skärm bild av en Microsoft Dynamics 365-logotyp](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 bedrägeri skydd](./partner-dynamics-365-fraud-protection.md) är en lösning som hjälper organisationer att skydda mot bedrägligt konto öppningar genom enhets finger avtryck. |
| ![Skärm bild av en ping-logotyp](./media/partner-gallery/ping-logo.png) | [Ping identitet](./partner-ping-identity.md) möjliggör säker hybrid åtkomst till lokala äldre program över flera moln. |
| ![Skärm bild av en Strata-logotyp](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) ger säker hybrid åtkomst till lokala program genom att tvinga konsekventa åtkomst principer, hålla identiteter synkroniserade och göra det enkelt att överföra program från äldre identitets system till standardbaserad autentisering och åtkomst kontroll som tillhandahålls av Azure AD B2C. |
| ![Skärm bild av en Zscaler-logotyp](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) ger principbaserad, säker åtkomst till privata program och till gångar utan kostnad, krångel eller säkerhets risker för en VPN-anslutning. |

## <a name="additional-information"></a>Ytterligare information

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>Nästa steg

Välj en partner i de tabeller som nämns för att lära dig hur du integrerar lösningen med Azure AD B2C.
