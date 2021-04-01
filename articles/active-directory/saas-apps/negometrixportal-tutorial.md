---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med NegometrixPortal enkel inloggning (SSO) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NegometrixPortal enkel inloggning (SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: d972868cf9c5d67824eab781bc99a7cac5f7b313
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92507150"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-negometrixportal-single-sign-on-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning (SSO) med NegometrixPortal

I den här självstudien får du lära dig att integrera NegometrixPortal enkel inloggning (SSO) med Azure Active Directory (Azure AD). När du integrerar NegometrixPortal enkel inloggning (SSO) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NegometrixPortal enkel inloggning (SSO).
* Gör det möjligt för användarna att logga in automatiskt till NegometrixPortal enkel inloggning (SSO) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* NegometrixPortal för enkel inloggning (SSO) för enkel inloggning (SSO) med en aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* NegometrixPortal enkel inloggning (SSO) stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-negometrixportal-single-sign-on-sso-from-the-gallery"></a>Lägga till NegometrixPortal enkel inloggning (SSO) från galleriet

Om du vill konfigurera integrering av NegometrixPortal enkel inloggning i Azure AD måste du lägga till NegometrixPortal enkel inloggning (SSO) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **NegometrixPortal enkel inloggning (SSO)** i sökrutan.
1. Välj **NegometrixPortal enkel inloggning (SSO)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-negometrixportal-single-sign-on-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för NegometrixPortal enkel inloggning (SSO)

Konfigurera och testa Azure AD SSO med NegometrixPortal enkel inloggning (SSO) med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i NegometrixPortal enkel inloggning (SSO).

Om du vill konfigurera och testa Azure AD SSO med NegometrixPortal enkel inloggning (SSO), slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera NegometrixPortal enkel inloggning (SSO) SSO](#configure-negometrixportal-single-sign-on-sso-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa NegometrixPortal för enkel inloggning (SSO)](#create-negometrixportal-single-sign-on-sso-test-user)** för att få en motsvarighet till B. Simon i NegometrixPortal enkel inloggning (SSO) som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan NegometrixPortal för **enkel inloggning (SSO)** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://portal.negometrix.com/sso/<CUSTOMURL>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [NegometrixPortal-support teamet för enkel inloggning (SSO)](mailto:sander.hoek@negometrix.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NegometrixPortal-programmet för enkel inloggning förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig NegometrixPortal-programmet för enkel inloggning (SSO) några fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------|  --------- |
    | UPN | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NegometrixPortal enkel inloggning (SSO).

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **NegometrixPortal enkel inloggning (SSO)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>Konfigurera NegometrixPortal enkel inloggning (SSO) SSO

Om du vill konfigurera enkel inloggning på **NegometrixPortal enkel inloggning (SSO)** , måste du skicka **URL: en för app Federation-Metadata** till [support teamet för enkel inloggning (SSO)](mailto:sander.hoek@negometrix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>Skapa test användare för NegometrixPortal enkel inloggning (SSO)

I det här avsnittet skapar du en användare som heter B. Simon i NegometrixPortal enkel inloggning (SSO). Arbeta med [NegometrixPortal för enkel inloggning (SSO)](mailto:sander.hoek@negometrix.com) för att lägga till användare i NegometrixPortal-plattformen för enkel inloggning (SSO). Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för enkel inloggning på NegometrixPortal (SSO) på åtkomst panelen, bör du loggas in automatiskt till NegometrixPortal enkel inloggning (SSO) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova NegometrixPortal enkel inloggning (SSO) med Azure AD](https://aad.portal.azure.com/)