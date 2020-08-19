---
title: 'Självstudie: Azure Active Directory integrering med AnswerHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: d453cc5300dc658e4b33bb0591100deae5f34aef
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544748"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Självstudie: Azure Active Directory integrering med AnswerHub

I den här självstudien lär du dig att integrera AnswerHub med Azure Active Directory (Azure AD).
När du integrerar AnswerHub med Azure AD innebär det följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till AnswerHub.
* Du kan låta dina användare logga in automatiskt på AnswerHub med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton från en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att konfigurera Azure AD-integrering med AnswerHub:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du påbörja [en månads utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En AnswerHub-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AnswerHub har stöd för SP-initierad enkel inloggning.

## <a name="add-answerhub-from-the-gallery"></a>Lägga till AnswerHub från galleriet

För att konfigurera integreringen av AnswerHub i Azure AD måste du lägga till AnswerHub från galleriet i dina hanterade SaaS-appar.

**Så här lägger du till AnswerHub från galleriet:**

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett program genom att välja **Nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **AnswerHub** i sökrutan. Välj **AnswerHub** i resultatlistan och sedan **Lägg till**.

     ![AnswerHub i resultatlistan](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med AnswerHub med hjälp av en testanvändare med namnet Britta Simon.
För enkel inloggning måste du upprätta en länk mellan en Azure AD-användare och motsvarande användare i AnswerHub.

Du behöver utföra följande uppgifter för att konfigurera och testa enkel inloggning med Azure AD för AnswerHub:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda funktionen.
2. [Konfigurera enkel inloggning för AnswerHub](#configure-answerhub-single-sign-on) för att konfigurera inställningarna för enkel inloggning på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) med namnet Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. Skapa en AnswerHub-testanvändare som motsvarar och är länkad till Azure AD-testanvändaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du enkel inloggning med Azure AD i Azure-portalen.

**Så här konfigurerar du enkel inloggning med Azure AD för AnswerHub:**

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **AnswerHub** och väljer **Enkel inloggning**.

    ![Knappen Enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Dialogrutan Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du redigera-ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Sidan Konfigurera enkel inloggning med SAML](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Avsnittet Grundläggande SAML-konfiguration](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du en URL som har det här mönstret: `https://<company>.answerhub.com`

    b. I rutan **Identifierare (entitets-ID)** anger du en URL som har det här mönstret: `https://<company>.answerhub.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [AnswerHub-supportteamet](mailto:success@answerhub.com) för att få värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**. Välj länken **Ladda ned** bredvid **Certifikat (Base64)** enligt dina behov och spara certifikatet på datorn.

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera AnswerHub** kopierar du lämplig URL eller URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

   Du kan kopiera dessa URL:er:
    - Inloggnings-URL

    - Azure AD-identifierare

    - Utloggnings-URL

### <a name="configure-answerhub-single-sign-on"></a>Konfigurera enkel inloggning för AnswerHub

I det här avsnittet konfigurerar du enkel inloggning för AnswerHub.  

**Så här konfigurerar du enkel inloggning för AnswerHub:**

1. Öppna ett nytt webbläsarfönster och logga in på din AnswerHub-företagswebbplats som administratör.

    > [!NOTE]
    > Om du behöver hjälp med att konfigurera AnswerHub kontaktar du [supportteamet för AnswerHub](mailto:success@answerhub.com.).

2. Gå till **Administration**.

3. På fliken **Användare och grupper** går du till avsnittet **Social Settings** (Sociala inställningar) i den vänstra rutan och väljer **SAML-konfiguration**.

4. På fliken **IDP Config** (IDP-konfiguration) utför du följande steg:

    ![Fliken användare & grupper](./media/answerhub-tutorial/ic785172.png "SAML-konfiguration")  
  
    a. I rutan för **inloggnings-URL för IDP** klistrar du in den **inloggnings-URL** som du kopierade från Azure-portalen.
  
    b. I rutan för **utloggnings-URL för IDP** klistrar du in den **utloggnings-URL** som du kopierade från Azure-portalen.

    c. I rutan för **format för IDP-namnidentifierare** anger du det värde för **Identifierare** som valts i avsnittet **Användarattribut** i Azure-portalen.
  
    d. Välj **Nycklar och certifikat**.

5. I avsnittet **Nycklar och certifikat** utför du följande steg:

    ![Avsnittet nycklar och certifikat](./media/answerhub-tutorial/ic785173.png "Nycklar och certifikat")  

    a. Öppna det base64-kodade certifikatet som du laddade ned från Azure-portalen i Anteckningar, kopiera innehållet och klistra sedan in det i rutan för **offentlig nyckel för IDP (x509-format)**.
  
    b. Välj **Spara**.

6. På fliken **IDP Config** (IDP-konfiguration) väljer du **Spara** igen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

**Så här skapar du en Azure AD-testanvändare:**

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Välj Azure Active Directory, Användare, Alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I användaregenskaperna utför du följande steg.

    ![Användaregenskaper](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **användar namn** anger du **brittasimon \@<yourcompanydomain. extension>**.  
    Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet konfigurerar du User Britta Simon till att använda enkel inloggning med Azure AD genom att bevilja användaren åtkomst till AnswerHub.

**Så här tilldelar du Azure AD-testanvändaren:**

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **AnswerHub**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **AnswerHub**.

    ![Programlista](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan **Användare** och väljer **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. 

7. Välj knappen **Välj** längst ned på skärmen.

8. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-answerhub-test-user"></a>Skapa en AnswerHub-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på AnswerHub måste du lägga till dem i AnswerHub. I AnswerHub utförs den här uppgiften manuellt.

**Så här konfigurerar du ett användarkonto:**

1. Logga in på din **AnswerHub**-företagswebbplats som administratör.

2. Gå till **Administration**.

3. Välj fliken **Användare och grupper**.

4. I den vänstra rutan går du till avsnittet **Hantera användare**, väljer **Skapa eller importera användare** och väljer sedan **Användare och grupper**.

   ![Fliken användare & grupper](./media/answerhub-tutorial/ic785175.png "Användare & grupper")

5. I motsvarande rutor anger du **E-postadress**, **Användarnamn** och **Lösenord** för ett giltigt Azure AD-konto som du vill lägga till, och väljer sedan **Spara**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för skapande av användarkonton som tillhandahålls av AnswerHub för att konfigurera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av åtkomstpanelen.

När du väljer AnswerHub-panelen i åtkomstpanelen bör du automatiskt loggas in på AnswerHub som du har konfigurerat enkel inloggning för. Mer information om åtkomst panelen finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

