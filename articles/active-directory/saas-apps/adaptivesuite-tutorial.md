---
title: 'Självstudie: Azure Active Directory integrering med anpassningsbara insikter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och anpassningsbara insikter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649095"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Självstudie: integrera anpassningsbara insikter med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar anpassningsbara insikter med Azure Active Directory (Azure AD). När du integrerar anpassningsbara insikter med Azure AD kan du:

* Kontroll i Azure AD som har till gång till anpassningsbara insikter.
* Gör det möjligt för användarna att logga in automatiskt till anpassningsbara insikter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Anpassad insikts aktive rad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Anpassningsbara insikter stöder **IDP** -INITIERAd SSO

## <a name="add-adaptive-insights-from-the-gallery"></a>Lägg till anpassningsbara insikter från galleriet

Om du vill konfigurera integrering av anpassningsbara insikter i Azure AD måste du lägga till anpassningsbara insikter från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **adaptiva insikter** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **adaptiva insikter** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Konfigurera och testa Azure AD SSO för anpassningsbara insikter

Konfigurera och testa Azure AD SSO med anpassningsbara insikter med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i anpassningsbara insikter.

Utför följande steg för att konfigurera och testa Azure AD SSO med anpassningsbara insikter:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera adaptiv Insights SSO](#configure-adaptive-insights-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa anpassningsbara insikter-test](#create-adaptive-insights-test-user)** för att få en motsvarighet till B. Simon i anpassningsbara insikter som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **anpassningsbara insikter** -program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Du kan hämta identifierare (enhets-ID) och svars-URL-värden från sidan med **Inställningar för SAML SSO-inställningar** för adaptiva insikter.

4. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera anpassningsbara insikter** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till anpassningsbara insikter.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **adaptiva insikter**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-adaptive-insights-sso"></a>Konfigurera adaptiv Insights SSO

1. Logga in på din anpassade Insights-företags webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **Administration**.

    ![Skärm bild som fokuserar på Administration i navigerings fönstret.](./media/adaptivesuite-tutorial/administration.png "Administratör")

3. I avsnittet **användare och roller** klickar du på **Inställningar för SAML SSO**.

    ![Hantera inställningar för SAML SSO](./media/adaptivesuite-tutorial/settings.png "Hantera inställningar för SAML SSO")

4. Utför följande steg på sidan **Inställningar för SAML SSO** :

    ![Inställningar för SAML SSO](./media/adaptivesuite-tutorial/saml.png "Inställningar för SAML SSO")

    a. I text rutan **namn på identitetsprovider** anger du ett namn för din konfiguration.

    b. Klistra in värdet för **Azure AD-identifieraren** som kopierats från Azure Portal till text rutan för **entitets-ID för identitetsprovider** .

    c. Klistra in värdet för **inloggnings-URL:** en som kopierats från Azure Portal i text rutan för **URL: en för autentiseringsprovider**

    d. Klistra in **URL** -värdet för utloggning som kopierats från Azure Portal till text rutan för **anpassad utloggnings-URL** .

    e. Överför ditt hämtade certifikat genom att klicka på **Välj fil**.

    f. Välj följande för:

     * **SAML-användar-ID**, Välj **användarens anpassade insikter-användar namn**.

     * **SAML-användarens ID-plats**, Välj **användar-ID i NameID för ämnet**.

     * **SAML NameID-format**, Välj **e-postadress**.

     * **Aktivera SAML**, Välj **Tillåt SAML SSO och Direct adaptiv Insights-inloggning**.

    ex. Kopiera **URL: en för adaptiv insikter** och klistra in i text rutorna **identifiera (enhets-ID)** och **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    h. Klicka på **Spara**.

### <a name="create-adaptive-insights-test-user"></a>Skapa anpassningsbara insikter-test användare

Om du vill att Azure AD-användare ska kunna logga in på anpassningsbara insikter måste de vara etablerade i anpassningsbara insikter. Vid anpassningsbara insikter är etableringen en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **anpassade Insights** företags webbplats som administratör.

2. Gå till **Administration**.

   ![Administratör](./media/adaptivesuite-tutorial/administration.png "Administratör")

3. I avsnittet **användare och roller** klickar du på **användare**.

   ![Lägg till användare](./media/adaptivesuite-tutorial/users.png "Lägg till användare")

4. I avsnittet **ny användare** utför du följande steg:

   ![Skicka](./media/adaptivesuite-tutorial/new.png "Skicka")

   a. Ange **namn**, **användar namn**, **e-postadress** och **lösen ord** för en giltig Azure Active Directory användare som du vill etablera i de relaterade text rutorna.

   b. Välj en **roll**.

   c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda andra verktyg för anpassade insikter för att skapa användar konton eller API: er som tillhandahålls av anpassningsbara insikter för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till de anpassningsbara insikter som du konfigurerar SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen adaptiva insikter i Mina appar, bör du loggas in automatiskt på de anpassningsbara insikter som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat anpassningsbara insikter kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).