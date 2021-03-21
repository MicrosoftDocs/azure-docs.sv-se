---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med ProProfs Knowledge Base | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ProProfs kunskaps bas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/27/2020
ms.author: jeedes
ms.openlocfilehash: 9747abb5b614c5a59b56159399c3386a831b3337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-proprofs-knowledge-base"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med kunskaps basen i ProProfs

I den här självstudien får du lära dig hur du integrerar ProProfs Knowledge Base med Azure Active Directory (Azure AD). När du integrerar ProProfs Knowledge Base med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ProProfs kunskaps bas.
* Gör det möjligt för användarna att logga in automatiskt till ProProfs kunskaps bas med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ProProfs-prenumeration med enkel inloggning (SSO) för kunskaps bas.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.


* ProProfs Knowledge Base stöder **IDP** INITIERAd SSO

## <a name="adding-proprofs-knowledge-base-from-the-gallery"></a>Lägga till ProProfs Knowledge Base från galleriet

Om du vill konfigurera integreringen av ProProfs-kunskaps basen i Azure AD måste du lägga till ProProfs Knowledge Base från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Proprofs Knowledge Base** i sökrutan.
1. Välj **Proprofs Knowledge Base** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-proprofs-knowledge-base"></a>Konfigurera och testa Azure AD SSO för ProProfs Knowledge Base

Konfigurera och testa Azure AD SSO med ProProfs Knowledge Base med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ProProfs Knowledge Base.

Utför följande steg för att konfigurera och testa Azure AD SSO med ProProfs Knowledge Base:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Proprofs Knowledge Base SSO](#configure-proprofs-knowledge-base-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Proprofs kunskaps bas test användare](#create-proprofs-knowledge-base-test-user)** -om du vill ha en motsvarighet till B. Simon i Proprofs Knowledge Base som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Proprofs Knowledge Base** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Proprofs Knowledge Base** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ProProfs kunskaps bas.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Proprofs Knowledge Base**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-proprofs-knowledge-base-sso"></a>Konfigurera ProProfs Knowledge Base SSO

Om du vill konfigurera enkel inloggning på Proprofs-sidan för **kunskaps basen** måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Proprofs Knowledge Base Support Team](mailto:support@proprofs.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-proprofs-knowledge-base-test-user"></a>Skapa ProProfs kunskaps bas test användare

I det här avsnittet skapar du en användare som heter Britta Simon i ProProfs Knowledge Base. Arbeta med [Proprofs support team för kunskaps bas](mailto:support@proprofs.com) för att lägga till användare i Proprofs Knowledge Base-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till den ProProfs-kunskaps bas som du ställer in för SSO

* Du kan använda Microsoft Mina appar. När du klickar på ProProfs kunskaps bas panelen i Mina appar, bör du loggas in automatiskt till den ProProfs-kunskaps bas som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ProProfs Knowledge Base kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).