---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Azure AD-integrering för Kemp: LoadMaster | Microsoft Docs'
description: 'Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kemp: LoadMaster Azure AD-integrering.'
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 1d974badd715d7966f552603a77d77aab95d70f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549978"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Självstudie: Azure Active Directory SSO-integrering med Kemp: LoadMaster Azure AD-integrering

I den här självstudien får du lära dig att integrera Kemp: LoadMaster Azure AD integration med Azure Active Directory (Azure AD). När du integrerar Kemp: LoadMaster Azure AD-integrering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Kemp: LoadMaster Azure AD-integrering.
* Gör det möjligt för användarna att logga in automatiskt till Kemp: LoadMaster Azure AD-integration med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Kemp: LoadMaster Azure AD integration enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Kemp: LoadMaster Azure AD-integration stöder **IDP** INITIERAd SSO
* När du har konfigurerat Kemp: LoadMaster Azure AD-integrering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Lägga till Kemp: LoadMaster Azure AD-integrering från galleriet

Om du vill konfigurera integreringen av Kemp: LoadMaster Azure AD-integrering i Azure AD måste du lägga till Kemp: LoadMaster Azure AD integration från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Kemp: LoadMaster Azure AD integration** i sökrutan.
1. Välj **Kemp: LoadMaster Azure AD integration** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Konfigurera och testa Azure AD SSO för Kemp: LoadMaster Azure AD-integrering

Konfigurera och testa Azure AD SSO med Kemp: LoadMaster Azure AD-integrering med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Kemp: LoadMaster Azure AD-integrering.

