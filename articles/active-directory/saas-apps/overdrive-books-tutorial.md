---
title: 'Självstudie: Azure Active Directory integration med överdrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och överdrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 390ccfa828ebaa0d3dec15990ffeb60cbb24360e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648602"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>Självstudie: Azure Active Directory integrering med överdrive

I den här självstudien får du lära dig att integrera överdriven med Azure Active Directory (Azure AD). När du integrerar överenhet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till överenhet.
* Gör det möjligt för användarna att logga in automatiskt till överdrivet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En överdriven enkel inloggning (SSO)-aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Överdrive stöder **SP** -initierad SSO.

* Överdrive stöder **just-in-Time** User-etablering.

## <a name="add-overdrive-from-the-gallery"></a>Lägg till överdrive från galleriet

Om du vill konfigurera integrering av överdrivet i Azure AD lägger du till överdrive från galleriet i listan över hanterade SaaS-appar genom att göra följande:
 
1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **överenhet** i sökrutan.
1. I resultat fönstret väljer du **överenhet** och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>Konfigurera och testa Azure AD SSO för överdrive

Konfigurera och testa Azure AD SSO med överenhet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i överenhet.

Utför följande steg för att konfigurera och testa Azure AD SSO med överenhet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera överdrivet SSO](#configure-overdrive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa överdrivet test användare](#create-overdrive-test-user)** – för att få en motsvarighet till B. Simon i överdriven som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **överenhets** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [överdrivet klient support team](https://help.overdrive.com/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På avsnittet **Konfigurera överenhet** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till överdrivet.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **överenhet**.

2. I listan program väljer du **överenhet**.

3. På menyn till vänster väljer du **Användare och grupper**.

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-overdrive-sso"></a>Konfigurera överdrivet SSO

Om du vill konfigurera enkel inloggning på **överenhets** sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [överdrive support team](https://help.overdrive.com/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-overdrive-test-user"></a>Skapa överdrivet test användare

I det här avsnittet skapas en användare som heter Britta Simon i överdriven. Överdrive stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i överdrive skapas en ny efter autentiseringen.

>[!NOTE]
>Du kan använda andra verktyg för att skapa överdrivet användar konton eller API: er som tillhandahålls av överdrivet för att etablera Azure AD-användarkonton.
>

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till överdrivet inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till överdriven inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på överenhets panelen i Mina appar omdirigeras den till överdriven inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat överdriven kan du framtvinga kontroll över sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).