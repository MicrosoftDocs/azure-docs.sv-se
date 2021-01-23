---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med RetrieverMediaDatabase | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RetrieverMediaDatabase.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 786925799d19bf01e8edebbf85de04d92213298b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732854"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retrievermediadatabase"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med RetrieverMediaDatabase

I den här självstudien får du lära dig hur du integrerar RetrieverMediaDatabase med Azure Active Directory (Azure AD). När du integrerar RetrieverMediaDatabase med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RetrieverMediaDatabase.
* Gör det möjligt för användarna att logga in automatiskt till RetrieverMediaDatabase med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* RetrieverMediaDatabase för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RetrieverMediaDatabase stöder **IDP** INITIERAd SSO

## <a name="adding-retrievermediadatabase-from-the-gallery"></a>Lägga till RetrieverMediaDatabase från galleriet

Om du vill konfigurera integreringen av RetrieverMediaDatabase i Azure AD måste du lägga till RetrieverMediaDatabase från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **RetrieverMediaDatabase** i sökrutan.
1. Välj **RetrieverMediaDatabase** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-retrievermediadatabase"></a>Konfigurera och testa Azure AD SSO för RetrieverMediaDatabase

Konfigurera och testa Azure AD SSO med RetrieverMediaDatabase med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RetrieverMediaDatabase.

Utför följande steg för att konfigurera och testa Azure AD SSO med RetrieverMediaDatabase:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RETRIEVERMEDIADATABASE SSO](#configure-retrievermediadatabase-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa RetrieverMediaDatabase test User](#create-retrievermediadatabase-test-user)** -om du vill ha en motsvarighet till B. Simon i RetrieverMediaDatabase som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **RetrieverMediaDatabase** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RetrieverMediaDatabase.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **RetrieverMediaDatabase**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-retrievermediadatabase-sso"></a>Konfigurera RetrieverMediaDatabase SSO

Om du vill konfigurera enkel inloggning på **RetrieverMediaDatabase** sida måste du skicka **URL: en för appens Federations-metadata** till [support teamet för RetrieverMediaDatabase](mailto:support@retriever.nl). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-retrievermediadatabase-test-user"></a>Skapa RetrieverMediaDatabase test användare

I det här avsnittet skapar du en användare som heter Britta Simon i RetrieverMediaDatabase. Arbeta med [RetrieverMediaDatabase support team](mailto:support@retriever.nl) för att lägga till användare i RetrieverMediaDatabase-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

1. Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den RetrieverMediaDatabase som du har konfigurerat SSO för.

1. Du kan använda Microsoft Mina appar. När du klickar på panelen RetrieverMediaDatabase i Mina appar, bör du loggas in automatiskt på den RetrieverMediaDatabase som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat RetrieverMediaDatabase kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).