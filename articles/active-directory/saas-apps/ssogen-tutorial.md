---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, liboch JDE | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 72b91be9875abf07e5f6f828ed32483a5d61eadd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524449"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE

I den här självstudien får du lära dig att integrera SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och med Azure Active Directory (Azure AD). När du integrerar SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, och JDE med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och.
* Gör det möjligt för användarna att logga in automatiskt till SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, och JDE med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite – EBS, östra och JDE enkel inloggning (SSO) med en aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE stöder **SP och IDP** initierad SSO.
* När du har konfigurerat SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE kan du genomdriva sessionsnycklar, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Lägga till SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite – EBS, östra och JDE från galleriet

Om du vill konfigurera integreringen av SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE i Azure AD, måste du lägga till SSOGEN-Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och SaaS från galleriet till listan över hanterade-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och** i sökrutan.
1. Välj **SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Konfigurera och testa enkel inloggning med Azure AD för SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE

Konfigurera och testa Azure AD SSO med SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SSOGEN-Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och.

Om du vill konfigurera och testa Azure AD SSO med SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SSOGEN Azure AD SSO Gateway för Oracle E Business Suite EBS, östra och JDE SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa SSOGEN Azure AD SSO-Gateway för Oracle e Business Suite EBS, östra och JDE-test User](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** -för att ha en motsvarighet till B. Simon i SSOGEN-Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **för SSOGEN-Azure AD SSO-Gateway för Oracle E-Business Suite – EBS, östra och JDE** program integration, letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite – EBS, östra och JDE klient support team](mailto:support@ssogen.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Din SSOGEN-Azure AD SSO-Gateway för Oracle E-Business Suite-EBS, JDE och-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och-programmet förväntar sig att **NameIdentifier** mappas med **User. egna namnet onpremisessamaccountname**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, JDE och.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Konfigurera SSOGEN Azure AD SSO Gateway för Oracle E Business Suite EBS, östra och JDE SSO

Om du vill konfigurera enkel inloggning på **SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE** sida, hittar du programspecifik SSO Registration-dokumentation nedan:

* Oracle-EBS – integrering av Azure AD SSO: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* Till och med Azure AD SSO-integrering: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards – Azure AD SSO-integrering: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache – Azure AD SSO-integrering: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Skapa SSOGEN Azure AD SSO Gateway för Oracle E Business Suite EBS, östra och JDE test User

Azure AD skickar ett unikt användar-ID (namn-ID) till användar programmet när autentiseringen har slutförts.  Se till att unikt användar-ID (namn-ID) matchar användar posten i programmet FND_USER. USER_NAME i Oracle-EBS till exempel.

Kontakta [info@ssogen.com](mailto:info@ssogen.com) och [support@ssogen.com](mailto:support@ssogen.com) om du vill ha hjälp.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SSOGEN-Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE på åtkomst panelen, bör du loggas in automatiskt på SSOGEN-Azure AD SSO-gatewayen för Oracle E-Business Suite-EBS, liboch JDE som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, östra och JDE med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, liboch JDE med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
