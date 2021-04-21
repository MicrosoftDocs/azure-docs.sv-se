---
title: ISV-partnergalleri för Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar med våra ISV-partner för att skräddarsy slutanvändaren efter dina behov. Vårt partnernätverk utökar våra lösningsfunktioner. aktivera MFA, säker kundautentisering, rollbaserad åtkomstkontroll; bedrägeri med identitetsverifieringsverifiering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b555c8651c6e1608193a6ae06c39b20f633e4ea9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813675"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV-partner

Vårt ISV-partnernätverk utökar våra lösningsfunktioner för att hjälpa dig att skapa sömlösa slutanvändarupplevelser. Med Azure AD B2C kan du integrera med ISV-partner för att aktivera multifaktorautentiseringsmetoder (MFA), utföra rollbaserad åtkomstkontroll, aktivera identitetsverifiering och bevis, förbättra säkerheten med botidentifiering och bedrägeriskydd och uppfylla kraven för betalningstjänstdirektiv 2 (PSD2) säker kundautentisering (SCA). Använd våra detaljerade exempelgenomgångar för att lära dig hur du integrerar appar med ISV-partner.

>[!NOTE]
>På [Azure Active Directory B2C community-webbplatsen på GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) finns även exempel på anpassade principer från communityn.

## <a name="identity-verification-and-proofing"></a>Identitetsverifiering och bevis

Microsoft samarbetar med följande ISV:er för identitetsverifiering och bevis.

| ISV-partner | Beskrivning och integreringsgenomgångar |
|:-------------------------|:--------------|
|![Skärmbild av en Experian-logotyp.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) är en identitetsverifierings- och bevisleverantör som utför riskbedömningar baserat på användarattribut för att förhindra bedrägerier. |
|![Skärmbild av en logotyp för IDology.](./media/partner-gallery/idology-logo.png) | [IDology är](./partner-idology.md) en identitetsverifierings- och proofing-leverantör med ID-verifieringslösningar, lösningar för bedrägeriskydd, efterlevnadslösningar med mera.|
|![Skärmbild av en Jumio-logotyp.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) är en ID-verifieringstjänst som möjliggör automatiserad ID-verifiering i realtid och skyddar kunddata. |
| ![Skärmbild av en LexisNexis-logotyp.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) är en profilerings- och identitetsvalideringsprovider som verifierar användaridentifiering och tillhandahåller omfattande riskbedömning baserat på användarens enhet. |
| ![Skärmbild av en Onfido-logotyp](./media/partner-gallery/onfido-logo.png) | [Onfido är](./partner-onfido.md) en verifieringslösning för dokument-ID och ansiktsigenkänning som gör att företag kan *uppfylla* kraven på att känna till din kund och din identitet i realtid.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA och lösenordslös autentisering

Microsoft samarbetar med följande ISV:er för MFA och lösenordsfri autentisering.

| ISV-partner | Beskrivning och integreringsgenomgångar |
|:-------------------------|:--------------|
| ![Skärmbild av en hypr-logotyp](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) är en lösenordslös autentiseringsprovider som ersätter lösenord med kryptering med offentliga nycklar, vilket eliminerar bedrägeri, nätfiske och återanvändning av autentiseringsuppgifter. |
| ![Skärmbild av itsme-logotypen](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) är en digital ID-lösning som är kompatibel med Electronic Identification, Authentication and Trust Services (eiDAS) så att användarna kan logga in på ett säkert sätt utan kortläsare, lösenord, tvåfaktorsautentisering och flera PIN-koder. |
|![Skärmbild av en nyckellös logotyp.](./media/partner-gallery/keyless-logo.png) | [Nyckellös](./partner-keyless.md) är en lösenordslös autentiseringsprovider som tillhandahåller autentisering i form av en ansiktsigenkänningssökning och eliminerar bedrägeri, nätfiske och återanvändning av autentiseringsuppgifter.
| ![Skärmbild av en nevis-logotyp](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) möjliggör lösenordsfri autentisering och tillhandahåller en mobilt, fullständigt varumärkesspecifik slutanvändarupplevelse med Nevis Access-appen för stark kundautentisering och för att uppfylla PSD2-transaktionskraven. |
| ![Skärmbild av en Trusona-logotyp](./media/partner-gallery/trusona-logo.png) | [Trusona-integrering](./partner-trusona.md) hjälper dig att logga in på ett säkert sätt och möjliggör lösenordsfri autentisering, MFA och digital licensgenomsökning. |
| ![Skärmbild av en twilio-logotyp.](./media/partner-gallery/twilio-logo.png) | [Twilio Verify-appen](./partner-twilio.md) tillhandahåller flera lösningar för att aktivera MFA via SMS engångslösenord (OTP), tidsbaserade engångslösenord (TOTP) och push-meddelanden samt för att uppfylla SCA-kraven för PSD2. |
| ![Skärmbild av en skrivningDNA-logotyp](./media/partner-gallery/typingdna-logo.png) | [Att skrivaDNA](./partner-typingdna.md) möjliggör stark kundautentisering genom att analysera en användares skrivmönster. Det hjälper företag att aktivera en tyst MFA och uppfylla SCA-kraven för PSD2. |
| ![Skärmbild av en whoiam-logotyp](./media/partner-gallery/whoiam-logo.png) | [WhoIAM är](./partner-whoiam.md) ett BRIMS-program (Branded Identity Management System) som gör det möjligt för organisationer att verifiera sin användarbas via röst, SMS och e-post. |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll 
 
