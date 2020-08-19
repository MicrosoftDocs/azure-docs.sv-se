---
title: 'Självstudie: Azure Active Directory integration med JIRA SAML SSO från Microsoft (V 5.2) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och JIRA SAML SSO från Microsoft (V5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: e0198fdcfea1656e3aec5179358e69fb6fb55723
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547589"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Självstudie: Azure Active Directory integration med JIRA SAML SSO från Microsoft (V 5.2)

I den här självstudien får du lära dig hur du integrerar JIRA SAML SSO från Microsoft (V5.2) med Azure Active Directory (Azure AD).
Integreringen av JIRA SAML SSO från Microsoft (V5.2) med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till JIRA SAML SSO från Microsoft (V5.2) från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in i JIRA SAML SSO från Microsoft (V5.2) (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="description"></a>Description

Använd ditt Microsoft Azure Active Directory-konto med Atlassian JIRA-servern för att aktivera enkel inloggning. På så sätt kan alla organisations användare använda Azure AD-autentiseringsuppgifterna för att logga in i JIRA-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med JIRA SAML SSO från Microsoft (V5.2) behöver du följande:

- En Azure AD-prenumeration
- JIRA Core och programvaruversion 5.2 bör vara installerat och konfigurerat för Windows 64-bitarsversionen
- JIRA-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för JIRA-plugin-programmet anges i avsnittet nedan.
- JIRA-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i JIRA
- WebSudo är inaktiverat i JIRA
- Testanvändare har skapats i JIRA-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en JIRA-produktionsmiljö. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få en månads utvärderings version här: [utvärderings](https://azure.microsoft.com/pricing/free-trial/)version.

## <a name="supported-versions-of-jira"></a>Versioner av JIRA som stöds

* JIRA Core och program vara: 5,2
* JIRA stöder också 6.0 till 7.12. Om du vill ha mer information klickar du på [JIRA SAML SSO från Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Observera att vårt JIRA-plugin-program också fungerar på Ubuntu version 16,04

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* JIRA SAML SSO från Microsoft (V5.2) stöder **SP**-initierad enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Lägga till JIRA SAML SSO från Microsoft (V5.2) från galleriet

För att konfigurera integreringen av JIRA SAML SSO från Microsoft (V5.2) med Azure AD måste du lägga till JIRA SAML SSO från Microsoft (V5.2) från galleriet i din lista över hanterade SaaS-appar.

**Lägg till JIRA SAML SSO från Microsoft (V5.2) från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **JIRA SAML SSO från Microsoft (V5.2)**, väljer **JIRA SAML SSO från Microsoft (V5.2)** i resultatrutan och klickar på **Lägg till** för att lägga till programmet.

    ![JIRA SAML SSO från Microsoft (V5.2) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft (V5.2) baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i JIRA SAML SSO från Microsoft (V5.2) upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft (V5.2) måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för JIRA SAML SSO från Microsoft (V5.2)](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för JIRA SAML SSO från Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** – för att skapa en motsvarighet till Britta Simon i JIRA SAML SSO från Microsoft (V5.2) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft (V5.2):

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering av **JIRA SAML SSO från Microsoft (V5.2)** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med domäner och URL:er för JIRA SAML SSO från Microsoft (V5.2)](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<domain:port>/`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira-pluginprogrammet, som beskrivs senare i självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Konfigurera enkel inloggning för JIRA SAML SSO från Microsoft (V5.2)

1. Logga in på din JIRA-instans som administratör i ett annat webbläsarfönster.

2. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon1.png)

3. Klicka på **Hantera tillägg** under fliken Tillägg.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon7.png)

4. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon12.png)

5. När plugin-programmet har installerats visas det i tilläggsavsnittet **Användarinstallerade**. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon13.png)

6. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    a. I textrutan **Metadata-URL** klistrar du in värdet för **URL:en med appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    b. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.

    d. I **SAML-användar-ID platser** väljer **du antingen användar-ID: t i NameIdentifier-elementet för ämnes instruktionen** eller **användar-ID: t finns i ett Attribute-element**.  Detta ID måste vara användar-ID för JIRA. Om användar-ID: t inte matchas kommer systemet inte att tillåta användare att logga in.

    > [!Note]
    > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    e. Om du väljer **användar-ID finns i ett attribut element** , anger du namnet på attributet där användar-ID förväntas i text rutan för **attributets namn** . 

    f. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    ex. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    h. Markera **aktivera enkel utloggning** om du vill logga ut från Azure AD när en användare loggar ut från Jira. 

    i. Klicka på knappen **Spara** för att spara inställningarna.

    > [!NOTE]
    > Mer information om installation och felsökning finns i [administratörsguiden för anslutningsapp för enkel inloggning med MS JIRA](../ms-confluence-jira-plugin-adminguide.md), och du kan läsa [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon\@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till JIRA SAML SSO från Microsoft (V5.2).

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **JIRA SAML SSO från Microsoft (V5.2)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **JIRA SAML SSO från Microsoft (V5.2)**.

    ![Länken för JIRA SAML SSO från Microsoft (V5.2) i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Skapa en testanvändare för JIRA SAML SSO från Microsoft (V5.2)

Om du vill att Azure AD-användare ska kunna logga in på den lokala JIRA-servern måste de tillhandahållas i den lokala JIRA-servern.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din lokala JIRA-server som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomstsidan där du kan ange **lösenord** och klicka på knappen **Bekräfta**.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user2.png)

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user3.png) 

5. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Lägga till medarbetare](./media/jira52microsoft-tutorial/user4.png)

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för JIRA SAML SSO från Microsoft (V5.2) på åtkomstpanelen bör du automatiskt loggas in i det JIRA SAML SSO från Microsoft (V5.2) som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
