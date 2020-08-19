---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med NetOp-portalen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netop Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: f316d7bab4b9d5b1f08d7a12cdc892be252128ae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549022"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netop-portal"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med NetOp-portalen

I den här självstudien får du lära dig att integrera NetOp-portalen med Azure Active Directory (Azure AD). När du integrerar NetOp-portalen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NetOp-portalen.
* Gör det möjligt för användarna att logga in automatiskt till NetOp-portalen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* NetOp för enkel inloggning (SSO) i Portal.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Netop Portal har stöd för **IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-netop-portal-from-the-gallery"></a>Lägga till Netop Portal från galleriet

För att konfigurera integreringen av Netop Portal i Azure AD behöver du lägga till Netop Portal från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **NetOp Portal** i sökrutan.
1. Välj **NetOp-portalen** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netop-portal"></a>Konfigurera och testa enkel inloggning med Azure AD för NetOp-portalen

Konfigurera och testa Azure AD SSO med NetOp-portalen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i NetOp-portalen.

Om du vill konfigurera och testa Azure AD SSO med NetOp-portalen slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera NetOp-portalen SSO](#configure-netop-portal-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa NetOp-portalen testa användare](#create-netop-portal-test-user)** – för att få en motsvarighet till B. Simon på NetOp-portalen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **NetOp Portal** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. NetOp-portalen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig NetOp-portalen att fler attribut skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name |  Källattribut|
    | ---------------| --------- |
    | NRC-ACCOUNT-ID | "adfs-demo" |
    | NRC-EMAIL | user.userprincipalname |
    | NRC-GIVEN-NAME | user.givenname |
    | NRC-SURNAME | user.surname |
    | NRC-USERNAME | user.userprincipalname |
    | nameidentifier | user.userprincipalname |
    | | |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera NetOp-portalen** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NetOp-portalen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Netop Portal**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-netop-portal-sso"></a>Konfigurera NetOp-portalen SSO

Om du vill konfigurera enkel inloggning på **NetOp Portal** sida måste du ladda ned den hämtade **XML-koden för federationsmetadata** och inloggnings-URL: en från Azure Portal. Följ anvisningarna i steg 3 i dokumentationen [här](https://kb.netop.com/article/netop-remote-control-portal-adfs-and-azure-ad-integration-456.html) för att konfigurera NetOp-portalen för Azure AD-autentisering.

### <a name="create-netop-portal-test-user"></a>Skapa Netop Portal-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Netop Portal. Arbeta med [NetOp Portal support team](mailto:casemanager@netop.com) för att lägga till användare i NetOp-portalen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Netop Portal-panelen i åtkomstpanelen bör du automatiskt loggas in på Netop Portal som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova NetOp-portalen med Azure AD](https://aad.portal.azure.com/)