Microsoft samarbetar med följande ISV:er för rollbaserad åtkomstkontroll.

| ISV-partner | Beskrivning och integreringsgenomgångar |
|:-------------------------|:--------------|
| ![Skärmbild av en n8identity-logotyp](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) är en styrningsplattform för identitet som en tjänst som tillhandahåller en lösning för migrering av kundkonton och CSR-administration (Customer Service Requests) som körs på Microsoft Azure. |
| ![Skärmbild av en Saviynt-logotyp](./media/partner-gallery/saviynt-logo.png) | [Den molnbaserade plattformen Saviynt](./partner-Saviynt.md) främjar bättre säkerhet, efterlevnad och styrning genom intelligent analys och integrering mellan program för att effektivisera IT-moderniseringen. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Skydda hybridåtkomst till lokala program

Microsoft samarbetar med följande ISV:er för att ge säker hybridåtkomst till lokala program. 

| ISV-partner | Beskrivning och integreringsgenomgångar |
|:-------------------------|:--------------|
| ![Skärmbild av en Ping-logotyp](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) ger säker hybridåtkomst till lokala äldre program i flera moln. |
| ![Skärmbild av en strata-logotyp](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) ger säker hybridåtkomst till lokala program genom att tillämpa konsekventa åtkomstprinciper, hålla identiteter synkroniserade och göra det enkelt att övergå program från äldre identitetssystem till standardbaserad autentisering och åtkomstkontroll som tillhandahålls av Azure AD B2C. |
| ![Skärmbild av en zscaler-logotyp](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) levererar principbaserad, säker åtkomst till privata program och tillgångar utan kostnad, problem eller säkerhetsrisker med ett VPN. |

## <a name="fraud-protection"></a>Bedrägeriskydd

Microsoft samarbetar med följande ISV:er för identifiering och förebyggande av bedrägerier. 

| ISV-partner | Genomgång av beskrivning och integrering |
|:-------------------------|:--------------|
| ![Skärmbild av en Arkose-labblogotyp](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) är en leverantör av bedrägeriskydd som hjälper organisationer att skydda mot robotattacker, kontoupptagandeattacker och bedrägliga kontoöppningar. |
| ![Skärmbild av en BioCatch-logotyp](./media/partner-gallery/biocatch-logo.png) | [BioCatch](./partner-biocatch.md) är en leverantör av bedrägeriskydd som analyserar en användares fysiska och kognitiva digitala beteenden för att generera insikter som skiljer mellan legitima kunder och cyberbrottsligheter. |
| ![Skärmbild av en Microsoft Dynamics 365-logotyp](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Bedrägeriskydd är](./partner-dynamics-365-fraud-protection.md) en lösning som hjälper organisationer att skydda mot falska kontoöppningar via fingeravtryck på enheten. |


## <a name="additional-information"></a>Ytterligare information

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Nästa steg

Välj en partner i tabellerna som nämns för att lära dig att integrera sin lösning med Azure AD B2C.
