---
title: Avancerade alternativ för certifikat signering för SAML-token för Azure AD-appar
description: Lär dig hur du använder avancerade alternativ för certifikat signering i SAML-token för förintegrerade appar i Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dd6a7a34ac231f8af9d08aab99e3d552f33ea1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259629"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ för certifikat signering i SAML-token för Galleri-appar i Azure Active Directory

Idag Azure Active Directory (Azure AD) stöder tusentals förintegrerade program i Azure Active Directory app-galleriet. Över 500 av programmen stöder enkel inloggning med hjälp av [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0-protokollet, till exempel [Netsuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) -programmet. När en kund autentiserar till ett program via Azure AD med hjälp av SAML, skickar Azure AD en token till programmet (via ett HTTP-inlägg). Programmet validerar och använder sedan token för att logga in kunden i stället för att begära ett användar namn och lösen ord. Dessa SAML-token är signerade med det unika certifikat som genereras i Azure AD och med specifika standardalgoritmer.

Azure AD använder några av standardinställningarna för Galleri programmen. Standardvärdena ställs in baserat på programmets krav.

I Azure AD kan du konfigurera alternativ för certifikat signering och algoritmen för certifikat signering.

## <a name="certificate-signing-options"></a>Alternativ för certifikatsignering

Azure AD stöder tre certifikat signerings alternativ:

* **Signera SAML-kontroll**. Det här standard alternativet är inställt för de flesta Galleri program. Om du väljer det här alternativet signerar Azure AD som en identitetsprovider (IdP) SAML-intyget och certifikatet med [X. 509](https://wikipedia.org/wiki/X.509) -certifikatet för programmet.

* **Signera SAML-svar**. Om du väljer det här alternativet signerar Azure AD som en IdP SAML-svaret med X. 509-certifikatet för programmet.

* **Signera SAML-svar och kontroll**. Om du väljer det här alternativet signerar Azure AD som en IdP hela SAML-token med X. 509-certifikatet för programmet.

## <a name="certificate-signing-algorithms"></a>Algoritmer för certifikat signering

Azure AD stöder två krypteringsalgoritmer eller SHA (Secure Hash Algorithms) för att signera SAML-svaret:

* **SHA-256**. Azure AD använder den här standardalgoritmen för att signera SAML-svaret. Det är den nyaste algoritmen och är säkrare än SHA-1. De flesta program stöder SHA-256-algoritmen. Om ett program stöder endast SHA-1 som Signeringsalgoritm, kan du ändra det. Annars rekommenderar vi att du använder SHA-256-algoritmen för signering av SAML-svaret.

* **SHA-1**. Den här algoritmen är äldre och behandlas som mindre säker än SHA-256. Om ett program stöder endast denna Signeringsalgoritm kan du välja det här alternativet i list rutan **Signeringsalgoritm** . Azure AD signerar sedan SAML-svaret med SHA-1-algoritmen.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Ändra alternativ för certifikat signering och Signeringsalgoritm

Om du vill ändra ett programs signerings alternativ för SAML-certifikat och algoritmen för certifikat signering väljer du programmet i fråga:

1. Logga in på ditt konto i [Azure Active Directory-portalen](https://aad.portal.azure.com/). Sidan **Azure Active Directory administrations Center** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företags program i ditt konto visas.
1. Välj ett program. En översikts sida för programmet visas.

   ![Exempel: översikts sida för program](./media/certificate-signing-options/application-overview-page.png)

Ändra sedan alternativen för certifikat signering i SAML-token för programmet:

1. I det vänstra fönstret på sidan program översikt väljer du **enkel inloggning**.
1. Om sidan **Konfigurera en enda Sign-On med SAML-förhands granskning** visas går du till steg 5.
1. Om sidan **Välj en enkel inloggnings metod** inte visas väljer du **ändra lägen för enkel inloggning** för att visa sidan.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML** om det är tillgängligt. (Om **SAML** inte är tillgängligt stöder programmet inte SAML, och du kan ignorera resten av den här proceduren och artikeln.)
1. På sidan **Konfigurera en enskild Sign-On med SAML-Preview** letar du upp certifikat rubriken för **SAML-signering** och väljer **redigerings** ikonen (en blyertspenna). Sidan **SAML-signerings certifikat** visas.

   ![Exempel: sidan för SAML-signerings certifikat](./media/certificate-signing-options/saml-signing-page.png)

1. I list rutan **signerings alternativ väljer du** **signera SAML-svar**, **signera SAML-kontroll** eller **signera SAML-svar och kontroll**. Beskrivningar av de här alternativen visas tidigare i den här artikeln i [alternativ för certifikat signering](#certificate-signing-options).
1. I list rutan **Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256**. Beskrivningar av de här alternativen visas tidigare i den här artikeln i avsnittet [algoritmer för certifikat signering](#certificate-signing-algorithms) .
1. Om du är nöjd med dina val väljer du **Spara** för att tillämpa de nya certifikat inställningarna för SAML-signering. Annars väljer du **X** för att ignorera ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory app-galleriet](./configure-saml-single-sign-on.md)
* [Felsöka SAML-baserad enkel inloggning](./debug-saml-sso-issues.md)