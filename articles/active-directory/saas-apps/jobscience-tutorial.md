---
title: 'Självstudie: Azure Active Directory integrering med Jobscience | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jobscience.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0de47cc12c461e47d8703861a99d11e7ee72d581
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547077"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Självstudie: Azure Active Directory integrering med Jobscience

I den här självstudien får du lära dig hur du integrerar Jobscience med Azure Active Directory (Azure AD).

Genom att integrera Jobscience med Azure AD får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jobscience
- Du kan låta dina användare automatiskt bli inloggade på Jobscience (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure Portal

Om du vill veta mer om SaaS-appens integrering med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jobscience behöver du följande objekt:

- En Azure AD-prenumeration
- En aktive rad Jobscience-prenumeration med enkel inloggning

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få en månads utvärderings version här: [utvärderings](https://azure.microsoft.com/pricing/free-trial/)version.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du enkel inloggning i Azure AD i en test miljö. Det scenario som beskrivs i den här självstudien består av två huvud Bygg stenar:

1. Lägga till Jobscience från galleriet
1. Konfigurera och testa enkel inloggning för Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Lägga till Jobscience från galleriet
Om du vill konfigurera integreringen av Jobscience i Azure AD måste du lägga till Jobscience från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jobscience från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen. 

    ![Active Directory][1]

1. Navigera till **företags program**. Gå sedan till **alla program**.

    ![Program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Program][3]

1. I rutan Sök skriver du **Jobscience**.

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. I resultat panelen väljer du **Jobscience**och klickar sedan på knappen **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning för Azure AD
I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Jobscience baserat på en test användare som kallas "Britta Simon."

För att enkel inloggning ska fungera måste Azure AD veta vad motsvarande användare i Jobscience är till en användare i Azure AD. Med andra ord måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Jobscience.

I Jobscience tilldelar du värdet för **användar namnet** i Azure AD som värdet för **användar** namnet för att upprätta länk relationen.

Om du vill konfigurera och testa enkel inloggning med Jobscience i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning i Azure AD](#configuring-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-test](#creating-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en Jobscience-test användare](#creating-a-jobscience-test-user)** – för att få en motsvarighet till Britta Simon i Jobscience som är länkad till Azure AD-representation av användare.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)** -för att aktivera Britta Simon för att använda enkel inloggning i Azure AD.
1. **[Testa enkel inloggning](#testing-single-sign-on)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning för Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal och konfigurerar enkel inloggning i ditt Jobscience-program.

**Utför följande steg för att konfigurera enkel inloggning med Jobscience i Azure AD:**

1. Klicka på **enkel inloggning**på sidan **Jobscience** Application Integration i Azure Portal.

    ![Konfigurera enkel inloggning][4]

1. I dialog rutan **enkel inloggning** väljer du **läge** som    **SAML-baserad inloggning** för att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Utför följande steg i avsnittet **Jobscience-domän och URL: er** :

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta det här värdet från [Jobscience-klientens support team](http://www.jobscience.com/support) eller från SSO-profilen som du skapar som beskrivs senare i självstudien. 
 
1. I avsnittet certifikat för **SAML-signering** klickar du på **certifikat (base64)** och sparar sedan certifikat filen på din dator.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_general_400.png)

1. I avsnittet **konfiguration av Jobscience** klickar du på **Konfigurera Jobscience** för att öppna **Konfigurera inloggnings** fönstret. Kopiera URL: en för **utloggning, SAML Entity ID och SAML Single Sign-on** från **avsnittet snabb referens.**

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Logga in på din Jobscience-företags webbplats som administratör.

1. Gå till **installations programmet**.
   
   ![Installation](./media/jobscience-tutorial/IC784358.png "Installation")

1. Klicka på **domän hantering** i avsnittet **Administrera** i det vänstra navigerings fönstret för att expandera det relaterade avsnittet och klicka sedan på **min domän** för att öppna sidan **min domän** . 
   
   ![Min domän](./media/jobscience-tutorial/ic767825.png "Min domän")

1. Kontrol lera att din domän har kon figurer ATS korrekt genom att se till att den är i "**steg 4 distribuerad till användare**" och granska dina "**Mina domän inställningar**".

    ![Domän distribuerad till användare](./media/jobscience-tutorial/ic784377.png "Domän distribuerad till användare")

1. På Jobscience Company-webbplatsen klickar du på **säkerhets kontroller**och sedan på **Inställningar för enkel inloggning**.
    
    ![Säkerhets kontroller](./media/jobscience-tutorial/ic784364.png "Säkerhetskontroller")

1. I avsnittet **Inställningar för enkel inloggning** utför du följande steg:
    
    ![Inställningar för enkel inloggning](./media/jobscience-tutorial/ic781026.png "Inställningar för enkel inloggning")
    
    a. Välj **SAML-aktiverat **.

    b. Klicka på **nytt**.

1. I dialog rutan **Redigera SAML-enkel inloggnings inställning** utför du följande steg:
    
    ![Inställning av SAML enkel inloggning](./media/jobscience-tutorial/ic784365.png "Inställning av SAML enkel inloggning")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.

    b. I text rutan **utfärdare** klistrar du in värdet för **SAML Entity ID**, som du har kopierat från Azure Portal.

    c. I text rutan **entitets-ID** skriver du `https://salesforce-jobscience.com`

    d. Klicka på **Bläddra** för att ladda upp ditt Azure AD-certifikat.

    e. Som **SAML-identitets typ**väljer du **Assertion innehåller Federations-ID: t från användarobjektet**.

    f. Som **SAML-identitets plats**väljer du **identitet i NameIdentfier-elementet för ämnes instruktionen**.

    ex. I text rutan **inloggnings-URL för identitetsprovider** klistrar du in värdet för **URL för enkel inloggning för SAML**, som du har kopierat från Azure Portal.

    h. I text rutan **Logga in URL för identitets leverantör** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    i. Klicka på **Spara**.

1. Klicka på **domän hantering** i avsnittet **Administrera** i det vänstra navigerings fönstret för att expandera det relaterade avsnittet och klicka sedan på **min domän** för att öppna sidan **min domän** . 
    
    ![Min domän](./media/jobscience-tutorial/ic767825.png "Min domän")

1. På sidan **min domän** i avsnittet anpassning av **inloggnings sidan** klickar du på **Redigera**.
    
    ![Anpassning av inloggnings Sidan](./media/jobscience-tutorial/ic767826.png "Anpassning av inloggnings Sidan")

1. På sidan **anpassning av inloggnings sidan** i avsnittet **Authentication Service** visas namnet på dina inställningar för **SAML SSO** . Markera det och klicka sedan på **Spara**.
    
    ![Anpassning av inloggnings Sidan](./media/jobscience-tutorial/ic784366.png "Anpassning av inloggnings Sidan")

1. Om du vill hämta inloggnings webb adressen för SP-initierad enkel inloggning klickar du på **Inställningar för enkel inloggning** i menyn **säkerhets kontroller** .

    ![Säkerhets kontroller](./media/jobscience-tutorial/ic784368.png "Säkerhetskontroller")
    
    Klicka på den SSO-profil som du har skapat i steget ovan. Den här sidan visar URL: en för enkel inloggning för ditt företag (till exempel `https://companyname.my.salesforce.com?so=companyid` .    

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om den inbäddade dokumentations funktionen här: [Azure AD Embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare för Azure AD
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en test användare i Azure AD:**

1. I **Azure Portal**i det vänstra navigerings fönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Om du vill visa listan över användare går du till **användare och grupper** och klickar på **alla användare**.
    
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Öppna dialog rutan **användare** genom att klicka på **Lägg till** längst upp i dialog rutan.
 
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Utför följande steg på sidan **användare** :
 
    ![Skapa en testanvändare för Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Skriv **BrittaSimon**i text rutan **namn** .

    b. Skriv **e-postadressen** för BrittaSimon i text rutan **användar namn** .

    c. Välj **Visa lösen ord** och skriv ned värdet för **lösen ordet**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jobscience-test-user"></a>Skapa en Jobscience-test användare

För att Azure AD-användare ska kunna logga in på Jobscience måste de tillhandahållas i Jobscience. När det gäller Jobscience är etableringen en manuell uppgift.

>[!NOTE]
>Du kan använda andra verktyg för Jobscience av användar konton eller API: er som tillhandahålls av Jobscience för att etablera Azure Active Directory användar konton.
>  
        
**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Jobscience** företags webbplats som administratör.

1. Gå till installations programmet.
   
   ![Installation](./media/jobscience-tutorial/ic784358.png "Installation")
1. Gå till **Hantera användare \> användare**.
   
   ![Användare](./media/jobscience-tutorial/ic784369.png "Användare")
1. Klicka på **Ny användare**.
   
   ![Alla användare](./media/jobscience-tutorial/ic784370.png "Alla användare")
1. I dialog rutan **Redigera användare** utför du följande steg:
   
   ![Redigera användare](./media/jobscience-tutorial/ic784371.png "Redigera användare")
   
   a. I text rutan **förnamn** skriver du ett förnamn för användaren som Britta.
   
   b. I text rutan **efter namn** skriver du ett efter namn på användaren som Simon.
   
   c. I text rutan **alias** skriver du ett aliasnamn för användaren som Brittas.

   d. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

   e. I text rutan **användar namn** anger du ett användar namn för användaren Brittasimon@contoso.com .

   f. I text rutan **namn på Nick** anger du ett Nick namn för användaren som Simon.

   ex. Klicka på **Spara**.

    
> [!NOTE]
> Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-test användare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Jobscience.

![Tilldela användare][200] 

**Utför följande steg för att tilldela Britta Simon till Jobscience:**

1. Öppna vyn program i Azure Portal och navigera sedan till vyn katalog och gå till **företags program** och klicka sedan på **alla program**.

    ![Tilldela användare][201] 

1. I listan program väljer du **Jobscience**.

    ![Konfigurera enkel inloggning](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. I menyn till vänster klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på knappen **Lägg till**. Välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Tilldela användare][203]

1. I dialog rutan **användare och grupper väljer du** **Britta Simon** i listan användare.

1. Klicka på knappen **Välj** i dialog rutan **användare och grupper** .

1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Jobscience på åtkomst panelen, bör du få automatiskt inloggad till ditt Jobscience-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

