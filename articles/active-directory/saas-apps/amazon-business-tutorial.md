---
title: 'Självstudie: Azure Active Directory integration med Amazon Business | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 0db43f438eaff3e783b829eabe7152c5f13e943d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548324"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Självstudie: integrera Amazon Business med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Amazon Business med Azure Active Directory (Azure AD). När du integrerar [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Amazon Business.
* Gör det möjligt för användarna att logga in automatiskt till Amazon Business med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* En prenumeration som är aktive rad för Amazon Business Single Sign-on (SSO). Gå till [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) -sidan för att skapa ett Amazon Business-konto.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i ett befintligt Amazon Business-konto.

* Amazon Business stöder **SP-och IDP** -INITIERAd SSO
* Amazon Business stöder **just-in-Time** User-etablering

## <a name="adding-amazon-business-from-the-gallery"></a>Lägga till Amazon Business från galleriet

Om du vill konfigurera integrationen av Amazon Business i Azure AD måste du lägga till Amazon Business från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **Amazon Business** i rutan Sök i avsnittet **Lägg till från Galleri** .
1. Välj **Amazon Business** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Amazon Business med hjälp av en test användare som heter **B. Simon**.

Slutför följande steg för att konfigurera och testa Azure AD SSO med Amazon Business:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Amazon Business SSO](#configure-amazon-business-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Amazon Business test User](#create-amazon-business-test-user)** – för att få en motsvarighet till B. Simon i Amazon Business som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **Amazon affärs** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera i **IDP** initierat läge, utför följande steg:

    1. I text rutan **identifierare (enhets-ID)** anger du en URL med något av följande mönster:
    
       | URL | Region |
       |-|-|
       | `https://www.amazon.com`| Nordamerika |
       | `https://www.amazon.co.jp`| Asien, östra |
       | `https://www.amazon.de`| Europa |

    1. I text rutan **svars-URL** skriver du en URL med något av följande mönster:
    
       | URL | Region |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Nordamerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Asien, östra |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Du får `<idpid>` värdet från avsnittet om konfiguration av Amazon Business SSO, som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Om du vill konfigurera programmet i **SP** -initierat läge måste du lägga till den fullständiga URL: en som finns i Amazon Business-konfigurationen till **inloggnings-URL: en** i avsnittet **Ange ytterligare URL: er** .

1. I följande skärmbild visas listan över standardattribut. Redigera attributen genom att klicka på ikonen **Redigera** i avsnittet **användarattribut &-anspråk** .

    ![Attribut](media/amazon-business-tutorial/map-attribute3.png)

1. Redigera attribut och kopiera **namn områdets** värde för dessa attribut till anteckningar.

    ![Attribut](media/amazon-business-tutorial/map-attribute4.png)

1. Förutom vidare förväntar Amazon Business Application att fler attribut skickas tillbaka i SAML-svar. I avsnittet **användarattribut &-anspråk** i dialog rutan **grupp anspråk** utför du följande steg:

    a. Klicka på **pennan** bredvid **grupper som returneras i anspråk**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Välj **alla grupper** i alternativ listan.

    c. Välj **grupp-ID** som **källattribut**.

    d. Markera kryss rutan **anpassa namnet på gruppen grupp anspråk** och ange grupp namnet enligt organisationens krav.

    e. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **metadata-XML** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. På sidan **Konfigurera Amazon Business** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Konfigurera Amazon Business SSO

1. Logga in på din Amazon Business Company-webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **användar profilen** och välj **företags inställningar**.

    ![Användarprofil](media/amazon-business-tutorial/user-profile.png)

1. I guiden **system integrering** väljer du **enkel inloggning (SSO)**.

    ![Enkel inloggning (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. I guiden **Konfigurera SSO** väljer du providern enligt organisationens krav och klickar på **Nästa**.

    ![Standardgrupp](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > Även om Microsoft ADFS är ett listat alternativ fungerar det inte med Azure AD SSO.

1. I guiden **nytt användar konto standarder** väljer du **standard gruppen** och väljer sedan **standard köp rollen** enligt användar rollen i din organisation och klickar på **Nästa**.

    ![Standardgrupp](media/amazon-business-tutorial/dafault-group2.png)

1. I guiden **Ladda upp din metadatafil** klickar du på **Bläddra** för att ladda upp **metadata-XML** -filen som du har laddat ned från Azure Portal och klickar på **överför**.

    ![Anslutnings data](media/amazon-business-tutorial/connection-data1.png)

1. När du har laddat ned den hämtade metadatafilen fylls fälten i avsnittet **anslutnings data** i automatiskt. Klicka sedan på **Nästa**.

    ![Anslutnings data](media/amazon-business-tutorial/connection-data2.png)

1. I guiden **Överför ditt Attribute-uttryck** klickar du på **hoppa över**.

    ![Attribut](media/amazon-business-tutorial/map-attribute1.png)

1. I guiden **attributmappning** lägger du till krav fälten genom att klicka på alternativet **+ Lägg till ett fält** . Lägg till attributvärdena inklusive namn området som du har kopierat från avsnittet **användarattribut &-anspråk** i Azure Portal till fältet  **SAML-ATTRIBUTENAME** och klicka på **Nästa**.

    ![Attribut](media/amazon-business-tutorial/map-attribute2.png)

1. I guiden **Amazon Connection data** klickar du på **Nästa**.

    ![Anslutning](media/amazon-business-tutorial/amazon-connect.png)

1. Kontrol lera **statusen** för de steg som har kon figurer ATS och klicka på **Starta testning**.

    ![Anslutning](media/amazon-business-tutorial/sso-connection1.png)

1. I guiden **testa SSO-anslutning** klickar du på **testa**.

    ![Anslutning](media/amazon-business-tutorial/sso-connection2.png)

1. I guiden **IDP initierad URL** , innan du klickar på **Aktivera**, kopierar du värdet som är tilldelat till **idpid** och klistrar in i parametern **idpid** i **SVARs-URL:** en i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ![Anslutning](media/amazon-business-tutorial/sso-connection3.png)

1. I guiden **är du redo att växla till aktiv SSO?** -guide, markera kryss rutan **fullständigt testade SSO och är redo att sätta** igång och klicka på **Växla till aktiv**.

    ![Anslutning](media/amazon-business-tutorial/sso-connection4.png)

1. Slutligen visas statusen i avsnittet **information om SSO-anslutnings** **status** som **aktiv**.

    ![Anslutning](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Om du vill konfigurera programmet i **SP** initierat läge slutför du följande steg, klistra in inloggnings-URL: en från skärm bilden ovanför i text rutan **INLOGGNINGs-URL** i text rutan **Ange ytterligare URL: er** i Azure Portal. Använd följande format:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

> [!NOTE]
> Administratörer måste skapa test användare i klient organisationen om det behövs. Följande steg visar hur du skapar en test användare.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure Portal

1. Klicka på **Azure Active Directory > alla grupper**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klicka på **ny grupp**:

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/new-group-tab.png)

1. Fyll i **grupp typ**, **grupp namn**, **grupp Beskrivning**, **medlemskaps typ**. Klicka på pilen för att välja medlemmar och Sök sedan efter eller klicka på den medlem som du vill lägga till i gruppen. Klicka på **Välj** för att lägga till de valda medlemmarna och klicka sedan på **skapa**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Amazon Business.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Amazon Business**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

    >[!NOTE]
    > Om du inte tilldelar användarna i Azure AD får du följande fel meddelande.

    ![Länken Lägg till användare](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Tilldela Azure AD-säkerhetsgruppen i Azure Portal

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Amazon Business**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program skriver du och väljer **Amazon Business**.

    ![Amazon Business-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Sök efter den säkerhets grupp du vill använda och klicka sedan på gruppen för att lägga till den i avsnittet Välj medlemmar. Klicka på **Välj**och sedan på **tilldela**.

    ![Sök säkerhets grupp](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Kontrol lera meddelandena i meny raden för att meddelas att gruppen har tilldelats till företags programmet i Azure Portal.

### <a name="create-amazon-business-test-user"></a>Skapa Amazon Business test-användare

I det här avsnittet skapas en användare som heter B. Simon i Amazon Business. Amazon Business har stöd för just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Amazon Business, skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på fliken Amazon-företag i åtkomst panelen bör du loggas in automatiskt på Amazon-företaget som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
