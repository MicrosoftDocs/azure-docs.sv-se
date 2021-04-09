---
title: 'Självstudie: Azure Active Directory integration med SolarWinds Service Desk (tidigare Samanage) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SolarWinds Service Desk (tidigare Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956051"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Självstudie: Azure Active Directory integration med SolarWinds Service Desk (tidigare Samanage)

I den här självstudien får du lära dig hur du integrerar SolarWinds med Azure Active Directory (Azure AD). När du integrerar SolarWinds med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SolarWinds.
* Gör det möjligt för användarna att logga in automatiskt till SolarWinds med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SolarWinds för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SolarWinds stöder **SP** -initierad SSO.

## <a name="add-solarwinds-from-the-gallery"></a>Lägg till SolarWinds från galleriet

Om du vill konfigurera integreringen av SolarWinds i Azure AD måste du lägga till SolarWinds från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SolarWinds** i sökrutan.
1. Välj **SolarWinds** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Konfigurera och testa Azure AD SSO för SolarWinds

Konfigurera och testa Azure AD SSO med SolarWinds med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SolarWinds.

Utför följande steg för att konfigurera och testa Azure AD SSO med SolarWinds:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SOLARWINDS SSO](#configure-solarwinds-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SolarWinds test User](#create-solarwinds-test-user)** -om du vill ha en motsvarighet till B. Simon i SolarWinds som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **SolarWinds** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien. Kontakta [supportteamet för Samanage-klienten](https://www.samanage.com/support) om du behöver mer information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SolarWinds** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SolarWinds.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SolarWinds**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen &quot;standard åtkomst&quot; vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

<a name=&quot;configure-solarwinds-single-sign-on&quot;></a>

## <a name=&quot;configure-solarwinds-sso&quot;></a>Konfigurera SolarWinds SSO

1. Logga in på din SolarWinds-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Instrumentpanel](./media/samanage-tutorial/tutorial-samanage-1.png &quot;Instrumentpanel")

3. Klicka på **Enkel inloggning**.
   
    ![Enkel inloggning](./media/samanage-tutorial/tutorial-samanage-2.png "för Aha!")

4. Gå till avsnittet **Login using SAML** (Logga in via SAML) och utför följande steg:
   
    ![Logga in med SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Logga in med SAML")
 
    a. Klicka på **Enable Single Sign-On with SAML** (Aktivera enkel inloggning med SAML).  
 
    b. I textrutan **Identity Provider URL** (URL för identitetsprovider) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.    
 
    c. Bekräfta att **inloggnings-URL:en** matchar **inloggnings-URL:en** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
 
    d. I textrutan för **utloggnings-URL:en** anger du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.
 
    e. I textrutan **SAML Issuer** (SAML-utfärdare) skriver du in den URI för app-ID som angetts i din identitetsprovider.
 
    f. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **Paste your Identity Provider x.509 Certificate below** (Klistra in x.509-certifikat för din identitetsprovider nedan).
 
    ex. Klicka på **skapa användare om de inte finns i SolarWinds**.
 
    h. Klicka på **Uppdatera**.

### <a name="create-solarwinds-test-user"></a>Skapa SolarWinds test användare

För att Azure AD-användare ska kunna logga in på SolarWinds måste de tillhandahållas i SolarWinds.  
När det gäller SolarWinds är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din SolarWinds företags webbplats som administratör.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Installation](./media/samanage-tutorial/tutorial-samanage-1.png "Konfiguration")

3. Klicka på fliken **användare**
   
    ![Användare](./media/samanage-tutorial/tutorial-samanage-6.png "Användare")

4. Klicka på **Ny användare**.
   
    ![Ny användare](./media/samanage-tutorial/tutorial-samanage-7.png "Ny användare")

5. Skriv **Namn** och **E-postadress** för ett Azure Active Directory-konto som du vill etablera och klicka på **Skapa användare**.
   
    ![Skapa användare](./media/samanage-tutorial/tutorial-samanage-8.png "Skapa användare")
   
   >[!NOTE]
   >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt. Du kan använda andra verktyg för SolarWinds av användar konton eller API: er som tillhandahålls av SolarWinds för att etablera Azure Active Directory användar konton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SolarWinds-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till SolarWinds-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SolarWinds i Mina appar omdirigeras det till SolarWinds-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SolarWinds kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).