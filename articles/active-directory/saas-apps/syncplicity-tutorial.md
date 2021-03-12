---
title: 'Självstudie: Azure Active Directory integrering med Syncplicity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201522"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Självstudie: integrera Syncplicity med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Syncplicity med Azure Active Directory (Azure AD). När du integrerar Syncplicity med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Syncplicity.
* Gör det möjligt för användarna att logga in automatiskt till Syncplicity med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en kostnads fri utvärderings period på 12 månader [här](https://azure.microsoft.com/free/).
* Syncplicity för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Syncplicity stöder **SP** -initierad SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Lägga till Syncplicity från galleriet

Om du vill konfigurera integreringen av Syncplicity i Azure AD måste du lägga till Syncplicity från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Under **skapa** klickar du på **företags program**.
1. I avsnittet **Bläddra i Azure AD-galleriet** skriver du **Syncplicity** i sökrutan.
1. Välj **Syncplicity** från resultat panelen och klicka sedan på **skapa** för att lägga till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med Syncplicity med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Syncplicity.

Om du vill konfigurera och testa Azure AD SSO med Syncplicity, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SYNCPLICITY SSO](#configure-syncplicity-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Syncplicity test User](#create-syncplicity-test-user)** -om du vill ha en motsvarighet till B. Simon i Syncplicity som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.
7. **[Uppdatera SSO](#update-sso)**) – gör nödvändiga ändringar i Syncplicity om du har ändrat SSO-inställningarna i Azure AD.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Syncplicity** , letar reda på avsnittet **komma igång** och väljer **Konfigurera enkel inloggning**.
2. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
3. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com/sp`

    b. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com`
    
    c. I text rutan **svars-URL: en (intygs mottagar tjänst)** anger du en URL med följande mönster: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Syncplicity client support team](https://www.syncplicity.com/contact-us) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på **Redigera** i avsnittet **SAML-signeringscertifikat** . I dialog rutan klickar du på knappen med tre punkter bredvid ditt aktiva certifikat och väljer **PEM certifikat hämtning**.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!NOTE]
    > Du behöver PEM-certifikatet, eftersom Syncplicity inte accepterar certifikat i CER-format.

6. I avsnittet **Konfigurera Syncplicity** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurera Syncplicity SSO

1. Logga in på din **Syncplicity** -klient.

1. Klicka på **administratör** på menyn högst upp, Välj **Inställningar** och klicka sedan på **anpassad domän och enkel inloggning**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Utför följande steg på dialog sidan **enkel Sign-On (SSO)** :

    ![Enkel Sign-On \( SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Skriv namnet på din domän i text rutan **anpassad domän** .
  
    b. Välj **aktive rad** som **enskild Sign-On status**.

    c. I text rutan **entitets-ID** klistrar du in ID-värdet **(enhets-ID)** som du har använt i den **grundläggande SAML-konfigurationen** i Azure Portal.

    d. I text rutan **URL för inloggnings sida** klistrar du in **inloggnings-URL:** en som du har kopierat från Azure Portal.

    e. I text rutan för **utloggnings sidans URL** klistrar du in den **URL för utloggning** som du har kopierat från Azure Portal.

    f. I **certifikat för identitetsprovider** klickar du på **Välj fil** och överför sedan det certifikat som du har laddat ned från Azure Portal.

    ex. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
2. Välj **ny användare** överst på skärmen.
3. I **användar** egenskaperna följer du de här stegen:

   a. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.

   b. I **Namn**-fältet skriver du `B.Simon`.  
   
   c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   
   d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Syncplicity.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Syncplicity**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare/grupp**.

    ![Länken Lägg till användare](common/add-assign-user.png)
1. På sidan **Lägg till tilldelning** väljer **du användare**. 
1. I dialog rutan **användare** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. På sidan **Lägg till tilldelning** klickar du på knappen **tilldela** .

### <a name="create-syncplicity-test-user"></a>Skapa Syncplicity test användare

För att Azure AD-användare ska kunna logga in måste de vara etablerade i Syncplicity-programmet. I det här avsnittet beskrivs hur du skapar Azure AD-användarkonton i Syncplicity.

**Utför följande steg för att etablera ett användar konto för Syncplicity:**

1. Logga in på din **Syncplicity** -klient (till exempel: `https://company.Syncplicity.com` ).

1. Klicka på **administratör** och välj **användar konton** och klicka sedan på **Lägg till en användare**.

    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "Hantera användare")

1. Ange **e-postadresserna** för ett Azure AD-konto som du vill etablera, Välj **användare** som **roll** och klicka sedan på **Nästa**.

    ![Konto information](./media/syncplicity-tutorial/ic769765.png "Kontoinformation")

    > [!NOTE]
    > Azure AD-användarkontot får ett e-postmeddelande med en länk för att bekräfta och aktivera kontot.

1. Välj en grupp i ditt företag som din nya användare ska bli medlem i och klicka sedan på **Nästa**.

    ![Grupp medlemskap](./media/syncplicity-tutorial/ic769772.png "Grupp medlemskap")

    > [!NOTE]
    > Om det inte finns några grupper i listan klickar du på **Nästa**.

1. Välj de mappar du vill placera under Syncplicity på användarens dator och klicka sedan på **Nästa**.

    ![Syncplicity-mappar](./media/syncplicity-tutorial/ic769773.png "Syncplicity-mappar")

> [!NOTE]
> Du kan använda andra verktyg för Syncplicity av användar konton eller API: er som tillhandahålls av Syncplicity för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Syncplicity på åtkomst panelen, bör du loggas in automatiskt på den Syncplicity som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

### <a name="update-sso"></a>Uppdatera SSO

När du behöver göra ändringar i SSO måste du kontrol lera certifikatet för SAML- **signering** som används. Om certifikatet har ändrats ser du till att ladda upp det nya till Syncplicity enligt beskrivningen i **[Konfigurera SYNCPLICITY SSO](#configure-syncplicity-sso)**.

Om du använder Syncplicity Mobile-appen kontaktar du Syncplicity kund support ( support@syncplicity.com ) för att få hjälp.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
