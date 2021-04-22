---
title: Vad är nytt i Windows Virtual Desktop? – Azure
description: Nya funktioner och produktuppdateringar för Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 2712115f19c7cc64a0475061e134d6be6de5d1ca
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867411"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Vad är nytt i Windows Virtual Desktop?

Windows Virtual Desktop uppdateras regelbundet. I den här artikeln får du veta mer om:

- De senaste uppdateringarna
- Nya funktioner
- Förbättringar av befintliga funktioner
- Felkorrigeringar

Den här artikeln uppdateras varje månad. Kom tillbaka hit ofta för att hålla koll på nya uppdateringar.

## <a name="client-updates"></a>Klientuppdateringar

Kolla in de här artiklarna om du vill veta mer om uppdateringar för våra Windows Virtual Desktop och Fjärrskrivbordstjänster:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Webb](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows Virtual Desktop agentuppdateringar

Den Windows Virtual Desktop agenten uppdateras minst en gång per månad.

Här är vad som har ändrats i Windows Virtual Desktop Agent:

- Version 1.0.2990.800: Den här uppdateringen släpptes 13 april 2021 och har följande ändringar:
    - Uppdaterade agentfelmeddelanden.
    - Lägger till ett undantag som förhindrar att du installerar icke-Windows 7-agenter på virtuella Windows 7-datorer.
    - Har uppdaterat logiken för pulsslagstjänsten.
- Version 1.0.2944.1400: Den här uppdateringen släpptes den 7 april 2021 och har följande ändringar:
    - Länkar till felsökningsguiden Windows Virtual Desktop agent i loggbokens loggar för agentfel.
    - Ytterligare ett undantag har lagts till för bättre felhantering.
    - Lade till WVDAgentUrlTool.exe som gör att kunderna kan kontrollera vilka url:er som de har åtkomst till.
- Version 1.0.2866.1500: Den här uppdateringen släpptes den 26 mars 2021 och åtgärdar ett problem med stackhälsokontrollen.
- Version 1.0.2800.2802: Den här uppdateringen släpptes den 10 mars 2021 och innehåller allmänna förbättringar och felkorrigeringar.
- Version 1.0.2800.2800: Den här uppdateringen släpptes den 2 mars 2021 och åtgärdar ett problem med omvänd anslutning.
- Version 1.0.2800.2700: Den här uppdateringen släpptes den 10 februari 2021 och innehåller allmänna förbättringar och felkorrigeringar.
- Version 1.0.2800.2700: Den här uppdateringen släpptes den 4 februari 2021 och åtgärdar ett problem med nekad åtkomstorkestrering.

## <a name="fslogix-updates"></a>FSLogix-uppdateringar

Är du nyfiken på de senaste uppdateringarna för FSLogix? Kolla in [Vad är nytt på FSLogix](/fslogix/whats-new).

## <a name="march-2021"></a>Mars 2021

Det här är vad som ändrades i mars 2021.

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Uppdateringar av Azure Portal användargränssnitt för Windows Virtual Desktop

Vi har gjort följande uppdateringar för att Windows Virtual Desktop för Azure Portal:

- Vi har aktiverat nya tillgänglighetsalternativ (tillgänglighetsuppsättning och zoner) för arbetsflödena för att skapa värdpooler och lägga till virtuella datorer.
- Vi har åtgärdat ett problem där en värd med statusen "Behöver hjälp" visades som otillgänglig. Nu har värden en varningsikon bredvid sig.
- Vi har aktiverat sortering för aktiva sessioner.
- Du kan nu skicka meddelanden till eller logga ut specifika användare på fliken värdinformation.
- Vi har ändrat fältet för maximal sessionsgräns.
- Vi har lagt till en verifieringssökväg för organisationsenheten i arbetsflödet för att skapa en värdpool.
- Du kan nu använda den senaste versionen av Windows 10 när du skapar en personlig värdpool.

### <a name="generation-2-images-and-trusted-launch"></a>Generation 2-avbildningar och betrodd start

Nu Azure Marketplace generation 2-avbildningar för Windows 10 Enterprise och Windows 10 Enterprise flera sessioner. Med dessa avbildningar kan du använda betrodda starta virtuella datorer. Läs mer om virtuella datorer i generation 2 på Ska jag skapa en virtuell dator av [första eller andra generationen.](../virtual-machines/generation-2.md) Information om hur du etablerar Windows Virtual Desktop trusted launch för virtuella datorer finns i [vårt TechCommunity-inlägg.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix är nu förinstallerat på Windows 10 Enterprise avbildningar för flera sessioner

Baserat på kundfeedback har vi konfigurerat en ny version av Windows 10 Enterprise-avbildningen för flera sessioner som redan har en okonfigurerad version av FSLogix installerad. Vi hoppas att detta gör Windows Virtual Desktop distributionen enklare.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor för Windows Virtual Desktop är nu allmänt tillgänglig

Azure Monitor för Windows Virtual Desktop är nu allmänt tillgänglig för allmänheten. Den här funktionen är en automatiserad tjänst som övervakar dina distributioner och låter dig visa händelser, hälsa och felsökningsförslag på en och samma plats. Mer information finns i [vår dokumentation eller](azure-monitor.md) i vårt [TechCommunity-inlägg.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>Uppdateringar mars 2021 för Teams på Windows Virtual Desktop

Vi har gjort följande uppdateringar för Teams på Windows Virtual Desktop:

- Vi har förbättrat videokvalitetsprestanda för anrop och 2x2-läge.
- Vi har minskat processoranvändningen med 5–10 % (beroende på cpu-generering) genom att använda maskinvaru-avlastning av videobearbetning (XVP).
- Äldre datorer kan nu använda XVP och maskinvaruavkodning för att visa fler inkommande videoströmmar smidigt i 2x2-läge.
- Vi har uppdaterat WebRTC-stacken från M74 till M88 för bättre AV-synkroniseringsprestanda och färre tillfälliga problem.
- Vi har ersatt vår programvara H264-kodare med OpenH264 (OSS som används i Teams på webben), vilket ökade videokvaliteten för den utgående kameran.
- Vi aktiverade 2x2-läge för Teams Server för allmänheten den 30 mars. 2x2-läge visar upp till fyra inkommande videoströmmar samtidigt.

### <a name="start-vm-on-connect-public-preview"></a>Starta virtuell dator på Anslut offentlig förhandsversion

Den nya inställningen för värdpoolen, Starta virtuell dator på Anslut, är nu tillgänglig i offentlig förhandsversion. Med den här inställningen kan du aktivera dina virtuella datorer när du behöver dem. Om du vill spara kostnader måste du allokera dina virtuella datorer genom att konfigurera Azure Compute inställningar. Mer information finns i [blogginlägget och](https://aka.ms/wvdstartvmonconnect) [i vår dokumentation.](start-virtual-machine-connect.md)

### <a name="windows-virtual-desktop-specialty-certification"></a>Windows Virtual Desktop Specialcertifiering

Vi har släppt en betaversion av AZ-140-provet som gör att du kan bevisa dina kunskaper inom Windows Virtual Desktop i Azure. Mer information finns i [techcommunity-inlägget](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107).

## <a name="february-2021"></a>Februari 2021

Det här är vad som ändrades i februari 2021.

### <a name="portal-experience"></a>Portalmiljö

Vi har förbättrat Azure Portal på följande sätt:

- Masstömningsläge på värdar på rutnätsfliken för sessionsvärdar. 
- MSIX-app attach är nu tillgängligt för offentlig förhandsversion.
- Översiktsinformation för värdpoolen för mörkt läge har åtgärdats.

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU-metadatalagring nu i offentlig förhandsversion

Vi är nu värd för en offentlig förhandsversion av geografin Europa (EU) som ett lagringsalternativ för tjänstmetadata i Windows Virtual Desktop. Kunder kan välja mellan Europa, västra eller Europa, norra när de skapar sina tjänstobjekt. Tjänstobjekten och metadata för värdpoolerna lagras i azure-geografin som är associerad med varje region. Mer information finns i [vårt blogginlägg om den offentliga förhandsversionen.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Teams on Windows Virtual Desktop plugin-uppdateringar

Vi har förbättrat videosamtalskvaliteten i plugin-programmet Windows Virtual Desktop genom att åtgärda de vanligaste problemen, till exempel när skärmen plötsligt skulle bli mörk eller videon och ljudet desynkroniserats. Dessa förbättringar bör öka prestandan för en enskild videovy med aktiv talarväxling. Vi har också åtgärdat ett problem där maskinvaruenheter med specialtecken inte var tillgängliga i Teams.

## <a name="january-2021"></a>Januari 2021

Det här är vad som ändrades i januari 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nytt Windows Virtual Desktop erbjudande

Nya kunder sparar 30 procent på Windows Virtual Desktop datorkostnader för virtuella datorer i D-serien och Bs-serien i upp till 90 dagar när du använder den interna Microsoft-lösningen. Du kan lösa in det här erbjudandet Azure Portal den 31 mars 2021. Läs mer på Windows Virtual Desktop [sidan med erbjudanden.](https://azure.microsoft.com/services/virtual-desktop/offer/)

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules-värdeändring 

I den Azure Resource Manager kapslade mallen ändrade vi standardvärdet för networkSecurityGroupRules från ett objekt till en matris. Detta förhindrar eventuella fel om du använder managedDisks-customimagevm.jspå utan att ange ett värde för networkSecurityGroupRules. Det här var inte en bakåtkompatibel ändring och är bakåtkompatibel.

### <a name="fslogix-hotfix-update"></a>Uppdatering av snabbkorrigeringar för FSLogix

Vi har släppt FSLogix, version 2009 HF_01 (2.9.7654.46150) för att lösa problem i den tidigare versionen (2.9.7621.30127). Vi rekommenderar att du slutar använda den tidigare versionen och uppdaterar FSLogix så snart som möjligt.

Mer information finns i viktig information i [Vad är nytt i FSLogix.](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)

### <a name="azure-portal-experience-improvements"></a>Azure Portal upplevelseförbättringar

Vi har gjort följande förbättringar Azure Portal upplevelsen:

- Nu kan du lägga till autentiseringsuppgifter för lokal VM-administratör direkt i stället för att behöva lägga till ett lokalt konto som skapats med autentiseringsuppgifterna för domänkopplingskontot i Active Directory.
- Användare kan nu visa både enskilda tilldelningar och grupptilldelningar på separata flikar för enskilda användare och grupper.
- Versionsnumret för den virtuella Windows Virtual Desktop agenten visas nu i översikten över virtuella datorer för värdpooler.
- Massborttagning har lagts till för värdpooler och programgrupper.
- Du kan nu aktivera eller inaktivera tömningsläge för flera sessionsvärdar i en värdpool.
- Det offentliga IP-fältet har tagits bort från informationssidan för den virtuella datorn.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>felsökning Windows Virtual Desktop agent

Vi konfigurerade nyligen [felsökningsguiden Windows Virtual Desktop Agent för](troubleshoot-agent.md) att hjälpa kunder som har stött på vanliga problem.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender för slutpunktsintegrering

Integreringen av Microsoft Defender för slutpunkter är nu allmänt tillgänglig. Den här funktionen ger Windows Virtual Desktop virtuella datorer samma undersökningsupplevelse som en lokal Windows 10 dator. Om du använder Windows 10 Enterprise flera sessioner stöder Microsoft Defender för slutpunkt upp till 50 samtidiga användaranslutningar, vilket ger kostnadsbesparingar för Windows 10 Enterprise flera sessioner och förtroendet för Microsoft Defender för slutpunkt. Mer information finns i [blogginlägget](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure-säkerhetsbaslinje för Windows Virtual Desktop

Vi har nyligen publicerat [en artikel om Azures](security-baseline.md) säkerhetsbaslinje för Windows Virtual Desktop som vi vill uppmärksamma dig på. Dessa riktlinjer innehåller information om hur du tillämpar Azure Security Benchmark, version 2.0 på Windows Virtual Desktop. Azure Security Benchmark beskriver de inställningar och metoder som vi rekommenderar att du använder för att skydda dina molnlösningar i Azure.

## <a name="december-2020"></a>December 2020

Det här är vad som ändrades i december 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor för Windows Virtual Desktop

Den offentliga förhandsversionen för Azure Monitor för Windows Virtual Desktop är nu tillgänglig. Den här nya funktionen innehåller en robust instrumentpanel som byggts ovanpå Azure Monitor arbetsböcker för att hjälpa IT-proffs att förstå Windows Virtual Desktop miljöer. Mer information [finns i tillkännagivandet på](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) vår blogg. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager ändra mall 

I den senaste uppdateringen har vi tagit bort alla parametrar för offentliga IP-adresser från Azure Resource Manager mallen för att skapa och etablera värdpooler. Vi rekommenderar starkt att du undviker att använda offentliga IP-adresser Windows Virtual Desktop att skydda distributionen. Om distributionen är beroende av offentliga IP-adresser måste du konfigurera om den så att den använder privata IP-adresser i stället, annars fungerar inte distributionen korrekt.

### <a name="msix-app-attach-public-preview"></a>Bifoga offentlig förhandsversion av MSIX-appen 

MSIX-app attach är en annan tjänst som påbörjade sin offentliga förhandsversion den här månaden. MSIX-apptillämpning är en tjänst som dynamiskt presenterar MSIX-program för Windows Virtual Desktop Session Host VMs. Mer information [finns i tillkännagivandet på](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) vår blogg. 

### <a name="screen-capture-protection"></a>Skydd mot skärmdumpar 

Den här månaden markerade också början av den offentliga förhandsversionen för skydd av skärmdumpar. Du kan använda den här funktionen för att förhindra att känslig information fångas in på klientslutpunkterna. Prova att skydda skärmdumpar genom att gå till den [här sidan.](https://aka.ms/WVDScreenCaptureProtection)  

### <a name="built-in-roles"></a>Inbyggda roller

Vi har lagt till nya inbyggda roller för Windows Virtual Desktop för administratörsbehörigheter. Mer information finns i [Inbyggda roller för Windows Virtual Desktop](rbac.md). 

### <a name="application-group-limit-increase"></a>Gränsökning för programgrupp

Vi har ökat standardgränsen för programgrupper per klientorganisation Azure Active Directory till 200 grupper.

### <a name="client-updates-for-december-2020"></a>Klientuppdateringar för december 2020

Vi har släppt nya versioner av följande klienter: 

- Android
- macOS
- Windows

Mer information om klientuppdateringar finns i [Klientuppdateringar.](whats-new.md#client-updates)

## <a name="november-2020"></a>November 2020

### <a name="azure-portal-experience"></a>Azure Portal upplevelse

Vi har åtgärdat två buggar i Azure Portal användarupplevelsen:

- Det egna namnet på skrivbordsprogrammet skrivs inte längre över i arbetsflödet "Lägg till virtuell dator".
- Sessionsvärdfliken läses nu in om sessionsvärdar ingår i skalningsuppsättningar.

### <a name="fslogix-client-version-2009"></a>FSLogix-klient, version 2009 

Vi har släppt en ny version av FSLogix-klienten med många korrigeringar och förbättringar. Läs mer [i vårt blogginlägg](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Offentlig förhandsversion av RDP Shortpath

RDP Shortpath introducerar direktanslutning till din Windows Virtual Desktop-sessionsvärd med punkt-till-plats- och plats-till-plats-VPN:er och ExpressRoute. Den introducerar även TRANSPORTPROTOKOLLET URCP. RDP Shortpath är utformat för att minska svarstider och nätverkshopp för att förbättra användarupplevelsen. Läs mer på [Windows Virtual Desktop RDP Shortpath](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, version 2.0.1

Vi har släppt version 2.0.1 av Windows Virtual Desktop cmdlets. Den här uppdateringen innehåller cmdlets som gör att du kan hantera MSIX-app bifoga. Du kan ladda ned den nya versionen [i PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Azure Advisor uppdateringar

Azure Advisor har nu en ny rekommendation för närhetsvägledning i Windows Virtual Desktop och en ny rekommendation för att optimera prestanda i djup-först-belastningsutjämnad värdpooler. Läs mer på [Azure-webbplatsen](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Oktober 2020

Det här är vad som ändrades i oktober 2020:

### <a name="improved-performance"></a>Förbättrade prestanda

- Vi har optimerat prestandan genom att minska anslutningsfördröjningen i följande Geografiska områden i Azure:
    - Schweiz
    - Kanada

Nu kan du använda [upplevelseberäknaren för att](https://azure.microsoft.com/services/virtual-desktop/assessment/) uppskatta kvaliteten på användarupplevelsen inom dessa områden.

### <a name="azure-government-cloud-availability"></a>Azure Government molntillgänglighet

Den Azure Government Cloud är nu allmänt tillgänglig. Läs mer [i vårt blogginlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure Portal uppdateringar

Vi har gjort några uppdateringar av Windows Virtual Desktop Azure Portal:

- Ett resourceID-fel som hindrade användare från att öppna fliken Sessioner har åtgärdats.
- Effektiviserat användargränssnittet på fliken "Sessionsvärdar".
- Inställningarna "Defaults", "Usability" (Användbarhet) och "Restore defaults" (Återställ standardvärden) har åtgärdats under RDP-egenskaper.
- Funktionerna "Ta bort" och "Ta bort" är konsekventa på alla flikar.
- Portalen validerar nu appnamn i arbetsflödet "Lägg till en app".
- Åtgärdat ett problem där sessionsvärdens exportdata inte justerades i kolumnerna.
- Åtgärdat ett problem där portalen inte kunde hämta användarsessioner.
- Åtgärdat ett problem vid hämtning av sessionsvärd som inträffade när den virtuella datorn skapades i en annan resursgrupp.
- Fliken Sessionsvärd har uppdaterats för att visa både aktiva och frånkopplade sessioner.
- Fliken "Program" innehåller nu sidor.
- Åtgärdat ett problem där texten "kräver kommandorad" inte visades korrekt på fliken "Programlista".
- Ett problem har åtgärdats när portalen inte kunde distribuera värdpooler eller virtuella datorer när den tyska versionen av Shared Image Gallery.

### <a name="client-updates-for-october-2020"></a>Klientuppdateringar för oktober 2020

Vi har släppt nya versioner av klienterna. Mer information finns i de här artiklarna:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Mer information om de andra klienterna finns i [Klientuppdateringar.](#client-updates)

## <a name="september-2020"></a>September 2020

Det här är vad som ändrades i september 2020:

- Vi har optimerat prestandan genom att minska anslutningsfördröjningen i följande Azure-geografiska områden:
    - Tyskland
    - Sydafrika (endast för valideringsmiljöer)

Nu kan du använda Experience [Estimator för att](https://azure.microsoft.com/services/virtual-desktop/assessment/) uppskatta kvaliteten på användarupplevelsen inom dessa områden.

- Vi släppte version 1.2.1364 av Windows Desktop-klienten för Windows Virtual Desktop. I den här uppdateringen har vi gjort följande ändringar:
    - Åtgärdat ett problem där enkel inloggning (SSO) inte fungerade i Windows 7.
    - Åtgärdat ett problem som gjorde att klienten kopplas från när en användare som aktiverade medieoptimering för Teams försökte anropa eller ansluta till ett Teams-möte medan en annan app hade en ljudström öppen i exklusivt läge.
    - Åtgärdat ett problem där Teams inte räknade upp ljud- eller videoenheter när medieoptimering för Teams aktiverades.
    - Lade till ett "Behöver du hjälp med inställningar?" länk till sidan för skrivbordsinställningar.
    - Åtgärdat ett problem med knappen "Prenumerera" som inträffade när du använde mörka högkontrastteman.
    
- Tack vare den enorma hjälpen från våra användare har vi åtgärdat två viktiga problem för Microsoft Store fjärrskrivbordsklienten. Vi fortsätter att granska feedback och åtgärda problem när vi utökar vår fasade version av klienten till fler användare över hela världen.
    
- Vi har lagt till en ny funktion som gör att du kan ändra vm-plats, avbildning, resursgrupp, prefixnamn, nätverkskonfiguration som en del av arbetsflödet för att lägga till en virtuell dator i distributionen i Azure Portal.

- IT-proffs kan nu hantera hybridbaserade Azure Active Directory-Windows 10 Enterprise virtuella datorer med Microsoft Endpoint Manager. Mer information finns i [vårt blogginlägg](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Augusti 2020

Det här är vad som ändrades i augusti 2020:

- Vi har förbättrat prestandan för att minska anslutningsfördröjningen i följande Azure-regioner: 

    - Storbritannien
    - Frankrike
    - Norge
    - Sydkorea

   Du kan använda [Experience Estimator för att](https://azure.microsoft.com/services/virtual-desktop/assessment/) få en allmän uppfattning om hur dessa ändringar påverkar användarna.

- Den Microsoft Store fjärrskrivbordsklienten (v10.2.1522+) är nu allmänt tillgänglig! Den här versionen av Microsoft Store fjärrskrivbordsklienten är kompatibel med Windows Virtual Desktop. Vi har också introducerat uppdaterade UI-flöden för bättre användarupplevelser. Den här uppdateringen innehåller flytande design, ljusa och mörka lägen och många andra spännande ändringar. Vi har också skrivit om klienten så att den använder samma underliggande RDP-motor (Remote Desktop Protocol) som iOS-, macOS- och Android-klienterna. På så sätt kan vi leverera nya funktioner snabbare på alla plattformar. [Ladda ned klienten](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) och prova!

- Vi har åtgärdat ett problem i Teams Desktop-klienten (version 1.3.00.21759) där klienten endast visade UTC-tidszonen i chatt, kanaler och kalender. Den uppdaterade klienten visar nu fjärrsessionens tidszon i stället.

- Azure Advisor är nu en del av Windows Virtual Desktop. När du använder Windows Virtual Desktop via Azure Portal kan du se rekommendationer för att optimera din Windows Virtual Desktop miljö. Läs mer på [Azure Advisor](azure-advisor.md).

- Azure CLI stöder nu Windows Virtual Desktop ( `az desktopvirtualization` ) som hjälper dig att automatisera Windows Virtual Desktop distributioner. Kolla in [desktopvirtualization för](/cli/azure/) en lista över tilläggskommandon.

- Vi har uppdaterat våra distributionsmallar så att de är helt kompatibla med Windows Virtual Desktop Azure Resource Manager gränssnitten. Du hittar mallarna på [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Portalen Windows Virtual Desktop US Gov nu i offentlig förhandsversion. Mer information finns i [vårt meddelande](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Juli 2020  

Juli var när Windows Virtual Desktop med Azure Resource Management-integrering blev allmänt tillgänglig.

Det här är vad som har ändrats i den här nya versionen: 

- "Fall 2019-versionen" kallas nu "Windows Virtual Desktop (klassisk)," medan "Spring 2020-versionen" nu bara är "Windows Virtual Desktop". Mer information finns i det [här blogginlägget.](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/) 

Mer information om nya funktioner finns i det [här blogginlägget.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) 

### <a name="autoscaling-tool-update"></a>Uppdatering av autoskalningsverktyg

Den senaste versionen av verktyget för automatisk skalning som fanns i förhandsversionen är nu allmänt tillgänglig. Det här verktyget använder ett Azure Automation-konto och Azure Logic App för att automatiskt stänga av och starta om sessionsvärdar (VM) i en värdpool, vilket minskar infrastrukturkostnaderna. Läs mer i [Skala sessionsvärdar med hjälp av Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Nu kan du göra följande med Azure Portal i Windows Virtual Desktop: 

- Tilldela användare direkt till personliga värdar för skrivbordssessioner  
- Ändra inställningen för valideringsmiljön för värdpooler 

### <a name="diagnostics"></a>Diagnostik

Vi har släppt några nya fördefinierade frågor för Log Analytics-arbetsytan. Om du vill komma åt frågorna går du **till Loggar** och under **Kategori** väljer **du Windows Virtual Desktop**. Läs mer i [Använda Log Analytics för diagnostikfunktionen](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Uppdatering för Fjärrskrivbordsklient för Android

[Fjärrskrivbordsklienten för Android stöder](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) nu Windows Virtual Desktop anslutningar. Från och med version 10.0.7 har Android-klienten ett nytt användargränssnitt för bättre användarupplevelse. Klienten kan också integreras med Microsoft Authenticator Android-enheter för att aktivera villkorlig åtkomst när du prenumererar på Windows Virtual Desktop arbetsytor.  

Den tidigare versionen av fjärrskrivbordsklienten heter nu "Fjärrskrivbord 8". Alla befintliga anslutningar som du har i den tidigare versionen av klienten överförs sömlöst till den nya klienten. Den nya klienten har skrivits om till samma underliggande RDP-kärnmotor som iOS- och macOS-klienterna, vilket ger snabbare lansering av nya funktioner på alla plattformar. 

### <a name="teams-update"></a>Teams-uppdatering

Vi har gjort förbättringar i Microsoft Teams för Windows Virtual Desktop. Det viktigaste är Windows Virtual Desktop stöder ljud- och videooptimering för Windows Desktop-klienten. Omdirigering förbättrar svarstiden genom att skapa direkta sökvägar mellan användare när de använder ljud eller video i samtal och möten. Mindre avstånd innebär färre hopp, vilket gör att anrop ser ut och låter jämnare. Läs mer i [Använda Teams på Windows Virtual Desktop](teams-on-wvd.md).

## <a name="june-2020"></a>Juni 2020

Förra månaden introducerade vi Windows Virtual Desktop med Azure Resource Manager integrering i förhandsversion. Den här uppdateringen innehåller många spännande nya funktioner som vi gärna berättar om. Här är vad som är nytt för den här versionen av Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop är nu integrerad med Azure Resource Manager

Windows Virtual Desktop är nu integrerad i Azure Resource Manager. I den senaste uppdateringen är alla Windows Virtual Desktop objekt nu Azure Resource Manager resurser. Den här uppdateringen är också integrerad med rollbaserad åtkomstkontroll i Azure (Azure RBAC). Se [Vad är Azure Resource Manager? om](../azure-resource-manager/management/overview.md) du vill veta mer.

Så här ser den här ändringen ut för dig:

- Windows Virtual Desktop är nu integrerad med Azure Portal. Det innebär att du kan hantera allt direkt i portalen, utan PowerShell, webbappar eller verktyg från tredje part som krävs. Kom igång genom att läsa vår självstudie i [Skapa en värdpool med Azure Portal](create-host-pools-azure-marketplace.md).

- Före den här uppdateringen kunde du bara publicera RemoteApps och Desktops till enskilda användare. Med Azure Resource Manager kan du nu publicera resurser till Azure Active Directory grupper.

- Den tidigare versionen av Windows Virtual Desktop hade fyra inbyggda administratörsroller som du kunde tilldela till en klientorganisation eller värdpool. Dessa roller finns nu i [rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/overview.md) Du kan tillämpa dessa roller på varje Windows Virtual Desktop Azure Resource Manager objekt, vilket gör att du kan ha en fullständig, omfattande delegeringsmodell.

- I den här uppdateringen behöver du inte längre köra Azure Marketplace GitHub-mallen upprepade gånger för att expandera en värdpool. Allt du behöver för att expandera en värdpool är att gå till värdpoolen i Azure Portal och välja **+** Lägg till för att distribuera ytterligare sessionsvärdar.

- Distributionen av värdpoolen är nu helt integrerad [med Azure Shared Image Gallery](../virtual-machines/shared-image-galleries.md). Shared Image Gallery är en separat Azure-tjänst som lagrar vm-avbildningsdefinitioner, inklusive versionshantering av avbildningar. Du kan också använda global replikering för att kopiera och skicka avbildningar till andra Azure-regioner för lokal distribution.

- Övervakningsfunktioner som tidigare gjordes via PowerShell eller diagnostics Service-webbappen har nu flyttats till Log Analytics i Azure Portal. Nu har du också två alternativ för att visualisera dina rapporter. Du kan köra Kusto-frågor och använda Arbetsböcker för att skapa visuella rapporter.

- Du behöver inte längre slutföra ditt Azure Active Directory (Azure AD)-medgivande för att använda Windows Virtual Desktop. I den här uppdateringen autentiserar Azure AD-klienten i din Azure-prenumeration dina användare och tillhandahåller Azure RBAC-kontroller för dina administratörer.

### <a name="powershell-support"></a>Stöd för PowerShell

Vi har lagt till nya AzWvd-cmdlets i Azure PowerShell Az Module med den här uppdateringen. Den här nya modulen stöds i PowerShell Core som körs på .NET Core.

Installera modulen genom att följa anvisningarna i [Konfigurera PowerShell-modulen för Windows Virtual Desktop](powershell-module.md).

Du kan också se en lista över tillgängliga kommandon i [PowerShell-referensen för AzWvd.](/powershell/module/az.desktopvirtualization/#desktopvirtualization)

Mer information om de nya funktionerna finns i [vårt blogginlägg](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Ytterligare gatewayer

Vi har lagt till ett nytt gatewaykluster i Sydafrika för att minska svarstiden för anslutningar.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams på Windows Virtual Desktop (förhandsversion)

Vi har gjort några förbättringar i Microsoft Teams för Windows Virtual Desktop. Det viktigaste är att Windows Virtual Desktop stöder omdirigering av ljud och visuellt objekt för anrop. Omdirigering förbättrar svarstiden genom att skapa direkta sökvägar mellan användare när de anropar med hjälp av ljud eller video. Mindre avstånd innebär färre hopp, vilket gör att anrop ser ut och låter jämnare.

Mer information finns i [vårt blogginlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om framtida planer [Microsoft 365 Windows Virtual Desktop översikten](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
