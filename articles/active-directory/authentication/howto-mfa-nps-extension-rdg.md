---
title: Integrera RDG med Azure AD MFA NPS-tillägget – Azure Active Directory
description: Integrera din infrastruktur för fjärrskrivbordsgateway med Azure AD MFA med hjälp av servertillägget nätverksprincip för Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f50792ec45570f7e90893a97150ea26b63ebf9c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829844"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera din infrastruktur för fjärrskrivbordsgateway med hjälp av NPS-tillägget (Network Policy Server) och Azure AD

Den här artikeln innehåller information om hur du integrerar din infrastruktur för fjärrskrivbordsgateway med Azure AD Multi-Factor Authentication (MFA) med hjälp av NPS-tillägget (Network Policy Server) för Microsoft Azure.

Med NPS-tillägget (Network Policy Server) för Azure kan kunder skydda Remote Authentication Dial-In User Service-klientautentisering (RADIUS) med hjälp av Azures [molnbaserade multifaktorautentisering (MFA).](./concept-mfa-howitworks.md) Den här lösningen tillhandahåller tvåstegsverifiering för att lägga till ett andra säkerhetslager för användarloggar och transaktioner.

Den här artikeln innehåller stegvisa instruktioner för att integrera NPS-infrastrukturen med Azure AD MFA med nps-tillägget för Azure. Detta möjliggör säker verifiering för användare som försöker logga in på en fjärrskrivbordsgateway.

> [!NOTE]
> Den här artikeln bör inte användas med MFA Server-distributioner och bör endast användas med Azure AD MFA-distributioner (molnbaserade).

Nätverksprincip- och åtkomsttjänster (NPS) ger organisationer möjlighet att göra följande:

* Definiera centrala platser för hantering och kontroll av nätverksbegäranden genom att ange vem som kan ansluta, vilka tider på dagen anslutningar tillåts, varaktigheten för anslutningar och den säkerhetsnivå som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje VPN- eller fjärrskrivbordsgatewayserver kan dessa principer anges en gång på en central plats. RADIUS-protokollet tillhandahåller centraliserad autentisering, auktorisering och redovisning (AAA).
* Upprätta och tillämpa Network Access Protection (NAP) för klienthälsa som avgör om enheter beviljas obegränsad eller begränsad åtkomst till nätverksresurser.
* Ge ett sätt att framtvinga autentisering och auktorisering för åtkomst till 802.1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.

Vanligtvis använder organisationer NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN-principer. Men många organisationer använder också NPS för att förenkla och centralisera hanteringen av auktoriseringsprinciper för fjärrskrivbordsanslutning (RD CAPs).

Organisationer kan också integrera NPS med Azure AD MFA för att förbättra säkerheten och ge en hög efterlevnadsnivå. På så sätt säkerställer du att användarna etablerar tvåstegsverifiering för att logga in på Fjärrskrivbordsgateway. För att användare ska beviljas åtkomst måste de ange sin kombination av användarnamn/lösenord tillsammans med information som användaren har i sin kontroll. Den här informationen måste vara betrodd och inte lätt att duplicera, till exempel ett mobiltelefonnummer, ett fasta nummer, ett program på en mobil enhet och så vidare. RDG stöder för närvarande telefonsamtal och push-meddelanden från Microsoft Authenticator-appmetoder för 2FA. Mer information om autentiseringsmetoder som stöds finns i avsnittet [Bestämma vilka autentiseringsmetoder som användarna kan använda.](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)

Innan NPS-tillägget för Azure blev tillgängligt var kunder som ville implementera tvåstegsverifiering för integrerade NPS- och Azure AD MFA-miljöer tvungna att konfigurera och underhålla en separat MFA-server i den lokala miljön enligt dokumenten i [Fjärrskrivbordsgateway](howto-mfaserver-nps-rdg.md)och Azure Multi-Factor Authentication-server med RADIUS.

Tillgängligheten för NPS-tillägget för Azure ger nu organisationer möjlighet att distribuera antingen en lokal baserad MFA-lösning eller en molnbaserad MFA-lösning för att skydda RADIUS-klientautentisering.

## <a name="authentication-flow"></a>Autentiseringsflöde

