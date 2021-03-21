---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med brand vägg för FortiWeb-webbprogram | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och FortiWeb-brand vägg för webb program.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731961"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med brand vägg för FortiWeb-webbprogram

I den här självstudien får du lära dig att integrera FortiWeb-brand vägg för webbaserade program med Azure Active Directory (Azure AD). När du integrerar FortiWeb-brand vägg för webb program med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till FortiWeb-brand vägg för webb program.
* Gör det möjligt för användarna att logga in automatiskt för att FortiWeb webb program brand väggen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* FortiWeb för enkel inloggning (SSO) för webb program brand väggen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* FortiWeb webb programs brand vägg stöder **SP** -initierad SSO.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Lägga till FortiWeb webb program brand vägg från galleriet

Om du vill konfigurera integreringen av FortiWeb-webbappens brand vägg i Azure AD måste du lägga till FortiWeb webb program brand vägg från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **FortiWeb Web Application Firewall** i sökrutan.
1. Välj **FortiWeb webb program brand vägg** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Konfigurera och testa Azure AD SSO för FortiWeb webb program brand vägg

Konfigurera och testa Azure AD SSO med FortiWeb WebApplication-brandvägg med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i brand väggen för FortiWeb-webbprogram.

Utför följande steg för att konfigurera och testa Azure AD SSO med FortiWeb WebApplication-brandvägg:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FortiWeb Web Application FIREWALL SSO](#configure-fortiweb-web-application-firewall-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa FortiWeb för webb program brand Väggs test](#create-fortiweb-web-application-firewall-test-user)** – om du vill ha en motsvarighet till B. Simon i FortiWeb webb program brand vägg som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för **FortiWeb webb programs brand Väggs** program integrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

   a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.<CUSTOMER_DOMAIN>.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.<CUSTOMER_DOMAIN>.com`

    d. I text rutan **utloggnings-URL** skriver du en URL med följande mönster: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` är en namn identifierare som kommer att användas senare när du anger konfigurationen till FortiWeb.
    > Kontakta [FortiWeb för webb programs brand vägg](mailto:support@fortinet.com) för att få verkliga URL-värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)


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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till FortiWeb webb program brand vägg.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **FortiWeb-brand vägg för webb program**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Konfigurera FortiWeb webb program brand vägg SSO

1.  Navigera till `https://<address>:8443` där `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella FortiWeb-datorn.

2.  Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiWeb-datorer.

1. Utför följande steg på följande sida.

    ![Sidan SAML-Server](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  Klicka på **användare** på menyn till vänster.

    b.  Klicka på **fjärrserver** under användare.

    c.  Klicka på **SAML-Server**.

    d.  Klicka på **Skapa ny**.

    e.  I fältet **namn** anger du värdet för `<fwName>` det som används i avsnittet Konfigurera Azure AD.

    f.  I text rutan **entitets-ID** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    ex. Klicka på **Välj fil** bredvid **metadata** och välj **XML-** filen för federationsmetadata som du har laddat ned från Azure Portal.

    h.  Klicka på **OK**.

### <a name="create-a-site-publishing-rule"></a>Skapa en plats publicerings regel

1.  Navigera till `https://<address>:8443` där `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella FortiWeb-datorn.

1.  Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiWeb-datorer.
1. Utför följande steg på följande sida.

    ![Plats publicerings regel](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  Klicka på **program leverans** i menyn till vänster.
    
    b.  Klicka på **Publicera på plats** under **program leverans**.
    
    c.  Klicka på **publicera** på plats under **plats publicering**.
    
    d.  Klicka på **plats publicerings regel**.
    
    e.  Klicka på **Skapa ny**.
    
    f.  Ange ett namn för plats publicerings regeln.
    
    ex.  Klicka på **reguljärt uttryck** bredvid den **publicerade plats typen**.
    
    i.  Bredvid **publicerad webbplats** anger du en sträng som ska matcha värd rubriken för den webbplats som du publicerar.
    
    j.  Bredvid **sökväg** anger du a/.
    
    k.  Bredvid autentiseringsmetod för **klient** väljer du **SAML-autentisering**.
    
    l.  I list rutan **SAML-Server** väljer du den SAML-server som du skapade tidigare.
    
    m.  Klicka på **OK**.


### <a name="create-a-site-publishing-policy"></a>Skapa en plats publicerings princip

1.  Navigera till `https://<address>:8443` där `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella FortiWeb-datorn.

2.  Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiWeb-datorer.

1. Utför följande steg på följande sida.

    ![Plats publicerings princip](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  Klicka på **program leverans** i menyn till vänster.

    b.  Klicka på **Publicera på plats** under **program leverans**.

    c.  Klicka på **publicera** på plats under **plats publicering**.

    d.  Klicka på **plats publicerings princip**.

    e.  Klicka på **Skapa ny**.

    f.  Ange ett namn för plats publicerings principen.

    ex.  Klicka på **OK**.

    h.  Klicka på **Skapa ny**.

    i.  I list rutan **regel** väljer du den plats publicerings regel som du skapade tidigare.

    j.  Klicka på **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Skapa och tilldela en webb skydds profil

1.  Navigera till `https://<address>:8443` där `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella FortiWeb-datorn.

2.  Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiWeb-datorer.
3.  Klicka på **princip** på den vänstra menyn.
4.  Under **princip** klickar du på **webb skydds profil**.
5.  Klicka på **infogat standard skydd** och klicka på **klona**.
6.  Ange ett namn för den nya webb skydds profilen och klicka på **OK**.
7.  Välj den nya webb skydds profilen och klicka på **Redigera**.
8.  Bredvid **Publicera på plats** väljer du den plats publicerings princip som du skapade tidigare.
9.  Klicka på **OK**.
 
    ![Publicera webbplats](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. Klicka på **princip** på den vänstra menyn.
11. Under **princip** klickar du på **Server princip**.
12. Välj den server princip som används för att publicera den webbplats som du vill använda Azure Active Directory för autentisering.
13. Klicka på **Redigera**.
14. I list rutan **webb skydds profil** väljer du den webb skydds profil som du nyss skapade.
15. Klicka på **OK**.
16. Försök att komma åt den externa URL: en som FortiWeb publicerar webbplatsen på. Du bör omdirigera till Azure Active Directory för autentisering.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Skapa FortiWeb webb program brand vägg test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i FortiWeb Web Application Firewall. Arbeta med [FortiWeb-teamet för webb programs brand vägg](mailto:support@fortinet.com) för att lägga till användare i FortiWeb webb program brand Väggs plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till FortiWeb webb program inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till FortiWeb webb program inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen FortiWeb webb program i Mina appar omdirigeras det till FortiWeb webb program inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat brand väggen för FortiWeb-webbprogram kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).