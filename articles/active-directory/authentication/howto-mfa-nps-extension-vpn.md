---
title: VPN med Azure MFA med NPS-tillägget-Azure Active Directory
description: Integrera din VPN-infrastruktur med Azure MFA genom att använda nätverks princip Server tillägget för Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 394a4c171153ecf50ff5d755c42e3c5f939b2ec7
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507186"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrera din VPN-infrastruktur med Azure MFA genom att använda nätverks princip Server tillägget för Azure

Nätverks princip Server (NPS)-tillägget för Azure gör det möjligt för organisationer att skydda Remote Authentication Dial-In User Service (RADIUS) klientautentisering med hjälp av molnbaserad [Azure-Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), vilket ger tvåstegsverifiering.

Den här artikeln innehåller instruktioner för att integrera NPS-infrastrukturen med MFA med hjälp av NPS-tillägget för Azure. Den här processen möjliggör säker tvåstegsverifiering för användare som försöker ansluta till nätverket med hjälp av en VPN-anslutning.

Nätverks princip-och nätverks åtkomst tjänster ger organisationer möjlighet att:

* Tilldela en central plats för hantering och kontroll av nätverks begär Anden för att ange:

  * Vem kan ansluta

  * Vilka tider på dags anslutningar som tillåts

  * Varaktigheten för anslutningar

  * Den säkerhets nivå som klienterna måste använda för att ansluta

    I stället för att ange principer på varje VPN-eller Fjärrskrivbordsgateway-server gör du det efter att de har en central plats. RADIUS-protokollet används för att tillhandahålla centraliserad autentisering, auktorisering och redovisning (AAA).

* Upprätta och framtvinga Network Access Protection (NAP) hälso principer för klient (NAP) som avgör om enheter beviljas obegränsad eller begränsad åtkomst till nätverks resurser.

