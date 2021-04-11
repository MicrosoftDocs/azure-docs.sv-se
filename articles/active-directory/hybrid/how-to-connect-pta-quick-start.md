---
title: Vidarekoppling av Azure AD – snabb start | Microsoft Docs
description: Den här artikeln beskriver hur du kommer igång med Azure Active Directory (Azure AD) genom strömnings-autentisering.
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e71522d53bd4e528b2a5a106a4dcc344df3ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732864"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory direktautentisering: snabb start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuera direkt autentisering i Azure AD

Med direkt autentisering i Azure Active Directory (Azure AD) kan användarna logga in i både lokala och molnbaserade program genom att använda samma lösen ord. Direktautentisering loggar användare i genom att verifiera sina lösen ord direkt mot lokala Active Directory.

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra Federations tekniker) till direktautentisering, rekommenderar vi starkt att du följer vår detaljerade distributions guide publicerad [här](https://aka.ms/adfstoPTADPDownload).

>[!NOTE]
>Om du distribuerar vidarekoppling med Azure Government molnet kan du Visa [hybrid identitets överväganden för Azure Government](./reference-connect-government-cloud.md).

Följ de här anvisningarna för att distribuera direktautentisering på klienten:

## <a name="step-1-check-the-prerequisites"></a>Steg 1: kontrol lera kraven

Se till att följande krav är uppfyllda.

>[!IMPORTANT]
>Av säkerhets synpunkt bör administratören behandla servern som kör PTA-agenten som om den vore en domänkontrollant.  PTA agent-servrarna bör vara härdade längs samma rader som beskrivs i [Skydda domänkontrollanter mot angrepp](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett globalt administratörs konto för molnet på Azure AD-klienten. På så sätt kan du hantera konfigurationen av din klient organisation om dina lokala tjänster kraschar eller blir otillgängliga. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domän namn](../fundamentals/add-custom-domain.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en server som kör Windows Server 2012 R2 eller senare för att köra Azure AD Connect. Om du inte redan har aktiverat [aktiverar du TLS 1,2 på servern](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Lägg till servern i samma Active Directory skog som de användare vars lösen ord du behöver validera. Det bör noteras att installationen av Pass-Through Authentication agent på Windows Server Core-versioner inte stöds. 
2. Installera den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) på den server som identifierades i föregående steg. Om du redan har Azure AD Connect kört, se till att versionen är 1.1.750.0 eller senare.

    >[!NOTE]
    >Azure AD Connect-versionerna 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem som rör hash-synkronisering av lösen ord. Om du _inte_ tänker använda Lösenordssynkronisering tillsammans med direktautentisering kan du läsa [Azure AD Connect viktig information](./reference-connect-version-history.md).

3. Identifiera en eller flera ytterligare servrar (som kör Windows Server 2012 R2 eller senare, med TLS 1,2 aktiverat) där du kan köra fristående autentiserings agenter. Dessa ytterligare servrar behövs för att säkerställa hög tillgänglighet för begär Anden att logga in. Lägg till servrarna i samma Active Directory skog som de användare vars lösen ord du behöver validera.

    >[!IMPORTANT]
    >I produktions miljöer rekommenderar vi att du har minst 3 autentiserings agenter som körs på din klient. Det finns en system gräns på 40-autentiseringspaket per klient. Och bästa praxis är att behandla alla servrar som kör autentiseringsprinciper som system på nivå 0 (se [referens](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
   - Se till att autentiserings agenter kan göra *utgående* begär anden till Azure AD över följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifieras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (valfritt) | Authentication agents rapporterar status var tionde minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas i Azure AD-portalen. Port 8080 används _inte_ för användar inloggningar. |
     
     Om brandväggen framtvingar regler baserat på de användare som genererar den öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om din brand vägg eller proxy låter dig lägga till DNS-poster i en tillåten, lägger du till anslutningar till **\* . msappproxy.net** och **\* . ServiceBus.Windows.net**. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
   - Undvik alla former av inaktive rad inspektion och avslutning på utgående TLS-kommunikation mellan Azure passthrough-agenten och Azure-slutpunkt. 
   - Om du har en utgående HTTP-proxy kontrollerar du att URL: en, autologon.microsoftazuread-sso.com, finns på listan över tillåtna. Du bör ange den här URL: en explicit eftersom jokertecken kanske inte accepteras. 
   - Dina autentiserings agenter behöver åtkomst till **login.Windows.net** och **login.microsoftonline.com** för inledande registrering. Öppna brand väggen för dessa URL: er även.
    - För certifikat validering, avblockera följande URL: er: **crl3.DigiCert.com:80**, **crl4.DigiCert.com:80**, **ocsp.digicert.com:80**, **www \. d-trust.net:80**, **root-C3-CA2-2009.OCSP.d-Trust.net:80**, **CRL.Microsoft.com:80**, **oneocsp.Microsoft.com:80** och **OCSP.msocsp.com:80**. Eftersom dessa URL: er används för certifikat validering med andra Microsoft-produkter kan dessa URL: er vara avblockerade.

### <a name="azure-government-cloud-prerequisite"></a>Förutsättning för Azure Government moln
Innan du aktiverar direktautentisering genom Azure AD Connect med steg 2 laddar du ned den senaste versionen av PTA-agenten från Azure Portal.  Du måste se till att agenten är versions **1.5.1742.0.** eller senare.  För att verifiera agenten se [Upgrade Authentication agents](how-to-connect-pta-upgrade-preview-authentication-agents.md)

När du har laddat ned den senaste versionen av agenten fortsätter du med anvisningarna nedan för att konfigurera Pass-Through autentisering via Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Steg 2: aktivera funktionen

Aktivera direkt autentisering via [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Du kan aktivera direktautentisering på den Azure AD Connect primära eller mellanlagrings servern. Vi rekommenderar starkt att du aktiverar den från den primära servern. Om du ställer in en Azure AD Connect fristående server i framtiden **måste** du fortsätta att välja direktautentisering som inloggnings alternativ. Om du väljer ett annat alternativ **inaktive ras** direkt autentisering på klienten och åsidosätter inställningen på den primära servern.

Om du installerar Azure AD Connect för första gången väljer du den [anpassade installations Sök vägen](how-to-connect-install-custom.md). På sidan **användar inloggning** väljer du **direktautentisering** som **inloggnings metod**. Vid lyckad slut för ande installeras en vidarekoppling på samma server som Azure AD Connect. Dessutom är funktionen för direkt autentisering aktive rad på din klient.

![Azure AD Connect: användar inloggning](./media/how-to-connect-pta-quick-start/sso3.png)

Om du redan har installerat Azure AD Connect med hjälp av [Express installation](how-to-connect-install-express.md) eller [anpassad installations](how-to-connect-install-custom.md) Sök väg väljer du uppgiften **ändra användar inloggning** på Azure AD Connect och väljer sedan **Nästa**. Välj sedan **direktautentisering** som inloggnings metod. Vid lyckad slut för ande installeras en vidarekoppling på samma server som Azure AD Connect och funktionen är aktive rad på din klient.

![Azure AD Connect: ändra användar inloggning](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Direktautentisering är en funktion på klient nivå. Att aktivera det påverkar inloggningen för användare i _alla_ hanterade domäner i din klient organisation. Om du växlar från Active Directory Federation Services (AD FS) (AD FS) för att sprida autentiseringen bör du vänta minst 12 timmar innan du stänger av din AD FS-infrastruktur. Den här vänte tiden är att se till att användarna kan fortsätta att logga in på Exchange ActiveSync under över gången. Mer hjälp om hur du migrerar från AD FS till direktautentisering finns i vår detaljerade distributions plan publicerad [här](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Steg 3: testa funktionen

Följ de här anvisningarna för att kontrol lera att du har aktiverat direktautentisering korrekt:

1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient organisation.
2. Välj **Azure Active Directory** i det vänstra fönstret.
3. Välj **Azure AD Connect**.
4. Kontrol lera att funktionen för **direkt autentisering** visas som **aktive rad**.
5. Välj **direktautentisering**. I fönstret genom **strömnings** fönstret visas de servrar där dina autentiseringsinställningar är installerade.

![Azure Active Directory administrations Center: Azure AD Connects fönstret](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory administrations Center: direkt inloggnings fönster](./media/how-to-connect-pta-quick-start/pta8.png)

I det här skedet kan användare från alla hanterade domäner i din klient logga in med hjälp av direktautentisering. Användare från federerade domäner fortsätter dock att logga in genom att använda AD FS eller en annan Federations leverantör som du tidigare har konfigurerat. Om du konverterar en domän från federerade till hanterad börjar alla användare från den domänen att logga in automatiskt med hjälp av direktautentisering. Funktionen för direkt autentisering påverkar inte enbart moln användare.

## <a name="step-4-ensure-high-availability"></a>Steg 4: Säkerställ hög tillgänglighet

Om du planerar att distribuera direktautentisering i en produktions miljö bör du installera ytterligare fristående autentiserings agenter. Installera de här autentiseringsinställningarna på _andra_ servrar än den som kör Azure AD Connect. Den här inställningen ger dig hög tillgänglighet för användar inloggnings begär Anden.

>[!IMPORTANT]
>I produktions miljöer rekommenderar vi att du har minst 3 autentiserings agenter som körs på din klient. Det finns en system gräns på 40-autentiseringspaket per klient. Och bästa praxis är att behandla alla servrar som kör autentiseringsprinciper som system på nivå 0 (se [referens](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Att installera flera direktautentisering för direktautentisering säkerställer hög tillgänglighet, men inte deterministisk belastnings utjämning mellan Authentication Agents. Ta reda på hur många autentiseringsmetoder du behöver för din klient genom att tänka på den högsta och genomsnittliga belastningen för de inloggnings begär Anden som du förväntar dig att se på din klient. Som riktmärke kan en enda autentiserings-agent hantera 300 till 400-autentiseringar per sekund på en standard processor på 4 kärnor, 16 GB RAM-Server.

Använd följande vägledning för att beräkna nätverks trafik:
- Varje begäran har en nytto Last storlek på (0,5 K + 1 KB * num_of_agents) byte, det vill säga data från Azure AD till Authentication agent. Här anger "num_of_agents" det antal autentiseringspaket som har registrerats för din klient.
- Varje svar har en nytto Last storlek på 1 kB byte, det vill säga data från Authentication agent till Azure AD.

För de flesta kunder räcker det att tre autentiserande agenter för hög tillgänglighet och kapacitet. Du bör installera autentiseringsprinciper nära domän kontrol Lanterna för att förbättra inloggnings fördröjningen.

Börja genom att följa de här anvisningarna för att hämta program varan för autentiserings agenten:

1. Om du vill hämta den senaste versionen av Autentiseringstjänsten (version 1.5.193.0 eller senare) loggar du in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med klient organisationens globala administratörs behörigheter.
2. Välj **Azure Active Directory** i det vänstra fönstret.
3. Välj **Azure AD Connect**, Välj **direktautentisering** och välj sedan **Hämta agent**.
4. Välj knappen **acceptera villkor & hämtning** .

![Azure Active Directory administrations Center: knappen Hämta Authentication agent](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory administrations Center: Ladda ned agent fönstret](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Du kan också [Hämta program varan för Authentication agent](https://aka.ms/getauthagent)direkt. Granska och godkänn [användnings villkoren för](https://aka.ms/authagenteula) autentiserings agenten _innan_ du installerar den.

Det finns två sätt att distribuera en fristående autentiserings agent:

Först kan du göra det interaktivt genom att köra den hämtade körbara autentiserings agenten och ange klient organisationens autentiseringsuppgifter för global administratör när du uppmanas till det.

Sedan kan du skapa och köra ett obevakat distributions skript. Detta är användbart när du vill distribuera flera autentiseringsförsök samtidigt eller installera autentiseringsprinciper på Windows-servrar som inte har användar gränssnitt aktiverat eller som du inte kan komma åt med fjärr skrivbord. Här följer instruktioner för hur du använder den här metoden:

1. Kör följande kommando för att installera en agent för autentisering: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` .
2. Du kan registrera Autentiseringstjänsten med tjänsten med hjälp av Windows PowerShell. Skapa ett PowerShell-autentiseringsuppgifter `$cred` som innehåller ett globalt administratörs användar namn och lösen ord för din klient. Kör följande kommando och Ersätt *\<username\>* och *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Gå till **c:\program\microsoft Azure AD Connect Authentication agent** och kör följande skript med det `$cred` objekt som du skapade:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Om en autentiseringstjänst är installerad på en virtuell dator kan du inte klona den virtuella datorn för att konfigurera en annan agent för autentisering. Den här metoden **stöds inte**.

## <a name="step-5-configure-smart-lockout-capability"></a>Steg 5: Konfigurera funktionen för smart utelåsning

Smart utelåsning hjälper dig att låsa upp dåliga aktörer som försöker gissa dina användares lösen ord eller använda brute-force-metoder för att komma igång. Genom att konfigurera inställningar för smart utelåsning i Azure AD och/eller lämpliga utelåsnings inställningar i lokala Active Directory kan attacker filtreras ut innan de når Active Directory. Läs [den här artikeln](../authentication/howto-password-smart-lockout.md) om du vill veta mer om hur du konfigurerar inställningar för smart utelåsning på din klient organisation för att skydda dina användar konton.

## <a name="next-steps"></a>Nästa steg
- [Migrera från AD FS till vidarekoppling](https://aka.ms/adfstoptadp) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smart utelåsning på klienten för att skydda användar konton.
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds med direkt autentiseringen och vilka som inte är det.
- [Teknisk djupgående](how-to-connect-pta-how-it-works.md): förstå hur funktionen för direkt autentisering fungerar.
- [Vanliga frågor](how-to-connect-pta-faq.md)och svar: få svar på vanliga frågor.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen för direkt autentisering.
- [Djupgående säkerhets](how-to-connect-pta-security-deep-dive.md)information: få teknisk information om funktionen för direkt autentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Lär dig mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet för att fil nya funktions begär Anden.
