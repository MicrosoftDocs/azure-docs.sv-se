---
title: Integrera RDG med Azure AD MFA NPS-tillägg – Azure Active Directory
description: Integrera din infrastruktur för fjärrskrivbordsgateway med Azure AD MFA med nätverks princip Server tillägget för Microsoft Azure
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
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743385"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera din infrastruktur för fjärrskrivbordsgateway med nätverks princip Server (NPS)-tillägget och Azure AD

Den här artikeln innehåller information om hur du integrerar din infrastruktur för fjärrskrivbordsgateway med Azure AD Multi-Factor Authentication (MFA) med hjälp av nätverks princip Server (NPS)-tillägget för Microsoft Azure.

Nätverks princip Server (NPS)-tillägget för Azure gör det möjligt för kunder att skydda Remote Authentication Dial-In User Service (RADIUS) klientautentisering med hjälp av Azures molnbaserade [Multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md). Den här lösningen ger tvåstegsverifiering för att lägga till ett andra säkerhets lager till användar inloggningar och transaktioner.

Den här artikeln innehåller steg-för-steg-instruktioner för att integrera NPS-infrastrukturen med Azure AD MFA med NPS-tillägget för Azure. Detta möjliggör säker verifiering för användare som försöker logga in på en fjärr skrivbords-Gateway.

> [!NOTE]
> Den här artikeln bör inte användas med MFA Server-distributioner och bör endast användas med Azure AD MFA (molnbaserad) distributioner.

Nätverks princip-och nätverks åtkomst tjänsterna (NPS) ger organisationer möjlighet att göra följande:

* Definiera centrala platser för hantering och kontroll av nätverks begär Anden genom att ange vem som kan ansluta, vilka tider på dags anslutningar som tillåts, varaktigheten för anslutningar och vilken säkerhets nivå som klienterna måste använda för att ansluta, och så vidare. I stället för att ange dessa principer på varje VPN-eller fjärr skrivbords server (RD) Gateway-server kan dessa principer anges en gång på en central plats. RADIUS-protokollet tillhandahåller centraliserad autentisering, auktorisering och redovisning (AAA).
* Upprätta och framtvinga Network Access Protection (NAP) hälso principer för klient (NAP) som avgör om enheter beviljas obegränsad eller begränsad åtkomst till nätverks resurser.
* Ger ett sätt att genomdriva autentisering och auktorisering för åtkomst till 802.1 x-kompatibla trådlösa åtkomst punkter och Ethernet-växlar.

Organisationer använder vanligt vis NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN-principer. Många organisationer använder dock även NPS för att förenkla och centralisera hanteringen av Auktoriseringsprinciper för fjärr skrivbords anslutning (RD-principer).

