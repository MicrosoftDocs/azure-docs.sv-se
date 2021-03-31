---
title: 'Självstudie: Azure Active Directory integrering med LogicMonitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649435"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudie: Azure Active Directory integrering med LogicMonitor

I den här självstudien får du lära dig hur du integrerar LogicMonitor med Azure Active Directory (Azure AD). När du integrerar LogicMonitor med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LogicMonitor.
* Gör det möjligt för användarna att logga in automatiskt till LogicMonitor med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LogicMonitor behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* LogicMonitor-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LogicMonitor stöder **SP** -INITIERAd SSO

## <a name="add-logicmonitor-from-the-gallery"></a>Lägg till LogicMonitor från galleriet

Om du vill konfigurera integreringen av LogicMonitor i Azure AD måste du lägga till LogicMonitor från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **LogicMonitor** i sökrutan.
1. Välj **LogicMonitor** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Konfigurera och testa Azure AD SSO för LogicMonitor

Konfigurera och testa Azure AD SSO med LogicMonitor med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LogicMonitor.

Utför följande steg för att konfigurera och testa Azure AD SSO med LogicMonitor:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LOGICMONITOR SSO](#configure-logicmonitor-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa LogicMonitor test User](#create-logicmonitor-test-user)** -om du vill ha en motsvarighet till B. Simon i LogicMonitor som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **LogicMonitor** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för LogicMonitor-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`
    
    c. I text rutan **svars-URL (intygs mottagar tjänst)** anger du en URL med följande mönster: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [LogicMonitor client support team](https://www.logicmonitor.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera LogicMonitor** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LogicMonitor.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **LogicMonitor**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-logicmonitor-sso"></a>Konfigurera LogicMonitor SSO

1. Logga in på din **LogicMonitor** -företags webbplats som administratör.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/logicmonitor-tutorial/ic790052.png "Inställningar")

3. I navigerings-bat till vänster klickar du på **enkel inloggning**.

    ![Enkel inloggning](./media/logicmonitor-tutorial/ic790053.png "för Aha!")

4. I avsnittet **Inställningar för enkel inloggning (SSO)** , utför följande steg:

    ![Inställningar för enskilda Sign-On](./media/logicmonitor-tutorial/ic790054.png "Inställningar för enskilda Sign-On")

    a. Välj **aktivera enkel inloggning**.

    b. Som **standard roll tilldelning** väljer du **skrivskyddad**.

    c. Öppna den hämtade metadata-filen i anteckningar och klistra sedan in innehållet i filen i text rutan för **identitetsprovider** .

    d. Klicka på **Spara ändringar**.

### <a name="create-logicmonitor-test-user"></a>Skapa LogicMonitor test användare

För att Azure AD-användare ska kunna logga in måste de vara etablerade i LogicMonitor-programmet med sina Azure Active Directory användar namn.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din LogicMonitor-företags webbplats som administratör.

2. I menyn högst upp klickar du på **Inställningar** och sedan på **roller och användare**.

    ![Roller och användare](./media/logicmonitor-tutorial/ic790056.png "Roller och användare")

3. Klicka på **Lägg till**.

4. I avsnittet **Lägg till ett konto** utför du följande steg:

    ![Lägga till ett konto](./media/logicmonitor-tutorial/ic790057.png "Lägga till ett konto")

    a. Ange **användar namn**, **e-post**, **lösen ord** och **lösen ords** värden för den Azure Active Directory användare som du vill etablera i de relaterade text rutorna.

    b. Välj **roller**, **Visa behörigheter** och **status**.

    c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda andra verktyg för LogicMonitor av användar konton eller API: er som tillhandahålls av LogicMonitor för att etablera Azure Active Directory användar konton.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till LogicMonitor-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till LogicMonitor-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen LogicMonitor i Mina appar, bör du loggas in automatiskt på den LogicMonitor som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat LogicMonitor kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).