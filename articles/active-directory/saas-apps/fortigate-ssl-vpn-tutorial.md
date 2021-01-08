---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med FortiGate SSL VPN | Microsoft Docs'
description: Lär dig de steg du behöver utföra för att integrera FortiGate SSL VPN med Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: b9a22025f124e7639aa1b9a157dbbd020e2ff966
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020272"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med FortiGate SSL VPN

I den här självstudien får du lära dig hur du integrerar FortiGate SSL VPN med Azure Active Directory (Azure AD). När du integrerar FortiGate SSL VPN med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till FortiGate SSL VPN.
* Gör det möjligt för användarna att logga in automatiskt till FortiGate SSL VPN med deras Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En FortiGate-SSL VPN-prenumeration med enkel inloggning aktive rad (SSO).

## <a name="tutorial-description"></a>Beskrivning av självstudie

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

FortiGate SSL VPN stöder SP-initierad SSO.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Lägg till FortiGate SSL VPN från galleriet

Om du vill konfigurera integreringen av FortiGate SSL VPN i Azure AD måste du lägga till FortiGate SSL VPN från galleriet till din lista över hanterade SaaS-appar:

1. Logga in på Azure Portal med ett arbets-eller skol konto eller med en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , ange **Fortigate SSL VPN** i sökrutan.
1. Välj **FORTIGATE SSL VPN** i resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Konfigurera och testa Azure AD SSO för FortiGate SSL VPN

Du konfigurerar och testar Azure AD SSO med FortiGate SSL VPN genom att använda en test användare som heter B. Simon. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i FortiGate SSL VPN.