Organisationer kan också integrera NPS med Azure AD MFA för att förbättra säkerheten och ge en hög nivå av efterlevnad. På så sätt ser du till att användarna etablerar tvåstegsverifiering för att logga in på Fjärrskrivbordsgateway. Användare som ska beviljas åtkomst måste ange sin kombination av användar namn/lösen ord tillsammans med information som användaren har i sin kontroll. Den här informationen måste vara betrodd och inte lätt att duplicera, till exempel ett mobiltelefon nummer, Landline nummer, program på en mobil enhet och så vidare. RDG stöder för närvarande telefonsamtal och push-meddelanden från Microsoft Authenticator app-metoder för 2FA. Mer information om autentiseringsmetoder som stöds finns i avsnittet [avgöra vilka autentiseringsmetoder som användarna kan använda](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Innan Azure-tillägget för Azure har Aktiver ATS måste kunder som vill implementera tvåstegsverifiering för integrerad NPS-och Azure AD MFA-miljö konfigurera och underhålla en separat MFA-server i den lokala miljön som dokumenteras i [Remote Desktop Gateway och azure Multi-Factor Authentication-Server med RADIUS](howto-mfaserver-nps-rdg.md).

Tillgängligheten för NPS-tillägget för Azure ger organisationer möjlighet att distribuera antingen en lokal baserad MFA-lösning eller en molnbaserad MFA-lösning för säker autentisering med RADIUS-klientautentisering.

## <a name="authentication-flow"></a>Autentiseringsflöde

För att användare ska beviljas åtkomst till nätverks resurser via en Fjärrskrivbordsgateway måste de uppfylla de villkor som anges i en auktoriseringsprincip för fjärr skrivbords anslutning och en auktoriseringsprincip för fjärr skrivbords resurser (RD RAP). Auktoriseringsprinciper för fjärr skrivbords anslutning anger vem som har behörighet att ansluta till RD-gatewayer. RD-Auktoriseringsprinciper anger nätverks resurserna, t. ex. fjärr skrivbord eller fjärrappar, som användaren får ansluta till via RD Gateway.

En RD Gateway kan konfigureras att använda en central princip lagring för Auktoriseringsprinciper för fjärr skrivbords anslutning. RD-auktoriseringsprinciper kan inte använda en central princip eftersom de bearbetas på RD Gateway. Ett exempel på en RD Gateway som kon figurer ATS för att använda en central princip lagring för Auktoriseringsprinciper för fjärr skrivbords anslutning är en RADIUS-klient till en annan NPS-server som fungerar som central princip arkiv.

När NPS-tillägget för Azure är integrerat med NPS-och fjärr skrivbords-gatewayen är det lyckade verifierings flödet följande:

1. Servern för fjärrskrivbordsgateway tar emot en autentiseringsbegäran från en fjärr skrivbords användare för att ansluta till en resurs, till exempel en fjärrskrivbordssession. Som agerar som en RADIUS-klient konverterar servern för fjärrskrivbordsgateway begäran till ett RADIUS-Access-Request meddelande och skickar meddelandet till RADIUS-servern (NPS) där NPS-tillägget är installerat.
1. Kombinationen av användar namn och lösen ord verifieras i Active Directory och användaren autentiseras.
1. Om alla villkor som anges i NPS-anslutningsbegäran och nätverks principerna är uppfyllda (till exempel tid på dag eller grupp medlemskaps begränsningar), utlöser NPS-tillägget en begäran om sekundär autentisering med Azure AD MFA.
1. Azure AD MFA kommunicerar med Azure AD, hämtar användarens information och utför den sekundära autentiseringen med metoder som stöds.
1. När MFA-utmaningen lyckas kommunicerar Azure AD MFA resultatet till NPS-tillägget.
1. NPS-servern, där tillägget är installerat, skickar ett RADIUS-Access-Accept meddelande för principen för fjärr skrivbords anslutning till servern för fjärrskrivbordsgateway.
1. Användaren beviljas åtkomst till den begärda nätverks resursen via RD Gateway.

## <a name="prerequisites"></a>Förutsättningar

I det här avsnittet beskrivs nödvändiga komponenter innan du integrerar Azure AD MFA med Fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats.  

* Infrastruktur för Fjärrskrivbordstjänster (RDS)
* Azure AD MFA-licens
* Windows Server-programvara
* Rollen nätverks princip-och nätverks åtkomst tjänster (NPS)
* Azure Active Directory synkroniseras med lokala Active Directory
* Azure Active Directory GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktur för Fjärrskrivbordstjänster (RDS)

Du måste ha en infrastruktur för arbets Fjärrskrivbordstjänster (RDS) på plats. Om du inte gör det kan du snabbt skapa den här infrastrukturen i Azure med hjälp av följande snabb starts mall: [skapa en samling distribution](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)av fjärrskrivbordssessioner.

Om du snabbt vill skapa en lokal infrastruktur för fjärr skrivbords tjänster manuellt för testning följer du stegen för att distribuera en.
**Läs mer**: [distribuera fjärr skrivbords tjänster med Azure snabb start](/windows-server/remote/remote-desktop-services/rds-in-azure) och [grundläggande distribution av fjärr skrivbords infrastruktur](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licens

Krävs är en licens för Azure AD MFA, som är tillgänglig via Azure AD Premium eller andra paket som innehåller den. Konsumtions licenser för Azure AD MFA, till exempel per användare eller per autentiserade licenser, är inte kompatibla med NPS-tillägget. Mer information finns i [så här skaffar du Azure AD-Multi-Factor Authentication](concept-mfa-licensing.md). I test syfte kan du använda en utvärderings prenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i det här avsnittet utfördes med hjälp av Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rollen nätverks princip-och nätverks åtkomst tjänster (NPS)

NPS-rolltjänsten tillhandahåller RADIUS-server och klient funktioner samt hälso tjänst för nätverks åtkomst princip. Den här rollen måste installeras på minst två datorer i infrastrukturen: Fjärrskrivbordsgateway och en annan medlems Server eller domänkontrollant. Rollen finns redan som standard på den dator som har kon figurer ATS som fjärr skrivbords-Gateway.  Du måste också installera NPS-rollen på minst en annan dator, till exempel en domänkontrollant eller medlems Server.

Information om hur du installerar NPS-rolltjänsten Windows Server 2012 eller äldre finns i [installera en NAP-hälsopolicy Server](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). En beskrivning av metod tips för NPS, inklusive rekommendationer för att installera NPS på en domänkontrollant finns i [metod tips för NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synkroniseras med lokala Active Directory

Om du vill använda NPS-tillägget måste lokala användare synkroniseras med Azure AD och aktive ras för MFA. Det här avsnittet förutsätter att lokala användare synkroniseras med Azure AD med AD Connect. Information om Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-ID

Om du vill installera NPS-tillägget måste du känna till GUID för Azure AD. Instruktioner för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication

Det här avsnittet innehåller anvisningar för att integrera Azure AD MFA med Fjärrskrivbordsgateway. Som administratör måste du konfigurera Azure AD MFA-tjänsten innan användarna kan registrera sina Multi-Factor-enheter eller-program själv.

Följ stegen i [komma igång med Azure ad Multi-Factor Authentication i molnet](howto-mfa-getstarted.md) för att aktivera MFA för dina Azure AD-användare.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering

När ett konto har Aktiver ATS för MFA kan du inte logga in på resurser som styrs av MFA-principen förrän du har konfigurerat en betrodd enhet som ska användas för den andra autentiserings faktorn och har autentiserats med hjälp av tvåstegsverifiering.

Följ stegen i [vad kan Azure AD Multi-Factor Authenticationa för mig?](../user-help/multi-factor-authentication-end-user-first-time.md) för att förstå och konfigurera dina enheter korrekt för MFA med ditt användar konto.

> [!IMPORTANT]
> Inloggnings beteendet för fjärrskrivbordsgateway ger inte möjlighet att ange en verifierings kod med Azure AD Multi-Factor Authentication. Ett användar konto måste konfigureras för telefon verifiering eller Microsoft Authenticator-appen med push-meddelanden.
>
> Om någon av dessa två autentiseringsmetoder inte har kon figurer ATS för en användare kan de inte slutföra Azure AD Multi-Factor Authentication-utmaningen och logga in på Fjärrskrivbordsgateway.

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägg

Det här avsnittet innehåller anvisningar för att konfigurera RDS-infrastruktur för att använda Azure AD MFA för klientautentisering med Fjärrskrivbordsgateway.

### <a name="acquire-azure-active-directory-tenant-id"></a>Hämta Azure Active Directory klient-ID

Som en del av konfigurationen av NPS-tillägget måste du ange autentiseringsuppgifter för administratörer och Azure AD-ID för din Azure AD-klient. Utför följande steg för att hämta klient-ID: t:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure-klienten.
1. I Azure Portal-menyn väljer du **Azure Active Directory** eller söker efter och väljer **Azure Active Directory** från vilken sida som helst.
1. På sidan **Översikt** visas *klient informationen* . Bredvid *klient-ID: t* väljer du **kopierings** ikonen, som du ser i följande exempel skärm bild:

   ![Hämtar klient-ID: t från Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

Installera NPS-tillägget på en server som har rollen nätverks princip-och nätverks åtkomst tjänster (NPS) installerad. Detta fungerar som RADIUS-server för din design.

> [!IMPORTANT]
> Installera inte NPS-tillägget på RDG-servern (Remote Desktop Gateway). RDG-servern använder inte RADIUS-protokollet med sin klient, så tillägget kan inte tolka och utföra MFA.
>
> När RDG-servern och NPS-servern med NPS-tillägget är olika servrar använder RDG NPS internt för att kommunicera med andra NPS-servrar och använder RADIUS som protokoll för att kommunicera korrekt.

1. Ladda ned [NPS-tillägget](https://aka.ms/npsmfa).
1. Kopiera installations programmets körbara fil (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
1. Dubbelklicka på **NpsExtnForAzureMfaInstaller.exe** på NPS-servern. Om du uppmanas till det klickar du på **Kör**.
1. I dialog rutan NPS-tillägg för installationen av Azure AD MFA granskar du licens villkoren för program vara, kontrollerar **att jag godkänner licens villkoren** och klickar på **Installera**.
1. I dialog rutan NPS-tillägg för installationen av Azure AD MFA klickar du på **Stäng**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Därefter måste du konfigurera certifikat som ska användas av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenterna innehåller ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat
* Associerar certifikatets offentliga nyckel till tjänstens huvud namn i Azure AD
* Lagrar certifikatet i den lokala datorns Arkiv
* Ger åtkomst till certifikatets privata nyckel till nätverks användaren
* Startar om nätverks princip Server tjänsten

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för ditt certifikat till tjänstens huvud namn i Azure AD och så vidare.

Om du vill använda skriptet anger du tillägget med dina autentiseringsuppgifter för Azure AD-administratören och det Azure AD-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerade NPS-tillägget. Gör något av följande:

1. Öppna en administrativ Windows PowerShell-prompt.
1. I PowerShell-prompten skriver du `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` och trycker på **RETUR**.
1. Skriv `.\AzureMfaNpsExtnConfigSetup.ps1` och tryck på **RETUR**. Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om den inte är installerad installerar skriptet modulen åt dig.

   ![Köra AzureMfaNpsExtnConfigSetup.ps1 i Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. När skriptet har verifierat installationen av PowerShell-modulen visas dialog rutan Azure Active Directory PowerShell-modul. Ange dina autentiseringsuppgifter och lösen ord för Azure AD-administratören i dialog rutan och klicka på **Logga** in.

   ![Autentisera till Azure AD i PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. När du uppmanas klistrar du in *klient-ID: t* som du kopierade till Urklipp tidigare och trycker på **RETUR**.

   ![Mata in klient-ID i PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skriptet skapar ett självsignerat certifikat och utför andra konfigurations ändringar. Utdata ska vara som den bild som visas nedan.

   ![Utdata från PowerShell som visar självsignerade certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på Fjärrskrivbordsgateway

I det här avsnittet konfigurerar du Auktoriseringsprinciper för fjärr skrivbords anslutning och andra RADIUS-inställningar.

Autentiserings flödet kräver att RADIUS-meddelanden utbyts mellan Fjärrskrivbordsgateway och NPS-servern där NPS-tillägget är installerat. Det innebär att du måste konfigurera RADIUS-klientinställningar på både Fjärrskrivbordsgateway och NPS-servern där NPS-tillägget är installerat.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera Auktoriseringsprinciper för fjärr skrivbords anslutning för att använda central lagring

Auktoriseringsprinciper för fjärr skrivbords anslutning (RD-inställningar) anger kraven för att ansluta till en server för fjärrskrivbordsgateway. Auktoriseringsprinciper för fjärr skrivbords anslutning kan lagras lokalt (standard) eller lagras i ett centralt arkiv för Auktoriseringsprinciper för fjärr skrivbords anslutning som kör NPS. Om du vill konfigurera integrering av Azure AD MFA med RDS måste du ange användningen av en central lagrings plats.

1. Öppna **Serverhanteraren** på RD Gateway-servern.
1. Klicka på **verktyg** på menyn, peka på **Fjärrskrivbordstjänster** och klicka sedan på **hanteraren för fjärrskrivbordsgateway**.
1. I Hanteraren för fjärrskrivbordsgateway högerklickar du på **\[ Server namn \] (lokalt)** och klickar på **Egenskaper**.
1. I dialog rutan Egenskaper väljer du fliken **lagring av Auktoriseringsprinciper för fjärr SKRIVBORDS anslutning** .
1. På fliken Arkiv för Auktoriseringsprinciper för fjärr skrivbords anslutning väljer du **Central Server som kör NPS**. 
1. I fältet **Ange ett namn eller en IP-adress för den server som kör NPS** anger du IP-adressen eller Server namnet för den server där du installerade NPS-tillägget.

   ![Ange namn eller IP-adress för din NPS-server](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicka på **Lägg till**.
1. I dialog rutan **delad hemlighet** anger du en delad hemlighet och klickar sedan på **OK**. Se till att du registrerar den här delade hemligheten och lagra posten på ett säkert sätt.

   >[!NOTE]
   >Delad hemlighet används för att upprätta förtroende mellan RADIUS-servrar och-klienter. Skapa en lång och komplex hemlighet.
   >

   ![Skapa en delad hemlighet för att upprätta förtroende](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS timeout-värde på NPS för fjärrskrivbordsgateway

För att säkerställa att det finns tid att validera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, ta emot svar och svara på RADIUS-meddelanden, är det nödvändigt att ändra värdet för RADIUS-timeout.

1. Öppna Serverhanteraren på RD Gateway-servern. På menyn klickar du på **verktyg** och sedan på **nätverks princip Server**.
1. I **NPS-konsolen (lokal)** expanderar du **RADIUS-klienter och-servrar** och väljer **fjärr-RADIUS-server**.

   ![Hanterings konsol för nätverks princip server som visar fjärr-RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. I informations fönstret dubbelklickar du på **TS Gateway Server grupp**.

   >[!NOTE]
   >Den här RADIUS-serverrollen skapades när du konfigurerade den centrala servern för NPS-principer. RD Gateway vidarebefordrar RADIUS-meddelanden till den här servern eller gruppen med servrar, om mer än en i gruppen.
   >

1. I dialog rutan **Egenskaper för TS Gateway-servergrupp** väljer du IP-adressen eller namnet på den NPS-server som du konfigurerade för att lagra Auktoriseringsprinciper för fjärr skrivbords anslutning och klickar sedan på **Redigera**.

   ![Välj IP-adressen eller namnet på den tidigare konfigurerade NPS-servern](./media/howto-mfa-nps-extension-rdg/image13.png)

1. I dialog rutan **Redigera RADIUS-server** väljer du fliken **belastnings utjämning** .
1. På fliken **belastnings utjämning** i **antal sekunder utan svar innan begäran anses vara släppt** , ändra standardvärdet från 3 till ett värde mellan 30 och 60 sekunder.
1. I **antal sekunder mellan begär anden när servern identifieras som otillgänglig** , ändra standardvärdet 30 sekunder till ett värde som är lika med eller större än värdet som du angav i föregående steg.

   ![Redigera inställningar för tids gräns för RADIUS-server på fliken belastnings utjämning](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicka på **OK** två gånger för att stänga dialog rutorna.

### <a name="verify-connection-request-policies"></a>Verifiera principer för anslutningsbegäran

När du konfigurerar RD Gateway att använda en central princip lagring för principer för anslutningsauktorisering, konfigureras RD Gateway som standard för att vidarebefordra CAP-begäranden till NPS-servern. NPS-servern med Azure AD MFA-tillägget installerat, bearbetar begäran om RADIUS-åtkomst. Följande steg visar hur du verifierar standard principen för anslutningsbegäran.  

1. På RD Gateway, i NPS-konsolen (lokal), expanderar du **principer** och väljer **principer för anslutningsbegäran**.
1. Dubbelklicka på **TS Gateway-auktoriseringsprincip**.
1. I dialog rutan **Egenskaper för TS Gateway-auktoriseringsprincip** klickar du på fliken **Inställningar** .
1. På fliken **Inställningar** , under vidarebefordra anslutningsbegäran, klickar du på **autentisering**. RADIUS-klienten är konfigurerad för att vidarebefordra förfrågningar om autentisering.

   ![Konfigurera autentiseringsinställningar som anger Server gruppen](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicka på **Avbryt**.

>[!NOTE]
> Mer information om hur du skapar en princip för anslutningsbegäran finns i artikeln [Konfigurera principer](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) för anslutningsbegäran. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på den server där NPS-tillägget är installerat

NPS-servern där NPS-tillägget har installerats måste kunna utväxla RADIUS-meddelanden med NPS-servern på Fjärrskrivbordsgateway. Om du vill aktivera det här meddelande utbytet måste du konfigurera NPS-komponenterna på den server där NPS-tillägget är installerat.

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory

För att fungera korrekt i det här scenariot måste NPS-servern registreras i Active Directory.

1. Öppna **Serverhanteraren** på NPS-servern.
1. I Serverhanteraren klickar du på **verktyg** och sedan på **nätverks princip Server**.
1. I konsolen nätverks princip Server högerklickar du på **NPS (lokal)** och klickar sedan på **registrera server i Active Directory**.
1. Klicka på **OK** två gånger.

   ![Registrera NPS-servern i Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Låt konsolen vara öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klient

Fjärrskrivbordsgateway måste konfigureras som en RADIUS-klient till NPS-servern.

1. På NPS-servern där NPS-tillägget är installerat går du till **NPS-konsolen (lokal)** , högerklickar på **RADIUS-klienter** och klickar på **ny**.

   ![Skapa en ny RADIUS-klient i NPS-konsolen](./media/howto-mfa-nps-extension-rdg/image17.png)

1. I dialog rutan **ny RADIUS-klient** anger du ett eget namn, till exempel _Gateway_ och IP-adressen eller DNS-namnet på servern för fjärrskrivbordsgateway.
1. I fälten **delad hemlighet** och **Bekräfta delad hemlighet** anger du samma hemlighet som du använde tidigare.

   ![Konfigurera ett eget namn och IP-eller DNS-adressen](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klicka på **OK** för att stänga dialog rutan ny RADIUS-klient.

### <a name="configure-network-policy"></a>Konfigurera nätverks princip

Kom ihåg att NPS-servern med Azure AD MFA-tillägget är den utsedda centrala princip lagringen för principen för anslutningsauktorisering (CAP). Därför måste du implementera ett tak på NPS-servern för att auktorisera giltiga anslutnings begär Anden.  

1. Öppna NPS-konsolen (lokal) på NPS-servern, expandera **principer** och klicka på **nätverks principer**.
1. Högerklicka på **anslutningar till andra åtkomst servrar** och klicka på **duplicera princip**.

   ![Duplicera anslutningen till andra åtkomst server principer](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Högerklicka på **kopia av anslutningar till andra åtkomst servrar** och klicka på **Egenskaper**.
1. I dialog rutan **kopia av anslutningar till andra åtkomst servrar** , under **princip namn**, anger du ett lämpligt namn, till exempel _RDG_CAP_. Kontrol lera att **principen är aktive rad** och välj **bevilja åtkomst**. Alternativt, i **typ av nätverks åtkomst Server**, väljer du **Fjärrskrivbordsgateway**, eller så kan du lämna den som **ospecificerad**.

   ![Namnge principen, aktivera och bevilja åtkomst](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicka på fliken **begränsningar** och markera **Tillåt att klienter ansluter utan att förhandla om en autentiseringsmetod**.

   ![Ändra autentiseringsmetoder för att tillåta klienter att ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Du kan också klicka på fliken **villkor** och lägga till villkor som måste uppfyllas för att anslutningen ska kunna godkännas, till exempel medlemskap i en speciell Windows-grupp.

   ![Alternativt kan du ange anslutnings villkor](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicka på **OK**. När du uppmanas att visa motsvarande hjälp avsnitt klickar du på **Nej**.
1. Se till att den nya principen är överst i listan, att principen är aktive rad och att den ger åtkomst.

   ![Flytta principen överst i listan](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfigurationen

För att verifiera konfigurationen måste du logga in på Remote Desktop Gateway med lämplig RDP-klient. Se till att använda ett konto som tillåts av dina principer för anslutningsauktorisering och är aktiverat för Azure AD MFA.

Som du ser i bilden nedan kan du använda **webb åtkomst sidan för fjärr skrivbord** .

![Testa i webb åtkomst till fjärr skrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering, visar dialog rutan Anslut till fjärr skrivbord statusen initierad fjärr anslutning, som visas nedan. 

Om du har autentiserat dig med den sekundära autentiseringsmetoden som du tidigare konfigurerade i Azure AD MFA är du ansluten till resursen. Men om den sekundära autentiseringen inte lyckas nekas du åtkomst till resursen. 

![Anslutning till fjärrskrivbord att initiera en fjärr anslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows Phone för att tillhandahålla den sekundära autentiseringen.

![Exempel på Windows Phone Authenticator-app som visar verifiering](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserat med den sekundära autentiseringsmetoden är du inloggad på Fjärrskrivbordsgateway som normalt. Men eftersom du måste använda en sekundär autentiseringsmetod med en mobilapp på en betrodd enhet är inloggnings processen säkrare än vad som annars skulle vara.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa Loggboken loggar för lyckade inloggnings händelser

Om du vill visa lyckade inloggnings händelser i Windows Loggboken loggar kan du utfärda följande Windows PowerShell-kommando för att fråga Windows Terminal Services-och Windows-säkerhetsloggarna.

Använd följande PowerShell-kommandon för att fråga lyckade inloggnings händelser i de operativa loggarna för gateway _(Event loggboken\program-och Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Det här kommandot visar Windows-händelser som visar att användaren uppfyllde princip kraven för resursutnyttjande (RD RAP) och beviljats åtkomst.

![Visa händelser med PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Det här kommandot visar de händelser som visar när användaren uppfyllde kraven för anslutningsauktorisering.

![Visa principen för anslutningsauktorisering med PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa loggen och filtrera efter händelse-ID: n, 300 och 200. Använd följande kommando för att fråga lyckade inloggnings händelser i logg boken för säkerhets loggen:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Det här kommandot kan köras antingen på den centrala NPS-servern eller RD Gateway-servern.

![Exempel på lyckade inloggnings händelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också Visa säkerhets loggen eller den anpassade vyn nätverks policy och åtkomst tjänster enligt nedan:

![Nätverks policy och åtkomst tjänster Loggboken](./media/howto-mfa-nps-extension-rdg/image31.png)

På den server där du installerade NPS-tillägget för Azure AD MFA kan du hitta Loggboken program loggar som är specifika för tillägget på _program-och tjänst Logs\Microsoft\AzureMfa_.

![Loggboken AuthZ program loggar](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Fel söknings guide

Om konfigurationen inte fungerar som förväntat kan du kontrol lera att användaren har kon figurer ATS för att använda Azure AD MFA. Be användaren att ansluta till [Azure Portal](https://portal.azure.com). Om användarna uppmanas att använda sekundär verifiering och kan autentiseras, kan du eliminera en felaktig konfiguration av Azure AD MFA.

Om Azure AD MFA arbetar för användare, bör du granska de relevanta händelse loggarna. Detta omfattar säkerhets händelsen, gatewayens operativa och Azure AD MFA-loggar som beskrivs i föregående avsnitt.

Nedan visas ett exempel på utdata från säkerhets loggen med en misslyckad inloggnings händelse (händelse-ID 6273).

![Exempel på en misslyckad inloggnings händelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterad händelse från AzureMFA-loggarna:

![Exempel på Azure AD MFA-inloggning Loggboken](./media/howto-mfa-nps-extension-rdg/image34.png)

Information om hur du utför avancerade fel söknings alternativ finns i loggfilerna för NPS-databasfilen där NPS-tjänsten är installerad. Loggfilerna skapas i mappen _%systemroot%\System32\Logs_ som kommaavgränsade textfiler.

En beskrivning av dessa loggfiler finns i [tolka loggfiler för NPS-databasfiler](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). Posterna i dessa loggfiler kan vara svåra att tolka utan att importera dem till ett kalkyl blad eller en databas. Du hittar flera IAS-tolkare online för att hjälpa dig att tolka loggfilerna.

Bilden nedan visar utdata från ett sådant nedladdnings Bart [shareware-program](https://www.deepsoftware.com/iasviewer).

![Exempel på shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Slutligen kan du använda en protokoll analys, till exempel [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide), för ytterligare fel söknings alternativ.

Bilden nedan från Microsoft Message Analyzer visar nätverks trafik som filtrerats på RADIUS-protokoll som innehåller användar namnet **CONTOSO\AliceC**.

![Microsoft Message Analyzer visar filtrerad trafik](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg

[Så här hämtar du Azure AD-Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)