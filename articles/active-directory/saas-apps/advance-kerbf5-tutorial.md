---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning med F5 | Microsoft Docs'
description: I den här artikeln får du lära dig de steg som du måste utföra för att integrera F5 med Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: db8977e484e8d1f2cf4b30427d47ba45969f2147
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654467"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Självstudie: Azure Active Directory (AD) integrering med enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör det möjligt för användarna att logga in automatiskt på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* F5-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

F5 stöder **SP-och IDP** -initierad SSO.

F5 SSO kan konfigureras på tre olika sätt:

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integrering av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **F5** i sökrutan.
1. Välj **F5** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa enkel inloggning med Azure AD för F5

Konfigurera och testa Azure AD SSO med F5 med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i F5.

Om du vill konfigurera och testa Azure AD SSO med F5 slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera F5 – SSO](#configure-f5-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa F5 test användare](#create-f5-test-user)** – om du vill ha en motsvarighet till B. Simon i F5 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **F5** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<YourCustomFQDN>.f5.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5-klientens support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera F5** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till F5.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **F5**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurera enkel inloggning med F5 för avancerat Kerberos-program

1. Öppna ett nytt webbläsarfönster och logga in på din plats med F5 (avancerad Kerberos) som administratör och utför följande steg:

1. Du måste importera certifikatet för metadata till F5 (avancerad Kerberos) som ska användas senare i installations processen. Gå till **System > certifikat hantering > hantering av trafik certifikat >> SSL-certifikat lista**. Klicka på **Importera** till höger hörn.

    ![Skärm bild som visar knappen Importera för att importera metadata-certifikatet.](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Om du vill konfigurera SAML-IDP går du till **åtkomst > Federation > SAML Service Provider > skapa > från metadata**.

    ![Skärm bild som visar hur du skapar SAML-IDP från metadata.](./media/advance-kerbf5-tutorial/configure02.png)

    ![Skärm bild som visar skärmen för att skapa en ny SAML IdP-anslutning.](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure04.png)

    ![Skärm bild som visar skärmen för enkel inloggning på tjänst inställningar. ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Ange det certifikat som laddats upp från uppgift 3

    ![Skärm bild som visar skärmen Redigera SAML-IdP anslutning.](./media/advance-kerbf5-tutorial/configure06.png)

    ![Skärm bild som visar skärmen Inställningar för tjänst inställningar för enkel utloggning.](./media/advance-kerbf5-tutorial/configure07.png)

 1. Om du vill konfigurera SAML SP går du till **åtkomst > federation > SAML service Federation > Local SP Services > skapa**.

    ![Skärm bild som visar skärmen där du skapar en lokal SP-tjänst.](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klicka på **OK**.

1. Välj SP-konfigurationen och klicka på **BIND/UnBind IDP-kopplingar**.

     ![Skärm bild som visar SAML-tjänstprovidern.](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klicka på **Lägg till ny rad** och välj den **externa IDP-anslutning** som skapades i föregående steg.

    ![Skärm bild som visar knappen Lägg till ny rad.](./media/advance-kerbf5-tutorial/configure10.png)
 
1. För att konfigurera Kerberos SSO, **åtkomst > enkel inloggning > Kerberos**

    >[!Note]
    >Du behöver kontot för Kerberos-delegering för att kunna skapas och anges. Se avsnittet KCD (se tillägg för variabel referenser)

    • Användar namn källa  `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Källa för användar sfär  `session.logon.last.domain`

    ![Skärm bild som fokuserar på åtkomst > enkel inloggning.](./media/advance-kerbf5-tutorial/configure11.png)

1. För att konfigurera åtkomst profil, **åtkomst > profil/principer > åtkomst profil (per sessionstillstånd)**.

    ![Skärm bild som visar fliken Egenskaper under meny alternativet profiler/principer.](./media/advance-kerbf5-tutorial/configure12.png)

    ![Skärm bild som visar fliken SSO/auth-domäner.](./media/advance-kerbf5-tutorial/configure13.png)

    ![Skärm bild som visar fliken åtkomst princip.](./media/advance-kerbf5-tutorial/configure14.png)

    ![Skärm bild som visar fliken Egenskaper i åtkomst principen.](./media/advance-kerbf5-tutorial/configure15.png)

    ![Skärm bild som visar egenskaperna för variabel tilldelning.](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session. logon. senaste. usernameUPN uttryck {[mcget {session. SAML. Last. Identity}]}

    * session. AD. lastactualdomain TEXT superdemo.live

    ![Skärm bild som visar AD-frågeegenskaper.](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {session. logon. senaste. usernameUPN})

    ![Skärm bild som visar fliken förgrenings regler och regel för att kontrol lera konto.](./media/advance-kerbf5-tutorial/configure18.png)

    ![Skärm bild som visar text rutorna anpassad variabel och anpassad uttryck.](./media/advance-kerbf5-tutorial/configure19.png)

    * session. logon. senaste. username-uttryck {"[mcget {session. AD. Last. attr. sAMAccountName}]"}

    ![Skärm bild som visar värdena i fälten SSO token-namn och lösen ord för SSO-token.](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session. logo. senaste. username}
    * mcget {session. logo. senaste. password}

1. För att lägga till en ny nod går du till **lokal trafik > noder > Node List > +**.

    ![Skärm bild som markerar lokala trafik > noder.](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Om du vill skapa en ny pool går du till **lokal trafik > pooler > pool lista > skapa**.

     ![Skärm bild som visar lokal trafik > pooler.](./media/advance-kerbf5-tutorial/configure22.png)

 1. Om du vill skapa en ny virtuell server går du till **lokal trafik > virtuella servrar > Virtual Server List > +**.

    ![Skärm bild som visar lokal trafik > virtuella servrar.](./media/advance-kerbf5-tutorial/configure23.png)

1. Ange åtkomst profilen som skapades i föregående steg.

    ![Skärm bild som visar var du anger den åtkomst profil som du skapade.](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Konfigurera Kerberos-delegering 

>[!Note]
>Mer information finns [här](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Steg 1: skapa ett Delegerings konto**

    * Exempel
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Steg 2: ange SPN (på kontot för APM-delegering)**

    *  Exempel
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Steg 3: SPN-delegering (för det App Service kontot)**

    * Konfigurera lämplig delegering för F5-Delegerings kontot.
    * I exemplet nedan konfigureras APM Delegerings kontot för KCD för FRP-App1.superdemo.live-appen.

        ![Skärm bild som visar Delegatio för APM-konto egenskaper > delegering.](./media/advance-kerbf5-tutorial/configure25.png)

1. Ange de uppgifter som anges i referens dokumentet ovan under [detta](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Bilaga – SAML – F5 BIG-IP-variabel mappningar som visas nedan:

    ![Skärm bild som visar fliken Översikt > aktiva sessioner.](./media/advance-kerbf5-tutorial/configure26.png)

    ![Skärm bild som visar variabler och sessionsnycklar.](./media/advance-kerbf5-tutorial/configure27.png) 

1. Nedan visas en fullständig lista över SAML-standardattribut som är standard. GivenName representeras med hjälp av följande sträng.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Session | Attribut |
| -- | -- |
| eb46b6b6. session. SAML. Last. assertionID | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. assertionIssueInstant  | `<ID>` |
| eb46b6b6. session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Identity/Claims/DisplayName | user0 |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Identity/Claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Identity/Claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Identity/Claims/tenantid | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/WS/2005/05/Identity/Claims/EmailAddress | `user0@superdemo.live` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/WS/2005/05/Identity/Claims/givenName | user0 |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/WS/2005/05/Identity/Claims/Name | `user0@superdemo.live` |
| eb46b6b6. session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/WS/2005/05/Identity/Claims/Surname | 0 |
| eb46b6b6. session. SAML. Last. Audience | `https://kerbapp.superdemo.live` |
| eb46b6b6. session. SAML. Last. authNContextClassRef | urn: Oasis: namn: TC: SAML: 2.0: AC: klasser: lösen ord |
| eb46b6b6. session. SAML. Last. authNInstant | `<ID>` |
| eb46b6b6. session. SAML. senaste. identitet | `user0@superdemo.live` |
| eb46b6b6. session. SAML. Last. inResponseTo | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. nameIDValue | `user0@superdemo.live` |
| eb46b6b6. session. SAML. Last. nameIdFormat | urn: Oasis: Names: TC: SAML: 1.1: NameID-format: emailAddress |
| eb46b6b6. session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. session. SAML. Last. responseId | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. responseIssueInstant | `<ID>` |
| eb46b6b6. session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. session. SAML. Last. result | 1 |
| eb46b6b6. session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. session. SAML. Last. sessionIndex | `<TENANT ID>` |
| eb46b6b6. session. SAML. Last. statusValue | urn: Oasis: namn: TC: SAML: 2.0: status: lyckad |
| eb46b6b6. session. SAML. Last. subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6. session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. session. SAML. Last. subjectConfirmMethod | urn: Oasis: Names: TC: SAML: 2.0: cm: Bearer |
| eb46b6b6. session. SAML. Last. validityNotBefore | `<ID>` |
| eb46b6b6. session. SAML. Last. validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Skapa F5-test användare

I det här avsnittet skapar du en användare som heter B. Simon i F5. Arbeta med [F5-klient support teamet](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna på F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på F5-panelen i åtkomst panelen, bör du loggas in automatiskt på F5 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [F5 BIG-IP APM och Azure AD-integrering för säker hybrid åtkomst](../manage-apps/f5-aad-integration.md)

- [Självstudie för att distribuera F5 stor IP Virtual Edition VM i Azure IaaS för säker hybrid åtkomst](../manage-apps/f5-bigip-deployment-guide.md)

- [Självstudie för Azure Active Directory integration med enkel inloggning med F5 BIG-IP för lösen ords mindre VPN](../manage-apps/f5-aad-password-less-vpn.md)