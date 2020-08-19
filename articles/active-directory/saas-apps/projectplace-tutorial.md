---
title: 'Självstudie: Azure Active Directory integrering med Projectplace | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: cbc65e64dfd18cdc16873a2d82aecadabeec4c28
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553520"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Självstudie: integrera Projectplace med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Projectplace med Azure Active Directory (Azure AD). När du integrerar Projectplace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Projectplace.
* Gör det möjligt för användarna att logga in automatiskt till Projectplace med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.
* Användare kan vara etablerade i Projectplace automatiskt.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Projectplace för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Projectplace stöder **SP-och IDP** -initierad SSO och stöder **just-in-Time** User-etablering.

## <a name="adding-projectplace-from-the-gallery"></a>Lägga till Projectplace från galleriet

Om du vill konfigurera integreringen av Projectplace i Azure AD måste du lägga till Projectplace från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Projectplace** i sökrutan.
1. Välj **Projectplace** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Projectplace med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Projectplace.

Om du vill konfigurera och testa Azure AD SSO med Projectplace, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Projectplace](#configure-projectplace)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa Projectplace test User](#create-projectplace-test-user)** för att ha en motsvarighet till B. Simon i Projectplace som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Projectplace** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, är programmet förkonfigurerat och de nödvändiga URL: erna redan har fyllts i i förväg med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://service.projectplace.com`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera **ikon** för att kopiera **URL: en för appens federationens metadata**enligt ditt krav och spara den i anteckningar.

   ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. I avsnittet **Konfigurera Projectplace** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurera Projectplace

Om du vill konfigurera enkel inloggning på **Projectplace** -sidan måste du skicka den kopierade **URL: en för appens Federations-metadata** från Azure Portal till [support teamet för Projectplace](https://success.planview.com/Projectplace/Support). Det här teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

>[!NOTE]
>Konfigurationen för enkel inloggning måste utföras av [Projectplace support-teamet](https://success.planview.com/Projectplace/Support). Du får ett meddelande så snart konfigurationen är klar. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Projectplace.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Projectplace**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-projectplace-test-user"></a>Skapa Projectplace test användare

>[!NOTE]
>Du kan hoppa över det här steget om du har aktiverat etableringen i Projectplace. Du kan be [Projectplace support-teamet](https://success.planview.com/Projectplace/Support) att aktivera provisoning, när du har utfört användare skapas i Projectplace under den första inloggningen.

Om du vill att Azure AD-användare ska kunna logga in på Projectplace måste du lägga till dem i Projectplace. Du måste lägga till dem manuellt.

**Gör så här för att skapa ett användar konto:**

1. Logga in på din **Projectplace** -företags webbplats som administratör.

2. Gå till **personer**och välj sedan **medlemmar**:
   
    ![Gå till personer och välj sedan medlemmar](./media/projectplace-tutorial/ic790228.png "People")

3. Välj **Lägg till medlem**:
   
    ![Välj Lägg till medlem](./media/projectplace-tutorial/ic790232.png "Lägg till medlemmar")

4. I avsnittet **Lägg till medlem** utför du följande steg.
   
    ![Avsnittet Lägg till medlem](./media/projectplace-tutorial/ic790233.png "Nya medlemmar")
   
    1. I rutan **nya medlemmar** anger du e-postadressen för ett giltigt Azure AD-konto som du vill lägga till.
   
    1. Välj **Skicka**.

   Ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktivt skickas till Azure AD-konto innehavaren.

>[!NOTE]
>Du kan också använda andra verktyg för skapande av användar konton eller API som tillhandahålls av Projectplace för att lägga till Azure AD-användarkonton.


### <a name="test-sso"></a>Testa SSO

När du väljer panelen Projectplace på åtkomst panelen, bör du loggas in automatiskt på den Projectplace som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)