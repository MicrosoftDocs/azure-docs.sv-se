---
title: 'Självstudie: Azure Active Directory integrering med Oracle Cloud Infrastructure Console | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Oracle Cloud Infrastructure-konsolen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: d4a478c402346e33a4f27ff2a363de75285b435b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543847"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Självstudie: integrera Oracle Cloud Infrastructure-konsolen med Azure Active Directory

I den här självstudien får du lära dig att integrera Oracle Cloud Infrastructure-konsolen med Azure Active Directory (Azure AD). När du integrerar en Oracle Cloud Infrastructure-konsol med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Oracle Cloud Infrastructure-konsolen.
* Gör det möjligt för användarna att logga in automatiskt till Oracle Cloud Infrastructure-konsolen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på Oracle Cloud Infrastructure Console enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Oracle Cloud Infrastructure Console stöder **SP** -initierad SSO.
* När du har konfigurerat Oracle Cloud Infrastructure-konsolen kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Lägga till Oracle Cloud Infrastructure-konsolen från galleriet

Om du vill konfigurera integreringen av Oracle Cloud Infrastructure-konsolen i Azure AD måste du lägga till Oracle Cloud Infrastructure-konsolen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Oracle Cloud Infrastructure Console** i sökrutan.
1. Välj **Oracle Cloud Infrastructure-konsolen** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med en Oracle Cloud Infrastructure-konsol med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Oracle Cloud Infrastructure-konsolen.

Om du vill konfigurera och testa Azure AD SSO med Oracle Cloud Infrastructure Console, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera en Oracle Cloud Infrastructure-konsol](#configure-oracle-cloud-infrastructure-console)** för att konfigurera SSO-inställningar på program sidan.
    1. **[Skapa en administratörs konsol för Oracle Cloud-konsolen](#create-oracle-cloud-infrastructure-console-test-user)** så att den har en motsvarighet till B. Simon i Oracle Cloud Infrastructure-konsolen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Oracle Cloud Infrastructure-konsolen** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

   > [!NOTE]
   > Du hämtar metadata-filen för tjänst leverantören från avsnittet **Konfigurera enkel inloggning i Oracle Cloud Infrastructure-konsolen** i självstudien.
    
   1. Klicka på **Ladda upp metadatafil**.

   1. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

   1. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i text rutan **grundläggande SAML-konfigurations** avsnitt.
    
      > [!NOTE]
      > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

      I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [support teamet för Oracle Cloud Infrastructure Console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Program varan för Oracle Cloud Infrastructure-konsolen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

   ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig program för Oracle Cloud Infrastructure-konsolen att fler attribut skickas tillbaka i SAML-svar. I avsnittet **användarattribut &-anspråk** i dialog rutan **grupp anspråk (förhands granskning)** utför du följande steg:

   1. Klicka på **pennan** bredvid **namn-ID-värde**.

   1. Välj **beständigt** som **Välj namn identifierare format**.
 
   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klicka på **pennan** bredvid **grupper som returneras i anspråk**.

   1. Välj **säkerhets grupper** i alternativ listan.

   1. Välj **källattribut** för **grupp-ID**.

   1. Markera **anpassa namnet på grupp anspråket**.

   1. Skriv **GroupName**i text rutan **namn** .

   1. I text rutan **namnrymd (valfritt)** skriver du `https://auth.oraclecloud.com/saml/claims` .

   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. I avsnittet **Konfigurera en Oracle Cloud Infrastructure-konsol** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Oracle Cloud Infrastructure-konsolen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Oracle Cloud Infrastructure-konsol**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurera en Oracle Cloud Infrastructure-konsol

1. Logga in på Oracle Cloud Infrastructure-konsolen som administratör i ett annat webbläsarfönster.

1. Klicka på den vänstra sidan av menyn och klicka på **identitet** och navigera sedan till **Federation**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config01.png)

1. Spara **metadata-filen för tjänst leverantören** genom att klicka på länken **Ladda ned det här dokumentet** och ladda upp den i avsnittet **grundläggande SAML-konfiguration** i Azure Portal och klicka sedan på **Lägg till identitets leverantör**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config02.png)

1. Utför följande steg på popup-sidan **Lägg till identitets leverantör** :

   ![Konfiguration](./media/oracle-cloud-tutorial/config03.png)

   1. I text rutan **namn** anger du ditt namn.

   1. Ange beskrivningen i text rutan **Beskrivning** .

   1. Välj **Microsoft Active Directory Federation Service (ADFS) eller SAML 2,0-kompatibel identitets leverantör** som **typ**.

   1. Klicka på **Bläddra** för att ladda upp XML-metadata för federationsmetadata som du har laddat ned från Azure Portal.

   1. Klicka på **Fortsätt** och gå till avsnittet **Redigera identitetsprovider** och utför följande steg:

      ![Konfiguration](./media/oracle-cloud-tutorial/config09.png)

   1. **Gruppen med identitets leverantörer** ska väljas som anpassad grupp. GRUPP-ID: t ska vara GUID för gruppen från Azure Active Directory. Gruppen måste mappas med motsvarande grupp i fältet **OCI-grupp** .

   1. Du kan mappa flera grupper efter installationen i Azure Portal och din organisation behöver. Klicka på **+ Lägg till mappning** för att lägga till så många grupper som du behöver.

   1. Klicka på **Skicka**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Skapa test användare för Oracle Cloud Infrastructure-konsolen

 Oracle Cloud Infrastructure Console stöder just-in-Time-etablering, som är som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas inte under ett försök att komma åt och behöver inte heller skapa användaren.

### <a name="test-sso"></a>Testa SSO

När du väljer konsol panelen för Oracle Cloud Infrastructure på åtkomst panelen omdirigeras du till Oracle Cloud Infrastructure-konsolens inloggnings sida. Välj **identitets leverantören** på den nedrullningsbara menyn och klicka på **Fortsätt** enligt beskrivningen nedan för att logga in. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfiguration](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Så här skyddar du Oracle Cloud Infrastructure-konsolen med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
