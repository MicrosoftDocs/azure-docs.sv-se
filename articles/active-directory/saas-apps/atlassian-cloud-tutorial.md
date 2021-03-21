---
title: 'Självstudie: Azure Active Directory integrering med Atlassian Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94330565"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Självstudie: integrera Atlassian Cloud med Azure Active Directory

I den här självstudien får du lära dig att integrera Atlassian Cloud med Azure Active Directory (Azure AD). När du integrerar Atlassian Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Atlassian-molnet.
* Gör det möjligt för användarna att logga in automatiskt till Atlassian-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Atlassian-prenumeration med enkel inloggning (SSO) för molnet.
* Om du vill aktivera enkel inloggning med SAML för Atlassian Cloud-produkter måste du konfigurera Atlassian-åtkomst. Läs mer om [Atlassians-åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

* Atlassian Cloud stöder **SP- och IDP**-initierad enkel inloggning
* Atlassian Cloud stöder [Automatisk användar etablering och avetablering](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Lägga till Atlassian Cloud från galleriet

Om du vill konfigurera integreringen av Atlassian Cloud i Azure AD måste du lägga till Atlassian Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Atlassian Cloud** i sökrutan.
1. Välj **Atlassian Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med Atlassian-molnet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Atlassian-molnet.

Utför följande steg för att konfigurera och testa Azure AD SSO med Atlassian Cloud:

1. **[Konfigurera Azure AD med Atlassian Cloud SSO](#configure-azure-ad-sso)** – så att användarna kan använda Azure AD-baserade SAML SSO med Atlassian-molnet.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Skapa Atlassian Cloud test User](#create-atlassian-cloud-test-user)** – för att få en motsvarighet till B. Simon i Atlassian-molnet som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Om du vill automatisera konfigurationen i Atlassian-molnet måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Atlassian Cloud** för att dirigera dig till Atlassian Cloud Application. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Atlassian-molnet. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Atlassian-molnet manuellt loggar du in på din Atlassian Cloud Company-webbplats som administratör och utför följande steg.

1. Innan du börjar går du till din Atlassian produkt instans och kopierar/sparar instans-URL: en
   > [!NOTE]
   > URL: en bör passa `https://<instancename>.atlassian.net` mönstret

   ![instans namn](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Öppna [administrations portalen för Atlassian](https://admin.atlassian.com/) och klicka på organisationens namn

   ![organisation](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Du måste verifiera din domän innan du fortsätter med att konfigurera enkel inloggning. Mer information finns i dokumentet [Atlassian-domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).
1. Från skärmen Atlassian Admin Portal väljer du **säkerhet** från den vänstra lådan

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Från säkerhets skärmen Atlassian Admin Portal väljer du **SAML enkel inloggning** från den vänstra lådan

   ![SAML SSO](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Klicka på **Lägg till SAML-konfiguration** och behåll sidan öppen

   ![Lägg till SAML-konfiguration](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![Lägg till SAML-konfiguration 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. I Azure Portal på sidan **Atlassian Cloud** Application Integration letar du upp avsnittet **Hantera** och väljer **Konfigurera enkel inloggning**.

   ![Konfigurera SSO](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.

   ![SAML i Azure](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. På sidan **Konfigurera en enskild Sign-On med SAML** rullar du ned till **Konfigurera Atlassian-molnet**
   
   a. Klicka på **konfigurations-URL: er**

   ![er](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Kopiera värdet för **Azure AD-identifieraren** från Azure Portal, klistra in det i text rutan för **entitets-ID för identitetsprovider** i Atlassian
   
   c. Kopiera **inloggnings-URL** -värde från Azure Portal, klistra in det i text rutan för **URL-adressen för identitetsprovider** i Atlassian

   ![URL för identitets leverantörens SSO](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![enhets-ID och SS](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![signerings certifikat](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certifikat 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Lägg till/Spara SAML-** konfigurationen i Atlassian

1. Om du vill konfigurera programmet i **IDP** initierat läge redigerar du avsnittet **grundläggande SAML-konfiguration** på sidan **Konfigurera enkla Sign-On med SAML** i Azure och öppnar **sidan SAML-enkel inloggning** på Atlassian admin-portalen

   a. Kopiera **SP-entitetens ID-** värde från Atlassian, klistra in det i rutan **identifierare (enhets-ID)** i Azure och ange det som standard
   
   b. Kopiera URL-värdet för den **försäkrans konsument tjänst** från Atlassian, klistra in det i rutan **svars-URL (intyg om försäkrans konsument tjänst)** i Azure och ange det som standard
   
   c. Kopiera **instansen URL** -värde, som du kopierade i steg 1 och klistra in det i rutan **relä tillstånd** i Azure

   ![Kopiera URL: er](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![knappen Redigera](./media/atlassian-cloud-tutorial/edit-button.png)

   ![URL-bild](./media/atlassian-cloud-tutorial/urls.png)
   
1. Om du vill konfigurera programmet i **SP** initierat läge redigerar du avsnittet **grundläggande SAML-konfiguration** på sidan **Konfigurera enkla Sign-On med SAML** i Azure. Kopiera din **instans-URL** (från steg 1) och klistra in den i rutan för **inloggnings-URL** i Azure

   ![knappen Redigera i URL: er](./media/atlassian-cloud-tutorial/edit-button.png)

   ![inloggnings-URL](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. Ditt Atlassian Cloud-program förväntar sig SAML-intygen i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Du kan redigera mappningen av attribut genom att klicka på ikonen **Redigera** . 

   ![dokumentattribut](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Mappning av attribut för en Azure AD-klient med en Microsoft 365-licens
      
      a. Klicka på anspråk på **unikt användar-ID (namn-ID)**

      ![attribut och anspråk](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud förväntar sig att **NameIdentifier** (**unik användar identifierare**) ska mappas till användarens e-post (**User.email**). Redigera **källattributet** och ändra det till **User. mail**. Spara ändringarna i anspråket.

      ![unikt användar-ID](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. De slutgiltiga attributens mappningar bör se ut så här.

      ![bild 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Attributmappning för en Azure AD-klient utan en Microsoft 365-licens 

      a. Klicka på `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` anspråket.

      ![bild 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Även om Azure inte fyller i **User. mail-** attributet för användare som skapats i Azure AD-klienter utan Microsoft 365 licenser och lagrar e-postmeddelandet för sådana användare i **userPrincipalName** -attributet. Atlassian Cloud förväntar sig att **NameIdentifier** (**unik användar identifierare**) ska mappas till användarens e-post (**User. UserPrincipalName**).  Redigera **källattributet**  och ändra det till **User. UserPrincipalName**. Spara ändringarna i anspråket.

      ![Ange e-post](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. De slutgiltiga attributens mappningar bör se ut så här.

      ![bild 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Atlassian-molnet.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Atlassian Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-atlassian-cloud-test-user"></a>Skapa testanvändare för Atlassian Cloud

Om du vill aktivera Azure AD-användare så att de kan logga in på Atlassian Cloud måste du etablera användarkontona manuellt i Atlassian Cloud genom att göra följande:

1. Välj **Användare** på **Administration**-panelen.

    ![Atlassian Cloud-användarlänken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Välj **Bjud in användare** om du vill skapa en användare i Atlassian Cloud.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Ange användarens e-postadress i rutan **E-postadress** och tilldela sedan programmet åtkomst.

    ![Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Välj **Bjud in användare** om du vill skicka en e-postinbjudan till användaren. En e-postinbjudan skickas till användaren och efter det att användaren har accepterat inbjudan aktiveras hen i systemet.

> [!NOTE]
> Du kan också skapa flera användare genom att använda knappen **Skapa flera** i avsnittet **Användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Atlassian Cloud Sign on URL där du kan starta inloggnings flödet.  

* Gå till Atlassian Cloud Sign-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till det Atlassian-moln som du ställer in SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen Atlassian Cloud på åtkomst panelen, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Atlassian-molnet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Atlassian-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).