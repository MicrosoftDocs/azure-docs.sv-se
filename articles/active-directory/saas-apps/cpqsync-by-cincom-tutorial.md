---
title: 'Självstudie: Azure Active Directory integration med CPQSync av Cincom | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CPQSync av Cincom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 4bd98acec06caf9c807844df2c957e61e0f0b2a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455135"
---
# <a name="tutorial-integrate-cpqsync-by-cincom-with-azure-active-directory"></a>Självstudie: integrera CPQSync med Cincom med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar CPQSync med Cincom med Azure Active Directory (Azure AD). När du integrerar CPQSync från Cincom med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CPQSync av Cincom.
* Gör det möjligt för användarna att logga in automatiskt till CPQSync av Cincom med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CPQSync av Cincom-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CPQSync av Cincom stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-cpqsync-by-cincom-from-the-gallery"></a>Lägga till CPQSync från Cincom från galleriet

Om du vill konfigurera integrering av CPQSync av Cincom i Azure AD måste du lägga till CPQSync från Cincom från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **CPQSync efter Cincom** i sökrutan.
1. Välj **CPQSync av Cincom** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cpqsync-by-cincom"></a>Konfigurera och testa enkel inloggning med Azure AD för CPQSync av Cincom

Konfigurera och testa Azure AD SSO med CPQSync genom Cincom med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CPQSync av Cincom.

Om du vill konfigurera och testa Azure AD SSO med CPQSync av Cincom slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera CPQSync av CINCOM SSO](#configure-cpqsync-by-cincom-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa CPQSync av Cincom test User](#create-cpqsync-by-cincom-test-user)** -om du vill ha en motsvarighet till B. Simon i CPQSync av Cincom som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **CPQSync av Cincom** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://cincom.oktapreview.com/sso/saml2/<CUSTOMURL>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://cincom.okta.com/sso/saml2/<CUSTOMDOMAIN>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://cincom.okta.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [CPQSync från Cincom client support team](https://cpqsupport.cincomcpq.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (RAW)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera CPQSync efter Cincom** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CPQSync av Cincom.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **CPQSync av Cincom**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cpqsync-by-cincom-sso"></a>Konfigurera CPQSync av Cincom SSO

Om du vill konfigurera enkel inloggning på **CPQSync av Cincom** -sidan måste du skicka det hämtade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [CPQSync av Cincom support team](https://cpqsupport.cincomcpq.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cpqsync-by-cincom-test-user"></a>Skapa CPQSync av Cincom-test användare

I det här avsnittet skapar du en användare som heter B. Simon i CPQSync av Cincom. Arbeta med [CPQSync av Cincom support team](https://cpqsupport.cincomcpq.com) för att lägga till användarna på CPQSync av Cincom-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen CPQSync av Cincom på åtkomst panelen, bör du loggas in automatiskt på CPQSync av Cincom som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)