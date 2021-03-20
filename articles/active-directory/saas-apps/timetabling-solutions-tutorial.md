---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Timetabling-lösningar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Timetabling-lösningar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.author: jeedes
ms.openlocfilehash: 527c0ce1278c920f53a2f63b7708bce09da789e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516363"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timetabling-solutions"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Timetabling-lösningar

I den här självstudien får du lära dig att integrera Timetabling-lösningar med Azure Active Directory (Azure AD). När du integrerar Timetabling-lösningar med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Timetabling-lösningar.
* Gör det möjligt för användarna att logga in automatiskt till Timetabling-lösningar med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Timetabling-lösningar enkel inloggning (SSO) aktive rad prenumeration.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Timetabling-lösningar har stöd för **SP** -INITIERAd SSO
* När du har konfigurerat Timetabling-lösningar kan du framtvinga kontroll av sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-timetabling-solutions-from-the-gallery"></a>Lägga till Timetabling-lösningar från galleriet

Om du vill konfigurera integreringen av Timetabling-lösningar i Azure AD måste du lägga till Timetabling-lösningar från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Timetabling-lösningar** i sökrutan.
1. Välj **Timetabling-lösningar** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-timetabling-solutions"></a>Konfigurera och testa enkel inloggning med Azure AD för Timetabling-lösningar

Konfigurera och testa Azure AD SSO med Timetabling-lösningar med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Timetabling-lösningar.

Om du vill konfigurera och testa Azure AD SSO med Timetabling-lösningar slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Timetabling-lösningar SSO](#configure-timetabling-solutions-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Timetabling-lösningar testa användare](#create-timetabling-solutions-test-user)** – för att få en motsvarighet till B. Simon i Timetabling-lösningar som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Timetabling Solutions** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://auth.timetabling.education/login`

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera Timetabling-lösningar** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Timetabling-lösningar.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Timetabling-lösningar**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-timetabling-solutions-sso"></a>Konfigurera Timetabling-lösningar SSO

Om du vill konfigurera enkel inloggning på **Timetabling lösnings** sidan måste du skicka **tumavtrycket** och lämpliga kopierade url: er från Azure Portal till [support teamet för Timetabling-lösningar](https://www.timetabling.com.au/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-timetabling-solutions-test-user"></a>Skapa Timetabling-lösningar test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Timetabling-lösningar. Arbeta med [support teamet för Timetabling Solutions](https://www.timetabling.com.au/contact-us/) för att lägga till användare på Timetabling-lösnings plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Timetabling lösningar på åtkomst panelen, bör du loggas in automatiskt på de Timetabling-lösningar som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Timetabling-lösningar med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Timetabling-lösningar med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)