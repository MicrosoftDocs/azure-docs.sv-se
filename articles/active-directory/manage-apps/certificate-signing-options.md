---
title: Avancerade signeringsalternativ för SAML-tokencertifikat för Azure AD-appar
description: Lär dig hur du använder avancerade alternativ för certifikatsignering i SAML-token för förintegrerade appar i Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377878"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ för certifikatsignering i SAML-token för galleriappar i Azure Active Directory

Idag Azure Active Directory (Azure AD) tusentals förintegrerade program i Azure Active Directory App Gallery. Över 500 av programmen stöder enkel inloggning med [hjälp av Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0-protokollet, till exempel [NetSuite-programmet.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) När en kund autentiserar till ett program via Azure AD med hjälp av SAML skickar Azure AD en token till programmet (via en HTTP POST). Programmet validerar sedan och använder token för att logga in kunden i stället för att fråga efter ett användarnamn och lösenord. Dessa SAML-token signeras med det unika certifikat som genereras i Azure AD och av specifika standardalgoritmer.

Azure AD använder några av standardinställningarna för galleriprogrammen. Standardvärdena konfigureras baserat på programmets krav.

I Azure AD kan du konfigurera alternativ för certifikatsignering och certifikatsigneringsalgoritmen.

## <a name="certificate-signing-options"></a>Alternativ för certifikatsignering

Azure AD stöder tre alternativ för certifikatsignering:

* **Signera SAML-försäkran**. Det här standardalternativet är inställt på de flesta galleriprogram. Om du väljer det här alternativet signerar Azure AD som identitetsprovider (IdP) SAML-försäkran och certifikatet med [X.509-certifikatet](https://wikipedia.org/wiki/X.509) för programmet.

* **Signera SAML-svar**. Om du väljer det här alternativet signerar Azure AD som IdP SAML-svaret med X.509-certifikatet för programmet.

* **Signera SAML-svar och försäkran**. Om du väljer det här alternativet signerar Azure AD som idP hela SAML-token med programmets X.509-certifikat.

## <a name="certificate-signing-algorithms"></a>Algoritmer för certifikatsignering

Azure AD stöder två signeringsalgoritmer, eller säkra hash-algoritmer (SHA) för att signera SAML-svaret:

* **SHA-256**. Azure AD använder den här standardalgoritmen för att signera SAML-svaret. Det är den senaste algoritmen och är säkrare än SHA-1. De flesta program stöder SHA-256-algoritmen. Om ett program endast stöder SHA-1 som signeringsalgoritm kan du ändra det. Annars rekommenderar vi att du använder SHA-256-algoritmen för att signera SAML-svaret.

* **SHA-1**. Den här algoritmen är äldre och behandlas som mindre säker än SHA-256. Om ett program endast stöder den här signeringsalgoritmen kan du välja det här alternativet i listrutan Signeringsalgoritm.  Azure AD signerar sedan SAML-svaret med SHA-1-algoritmen.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Ändra alternativ för certifikatsignering och signeringsalgoritm

Om du vill ändra ett programs ALTERNATIV för SAML-certifikatsignering och certifikatsigneringsalgoritmen väljer du programmet i fråga:

1. Logga in [Azure Active Directory ditt](https://aad.portal.azure.com/)konto i Azure Active Directory portal. Sidan **Azure Active Directory administrationscenter** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företagsprogram i ditt konto visas.
1. Välj ett program. En översiktssida för programmet visas.

   ![Exempel: Sidan Programöversikt](./media/certificate-signing-options/application-overview-page.png)

Ändra sedan alternativen för certifikatsignering i SAML-token för programmet:

1. I den vänstra rutan på programöversiktssidan väljer **du Enkel inloggning.**
1. Om sidan **Konfigurera enkel Sign-On med SAML – förhandsversion** visas går du till steg 5.
1. Om sidan **Välj en metod för enkel inloggning** inte visas väljer du Ändra lägen för enkel inloggning för **att** visa sidan.
1. På sidan **Välj en metod för enkel inloggning väljer** du **SAML om** det är tillgängligt. (Om **SAML** inte är tillgängligt stöder programmet inte SAML och du kan ignorera resten av den här proceduren och artikeln.)
1. På sidan **Set up Single Sign-On with SAML - Preview** (Konfigurera enkel inloggning med SAML – förhandsversion) hittar du rubriken **SAML-signeringscertifikat** och väljer ikonen **Redigera** (en penna). Sidan **SAML-signeringscertifikat** visas.

   ![Exempel: Sida för SAML-signeringscertifikat](./media/certificate-signing-options/saml-signing-page.png)

1. I **listrutan Signeringsalternativ** väljer du Sign **SAML response**(Signera SAML-svar) , Sign SAML assertion (Signera SAML-försäkran) eller Sign SAML response and assertion (Signera **SAML-försäkran)** eller **Sign SAML response and assertion (Signera SAML-svar och -försäkran).** Beskrivningar av dessa alternativ visas tidigare i den här artikeln i [signeringsalternativen för certifikat.](#certificate-signing-options)
1. I **listrutan Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256.** Beskrivningar av dessa alternativ visas tidigare i den här artikeln i avsnittet [Algoritmer för certifikatsignering.](#certificate-signing-algorithms)
1. Om du är nöjd med dina val väljer du Spara för **att tillämpa** de nya inställningarna för SAML-signeringscertifikat. Annars väljer du **X för** att ta bort ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera enkel inloggning för program som inte finns i Azure Active Directory App Gallery](./configure-saml-single-sign-on.md)
* [Felsöka SAML-baserad enkel inloggning](./debug-saml-sso-issues.md)