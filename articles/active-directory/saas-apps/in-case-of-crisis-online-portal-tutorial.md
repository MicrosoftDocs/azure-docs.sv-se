---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) i händelse av kris-online-portalen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och i händelse av kris-online-portalen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 124b53297c04d3c9958a233cf40f56ecda57bbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---online-portal"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med i händelse av kris – online portal

I den här självstudien får du lära dig att integrera i händelse av kris-online-portalen med Azure Active Directory (Azure AD). När du integrerar i händelse av kris-online-portalen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till i händelse av kris-online-portalen.
* Gör det möjligt för användarna att logga in automatiskt till i händelse av kris-online-portalen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* I händelse av kris-online portal aktive rad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* I händelse av kris-online-portalen stöds **IDP** -INITIERAd SSO
* När du har konfigurerat i händelse av en kris-online-portal kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-in-case-of-crisis---online-portal-from-the-gallery"></a>Lägga till i händelse av kris-online-portalen från galleriet

Om du vill konfigurera integreringen av i händelse av kris-online-portalen i Azure AD måste du lägga till i händelse av kris-online-portalen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **i händelse av kris-online-portalen** i sökrutan.
1. Välj **i händelse av kris – online portal** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---online-portal"></a>Konfigurera och testa enkel inloggning med Azure AD för i händelse av kris-online-portalen

Konfigurera och testa Azure AD SSO med i händelse av kris-online-portalen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i i händelse av kris-online-portalen.

Om du vill konfigurera och testa Azure AD SSO med i händelse av kris-online portal, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. Konfigurera om du vill konfigurera inställningar för enkel inloggning på program Sidan **[i kris online portal SSO](#configure-in-case-of-crisis-online-portal-sso)** .
    * Skapa om du vill att en **[test användare på en kris online portal](#create-in-case-of-crisis-online-portal-test-user)** ska ha en motsvarighet till B. Simon i om det gäller kris-online-portalen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan om program integrering **online portal** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet Konfigurera om du vill **ha en kris online-portal** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till i händelse av kris-online-portalen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **i händelse av kris-online-portalen**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-in-case-of-crisis-online-portal-sso"></a>Konfigurera i händelse av kris online portal SSO

Om du vill konfigurera enkel inloggning på en sida **för kris-online-portalen** måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [i händelse av krisen support team online portal](mailto:support@rockdovesolutions.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-in-case-of-crisis-online-portal-test-user"></a>Skapa om du vill testa en kris online portal

I det här avsnittet skapar du en användare som heter B. Simon i i händelse av kris-online-portalen. Arbeta med [i händelse av kris – online portal support team](mailto:support@rockdovesolutions.com) för att lägga till användarna i om det rör sig om en kris-online portal plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för kris-online-portalen på åtkomst panelen, bör du loggas in automatiskt till i händelse av kris-online-portalen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Försök i händelse av kris-online-portalen med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)