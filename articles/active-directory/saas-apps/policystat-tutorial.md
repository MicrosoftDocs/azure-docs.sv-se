---
title: 'Självstudie: Azure Active Directory integrering med PolicyStat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5c2520c8e209ab8319cbc5a369b70d247a52232c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601006"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudie: Azure Active Directory integrering med PolicyStat

I den här självstudien lär du dig att integrera PolicyStat med Azure Active Directory (Ad Azure).
Integreringen av PolicyStat med Azure AD ger dig följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till PolicyStat.
* Du kan göra så att dina användare automatiskt loggas in på PolicyStat (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med PolicyStat behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD US Government Cloud-miljön. Du hittar det här programmet i Azure AD US Government Cloud Application Gallery och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PolicyStat stöder **SP-initierad** enkel inloggning

* PolicyStat stöder **just-in-time-användareablering**

## <a name="adding-policystat-from-the-gallery"></a>Lägga till PolicyStat från galleriet

För att konfigurera integreringen av PolicyStat i Azure AD måste du lägga till PolicyStat från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PolicyStat från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **PrincipStat,** väljer **PrincipStat** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![PolicyStat i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med PolicyStat baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PolicyStat upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med PolicyStat behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för PolicyStat](#configure-policystat-single-sign-on)** – för att konfigurera inställningarna Sign-On enkel inloggning på programsidan.
3. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
4. **[Skapa PolicyStat-testanvändare](#create-policystat-test-user)** – för att ha en motsvarighet för Britta Simon i PolicyStat som är länkad till En Azure AD-representation av användaren.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med PolicyStat:

1. I [Azure Portal](https://portal.azure.com/)programintegreringssidan **för PolicyStat** väljer **du Enkel inloggning.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PolicyStat-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för PolicyStat och](https://rldatix.com/services-support/support) be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Ditt PolicyStat-program förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka **på ikonen** Redigera för att öppna dialogrutan **Användarattribut.**

    ![Skärmbild som visar dialogrutan Användarattribut med ikonen "Redigera" markerad.](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig PolicyStat-programmet att några fler attribut skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.
    
    ![Skärmbild som visar avsnittet "Använda anspråk" med åtgärderna "Lägg till nytt anspråk" och "Spara" markerade.](common/new-save-attribute.png)

    ![Skärmbild som visar dialogrutan "Hantera användaranspråk" med textrutorna "Namn", "Transformering" och "Parameter" markerade och knappen "Spara" markerad.](./media/policystat-tutorial/attribute01.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Transformering.**

    e. I listan **Transformering** skriver du det attributvärde som visas för den raden.
    
    f. I listan **Parameter 1** skriver du det attributvärde som visas för den raden.

    ex. Klicka på **Spara**.

7. I **avsnittet Konfigurera PolicyStat** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-policystat-single-sign-on&quot;></a>Konfigurera enkel Sign-On

1. I ett annat webbläsarfönster loggar du in på din PolicyStat-företagswebbplats som administratör.

2. Klicka på **fliken** Admin och sedan på **Enkel konfiguration Sign-On i** det vänstra navigeringsfönstret.
   
    ![Administratörsmeny](./media/policystat-tutorial/ic808633.png &quot;Administratörsmeny")

3. Klicka **på Dina IDP-metadata** och utför sedan följande steg i avsnittet Dina **IDP-metadata:**
   
    ![Skärmbild som visar åtgärden "Dina I D P-metadata" markerad.](./media/policystat-tutorial/ic808636.png "Konfiguration Sign-On enstaka konfiguration")
   
    a. Öppna den nedladdade metadatafilen, kopiera innehållet och klistra in det i **textrutan Metadata för din identitetsprovider.**

    b. Klicka **på Spara ändringar.**

4. Klicka **på Konfigurera** attribut och utför sedan **följande** steg i avsnittet Konfigurera attribut:
   
    a. I **textrutan Användarnamnattribut** skriver du **uid**.

    b. I **textrutan Förnamnsattribut** skriver du anspråksnamnet för förnamnsattributet från Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. I **textrutan Efternamnsattribut** skriver du anspråksnamnet för efternamnsattributet från Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. I **textrutan E-postattribut** skriver du anspråksnamnet för e-postattributet från Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Klicka **på Spara ändringar.**

5. I **avsnittet Installation** väljer du **Enable Single Sign-on Integration**(Aktivera integrering med enkel inloggning).
   
    ![Konfiguration Sign-On enskild enhet](./media/policystat-tutorial/ic808634.png "Konfiguration Sign-On enstaka konfiguration")


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för ditt eget konto att använda enkel inloggning med Azure genom att ge åtkomst till PolicyStat.

1. I Azure Portal väljer du **Företagsprogram,** **Alla program** och sedan **PrincipStat.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **PolicyStat**.

    ![PolicyStat-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och** grupper väljer du ditt konto  i listan Användare och klickar sedan på knappen Välj längst ned på skärmen.

6. Om du förväntar dig något rollvärde i  SAML-försäkran väljer du i dialogrutan Välj roll  lämplig roll för användaren i listan och klickar sedan på knappen Välj längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-policystat-test-user"></a>Skapa PolicyStat-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i PolicyStat. PolicyStat stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i PolicyStat skapas en ny efter autentisering.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för PolicyStat-kontoskapande som tillhandahålls av PolicyStat för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på PolicyStat-panelen i Åtkomstpanelen bör du automatiskt loggas in på PolicyStat som du har ställt in enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
