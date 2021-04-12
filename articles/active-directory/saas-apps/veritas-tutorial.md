---
title: 'Självstudie: Azure Active Directory integrering med Veritas Enterprise-Vault.cloud SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veritas Enterprise-Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222290"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Självstudie: Azure Active Directory integrering med Veritas Enterprise-Vault.cloud SSO

I den här självstudien får du lära dig att integrera Veritas Enterprise-Vault.cloud SSO med Azure Active Directory (Azure AD). När du integrerar Veritas Enterprise-Vault.cloud SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Veritas Enterprise-Vault.cloud SSO.
* Gör det möjligt för användarna att logga in automatiskt till Veritas Enterprise-Vault.cloud SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Veritas Enterprise Vault.cloud SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Veritas Enterprise Vault.cloud SSO enkel inloggning aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Veritas Enterprise-Vault.cloud SSO stöder **SP** -initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Lägg till Veritas Enterprise-Vault.cloud SSO från galleriet

Om du vill konfigurera integrering av Veritas Enterprise-Vault.cloud SSO i Azure AD måste du lägga till Veritas Enterprise Vault.cloud SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du in **Veritas Enterprise-Vault.Cloud SSO** i sökrutan.
1. Välj **Veritas Enterprise Vault.Cloud SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Konfigurera och testa Azure AD SSO för Veritas Enterprise Vault.cloud SSO

Konfigurera och testa Azure AD SSO med Veritas Enterprise Vault.cloud SSO med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Veritas Enterprise-Vault.cloud SSO.

Utför följande steg för att konfigurera och testa Azure AD SSO med Veritas Enterprise Vault.cloud SSO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Veritas Enterprise Vault.Cloud SSO SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa användare av Veritas Enterprise-Vault.Cloud SSO-test](#create-veritas-enterprise-vaultcloud-sso-test-user)** för att få en motsvarighet till B. Simon i Veritas Enterprise Vault.Cloud SSO som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Veritas Enterprise-Vault.Cloud SSO** -program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. I rutan **identifierare** skriver du en av URL: erna enligt data centret:

    | Datacenter| URL |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|

    c. I text rutan **svars-URL** skriver du en av URL: erna enligt data centret:

    | Datacenter| URL |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Veritas Enterprise Vault.Cloud SSO client support team](https://www.veritas.com/support/.html) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Veritas Enterprise-Vault.Cloud SSO** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veritas Enterprise-Vault.cloud SSO.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Veritas Enterprise-Vault.Cloud SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Konfigurera Veritas Enterprise Vault.cloud SSO SSO

Om du vill konfigurera enkel inloggning på **Veritas Enterprise-Vault.Cloud SSO** -sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Veritas Enterprise Vault.Cloud SSO support team](https://www.veritas.com/support/.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Skapa en Veritas Enterprise Vault.cloud SSO-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Veritas Enterprise Vault.cloud SSO. Arbeta med [Veritas enterprise Vault.Cloud SSO support team](https://www.veritas.com/support/.html) för att lägga till användare i Veritas Enterprise Vault.Cloud SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Veritas Enterprise-Vault.cloud SSO-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Veritas Enterprise Vault.cloud SSO inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på den Veritas Enterprise-Vault.cloud SSO-panelen i Mina appar omdirigeras det till Veritas Enterprise Vault.cloud SSO-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Veritas Enterprise Vault.cloud SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
