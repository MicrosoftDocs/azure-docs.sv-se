---
title: 'Självstudie: Azure Active Directory integrering med HubSpot | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 3b60ab21bb1e8f5270512d3f1e77aeab2baedc31
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968644"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudie: Azure Active Directory integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (Azure AD). När du integrerar HubSpot med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till HubSpot.
* Gör det möjligt för användarna att logga in automatiskt till HubSpot med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med HubSpot behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En HubSpot-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar HubSpot med Azure AD.

HubSpot stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="adding-hubspot-from-the-gallery"></a>Lägga till HubSpot från galleriet

Om du vill konfigurera integreringen av HubSpot i Azure AD måste du lägga till HubSpot från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **HubSpot** i sökrutan.
1. Välj **HubSpot** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Konfigurera och testa Azure AD SSO för HubSpot

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med HubSpot baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i HubSpot.

Om du vill konfigurera och testa enkel inloggning med HubSpot i Azure AD måste du slutföra följande Bygg stenar:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör att användarna kan använda den här funktionen. |
| **[Konfigurera HubSpot enkel inloggning](#configure-hubspot-single-sign-on)** | Konfigurerar inställningarna för enkel inloggning i programmet. |
| **[Skapa en test användare i Azure AD](#create-an-azure-ad-test-user)** | Testar enkel inloggning i Azure AD för en användare med namnet Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör att Britta Simon kan använda enkel inloggning med Azure AD. |
| **[Skapa en HubSpot-test användare](#create-a-hubspot-test-user)** | Skapar en motsvarighet till Britta Simon i HubSpot som är länkad till användarens Azure AD-representation. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

1. I Azure Portal går du till sidan för program integrering i **HubSpot** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** konfigurerar du *IDP-initierat läge* genom att utföra följande steg:

    1. I rutan **identifierare** anger du en URL som har följande mönster: https: \/ /API.HubSpot.com/login-API/v1/SAML/login?portalId = \<CUSTOMER ID\> .

    1. I rutan **svars-URL** anger du en URL som har följande mönster: https: \/ /API.HubSpot.com/login-API/v1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![Information om enkel inloggning för HubSpot-domän och URL: er](common/idp-intiated.png)

    > [!NOTE]
    > Om du vill formatera URL: erna kan du även se de mönster som visas i fönstret **grundläggande SAML-konfiguration** i Azure Portal.

1. Så här konfigurerar du programmet i *SP-initierat* läge:

    1. Välj **Ange ytterligare URL: er**.

    1. I rutan **inloggnings-URL** anger du **https: \/ /app.HubSpot.com/login**.

    ![Alternativet Ange ytterligare URL: er](common/metadata-upload-additional-signon.png)

1. I avsnittet **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , väljer du **Hämta** bredvid **certifikat (base64)**. Välj ett nedladdnings alternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtnings alternativet för certifikat (base64)](common/certificatebase64.png)

1. I avsnittet **Konfigurera HubSpot** kopierar du följande webb adresser utifrån dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurera HubSpot enkel inloggning

1. Öppna en ny flik i webbläsaren och logga in på ditt HubSpot-administratörs konto.

1. Välj **inställnings** ikonen i det övre högra hörnet på sidan.

    ![Inställnings ikonen i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **standardinställningar för kontot**.

    ![Alternativet konto inställningar i HubSpot](./media/hubspot-tutorial/config2.png)

1. Rulla ned till avsnittet **säkerhet** och välj sedan **Konfigurera**.

    ![Alternativet Konfigurera i HubSpot](./media/hubspot-tutorial/config3.png)

1. I avsnittet **Konfigurera enkel inloggning** utför du följande steg:

    1. I rutan **målgrupps-URL (enhets-ID för Service Provider)** väljer du **Kopiera** för att kopiera värdet. Klistra in värdet i rutan **identifierare** i rutan **grundläggande SAML-konfiguration** i Azure Portal.

    1. I rutan **Logga in URL, ACS, mottagare eller omdirigering** väljer du **Kopiera** för att kopiera värdet. I Azure Portal i fönstret **grundläggande SAML-konfiguration** klistrar du in värdet i rutan **svars-URL** .

    1. I HubSpot, i rutan ID för **identitets leverantör eller utfärdare** , klistrar du in värdet för **Azure AD-identifieraren** som du kopierade i Azure Portal.

    1. I HubSpot, i rutan **identitets leverantörens Sign-On URL** , klistrar du in värdet för **inloggnings-URL** som du kopierade i Azure Portal.

    1. Öppna den certifikat fil (base64) som du laddade ned i Windows Anteckningar. Markera och kopiera innehållet i filen. I HubSpot klistrar du sedan in det i rutan **X. 509-certifikat** .

    1. Välj **Verifiera**.

        ![Avsnittet Konfigurera enkel inloggning i HubSpot](./media/hubspot-tutorial/config4.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till HubSpot.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **HubSpot**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-a-hubspot-test-user"></a>Skapa en HubSpot-test användare

Användaren måste vara etablerad i HubSpot för att Azure AD ska kunna logga in på HubSpot. I HubSpot är etableringen en manuell uppgift.

Så här etablerar du ett användar konto i HubSpot:

1. Logga in på din HubSpot företags webbplats som administratör.

1. Välj **inställnings** ikonen i det övre högra hörnet på sidan.

    ![Inställnings ikonen i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **användare & team**.

    ![Alternativet användare & Teams i HubSpot](./media/hubspot-tutorial/user1.png)

1. Välj **skapa användare**.

    ![Alternativet Skapa användare i HubSpot](./media/hubspot-tutorial/user2.png)

1. I rutan **Lägg till e-addess (ES)** anger du användarens e-postadress i formatet brittasimon \@ contoso.com och väljer sedan **Nästa**.

    ![Rutan Lägg till e-postadress (er) i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user3.png)

1. I avsnittet **skapa användare** väljer du varje flik. Ange de relevanta alternativen och behörigheterna för användaren på varje flik. Välj **Nästa**.

    ![Flikar i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user4.png)

1. Om du vill skicka inbjudan till användaren väljer du **Skicka**.

    ![Alternativet Skicka i HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren aktive ras när användaren har accepterat inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till HubSpot-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till HubSpot-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den HubSpot som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen HubSpot i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på HubSpot som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat HubSpot kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).