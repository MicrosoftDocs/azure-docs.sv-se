---
title: 'Självstudie: Azure Active Directory integration med Five9 plus adapter (CTI, Contact Center-agenter) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Five9 plus adapter (CTI, Contact Center-agenter).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218727"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Självstudie: Azure Active Directory integration med Five9 plus adapter (CTI, Contact Center-agenter)

I den här självstudien får du lära dig hur du integrerar Five9 plus adapter (CTI, Contact Center-agenter) med Azure Active Directory (Azure AD). När du integrerar Five9 plus adapter (CTI, Contact Center-agenter) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Five9 plus adapter (CTI, Contact Center-agenter).
* Gör det möjligt för användarna att logga in automatiskt till Five9 plus adapter (CTI, Contact Center-agenter) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Five9 plus adapter (CTI, Contact Center agents) behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Five9 plus adapter (CTI, Contact Center agents) enkel inloggning aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Five9 plus adapter (CTI, Contact Center-agenter) stöder **IDP** initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Lägg till Five9 plus adapter (CTI, Contact Center-agenter) från galleriet

Om du vill konfigurera integrationen av Five9 plus adapter (CTI, Contact Center agents) i Azure AD, måste du lägga till Five9 plus adapter (CTI, Contact Center-agenter) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Five9 plus adapter (CTI, Contact Center agents)** i sökrutan.
1. Välj **Five9 plus adapter (CTI, Contact Center-agenter)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Konfigurera och testa Azure AD SSO för Five9 plus adapter (CTI, Contact Center-agenter)

Konfigurera och testa Azure AD SSO med Five9 plus adapter (CTI, Contact Center-agenter) med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Five9 plus adapter (CTI, Contact Center agents).

Utför följande steg för att konfigurera och testa Azure AD SSO med Five9 plus adapter (CTI, Contact Center agents):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Five9 plus adapter (CTI, Contact Center agents) SSO](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Five9 plus adapter (CTI, Contact Center-agenter) testa användare](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – om du vill ha en motsvarighet till B. Simon i Five9 plus adapter (CTI, Contact Center-agenter) som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för program integration Azure Portal i **Five9 plus adapter (CTI, Contact Center agents)** , letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    a. I text rutan **identifierare** skriver du en av följande URL: er:
    
    |    Miljö      |       URL      |
    | :-- | :-- |
    | För "Five9 plus adapter för Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | För "Five9 plus adapter för Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | För "Five9 plus adapter för agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. I text rutan **svars-URL** skriver du en av följande URL: er:

    |      Miljö     |      URL      |
    | :--                  | :--           |
    | För "Five9 plus adapter för Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | För "Five9 plus adapter för Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | För "Five9 plus adapter för agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Five9 plus adapter (CTI, Contact Center agents)** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Five9 plus adapter (CTI, Contact Center-agenter).

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Five9 plus adapter (CTI, Contact Center-agenter)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Konfigurera Five9 plus adapter (CTI, Contact Center agents) SSO

1. Om du vill konfigurera enkel inloggning på **Five9 plus adapter (CTI, Contact Center agents)** -sidan, måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade URL: er till [support teamet för Five9 plus adapter (CTI, Contact Center agents)](https://www.five9.com/about/contact). Du kan också konfigurera SSO ytterligare genom att följa stegen nedan i enlighet med kortet:

    a. Administratörs guide för "Five9 plus adapter för agent Desktop Toolkit": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Administratörs guide för "Five9 plus adapter för Microsoft Dynamics CRM": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Administratörs guide för "Five9 plus adapter för Zendesk": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Skapa Five9 plus adapter (CTI, Contact Center agents) testa användare

I det här avsnittet skapar du en användare som kallas Britta Simon i Five9 plus adapter (CTI, Contact Center-agenter). Arbeta med [Five9 plus adapter (CTI, Contact Center agents) support team](https://www.five9.com/about/contact) för att lägga till användarna på plattformen Five9 plus adapter (CTI, Contact Center agent). Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på Five9 plus adapter (CTI, Contact Center-agenter) som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Five9 plus adapter (CTI, Contact Center agents) i Mina appar, bör du loggas in automatiskt på Five9 plus adapter (CTI, Contact Center-agenter) som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Five9 plus adapter (CTI, Contact Center agents) kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
