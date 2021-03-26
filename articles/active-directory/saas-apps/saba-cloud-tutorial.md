---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Saba-molnet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Saba-molnet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Saba Cloud

I den här självstudien får du lära dig att integrera Saba Cloud med Azure Active Directory (Azure AD). När du integrerar Saba Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Saba-molnet.
* Gör det möjligt för användarna att logga in automatiskt till Saba-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Saba-prenumeration med enkel inloggning (SSO) för molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Saba Cloud stöder **SP-och IDP** -initierad SSO.
* Saba Cloud stöder **just-in-Time** User-etablering.
* Saba Cloud Mobile Application kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="adding-saba-cloud-from-the-gallery"></a>Lägga till Saba-molnet från galleriet

Om du vill konfigurera integreringen av Saba-molnet i Azure AD måste du lägga till Saba Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Saba Cloud** i sökrutan.
1. Välj **Saba Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Konfigurera och testa Azure AD SSO för Saba-moln

Konfigurera och testa Azure AD SSO med Saba-molnet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Saba-molnet.

Utför följande steg för att konfigurera och testa Azure AD SSO med Saba Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Saba Cloud SSO](#configure-saba-cloud-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Saba Cloud test User](#create-saba-cloud-test-user)** – för att få en motsvarighet till B. Simon i Saba-molnet som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.
1. **[Testa SSO för Saba Cloud (mobil)](#test-sso-for-saba-cloud-mobile)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Saba Cloud** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. I text rutan **relä tillstånd** anger du en URL med hjälp av följande mönster: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` eller om SAML har kon figurer ATS för en microsite anger du en URL med hjälp av följande mönster: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Mer information om hur du konfigurerar RelayState finns i [den här](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) länken.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med aktuell identifierare, svars-URL, inloggnings-URL och relä status. Kontakta [Saba Cloud client support team](mailto:support@saba.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Saba Cloud** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Saba-molnet.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Saba Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-saba-cloud-sso"></a>Konfigurera Saba-molnets SSO

1. Logga in på din Saba Cloud Company-webbplats som administratör.
1. Klicka på **meny** ikonen och sedan på **administratör** och fliken **system administratör** .

    ![skärm bild för system administratör](./media/saba-cloud-tutorial/system.png)

1. I **Konfigurera system** väljer du **konfiguration av SAML SSO** och klickar på **Konfigurera SAML SSO** -knappen. 

    ![skärm bild för konfiguration](./media/saba-cloud-tutorial/configure.png)

1. I popup-fönstret väljer du **microsite** i list rutan och klickar på **Lägg till och konfigurera**.

    ![skärm bild för Lägg till plats/microsite](./media/saba-cloud-tutorial/microsite.png)

1. I avsnittet **Konfigurera IDP** klickar du på **Bläddra** för att ladda upp **XML-** filen för federationsmetadata som du har laddat ned från Azure Portal. Markera kryss rutan **SITESPECIFIKA IDP** och klicka på **Importera**.

    ![skärm bild för certifikat import](./media/saba-cloud-tutorial/certificate.png) 

1. I avsnittet **Konfigurera SP** kopierar du värdet för **Entity alias** och klistrar in värdet i text rutan **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal. Klicka på **generera**.

    ![skärm bild för konfigurera SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. I avsnittet **Konfigurera egenskaper** kontrollerar du de ifyllda fälten och klickar på **Spara**. 

    ![skärm bild för konfigurera egenskaper](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Skapa Saba Cloud test User

I det här avsnittet skapas en användare som kallas Britta Simon i Saba-molnet. Saba Cloud stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Saba-molnet skapas en ny efter autentiseringen.

> [!NOTE]
> Information om hur du aktiverar SAML precis i tid för användar etablering med Saba Cloud finns i [den här](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) dokumentationen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Saba Cloud Sign on URL där du kan starta inloggnings flödet.  

* Gå till Saba Cloud Sign-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till det Saba-moln som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Saba Cloud i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du logga in automatiskt till Saba-molnet som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Om inloggnings-URL: en inte är ifylld i Azure AD, behandlas programmet som IDP initierat läge och om inloggnings-URL: en fylls kommer Azure AD alltid att omdirigera användaren till Saba Cloud Application för det initierade flödet av service providern.

## <a name="test-sso-for-saba-cloud-mobile"></a>Testa SSO för Saba Cloud (mobil)

1. Öppna Saba Cloud Mobile Application, ge **plats namnet** i text rutan och klicka på **RETUR**.

    ![Skärm bild för webbplats namn.](./media/saba-cloud-tutorial/site-name.png)

1. Ange din **e-postadress** och klicka på **Nästa**.

    ![Skärm bild för e-postadress.](./media/saba-cloud-tutorial/email-address.png)

1. När inloggningen är klar visas sidan program.

    ![Skärm bild för lyckad inloggning.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Saba-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


