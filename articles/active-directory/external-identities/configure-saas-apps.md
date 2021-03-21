---
title: Konfigurera SaaS-appar för B2B-samarbete – Azure AD
description: Lär dig hur du konfigurerar SaaS-appar för Azure Active Directory B2B-samarbete och Visa fler tillgängliga resurser.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90705518"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurera SaaS-appar för B2B-samarbete

Azure Active Directory (Azure AD) B2B-samarbete fungerar med de flesta appar som integreras med Azure AD. I det här avsnittet går vi igenom anvisningarna för att konfigurera några populära SaaS-appar för användning med Azure AD B2B.

Innan du tittar på App-Specific-instruktioner är här några regler för tummen:

* För de flesta av apparna måste användar installations programmet ske manuellt. Det vill säga användare måste skapas manuellt i appen.

* För appar som stöder automatisk konfiguration, till exempel Dropbox, skapas separata inbjudningar från apparna. Användarna måste vara säkra på att godkänna varje inbjudan.

* I användarattribut, för att minimera eventuella problem med Mangled-UPD (User profile disk) i gäst användare, anger du alltid **användar-ID** till **User. mail**.


## <a name="dropbox-business"></a>Dropbox Business

Om du vill att användarna ska kunna logga in med sitt organisations konto måste du manuellt konfigurera Dropbox-företag att använda Azure AD som en Security Assertion Markup Language (SAML) identitets leverantör. Om Dropbox-företag inte har kon figurer ATS för detta, kan det inte ställas in eller på annat sätt tillåta att användare loggar in med Azure AD.

1. Om du vill lägga till Dropbox-affärsappen i Azure AD väljer du **företags program** i det vänstra fönstret och klickar sedan på **Lägg till**.

   ![Knappen Lägg till på sidan företags program](media/configure-saas-apps/add-dropbox.png)

2. I fönstret **Lägg till ett program** anger du **Dropbox** i sökrutan och väljer sedan **Dropbox for Business** i resultat listan.

   ![Sök efter "Dropbox" på sidan Lägg till ett program](media/configure-saas-apps/add-app-dialog.png)

3. På sidan **enkel inloggning** väljer du **enkel inloggning** i det vänstra fönstret och anger sedan **User. mail** i rutan **användar identifierare** . (Den anges som UPN som standard.)

   ![Konfigurera enkel inloggning för appen](media/configure-saas-apps/configure-app-sso.png)

4. Om du vill ladda ned certifikatet som ska användas för Dropbox-konfiguration väljer du **Konfigurera Dropbox** och väljer sedan **SAML-URL för enkel inloggning** i listan.

   ![Laddar ned certifikatet för Dropbox-konfiguration](media/configure-saas-apps/download-certificate.png)

5. Logga in på Dropbox med inloggnings-URL: en från sidan för **enkel inloggning** .

   ![Skärm bild som visar inloggnings sidan för Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. På menyn väljer du **Administratörs konsolen**.

   ![Länken "administrations konsolen" i Dropbox-menyn](media/configure-saas-apps/dropbox-menu.png)

7. I dialog rutan **autentisering** väljer du **mer**, överför certifikatet och i rutan **Logga in URL** anger du URL: en för SAML enkel inloggning.

   ![Länken "mer" i dialog rutan komprimerad autentisering](media/configure-saas-apps/dropbox-auth-01.png)

   ![Inloggnings-URL: en i dialog rutan utökad autentisering](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Om du vill konfigurera automatiska användar inställningar i Azure Portal väljer du **etablering** i den vänstra rutan, väljer **Automatisk** i **etablerings läge** och väljer sedan **auktorisera**.

   ![Konfigurera automatisk användar etablering i Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

När gäst-eller medlems användare har kon figurer ATS i Dropbox-appen får de en separat inbjudan från Dropbox. Om du vill använda enkel inloggning med Dropbox måste inbjudna acceptera inbjudan genom att klicka på en länk i den.

## <a name="box"></a>Box
Du kan göra det möjligt för användare att autentisera Box gäst användare med deras Azure AD-konto genom att använda Federation som baseras på SAML-protokollet. I den här proceduren laddar du upp metadata till Box.com.

1. Lägg till Box-appen från Enterprise Apps.

2. Konfigurera enkel inloggning i följande ordning:

   ![Skärm bild som visar konfigurations inställningar för enkel inloggning](media/configure-saas-apps/configure-box-sso.png)

   a. I rutan **inloggnings-URL** kontrollerar du att inloggnings-URL: en har angetts korrekt för box i Azure Portal. URL: en är URL: en för din Box.com-klient. Den bör följa namngivnings konventionen *https://.box.com* .  
   **Identifieraren** gäller inte för den här appen, men den visas fortfarande som ett obligatoriskt fält.

   b. I rutan **användar identifierare** anger du **User. mail** (för SSO för gäst konton).

   c. Under **SAML-signerings certifikat** klickar du på **Skapa nytt certifikat**.

   d. Börja konfigurera din Box.com-klient för att använda Azure AD som en identitetsprovider genom att ladda ned metadatafilen och sedan spara den på den lokala enheten.

   e. Vidarebefordra metadatafilen till Box support-teamet, som konfigurerar enkel inloggning åt dig.

3. För automatisk användar konfiguration i Azure AD väljer du **etablering** i den vänstra rutan och väljer sedan **auktorisera**.

   ![Auktorisera Azure AD för att ansluta till Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Som Dropbox bjuder in, måste Box-inbjudan lösa in sin inbjudan från Box-appen.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
- [Mappning av användar anspråk för B2B-samarbete](claims-mapping.md)
- [Microsoft 365 extern delning](o365-external-user.md)

