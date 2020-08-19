---
title: 'Självstudie: Azure Active Directory integration med bedömning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appraisd.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 2b8e8a6df2fc355ad6a8ef50d801dc7457e97c63
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547791"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Självstudie: integrera med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar en bedömning med Azure Active Directory (Azure AD). När du integrerar en bedömning med Azure AD kan du:

* Kontroll i Azure AD som har till gång till en bedömning.
* Gör det möjligt för användarna att logga in automatiskt för att bedöma sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Den uppdelade prenumerationen för enkel inloggning (SSO) har Aktiver ATS.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Uppvärderad stöder **SP-och IDP** -initierad SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Lägga till Appraisd från galleriet

För att konfigurera integrering av Appraisd i Azure AD måste du lägga till Appraisd från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **bedömas** i sökrutan.
1. Välj **bedömas** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med en bedömning med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i en bedömning.

Om du vill konfigurera och testa Azure AD SSO med en bedömning slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera bedömning](#configure-appraisd)** för att konfigurera SSO-inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. Skapa en förväntad **[test användare](#create-appraisd-test-user)** för att få en motsvarighet till B. Simon i en bedömning som är kopplad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **bedömning** av program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen Spara och utföra följande steg:

    a. Klicka på **Ange ytterligare URL:er**.

    b. Skriv en URL i textrutan **Vidarebefordransstatus**: `<TENANTCODE>`

    c. Om du vill konfigurera programmet i **SP**-initierat läge skriver du en URL med hjälp av följande mönster i textrutan **Inloggnings-URL**: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Du får det faktiska värdet för inloggnings-URL och värdet för vidarebefordransstatus på konfigurationssidan för Appraisd SSO som beskrivs senare i självstudien.

1. Ett bedömt program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Ett bedömt program förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera bedömning** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurera en bedömning

1. Om du vill automatisera konfigurationen i en bedömning måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på den uppfyllde **installationen** som leder dig till det uppvärderade programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på en bedömning. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera en bedömning manuellt öppnar du ett nytt webbläsarfönster och loggar in på den uppvärderade företags platsen som administratör och utför följande steg:

4. Längst upp till höger på sidan klickar du på ikonen **Inställningar** och navigerar sedan till **konfiguration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. På vänster sida av menyn klickar du på **Enkel inloggning med SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. På sidan för **konfiguration av enkel inloggning för SAML 2.0** utför du följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiera det **förvalda värdet för relä status** och klistra in det i text rutan i **Relay-tillstånd** i **grundläggande SAML-konfiguration** på Azure Portal.

    b. Kopiera värdet för **inloggnings-URL för tjänsten som initieras** och klistra in det i text rutan för **inloggnings-URL** i **grundläggande SAML-konfiguration** på Azure Portal.

7. Rulla nedåt på samma sida under **Identifiera användare** och utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och klickar på **Spara**.

    b. I text rutan för **identitets leverantörens URL-adress** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal och klickar på **Spara**.

    c. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i rutan **X. 509-certifikat** och klickar på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till en bedömning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan med program väljer **Appraisd**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-appraisd-test-user"></a>Skapa Appraisd-testanvändare

För att Azure AD-användare ska kunna logga in på Appraisd måste de etableras till Appraisd. I Appraisd är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Appraisd som administratör.

2. Klicka på ikonen **Inställningar** längst upp till höger på sidan och gå sedan till **administrations centret**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. I verktygsfältet högst upp på sidan klickar du på **personer**och navigerar sedan för att **lägga till en ny användare**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Utför följande steg i rutan **Lägg till en ny användare**:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **Britta**.

    b. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    c. I textrutan **E-post** anger du användarens e-postadress som `B. Simon@contoso.com`.

    d. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer den uppfattade panelen i åtkomst panelen, bör du loggas in automatiskt till den som du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
