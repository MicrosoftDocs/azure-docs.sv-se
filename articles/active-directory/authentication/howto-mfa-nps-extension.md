---
title: Använda Azure AD Multi-Factor Authentication med NPS – Azure Active Directory
description: Lär dig hur du använder Azure AD Multi-Factor Authentication-funktioner med din befintliga infrastruktur för nätverks Policy Server (NPS)
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: deb05083ca45c24a58cabf9e923b706575ef093b
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562148"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Integrera din befintliga infrastruktur för nätverks Policy Server (NPS) med Azure AD Multi-Factor Authentication

Nätverks princip Server (NPS)-tillägget för Azure AD Multi-Factor Authentication lägger till molnbaserade MFA-funktioner till din infrastruktur för autentisering med hjälp av befintliga servrar. Med NPS-tillägget kan du lägga till telefonsamtal, textmeddelande eller telefon programs verifiering till ditt befintliga autentiseringspaket utan att behöva installera, konfigurera och underhålla nya servrar.

NPS-tillägget fungerar som ett kort mellan RADIUS-och molnbaserad Azure AD-Multi-Factor Authentication för att tillhandahålla en andra faktor för autentisering för federerade eller synkroniserade användare.

## <a name="how-the-nps-extension-works"></a>Så här fungerar NPS-tillägget

När du använder NPS-tillägget för Azure AD Multi-Factor Authentication innehåller autentiserings flödet följande komponenter:

1. **NAS/VPN-servern** tar emot begär Anden från VPN-klienter och konverterar dem till RADIUS-begäranden till NPS-servrar.
2. **NPS-servern** ansluter till Active Directory Domain Services (AD DS) för att utföra den primära autentiseringen för RADIUS-begärandena och skickar begäran till eventuella installerade tillägg vid lyckad.  
3. **NPS-tillägget** utlöser en begäran till Azure AD Multi-Factor Authentication för den sekundära autentiseringen. När tillägget får svaret, och om MFA-utmaningen lyckas, slutförs autentiseringsbegäran genom att tillhandahålla NPS-servern med säkerhetstoken som innehåller ett MFA-anspråk som utfärdats av Azure STS.
4. **Azure AD MFA** kommunicerar med Azure Active Directory (Azure AD) för att hämta användarens information och utför den sekundära autentiseringen med hjälp av en verifieringsmetod som kon figurer ATS för användaren.

Följande diagram illustrerar det här flödet för begäran om autentisering på hög nivå:

