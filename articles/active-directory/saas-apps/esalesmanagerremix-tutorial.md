---
title: 'Självstudie: Azure Active Directory integration med E-Remix för försäljnings chef | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och E Sales Manager Remix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: dd5a200d1c17f0417b2ee88f7f905ee6de3cfe62
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555397"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrera Azure Active Directory med E-Remix för försäljnings chef

I den här självstudien får du lära dig att integrera Azure Active Directory (Azure AD) med E Sales Manager-Remix.

Genom att integrera Azure AD med E Sales Manager-Remix får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till E-Remix för försäljnings chef.
- Du kan låta dina användare logga in automatiskt till E-Remix (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med E Sales Manager-Remix behöver du följande objekt:

- En Azure AD-prenumeration
- En E-Remix med ett E-postaktiverat för försäljnings chef

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du *inte* använder en produktions miljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du enkel inloggning i Azure AD i en test miljö. 

Det scenario som beskrivs i den här självstudien består av två huvud Bygg stenar:

* Lägga till E Sales Manager-Remix från galleriet
* Konfigurera och testa enkel inloggning för Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Lägg till E Sales Manager-Remix från galleriet
Om du vill konfigurera integreringen av Azure AD med E Sales Manager-Remix lägger du till E Sales Manager-Remix från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

1. Välj **företags program**  >  **alla program**.

    ![Fönstret "företags program"][2]
    
1. Om du vill lägga till ett nytt program väljer du **nytt program** överst i fönstret.

    ![Knappen Nytt program][3]

1. I sökrutan skriver du **e Sales Manager Remix**, väljer **e Sales Manager-Remix** i resultat listan och väljer sedan **Lägg till**.

    ![E försäljnings hanterarens Remix i resultat listan](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med E Sales Manager Remix, baserat på en test användare som kallas "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD identifiera E-Remix för försäljnings hanteraren och dess motsvarighet i Azure AD. Med andra ord måste en länk relation mellan en Azure AD-användare och samma användare i E Sales Manager-Remix upprättas.

Om du vill konfigurera och testa enkel inloggning med E-Remix i Azure AD, slutför du Bygg stenarna i följande fem avsnitt:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

Aktivera enkel inloggning i Azure AD i Azure Portal och konfigurera enkel inloggning i din E-Remix program för försäljnings hanteraren genom att göra följande:

1. I Azure Portal på sidan E- **Remix för försäljnings chef** väljer du **enkel inloggning**.

    ![Länken "enkel inloggning"][4]

1. I fönstret **enkel inloggning** väljer du **SAML-baserad inloggning**i rutan **läge för enkel inloggning** .
 
    ![Fönstret "enkel inloggning"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Under **E Remix-domän och URL: er för försäljnings hanteraren**gör du följande:

    ![E-Remix för information om domän och URL: er för försäljnings chef](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. I rutan **inloggnings-URL** skriver du en URL i följande format: *https:// \<Server-Based-URL> / \<sub-domain> /eSales-PC*.

    b. I rutan **identifierare** anger du en URL i följande format: *https:// \<Server-Based-URL> / \<sub-domain> / *.

    c. Observera **ID** -värdet för senare användning i den här självstudien.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med den faktiska inloggnings-URL: en och identifieraren. Om du vill hämta värdena kontaktar du [E-Remix för kund support för försäljnings chef](mailto:esupport@softbrain.co.jp).

1. Under **SAML-signeringscertifikat**väljer du **certifikat (base64)** och sparar sedan certifikat filen på din dator.

    ![Hämtnings länken för certifikatet (base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Markera kryss rutan **Visa och redigera alla andra** användarattribut och välj sedan attributet **EmailAddress** .
    
    ![Fönstret användarattribut](./media/esalesmanagerremix-tutorial/configure1.png)

    Fönstret **Redigera attribut** öppnas.

1. Kopiera **namn områdes** -och **namn** värden. Generera värdet i mönstret *\<Namespace>/\<Name>* och spara det för senare användning i den här självstudien.

    ![Fönstret redigera attribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. Under **E-Remix konfiguration av försäljnings chef**väljer du **Konfigurera E Sales Manager Remix**.

    ![Konfiguration av E-Remix för försäljnings chef](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Fönstret **Konfigurera inloggning** öppnas.

1. I avsnittet **snabb referens** kopierar du den utloggnings-URL: en och URL: en för tjänsten SAML enkel inloggning.

1. Välj **Spara**.

    ![Knappen Spara](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Logga in på ditt E-Remix-program för försäljnings chef som administratör.

1. Längst upp till höger väljer **du till administratörs menyn**.

    ![Kommandot "till administratörs meny"](./media/esalesmanagerremix-tutorial/configure4.png)

1. I det vänstra fönstret väljer du **system inställnings**  >  **samarbete med externt system**.

    ![Länkarna "system inställningar" och "samarbete med externt system"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. I fönstret **samar beta med externt system** väljer du **SAML**.

    ![Fönstret "samar beta med externt system"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Under **inställningen SAML-autentisering**gör du följande:

    ![Avsnittet "SAML Authentication Setting"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Markera kryss rutan **PC-version** .
    
    b. Välj **e-post**i list rutan i avsnittet **samarbets objekt** .

    c. I rutan **samarbets objekt** klistrar du in det anspråk värde som du kopierade tidigare från Azure Portal (det vill säga **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. I rutan **utfärdare (enhets-ID)** klistrar du in det ID-värde som du kopierade tidigare i avsnittet **E-Remix för Sales Manager-domänen och URL: erna** i Azure Portal.

    e. Om du vill ladda upp det hämtade certifikatet från Azure Portal väljer du **fil val**.

    f. I rutan **ID-providerns inloggnings-URL** klistrar du in URL: en för den enkla inloggnings tjänsten för SAML som du kopierade tidigare i Azure Portal.

    ex. I rutan **Logga in URL för identitets leverantör** klistrar du in värdet för utloggnings-URL som du kopierade tidigare i Azure Portal.

    h. Välj **inställningen slutförd**.

> [!TIP]
> När du konfigurerar appen kan du läsa en kortare version av föregående anvisningar i [Azure Portal](https://portal.azure.com). När du har lagt till appen i avsnittet **Active Directory**  >  **företags program** väljer du fliken **enkel inloggning** och öppnar sedan den inbäddade dokumentationen i **konfigurations** avsnittet längst ned. Mer information om funktionen Embedded documentation finns i [dokumentationen för Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du test User Britta Simon i Azure Portal genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. I Azure Portal väljer du **Azure Active Directory**i det vänstra fönstret.

    ![Azure Active Directory länken](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över aktuella användare väljer **du användare och grupper**  >  **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Klicka på **Lägg till**längst upp i fönstret **alla användare** .

    ![Knappen Lägg till](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Fönstret **användare** öppnas.

1. Gör följande i fönstret **användare** :

    ![Fönstret användare](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Skriv **BrittaSimon**i rutan **namn** .

    b. Skriv e-postadressen för användaren Britta Simon i rutan **användar namn** .

    c. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .

    d. Välj **Skapa**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Skapa en E-Remix test användare för Sales Manager

1. Logga in på ditt E-Remix-program för försäljnings chef som administratör.

1. Välj **till administratörs menyn** på menyn längst upp till höger.

    ![Konfiguration av E-Remix för försäljnings chef](./media/esalesmanagerremix-tutorial/configure4.png)

1. Välj **ditt företags inställningar**  >  **underhåll av avdelningar och anställda**och välj sedan **medarbetare registrerade**.

    ![Fliken "anställda har registrerats"](./media/esalesmanagerremix-tutorial/user1.png)

1. I avsnittet **ny personal registrering** gör du följande:
    
    ![Avsnittet "ny personal registrering"](./media/esalesmanagerremix-tutorial/user2.png)

    a. I rutan **namn på anställd** skriver du namnet på användaren (till exempel **Britta**).

    b. Fyll i de återstående obligatoriska fälten.
    
    c. Om du aktiverar SAML kan administratören inte logga in från inloggnings sidan. Bevilja administratörs inloggnings privilegier för användaren genom att markera kryss rutan **admin-inloggning** .

    d. Välj **registrering**.

1. Om du vill logga in som administratör i framtiden loggar du in som den användare som har administratörs behörighet och väljer sedan på **Administratörs menyn**längst upp till höger.

    ![Kommandot "till administratörs meny"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du User Britta Simon till att använda enkel inloggning med Azure genom att bevilja åtkomst till E-Remix för försäljnings chef. Gör så här: 

![Tilldela användar rollen][200] 

1. I Azure Portal öppnar du vyn **program** , går till vyn **katalog** och väljer sedan **företags program**  >  **alla program**.

    ![Länkarna "företags program" och "alla program"][201] 

1. I listan **program** väljer du **E Sales Manager Remix**.

    ![E-länken för E-Remix försäljnings chef](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. I det vänstra fönstret väljer **du användare och grupper**.

    ![Länken ”Användare och grupper”][202]

1. Välj **Lägg till** och sedan i fönstret **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

1. I fönstret **användare och grupper** väljer du **Britta Simon**i listan **användare** .

1. Välj knappen **Välj** .

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer ikonen E-Remix för försäljnings chef på åtkomst panelen, bör du logga in automatiskt till din E-Remix för försäljnings chef.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om att integrera SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

