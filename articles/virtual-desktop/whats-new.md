---
title: Vad är nytt i det virtuella Windows-skrivbordet? – Azure
description: Nya funktioner och produkt uppdateringar för virtuella Windows-datorer.
author: Heidilohr
ms.topic: overview
ms.date: 03/20/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: acb9b6d9d2080c63243ec4b924d07a927d6ccdfd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774447"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Vad är nytt i det virtuella Windows-skrivbordet?

Uppdateringar av virtuella Windows-datorer regelbundet. I den här artikeln får du lära dig att:

- De senaste uppdateringarna
- Nya funktioner
- Förbättringar av befintliga funktioner
- Felkorrigeringar

Den här artikeln uppdateras varje månad. Se till att kontrol lera igen ofta för att hålla dig uppdaterad med nya uppdateringar.

## <a name="client-updates"></a>Klient uppdateringar

Ta en titt på de här artiklarna om du vill veta mer om uppdateringar för våra klienter för virtuella Windows-datorer och Fjärrskrivbordstjänster:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Webb](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Uppdateringar av Windows Virtual Desktop agent

Windows-agenten för virtuella Skriv bords uppdateringar minst en gång per månad.

Här är what's ändrat i Windows Virtual Desktop-agenten:

- Version 1.0.2800.2800: den här uppdateringen släpptes i mars 2021 och korrigerade ett problem med omvänd anslutning.
- Version 1.0.2800.2700: den här uppdateringen släpptes i februari 2021 och åtgärdades problem vid nekad åtkomst.

## <a name="fslogix-updates"></a>FSLogix uppdateringar

Undrar du över de senaste uppdateringarna för FSLogix? Kolla [vad som är nytt på FSLogix](/fslogix/whats-new).

## <a name="february-2021"></a>Februari 2021

Det här är vad som har ändrats i februari 2021.

### <a name="portal-experience"></a>Portalmiljö

Vi har förbättrat Azure Portal upplevelsen på följande sätt:

- Mass tömnings läge på värdar på fliken rutnät i sessions värd. 
- MSIX app Attach är nu tillgängligt för offentlig för hands version.
- Översikts information för den fasta poolen för värdar för mörkt läge.

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU metadata Storage nu i offentlig för hands version

Vi är nu värd för en offentlig för hands version av Europe-geografin (EU) som ett lagrings alternativ för tjänstens metadata i det virtuella Windows-skrivbordet. Kunder kan välja mellan väst eller Nord Europa när de skapar sina tjänst objekt. Tjänst objekt och metadata för värdbaserade pooler kommer att lagras i den Azure-geografi som är kopplad till varje region. Läs det här blogg inlägget om du vill veta mer om [den offentliga för hands versionen](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Teams för uppdateringar av plugin-program för virtuella Windows-datorer

Vi har förbättrat video samtals kvaliteten på Windows-plugin-programmet för virtuella skriv bord genom att hantera de vanligaste problemen, till exempel när skärmen plötsligt blir mörk eller om video och ljud desynkroniseras. Dessa förbättringar bör öka prestandan för en enskild video-vy med aktiv högtalar växling. Vi har också åtgärdat ett problem där maskin varu enheter med specialtecken inte var tillgängliga i team.

## <a name="january-2021"></a>Januari 2021

Följande har ändrats i januari 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nytt Windows Virtual Desktop-erbjudande

Nya kunder sparar 30 procent på Windows-kostnader för virtuella Windows-datorer för virtuella datorer i D-serien och BS-serien i upp till 90 dagar med den ursprungliga Microsoft-lösningen. Du kan lösa in det här erbjudandet i Azure Portal före den 31 mars 2021. Läs mer på [sidan erbjudande för virtuella Windows-datorer](https://azure.microsoft.com/services/virtual-desktop/offer/).

### <a name="networksecuritygrouprules-value-change"></a>ändring av networkSecurityGroupRules-värde 

I den Azure Resource Manager kapslade mallen har vi ändrat standardvärdet för networkSecurityGroupRules från ett objekt till en matris. Detta förhindrar eventuella fel om du använder managedDisks-customimagevm.jspå utan att ange ett värde för networkSecurityGroupRules. Detta var inte en större ändring och är bakåtkompatibel.

### <a name="fslogix-hotfix-update"></a>Uppdatering av FSLogix Hotfix

Vi har lanserat FSLogix, version 2009 HF_01 (2.9.7654.46150) för att lösa problem i den tidigare versionen (2.9.7621.30127). Vi rekommenderar att du slutar använda den tidigare versionen och uppdaterar FSLogix så snart som möjligt.

Mer information finns i versions anteckningar i [Nyheter i FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150).

### <a name="azure-portal-experience-improvements"></a>Förbättringar i Azure Portal Experience

Vi har gjort följande förbättringar i Azure Portals upplevelsen:

- Du kan nu lägga till autentiseringsuppgifter för lokal administratör för virtuella datorer direkt i stället för att behöva lägga till ett lokalt konto som skapats med inloggnings kontots autentiseringsuppgifter för den Active Directory domänen.
- Användarna kan nu visa både enskilda och grupp tilldelningar på separata flikar för enskilda användare och grupper.
- Versions numret för Windows Virtual Desktop-agenten visas nu i den virtuella datorns översikt över värdar för pooler.
- Mass borttagning har lagts till för värdbaserade pooler och program grupper.
- Nu kan du aktivera eller inaktivera dränerings läge för flera värdbaserade sessioner i en adresspool.
- Tog bort fältet offentlig IP-adress från sidan information om virtuell dator.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Fel sökning av Windows Virtual Desktop-agent

Vi har nyligen konfigurerat [fel söknings guiden för Windows Virtual Desktop agent](troubleshoot-agent.md) för att hjälpa kunder som har påträffat vanliga problem.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender för slut punkts integrering

Microsoft Defender för slut punkts integrering är nu allmänt tillgänglig. Den här funktionen ger dina virtuella Windows-datorer samma gransknings upplevelse som en lokal Windows 10-dator. Om du använder Windows 10 Enterprise multi-session har Microsoft Defender för slut punkten stöd för upp till 50 samtidiga användar anslutningar, vilket ger dig kostnads besparingar för Windows 10 Enterprise multi-session och förtroendet för Microsoft Defender för slut punkt. Mer information finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azures säkerhets bas linje för Windows Virtual Desktop

Vi har nyligen publicerat [en artikel om Azures säkerhets bas linje](security-baseline.md) för det virtuella Windows-skrivbord som vi skulle vilja kalla din uppmärksamhet för. Dessa rikt linjer innehåller information om hur du tillämpar Azures säkerhets benchmark, version 2,0 till Windows Virtual Desktop. I Azures säkerhets benchmark beskrivs de inställningar och metoder som vi rekommenderar att du använder för att skydda dina moln lösningar på Azure.

## <a name="december-2020"></a>December 2020

Följande har ändrats i december 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor för virtuellt Windows-skrivbord

Den allmänt tillgängliga för hands versionen för Azure Monitor för virtuella Windows-datorer är nu tillgänglig. Den här nya funktionen innehåller en robust instrument Panel byggd ovanpå Azure Monitor arbets böcker som hjälper IT-proffs att förstå sina Windows-miljöer för virtuella datorer. Kolla [in meddelandet på vår blogg](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) för mer information. 

### <a name="azure-resource-manager-template-change"></a>Ändra Azure Resource Manager mal len 

I den senaste uppdateringen har vi tagit bort alla offentliga IP-adressprefix från Azure Resource Manager-mallen för att skapa och tillhandahålla värdar för pooler. Vi rekommenderar starkt att du undviker att använda offentliga IP-adresser för virtuella Windows-datorer för att skydda din distribution. Om distributionen förlitar sig på offentliga IP-adresser måste du konfigurera om den så att den använder privata IP-adresser i stället, annars fungerar inte distributionen som den ska.

### <a name="msix-app-attach-public-preview"></a>MSIX-app bifoga offentlig för hands version 

MSIX app Attach är en annan tjänst som började sin offentliga för hands version den här månaden. MSIX app Attach är en tjänst som dynamiskt presenterar MSIX-program på virtuella Windows-datorer för fjärrskrivbordssession. Kolla [in meddelandet på vår blogg](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) för mer information. 

### <a name="screen-capture-protection"></a>Skärm bilds skydd 

Den här månaden markerade även början av den offentliga för hands versionen för skärm dum skydd. Du kan använda den här funktionen för att förhindra att känslig information samlas in på klientens slut punkter. Ge skärm bilds skydd ett försök genom att gå till [den här sidan](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Inbyggda roller

Vi har lagt till nya inbyggda roller för Windows Virtual Desktop för administratörs behörighet. Mer information finns i [inbyggda roller för virtuella Windows-datorer](rbac.md). 

### <a name="application-group-limit-increase"></a>Ökning av program grupps gräns

Vi har ökat standard antalet program grupper per Azure Active Directory klient till 200 grupper.

### <a name="client-updates-for-december-2020"></a>Klient uppdateringar för december 2020

Vi har släppt nya versioner av följande klienter: 

- Android
- macOS
- Windows

Mer information om klient uppdateringar finns i [klient uppdateringar](whats-new.md#client-updates).

## <a name="november-2020"></a>November 2020

### <a name="azure-portal-experience"></a>Azure Portal upplevelse

Vi har åtgärdat två buggar i Azure Portal användar upplevelsen:

- Det egna namnet på Desktop-programmet skrivs inte längre över till arbets flödet "Lägg till virtuell dator".
- Fliken Session Host kommer nu att läsas in om sessions-värdar ingår i skalnings uppsättningar.

### <a name="fslogix-client-version-2009"></a>FSLogix-klient, version 2009 

Vi har lanserat en ny version av FSLogix-klienten med många korrigeringar och förbättringar. Läs mer i [vårt blogg inlägg](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>RDP-Shortpath offentlig för hands version

RDP-Shortpath introducerar direkt anslutning till din Windows-värd för fjärrskrivbordssession med hjälp av punkt-till-plats-och plats-till-plats-VPN och ExpressRoute. Den introducerar också transport protokollet URCP. RDP-Shortpath har utformats för att minska svars tid och nätverks hopp för att förbättra användar upplevelsen. Läs mer på [RDP-Shortpath för Windows Virtual Desktop](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>AZ. DesktopVirtualization, version 2.0.1

Vi har släppt version 2.0.1 av Windows-cmdletar för virtuella skriv bord. Den här uppdateringen innehåller cmdletar som gör att du kan hantera MSIX app Attach. Du kan hämta den nya versionen i [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Azure Advisor uppdateringar

Azure Advisor har nu en ny rekommendation för närhets vägledning i det virtuella Windows-skrivbordet och en ny rekommendation för att optimera prestanda i djup-första belastningsutjämnade värdbaserade pooler. Läs mer på [Azure-webbplatsen](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Oktober 2020

Följande har ändrats i oktober 2020:

### <a name="improved-performance"></a>Förbättrade prestanda

- Vi har optimerat prestanda genom att minska anslutnings fördröjningen i följande Azure-geografiska områden:
    - Schweiz
    - Kanada

Nu kan du använda [Experience-uppskattningen](https://azure.microsoft.com/services/virtual-desktop/assessment/) för att uppskatta kvaliteten på användar upplevelsen i dessa områden.

### <a name="azure-government-cloud-availability"></a>Azure Government moln tillgänglighet

Azure Government molnet är nu allmänt tillgängligt. Läs mer i [vårt blogg inlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure Portal-uppdateringar

Vi har gjort några uppdateringar av Windows Virtual Desktop Azure Portal:

- Ett resourceID-fel har åtgärd ATS som hindrade användare från att öppna fliken "sessioner".
- Effektiviserat användar gränssnittet på fliken "sessioner".
- Korrigera inställningarna "standard," användbarhet "och" Återställ standardvärden "under RDP-egenskaperna.
- Gjorde "ta bort" och "ta bort"-funktioner på alla flikar.
- Portalen validerar nu namn på appar i arbets flödet "Lägg till en app".
- Ett problem har åtgärd ATS där export data för sessionsvärdservern inte justerats i kolumnerna.
- Ett problem har åtgärd ATS där portalen inte kunde hämta användarsessioner.
- Ett problem har åtgärd ATS i en sessions värd hämtning som inträffade när den virtuella datorn skapades i en annan resurs grupp.
- Fliken "Session Host" har uppdaterats till lista både aktiva och frånkopplade sessioner.
- Fliken "program" har nu sidor.
- Ett problem har åtgärd ATS där texten "kräver kommando rad" inte visas korrekt på fliken "program lista".
- Ett problem har åtgärd ATS när portalen inte kunde distribuera värdar eller virtuella datorer när du använder den tyska språk versionen av den delade avbildnings galleriet.

### <a name="client-updates-for-october-2020"></a>Klient uppdateringar för oktober 2020

Vi har släppt nya versioner av-klienterna. Mer information finns i de här artiklarna:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Mer information om de andra klienterna finns i [klient uppdateringar](#client-updates).

## <a name="september-2020"></a>September 2020

Följande har ändrats i september 2020:

- Vi har optimerat prestanda genom att minska anslutnings fördröjningen i följande Azure-geografiska områden:
    - Tyskland
    - Sydafrika (endast för validerings miljöer)

Nu kan du använda [Experience-uppskattningen](https://azure.microsoft.com/services/virtual-desktop/assessment/) för att uppskatta kvaliteten på användar upplevelsen i dessa områden.

- Vi har publicerat version 1.2.1364 av Windows Desktop-klienten för Windows Virtual Desktop. I den här uppdateringen har vi gjort följande ändringar:
    - Ett problem har åtgärd ATS där enkel inloggning (SSO) inte fungerade på Windows 7.
    - Ett problem har åtgärd ATS som gjorde att klienten kopplades från när en användare som aktiverade medie optimering för team försökte anropa eller ansluts till ett team möte medan en annan app hade en ljud ström öppen i exklusivt läge.
    - Ett problem har åtgärd ATS där team inte räknar upp ljud-eller video enheter när medie optimering för team aktiverades.
    - Har du lagt till en "behöver hjälp med inställningar?" länk till sidan med Skriv bords inställningar.
    - Ett problem har åtgärd ATS med knappen "Prenumerera" som hände när du använde mörka teman med hög kontrast.
    
- Tack vare den fantastiska hjälpen från våra användare har vi åtgärdat två viktiga problem för Microsoft Store fjärr skrivbords klienten. Vi fortsätter att granska feedback och åtgärda problem eftersom vi utökar vår stegvisa lansering av klienten till fler användare över hela världen.
    
- Vi har lagt till en ny funktion som gör att du kan ändra plats för virtuell dator, avbildning, resurs grupp, prefix, nätverks konfiguration som en del av arbets flödet för att lägga till en virtuell dator i distributionen i Azure Portal.

- IT-proffs kan nu hantera hybrid Azure Active Directory anslutna Windows 10 Enterprise-datorer med Microsoft Endpoint Manager. Mer information finns i [vårt blogg inlägg](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Augusti 2020

Följande har ändrats i augusti 2020:

- Vi har förbättrat prestanda för att minska anslutnings fördröjningen i följande Azure-regioner: 

    - Storbritannien
    - Frankrike
    - Norge
    - Sydkorea

   Du kan använda [Experience-uppskattningen](https://azure.microsoft.com/services/virtual-desktop/assessment/) för att få en allmän uppfattning om hur dessa ändringar kommer att påverka användarna.

- Microsoft Store fjärr skrivbords klienten (v-10.2.1522 +) är nu allmänt tillgänglig! Den här versionen av Microsoft Store fjärr skrivbords klienten är kompatibel med Windows Virtual Desktop. Vi har också infört uppdaterade GRÄNSSNITTs flöden för förbättrade användar upplevelser. Den här uppdateringen omfattar Fluent design, lätta och mörka lägen och många andra spännande förändringar. Vi har också skrivit om klienten för att använda samma underliggande RDP-motor (Remote Desktop Protocol) som iOS-, macOS-och Android-klienter. På så sätt kan vi leverera nya funktioner till ett snabbare pris på alla plattformar. [Ladda ned klienten](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) och ge den ett try!

- Vi har åtgärdat ett problem i Teams Skriv bords klienten (version 1.3.00.21759) där klienten endast visade UTC-tidszonen i chatten, kanalerna och kalendern. Den uppdaterade klienten visar nu Fjärrsessionens tidszon i stället.

- Azure Advisor är nu en del av det virtuella Windows-skrivbordet. När du ansluter till virtuella Windows-datorer via Azure Portal kan du se rekommendationer för att optimera din Windows-miljö för virtuella skriv bord. Läs mer på [Azure Advisor](azure-advisor.md).

- Azure CLI stöder nu Windows Virtual Desktop ( `az desktopvirtualization` ) för att hjälpa dig att automatisera dina Windows-distributioner av virtuella datorer. Ta en titt på [desktopvirtualization](/cli/azure/ext/desktopvirtualization/) för en lista över tilläggs kommandon.

- Vi har uppdaterat våra mallar för att göra dem fullständigt kompatibla med Windows Virtual Desktop Azure Resource Manager-gränssnitt. Du kan hitta mallarna på [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Windows Virtual Desktop US Gov-portalen finns nu som offentlig för hands version. Mer information finns i [vårt meddelande](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Juli 2020  

Juli var när Windows Virtual Desktop med Azure Resource Management-integrering blev allmänt tillgängligt.

Detta är vad som har ändrats med den nya versionen: 

- "Hösten 2019-versionen" kallas nu "Windows Virtual Desktop (klassisk)" medan "våren 2020 release" nu är bara "Windows Virtual Desktop". Mer information finns i [det här blogg inlägget](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Om du vill veta mer om nya funktioner kan du kolla i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Uppdatering av autoskalning Tool

Den senaste versionen av verktyget autoskalning som fanns i för hands versionen är nu allmänt tillgänglig. Det här verktyget använder ett Azure Automation-konto och Azure Logic-appen för att automatiskt stänga och starta om sessionens virtuella datorer (VM) i en adresspool, vilket minskar infrastruktur kostnaderna. Läs mer i [Scale session-värdar med hjälp av Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Nu kan du göra följande saker med Azure Portal i det virtuella Windows-Skriv bordet: 

- Tilldela användare direkt till personliga värdar för fjärrskrivbordssessioner  
- Ändra validerings miljö inställningen för värdbaserade pooler 

### <a name="diagnostics"></a>Diagnostik

Vi har släppt några nya fördefinierade frågor för Log Analytics arbets ytan. Om du vill komma åt frågorna går du till **loggar** och under **kategori** väljer du **virtuellt Windows-skrivbord**. Läs mer i [använda Log Analytics för funktionen diagnostik](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Uppdatering för fjärr skrivbords klient för Android

[Fjärr skrivbords klienten för Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) stöder nu Windows virtuella Skriv bords anslutningar. Från och med version 10.0.7 innehåller Android-klienten ett nytt gränssnitt för bättre användar upplevelse. Klienten integreras också med Microsoft Authenticator på Android-enheter för att aktivera villkorlig åtkomst när du prenumererar på Windows Virtual Desktop-arbetsytor.  

Den tidigare versionen av fjärr skrivbords klienten kallas nu "fjärr skrivbord 8". Alla befintliga anslutningar som du har i den tidigare versionen av klienten överförs sömlöst till den nya klienten. Den nya klienten har skrivits om till samma underliggande RDP Core-motor som iOS-och macOS-klienter, och snabbare lansera nya funktioner på alla plattformar. 

### <a name="teams-update"></a>Uppdatering av team

Vi har gjort förbättringar av Microsoft Teams för virtuella Windows-datorer. Det viktigaste är att Windows Virtual Desktop stöder ljud-och video optimering för Windows Desktop-klienten. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de använder ljud eller video i samtal och möten. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare. Läs mer i [använda team på Windows Virtual Desktop](teams-on-wvd.md).

## <a name="june-2020"></a>Juni 2020

Förra månaden introducerade vi Windows Virtual Desktop med Azure Resource Manager-integrering i för hands versionen. Den här uppdateringen innehåller många spännande nya funktioner som vi älskar att berätta om. Här är what's nya för den här versionen av det virtuella Windows-skrivbordet.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop är nu integrerat med Azure Resource Manager

Windows Virtual Desktop är nu integrerat i Azure Resource Manager. I den senaste uppdateringen Azure Resource Manager alla virtuella Windows-skrivbordet-objekt. Den här uppdateringen är också integrerad med rollbaserad åtkomst kontroll i Azure (Azure RBAC). Se [Vad är Azure Resource Manager?](../azure-resource-manager/management/overview.md) om du vill veta mer.

Den här ändringen gör du:

- Det virtuella Windows-skrivbordet är nu integrerat med Azure Portal. Det innebär att du kan hantera allt direkt i portalen, inga PowerShell-, webbappar eller verktyg från tredje part krävs. Kom igång genom att titta på vår självstudie i [skapa en adresspool med Azure Portal](create-host-pools-azure-marketplace.md).

- Innan den här uppdateringen kan du bara publicera RemoteApp-och skriv bord till enskilda användare. Med Azure Resource Manager kan du nu publicera resurser i Azure Active Directory grupper.

- Den tidigare versionen av Windows Virtual Desktop hade fyra inbyggda administratörs roller som du kan tilldela till en klient eller adresspool. Dessa roller finns nu i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Du kan tillämpa dessa roller på alla virtuella Windows-datorer Azure Resource Manager objekt, vilket gör att du kan ha en fullständig, omfattande Delegerings modell.

- I den här uppdateringen behöver du inte längre köra Azure Marketplace eller GitHub-mallen upprepade gånger för att expandera en adresspool. Allt du behöver för att expandera en adresspool är att gå till din värd-pool i Azure Portal och välja **+ Lägg** till för att distribuera ytterligare värdar för sessioner.

- Distribution av värd bassäng är nu fullständigt integrerat med [galleriet för Azures delade avbildningar](../virtual-machines/shared-image-galleries.md). Delade avbildnings galleriet är en separat Azure-tjänst som lagrar definitioner av virtuella dator avbildningar, inklusive avbildnings versioner. Du kan också använda global replikering för att kopiera och skicka dina avbildningar till andra Azure-regioner för lokal distribution.

- Övervakning av funktioner som används för att utföras via PowerShell eller diagnostik-webbappen har nu flyttats till Log Analytics i Azure Portal. Nu har du två alternativ för att visualisera dina rapporter. Du kan köra Kusto-frågor och använda arbets böcker för att skapa visuella rapporter.

- Du behöver inte längre slutföra Azure Active Directory (Azure AD) medgivande för att använda Windows Virtual Desktop. I den här uppdateringen autentiserar Azure AD-klienten på din Azure-prenumeration användarna och tillhandahåller Azure RBAC-kontroller för dina administratörer.

### <a name="powershell-support"></a>Stöd för PowerShell

Vi har lagt till nya AzWvd-cmdletar i modulen Azure PowerShell AZ med den här uppdateringen. Den här nya modulen stöds i PowerShell Core, som körs på .NET Core.

Installera modulen genom att följa anvisningarna i [Konfigurera PowerShell-modulen för Windows Virtual Desktop](powershell-module.md).

Du kan också se en lista över tillgängliga kommandon i [AzWvd PowerShell-referensen](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Mer information om de nya funktionerna finns i [vårt blogg inlägg](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Ytterligare gatewayer

Vi har lagt till ett nytt Gateway-kluster i Sydafrika för att minska anslutnings fördröjningen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams på Windows Virtual Desktop (för hands version)

Vi har gjort några förbättringar av Microsoft Teams för Windows Virtual Desktop. Det viktigaste är att Windows Virtual Desktop har nu stöd för ljud-och visuell omdirigering för samtal. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de anropar med hjälp av ljud eller video. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare.

Mer information finns i [vårt blogg inlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om framtida planer i [Microsoft 365 översikt över virtuella Windows-datorer](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