För att användare ska beviljas åtkomst till nätverksresurser via en fjärrskrivbordsgateway måste de uppfylla villkoren som anges i en auktoriseringsprincip för fjärrskrivbordsanslutning (RD CAP) och en auktoriseringsprincip för fjärrskrivbordsresurser (RD AUKTORISERING). Fjärrskrivbords-CAP:er anger vem som har behörighet att ansluta till fjärrskrivbordsgatewayer. RD RAPs anger nätverksresurser, till exempel fjärrskrivbord eller fjärrappar, som användaren tillåts att ansluta till via fjärrskrivbordsgatewayen.

En fjärrskrivbordsgateway kan konfigureras för att använda ett centralt principarkiv för FJÄRRSKRIVBORDS-CAP:er. RD RAP:er kan inte använda en central princip eftersom de bearbetas på fjärrskrivbordsgatewayen. Ett exempel på en fjärrskrivbordsgateway som har konfigurerats för att använda ett centralt principarkiv för fjärrskrivbords-CAP:er är en RADIUS-klient till en annan NPS-server som fungerar som central principlagring.

När NPS-tillägget för Azure är integrerat med NPS och Fjärrskrivbordsgateway ser autentiseringsflödet ut så här:

1. Servern för fjärrskrivbordsgateway tar emot en autentiseringsbegäran från en fjärrskrivbordsanvändare om att ansluta till en resurs, till exempel en fjärrskrivbordssession. Som EN RADIUS-klient konverterar Servern för fjärrskrivbordsgateway begäran till ett RADIUS Access-Request-meddelande och skickar meddelandet till RADIUS-servern (NPS) där NPS-tillägget är installerat.
1. Kombinationen av användarnamn och lösenord verifieras i Active Directory och användaren autentiseras.
1. Om alla villkor som anges i NPS-anslutningsbegäran och nätverksprinciperna är uppfyllda (till exempel begränsningar för tid på dagen eller gruppmedlemskap), utlöser NPS-tillägget en begäran om sekundär autentisering med Azure AD MFA.
1. Azure AD MFA kommunicerar med Azure AD, hämtar användarens information och utför den sekundära autentiseringen med hjälp av metoder som stöds.
1. När MFA-utmaningen har lyckats kommunicerar Azure AD MFA resultatet med NPS-tillägget.
1. NPS-servern, där tillägget är installerat, skickar ett RADIUS-Access-Accept för principen för fjärrskrivbords-CAP till servern för fjärrskrivbordsgatewayen.
1. Användaren beviljas åtkomst till den begärda nätverksresursen via fjärrskrivbordsgatewayen.

## <a name="prerequisites"></a>Förutsättningar

Det här avsnittet beskriver de nödvändiga förutsättningarna innan du integrerar Azure AD MFA med Fjärrskrivbordsgateway. Innan du börjar måste du ha följande förutsättningar på plats.  

* Fjärrskrivbordstjänster (RDS)-infrastruktur
* Azure AD MFA-licens
* Windows Server-programvara
* Rollen Nätverksprincip och åtkomsttjänster (NPS)
* Azure Active Directory synkroniseras med lokal Active Directory
* AZURE ACTIVE DIRECTORY GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Fjärrskrivbordstjänster (RDS)-infrastruktur

Du måste ha en fungerande Fjärrskrivbordstjänster (RDS)-infrastruktur på plats. Om du inte gör det kan du snabbt skapa den här infrastrukturen i Azure med hjälp av följande snabbstartsmall: Skapa [distribution av Sessionssamling för fjärrskrivbord.](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)

