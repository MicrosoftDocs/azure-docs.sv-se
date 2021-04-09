---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med antal i drift instrument panel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och antalet i Operations-instrumentpanelen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727700"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med räkna mig i drift instrument panel

I den här självstudien får du lära dig hur du integrerar ett antal åtgärder i drifts instrument panelen med Azure Active Directory (Azure AD). När du integrerar räkna mig på instrument panelen för åtgärder med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst att räkna mig i instrument panelen för åtgärder.
* Gör det möjligt för användarna att vara inloggade automatiskt så att du kan räkna med sina Azure AD-konton i drifts instrument panelen.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Räkna mig inaktive rad prenumeration med enkel inloggning (SSO) i Operations Dashboard.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Räkna mig i drift instrument panel stöder **SP** -INITIERAd SSO

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Lägga till antal i åtgärds instrument panelen i galleriet

Om du vill konfigurera integrering av räknaren för att räkna mig i drifts instrument panelen i Azure AD måste du lägga till räkna mig i åtgärds instrument panelen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du in **antal åtgärder på instrument panelen** i rutan Sök.
1. Välj **räkna mig på instrument panelen för åtgärder i** panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Konfigurera och testa Azure AD SSO för räkna mig i drift instrument panelen

Konfigurera och testa Azure AD SSO med räkna mig in-Operations-instrumentpanel med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i antal åtgärder på instrument panelen.

Utför följande steg för att konfigurera och testa Azure AD SSO med räkna mig in-Operations-instrumentpanelen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera antal mig In-Operations instrument panelen SSO](#configure-count-me-in-operations-dashboard-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa antal mig In-Operations instrument panels test användare](#create-count-me-in-operations-dashboard-test-user)** – om du vill ha en motsvarighet till B. Simon i räknaren i åtgärds instrument panel som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal, på sidan **antal åtgärder på instrument panelen för instrument panelen** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `api-us.localz.io/<PROJECT_ID>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [antal support på drift instrument panel klient support grupp](mailto:support@localz.co) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Räkna mig i instrument panels programmet för åtgärder förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar vi att fler attribut ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------- | --------- |
    | tilldelade roller | user.assignedroles |

    > [!NOTE]
    > Antal användare i åtgärds instrument panelen förväntar sig roller för användare som tilldelats programmet. Konfigurera de här rollerna i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På **instrument panelen för att ange antal i åtgärder på instrument panelen** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet kommer du att aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst att räkna upp mig på instrument panelen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **räkna mig i instrument panelen för åtgärder**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Konfigurera antal mig In-Operations instrument panelen SSO

Om du vill konfigurera enkel inloggning för **Count in-the-Operations Dashboard-** sidan, måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal för att [räkna antalet i Operations Dashboard support team](mailto:support@localz.co). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Skapa antal mig In-Operations instrument panels test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i räkna mig i drift instrument panelen. Arbeta med [räkna mig in-Operations Dashboard support team](mailto:support@localz.co) för att lägga till användare i antal drift instrument panels plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta kommer att omdirigeras för att räkna inloggnings-URL: en för Operations på instrument panelen där du kan starta inloggnings flödet. 

* Gå till räkna inloggnings instrument panelens URL-adress direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen räkna upp till gångs instrument panelen i Mina appar kommer det att omdirigeras för att räkna inloggnings-URL: en i Operations på instrument panelen. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat räkna mig i åtgärds instrument panelen kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
