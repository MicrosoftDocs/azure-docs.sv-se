---
title: 'Självstudie: Azure Active Directory integration med SAP Business ByDesign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: e8f1bff50c5f163894392a9e5d08cc8451d835fa
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896417"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Självstudier: Azure Active Directory-integration med SAP Business ByDesign

I den här självstudien får du lära dig hur du integrerar SAP Business ByDesign med Azure Active Directory (Azure AD). När du integrerar SAP Business ByDesign med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Business ByDesign.
* Gör det möjligt för användarna att logga in automatiskt till SAP Business ByDesign med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP Business ByDesign-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAP Business ByDesign stöder **SP** -INITIERAd SSO

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Lägg till SAP Business ByDesign från galleriet

Om du vill konfigurera integreringen av SAP Business ByDesign i Azure AD måste du lägga till SAP Business ByDesign från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SAP Business ByDesign** i sökrutan.
1. Välj **SAP Business ByDesign** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med SAP Business ByDesign med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP Business ByDesign.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAP Business ByDesign:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SAP Business BYDESIGN SSO](#configure-sap-business-bydesign-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa en SAP Business ByDesign-test](#create-sap-business-bydesign-test-user)** för att få en motsvarighet till Britta Simon i SAP Business ByDesign som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **SAP Business ByDesign** , letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [SAP Business ByDesign client support team](https://www.sap.com/products/cloud-analytics.support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I SAP Business ByDesign-programmet förväntas SAML-kontroller i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image1](common/edit-attribute.png)

6. Klicka på **redigerings** ikonen för att redigera **namnet på namn identifieraren**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. I avsnittet **hantera användar anspråk** utför du följande steg:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Välj **omvandling** som **källa**.

    b. I list rutan **omvandling** väljer du **ExtractMailPrefix ()**.

    > [!NOTE]
    > Per standard-ByD används NameID-formatet **Ospecificerat** för användar mappning. ByD mappar NameID för SAML-intyg i användar Ali Aset ByD. Dessutom stöder ByD namn-ID-formatet **EmailAddress**. I det här fallet ByD mappar NameID för SAM-försäkran på ByD-användarens e-postadress för de anställdas kontakt data i ByD. För mer information kan du se [SAP-bloggen](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. I avsnittet **Konfigurera SAP Business ByDesign** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Business ByDesign.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAP Business ByDesign**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.

1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-business-bydesign-sso"></a>Konfigurera SAP Business ByDesign SSO

1. Logga in på din SAP Business ByDesign-Portal med administratörs behörighet.

2. Gå till **program-och användar hantering, gemensam uppgift** och klicka på fliken **identitets leverantör** .

3. Klicka på **ny identitetsprovider** och välj XML-filen med metadata som du laddade ned från Azure Portal. Genom att importera metadata överför systemet automatiskt de nödvändiga signatur- och krypteringscertifikaten.

    ![Konfigurera enskilda Sign-On1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Om du vill ta med **URL: en för intygad konsument tjänst** till SAML-begäran väljer du **ta med URL för intygad konsument tjänst**.

5. Klicka på **Aktivera enkel inloggning**.

6. Spara ändringarna.

7. Klicka på fliken **Mina System**.

    ![Konfigurera enskilda Sign-On2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. I text rutan för **inloggnings-URL i Azure AD** klistrar du in **inloggnings-URL** -värde, som du har kopierat från Azure Portal.

    ![Konfigurera enskilda Sign-On3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Ange om medarbetaren manuellt kan välja mellan att logga in med användar-ID och lösen ord eller SSO genom att välja **manuellt val av identitets leverantör**.

10. I avsnittet **SSO URL** anger du den URL som ska användas av den anställda till inloggning i systemet.
    I list rutan URL som skickats till anställd kan du välja mellan följande alternativ:

    **Inte URL för enkel inloggning**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren kan inte inloggning med SSO och måste använda lösen ord eller certifikat i stället.

    **URL FÖR ENKEL INLOGGNING**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren kan inloggning med enkel inloggning. Begäran om autentisering dirigeras via IDP:n.

    **Automatiskt val**

    Om enkel inloggning inte ar aktivt skickar systemet bara den vanliga system-URL:en till den anställde. Om enkel inloggning är aktivt kontrollerar systemet om den anställde har ett lösenord. Om ett lösenord är tillgängligt skickas både URL:en för enkel inloggning och URL:en för inloggning utan enkel inloggning till den anställde. Om den anställde inte har något lösenord skickas bara URL:en för enkel inloggning till den anställde.

11. Spara ändringarna.

### <a name="create-sap-business-bydesign-test-user"></a>Skapa test användare för SAP Business ByDesign

I det här avsnittet skapar du en användare som heter Britta Simon i SAP Business ByDesign. Arbeta med [SAP Business ByDesign client support team](https://www.sap.com/products/cloud-analytics.support.html) för att lägga till användare i SAP Business ByDesign-plattformen. 

> [!NOTE]
> Se till att NameID-värdet ska överensstämma med fältet username i SAP Business ByDesign-plattformen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAP Business ByDesign-inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till SAP Business ByDesign-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Mina appar. När du klickar på panelen SAP Business ByDesign i Mina appar omdirigeras den till SAP Business ByDesign-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

* När du har konfigurerat SAP Business-ByDesign kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
