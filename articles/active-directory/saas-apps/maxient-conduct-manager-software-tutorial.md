---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Maxient utföra Manager-programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Maxient utföra Manager-programvara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: dd6872fd8dca3f29b61c6f1dffb5f219abac5cb8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518918"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Maxient utföra Manager-programvara

I den här självstudien får du lära dig hur du integrerar Maxient med program vara med Azure Active Directory (Azure AD). När du integrerar Maxient Driver Manager med Azure AD kan du:

* Använd Azure AD för att autentisera dina användare för Maxient utföra Manager-programvaran
* Gör det möjligt för användarna att logga in automatiskt till Maxient utföra Manager-programvara med sina Azure AD-konton.


Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Maxient gör det enkelt att logga in med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du din Azure AD för användning med Maxient utföra Manager-programvara.


* Maxient utföra Manager-programvara stöder **SP-och IDP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Lägga till Maxient utföra Manager-programvara från galleriet

Om du vill konfigurera integrering av Maxient med Manager-programvara i Azure AD måste du lägga till Maxient Manager-programvara från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Maxient Driver Manager-programvara** i sökrutan.
1. Välj **Maxient utför Manager-programvara** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Konfigurera och testa enkel inloggning med Azure AD för Maxient utföra Manager-programvara

Konfigurera och testa Azure AD SSO med Maxient utföra Manager-programvara. För att SSO ska fungera måste du upprätta en anslutning mellan Azure AD och Maxient utföra Manager-programvaran.

Om du vill konfigurera och testa Azure AD SSO med Maxient utföra Manager-programvara slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan autentiseras för användning med Maxient utföra Manager-programvaran
    1. **[Tilldela alla användare att använda Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** – så att alla på din institution kan autentisera sig.
1. **[Testa Azure AD-installationen med Maxient](#test-with-maxient)** – för att kontrol lera om konfigurationen fungerar och rätt attribut släpps

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Maxient utföra Manager Software** Application Integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration**   är programmet förkonfigurerat i **IDP**   initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara**   .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Arbeta med din Maxient-implementering/support-representant för att hämta värdet.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.  Du måste ange din Maxient-implementering/support-representant med denna URL.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Tilldela alla användare för att kunna autentisera sig för Maxient utföra Manager-programvaran

I det här avsnittet får du beviljar åtkomst för alla konton att autentisera med hjälp av Azure-systemet för Maxient utföra Manager-programvaran.  Det är viktigt att Observera att det här steget **krävs** för att Maxient ska fungera korrekt.  Maxient utnyttjar ditt Azure AD-system för att *autentisera* användare. *Auktoriseringen* av användare utförs i Maxient-systemet för den specifika funktion som de försöker utföra. Maxient använder inte attribut från din katalog för att fatta beslut.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Maxient Driver Manager-programvara**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du alla användare (eller lämpliga grupper) och **tilldelar** dem att kunna autentisera med Maxient.

## <a name="test-with-maxient"></a>Testa med Maxient 

Om ett support ärende inte redan har öppnats med en Maxient implementering/support-representant, skickar du ett e-postmeddelande till [support@maxient.com](mailto:support@maxient.com) med ämnet "Campus-baserad autentisering/Azure-installation- \<\<School Name\> \> ". I e-postmeddelandets brödtext anger du **URL: en för appens federationens metadata**. Maxient personal kommer att svara med en test länk för att kontrol lera att rätt attribut släpps.  
    
## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Maxient Driver Manager-programvara med Azure AD](https://aad.portal.azure.com/)

