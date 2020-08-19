---
title: 'Självstudie: Azure Active Directory integrering med Costpoint | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 1db1589561968cf1e2974edcee2bfe1cccb4a327
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549259"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Självstudie: integrera Costpoint med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Costpoint med Azure Active Directory (Azure AD). När du integrerar Costpoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Costpoint.
* Gör det möjligt för användarna att logga in automatiskt till Costpoint med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Costpoint-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kommer du att konfigurera och testa Azure AD SSO i en test miljö. Costpoint stöder **SP-och IDP** -initierad SSO.

## <a name="generate-costpoint-metadata"></a>Generera Costpoint metadata

Costpoint SAML SSO-konfiguration beskrivs i **DeltekCostpoint711Security.pdfs ** guiden. Hämta den här guiden från Deltek Costpoint Support Site och se **installations programmet för SAML enkel inloggning**  >  **Konfigurera SAML enkel inloggning mellan Costpoint och Microsoft Azure** avsnittet. Följ instruktionerna och generera en **XML-fil för COSTPOINT SP-federationsmetadata** . 

![Konfigurations verktyg för Costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Lägg till Costpoint från galleriet

Om du vill integrera Costpoint med Azure AD lägger du först till Costpoint i listan över hanterade SaaS-appar från galleriet i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.

1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .

   ![Azure Active Directory-knappen](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

   ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Välj **nytt program**om du vill lägga till ett nytt program.

   ![Knappen Nytt program](common/add-new-app.png)

1. I avsnittet **Lägg till från galleriet** , ange **Costpoint** i sökrutan.

   ![Costpoint i resultat listan](common/search-new-app.png)

1. I listan resultat väljer du **Costpoint**och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Konfigurera och testa Azure AD Single SGN

Konfigurera och testa Azure AD SSO med Costpoint med hjälp av en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Costpoint.

Om du vill konfigurera och testa Azure AD SSO med Costpoint, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
1. **[Konfigurera Costpoint](#configure-costpoint)** för att konfigurera inställningar för SAML SSO på program sidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Skapa en Costpoint-testanvändare](#create-a-costpoint-test-user)** som ska ha en motsvarighet till B. Simon i Costpoint som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. På sidan **Costpoint** Application Integration väljer du **enkel inloggning**.

   ![Konfigurera länken för enkel inloggning](common/select-sso.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har *metadata-filen för tjänste leverantören*, Slutför följande steg:

   > [!NOTE]
   > Du hämtar metadata-filen för tjänst leverantören i [generera metadata för Costpoint](#generate-costpoint-metadata). Hur du använder filen beskrivs senare i självstudien.
 
   1. Välj knappen **Ladda upp metadatafil** och välj **XML-filen för Costpoint SP-federationsmetadata** som tidigare genererades av Costpoint och välj sedan knappen **Lägg till** för att ladda upp filen.

      ![Ladda upp metadatafilen](./media/costpoint-tutorial/upload-metadata.png)
    
   1. När metadatafilen har laddats upp fylls **ID** och **svars-URL** -värden automatiskt i Costpoint-avsnittet.

      > [!NOTE]
      > Om **ID** -och **svars-URL** -värdena inte är automatiska polulated anger du värdena manuellt enligt ditt krav. Verifiera att **identifieraren (entitets-ID)** och **svars-URL (intygets konsument tjänst-URL)** är korrekt inställda och att **ACS-URL:** en är en giltig Costpoint-URL som slutar med **/LoginServlet.CPS**.

   1. Välj **Ange ytterligare URL: er**. För **relä tillstånd**anger du ett värde med hjälp av följande mönster: `system=[your system]` (till exempel **system = DELTEKCP**).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **kopierings** ikonen för att kopiera **URL: en för appens Federations-metadata** och spara den i anteckningar.

   ![SAML-signeringscertifikat](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Konfigurera Costpoint

1. Återgå till konfigurations verktyget för Costpoint. I text rutan **XML-IDP för federationsmetadata** klistrar du in innehållet i URL-filen för *app Federation-Metadata* . 

   ![Konfigurations verktyg för Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Fortsätt med instruktionerna från **DeltekCostpoint711Security.pdfs ** guiden för att avsluta SAML-installationen av Costpoint.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Syftet med det här avsnittet är att skapa en test användare i Azure Portal med namnet B. Simon.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

   ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

1. Välj **Ny användare**.

   ![Knappen Ny användare](common/new-user.png)

1. I **användar** egenskaperna slutför du de här stegen:

   ![Dialogrutan Användare](common/user-properties.png)

   1. I fältet **namn** anger du **B. Simon**.
   
   1. I fältet **användar namn** anger du `b.simon\@yourcompanydomain.extension` (till exempel B.Simon@contoso.com ).
   
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i fältet **lösen ord** .
   
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja B. Simon åtkomst till Costpoint.

1. I Azure Portal väljer du **företags program**  >  **alla program**.

1. I listan program väljer du **Costpoint**.

1. I avsnittet **Hantera** på appens översikts sida väljer **du användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon**i listan **användare** . Välj sedan **Välj**.

1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och väljer sedan **Välj**.

1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-costpoint-test-user"></a>Skapa en Costpoint-test användare

I det här avsnittet skapar du en användare i Costpoint. Anta att användar-ID: t är **b. Simon** och att användarens namn är **b. Simon**. Arbeta med [Costpoint-klientens support team](https://www.deltek.com/about/contact-us) för att lägga till användaren på Costpoint-plattformen. Användaren måste skapas och aktive ras innan de kan använda enkel inloggning.

När användaren har skapats måste du välja användarens **autentiseringsmetod** **Active Directory**, kryss rutan **SAML-enkel inloggning** måste vara markerad och användar namnet från Azure Active Directory måste vara **Active Directory eller certifikat-ID** (visas i följande skärm bild).

![Costpoint-användare](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Costpoint på åtkomst panelen, bör du loggas in automatiskt till Costpoint-programmet eftersom du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista med självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