Om du vill konfigurera och testa Azure AD SSO med FortiGate SSL VPN slutför du följande steg på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** för att aktivera funktionen för dina användare.
    1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
    1. **[Bevilja åtkomst till test användaren](#grant-access-to-the-test-user)** för att aktivera enkel inloggning med Azure AD för den användaren.
1. **[Konfigurera FORTIGATE SSL VPN SSO](#configure-fortigate-ssl-vpn-sso)** på program sidan.
    1. **Skapa en Fortigate för SSL VPN-test** som motsvarighet till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal på sidan **FORTIGATE SSL VPN-** program integration i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Skärm bild som visar Penn knappen för att redigera den grundläggande SAML-konfigurationen.](common/edit-urls.png)

1. Ange följande värden på sidan **Konfigurera en enskild Sign-On med SAML** :

    a. I rutan **inloggnings-URL** anger du en URL i mönstret `https://<FQDN>/remote/login` .

    b. I rutan **identifierare** anger du en URL i mönstret `https://<FQDN>/remote/saml/metadata` .

    c. I rutan **svars-URL** anger du en URL i mönstret `https://<FQDN>/remote/saml/login` .

    d. I rutan **Logga in URL** anger du en URL i mönstret `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Dessa värden är bara mönster. Du måste använda den faktiska **inloggnings-URL: en**, **identifierare**, **svars-URL** och **en utloggnings-URL**. Kontakta [Fortinet support](https://support.fortinet.com) om du behöver hjälp. Du kan också se de exempel mönster som visas i Fortinet-dokumentationen och i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. FortiGate SSL VPN-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild som visar standardattributen.](common/default-attributes.png)

1. De två ytterligare anspråk som krävs av FortiGate SSL VPN visas i följande tabell. Namnen på dessa anspråk måste matcha namnen som används i avsnittet **utföra Fortigate kommando rads konfiguration** i den här självstudien. 

   | Namn |  Källattribut|
   | ------------ | --------- |
   | användarnamn | user.userprincipalname |
   | group | användare. grupper |
   
   Så här skapar du följande ytterligare anspråk:

   a. Bredvid användarattribut **& anspråk** väljer du **Redigera**.

   b. Välj **Lägg till nytt anspråk**.

   c. Som **namn** anger du **användar namn**.

   d. För **källattribut** väljer du **User. UserPrincipalName**.

   e. Välj **Spara**.

   f. Välj **Lägg till ett grupp anspråk**.

   ex. Välj **Alla grupper**.

   h. Markera kryss rutan **anpassa namnet på grupp anspråket** .

   i. I **namn** anger du **grupp**.
   
   j. Välj **Spara**.   

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du länken **Hämta** bredvid **certifikat (base64)** för att hämta certifikatet och spara det på datorn:

    ![Skärm bild som visar länken Hämta certifikat.](common/certificatebase64.png)

1. I avsnittet **Konfigurera FORTIGATE SSL-VPN** kopierar du lämplig URL eller URL, baserat på dina krav:

    ![Skärm bild som visar konfigurations-URL: er.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare** och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna slutför du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.  
   1. I rutan **användar namn** anger du \<username> @ \<companydomain> . \<extension> . Ett exempel är `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

#### <a name="grant-access-to-the-test-user"></a>Bevilja åtkomst till test användaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja användaren åtkomst till FortiGate SSL VPN.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **FORTIGATE SSL VPN**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Klicka på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

#### <a name="create-a-security-group-for-the-test-user"></a>Skapa en säkerhets grupp för test användaren

I det här avsnittet ska du skapa en säkerhets grupp i Azure Active Directory för test användaren. FortiGate kommer att använda den här säkerhets gruppen för att ge användaren åtkomst till nätverket via VPN.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj sedan **grupper**.
1. Välj **ny grupp** överst på skärmen.
1. I de **nya grupp** egenskaperna slutför du de här stegen:
   1. Välj **säkerhet** i listan **typ av grupp** .
   1. I rutan **grupp namn** anger du **FortiGateAccess**.
   1. I rutan **grupp Beskrivning** anger du **grupp för beviljande av Fortigate VPN-åtkomst**.
   1. För **Azure AD-roller kan tilldelas gruppen (förhands granskning)** , väljer du **Nej**.
   1. I rutan **medlemskaps typ** väljer du **tilldelad**.
   1. Under **medlemmar** väljer du **inga medlemmar markerade**.
   1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
   1. Välj **Skapa**.
1. När du är tillbaka i avsnittet **grupper** i Azure Active Directory hittar du åtkomst gruppen **Fortigate** och noterar **objekt-ID: t**. Du behöver den senare.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurera FortiGate SSL VPN SSO

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Överför base64 SAML-certifikatet till FortiGate-enheten

När du har slutfört SAML-konfigurationen av FortiGate-appen i din klient, hämtade du det Base64-kodade SAML-certifikatet. Du måste ladda upp det här certifikatet till FortiGate-enheten:

1. Logga in på hanterings portalen för FortiGate-enheten.
1. I det vänstra fönstret väljer du **system**.
1. Under **system** väljer du **certifikat**.
1. Välj **Importera**  >  **fjärrcertifikat**.
1. Bläddra till det certifikat som har laddats ned från FortiGate app Deployment i Azure-klienten, Välj det och välj sedan **OK**.

När certifikatet har överförts noterar du dess namn under **system**  >  **certifikatets**  >  **fjärrcertifikat**. Som standard får den namnet REMOTE_Cert_ *N*, där *N* är ett heltals värde.

#### <a name="complete-fortigate-command-line-configuration"></a>Slutför FortiGate kommando rads konfiguration

Följande steg kräver att du konfigurerar URL: en för Azure-utloggning. Denna URL innehåller ett frågetecken (?). Du måste vidta vissa åtgärder för att kunna skicka det här problemet. Du kan inte slutföra de här stegen från FortiGate CLI-konsolen. Upprätta i stället en SSH-session till FortiGate-installationen med hjälp av ett verktyg som till exempel. Om din FortiGate-apparat är en virtuell Azure-dator kan du utföra följande steg från serie konsolen för virtuella Azure-datorer.

För att slutföra de här stegen behöver du de värden du registrerade tidigare:

- Enhets-ID
- Svars-URL
- Utloggnings-URL
- Azure inloggnings-URL
- Azure AD-identifierare
- URL för Azure-utloggning
- Base64 certifikat namn för SAML (REMOTE_Cert_ *N*)

1. Upprätta en SSH-session med FortiGate-installationen och logga in med ett administratörs konto för FortiGate.
1. Kör dessa kommandon:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > URL: en för Azure-utloggning innehåller ett- `?` blank steg. Du måste ange en specialorder för att tillhandahålla denna URL korrekt till FortiGate-serie konsolen. URL: en är vanligt vis `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Ange URL: en för Azure-utloggning i serie konsolen genom att ange `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Välj sedan CTRL + V och klistra in resten av webb adressen för att slutföra raden: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>Konfigurera FortiGate för grupp matchning

I det här avsnittet ska du konfigurera FortiGate för att identifiera objekt-ID: t för den säkerhets grupp som innehåller test användaren. Med den här konfigurationen kan FortiGate fatta åtkomst beslut baserat på grupp medlemskapet.

För att utföra de här stegen behöver du objekt-ID för FortiGateAccess-säkerhetsgruppen som du skapade tidigare i den här självstudien.

1. Upprätta en SSH-session med FortiGate-installationen och logga in med ett administratörs konto för FortiGate.
1. Kör dessa kommandon:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Skapa en FortiGate VPN-Portal och brand Väggs princip

I det här avsnittet ska du konfigurera en FortiGate VPN-Portal och brand Väggs princip som ger åtkomst till FortiGateAccess-säkerhetsgruppen som du skapade tidigare i den här självstudien.

Arbeta med [Fortigate support-teamet](mailto:tac_amer@fortinet.com) för att lägga till VPN-portaler och brand Väggs principen till Fortigate VPN-plattformen. Du måste utföra det här steget innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till FortiGate VPN-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till FortiGate VPN-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på FortiGate VPN-panelen i Mina appar omdirigeras detta till FortiGate VPN-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat FortiGate VPN kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
