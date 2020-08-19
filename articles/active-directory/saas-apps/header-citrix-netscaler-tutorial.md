---
title: 'Självstudie: Azure Active Directory enkel inloggning med Citrix NetScaler (huvud-baserad autentisering) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning (SSO) mellan Azure Active Directory och Citrix NetScaler med hjälp av rubrik-baserad autentisering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.openlocfilehash: 48fedf7f3a73ce7fde60a1df80d971a5d7f88dd7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Självstudie: Azure Active Directory enkel inloggning med Citrix NetScaler (huvud-baserad autentisering)

I den här självstudien får du lära dig att integrera Citrix NetScaler med Azure Active Directory (Azure AD). När du integrerar Citrix NetScaler med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Citrix NetScaler.
* Gör det möjligt för användarna att logga in automatiskt till Citrix NetScaler med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Citrix NetScaler-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. I självstudien ingår följande scenarier:

* **SP-initierad** SSO för Citrix NetScaler

* **Just in Time** User-etablering för Citrix NetScaler

* [Huvud-baserad autentisering för Citrix NetScaler](#publish-the-web-server)

* [Kerberos-baserad autentisering för Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Lägg till Citrix NetScaler från galleriet

Om du vill integrera Citrix NetScaler med Azure AD lägger du först till Citrix NetScaler i listan över hanterade SaaS-appar från galleriet:

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.

1. På den vänstra menyn väljer du **Azure Active Directory**.

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

1. Välj **nytt program**om du vill lägga till ett nytt program.

1. I avsnittet **Lägg till från galleriet** , anger du **Citrix NetScaler** i rutan Sök.

1. I resultaten väljer du **Citrix NetScaler**och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurera och testa enkel inloggning med Azure AD för Citrix NetScaler

Konfigurera och testa Azure AD SSO med Citrix NetScaler med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Citrix NetScaler.

Om du vill konfigurera och testa Azure AD SSO med Citrix NetScaler, fyller du i följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) – så att användarna kan använda den här funktionen.

    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med B. Simon.

    1. [Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user) – för att aktivera B. Simon att använda Azure AD SSO.

1. [Konfigurera Citrix NetScaler SSO](#configure-citrix-netscaler-sso) – för att konfigurera SSO-inställningar på program sidan.

    * [Skapa en Citrix NetScaler-test användare](#create-a-citrix-netscaler-test-user) – för att få en motsvarighet till B. Simon i Citrix NetScaler som är länkad till Azure AD-representation av användaren.

1. [Testa SSO](#test-sso) – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Slutför de här stegen för att aktivera Azure AD SSO med hjälp av Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)går du till fönstret **Citrix NetScaler** Application Integration under **Hantera**och väljer **enkel inloggning**.

1. Välj **SAML**i fönstret **Välj en enkel inloggnings metod** .

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du ikonen Penn **redigering** för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** kan du konfigurera programmet i **IDP-initierat** läge:

    1. I text rutan **identifierare** anger du en URL som har följande mönster: `https://<Your FQDN>`

    1. I text rutan **svars-URL** anger du en URL som har följande mönster: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Om du vill konfigurera programmet i **SP-initierat** läge väljer du **Ange ytterligare URL: er** och Slutför följande steg:

    * I text rutan **inloggnings-URL** anger du en URL som har följande mönster: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * URL: erna som används i det här avsnittet är inte verkliga värden. Uppdatera värdena med de faktiska värdena för identifierare, svars-URL och inloggnings-URL. Kontakta [support teamet för Citrix NetScaler-klienten](https://www.citrix.com/contact/technical-support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    > * Om du vill konfigurera SSO måste URL: erna vara tillgängliga från offentliga webbplatser. Du måste aktivera brand väggen eller andra säkerhets inställningar på Citrix NetScaler-sidan för att Enble Azure AD för att kunna publicera token på den konfigurerade URL: en.

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , för **URL för app Federation-Metadata**, kopierar du URL: en och sparar den i anteckningar.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Citrix NetScaler-programmet förväntar sig att SAML-intyg ska vara i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Välj **redigerings** ikonen och ändra attributets mappningar.

    ![Redigera mappningen av SAML-attribut](common/edit-attribute.png)

1. Citrix NetScaler-programmet förväntar sig också några fler attribut som ska skickas tillbaka i SAML-svaret. I dialog rutan **användarattribut** under **användar anspråk**utför du följande steg för att lägga till SAML-token-attributen som visas i tabellen:

    | Name | Källattribut|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I text rutan **namn** anger du det attributnamn som visas för raden.

    1. Lämna **Namnrymd** tom.

    1. För **attribut**väljer du **källa**.

    1. I listan **källattribut** anger du det attributvärde som visas för den raden.

    1. Välj **OK**.

    1. Välj **Spara**.

1. I avsnittet **Konfigurera Citrix Netscaleer** kopierar du relevanta URL: er baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. På den vänstra menyn i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.

1. Välj **ny användare** längst upp i fönstret.

1. I **användar** egenskaper slutför du de här stegen:

   1. Som **namn**anger du `B.Simon` .  

   1. För **användar namn**anger du _username@companydomain.extension_ . Till exempel `B.Simon@contoso.com`.

   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned eller kopiera värdet som visas i **lösen ordet**.

   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du användare B. Simon för att använda Azure SSO genom att ge användaren åtkomst till Citrix NetScaler.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.

1. I listan program väljer du **Citrix NetScaler**.

1. I Översikt över appen, under **Hantera**, väljer **du användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** . Välj **Välj**.

1. Om du förväntar dig ett roll värde i SAML-intyget väljer du den relevanta rollen för användaren i listan i dialog rutan **Välj roll** och väljer sedan **Välj**.

1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurera Citrix NetScaler SSO

Välj en länk för steg för den typ av autentisering som du vill konfigurera:

- [Konfigurera Citrix NetScaler SSO för huvud-baserad autentisering](#publish-the-web-server)

- [Konfigurera Citrix NetScaler SSO för Kerberos-baserad autentisering](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicera webb servern 

Så här skapar du en virtuell server:

1. Välj **Traffic Management**  >  **belastnings Utjämnings**  >  **tjänster**för trafik hantering.
    
1. Välj **Lägg till**.

    ![Citrix NetScaler-konfiguration – fönstret tjänster](./media/header-citrix-netscaler-tutorial/web01.png)

1. Ange följande värden för webb servern som kör programmen:

   * **Tjänstens namn**
   * **Server-IP/befintlig server**
   * **Protokoll**
   * **Port**

     ![Konfigurations fönstret Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Konfigurera belastningsutjämnaren

Så här konfigurerar du belastningsutjämnaren:

1. Gå till **Traffic Management**  >  **belastnings Utjämnings**  >  **virtuella servrar**för trafik hantering.

1. Välj **Lägg till**.

1. Ange följande värden enligt beskrivningen i följande skärm bild:

    * **Namn**
    * **Protokoll**
    * **IP-adress**
    * **Port**

1. Välj **OK**.

    ![Citrix NetScaler-konfiguration – grundläggande inställnings fönster](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Bind den virtuella servern

Så här binder du belastningsutjämnaren till den virtuella servern:

1. I fönstret **tjänster och tjänst grupper** väljer du **ingen belastnings utjämning virtuell server tjänst bindning**.

   ![Citrix NetScaler-konfiguration – belastnings utjämning virtuell server tjänst bindnings fönster](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Kontrol lera inställningarna som visas i följande skärm bild och välj sedan **Stäng**.

   ![Citrix NetScaler-konfiguration – verifiera bindningen för virtuella server tjänster](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Bind certifikatet

Om du vill publicera den här tjänsten som TLS, binder du Server certifikatet och testar sedan ditt program:

1. Under **certifikat**väljer du **inget Server certifikat**.

   ![Citrix NetScaler-konfiguration – fönstret Server certifikat](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Kontrol lera inställningarna som visas i följande skärm bild och välj sedan **Stäng**.

   ![Citrix NetScaler-konfiguration – verifiera certifikatet](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

Om du vill konfigurera Citrix ADC SAML-profilen, fyller du i följande avsnitt:

### <a name="create-an-authentication-policy"></a>Skapa en autentiseringsprincip

Så här skapar du en autentiseringsprincip:

1. Gå till **Security**  >  **AAA –** principer för autentisering av program trafik  >  **Policies**  >  **Authentication**  >  **Authentication Policies**.

1. Välj **Lägg till**.

1. I fönstret **skapa autentiseringsprincip** anger eller väljer du följande värden:

    * **Namn**: Ange ett namn för din autentiseringsprincip.
    * **Åtgärd**: ange **SAML**och välj sedan **Lägg till**.
    * **Uttryck**: ange **Sant**.     
    
    ![Citrix NetScaler-konfiguration – fönstret Skapa autentiseringsprincip](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Välj **Skapa**.

### <a name="create-an-authentication-saml-server"></a>Skapa en SAML-Server för autentisering

Om du vill skapa en SAML-Server för autentisering går du till fönstret **skapa autentiserings-SAML-Server** och utför följande steg:

1. I **namn**anger du ett namn för SAML-servern för autentisering.

1. Under **Exportera SAML-metadata**:

   1. Markera kryss rutan **Importera metadata** .

   1. Ange URL: en för federationsmetadata från det Azure SAML-gränssnitt som du kopierade tidigare.
    
1. Ange relevant URL för **utfärdarens namn**.

1. Välj **Skapa**.

![Citrix NetScaler-konfiguration – skapa autentisering SAML-Server fönstret](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Skapa en virtuell autentiserings Server

Så här skapar du en virtuell autentiserings Server:

1.  Gå till **Security**  >  **AAA –** autentisering av autentisering för program trafik  >  **Policies**  >  **Authentication**  >  **virtuella servrar**.

1.  Välj **Lägg till**och utför sedan följande steg:

    1. I **namn**anger du ett namn för den virtuella autentiserings servern.

    1. Markera kryss rutan **ej adresserbar** .

    1. För **protokoll**väljer du **SSL**.

    1. Välj **OK**.

    ![Citrix NetScaler-konfiguration – fönstret virtuell server för autentisering](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurera den virtuella autentiserings servern så att den använder Azure AD

Ändra två avsnitt för den virtuella autentiserings servern:

1.  I fönstret **avancerade autentiseringsprinciper** väljer du **ingen autentiseringsprincip**.

    ![Citrix NetScaler-konfiguration – fönstret avancerade autentiseringsprinciper](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. I fönstret **princip bindning** väljer du autentiseringsprincip och väljer sedan **BIND**.

    ![Citrix NetScaler-konfiguration – princip bindnings fönstret](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Välj **ingen virtuell server för belastnings utjämning**i fönstret **formulärbaserade virtuella servrar** .

    ![Citrix NetScaler-konfiguration – formulär baserade virtuella servrar-fönstret](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. För **autentiserings-FQDN**anger du ett fullständigt kvalificerat domän namn (FQDN) (obligatoriskt).

1. Välj den virtuella belastnings Utjämnings server som du vill skydda med Azure AD-autentisering.

1. Välj **BIND**.

    ![Citrix NetScaler-konfiguration – belastnings utjämning virtuell server bindnings fönster](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Se till att du väljer **slutförd** i konfigurations fönstret för den **virtuella verifierings servern** .

1. Om du vill verifiera ändringarna går du till programmets URL i en webbläsare. Du bör se inloggnings sidan för klient organisationen i stället för den oautentiserade åtkomst som du hade sett tidigare.

    ![Citrix NetScaler-konfiguration – en inloggnings sida i en webbläsare](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Konfigurera Citrix NetScaler SSO för huvud-baserad autentisering

### <a name="configure-citrix-adc"></a>Konfigurera Citrix ADC

Om du vill konfigurera Citrix ADC för huvud-baserad autentisering, fyller du i följande avsnitt.

#### <a name="create-a-rewrite-action"></a>Skapa en Skriv åtgärd

1. Gå till **AppExpert**  >  **Skriv**om Skriv  >  **åtgärder**.
 
    ![Citrix NetScaler-konfiguration – Skriv åtgärds fönstret](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Välj **Lägg till**och utför sedan följande steg:

    1. I **namn**anger du ett namn för åtgärden Skriv om.

    1. För **typ**anger du **INSERT_HTTP_HEADER**.

    1. För **huvud namn**anger du ett rubrik namn (i det här exemplet använder vi _SecretID_).

    1. För **uttryck**anger du **AAA. Användarvänlig. ATTRIBUTE ("mySecretID")**, där **MySecretID** är Azure AD SAML-anspråket som skickades till Citrix ADC.

    1. Välj **Skapa**.

    ![Citrix NetScaler-konfiguration – skapa åtgärds fönstret för omskrivning](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Skapa en princip för att skriva om

1.  Gå till **AppExpert**  >  **Skriv**om  >  **omskrivnings principer**.
 
    ![Citrix NetScaler-konfiguration – Skriv om principer-fönstret](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Välj **Lägg till**och utför sedan följande steg:

    1. I **namn**anger du ett namn för principen för att skriva om.

    1. För **åtgärd**väljer du den Skriv åtgärd som du skapade i föregående avsnitt.

    1. För **uttryck**anger du **Sant**.

    1. Välj **Skapa**.

    ![Citrix NetScaler-konfiguration – skapa princip för att skriva över](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Bind en omskrivnings princip till en virtuell server

Så här binder du en omskrivnings princip till en virtuell server med hjälp av det grafiska användar gränssnittet:

1. Gå till **Traffic Management**  >  **belastnings Utjämnings**  >  **virtuella servrar**för trafik hantering.

1. I listan över virtuella servrar väljer du den virtuella server som du vill binda den omskrivnings principen till och väljer sedan **Öppna**.

1. I fönstret **belastnings utjämning virtuell server** under **Avancerade inställningar**väljer du **principer**. Alla principer som har kon figurer ATS för NetScaler-instansen visas i listan.
 
    ![Citrix NetScaler-konfiguration – fönster för virtuell server med belastnings utjämning](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler-konfiguration – fönster för virtuell server med belastnings utjämning](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Markera kryss rutan bredvid namnet på den princip som du vill binda till den här virtuella servern.
 
    ![Citrix NetScaler-konfiguration – belastnings utjämning virtuell server trafik princip bindnings fönster](./media/header-citrix-netscaler-tutorial/header08.png)

1. I dialog rutan **Välj typ** :

    1. För **Välj princip**väljer du **trafik**.

    1. För **Välj typ**väljer du **begäran**.

    ![Dialog rutan Citrix NetScaler-konfiguration – principer](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Välj **OK**. Ett meddelande i statusfältet visar att principen har kon figurer ATS korrekt.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Ändra SAML-servern för att extrahera attribut från ett anspråk

1.  Gå till **Security**  >  **AAA – program trafik**  >  **principer**  >  **autentisering**  >  **avancerade principer**  >  **Åtgärds**  >  **servrar**.

1.  Välj lämplig SAML-Server för autentisering för programmet.
 
    ![Citrix NetScaler-konfiguration – konfigurera autentisering SAML-Server fönstret](./media/header-citrix-netscaler-tutorial/header09.png)

1. I smärta för **attribut** anger du de SAML-attribut som du vill extrahera, avgränsade med kommatecken. I vårt exempel anger vi attributet `mySecretID` .
 
    ![Citrix NetScaler-konfiguration – attribut-fönstret](./media/header-citrix-netscaler-tutorial/header10.png)

1. Om du vill kontrol lera åtkomsten går du till URL: en i en webbläsare och letar efter SAML-attributet under **headers-samlingen**.

    ![Citrix NetScaler-konfiguration – rubrik samling på URL: en](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Skapa en Citrix NetScaler-test användare

I det här avsnittet skapas en användare som heter B. Simon i Citrix NetScaler. Citrix NetScaler stöder just-in-Time-användar etablering, som är aktiverat som standard. Det finns ingen åtgärd att ta med i det här avsnittet. Om en användare inte redan finns i Citrix NetScaler, skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kan du kontakta [support teamet för Citrix NetScaler-klienten](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen för att skapa en netscaleor-panel i åtkomst panelen, bör du loggas in automatiskt på den Citrix netscaleer som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Citrix NetScaler med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera Citrix NetScaler enkel inloggning för Kerberos-baserad autentisering](citrix-netscaler-tutorial.md)