* Tillhandahålla ett sätt att genomdriva autentisering och auktorisering för åtkomst till 802.1 x-kompatibla trådlösa åtkomst punkter och Ethernet-växlar.
  Mer information finns i [nätverks Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

För att förbättra säkerheten och tillhandahålla en hög nivå av efterlevnad kan organisationer integrera NPS med Azure Multi-Factor Authentication för att säkerställa att användarna använder tvåstegsverifiering för att ansluta till den virtuella porten på VPN-servern. För att användare ska beviljas åtkomst måste de ange kombinationen av användar namn och lösen ord och annan information som de kontrollerar. Den här informationen måste vara betrodd och inte lätt att duplicera. Den kan innehålla ett mobiltelefon nummer, ett Landline nummer eller ett program på en mobil enhet.

Innan Azure-tillägget är tillgängligt för Azure måste kunder som vill implementera tvåstegsverifiering för integrerade NPS-och MFA-miljöer konfigurera och underhålla en separat MFA-server i en lokal miljö. Den här typen av autentisering erbjuds av Fjärrskrivbordsgateway och Azure Multi-Factor Authentication-server med RADIUS.

Med NPS-tillägget för Azure kan organisationer skydda RADIUS-klientautentisering genom att distribuera antingen en lokal baserad MFA-lösning eller en molnbaserad MFA-lösning.

## <a name="authentication-flow"></a>Autentiseringsflöde

När användarna ansluter till en virtuell port på en VPN-server måste de först autentiseras med hjälp av olika protokoll. Protokollen gör det möjligt att använda en kombination av användar namn och lösen ord och certifikatbaserad autentiseringsmetoder.

Förutom att autentisera och verifiera sin identitet måste användarna ha lämpliga inloggnings behörigheter. I enkla implementeringar är inloggnings behörigheter som tillåter åtkomst inställda direkt på Active Directory användar objekt.

![Fliken fjärråtkomst i Active Directory användare och datorer användar egenskaper](./media/howto-mfa-nps-extension-vpn/image1.png)

I enkla implementeringar beviljas eller nekar varje VPN-Server åtkomst baserat på principer som definieras på varje lokal VPN-server.

I större och mer skalbara implementeringar centraliseras de principer som beviljar eller nekar VPN-åtkomst på RADIUS-servrar. I dessa fall fungerar VPN-servern som en åtkomst Server (RADIUS-klient) som vidarebefordrar anslutnings begär Anden och konto meddelanden till en RADIUS-server. För att ansluta till den virtuella porten på VPN-servern måste användarna autentiseras och uppfylla de villkor som definieras centralt på RADIUS-servrar.

När NPS-tillägget för Azure är integrerat med NPS är ett lyckat resultat för autentiserings flödet enligt följande:

1. VPN-servern tar emot en autentiseringsbegäran från en VPN-användare som innehåller användar namnet och lösen ordet för att ansluta till en resurs, till exempel en fjärrskrivbordssession.
2. VPN-servern fungerar som en RADIUS-klient och konverterar begäran till ett meddelande om RADIUS- *åtkomstbegäran* och skickar det (med ett krypterat lösen ord) till RADIUS-servern där NPS-tillägget är installerat.
3. Kombinationen av användar namn och lösen ord verifieras i Active Directory. Om användar namnet eller lösen ordet är felaktigt skickar RADIUS-servern ett meddelande om *nekad åtkomst* .
4. Om alla villkor, enligt vad som anges i NPS-anslutningsbegäran och nätverks principer, är uppfyllda (till exempel tid på dag eller grupp medlemskaps begränsningar), utlöser NPS-tillägget en begäran om sekundär autentisering med Azure Multi-Factor Authentication.
5. Azure Multi-Factor Authentication kommunicerar med Azure Active Directory, hämtar användarens information och utför den sekundära autentiseringen med hjälp av den metod som har kon figurer ATS av användaren (mobil samtal, textmeddelande eller mobilapp).
6. När MFA-utmaningen lyckas, kommunicerar Azure-Multi-Factor Authentication resultatet till NPS-tillägget.
7. När anslutnings försöket är både autentiserat och auktoriserat skickas *ett meddelande om RADIUS-åtkomstaccepterande till* VPN-servern (RADIUS-klienten) när tillägget är installerat.
8. Användaren beviljas åtkomst till den virtuella porten på VPN-servern och upprättar en krypterad VPN-tunnel.

## <a name="prerequisites"></a>Krav

Det här avsnittet innehåller information om de förutsättningar som måste slutföras innan du kan integrera MFA med VPN. Innan du börjar måste du ha följande krav på plats:

* VPN-infrastruktur
* Rollen nätverks policy och åtkomst tjänster
* Azure Multi-Factor Authentication-licens
* Windows Server-programvara
* Bibliotek
* Azure Active Directory (Azure AD) har synkroniserats med lokal Active Directory
* Azure Active Directory GUID-ID

### <a name="vpn-infrastructure"></a>VPN-infrastruktur

Den här artikeln förutsätter att du har en fungerande VPN-infrastruktur som använder Microsoft Windows Server 2016 och att VPN-servern för närvarande inte har kon figurer ATS för att vidarebefordra anslutnings begär anden till en RADIUS-server. I artikeln konfigurerar du VPN-infrastrukturen så att den använder en central RADIUS-server.

Om du inte har en fungerande VPN-infrastruktur på plats kan du snabbt skapa en genom att följa rikt linjerna i de olika självstudierna för VPN-installationen som du hittar på Microsoft och tredje parts webbplatser.

### <a name="the-network-policy-and-access-services-role"></a>Rollen nätverks policy och åtkomst tjänster

Nätverks princip-och nätverks åtkomst tjänster tillhandahåller funktioner för RADIUS-server och klient. Den här artikeln förutsätter att du har installerat rollen nätverks policy och åtkomst tjänster på en medlems Server eller domänkontrollant i din miljö. I den här guiden konfigurerar du RADIUS för en VPN-konfiguration. Installera rollen nätverks policy och åtkomst tjänster på en annan server *än* VPN-servern.

Information om hur du installerar roll tjänsten nätverks policy och åtkomst tjänster Windows Server 2012 eller senare finns i [installera en NAP-hälsopolicy Server](https://technet.microsoft.com/library/dd296890.aspx). NAP är inaktuellt i Windows Server 2016. En beskrivning av metod tips för NPS, inklusive rekommendationer för att installera NPS på en domänkontrollant finns i [metod tips för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Det krävs en licens för Azure Multi-Factor Authentication och den är tillgänglig via en Azure AD Premium, Enterprise Mobility + Security eller en Multi-Factor Authentication fristående licens. Användnings licenser för Azure MFA, till exempel per användare eller per autentisering, är inte kompatibla med NPS-tillägget. Mer information finns i [så här skaffar du Azure-Multi-Factor Authentication](concept-mfa-licensing.md). I test syfte kan du använda en utvärderings prenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare, med rollen nätverks policy och åtkomst tjänster installerad. Alla steg i den här guiden utfördes med Windows Server 2016.

### <a name="libraries"></a>Bibliotek

Följande bibliotek installeras automatiskt med NPS-tillägget:

-    [Visual C++ Redistributable-paket för Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Microsoft Azure Active Directory-modul för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Om Microsoft Azure Active Directory PowerShell-modulen inte redan finns installeras den med ett konfigurations skript som du kör som en del av installations processen. Du behöver inte installera modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synkroniseras med lokala Active Directory

Om du vill använda NPS-tillägget måste lokala användare synkroniseras med Azure Active Directory och aktive ras för MFA. Den här guiden förutsätter att lokala användare synkroniseras med Azure Active Directory via Azure AD Connect. Instruktioner för att aktivera användare för MFA anges nedan.

Information om Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-ID

Om du vill installera NPS-tillägget måste du känna till GUID för Azure Active Directory. Instruktioner för att hitta GUID för Azure Active Directory finns i nästa avsnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurera RADIUS för VPN-anslutningar

Om du har installerat NPS-rollen på en medlems Server måste du konfigurera den för att autentisera och auktorisera VPN-klienten som begär VPN-anslutningar. 

Det här avsnittet förutsätter att du har installerat rollen nätverks policy och åtkomst tjänster men inte har konfigurerat den för användning i din infrastruktur.

> [!NOTE]
> Om du redan har en fungerande VPN-server som använder en centraliserad RADIUS-server för autentisering kan du hoppa över det här avsnittet.
>

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory

För att fungera korrekt i det här scenariot måste NPS-servern registreras i Active Directory.

1. Öppna Serverhanteraren.

2. I Serverhanteraren väljer du **verktyg**och sedan **nätverks princip Server**.

3. Högerklicka på **NPS (lokal)** i konsolen nätverks princip Server och välj sedan **registrera server i Active Directory**. Välj **OK** två gånger.

    ![Meny alternativet registrera servern i Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Låt konsolen vara öppen för nästa procedur.

### <a name="use-wizard-to-configure-the-radius-server"></a>Använd guiden för att konfigurera RADIUS-servern

Du kan använda ett standard konfigurations alternativ (guide-baserad) eller Avancerat konfigurations alternativ för att konfigurera RADIUS-servern. I det här avsnittet förutsätter vi att du använder det guidebaserade standard konfigurations alternativet.

1. I konsolen nätverks princip server väljer du **NPS (lokal)**.

2. Under **standard konfiguration**väljer du **RADIUS-server för fjärr anslutningar eller VPN-anslutningar**och väljer sedan **Konfigurera VPN eller fjärr anslutning**.

    ![Konfigurera RADIUS-server för fjärr anslutningar eller VPN-anslutningar](./media/howto-mfa-nps-extension-vpn/image3.png)

3. I fönstret **Välj anslutning för fjärr anslutning eller virtuellt privat nätverk** väljer du anslutningar för **virtuella privata nätverk**och väljer sedan **Nästa**.

    ![Konfigurera anslutningar för virtuella privata nätverk](./media/howto-mfa-nps-extension-vpn/image4.png)

4. I fönstret **Ange fjärr anslutning eller VPN-Server** väljer du **Lägg till**.

5. I fönstret **ny RADIUS-klient** anger du ett eget namn, anger det matchade namnet eller IP-adressen för VPN-servern och anger sedan ett delat hemligt lösen ord. Gör det delade hemliga lösen ordet långt och komplicerat. Registrera den eftersom du behöver den i nästa avsnitt.

    ![Skapa ett nytt fönster för en RADIUS-klient](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Välj **OK**och välj sedan **Nästa**.

7. I fönstret **Konfigurera autentiseringsmetoder** godkänner du standard valet (**Microsoft Encrypted Authentication version 2 [MS-CHAPv2])** eller väljer ett annat alternativ och väljer **Nästa**.

    > [!NOTE]
    > Om du konfigurerar EAP (Extensible Authentication Protocol) måste du använda antingen Microsoft Challenge-Handshake Authentication Protocol (CHAPv2) eller PEAP (Protected Extensible Authentication Protocol). Ingen annan EAP stöds.

8. I fönstret **Ange användar grupper** väljer du **Lägg till**och väljer sedan en lämplig grupp. Lämna alternativet tomt om det inte finns någon grupp för att bevilja åtkomst till alla användare.

    ![Fönstret Ange användar grupper för att tillåta eller neka åtkomst](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Välj **Nästa**.

10. I fönstret **Ange IP-filter** väljer du **Nästa**.

11. I fönstret **Ange krypterings inställningar** godkänner du standardinställningarna och väljer sedan **Nästa**.

    ![Fönstret Ange krypterings inställningar](./media/howto-mfa-nps-extension-vpn/image8.png)

12. I fönstret **Ange ett sfärnamn** lämnar du sfär namnet tomt, accepterar standardinställningen och väljer sedan **Nästa**.

    ![Fönstret Ange ett sfärnamn](./media/howto-mfa-nps-extension-vpn/image9.png)

13. I fönstret **Slutför nya fjärr anslutningar eller virtuella privata nätverk och RADIUS-klienter** väljer du **Slutför**.

    ![Slutfört konfigurations fönster](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verifiera RADIUS-konfigurationen

I det här avsnittet beskrivs konfigurationen som du skapade med hjälp av guiden.

1. På nätverks princip servern, i NPS-konsolen (lokal), expanderar du **RADIUS-klienter**och väljer sedan **RADIUS-klienter**.

2. I informations fönstret högerklickar du på RADIUS-klienten som du skapade och väljer sedan **Egenskaper**. Egenskaperna för RADIUS-klienten (VPN-servern) bör vara som de som visas här:

    ![Verifiera VPN-egenskaperna och konfigurationen](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Välj **Avbryt**.

4. På nätverks princip servern, i NPS-konsolen (lokal), expanderar du **principer**och väljer sedan **principer för anslutningsbegäran**. Principen för VPN-anslutningar visas på det sätt som visas i följande bild:

    ![Princip för anslutningsbegäran som visar princip för VPN-anslutning](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Under **principer**väljer du **nätverks principer**. Du bör se en anslutning för virtuella privata nätverk (VPN) som liknar principen som visas i följande bild:

    ![Nätverks principer som visar princip för anslutningar för virtuella privata nätverk](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurera VPN-servern så att den använder RADIUS-autentisering

I det här avsnittet konfigurerar du VPN-servern för att använda RADIUS-autentisering. Anvisningarna förutsätter att du har en fungerande konfiguration av en VPN-server men inte har konfigurerat den för att använda RADIUS-autentisering. När du har konfigurerat VPN-servern bekräftar du att konfigurationen fungerar som förväntat.

> [!NOTE]
> Om du redan har en fungerande VPN-serverkonfiguration som använder RADIUS-autentisering kan du hoppa över det här avsnittet.
>

### <a name="configure-authentication-provider"></a>Konfigurera autentiseringsprovider

1. Öppna Serverhanteraren på VPN-servern.

2. I Serverhanteraren väljer du **verktyg**och sedan **Routning och fjärråtkomst**.

3. I fönstret **Routning och fjärråtkomst** högerklickar du på ** \<server name> (lokalt)** och väljer sedan **Egenskaper**.

4. I fönstret ** \<server name> egenskaper (lokal)** väljer du fliken **säkerhet** .

5. På fliken **säkerhet** under **autentiseringsprovider**väljer du **RADIUS-autentisering**och väljer sedan **Konfigurera**.

    ![Konfigurera Provider för RADIUS-autentisering](./media/howto-mfa-nps-extension-vpn/image15.png)

6. I fönstret **RADIUS-autentisering** väljer du **Lägg till**.

7. Gör följande i fönstret **Lägg till RADIUS-server** :

    a. I rutan **Server namn** anger du namnet eller IP-adressen för RADIUS-servern som du konfigurerade i föregående avsnitt.

    b. För den **delade hemligheten**väljer du **ändra**och anger sedan det delade hemliga lösen ord som du skapade och registrerade tidigare.

    c. I rutan **tids gräns (sekunder)** anger du ett värde på **30**.  
    Timeout-värdet är nödvändigt för att tillåta tillräckligt med tid för att slutföra den andra autentiserings faktorn. Vissa VPN eller regioner kräver timeout-inställningar som är längre än 30 sekunder för att förhindra att användare tar emot flera telefonsamtal. Om användarna upplever det här problemet ökar du timeout-värdet **(sekunder)** i steg om 30 sekunder tills problemet inte återkommer.

    ![Fönstret Lägg till RADIUS-server konfigurera timeout](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Välj **OK**.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutning

I det här avsnittet bekräftar du att VPN-klienten autentiseras och auktoriseras av RADIUS-servern när du försöker ansluta till den virtuella VPN-porten. Anvisningarna förutsätter att du använder Windows 10 som en VPN-klient.

> [!NOTE]
> Om du redan har konfigurerat en VPN-klient för att ansluta till VPN-servern och har sparat inställningarna kan du hoppa över de steg som är relaterade till att konfigurera och spara ett VPN-anslutningsobjektet.
>

1. På din VPN-klientdator väljer du knappen **Start** och väljer sedan knappen **Inställningar** .

2. I fönstret **Windows-inställningar** väljer du **nätverks & Internet**.

3. Välj **VPN**.

4. Välj **Lägg till en VPN-anslutning**.

5. I rutan **VPN-Provider** i rutan **Lägg till en VPN-anslutning** väljer du **Windows (inbyggt)**, fyller i återstående fält efter behov och väljer sedan **Spara**.

    ![Fönstret "Lägg till en VPN-anslutning"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Gå till **kontroll panelen**och välj nätverks- **och delnings Center**.

7. Välj **ändra inställningar för nätverkskort**.

    ![Nätverks-och delnings Center – ändra inställningar för nätverkskort](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Högerklicka på VPN-nätverksanslutningen och välj sedan **Egenskaper**.

9. I fönstret VPN-egenskaper väljer du fliken **säkerhet** .

10. På fliken **säkerhet** kontrollerar du att endast **Microsoft CHAP version 2 (MS-CHAP v2)** är markerat och väljer sedan **OK**.

    ![Alternativet Tillåt följande protokoll](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Högerklicka på VPN-anslutningen och välj sedan **Anslut**.

12. I fönstret **Inställningar** väljer du **Anslut**.  
    En lyckad anslutning visas i säkerhets loggen på RADIUS-servern, som händelse-ID 6272, som du ser här:

    ![Händelse Fönstret Egenskaper visar en lyckad anslutning](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Felsöka RADIUS

Anta att VPN-konfigurationen fungerade innan du konfigurerade VPN-servern för att använda en centraliserad RADIUS-server för autentisering och auktorisering. Om konfigurationen fungerade är det troligt att problemet orsakas av en felaktig konfiguration av RADIUS-servern eller att ett ogiltigt användar namn eller lösen ord används. Om du till exempel använder det alternativa UPN-suffixet i användar namnet kan inloggnings försöket Miss lyckas. Använd samma konto namn för bästa möjliga resultat.

För att felsöka problemen är det en bra plats att börja med att granska säkerhets händelse loggarna på RADIUS-servern. Om du vill spara tid genom att söka efter händelser kan du använda den rollbaserade nätverks principen och åtkomst servern anpassad vy i Loggboken, som visas här. "Händelse-ID 6273" indikerar händelser där NPS nekade åtkomst till en användare.

![Loggboken som visar NPAS-händelser](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication

Om du behöver hjälp med att konfigurera användare för Multi-Factor Authentication se artiklarna [Planera en molnbaserad Azure-Multi-Factor Authentication distribution](howto-mfa-getstarted.md#create-conditional-access-policy) och [Konfigurera mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller instruktioner för hur du konfigurerar VPN att använda MFA för klientautentisering med VPN-servern.

> [!NOTE]
> Register nyckeln REQUIRE_USER_MATCH är Skift läges känslig. Alla värden måste anges i versal format.
>

När du har installerat och konfigurerat NPS-tillägget krävs all RADIUS-baserad klientautentisering som bearbetas av den här servern för att använda MFA. Om alla VPN-användare inte har registrerats i Azure Multi-Factor Authentication kan du göra något av följande:

* Konfigurera en annan RADIUS-server för att autentisera användare som inte är konfigurerade att använda MFA.

* Skapa en register post som gör det möjligt för användaren att ange en andra autentiseringsmetod om de registreras i Azure Multi-Factor Authentication.

Skapa ett nytt sträng värde med namnet _REQUIRE_USER_MATCH i HKLM\SOFTWARE\Microsoft\AzureMfa_och ange värdet *True* eller *false*.

![Inställningen Kräv användar matchning](./media/howto-mfa-nps-extension-vpn/image34.png)

Om värdet är inställt på *Sant* eller tomt, omfattas alla autentiseringsbegäranden till en MFA-utmaning. Om värdet är *false*utfärdas MFA-utmaningarna endast till användare som är registrerade i Azure Multi-Factor Authentication. Använd inställningen *falskt* endast vid testning eller i produktions miljöer under en onboarding-period.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Hämta Azure Active Directory klient-ID

Som en del av konfigurationen av NPS-tillägget måste du ange administratörsautentiseringsuppgifter och ID för din Azure AD-klient. Utför följande steg för att hämta klient-ID: t:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure-klienten.
1. I Azure Portal-menyn väljer du **Azure Active Directory**eller söker efter och väljer **Azure Active Directory** från vilken sida som helst.
1. På sidan **Översikt** visas *klient informationen* . Bredvid *klient-ID: t*väljer du **kopierings** ikonen, som du ser i följande exempel skärm bild:

   ![Hämtar klient-ID: t från Azure Portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

NPS-tillägget måste vara installerat på en server som har rollen nätverks policy och åtkomst tjänster installerad och som fungerar som RADIUS-server i din design. Installera *inte* NPS-tillägget på VPN-servern.

1. Ladda ned NPS-tillägget från [Microsoft Download Center](https://aka.ms/npsmfa).

2. Kopiera installations programmets körbara fil (*NpsExtnForAzureMfaInstaller.exe*) till NPS-servern.

3. På NPS-servern dubbelklickar du på **NpsExtnForAzureMfaInstaller.exe** och väljer **Kör**om du uppmanas att göra det.

4. I fönstret **NPS-tillägg för Azure MFA-installationsprogrammet** granskar du licens villkoren för program vara, markerar kryss rutan **Jag godkänner licens villkoren och** väljer sedan **Installera**.

    ![Fönstret "NPS-tillägg för Azure MFA-installation"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. I fönstret **NPS-tillägg för Azure MFA-installationen** väljer du **Stäng**.  

    ![Bekräftelse fönstret "installationen lyckades"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Konfigurera certifikat för användning av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenterna innehåller ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat.
* Associerar certifikatets offentliga nyckel till tjänstens huvud namn i Azure AD.
* Lagrar certifikatet i den lokala datorns Arkiv.
* Ger nätverks användaren åtkomst till certifikatets privata nyckel.
* Startar om NPS-tjänsten.

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för ditt certifikat med tjänstens huvud namn i Azure AD och så vidare.

Om du vill använda skriptet anger du fil namns tillägget med Azure Active Directory administratörs behörighet och Azure Active Directory klient-ID som du kopierade tidigare. Kontot måste finnas i samma Azure AD-klient som du vill aktivera tillägget för. Kör skriptet på varje NPS-server där du installerar NPS-tillägget.

1. Kör Windows PowerShell som administratör.

2. I PowerShell-kommandotolken skriver du **CD "C:\Program Files\Microsoft\AzureMfa\Config"** och väljer sedan Retur.

3. I nästa kommando tolk skriver du **.\AzureMfaNpsExtnConfigSetup.ps1**och väljer sedan Retur. Skriptet kontrollerar om Azure AD PowerShell-modulen är installerad. Om den inte är installerad, installerar skriptet modulen åt dig.

    ![Köra konfigurations skriptet för AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    Om du får ett säkerhets fel på grund av TLS aktiverar du TLS 1,2 med hjälp av `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` kommandot från PowerShell-prompten.
    
    När skriptet har verifierat installationen av PowerShell-modulen visas Azure Active Directory PowerShell-modulens inloggnings fönster.

4. Ange dina autentiseringsuppgifter och lösen ord för Azure AD-administratören och välj sedan **Logga**in.

    ![Autentisera till Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. I kommando tolken klistrar du in klient-ID: t som du kopierade tidigare och väljer sedan Retur.

    ![Mata in det Azure AD-klient-ID som kopierats före](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skriptet skapar ett självsignerat certifikat och utför andra konfigurations ändringar. Utdata ser ut som i följande bild:

    ![PowerShell-fönster som visar självsignerade certifikat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Starta om servern.

### <a name="verify-the-configuration"></a>Verifiera konfigurationen

För att verifiera konfigurationen måste du upprätta en ny VPN-anslutning till VPN-servern. När du har angett dina autentiseringsuppgifter för primär autentisering, väntar VPN-anslutningen på att den sekundära autentiseringen ska lyckas innan anslutningen upprättas, som du ser nedan.

![Windows inställningar VPN-fönstret](./media/howto-mfa-nps-extension-vpn/image42.png)

Om du har autentiserat dig med den sekundära verifierings metoden som du tidigare har konfigurerat i Azure MFA är du ansluten till resursen. Men om den sekundära autentiseringen Miss lyckas nekas du åtkomst till resursen.

I följande exempel tillhandahåller Microsoft Authenticator-appen på en Windows Phone den sekundära autentiseringen:

![Exempel på MFA-prompt på Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

När du har autentiserat med hjälp av den sekundära metoden beviljas du åtkomst till den virtuella porten på VPN-servern. Eftersom du behövde använda en sekundär autentiseringsmetod med hjälp av en mobilapp på en betrodd enhet är inloggnings processen säkrare än om den bara använde en kombination av användar namn och lösen ord.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Visa Loggboken loggar för lyckade inloggnings händelser

Om du vill visa lyckade inloggnings händelser i Windows-Loggboken loggar frågar du i Windows säkerhets logg på NPS-servern genom att ange följande PowerShell-kommando:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![PowerShell-säkerhet Loggboken](./media/howto-mfa-nps-extension-vpn/image44.png)

Du kan också Visa säkerhets loggen eller den anpassade vyn nätverks policy och åtkomst tjänster, som du ser här:

![Exempel på nätverks princip Server logg](./media/howto-mfa-nps-extension-vpn/image45.png)

På den server där du installerade NPS-tillägget för Azure Multi-Factor Authentication kan du hitta Loggboken program loggar som är specifika för tillägget på program- *och tjänst Logs\Microsoft\AzureMfa*.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Exempel fönstret Loggboken AuthZ-loggar](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Felsökningsguide

Om konfigurationen inte fungerar som förväntat startar du fel sökningen genom att verifiera att användaren är konfigurerad att använda MFA. Be användaren att ansluta till [Azure Portal](https://portal.azure.com). Om användaren uppmanas att använda den sekundära autentiseringen och kan autentiseras kan du eliminera en felaktig konfiguration av MFA som ett problem.

Om MFA arbetar för användaren granskar du relevanta Loggboken loggar. Loggarna omfattar säkerhets händelsen, gatewayen och Azure Multi-Factor Authentication loggar som beskrivs i föregående avsnitt.

Ett exempel på en säkerhets logg som visar en misslyckad inloggnings händelse (händelse-ID 6273) visas här:

![Säkerhets loggen visar en misslyckad inloggnings händelse](./media/howto-mfa-nps-extension-vpn/image47.png)

En relaterad händelse från Azure Multi-Factor Authentication loggen visas här:

![Azure Multi-Factor Authentication-loggar](./media/howto-mfa-nps-extension-vpn/image48.png)

Information om hur du utför avancerad fel sökning finns i loggfilerna för NPS-databasfilen där NPS-tjänsten är installerad. Loggfilerna skapas i mappen _%systemroot%\System32\Logs_ som kommaavgränsade textfiler. En beskrivning av loggfilerna finns i [tolka loggfiler för NPS-databasnamn](https://technet.microsoft.com/library/cc771748.aspx).

Posterna i dessa loggfiler är svåra att tolka om du inte exporterar dem till ett kalkyl blad eller en databas. Du kan hitta många Internet Authentication Service (IAS)-verktyg online som hjälper dig att tolka loggfilerna. Utdata från ett sådant nedladdnings Bart [shareware-program](https://www.deepsoftware.com/iasviewer) visas här:

![Exempel på shareware-app IAS-parser](./media/howto-mfa-nps-extension-vpn/image49.png)

Om du vill göra ytterligare fel sökning kan du använda en protokoll analys som Wireshark eller [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Följande bild från wireshark visar RADIUS-meddelanden mellan VPN-servern och NPS.

![Microsoft Message Analyzer visar filtrerad trafik](./media/howto-mfa-nps-extension-vpn/image50.png)

Mer information finns i [integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Nästa steg

[Skaffa Azure-Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
