---
title: 'Självstudie: Azure Active Directory integrering med Gigya | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516115"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Självstudie: Azure Active Directory integrering med Gigya

I den här självstudien lär du dig att integrera Gigya med Azure Active Directory (Azure AD). När du integrerar Gigya med Azure AD kan du:

* Styra vem som har åtkomst till Gigya från Azure AD.
* Gör så att dina användare automatiskt loggas in på Gigya med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Gigya-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Gigya har stöd för **SP-initierad** enkel inloggning.

## <a name="add-gigya-from-the-gallery"></a>Lägga till Gigya från galleriet

För att konfigurera integreringen av Gigya i Azure AD måste du lägga till Gigya från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Gigya** i sökrutan.
1. Välj **Gigya** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Konfigurera och testa Azure AD SSO för Gigya

Konfigurera och testa enkel inloggning i Azure AD med Gigya med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Gigya.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Gigya:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Gigya SSO](#configure-gigya-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Gigya-testanvändare](#create-gigya-test-user)** – för att ha en motsvarighet för B.Simon i Gigya som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för Gigya** hittar du **avsnittet Hantera** och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `http://<companyname>.gigya.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Gigya** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Gigya.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I listan med program väljer du **Gigya**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen &quot;Standardåtkomst&quot;.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name=&quot;configure-gigya-sso&quot;></a>Konfigurera Gigya SSO

1. Logga in på Gigya-företagswebbplatsen som administratör.

2. Gå till **Inställningar \>SAML-inloggning** och klicka sedan på knappen **Lägg till**.
   
    ![SAML-inloggning](./media/gigya-tutorial/login.png &quot;SAML-inloggning")

3. Gå till avsnittet **SAML-inloggning** och utför följande steg:
   
    ![SAML-konfiguration](./media/gigya-tutorial/configuration.png "SAML-konfiguration")
   
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.
   
    b. I **textrutan** Issuer (Utfärdare) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure Portal. 
   
    c. I **textrutan Single Sign-On Service URL (URL** för enkel inloggningstjänst) klistrar du in värdet för den inloggnings-URL som du har kopierat från Azure Portal. 
   
    d. I **textrutan Name ID Format** (Namn-ID-format) klistrar du in värdet för det namnidentifierarformat som du har kopierat från Azure Portal. 
   
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **X.509 Certificate** (X.509-certifikat).
   
    f. Klicka **på Spara inställningar.**

## <a name="create-gigya-test-user"></a>Skapa Gigya-testanvändare

För att Azure AD-användare ska kunna logga in i Gigya måste de etableras till Gigya. I Gigya görs etablering manuellt.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla ett användarkonto:

1. Logga in på **Gigya**-företagswebbplatsen som administratör.

2. Gå till **Admin \> Hantera användare** och klicka sedan på **Bjud in användare**.
   
    ![Hantera användare](./media/gigya-tutorial/users.png "Hantera användare")

3. Gör följande i dialogrutan Bjud in användare:
   
    ![Bjud in användare](./media/gigya-tutorial/invite-user.png "Bjud in användare")
   
    a. I textrutan **E-post** skriver du e-postalias för ett giltigt Azure Active Directory-konto som du vill etablera.
    
    b. Klicka på **Bjud in användare**.
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    > 

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Gigyas inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Gigya Inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Gigya-panelen i Mina appar omdirigeras detta till Gigyas inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Gigya kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
