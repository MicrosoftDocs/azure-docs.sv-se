---
title: 'Självstudie: Azure Active Directory-integrering med Carbonite Endpoint backup | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Carbonite-slutpunktens säkerhets kopiering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 9888ae6f7d28f6fef5901ea246d71d7b9ae9f78f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530621"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Självstudie: integrera Carbonite Endpoint backup med Azure Active Directory

I den här självstudien får du lära dig att integrera Carbonite Endpoint backup med Azure Active Directory (Azure AD). När du integrerar Carbonite-slutpunktens säkerhets kopiering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Carbonite Endpoint backup.
* Gör det möjligt för användarna att logga in automatiskt till Carbonite-slutpunkten med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Carbonite för slut punkts säkerhets kopiering med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Carbonite för slut punkts säkerhets kopiering stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Lägga till Carbonite för slut punkts säkerhets kopiering från galleriet

Om du vill konfigurera Carbonite-slutpunkten för säkerhets kopiering i Azure AD måste du lägga till Carbonite-slutpunktens säkerhets kopiering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Carbonite Endpoint backup** i sökrutan.
1. Välj **Carbonite-slutpunkt-säkerhetskopiering** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Carbonite Endpoint backup med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i säkerhets kopieringen av Carbonite-slutpunkten.

Slutför följande Bygg stenar om du vill konfigurera och testa Azure AD SSO med Carbonite Endpoint backup:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Carbonite Endpoint backup SSO](#configure-carbonite-endpoint-backup-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Carbonite Endpoint backup test User](#create-carbonite-endpoint-backup-test-user)** -för att få en motsvarighet till B. Simon i Carbonite Endpoint backup som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering för **Carbonite-slutpunkt** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** skriver du en av följande URL: er:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. I text rutan **svars-URL** skriver du en av följande URL: er:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en av följande URL: er:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Carbonite för slut punkts säkerhets kopiering** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurera Carbonite Endpoint backup SSO

1. Om du vill automatisera konfigurationen inom Carbonite-slutpunktens säkerhets kopiering måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations Carbonite slut punkt säkerhets kopiering** dirigerar du till Carbonite-slutpunktsmapparen för program vara. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Carbonite Endpoint backup. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Carbonite-slutpunkten för säkerhets kopiering manuellt öppnar du ett nytt webbläsarfönster och loggar in på din företags webbplats för Carbonite-slutpunkten som administratör och utför följande steg:

4. Klicka på **företaget** i det vänstra fönstret.

    ![Konfiguration av Carbonite-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Klicka på **enkel inloggning**.

    ![Konfiguration av Carbonite-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Klicka på **Aktivera** och klicka sedan på **Redigera inställningar** för att konfigurera.

    ![Konfiguration av Carbonite-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Utför följande steg på sidan Inställningar för **enkel inloggning** :

    ![Konfiguration av Carbonite-slutpunkt ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. I text rutan **namn på identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    1. I text rutan **URL för identitetsprovider** klistrar du in värdet för **inloggnings-URL** , som du har kopierat från Azure Portal.

    1. Klicka på **Välj fil** för att överföra den hämtade **certifikat filen (Base64)** från Azure Portal.

    1. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Carbonite Endpoint backup.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **säkerhets kopiering av Carbonite-slutpunkt**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-carbonite-endpoint-backup-test-user"></a>Skapa Carbonite Endpoint backup test User

1. Logga in på företags platsen för Carbonite-slutpunkten som administratör i ett annat webbläsarfönster.

1. Klicka på **användare** i det vänstra fönstret och klicka sedan på **Lägg till användare**.

    ![Lägg till användare i Carbonite Endpoint backup](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Utför följande steg på sidan **Lägg till användare** :

    ![Lägg till användare i Carbonite Endpoint backup](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Ange **e-post**, **förnamn**, **efter namn** på användaren och ange de behörigheter som krävs för användaren enligt organisationens krav.

    1. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för säkerhets kopiering av Carbonite på åtkomst panelen, bör du loggas in automatiskt till Carbonite-slutpunktens säkerhets kopia som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)