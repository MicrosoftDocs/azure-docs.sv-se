---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med dynaTrace | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dynaTrace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: a1f052df9b233fd564ed7e16c04c32c0c7234e1a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555687"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med dynaTrace

I den här självstudien får du lära dig hur du integrerar dynaTrace med Azure Active Directory (Azure AD). När du integrerar dynaTrace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till dynaTrace.
* Gör det möjligt för användarna att logga in automatiskt till dynaTrace med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* DynaTrace för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* DynaTrace stöder **SP-och IDP** -INITIERAd SSO
* DynaTrace stöder **just-in-Time** User-etablering

> [!NOTE]
> Identifieraren för det här programmet är ett fast sträng värde. Endast en instans kan konfigureras i en klient.

## <a name="adding-dynatrace-from-the-gallery"></a>Lägga till dynaTrace från galleriet

Om du vill konfigurera integreringen av dynaTrace i Azure AD måste du lägga till dynaTrace från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program**och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **dynaTrace** i sökrutan.
1. Välj **dynaTrace** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-dynatrace"></a>Konfigurera och testa enkel inloggning med Azure AD för dynaTrace

Konfigurera och testa Azure AD SSO med dynaTrace med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i dynaTrace.

Om du vill konfigurera och testa Azure AD SSO med dynaTrace, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera DYNATRACE SSO](#configure-dynatrace-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa dynaTrace test User](#create-dynatrace-test-user)** -om du vill ha en motsvarighet till B. Simon i dynaTrace som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **dynaTrace** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL: er** och Slutför följande steg för att konfigurera programmet i **SP** -initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://sso.dynatrace.com/`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **SAML-signerings certifikat** väljer du knappen **Redigera** för att öppna dialog rutan **SAML-signeringscertifikat** . Slutför följande steg:

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

    a. Inställningen för **signerings alternativ** är ifylld. Kontrol lera inställningarna enligt din organisation.

    b. Klicka på **Spara**.

    ![Communifire-signeringsalternativ](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. I avsnittet **Konfigurera dynaTrace** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till dynaTrace.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **dynaTrace**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-dynatrace-sso"></a>Konfigurera dynaTrace SSO

Om du vill konfigurera enkel inloggning på **dynaTrace** -sidan måste du skicka den hämtade **XML-** filen med federationsmetadata och lämpliga kopierade url: er från Azure Portal till [dynaTrace](https://www.dynatrace.com/support/help/shortlink/users-sso-hub). Du kan följa instruktionerna på dynaTrace-webbplatsen för att konfigurera SAML SSO-anslutningen på båda sidorna.

### <a name="create-dynatrace-test-user"></a>Skapa dynaTrace test användare

I det här avsnittet skapas en användare som heter B. Simon i dynaTrace. DynaTrace stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i dynaTrace skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen dynaTrace på åtkomst panelen, bör du loggas in automatiskt på den dynaTrace som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova dynaTrace med Azure AD](https://aad.portal.azure.com/)
