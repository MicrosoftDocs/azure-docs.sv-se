---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ContractSafe Saml2 SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 9459d13e8943f7e9177d2ad493873257f46f6649
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544407"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Självstudie: integrera Azure Active Directory enkel inloggning (SSO) med ContractSafe Saml2 SSO

I den här självstudien får du lära dig att integrera ContractSafe Saml2 SSO med Azure Active Directory (Azure AD). När du integrerar ContractSafe Saml2 SSO med Azure AD kan du:

* Kontrol lera vem som har åtkomst till ContractSafe Saml2 SSO i Azure AD.
* Gör det möjligt för användarna att automatiskt logga in på ContractSafe Saml2 SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS-appar (Software as a Service) med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ContractSafe Saml2 SSO-prenumeration med SSO aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. ContractSafe Saml2 SSO stöder **IDP**-initierad SSO.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Lägg till ContractSafe Saml2 SSO från galleriet

Om du vill konfigurera integreringen av ContractSafe Saml2 SSO i Azure AD måste du lägga till ContractSafe Saml2 SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) genom att använda antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ContractSafe Saml2 SSO** i sökrutan.
1. Välj **ContractSafe SAML2 SSO** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Konfigurera och testa Azure AD SSO för ContractSafe Saml2 SSO

Konfigurera och testa Azure AD SSO med ContractSafe Saml2 SSO genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ContractSafe Saml2 SSO.

Om du vill konfigurera och testa Azure AD SSO med ContractSafe Saml2 SSO, fyller du i följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
   * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med hjälp av **B. Simon** -kontot.
   * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera **B. Simon** att använda Azure AD SSO.

1. [Konfigurera ContractSafe SAML2 SSO](#configure-contractsafe-saml2-sso) för att konfigurera SSO-inställningarna på program sidan.
   * [Skapa en ContractSafe SAML2 SSO-test](#create-a-contractsafe-saml2-sso-test-user) för att få en motsvarighet till **B. Simon** i ContractSafe Saml2 SSO som är länkad till användarens Azure AD-representation.
2. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **ContractSafe Saml2 SSO** -programintegration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen Redigera (PEN) för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du följande värden i motsvarande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande format: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. I text rutan **svars-URL** anger du en URL med hjälp av följande format: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Kontakta [ContractSafe SAML2 SSO client support team](mailto:support@contractsafe.com) för att hämta dessa värden. Du kan också se de format som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. ContractSafe Saml2 SSO förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Vanliga standardattribut](common/default-attributes.png)

1. Förutom standardattributen, förväntar sig ett antal attribut att skickas tillbaka i SAML-svaret i ContractSafe Saml2 SSO-programmet. Dessa attribut fylls i i förväg, men du kan granska dem enligt dina krav. I följande lista visas ytterligare attribut.

    | Name | Källattribut|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | e-post | User. onpremisesuserprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera ContractSafe SAML2 SSO** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas **B. Simon**.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare**och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du en e-postadress i `username@companydomain.extension` formatet. Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

## <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera **B. Simon** för att använda Azure SSO genom att bevilja åtkomst till CONTRACTSAFE Saml2 SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ContractSafe SAML2 SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer sedan **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** . Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurera ContractSafe Saml2 SSO

Om du vill konfigurera SSO på **inloggnings sidan för ContractSafe Saml2** måste du skicka den hämtade **XML-koden för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Teamet ansvarar för att ställa in SAML SSO-anslutningen korrekt på båda sidor.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Skapa en ContractSafe Saml2 SSO-test användare

Skapa en användare som kallas B. Simon i ContractSafe Saml2 SSO. Arbeta med [ContractSafe SAML2 SSO-support teamet](mailto:support@contractsafe.com) för att lägga till användarna på CONTRACTSAFE Saml2 SSO-plattformen. Användare måste skapas och aktive ras innan du använder SSO.

## <a name="test-sso"></a>Testa SSO

Testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen. När du väljer ContractSafe Saml2 SSO-panelen på åtkomst panelen, bör du loggas in automatiskt till ContractSafe Saml2 SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ContractSafe Saml2 SSO med Azure AD](https://aad.portal.azure.com/)
