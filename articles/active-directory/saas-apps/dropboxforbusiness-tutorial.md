---
title: 'Självstudie: Azure Active Directory integrering med Dropbox Business | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox-företag.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 3c1ff9de1057a5ef551ad941965f64907e4243e5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555747"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Självstudie: integrera Dropbox Business med Azure Active Directory

I den här självstudien får du lära dig att integrera Dropbox-företag med Azure Active Directory (Azure AD). När du integrerar Dropbox-företag med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Dropbox-företag.
* Gör det möjligt för användarna att logga in automatiskt till Dropbox Business med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Dropbox Business Single Sign-on (SSO)-prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

* I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Dropbox Business har stöd för **SP** -INITIERAd SSO

* Dropbox Business har stöd för [Automatisk användar etablering och avetablering](dropboxforbusiness-tutorial.md)
* När du har konfigurerat Dropbox kan du framtvinga kontroll över sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Lägga till Dropbox-företag från galleriet

Om du vill konfigurera integrering av Dropbox-företag i Azure AD måste du lägga till Dropbox-företag från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Dropbox Business** i sökrutan.
1. Välj **Dropbox Business** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Dropbox Business med en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Dropbox Business.

Om du vill konfigurera och testa Azure AD SSO med Dropbox Business, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.    
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Dropbox Business SSO](#configure-dropbox-business-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Dropbox Business test User](#create-dropbox-business-test-user)** – om du vill ha en motsvarighet till Britta Simon i Dropbox Business som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **affärs** program integration i Dropbox och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I textrutan **Identifierare (entitets-ID)** anger du ett värde: `Dropbox`

    > [!NOTE]
    > Föregående inloggnings-URL är inte verkligt. Du uppdaterar värdet med den faktiska inloggnings-URL:en, som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Dropbox-företag** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Dropbox-företag.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Dropbox Business**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-dropbox-business-sso"></a>Konfigurera Dropbox Business SSO

1. Om du vill automatisera konfigurationen i Dropbox-företag måste du installera **tillägget Mina appar säker inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **Installera Dropbox Business** för att dirigera dig till affärs programmet för Dropbox. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Dropbox Business. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Dropbox Business manuellt öppnar du ett nytt webbläsarfönster och går till Dropbox-företags klienten och loggar in på Dropbox-företags klienten. och utför följande steg:

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurera enkel inloggning")

4. Klicka på **användarikonen** och välj fliken **Inställningar**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurera enkel inloggning")

5. Klicka på **Administratörskonsol** i den vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurera enkel inloggning")

6. I **Administratörskonsol** klickar du på **Inställningar** i det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurera enkel inloggning")

7. Välj alternativet **Enkel inloggning** under avsnittet **Autentisering**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurera enkel inloggning")

8. I avsnittet **enkel inloggning** utför du följande steg:  

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurera enkel inloggning")

    a. Välj **obligatoriskt** som ett alternativ i list rutan för **enkel inloggning**.

    b. Klicka på **Add sign-in URL** (Lägg till inloggnings-URL), och i textrutan **Identity provider sign-in URL** (Inloggnings-URL för identitetsprovider) klistrar in du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och väljer sedan **Done** (Klart).

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurera enkel inloggning")

    c. Klicka på **Ladda upp certifikat** och bläddra sedan till din **Base64-kodade certifikatfil** som du har laddat ned från Azure-portalen.

    d. Klicka på **Kopiera länk** och klistra in det kopierade värdet i text rutan för **inloggnings-URL** i avsnittet **Dropbox företags domän och URL: er** på Azure Portal.

    e. Klicka på **Spara**.

### <a name="create-dropbox-business-test-user"></a>Skapa Dropbox Business test användare

I det här avsnittet skapas en användare som heter B. Simon i Dropbox Business. Dropbox Business har stöd för just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Dropbox-företag, skapas en ny efter autentiseringen.

>[!Note]
>Kontakta [Dropbox Business client support team](https://www.dropbox.com/business/contact) om du behöver skapa en användare manuellt

### <a name="test-sso"></a>Testa SSO

När du väljer företags panelen för Dropbox på åtkomst panelen, bör du loggas in automatiskt till Dropbox-företaget som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Testa Dropbox-företag med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)