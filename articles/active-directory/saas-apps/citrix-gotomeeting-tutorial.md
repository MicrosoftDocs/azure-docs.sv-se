---
title: 'Självstudie: Azure Active Directory integrering med GoToMeeting | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: b410c4a106f65c8913dc41f5957cdaf10cad3c89
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551242"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med GoToMeeting

I den här självstudien får du lära dig hur du integrerar GoToMeeting med Azure Active Directory (Azure AD). När du integrerar GoToMeeting med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till GoToMeeting.
* Gör det möjligt för användarna att logga in automatiskt till GoToMeeting med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* GoToMeeting för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* GoToMeeting stöder **IDP** initierad SSO.
* När du har konfigurerat GoToMeeting kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-gotomeeting-from-the-gallery"></a>Lägga till GoToMeeting från galleriet

För att konfigurera integreringen av GoToMeeting till Azure AD behöver du lägga till GoToMeeting från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **GoToMeeting** i sökrutan.
1. Välj **GoToMeeting** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-gotomeeting"></a>Konfigurera och testa enkel inloggning med Azure AD för GoToMeeting

Konfigurera och testa Azure AD SSO med GoToMeeting med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i GoToMeeting.

Om du vill konfigurera och testa Azure AD SSO med GoToMeeting, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GOTOMEETING SSO](#configure-gotomeeting-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa GoToMeeting test User](#create-gotomeeting-test-user)** -om du vill ha en motsvarighet till B. Simon i GoToMeeting som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **GoToMeeting** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://authentication.logmeininc.com/saml/sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://authentication.logmeininc.com/saml/acs`

    c. Klicka på **ange ytterligare URL:er** och konfigurera nedanstående URL:er

    d. **Inloggnings-URL** (behåll detta tomt)

    e. I textrutan **RelayState** skriver du en URL med följande mönster:

   - För GoToMeeting-appen använder du `https://global.gotomeeting.com`

   - För GoToTraining använder du `https://global.gototraining.com`

   - För GoToWebinar använder du `https://global.gotowebinar.com` 

   - För GoToAssist använder du `https://app.gotoassist.com`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för GoToMeeting-klienten](https://go.microsoft.com/fwlink/?linkid=845985). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera GoToMeeting** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL


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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till GoToMeeting.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **GoToMeeting**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-gotomeeting-sso"></a>Konfigurera GoToMeeting SSO

1. I ett annat webbläsarfönster loggar du in på ditt [GoToMeeting-organisationscenter](https://organization.logmeininc.com/). Du uppmanas att bekräfta att IdP har uppdaterats.

2. Markera kryssrutan ”My Identity Provider has been updated with the new domain” (Min identitetsprovider har uppdaterats med den nya domänen). Klicka på **Klar** när du är klar.

### <a name="create-gotomeeting-test-user"></a>Skapa GoToMeeting-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i GoToMeeting. GoToMeeting stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i GoToMeeting skapas en ny när du försöker komma åt GoToMeeting.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för GoToMeeting](https://support.logmeininc.com/gotomeeting).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på GoToMeeting-panelen i åtkomstpanelen bör du automatiskt loggas in på GoToMeeting som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova GoToMeeting med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du GoToMeeting med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