![Diagram över autentiseringsschemat för användare som autentiseras via en VPN-server till NPS-servern och Azure AD Multi-Factor Authentication NPS-tillägget](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>RADIUS-protokoll beteende och NPS-tillägget

När RADIUS är ett UDP-protokoll antar avsändaren paket förlust och väntar på ett svar. Efter en viss tids period kan anslutningen ta lång tid. I så fall skickas paketet igen eftersom avsändaren antar att paketet inte når målet. I scenariot för autentisering i den här artikeln skickar VPN-servrar begäran och väntar på svar. Om anslutnings tiden är slut skickar VPN-servern begäran igen.

![Diagram över RADIUS-paketets flöde och begär Anden efter tids gränsen för svar från NPS-servern](./media/howto-mfa-nps-extension/radius-flow.png)

NPS-servern svarar eventuellt inte på VPN-serverns ursprungliga begäran innan anslutnings tiden är slut eftersom MFA-begäran fortfarande bearbetas. Användaren har kanske inte svarat på MFA-prompten, så Azure AD Multi-Factor Authentication NPS-tillägget väntar på att händelsen ska slutföras. I det här fallet identifierar NPS-servern ytterligare VPN-server begär Anden som en dubblett av begäran. NPS-servern ignorerar dessa dubbla VPN-begäranden.

![Diagram över NPS-servern som tar bort dubbla begär Anden från RADIUS-servern](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Om du tittar på NPS-serverns loggar kan du se att dessa ytterligare begär Anden tas bort. Det här beteendet är avsiktligt för att skydda användaren från att få flera begär Anden för ett enda autentiseringsförsök. Ignorerade begär anden i händelse loggen för NPS-servern indikerar inte att det är ett problem med NPS-servern eller Azure AD Multi-Factor Authentication NPS-tillägget.

För att minimera ignorerade begär Anden rekommenderar vi att VPN-servrar konfigureras med en tids gräns på minst 60 sekunder. Om det behövs, eller om du vill minska antalet förkastade begär anden i händelse loggarna, kan du öka timeout-värdet för VPN-servern till 90 eller 120 sekunder.

På grund av detta UDP-protokoll kan NPS-servern ta emot en dubblett av begäran och skicka en annan MFA-prompt, även om användaren redan har svarat på den ursprungliga begäran. För att undvika detta tids villkor fortsätter Azure AD Multi-Factor Authentication NPS-tillägget att filtrera och ta bort dubbla begär anden i upp till 10 sekunder efter att ett lyckat svar har skickats till VPN-servern.

![Diagram över NPS-servern fortsätter att ta bort dubbla begär Anden från VPN-servern i tio sekunder efter att ett lyckat svar returnerats](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Återigen kan du se ignorerade begär anden i händelse loggarna för NPS-servern, även när Azure AD-Multi-Factor Authentication-prompten lyckades. Detta är ett förväntat beteende och indikerar inte något problem med NPS-servern eller Azure AD Multi-Factor Authentication NPS-tillägget.

## <a name="plan-your-deployment"></a>Planera distributionen

NPS-tillägget hanterar automatiskt redundans, så du behöver inte en speciell konfiguration.

Du kan skapa så många Azure AD Multi-Factor Authentication-aktiverade NPS-servrar som du behöver. Om du installerar flera servrar bör du använda ett skillnads klient certifikat för var och en av dem. Att skapa ett certifikat för varje server innebär att du kan uppdatera varje certifikat individuellt och inte bekymra dig om nedtid på alla dina servrar.

VPN-servrar dirigerar autentiseringsbegäranden, så de måste vara medvetna om de nya Azure AD Multi-Factor Authentication-aktiverade NPS-servrarna.

## <a name="prerequisites"></a>Förutsättningar

NPS-tillägget är avsett att fungera med din befintliga infrastruktur. Kontrol lera att du har följande krav innan du börjar.

### <a name="licenses"></a>Licenser

NPS-tillägget för Azure AD Multi-Factor Authentication är tillgängligt för kunder med [licenser för Azure ad Multi-Factor Authentication](./concept-mfa-howitworks.md). Användnings licenser för Azure AD Multi-Factor Authentication, till exempel per användare eller per autentisering, är inte kompatibla med NPS-tillägget.

### <a name="software"></a>Programvara

Windows Server 2012 eller senare.

### <a name="libraries"></a>Bibliotek

Du måste installera följande bibliotek manuellt:

- [Visual C++ Redistributable för Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Följande bibliotek installeras automatiskt med tillägget.

- [Visual C++ Redistributable-paket för Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-modul för Windows PowerShell version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory-modul för Windows PowerShell installeras också via ett konfigurations skript som du kör som en del av installations processen, om det inte redan finns. Du behöver inte installera den här modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory"></a>Azure Active Directory

Alla som använder NPS-tillägget måste synkroniseras till Azure AD med hjälp av Azure AD Connect och måste registreras för MFA.

När du installerar tillägget behöver du *klient-ID* och admin-autentiseringsuppgifter för din Azure AD-klient. Utför följande steg för att hämta klient-ID: t:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure-klienten.
1. Sök efter och välj **Azure Active Directory**.
1. På sidan **Översikt** visas *klient informationen* . Bredvid *klient-ID: t* väljer du **kopierings** ikonen, som du ser i följande exempel skärm bild:

   ![Hämtar klient-ID: t från Azure Portal](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Nätverkskrav

NPS-servern måste kunna kommunicera med följande URL: er över portarna 80 och 443:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /credentials.Azure.com*

Dessutom krävs anslutning till följande URL: er för att slutföra [installationen av kortet med hjälp av det angivna PowerShell-skriptet](#run-the-powershell-script):

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du installerar NPS-tillägget förbereder du miljön för att hantera autentiserings trafiken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivera NPS-rollen på en domänansluten Server

NPS-servern ansluter till Azure AD och autentiserar MFA-begärandena. Välj en server för den här rollen. Vi rekommenderar att du väljer en server som inte hanterar förfrågningar från andra tjänster, eftersom NPS-tillägget genererar fel för förfrågningar som inte är RADIUS. NPS-servern måste vara konfigurerad som den primära och sekundära autentiseringsservern för din miljö. Den kan inte proxy RADIUS-begäranden till en annan server.

1. Öppna **Serverhanteraren** på servern. Välj **guiden Lägg till roller och funktioner** från *snabb starts* menyn.
2. Välj **rollbaserad eller funktions baserad installation** för installations typen.
3. Välj Server rollen **nätverks policy och åtkomst tjänster** . Ett fönster kan visas som informerar dig om ytterligare nödvändiga funktioner för att köra den här rollen.
4. Fortsätt med guiden tills *bekräftelse* sidan. När du är klar väljer du **Installera**.

Det kan ta några minuter att installera NPS-serverrollen. När du är färdig fortsätter du med följande avsnitt för att konfigurera servern för att hantera inkommande RADIUS-begäranden från VPN-lösningen.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurera VPN-lösningen för att kommunicera med NPS-servern

Stegen för att konfigurera din RADIUS-autentiseringsprincip varierar beroende på vilken VPN-lösning du använder. Konfigurera VPN-principen så att den pekar på RADIUS-NPS-servern.

### <a name="sync-domain-users-to-the-cloud"></a>Synkronisera domän användare till molnet

Det här steget kanske redan har slutförts på din klient, men det är bäst att kontrol lera att Azure AD Connect har synkroniserat dina databaser nyligen.

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory**  >  **Azure AD Connect**
3. Kontrol lera att din synkroniseringsstatus är **aktive rad** och att din senaste synkronisering var mindre än en timme sedan.

Om du behöver starta en ny Round-synkronisering kan du läsa [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bestäm vilka autentiseringsmetoder som användarna kan använda

Det finns två faktorer som påverkar vilka autentiseringsmetoder som är tillgängliga med en distribution av NPS-tillägg:

* Algoritmen för lösen ords kryptering som används mellan RADIUS-klienten (VPN, NetScaler Server eller andra) och NPS-servrarna.
   - **PAP** stöder alla autentiseringsmetoder för Azure AD-Multi-Factor Authentication i molnet: telefonsamtal, envägs textmeddelande, mobilapp, Oath-token och verifierings kod för mobilapp.
   - **CHAPv2** -och **EAP** -support för telefonsamtal och aviseringar för mobilapp.

    > [!NOTE]
    > När du distribuerar NPS-tillägget använder du dessa faktorer för att utvärdera vilka metoder som är tillgängliga för dina användare. Om din RADIUS-klient har stöd för PAP, men klientens UX saknar indatafält för en verifierings kod, är telefonsamtal och aviseringar för mobilapp de två alternativen som stöds.
    >
    > Dessutom, oavsett vilket autentiseringsprotokoll som används (PAP, CHAP eller EAP), om din MFA-metod är textbaserad (SMS, verifierings kod eller OATH-token) och kräver att användaren anger en kod eller text i indatatypen för VPN-klientens UI, kan autentiseringen lyckas. *Men* alla RADIUS-attribut som kon figurer ATS i principen för nätverks åtkomst vidarebefordras *inte* till RADIUS-cient (nätverks åtkomst enheten, t. ex. VPN-gatewayen). Det innebär att VPN-klienten kan ha mer åtkomst än du vill att den ska ha eller mindre åtkomst eller ingen åtkomst.

* De ingångs metoder som klient programmet (VPN, NetScaler-servern eller andra) kan hantera. Exempelvis har VPN-klienten några sätt att tillåta användaren att ange en verifierings kod från en text-eller mobilapp?

Du kan [inaktivera autentiseringsmetoder som inte stöds](howto-mfa-mfasettings.md#verification-methods) i Azure.

### <a name="register-users-for-mfa"></a>Registrera användare för MFA

Innan du distribuerar och använder NPS-tillägget måste användare som krävs för att utföra Azure AD-Multi-Factor Authentication registreras för MFA. Om du vill testa tillägget när du distribuerar det måste du också ha minst ett test konto som är fullständigt registrerat för Azure AD Multi-Factor Authentication.

Om du behöver skapa och konfigurera ett test konto använder du följande steg:

1. Logga in på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) med ett test konto.
2. Följ anvisningarna för att ställa in en verifieringsmetod.
3. I Azure Portal som administratörs användare skapar du [en princip för villkorlig åtkomst](howto-mfa-getstarted.md#create-conditional-access-policy) som kräver Multi-Factor Authentication för test kontot.

> [!IMPORTANT]
>
> Kontrol lera att användarna har registrerat sig för Azure AD Multi-Factor Authentication. Om användare tidigare bara har registrerats för självbetjäning för återställning av lösen ord (SSPR), aktive ras *StrongAuthenticationMethods* för sitt konto. Azure AD Multi-Factor Authentication aktive ras när *StrongAuthenticationMethods* har kon figurer ATS, även om användaren bara har registrerats för SSPR.
>
> Kombinerad säkerhets registrering kan aktive ras som konfigurerar SSPR och Azure AD Multi-Factor Authentication samtidigt. Mer information finns i [Aktivera kombinerad säkerhets informations registrering i Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Du kan också [tvinga användare att registrera autentiseringsmetoder igen](howto-mfa-userdevicesettings.md#manage-user-authentication-options) om de tidigare bara har aktiverat SSPR.

## <a name="install-the-nps-extension"></a>Installera NPS-tillägget

> [!IMPORTANT]
> Installera NPS-tillägget på en annan server än VPN-åtkomst punkten.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Hämta och installera NPS-tillägget för Azure AD MFA

Slutför följande steg för att ladda ned och installera NPS-tillägget:

1. [Ladda ned NPS-tillägget](https://aka.ms/npsmfa) från Microsoft Download Center.
1. Kopiera binärfilen till den nätverks princip server som du vill konfigurera.
1. Kör *setup.exe* och följ installations anvisningarna. Om du stöter på fel, se till att [biblioteken från avsnittet förutsättning](#libraries) har installerats.

#### <a name="upgrade-the-nps-extension"></a>Uppgradera NPS-tillägget

Om du senare uppgraderar en befintlig installation av NPS-tillägget måste du utföra följande steg för att undvika en omstart av den underliggande servern:

1. Avinstallera den befintliga versionen.
1. Kör det nya installations programmet.
1. Starta om tjänsten *nätverks princip Server (IAS)* .

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Installations programmet skapar ett PowerShell-skript vid `C:\Program Files\Microsoft\AzureMfa\Config` (där `C:\` är installations enheten). Det här PowerShell-skriptet utför följande åtgärder varje gången det körs:

* Skapar ett självsignerat certifikat.
* Associerar certifikatets offentliga nyckel till tjänstens huvud namn i Azure AD.
* Lagrar certifikatet i den lokala datorns certifikat arkiv.
* Beviljar åtkomst till certifikatets privata nyckel till nätverks användaren.
* Startar om NPS-tjänsten.

Om du inte vill använda dina egna certifikat (i stället för de självsignerade certifikat som PowerShell-skriptet genererar) kör du PowerShell-skriptet för att slutföra installationen av NPS-tillägget. Om du installerar tillägget på flera servrar bör varje server ha ett eget certifikat.

Om du vill tillhandahålla funktioner för belastnings utjämning eller för redundans upprepar du de här stegen på ytterligare NPS-servrar som du vill:

1. Öppna en Windows PowerShell-prompt som administratör.
1. Ändra kataloger till den plats där installations programmet skapade PowerShell-skriptet:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Kör PowerShell-skriptet som skapats av installations programmet.

   Du kan behöva först aktivera TLS 1,2 för PowerShell för att kunna ansluta och Hämta paket korrekt:
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > För kunder som använder Azure Government-eller Azure Kina 21Vianet-moln måste du först redigera `Connect-MsolService` cmdletarna i *AzureMfaNpsExtnConfigSetup.ps1* -skriptet för att inkludera *AzureEnvironment* -parametrarna för det moln som krävs. Ange till exempel *-AzureEnvironment USGovernment* eller *-AzureEnvironment AzureChinaCloud*.
   >
   > Mer information finns i [referens för Connect-MSOLService-parametern](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. När du uppmanas till det loggar du in på Azure AD som administratör.
1. PowerShell-prompt för klient-ID. Använd *klient-ID-* GUID som du kopierade från Azure Portal i avsnittet krav.
1. Ett meddelande visas när skriptet har slutförts.  

Om ditt tidigare dator certifikat har upphört att gälla och ett nytt certifikat har skapats, bör du ta bort eventuella utgångna certifikat. Certifikat som har upphört att gälla kan orsaka problem med att NPS-tillägget startar.

> [!NOTE]
> Om du använder egna certifikat i stället för att skapa certifikat med PowerShell-skriptet ser du till att de överensstämmer med namngivnings konventionen för NPS. Ämnes namnet måste vara **CN = \<TenantID\> , OU = Microsoft NPS-tillägg**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Ytterligare steg för Microsoft Azure Government eller Azure Kina 21Vianet

För kunder som använder Azure Government-eller Azure Kina 21Vianet-moln krävs följande ytterligare konfigurations steg på varje NPS-server.

> [!IMPORTANT]
> Konfigurera bara de här register inställningarna om du är en Azure Government eller Azure Kina 21Vianet-kund.

1. Om du är en Azure Government eller Azure Kina 21Vianet-kund öppnar du **Registereditorn** på NPS-servern.
1. Navigera till `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Ange följande nyckel värden för Azure Government kunder:

    | Registernyckel       | Värde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. För Azure Kina 21Vianet-kunder anger du följande nyckel värden:

    | Registernyckel       | Värde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Upprepa föregående två steg för att ange register nyckel värden för varje NPS-server.
1. Starta om NPS-tjänsten för varje NPS-server.

    För minimal påverkan tar du bort varje NPS-server från NLB-rotationen en i taget och väntar på att alla anslutningar ska tömmas.

### <a name="certificate-rollover"></a>Certifikat förnyelse

Med version *1.0.1.32* av NPS-tillägget stöds nu läsning av flera certifikat. Den här funktionen hjälper till att under lätta rullandea certifikat uppdateringar innan de upphör att gälla. Om din organisation kör en tidigare version av NPS-tillägget uppgraderar du till version *1.0.1.32* eller högre.

Certifikat som skapats av `AzureMfaNpsExtnConfigSetup.ps1` skriptet är giltiga i två år. Övervaka certifikat för förfallo datum. Certifikat för NPS-tillägget placeras i den *lokala datorns* certifikat Arkiv under *personligt* och *utfärdas till* klient-ID: t för installations skriptet.

När ett certifikat närmar sig förfallo datumet ska ett nytt certifikat skapas för att ersätta det.  Den här processen åstadkommer du genom att köra `AzureMfaNpsExtnConfigSetup.ps1` igen och behålla samma klient-ID när du uppmanas till det. Den här processen bör upprepas på varje NPS-server i din miljö.

## <a name="configure-your-nps-extension"></a>Konfigurera ditt NPS-tillägg

Med din miljö för beredd och NPS-tillägget nu installerat på de servrar som krävs kan du konfigurera tillägget.

Det här avsnittet innehåller design överväganden och förslag för lyckade distributioner av NPS-tillägg.

### <a name="configuration-limitations"></a>Konfigurations begränsningar

- NPS-tillägget för Azure AD Multi-Factor Authentication innehåller inte verktyg för att migrera användare och inställningar från MFA server till molnet. Därför rekommenderar vi att du använder tillägget för nya distributioner i stället för en befintlig distribution. Om du använder tillägget för en befintlig distribution måste användarna göra ett korrektur och fylla i sina MFA-uppgifter i molnet.  
- NPS-tillägget använder UPN från den lokala AD DS-miljön för att identifiera användaren på Azure AD Multi-Factor Authentication för att utföra den sekundära autentiseringen. Tillägget kan konfigureras för att använda en annan identifierare, t. ex. alternativt inloggnings-ID eller anpassat AD DS-fält än UPN. Mer information finns i artikeln [avancerade konfigurations alternativ för NPS-tillägget för Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Alla krypterings protokoll stöder inte alla verifierings metoder.
   - **PAP** stöder telefonsamtal, envägs textmeddelande, mobilapp och verifierings kod för mobilapp
   - **CHAPv2** -och **EAP** -support för telefonsamtal och aviseringar för mobilapp

### <a name="control-radius-clients-that-require-mfa"></a>Kontrol lera RADIUS-klienter som kräver MFA

När du aktiverar MFA för en RADIUS-klient med hjälp av NPS-tillägget, krävs alla autentiseringar för den här klienten för att utföra MFA. Om du vill aktivera MFA för vissa RADIUS-klienter, men inte andra, kan du konfigurera två NPS-servrar och installera tillägget på bara en av dem.

Konfigurera RADIUS-klienter som du vill kräva MFA för att skicka begär anden till NPS-servern som kon figurer ATS med tillägget, och andra RADIUS-klienter till NPS-servern som inte har kon figurer ATS med tillägget.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Förbereda för användare som inte har registrerats för MFA

Om du har användare som inte är registrerade för MFA kan du bestämma vad som händer när de försöker autentisera sig. Om du vill styra det här beteendet använder du inställningen *REQUIRE_USER_MATCH* i register Sök vägen *HKLM\Software\Microsoft\AzureMFA*. Den här inställningen har ett enda konfigurations alternativ:

| Tangent | Värde | Standardvärde |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Inte angivet (motsvarar sant) |

Den här inställningen avgör vad som ska göras när en användare inte har registrerats för MFA. Om nyckeln inte finns, eller har angetts till *True*, och användaren inte är registrerad, Miss lyckas inte MFA-utmaningen av tillägget.

När nyckeln har angetts till *false* och användaren inte är registrerad, fortsätter autentiseringen utan att utföra MFA. Om en användare har registrerats i MFA måste de autentiseras med MFA även om *REQUIRE_USER_MATCH* har angetts till *false*.

Du kan välja att skapa den här nyckeln och ange den som *falsk* när dina användare registrerar sig, och de kanske inte alltid registreras för Azure AD Multi-Factor Authentication ännu. Men eftersom om du anger nyckeln tillåter användare som inte har registrerats för MFA för att logga in, bör du ta bort den här nyckeln innan du kommer till produktionen.

## <a name="troubleshooting"></a>Felsökning

### <a name="nps-extension-health-check-script"></a>Skript för hälso kontroll av NPS-tillägg

Följande skript är tillgängligt för att utföra grundläggande hälso kontrolls steg när du felsöker NPS-tillägget.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hur gör jag för att verifiera att klient certifikatet är installerat som det ska?

Leta efter det självsignerade certifikatet som skapats av installations programmet i certifikat arkivet och kontrol lera att den privata nyckeln har behörighet för användar *nätverks tjänsten*. Certifikatet har ämnes namnet **CN \<tenantid\> , OU = Microsoft NPS-tillägg**

Självsignerade certifikat som genereras av `AzureMfaNpsExtnConfigSetup.ps1` skriptet har en giltighets tid på två år. När du verifierar att certifikatet har installerats bör du också kontrol lera att certifikatet inte har upphört att gälla.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Hur kan jag kontrol lera att mitt klient certifikat är kopplat till min klient organisation i Azure AD?

Öppna PowerShell-Kommandotolken och kör följande kommandon:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Dessa kommandon skriver ut alla certifikat som associerar din klient organisation med din instans av NPS-tillägget i din PowerShell-session. Sök efter ditt certifikat genom att exportera klient certifikatet som en *Base-64-kodad X. 509-fil (. cer)* utan den privata nyckeln och jämför den med listan från PowerShell.

Följande kommando skapar en fil med namnet *npscertificate* i roten på din *C:* enhet i formatet *. cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

När du har kört kommandot går du till roten på enheten *C:* , letar upp filen och dubbelklickar på den. Gå till information och rulla ned till "tumavtryck". Jämför tumavtrycket för det certifikat som är installerat på servern. Certifikatets tumavtrycken bör överensstämma.

*Giltig – från* och *giltig – tills* tidsstämplar, som är i läslig form, kan användas för att filtrera ut uppenbara felaktigheter om kommandot returnerar mer än ett certifikat.

### <a name="why-cannot-i-sign-in"></a>Varför kan jag inte logga in?

Kontrol lera att ditt lösen ord inte har gått ut. NPS-tillägget stöder inte ändring av lösen ord som en del av inloggnings arbets flödet. Kontakta din organisations IT-personal om du behöver ytterligare hjälp.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Varför fungerar inte mina begär Anden med ADAL-token-fel?

Det här felet kan bero på en av flera olika orsaker. Använd följande steg för att felsöka:

1. Starta om NPS-servern.
2. Kontrol lera att klient certifikatet är installerat som förväntat.
3. Kontrol lera att certifikatet är kopplat till din klient organisation i Azure AD.
4. Kontrollera att `https://login.microsoftonline.com/` kan nås från servern som kör tillägget.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Varför Miss känner autentiseringen med ett fel i HTTP-loggar som anger att användaren inte hittas?

Kontrol lera att AD Connect körs och att användaren finns både i den lokala AD DS-miljön och i Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Varför visas HTTP Connect-fel i loggar med alla mina autentiseringar som inte fungerar?

Kontrollera att https://adnotifications.windowsazure.com kan nås från servern som kör NPS-tillägget.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Varför fungerar inte autentisering, trots att ett giltigt certifikat finns?

Om ditt tidigare dator certifikat har upphört att gälla och ett nytt certifikat har genererats, tar du bort eventuella utgångna certifikat. Utgångna certifikat kan orsaka problem med att NPS-tillägget startar.

Kontrol lera att du har ett giltigt certifikat genom att kontrol lera det lokala *dator kontots certifikat Arkiv* med MMC och se till att certifikatet inte har passerat förfallo datumet. Om du vill generera ett nytt giltigt certifikat kör du om stegen från [köra PowerShell-skriptet för installations programmet](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Varför visas ignorerade begär anden i NPS-server loggarna?

En VPN-server kan skicka upprepade begär anden till NPS-servern om timeout-värdet är för lågt. NPS-servern identifierar dessa dubbla begär Anden och tar bort dem. Det här beteendet är avsiktligt, och indikerar inte ett problem med NPS-servern eller Azure AD Multi-Factor Authentication NPS-tillägget.

Mer information om varför du ser ignorerade paket i NPS-serverns loggar finns i [RADIUS-protokollets beteende och NPS-tillägget](#radius-protocol-behavior-and-the-nps-extension) i början av den här artikeln.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Hantering av TLS/SSL-protokoll och chiffersviter

Vi rekommenderar att äldre och svagare chiffersviter är inaktiverade eller tas bort om organisationen inte kräver det. Information om hur du kan slutföra den här uppgiften finns i artikeln [Hantera SSL/TLS-protokoll och chiffersviter för AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Ytterligare felsökning

Ytterligare fel söknings vägledning och möjliga lösningar finns i artikeln [Lös fel meddelanden från NPS-tillägget för Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt och konfiguration av nätverks princip server i Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Konfigurera alternativa ID: n för inloggning eller konfigurera en undantags lista för IP-adresser som inte utför tvåstegsverifiering i [avancerade konfigurations alternativ för NPS-tillägget för Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Lär dig att integrera [Fjärrskrivbordsgateway](howto-mfa-nps-extension-rdg.md) och [VPN-servrar](howto-mfa-nps-extension-vpn.md) med hjälp av NPS-tillägget

- [Lös fel meddelanden från NPS-tillägget för Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