Om du vill skapa en lokal RDS-infrastruktur manuellt snabbt i testsyfte följer du stegen för att distribuera en.
**Läs mer:** [Distribuera fjärrskrivbordstjänster med Azure-snabbstart och](/windows-server/remote/remote-desktop-services/rds-in-azure) [distribution av grundläggande RDS-infrastruktur.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licens

Krävs är en licens för Azure AD MFA, som är tillgänglig via Azure AD Premium eller andra paket som innehåller den. Förbrukningsbaserade licenser för Azure AD MFA, till exempel per användare eller per autentiseringslicenser, är inte kompatibla med NPS-tillägget. Mer information finns i Så [här hämtar du Azure AD Multi-Factor Authentication.](concept-mfa-licensing.md) I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i det här avsnittet utfördes med Hjälp av Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rollen Nätverksprincip och åtkomsttjänster (NPS)

NPS-rolltjänsten tillhandahåller RADIUS-server- och klientfunktioner samt hälsotjänst för nätverksåtkomstprincip. Den här rollen måste installeras på minst två datorer i infrastrukturen: Fjärrskrivbordsgateway och en annan medlemsserver eller domänkontrollant. Som standard finns rollen redan på den dator som konfigurerats som fjärrskrivbordsgateway.  Du måste också installera NPS-rollen på minst en annan dator, till exempel en domänkontrollant eller medlemsserver.

Information om hur du installerar NPS-rolltjänsten Windows Server 2012 eller äldre finns i [Installera en NAP-hälsoprincipserver.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)) En beskrivning av metodtips för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant, finns i [Metodtips för NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synkroniserat med lokal Active Directory

Om du vill använda NPS-tillägget måste lokala användare synkroniseras med Azure AD och aktiveras för MFA. Det här avsnittet förutsätter att lokala användare synkroniseras med Azure AD med hjälp av AD Connect. Information om Azure AD Connect finns [i Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>AZURE ACTIVE DIRECTORY GUID-ID

Om du vill installera NPS-tillägget måste du känna till GUID för Azure AD. Anvisningar för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication

Det här avsnittet innehåller anvisningar för att integrera Azure AD MFA med fjärrskrivbordsgatewayen. Som administratör måste du konfigurera Azure AD MFA-tjänsten innan användarna kan registrera sina multifaktorenheter eller program själv.

Följ stegen i Komma [igång med Azure AD Multi-Factor Authentication i molnet för](howto-mfa-getstarted.md) att aktivera MFA för dina Azure AD-användare.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering

När ett konto har aktiverats för MFA kan du inte logga in på resurser som styrs av MFA-principen förrän du har konfigurerat en betrodd enhet att använda för den andra autentiseringsfaktorn och har autentiserat med tvåstegsverifiering.

Följ stegen i [Vad innebär Azure AD Multi-Factor Authentication för mig?](../user-help/multi-factor-authentication-end-user-first-time.md) för att förstå och konfigurera dina enheter korrekt för MFA med ditt användarkonto.

> [!IMPORTANT]
> Inloggningsbeteendet för Fjärrskrivbordsgateway ger inte möjlighet att ange en verifieringskod med Azure AD Multi-Factor Authentication. Ett användarkonto måste konfigureras för telefonverifiering eller Microsoft Authenticator med push-meddelanden.
>
> Om varken telefonverifiering eller Microsoft Authenticator-appen med push-meddelanden har konfigurerats för en användare kan användaren inte slutföra Azure AD Multi-Factor Authentication-utmaningen och logga in på Fjärrskrivbordsgateway.
>
> SMS-textmetoden fungerar inte med Fjärrskrivbordsgateway eftersom den inte ger möjlighet att ange en verifieringskod.

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägg

Det här avsnittet innehåller anvisningar för hur du konfigurerar RDS-infrastrukturen för användning av Azure AD MFA för klientautentisering med fjärrskrivbordsgatewayen.

### <a name="acquire-azure-active-directory-tenant-id"></a>Hämta Azure Active Directory klientorganisations-ID

Som en del av konfigurationen av NPS-tillägget måste du ange administratörsautentiseringsuppgifter och Azure AD-ID för din Azure AD-klientorganisation. Hämta klientorganisations-ID:t genom att utföra följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure-klientorganisationen.
1. I menyn Azure Portal väljer du **Azure Active Directory**, eller söker efter och väljer **Azure Active Directory** från valfri sida.
1. **Klientorganisationsinformationen** visas på *sidan* Översikt. Bredvid *Klientorganisations-ID* väljer du **ikonen Kopiera,** som du ser i följande exempelskärmbild:

   ![Hämta klientorganisations-ID:t från Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

Installera NPS-tillägget på en server som har rollen Nätverksprincip och åtkomsttjänster (NPS) installerad. Detta fungerar som RADIUS-servern för din design.

> [!IMPORTANT]
> Installera inte NPS-tillägget på RDG-servern (Fjärrskrivbordsgateway). RDG-servern använder inte RADIUS-protokollet med sin klient, så tillägget kan inte tolka och utföra MFA.
>
> När RDG-servern och NPS-servern med NPS-tillägget är olika servrar, använder RDG NPS internt för att kommunicera med andra NPS-servrar och använder RADIUS som protokoll för att kommunicera korrekt.

1. Ladda ned [NPS-tillägget](https://aka.ms/npsmfa).
1. Kopiera den körbara installationsfilen (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
1. På NPS-servern dubbelklickar du **påNpsExtnForAzureMfaInstaller.exe**. Klicka på Kör om du uppmanas **att göra det.**
1. I dialogrutan NPS-tillägg för Installation av Azure AD MFA granskar du licensvillkoren för programvara, markerar **Jag** godkänner licensvillkoren och klickar på **Installera.**
1. I dialogrutan NPS-tillägg för Azure AD MFA-installation klickar du på **Stäng**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Därefter måste du konfigurera certifikat för användning av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenterna innehåller ett Windows PowerShell skript som konfigurerar ett själv signerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett själv signerat certifikat
* Associerar den offentliga nyckeln för certifikatet med tjänstens huvudnamn i Azure AD
* Lagrar certifikatet i det lokala datorarkivet
* Ger nätverksanvändaren åtkomst till certifikatets privata nyckel
* Startar om tjänsten Nätverksprincipserver

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för certifikatet med tjänstens huvudnamn i Azure AD och så vidare.

Om du vill använda skriptet anger du tillägget med dina autentiseringsuppgifter för Azure AD-administratör och det Azure AD-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerade NPS-tillägget. Gör något av följande:

1. Öppna en administrativ Windows PowerShell fråga.
1. Skriv i PowerShell-prompten `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` och tryck på **RETUR.**
1. Skriv `.\AzureMfaNpsExtnConfigSetup.ps1` och tryck på **RETUR.** Skriptet kontrollerar om powershell Azure Active Directory modulen har installerats. Om det inte är installerat installerar skriptet modulen åt dig.

   ![Köra AzureMfaNpsExtnConfigSetup.ps1 i Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. När skriptet har verifierat installationen av PowerShell-modulen visas dialogrutan Azure Active Directory PowerShell-modulen. I dialogrutan anger du dina autentiseringsuppgifter och lösenord för Azure AD-administratören och klickar på **Logga in.**

   ![Autentisera till Azure AD i PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. När du uppmanas till det klistrar du in *det* klientorganisations-ID som du kopierade till Urklipp tidigare och trycker på **RETUR.**

   ![Ange klientorganisations-ID i PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skriptet skapar ett själv signerat certifikat och utför andra konfigurationsändringar. Utdata bör se ut som på bilden nedan.

   ![Utdata från PowerShell som visar själv signerat certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på Fjärrskrivbordsgateway

I det här avsnittet konfigurerar du anslutningsauktoriseringsprinciper för fjärrskrivbordsgateway och andra RADIUS-inställningar.

Autentiseringsflödet kräver att RADIUS-meddelanden utbyts mellan fjärrskrivbordsgatewayen och NPS-servern där NPS-tillägget är installerat. Det innebär att du måste konfigurera RADIUS-klientinställningar på både fjärrskrivbordsgatewayen och NPS-servern där NPS-tillägget är installerat.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera auktoriseringsprinciper för fjärrskrivbordsgateway för att använda central lagring

Principer för auktorisering av fjärrskrivbordsanslutning anger kraven för att ansluta till en fjärrskrivbordsgatewayserver. RD CAP:er kan lagras lokalt (standard) eller lagras i ett centralt ARKIV för fjärrskrivbords-CAP som kör NPS. För att konfigurera integrering av Azure AD MFA med RDS måste du ange användningen av ett centralt arkiv.

1. Öppna på servern för fjärrskrivbordsgatewayen **Serverhanteraren**.
1. Klicka på Verktyg på **menyn,** peka på **Fjärrskrivbordstjänster** och klicka sedan på **Hanteraren för fjärrskrivbordsgateway.**
1. I hanteraren för fjärrskrivbordsgateway högerklickar du **\[ på Servernamn \] (lokal)** och klickar på **Egenskaper.**
1. I dialogrutan Egenskaper väljer du fliken **Arkiv för fjärrskrivbords-CAP.**
1. På fliken Arkiv för fjärrskrivbords-CAP väljer du **Central server som kör NPS**. 
1. I fältet Ange ett namn eller en IP-adress för servern som kör **NPS** anger du IP-adressen eller servernamnet för den server där du installerade NPS-tillägget.

   ![Ange nps-serverns namn eller IP-adress](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicka på **Lägg till**.
1. I dialogrutan **Delad hemlighet** anger du en delad hemlighet och klickar sedan på **OK.** Se till att du registrerar den här delade hemligheten och lagrar posten på ett säkert sätt.

   >[!NOTE]
   >Delad hemlighet används för att upprätta förtroende mellan RADIUS-servrar och -klienter. Skapa en lång och komplex hemlighet.
   >

   ![Skapa en delad hemlighet för att upprätta förtroende](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS-timeoutvärde på NPS för fjärrskrivbordsgateway

För att säkerställa att det finns tid att verifiera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, ta emot svar och svara på RADIUS-meddelanden är det nödvändigt att justera RADIUS-timeoutvärdet.

1. Öppna servern för fjärrskrivbordsgatewayen Serverhanteraren. På menyn klickar du på **Verktyg** och sedan på **Nätverksprincipserver.**
1. I **NPS-konsolen (lokal)** expanderar **du RADIUS-klienter och**-servrar och väljer **Fjärr-RADIUS-server.**

   ![Hanteringskonsolen för nätverksprincipservern som visar radius-fjärrservern](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbelklicka på TS GATEWAY SERVER **GROUP (TS GATEWAY-SERVERGRUPP) i informationsfönstret.**

   >[!NOTE]
   >Den här RADIUS-servergruppen skapades när du konfigurerade den centrala servern för NPS-principer. Fjärrskrivbordsgatewayen vidarebefordrar RADIUS-meddelanden till den här servern eller gruppen med servrar, om det finns fler än en i gruppen.
   >

1. I dialogrutan **Egenskaper för TS GATEWAY-SERVERGRUPP** väljer du IP-adressen eller namnet på den NPS-server som du har konfigurerat för att lagra fjärrskrivbords-CAPs och klickar sedan **på Redigera.**

   ![Välj IP-adressen eller namnet på nps-servern som konfigurerades tidigare](./media/howto-mfa-nps-extension-rdg/image13.png)

1. I dialogrutan **Redigera RADIUS-server** väljer du fliken **Belastningsutjämning.**
1. På fliken **Belastningsutjämning** går du till fältet Antal sekunder utan svar innan begäran anses vara utelämnad och ändrar standardvärdet från 3 till ett värde mellan 30 och 60 sekunder. 
1. I fältet **Antal sekunder mellan** begäranden när servern identifieras som otillgänglig ändrar du standardvärdet 30 sekunder till ett värde som är lika med eller större än det värde som du angav i föregående steg.

   ![Redigera radius-serverns tidsgränsinställningar på fliken för belastningsutjämning](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicka **på OK** två gånger för att stänga dialogrutorna.

### <a name="verify-connection-request-policies"></a>Verifiera principer för anslutningsbegäran

När du konfigurerar fjärrskrivbordsgatewayen att använda ett centralt principarkiv för principer för anslutningsauktorisering konfigureras fjärrskrivbordsgatewayen som standard för att vidarebefordra CAP-begäranden till NPS-servern. NPS-servern med Azure AD MFA-tillägget installerat bearbetar RADIUS-åtkomstbegäran. Följande steg visar hur du verifierar standardprincipen för anslutningsbegäran.  

1. På RD Gateway går du till NPS-konsolen (lokal) och **expanderar Principer** och väljer **Principer för anslutningsbegäran.**
1. Dubbelklicka på **TS GATEWAY AUTHORIZATION POLICY (TS GATEWAY-AUKTORISERINGSPRINCIP).**
1. I dialogrutan **Egenskaper för TS GATEWAY-AUKTORISERINGSPRINCIP** klickar du på **fliken** Inställningar.
1. På **fliken** Inställningar, under Vidarebefordra anslutningsbegäran, klickar du på **Autentisering.** RADIUS-klienten är konfigurerad för att vidarebefordra begäranden om autentisering.

   ![Konfigurera autentiseringsinställningar för att ange servergruppen](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicka **på Avbryt.**

>[!NOTE]
> Mer information om hur du skapar en princip för anslutningsbegäran finns i artikeln [Konfigurera principer för anslutningsbegäran](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) för samma sak. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på servern där NPS-tillägget är installerat

NPS-servern där NPS-tillägget är installerat måste kunna utbyta RADIUS-meddelanden med NPS-servern på fjärrskrivbordsgatewayen. Om du vill aktivera det här meddelandeutbytet måste du konfigurera NPS-komponenterna på den server där NPS-tilläggstjänsten är installerad.

### <a name="register-server-in-active-directory"></a>Registrera server i Active Directory

Nps-servern måste vara registrerad i Active Directory för att fungera korrekt i det här scenariot.

1. På NPS-servern öppnar du **Serverhanteraren**.
1. I Serverhanteraren klickar du **på Verktyg** och sedan på **Nätverksprincipserver.**
1. I konsolen Nätverksprincipserver högerklickar du på **NPS (lokal)** och klickar sedan på **Registrera server i Active Directory**.
1. Klicka **på OK** två gånger.

   ![Registrera NPS-servern i Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Låt konsolen vara öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klienten

Fjärrskrivbordsgatewayen måste konfigureras som en RADIUS-klient för NPS-servern.

1. Högerklicka på RADIUS-klienter i NPS-konsolen **(lokal)** på NPS-servern där NPS-tillägget är installerat och **klicka** på **Ny.**

   ![Skapa en ny RADIUS-klient i NPS-konsolen](./media/howto-mfa-nps-extension-rdg/image17.png)

1. I dialogrutan **Ny RADIUS-klient** anger du ett eget namn, till exempel _Gateway,_ och IP-adressen eller DNS-namnet för servern för fjärrskrivbordsgatewayen.
1. I fälten **Delad hemlighet** och Bekräfta **delad hemlighet** anger du samma hemlighet som du använde tidigare.

   ![Konfigurera ett eget namn och IP- eller DNS-adressen](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klicka **på OK** för att stänga dialogrutan Ny RADIUS-klient.

### <a name="configure-network-policy"></a>Konfigurera nätverksprincip

Kom ihåg att NPS-servern med Azure AD MFA-tillägget är det centrala principarkivet för anslutningsauktoriseringsprincipen (CAP). Därför måste du implementera en CAP på NPS-servern för att auktorisera giltiga anslutningsbegäranden.  

1. Öppna NPS-konsolen (lokal) på NPS-servern, expandera **Principer** och klicka på **Nätverksprinciper.**
1. Högerklicka på Anslutningar **till andra åtkomstservrar och** klicka på Duplicera **princip.**

   ![Duplicera anslutningen till andra åtkomstservrar princip](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Högerklicka på Kopiera **anslutningar till andra åtkomstservrar och** klicka på **Egenskaper.**
1. I dialogrutan **Kopiera anslutningar till andra åtkomstservrar** går du till **Principnamn** och anger ett lämpligt namn, till exempel _RDG_CAP_. Markera **Princip aktiverad** och välj Bevilja **åtkomst.** Du kan också **välja Fjärrskrivbordsgateway** i Typ av nätverksåtkomstserver, eller så kan du lämna det som **Ospecificerad**.

   ![Namnge principen, aktivera och bevilja åtkomst](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicka på **fliken Begränsningar** och markera Tillåt klienter att ansluta utan att förhandla om **en autentiseringsmetod**.

   ![Ändra autentiseringsmetoder så att klienter kan ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Du kan också klicka **på fliken** Villkor och lägga till villkor som måste uppfyllas för att anslutningen ska godkännas, till exempel medlemskap i en specifik Windows-grupp.

   ![Om du vill kan du ange anslutningsvillkor](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicka på **OK**. När du uppmanas att visa motsvarande hjälpavsnitt klickar du på **Nej.**
1. Kontrollera att den nya principen finns överst i listan, att principen är aktiverad och att den beviljar åtkomst.

   ![Flytta principen överst i listan](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfigurationen

För att verifiera konfigurationen måste du logga in på fjärrskrivbordsgatewayen med en lämplig RDP-klient. Se till att använda ett konto som tillåts av dina principer för anslutningsauktorisering och är aktiverat för Azure AD MFA.

Som du ser i bilden nedan kan du använda **sidan Fjärrskrivbordswebbåtkomst.**

![Testa i Webbåtkomst till fjärrskrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering visas statusen Initierar fjärranslutning i dialogrutan Anslut till fjärrskrivbord, enligt nedan. 

Om du autentiserar med den sekundära autentiseringsmetoden som du tidigare konfigurerade i Azure AD MFA är du ansluten till resursen. Men om den sekundära autentiseringen inte lyckas nekas du åtkomst till resursen. 

![Anslutning till fjärrskrivbord initiera en fjärranslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows Phone för att tillhandahålla den sekundära autentiseringen.

![Exempel Windows Phone Authenticator-app som visar verifiering](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserats med hjälp av den sekundära autentiseringsmetoden loggas du in på Fjärrskrivbordsgateway som vanligt. Men eftersom du måste använda en sekundär autentiseringsmetod med hjälp av en mobilapp på en betrodd enhet är inloggningsprocessen säkrare än annars.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa Loggboken loggar för lyckade inloggningshändelser

Om du vill visa lyckade inloggningshändelser i Windows Loggboken-loggarna kan du utfärda följande Windows PowerShell-kommando för att fråga Windows-terminal Services och Windows-säkerhet loggar.

Om du vill fråga efter lyckade inloggningshändelser i gatewayens driftloggar _(Loggboken\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ använder du följande PowerShell-kommandon:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Det här kommandot visar Windows-händelser som visar att användaren har uppfyllt principkraven för resursauktorisering (RD FJÄRRÅTKOMST) och beviljats åtkomst.

![Visa händelser med PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Det här kommandot visar de händelser som visar när användaren uppfyllde principkraven för anslutningsauktorisering.

![visa principen för anslutningsauktorisering med PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa den här loggen och filtrera på händelse-ID: 300 och 200. Om du vill fråga efter lyckade inloggningshändelser i loggarna för loggboken säkerhet använder du följande kommando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Det här kommandot kan köras antingen på den centrala NPS- eller RD Gateway-servern.

![Exempel på lyckade inloggningshändelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också visa säkerhetsloggen eller vyn Nätverksprincip och åtkomsttjänster enligt nedan:

![Nätverksprincip och åtkomsttjänster Loggboken](./media/howto-mfa-nps-extension-rdg/image31.png)

På servern där du installerade NPS-tillägget för Azure AD MFA hittar du Loggboken-programloggar som är specifika för tillägget i Program- och _tjänstloggar\Microsoft\AzureMfa._

![Loggboken AuthZ-programloggar](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Felsökningsguide

Om konfigurationen inte fungerar som förväntat är det första stället att börja felsöka att kontrollera att användaren är konfigurerad för att använda Azure AD MFA. Be användaren att ansluta till [Azure Portal](https://portal.azure.com). Om användarna tillfrågas om sekundär verifiering och kan autentiseras kan du eliminera en felaktig konfiguration av Azure AD MFA.

Om Azure AD MFA fungerar för användarna bör du granska relevanta händelseloggar. Dessa inkluderar loggarna Säkerhetshändelse, Gateway i drift och Azure AD MFA som beskrivs i föregående avsnitt.

Nedan visas ett exempel på utdata från säkerhetsloggen som visar en misslyckad inloggningshändelse (händelse-ID 6273).

![Exempel på en misslyckad inloggningshändelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterad händelse från AzureMFA-loggarna:

![Exempel på Azure AD MFA-inloggning Loggboken](./media/howto-mfa-nps-extension-rdg/image34.png)

Om du vill utföra avancerade felsökningsalternativ kan du läsa loggfilerna i NPS-databasformatet där NPS-tjänsten är installerad. Dessa loggfiler skapas i _mappen %SystemRoot%\System32\Logs_ som kommaavgränsade textfiler.

En beskrivning av dessa loggfiler finns i [Tolka loggfiler för NPS-databasformat.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)) Posterna i dessa loggfiler kan vara svåra att tolka utan att importera dem till ett kalkylblad eller en databas. Du hittar flera IAS-parser online som hjälper dig att tolka loggfilerna.

Bilden nedan visar utdata från ett sådant [nedladdningsbart shareware-program](https://www.deepsoftware.com/iasviewer).

![Exempel på IAS-parser för Shareware-app](./media/howto-mfa-nps-extension-rdg/image35.png)

För ytterligare felsökningsalternativ kan du använda ett protokollanalysverktyg, till exempel [Microsoft Message Analyzer.](/message-analyzer/microsoft-message-analyzer-operating-guide)

Bilden nedan från Microsoft Message Analyzer visar nätverkstrafik filtrerad på RADIUS-protokollet som innehåller användarnamnet **CONTOSO\AliceC**.

![Microsoft Message Analyzer som visar filtrerad trafik](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg

[Så här hämtar du Azure AD Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
