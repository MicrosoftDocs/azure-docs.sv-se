---
title: 'Självstudie: Azure Active Directory integrering med Wandera RADAR-administratör | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wandera RADAR-administratör.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 383b49a7363fdca9327d419f5bed092d489a95d5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951818"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Självstudie: integrera Wandera POLÄRDIAGRAM-administratör med Azure Active Directory

I den här självstudien får du lära dig att integrera Wandera POLÄRDIAGRAM-administratören med Azure Active Directory (Azure AD). När du integrerar Wandera RADAR-administratör med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Wandera RADAR-administratör.
* Gör det möjligt för användarna att logga in automatiskt till Wandera RADAR-administratör med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Wandera RADAR admin, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Wandera RADAR-administratören stöder **IDP** INITIERAd SSO
* När du har konfigurerat Wandera RADAR-administratör kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Lägga till Wandera RADAR-administratör från galleriet

Om du vill konfigurera integreringen av Wandera RADAR-administratören i Azure AD måste du lägga till Wandera POLÄRDIAGRAM från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Wandera radar admin** i sökrutan.
1. Välj **WANDERA radar-administratör** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med Wandera RADAR-administratör med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Wandera POLÄRDIAGRAM-administratören.

Om du vill konfigurera och testa Azure AD SSO med Wandera RADAR-administratör slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
   * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera WANDERA radar admin SSO](#configure-wandera-radar-admin-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
   * **[Skapa WANDERA radar Admin test User](#create-wandera-radar-admin-test-user)** – om du vill ha en motsvarighet till B. Simon i Wandera polärdiagram-administratören som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Wandera radar admin** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [WANDERA polärdiagram admin client support team](https://www.wandera.com/about-wandera/contact/#supportsection) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Ersätt noggrant <tenant id> delen av URL: en med det klient-ID som visas på sidan **Inställningar**  >  **Administration**  >  **enkel inloggning** i ditt Wandera-konto.


1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **SAML-signerings certifikat** för att redigera inställningarna.

    ![Signerings alternativ](common/signing-option.png)

    1. Välj **signerings alternativ** som **signerat SAML-svar och kontroll**.

    1. Välj **signeringsalgoritmen** som **SHA-256**.

1. I avsnittet **Konfigurera WANDERA radar-administratör** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Wandera RADAR-administratör.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **WANDERA radar-administratör**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-wandera-radar-admin-sso"></a>Konfigurera Wandera RADAR admin SSO

1. Om du vill automatisera konfigurationen i Wandera RADAR-administratören måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Setup WANDERA radar admin** dirigerar dig till Wandera-administrations programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Wandera RADAR-administratören. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-4.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Wandera RADAR-administratören manuellt öppnar du ett nytt webbläsarfönster och loggar in på företags platsen för Wandera RADAR-administratören som administratör och utför följande steg:

4. I det övre högra hörnet på sidan klickar du på **Inställningar**  >  **Administration**  >  **enkel inloggning** och markerar sedan alternativet **Aktivera SAML 2,0** för att utföra följande steg.

    ![Konfiguration av Wandera RADAR-admin](./media/wandera-tutorial/config01.png)

    a. Klicka på **eller ange de obligatoriska fälten manuellt**.

    b. I text rutan **IDP EntityId** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. Öppna XML-metadata för federationsmetadata i anteckningar, kopiera dess innehåll och klistra in det i text rutan **IDP Public X. 509 Certificate** .

    d. Klicka på **Spara**.

### <a name="create-wandera-radar-admin-test-user"></a>Skapa Wandera POLÄRDIAGRAM-administratör test användare

I det här avsnittet skapar du en användare som heter B. Simon i Wandera RADAR-administratören. arbeta med [support teamet för WANDERA polärdiagram](https://www.wandera.com/about-wandera/contact/#supportsection) för att lägga till användarna i Wandera radar-administratörskonsolen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Wandera RADAR-administratör på åtkomst panelen, bör du loggas in automatiskt på den Wandera RADAR-administratör som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
