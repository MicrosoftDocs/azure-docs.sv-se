---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Pulse Secure Virtual Traffic Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pulse Secure Virtual Traffic Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Pulse Secure Virtual Traffic Manager

I den här självstudien får du lära dig hur du integrerar Pulse Secure Virtual Traffic Manager med Azure Active Directory (Azure AD). När du integrerar Pulse Secure Virtual Traffic Manager med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Pulse Secure Virtual Traffic Manager.
* Gör det möjligt för användarna att logga in automatiskt för att pulsa säkra virtuella Traffic Manager med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Pulse Secure Virtual Traffic Manager-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Pulse Secure Virtual Traffic Manager stöder **SP** -INITIERAd SSO

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Lägga till en säker puls för virtuella Traffic Manager från galleriet

Du måste lägga till en säker virtuell Traffic Manager från galleriet till din lista över hanterade SaaS-appar för att kunna konfigurera integreringen av det säkra virtuella puls Traffic Manager i Azure AD.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Pulse Secure Virtual Traffic Manager** i sökrutan.
1. Välj **Pulse Secure Virtual Traffic Manager** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Konfigurera och testa Azure AD SSO for Pulse Secure Virtual Traffic Manager

Konfigurera och testa Azure AD SSO med Pulse Secure Virtual Traffic Manager med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i det säkra virtuella puls-Traffic Manager.

Utför följande steg för att konfigurera och testa Azure AD SSO med Pulse Secure Virtual Traffic Manager:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. Konfigurera enkel inloggnings inställningar på program sidan genom att **[Konfigurera Pulse Secure Virtual Traffic Manager SSO](#configure-pulse-secure-virtual-traffic-manager-sso)** .
    1. **[Skapa Pulse Secure virtual Traffic Manager test User](#create-pulse-secure-virtual-traffic-manager-test-user)** -om du vill ha en motsvarighet till B. Simon i det säkra virtuella puls-Traffic Manager som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **säker virtuell Traffic Manager** program integrering i pulsen och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<published virtual server FQDN>/saml/consume`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<published virtual server FQDN>/saml/metadata`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [pulsen för det säkra virtuella Traffic Manager klient support teamet](mailto:support@pulsesecure.net) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Pulse Secure Virtual Traffic Manager** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till en säker virtuell Traffic Manager.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Pulse Secure Virtual Traffic Manager**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Konfigurera Pulse Secure Virtual Traffic Manager SSO

I det här avsnittet beskrivs den konfiguration som krävs för att aktivera Azure AD SAML-autentisering på den virtuella pulsen Traffic Manager. Alla konfigurations ändringar görs på den virtuella pulsen Traffic Manager med administratörs webb gränssnittet. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Skapa en SAML-betrodd identitets leverantör

a. Gå till den **virtuella Traffic Manager utrustnings administratörs användar gränssnitt > katalog > SAML > katalogen för betrodda identitets leverantörer** och klicka på **Redigera**.

![Sidan SAML-kataloger](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Lägg till information för den nya SAML Trusted Identity-providern, kopiera informationen från Azure AD Enterprise-programmet på sidan Inställningar för enkel inloggning och klicka sedan på **Skapa ny betrodd identitetsprovider**.

![Skapa ny betrodd identitetsprovider](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* I text rutan **namn** anger du ett namn för den betrodda identitets leverantören. 

* I text rutan **Entity_id** anger du det ID-värde för **Azure AD** som du har kopierat från Azure Portal.  

* I text rutan **URL** anger du **inloggnings-URL** -värdet som du har kopierat från Azure Portal. 

* Öppna det hämtade **certifikatet** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** .

c. Verifiera att den nya SAML Identity-providern har skapats. 

![Verifiera betrodd identitetsprovider](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Konfigurera den virtuella servern för att använda Azure AD-autentisering

a. Gå till sidan för **Administratörs gränssnittet för virtuella Traffic Manager utrustnings administratörer > tjänster > virtuella servrar** och klicka på **Redigera** bredvid den virtuella servern som skapats tidigare.

![Redigera virtuella servrar](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. I avsnittet **autentisering** klickar du på **Redigera**. 

![Avsnittet Authentication](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Konfigurera följande autentiseringsinställningar för den virtuella servern: 

1. Anspråksautentisering

    ![autentiseringsinställningar för virtuell server](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. I **auth!-typen** väljer du **SAML-tjänstprovider** 

    b. I **auth! verbose** ställer du in på "Ja" för att felsöka eventuella autentiseringsproblem, annars lämnar du standardvärdet som "nej" 

2. Hantering av autentiserings-session –

    ![Hantering av autentiserings-session](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Lämna standard som "VS_SamlSP_Auth" för **auth! session! cookie_name** 

    b. För **auth! session! timeout**, lämna standardvärdet till "7200" 

    c. I **auth! session! log_external_state** anger du till "Ja" för att felsöka eventuella autentiseringsproblem, annars lämnar du standardvärdet som "nej" 

    d. I **auth! session! cookie_attributes** ändrar du till "HTTPOnly" 

3. SAML-tjänstprovider –

    ![SAML-tjänstprovider](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. I text rutan **auth! SAML! sp_entity_id** anger du samma URL som används som konfigurations identifierare för enkel inloggning i Azure AD (enhets-ID). Som `https://pulseweb.labb.info/saml/metadata` . 

    b. I **autentiseringen auth! SAML! sp_acs_url** anger du samma URL som används som Azure AD-konfiguration för enkel inloggning i Azure AD-URL (intyg om försäkrans konsument tjänst). Som `https://pulseweb.labb.info/saml/consume` . 

    c. I **auth! SAML!-IDP** väljer du den **betrodda identitets leverantör** som du skapade i föregående steg. 

    d. I auth! SAML! time_tolerance lämnar du standardvärdet 5 sekunder. 

    e. I auth! SAML! nameid_format väljer du **ospecificerad**.

    f. Tillämpa ändringarna genom att klicka på **Uppdatera** längst ned på sidan.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Skapa en puls för säker virtuell Traffic Manager-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Pulse Secure Virtual Traffic Manager. Arbeta med den [säkra virtuella Traffic Manager Support teamet för Pulse](mailto:support@pulsesecure.net) för att lägga till användare i den säkra virtuella pulsen Traffic Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till en säker virtuell Traffic Manager inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till Pulse Secure virtuell Traffic Manager inloggnings-URL direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen för säker virtuell puls Traffic Manager på åtkomst panelen omdirigeras det till den säkra virtuella Traffic Manager inloggnings-URL: en Pulse. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Pulse Secure Virtual Traffic Manager kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).