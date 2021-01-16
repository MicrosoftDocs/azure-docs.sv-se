---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med internationella SOS-hjälp produkter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och internationella SOS-hjälp produkter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 2a654e8934b52909a52ef4179948bc5bf8b0f37c
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247659"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-international-sos-assistance-products"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med hjälp av internationella SOS-hjälp produkter

I den här självstudien får du lära dig hur du integrerar internationella SOS-hjälp produkter med Azure Active Directory (Azure AD). När du integrerar internationella SOS-hjälp produkter med Azure AD kan du:

* Kontroll i Azure AD som har till gång till internationella SOS-hjälp produkter.
* Gör det möjligt för användarna att logga in automatiskt till internationella SOS-hjälp produkter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Internationella SOS Assistance Products-produkter enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Internationella SOS-hjälp produkter stöder **SP** -INITIERAd SSO

* International SOS Assistance-produkter stöder **just-in-Time** User-etablering


## <a name="adding-international-sos-assistance-products-from-the-gallery"></a>Lägga till internationella SOS Assistance-produkter från galleriet

Om du vill konfigurera integrering av internationella SOS-Assistance-produkter i Azure AD måste du lägga till internationella SOS-Assistance-produkter från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet skriver du** in **internationella SOS Assistance-produkter** i sökrutan.
1. Välj **internationella SOS-hjälp produkter** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-international-sos-assistance-products"></a>Konfigurera och testa Azure AD SSO för internationella SOS-hjälp produkter

Konfigurera och testa Azure AD SSO med internationella SOS Assistance-produkter med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i internationella SOS-hjälp produkter.

Utför följande steg för att konfigurera och testa Azure AD SSO med internationella SOS Assistance-produkter:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera de internationella SOS Assistance-produkterna SSO](#configure-international-sos-assistance-products-sso)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa internationella SOS Assistance-produkter testa användaren](#create-international-sos-assistance-products-test-user)** för att få en motsvarighet till B. Simon i internationella SOS-biståndsprodukter som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **International SOS Assistance Products** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.outsystemsenterprise.com/myassist`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.internationalsos.com/sso/saml2/<CUSTOM_ID>`

    c. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.okta.com/saml2/service-provider/<IN>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare. Kontakta [International SOS Assistance Products support team](mailto:onlinehelp@internationalsos.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till internationella SOS-hjälp produkter.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **internationella SOS Assistance-produkter** i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-international-sos-assistance-products-sso"></a>Konfigurera internationella SOS Assistance-produkter SSO

Om du vill konfigurera enkel inloggning på **produkter från internationella SOS-assistans** måste du skicka **URL: en för appens Federations-metadata** till [internationella SOS Assistance Products support-team](mailto:onlinehelp@internationalsos.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-international-sos-assistance-products-test-user"></a>Skapa test användare av internationella SOS-hjälp produkter

I det här avsnittet skapas en användare som kallas Britta Simon i internationella SOS Assistance-produkter. Internationella SOS Assistance-produkter stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i internationella SOS-hjälp produkter skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till de internationella SOS-hjälp produkternas inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till internationella SOS hjälp Products inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen internationella SOS för att få hjälp med produkter i Mina appar omdirigeras den till internationella SOS för hjälp produkter till inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat internationella SOS Assistance-produkter kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