Om du vill konfigurera och testa Azure AD SSO med Kemp: LoadMaster Azure AD-integrering slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Kemp: LoadMaster Azure AD integration SSO](#configure-kemp-loadmaster-azure-ad-integration-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.

1. **[Publicerar webb server](#publishing-web-server)**
    1. **[Skapa en virtuell tjänst](#create-a-virtual-service)**
    1. **[Certifikat och säkerhet](#certificates-and-security)**
    1. **[SAML-profil för Kemp: LoadMaster Azure AD-integration](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Verifiera ändringarna](#verify-the-changes)**
1. **[Konfigurera Kerberos-baserad autentisering](#configuring-kerberos-based-authentication)**
    1. **[Skapa ett Kerberos Delegerings konto för Kemp: LoadMaster Azure AD-integrering](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp: LoadMaster Azure AD integration KCD (Kerberos Delegerings konton)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp: LoadMaster Azure AD-integrering ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Skapa Kemp: LoadMaster Azure AD integration test User](#create-kemp-loadmaster-azure-ad-integration-test-user)** – om du vill ha en motsvarighet till B. Simon i Kemp: LOADMASTER Azure AD-integrering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Kemp: LoadMaster Azure AD integration** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare (enhets-ID)** anger du en URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. Skriv en URL i textrutan **Svars-URL**: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Kemp: LoadMaster Azure AD integration client support team](mailto:support@kemp.ax) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet Grundläggande SAML-konfiguration i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och **federationsmetadata XML för federationsmetadata**, väljer **Hämta** för att hämta XML-filerna för certifikatet och federationsmetadata och sparar dem på din dator.

    ![Länk för nedladdning av certifikatet](./media/kemp-tutorial/certificate-base-64.png)

1. I avsnittet **Konfigurera Kemp: LoadMaster Azure AD-integrering** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kemp: LoadMaster Azure AD-integrering.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Kemp: LoadMaster Azure AD integration**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Konfigurera Kemp: LoadMaster Azure AD integration SSO

## <a name="publishing-web-server"></a>Publicerar webb server 

### <a name="create-a-virtual-service"></a>Skapa en virtuell tjänst 

1. Gå till Kemp: LoadMaster Azure AD integration load Master-webbgränssnitt > virtuella tjänster > Lägg till ny.

1. Klicka på Lägg till ny.

1. Ange parametrar för den virtuella tjänsten.

    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-1.png)

    a. Virtuell adress
    
    b. Port
    
    c. Tjänst namn (valfritt)
    
    d. Protokoll 

1. Avsnittet navigera till riktiga servrar.

1. Klicka på Lägg till ny.

1. Ange parametrar för den riktiga servern.
    
    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-2.png)

    a. Välj Tillåt fjär adresser
    
    b. Typ i verklig server adress
    
    c. Port
    
    d. Vidarebefordrings metod
    
    e. Vikt
    
    f. Anslutnings gräns
    
    ex. Klicka på Lägg till den här riktiga servern

## <a name="certificates-and-security"></a>Certifikat och säkerhet
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Importera certifikat på Kemp: LoadMaster Azure AD-integrering 
 
1. Gå till Kemp: LoadMaster Azure AD integration Web Portal > certifikat & säkerhet > SSL-certifikat.

1. Under hantera certifikat > certifikat konfiguration.

1. Klicka på Importera certifikat.

1. Ange namnet på filen som innehåller certifikatet. Filen kan även innehålla den privata nyckeln. Om filen inte innehåller den privata nyckeln måste även filen som innehåller den privata nyckeln anges. Certifikatet kan vara i båda. PEM eller. PFX-format (IIS).

1. Klicka på Välj fil på certifikat fil.

1. Klicka på nyckel fil (valfritt).

1. Klicka på Spara.

### <a name="ssl-acceleration"></a>SSL-acceleration
 
1. Gå till Kemp: Load Master Web UI > virtuella tjänster > virtuella tjänster.

1. Klicka på ändra under åtgärd.

1. Under egenskaper för TCP/x.x.x.: 443 (ID: 6) – drift på nivå 7 klickar du på SSL-egenskaper.
    
    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-3.png)
    
    a. Klicka på aktive rad i SSL-acceleration.
    
    b. Under tillgängliga certifikat väljer du det importerade certifikatet och klickar på `>` symbol.
    
    c. När önskat SSL-certifikat visas i tilldelade certifikat klickar du på **Ange certifikat**.

    > [!NOTE]
    > Se till att du klickar på **Ange certifikat**.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>SAML-profil för Kemp: LoadMaster Azure AD-integration
 
### <a name="import-idp-certificate"></a>Importera IdP-certifikat

Gå till Kemp: LoadMaster Azure AD integration webb konsol 

1. Klicka på mellanliggande certifikat under certifikat och utfärdare.

    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-6.png)

    a. Klicka på Välj fil i Lägg till ett nytt mellanliggande certifikat.
    
    b. Gå till certifikat filen som tidigare hämtats från Azure AD Enterprise-programmet.
    
    c. Klicka på öppna.
    
    d. Ange ett namn på certifikat namnet.
    
    e. Klicka på Lägg till certifikat.

### <a name="create-authentication-policy"></a>Skapa autentiseringsprincip 
 
Gå till hantera SSO under virtuella tjänster.

   ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-7.png)
   
   a. Klicka på Lägg till under Lägg till ny konfiguration på klient sidan när du har angett ett namn.

   b. Välj SAML under autentiseringsprotokoll.

   c. Välj metadatafil under IdP-etablering. 

   d. Klicka på Välj fil.

   e. Navigera till tidigare hämtade XML från Azure Portal.

   f. Klicka på öppna och klicka på Importera IdP-metadatafil.

   ex. Välj mellanliggande certifikat från IdP-certifikat.

   h. Ange det SP-enhets-ID som ska överensstämma med den identitet som skapades i Azure Portal 

   i. Klicka på Ange SP-enhets-ID.

### <a name="set-authentication"></a>Ange autentisering  
 
Kemp: LoadMaster Azure AD integration-webbkonsol

1. Klicka på virtuella tjänster.

1. Klicka på Visa/ändra tjänster.

1. Klicka på ändra och navigera till ESP-alternativ.
    
    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-8.png)

    a. Klicka på Aktivera ESP.
    
    b. Välj SAML i autentiseringsläge för klient.
    
    c. Välj tidigare skapad autentisering på klient sidan i SSO-domänen.
    
    d. Ange värd namnet i tillåtna virtuella värdar och klicka på Ange tillåtna virtuella värdar.
    
    e. Skriv/* i tillåtna virtuella kataloger (baserat på åtkomst krav) och klicka på Ange tillåtna kataloger.

### <a name="verify-the-changes"></a>Verifiera ändringarna 
 
Bläddra till programmets URL 

Du bör se din klient inloggnings sida i stället för oautentiserad åtkomst tidigare. 

![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Konfigurera Kerberos-baserad autentisering 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Skapa ett Kerberos Delegerings konto för Kemp: LoadMaster Azure AD-integrering 

1. Skapa ett användar konto (i det här exemplet AppDelegation).
    
    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-10.png)


    a. Välj fliken Redigera attribut.

    b. Navigera till servicePrincipalName. 

    c. Välj servicePrincipalName och klicka på Redigera.

    d. Skriv http/kcduser i fältet värde att lägga till och klicka på Lägg till. 

    e. Klicka på tillämpa och OK. Fönstret måste stängas innan du kan öppna det igen (för att se fliken ny delegering). 

1. Öppna fönstret användar egenskaper igen och fliken delegering blir tillgänglig. 

1. Välj fliken delegering.

    ![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-11.png)

    a. Välj Den här användaren är endast betrodd för delegering till de tjänster som anges.

    b. Välj Använd valfritt autentiseringsprotokoll.

    c. Lägg till de riktiga servrarna och Lägg till http som tjänst. 
    
    d. Markera kryss rutan utökad. 

    e. Du kan se alla servrar med både värd namnet och det fullständiga domän namnet.
    
    f. Klicka på OK.

> [!NOTE]
> Ange SPN för programmet/webbplatsen efter vad som är tillämpligt. För att komma åt programmet när identiteten för programpoolsidentiteten har angetts. För att få åtkomst till IIS-appen med hjälp av FQDN-namnet, går du till kommando tolken i faktisk server och skriver SetSpn med nödvändiga parametrar. T. ex. Setspn – S HTTP/sescoindc. sunehes. co. i suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp: LoadMaster Azure AD integration KCD (Kerberos Delegerings konton) 

Gå till Kemp: LoadMaster Azure AD integration webb konsolen > virtuella tjänster > hantera SSO.

![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-12.png)

a. Gå till konfigurationer för enkel inloggning på Server sidan.

b. Skriv namn i Lägg till ny konfiguration på Server sidan och klicka på Lägg till.

c. Välj Kerberos-begränsad delegering i autentiseringsprotokoll.

d. Skriv domän namnet i Kerberos-sfären.

e. Klicka på Ange Kerberos-sfär.

f. Ange domänkontrollantens IP-adress i Kerberos-Key Distribution Center.

ex. Klicka på Ange Kerberos KDC.

h. Skriv KCD användar namn i Kerberos-betrott användar namn.

i. Klicka på Ange betrott användar namn för KDC.

j. Skriv lösen ord i Kerberos-betrott användar lösen ord.

k. Klicka på Ange KCD-betrott användar lösen ord.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp: LoadMaster Azure AD-integrering ESP 

Gå till Virtual Services > Visa/ändra tjänster.

![Kemp: LoadMaster Azure AD integration-webbserver](./media/kemp-tutorial/kemp-13.png)

a. Klicka på ändra på den virtuella tjänstens Nick namn.
    
b. Klicka på ESP-alternativ.
    
c. Under autentiseringsläge för server väljer du KCD.
        
d. Under konfiguration på Server sidan väljer du den tidigare skapade profil på Server sidan.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Skapa Kemp: LoadMaster Azure AD integration test användare

I det här avsnittet skapar du en användare som heter B. Simon i Kemp: LoadMaster Azure AD-integrering. Arbeta med [Kemp: LoadMaster Azure AD integration client support team](mailto:support@kemp.ax) för att lägga till användare i Kemp: LOADMASTER Azure AD integration Platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kemp: LoadMaster Azure AD-integration på åtkomst panelen, bör du loggas in automatiskt på den Kemp: LoadMaster Azure AD-integration som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Kemp: LoadMaster Azure AD integration med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Kemp: LoadMaster Azure AD-integrering med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
