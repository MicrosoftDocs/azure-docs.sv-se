---
title: 'Självstudie: Azure Active Directory integrering med Printix | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Printix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: e9bc7969c5fee83944dab7cb35832f82b57c5595
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552351"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Självstudie: Azure Active Directory integrering med Printix

I den här självstudien får du lära dig hur du integrerar Printix med Azure Active Directory (Azure AD).

Genom att integrera Printix med Azure AD får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Printix
- Du kan låta dina användare automatiskt bli inloggade på Printix (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure Portal

Om du vill veta mer om SaaS-appens integrering med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Printix behöver du följande objekt:

- En Azure AD-prenumeration
- En aktive rad Printix-prenumeration med enkel inloggning

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få en månads utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du enkel inloggning i Azure AD i en test miljö. Det scenario som beskrivs i den här självstudien består av två huvud Bygg stenar:

1. Lägga till Printix från galleriet
1. Konfigurera och testa enkel inloggning för Azure AD

## <a name="adding-printix-from-the-gallery"></a>Lägga till Printix från galleriet
Om du vill konfigurera integreringen av Printix i Azure AD måste du lägga till Printix från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Printix från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen. 

    ![Active Directory][1]

1. Navigera till **företags program**. Gå sedan till **alla program**.

    ![Program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Program][3]

1. I rutan Sök skriver du **Printix**.

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. I resultat panelen väljer du **Printix**och klickar sedan på knappen **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning för Azure AD
I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Printix baserat på en test användare som kallas "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motsvarande användare i Printix är till en användare i Azure AD. Med andra ord måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Printix.

I Printix tilldelar du värdet för **användar namnet** i Azure AD som värdet för **användar** namnet för att upprätta länk relationen.

Om du vill konfigurera och testa enkel inloggning med Printix i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning i Azure AD](#configuring-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-test](#creating-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en Printix-test användare](#creating-a-printix-test-user)** – för att få en motsvarighet till Britta Simon i Printix som är länkad till Azure AD-representation av användare.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)** -för att aktivera Britta Simon för att använda enkel inloggning i Azure AD.
1. **[Testa enkel inloggning](#testing-single-sign-on)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning för Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal och konfigurerar enkel inloggning i ditt Printix-program.

**Utför följande steg för att konfigurera enkel inloggning med Printix i Azure AD:**

1. Klicka på **enkel inloggning**på sidan **Printix** Application Integration i Azure Portal.

    ![Konfigurera enkel inloggning][4]

1. I dialog rutan **enkel inloggning** väljer du **läge** som **SAML-baserad inloggning** för att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Utför följande steg i avsnittet **Printix-domän och URL: er** :

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_url.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Printix client support team](mailto:support@printix.net) för att hämta värdet. 
 
1. I avsnittet **SAML-signeringscertifikat** klickar du på **metadata-XML** och sparar sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_general_400.png)

1. Logga in på Printix-klienten som administratör.

1. I menyn högst upp klickar du på ikonen i det övre högra hörnet och väljer "**autentisering**".
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_06.png)

1. På fliken **installation** väljer du **Aktivera Azure/Office 365-autentisering**
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_07.png)

1. På fliken **Azure** i URL: en för indatakälla-metadata till text rutan för "**federationsmetadata**". 

    Bifoga metadata-XML-filen som du laddade ned från Azure AD till [Printix support team](mailto:support@printix.net). Sedan laddar de upp XML-filen och anger en URL för federationsmetadata.
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klicka på knappen "**test**" och klicka på**OK**om testet lyckades.
   
     Sidan Azure Active Directory visas när du klickar på knappen **testa** . "Testet lyckades" här betyder att när du har angett autentiseringsuppgifterna för ditt Azure test-konto visas ett meddelande om att inställningar har testats OK. Klicka sedan på **OK** .
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_09.png)

1. Klicka på knappen **Spara** på sidan**autentisering**.


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om den inbäddade dokumentations funktionen här: [Azure AD Embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare för Azure AD
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en test användare i Azure AD:**

1. I **Azure Portal**i det vänstra navigerings fönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Om du vill visa listan över användare går du till **användare och grupper** och klickar på **alla användare**.
    
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Öppna dialog rutan **användare** genom att klicka på **Lägg till** längst upp i dialog rutan.
 
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Utför följande steg på sidan **användare** :
 
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. Skriv **BrittaSimon**i text rutan **namn** .

    b. Skriv **e-postadressen** för BrittaSimon i text rutan **användar namn** .

    c. Välj **Visa lösen ord** och skriv ned värdet för **lösen ordet**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-printix-test-user"></a>Skapa en Printix-test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Printix. Printix stöder just-in-Time-etablering, som är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt Printix om den inte redan finns. 

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Printix support-teamet](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-test användare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Printix.

![Tilldela användare][200] 

**Utför följande steg för att tilldela Britta Simon till Printix:**

1. Öppna vyn program i Azure Portal och navigera sedan till vyn katalog och gå till **företags program** och klicka sedan på **alla program**.

    ![Tilldela användare][201] 

1. I listan program väljer du **Printix**.

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_app.png) 

1. I menyn till vänster klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på knappen **Lägg till**. Välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Tilldela användare][203]

1. I dialog rutan **användare och grupper väljer du** **Britta Simon** i listan användare.

1. Klicka på knappen **Välj** i dialog rutan **användare och grupper** .

1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Printix på åtkomst panelen, bör du få automatiskt inloggad till ditt Printix-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

