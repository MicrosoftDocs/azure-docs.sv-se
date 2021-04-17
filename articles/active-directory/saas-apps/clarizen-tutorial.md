---
title: 'Självstudie: Azure Active Directory integrering med Clarizen One | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clarizen One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516949"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Självstudie: Azure Active Directory integrering med Clarizen One

I den här självstudien lär du dig att integrera Clarizen One med Azure Active Directory (Azure AD). När du integrerar Clarizen One med Azure AD kan du:

* Kontrollera vem som har åtkomst till Clarizen One från Azure AD.
* Gör så att dina användare automatiskt loggas in på Clarizen One med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Clarizen One-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Clarizen One stöder **IDP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-clarizen-one-from-the-gallery"></a>Lägga till Clarizen One från galleriet

För att konfigurera integreringen av Clarizen One i Azure AD behöver du lägga till Clarizen One från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Clarizen One** i sökrutan.
1. Välj **Clarizen One** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Konfigurera och testa Azure AD SSO för Clarizen One

Konfigurera och testa Azure AD SSO med Clarizen One med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Clarizen One.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Clarizen One:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Clarizen One SSO](#configure-clarizen-one-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Clarizen-testanvändare](#create-clarizen-one-test-user)** – för att ha en motsvarighet för B.Simon i Clarizen One som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal på sidan för **Clarizen One-programintegrering** går du till **avsnittet Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    a. I **textrutan** Identifierare skriver du värdet: `Clarizen`

    b. I **textrutan Svars-URL** skriver du url:en: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I **avsnittet Konfigurera Clarizen One** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Clarizen One.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Clarizen One**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen &quot;Standardåtkomst&quot;.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name=&quot;configure-clarizen-one-sso&quot;></a>Konfigurera Clarizen One SSO

1. I ett annat webbläsarfönster loggar du in på din Clarizen One-företagswebbplats som administratör.

1. Klicka på ditt användarnamn och klicka sedan på **Inställningar**.

    ![Klicka på Inställningar under ditt användarnamn](./media/clarizen-tutorial/setting.png &quot;Inställningar")

1. Klicka på **fliken Globala** inställningar. Klicka sedan på Redigera bredvid  **Federerad autentisering.**

    ![Fliken Globala inställningar](./media/clarizen-tutorial/authentication.png "Globala inställningar")

1. I dialogrutan **Sammansluten autentisering** utför du följande steg:

    ![Dialogrutan "Federerad autentisering"](./media/clarizen-tutorial/federated-authentication.png "Federerad autentisering")

    a. Välj **Aktivera sammansluten autentisering**.

    b. Klicka på **Ladda upp** för att ladda upp det nedladdade certifikatet.

    c. I rutan **Inloggnings-URL** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. I rutan **Utloggnings-URL** anger du värdet för **utloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    e. Välj **Använd POST**.

    f. Klicka på **Spara**.

### <a name="create-clarizen-one-test-user"></a>Skapa Clarizen One-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Clarizen One.

**Om du behöver skapa användare manuellt så gör du följande:**

Om du vill göra det möjligt för Azure AD-användare att logga in på Clarizen One måste du etablera användarkonton. När det gäller Clarizen One är etablering en manuell uppgift.

1. Logga in på din Clarizen One-företagswebbplats som administratör.

2. Klicka på **Personer**.

    ![Klicka på "Personer"](./media/clarizen-tutorial/people.png "People")

3. Klicka på **Bjud in användare**.

    ![Knappen Bjud in användare](./media/clarizen-tutorial/user.png "Bjud in användare")

1. I dialogrutan **Bjud in personer** utför du följande steg:

    ![Dialogrutan Bjud in personer](./media/clarizen-tutorial/invite-people.png "Bjud in personer")

    a. I rutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    b. Klicka på **Bjud in**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ.

* Klicka på Testa det här Azure Portal så bör du automatiskt loggas in på Clarizen One som du har ställt in enkel inloggning för.

* Du kan använda Microsoft Mina appar. När du klickar på Clarizen One-panelen i Mina appar bör du automatiskt loggas in på Clarizen One som du har ställt in enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Clarizen One kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).