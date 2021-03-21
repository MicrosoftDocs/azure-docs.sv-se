---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning med Citrix ADC (Kerberos-baserad autentisering) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning (SSO) mellan Azure Active Directory-och Citrix-ADC med hjälp av Kerberos-baserad autentisering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736447"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>Självstudie: Azure Active Directory integrering med enkel inloggning med Citrix ADC (Kerberos-baserad autentisering)

I den här självstudien får du lära dig att integrera Citrix ADC med Azure Active Directory (Azure AD). När du integrerar Citrix ADC med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Citrix ADC.
* Gör det möjligt för användarna att logga in automatiskt på Citrix ADC med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Citrix ADC-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. I självstudien ingår följande scenarier:

* **SP-initierad** SSO för Citrix ADC

* **Just in Time** User-etablering för Citrix ADC

* [Kerberos-baserad autentisering för Citrix ADC](#publish-the-web-server)

* [Huvud-baserad autentisering för Citrix ADC](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>Lägg till Citrix ADC från galleriet

Om du vill integrera Citrix ADC med Azure AD lägger du först till Citrix ADC i listan över hanterade SaaS-appar från galleriet:

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.

1. På den vänstra menyn väljer du **Azure Active Directory**.

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

1. Välj **nytt program** om du vill lägga till ett nytt program.

1. Skriv **Citrix ADC** i sökrutan i avsnittet **Lägg till från galleriet** .

1. I resultaten väljer du **Citrix ADC** och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Konfigurera och testa Azure AD SSO för Citrix ADC

Konfigurera och testa Azure AD SSO med Citrix ADC genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Citrix ADC.

Utför följande steg för att konfigurera och testa Azure AD SSO med Citrix ADC:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) – så att användarna kan använda den här funktionen.

    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med B. Simon.

    1. [Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user) – för att aktivera B. Simon att använda Azure AD SSO.

1. [Konfigurera Citrix ADC SSO](#configure-citrix-adc-sso) – för att konfigurera SSO-inställningar på program sidan.

    * [Skapa en Citrix ADC-test](#create-a-citrix-adc-test-user) för att få en motsvarighet till B. Simon i Citrix ADC som är länkad till Azure AD-representation av användaren.

1. [Testa SSO](#test-sso) – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Slutför de här stegen för att aktivera Azure AD SSO med hjälp av Azure Portal:

1. I Azure Portal i fönstret **Citrix ADC** Application Integration under **Hantera** väljer du **enkel inloggning**.

1. Välj **SAML** i fönstret **Välj en enkel inloggnings metod** .

1. I fönstret **Konfigurera enskilda Sign-On med SAML** väljer du ikonen Penn **redigering** för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** kan du konfigurera programmet i **IDP-initierat** läge:

    1. I text rutan **identifierare** anger du en URL som har följande mönster: `https://<Your FQDN>`

    1. I text rutan **svars-URL** anger du en URL som har följande mönster: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Om du vill konfigurera programmet i **SP-initierat** läge väljer du **Ange ytterligare URL: er** och Slutför följande steg:

    * I text rutan **inloggnings-URL** anger du en URL som har följande mönster: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * URL: erna som används i det här avsnittet är inte verkliga värden. Uppdatera värdena med de faktiska värdena för identifierare, svars-URL och inloggnings-URL. Kontakta [Citrix ADC-klientens support team](https://www.citrix.com/contact/technical-support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    > * Om du vill konfigurera SSO måste URL: erna vara tillgängliga från offentliga webbplatser. Du måste aktivera brand väggen eller andra säkerhets inställningar på Citrix ADC-sidan för att Enble Azure AD ska kunna publicera token på den konfigurerade URL: en.

1. I avsnittet **Konfigurera enskilda Sign-On med SAML** i avsnittet **SAML-signeringscertifikat** , för **URL för app Federation-Metadata**, kopierar du URL: en och sparar den i anteckningar.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Citrix ADC** kopierar du de relevanta URL: erna baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. På den vänstra menyn i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.

1. Välj **ny användare** längst upp i fönstret.

1. I **användar** egenskaper slutför du de här stegen:

   1. Som **namn** anger du `B.Simon` .  

   1. För **användar namn** anger du _username@companydomain.extension_ . Till exempel `B.Simon@contoso.com`.

   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned eller kopiera värdet som visas i **lösen ordet**.

   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du användare B. Simon för att använda Azure SSO genom att ge användaren åtkomst till Citrix ADC.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.

1. I listan program väljer du **Citrix ADC**.

1. I Översikt över appen, under **Hantera**, väljer **du användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** . Välj **Välj**.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-citrix-adc-sso"></a>Konfigurera Citrix ADC SSO

Välj en länk för steg för den typ av autentisering som du vill konfigurera:

- [Konfigurera Citrix ADC SSO för Kerberos-baserad autentisering](#publish-the-web-server)

- [Konfigurera Citrix ADC SSO för huvud-baserad autentisering](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicera webb servern 

Så här skapar du en virtuell server:

1. Välj   >  **belastnings Utjämnings**  >  **tjänster** för trafik hantering.
    
1. Välj **Lägg till**.

    ![Citrix ADC-konfiguration – fönstret tjänster](./media/citrix-netscaler-tutorial/web01.png)

1. Ange följande värden för webb servern som kör programmen:

   * **Tjänst namn**
   * **Server-IP/befintlig server**
   * **Protokoll**
   * **Port**

### <a name="configure-the-load-balancer"></a>Konfigurera belastningsutjämnaren

Så här konfigurerar du belastningsutjämnaren:

1. Gå till   >  **belastnings Utjämnings**  >  **virtuella servrar** för trafik hantering.

1. Välj **Lägg till**.

1. Ange följande värden enligt beskrivningen i följande skärm bild:

    * **Namn**
    * **Protokoll**
    * **IP-adress**
    * **Port**

1. Välj **OK**.

    ![Citrix ADC-konfiguration – grundläggande inställnings fönster](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Bind den virtuella servern

Så här binder du belastningsutjämnaren till den virtuella servern:

1. I fönstret **tjänster och tjänst grupper** väljer du **ingen belastnings utjämning virtuell server tjänst bindning**.

   ![Citrix ADC Configuration – belastnings utjämning virtuell server tjänst bindnings fönster](./media/citrix-netscaler-tutorial/bind01.png)

1. Kontrol lera inställningarna som visas i följande skärm bild och välj sedan **Stäng**.

   ![Citrix ADC-konfiguration – verifiera bindningen för virtuella server tjänster](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Bind certifikatet

Om du vill publicera den här tjänsten som TLS, binder du Server certifikatet och testar sedan ditt program:

1. Under **certifikat** väljer du **inget Server certifikat**.

   ![Citrix ADC-konfiguration – fönstret Server certifikat](./media/citrix-netscaler-tutorial/bind03.png)

1. Kontrol lera inställningarna som visas i följande skärm bild och välj sedan **Stäng**.

   ![Citrix ADC-konfiguration – verifiera certifikatet](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

Om du vill konfigurera Citrix ADC SAML-profilen, fyller du i följande avsnitt.

### <a name="create-an-authentication-policy"></a>Skapa en autentiseringsprincip

Så här skapar du en autentiseringsprincip:

1. Gå till **Security**  >  **AAA –** principer för autentisering av program trafik  >    >    >  .

1. Välj **Lägg till**.

1. I fönstret **skapa autentiseringsprincip** anger eller väljer du följande värden:

    * **Namn**: Ange ett namn för din autentiseringsprincip.
    * **Åtgärd**: ange **SAML** och välj sedan **Lägg till**.
    * **Uttryck**: ange **Sant**.     
    
    ![Citrix ADC-konfiguration – fönstret Skapa autentiseringsprincip](./media/citrix-netscaler-tutorial/policy01.png)

1. Välj **Skapa**.

### <a name="create-an-authentication-saml-server"></a>Skapa en SAML-Server för autentisering

Om du vill skapa en SAML-Server för autentisering går du till fönstret **skapa autentiserings-SAML-Server** och utför följande steg:

1. I **namn** anger du ett namn för SAML-servern för autentisering.

1. Under **Exportera SAML-metadata**:

   1. Markera kryss rutan **Importera metadata** .

   1. Ange URL: en för federationsmetadata från det Azure SAML-gränssnitt som du kopierade tidigare.
    
1. Ange relevant URL för **utfärdarens namn**.

1. Välj **Skapa**.

![Citrix ADC-konfiguration – fönstret Skapa autentisering SAML-Server](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Skapa en virtuell autentiserings Server

Så här skapar du en virtuell autentiserings Server:

1.  Gå till **Security**  >  **AAA –** autentisering av autentisering för program trafik  >    >    >  **virtuella servrar**.

1.  Välj **Lägg till** och utför sedan följande steg:

    1. I **namn** anger du ett namn för den virtuella autentiserings servern.

    1. Markera kryss rutan **ej adresserbar** .

    1. För **protokoll** väljer du **SSL**.

    1. Välj **OK**.
    
1. Välj **Fortsätt**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurera den virtuella autentiserings servern så att den använder Azure AD

Ändra två avsnitt för den virtuella autentiserings servern:

1.  I fönstret **avancerade autentiseringsprinciper** väljer du **ingen autentiseringsprincip**.

    ![Citrix ADC-konfiguration – fönstret avancerade autentiseringsprinciper](./media/citrix-netscaler-tutorial/virtual01.png)

1. I fönstret **princip bindning** väljer du autentiseringsprincip och väljer sedan **BIND**.

    ![Citrix ADC-konfiguration – princip bindnings fönstret](./media/citrix-netscaler-tutorial/virtual02.png)

1. Välj **ingen virtuell server för belastnings utjämning** i fönstret **formulärbaserade virtuella servrar** .

    ![Citrix ADC-fönstret för konfigurations formulär baserade virtuella servrar](./media/citrix-netscaler-tutorial/virtual03.png)

1. För **autentiserings-FQDN** anger du ett fullständigt kvalificerat domän namn (FQDN) (obligatoriskt).

1. Välj den virtuella belastnings Utjämnings server som du vill skydda med Azure AD-autentisering.

1. Välj **BIND**.

    ![Citrix ADC Configuration – belastnings utjämning virtuell server bindnings fönster](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Se till att du väljer **slutförd** i konfigurations fönstret för den **virtuella verifierings servern** .

1. Om du vill verifiera ändringarna går du till programmets URL i en webbläsare. Du bör se inloggnings sidan för klient organisationen i stället för den oautentiserade åtkomst som du hade sett tidigare.

    ![Citrix ADC-konfiguration – en inloggnings sida i en webbläsare](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Konfigurera Citrix ADC SSO för Kerberos-baserad autentisering

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Skapa ett Kerberos Delegerings konto för Citrix ADC

1. Skapa ett användar konto (i det här exemplet använder vi _AppDelegation_).

    ![Citrix ADC-konfiguration – egenskaps fönstret](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Konfigurera ett SPN för värd för det här kontot. 

    Exempel: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    I det här exemplet:

    * `IDENTT.WORK` är domänens FQDN.
    * `identt` är domänens NetBIOS-namn.
    * `appdelegation` är namnet på användar kontot för delegering.

1. Konfigurera delegering för webb servern så som visas på följande skärm bild:
 
    ![Citrix ADC Configuration-delegering under rutan Egenskaper](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > I skärm bilden exempel är det interna webb Server namnet som kör WIA-platsen (Windows Integrated Authentication) _CWEB2_.

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (Kerberos Delegerings konton)

Så här konfigurerar du Citrix ADC AAA KCD-kontot:

1.  Gå till **Citrix Gateway**  >  **AAA KCD-konton (Kerberos-begränsad delegering)**.

1.  Välj **Lägg till** och ange eller Välj följande värden:

    * **Namn**: Ange ett namn för KCD-kontot.

    * **Sfär**: ange domänen och tillägget i versaler.

    * **Tjänst-SPN**: `http/<host/fqdn>@<DOMAIN.COM>` .
    
        > [!NOTE]
        > `@DOMAIN.COM` är obligatoriskt och måste vara versaler. Exempel: `http/cweb2@IDENTT.WORK`.

    * **Delegerad användare**: Ange det delegerade användar namnet.

    * Markera kryss rutan **lösen ord för delegerad användare** och ange och bekräfta ett lösen ord.

1. Välj **OK**.
 
    ![Citrix ADC-konfiguration – fönstret Konfigurera KCD-konto](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix Traffic policy och trafik profil

Så här konfigurerar du Citrix Traffic policy och trafik profil:

1.  Gå till **Security**  >  **AAA – principer för program trafik**  >    >  **trafik principer, profiler och formulär SSO ProfilesTraffic-principer**.

1.  Välj **trafik profiler**.

1.  Välj **Lägg till**.

1.  Om du vill konfigurera en trafik profil anger eller väljer du följande värden.

    * **Namn**: Ange ett namn för trafik profilen.

    * **Enkel inloggning**: Välj **på**.

    * **KCD-konto**: Välj det KCD-konto som du skapade i föregående avsnitt.

1. Välj **OK**.

    ![Citrix ADC-konfiguration – konfigurera Traffic Profile-fönstret](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Välj **trafik princip**.

1.  Välj **Lägg till**.

1.  Om du vill konfigurera en trafik princip anger eller väljer du följande värden:

    * **Namn**: Ange ett namn för Traffic-principen.

    * **Profil**: Välj den trafik profil som du skapade i föregående avsnitt.

    * **Uttryck**: ange **Sant**.

1. Välj **OK**.

    ![Citrix ADC-konfiguration – fönstret Konfigurera trafik princip](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Bind en trafik princip till en virtuell server i Citrix

Så här binder du en trafik princip till en virtuell server med hjälp av det grafiska användar gränssnittet:

1. Gå till   >  **belastnings Utjämnings**  >  **virtuella servrar** för trafik hantering.

1. I listan över virtuella servrar väljer du den virtuella server som du vill binda den omskrivnings principen till och väljer sedan **Öppna**.

1. I fönstret **belastnings utjämning virtuell server** under **Avancerade inställningar** väljer du **principer**. Alla principer som har kon figurer ATS för NetScaler-instansen visas i listan.
 
    ![Citrix ADC Configuration – fönstret virtuell server för belastnings utjämning](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Dialog rutan Citrix ADC Configuration – policys](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Markera kryss rutan bredvid namnet på den princip som du vill binda till den här virtuella servern.
 
    ![Citrix ADC Configuration – belastnings utjämning virtuell server trafik princip bindnings fönster](./media/citrix-netscaler-tutorial/kerberos09.png)

1. I dialog rutan **Välj typ** :

    1. För **Välj princip** väljer du **trafik**.

    1. För **Välj typ** väljer du **begäran**.

    ![Citrix ADC-konfiguration – Välj typ av fönster](./media/citrix-netscaler-tutorial/kerberos08.png)

1. När principen är kopplad väljer du **slutförd**.
 
    ![Citrix ADC-konfiguration – policys-fönstret](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Testa bindningen med hjälp av WIA-webbplatsen.

    ![Citrix ADC-konfiguration – en test sida i en webbläsare](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Skapa en Citrix ADC-test användare

I det här avsnittet skapas en användare som heter B. Simon i Citrix ADC. Citrix ADC stöder just-in-Time-etablering, som är aktiverat som standard. Det finns ingen åtgärd att ta med i det här avsnittet. Om en användare inte redan finns i Citrix ADC skapas en ny efter autentiseringen.

> [!NOTE]
> Om du behöver skapa en användare manuellt kan du kontakta [support teamet för Citrix ADC-klienten](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Citrix ADC-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Citrix ADC-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Citrix ADC i Mina appar omdirigeras detta till Citrix ADC-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Citrix ADC kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).