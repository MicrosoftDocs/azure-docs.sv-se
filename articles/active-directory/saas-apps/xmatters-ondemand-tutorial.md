---
title: 'Självstudie: Azure Active Directory-integrering med xMatters OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735632"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudie: Azure Active Directory integrering med xMatters OnDemand

I den här självstudien får du lära dig att integrera xMatters OnDemand med Azure Active Directory (Azure AD).
Genom att integrera xMatters OnDemand med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till xMatters OnDemand.
* Du kan göra det möjligt för användarna att logga in automatiskt till xMatters OnDemand (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med xMatters OnDemand behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* xMatters OnDemand enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* xMatters OnDemand stöder **IDP** INITIERAd SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Lägger till xMatters OnDemand från galleriet

Om du vill konfigurera integreringen av xMatters OnDemand i Azure AD måste du lägga till xMatters OnDemand från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **xMatters OnDemand** i sökrutan.
1. Välj **XMatters OnDemand** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Konfigurera och testa Azure AD SSO för xMatters OnDemand

Konfigurera och testa Azure AD SSO med xMatters OnDemand med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i xMatters OnDemand.

Utför följande steg för att konfigurera och testa Azure AD SSO med xMatters OnDemand:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    2. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera XMatters ONDEMAND SSO](#configure-xmatters-ondemand-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa XMatters OnDemand test User](#create-xmatters-ondemand-test-user)** – om du vill ha en motsvarighet till Britta Simon i xMatters OnDemand som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **XMatters OnDemand** application integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare** anger du en URL med något av följande mönster:

    | Identifierare |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. I text rutan **svars-URL** skriver du en URL med något av följande mönster:

    | Svars-URL |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [XMatters OnDemand client support team](https://www.xmatters.com/company/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!IMPORTANT]
    > Du måste vidarebefordra certifikatet till [support teamet XMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikatet måste laddas upp av xMatters support-teamet innan du kan slutföra konfigurationen för enkel inloggning.

6. I avsnittet **Konfigurera XMatters OnDemand** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till xMatters OnDemand.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **XMatters OnDemand**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-xmatters-ondemand-sso"></a>Konfigurera xMatters OnDemand SSO

1. Logga in på din XMatters OnDemand-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **administratör** och klicka sedan på **företags information**.

    ![Sidan administratör](./media/xmatters-ondemand-tutorial/admin.png "Administratör")

3. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![Avsnittet SAML-konfiguration ](./media/xmatters-ondemand-tutorial/saml-configuration.png "SAML-konfiguration")

    a. Välj **Aktivera SAML**.

    b. I text rutan **ID för identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    c. I text rutan **enkel inloggnings-URL** klistrar du in **inloggnings-URL** -värdet som du har kopierat från Azure Portal.

    d. I text rutan **Logga ut URL-omdirigering** , klistra in **URL** för att klistra in, som du har kopierat från Azure Portal.

    e. Klicka på **Välj fil** för att överföra **certifikatet (base64)** som du har laddat ned från Azure Portal. 

    f. Klicka på **Spara ändringar** längst upp på sidan företags information.

    ![Företagsinformation](./media/xmatters-ondemand-tutorial/save-button.png "Företagsinformation")

### <a name="create-xmatters-ondemand-test-user"></a>Skapa xMatters OnDemand test User

1. Logga in på din **XMatters OnDemand** -klient.

2. Gå till **användarens ikon**  >  **användare** och klicka sedan på **Lägg till användare**.

    ![Användare](./media/xmatters-ondemand-tutorial/add-user.png "Användare")

3. Fyll i de obligatoriska fälten i avsnittet **Lägg till användare** och klicka på knappen **Lägg till användare** .

    ![Lägg till en användare](./media/xmatters-ondemand-tutorial/add-user-2.png "Lägg till en användare")



### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal och du bör logga in automatiskt till xMatters OnDemand som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen xMatters OnDemand i Mina appar, bör du loggas in automatiskt till xMatters OnDemand för vilken du ställer in SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat xMatters OnDemand kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).