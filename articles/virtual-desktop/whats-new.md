---
title: Vad är nytt i det virtuella Windows-skrivbordet? – Azure
description: Nya funktioner och produkt uppdateringar för virtuella Windows-datorer.
author: Heidilohr
ms.topic: overview
ms.date: 12/02/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: e42e49f69a159bf3bfdc732d41dc66a9a707f42f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617365"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Vad är nytt i det virtuella Windows-skrivbordet?

Uppdateringar av virtuella Windows-datorer regelbundet. I den här artikeln får du lära dig att:

- De senaste uppdateringarna
- Nya funktioner
- Förbättringar av befintliga funktioner
- Felkorrigeringar

Den här artikeln uppdateras varje månad. Se till att kontrol lera igen ofta för att hålla dig uppdaterad med nya uppdateringar.

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

- Azure CLI stöder nu Windows Virtual Desktop ( `az desktopvirtualization` ) för att hjälpa dig att automatisera dina Windows-distributioner av virtuella datorer. Ta en titt på [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true) för en lista över tilläggs kommandon.

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

- Distribution av värd bassäng är nu fullständigt integrerat med [galleriet för Azures delade avbildningar](../virtual-machines/windows/shared-image-galleries.md). Delade avbildnings galleriet är en separat Azure-tjänst som lagrar definitioner av virtuella dator avbildningar, inklusive avbildnings versioner. Du kan också använda global replikering för att kopiera och skicka dina avbildningar till andra Azure-regioner för lokal distribution.

- Övervakning av funktioner som används för att utföras via PowerShell eller diagnostik-webbappen har nu flyttats till Log Analytics i Azure Portal. Nu har du två alternativ för att visualisera dina rapporter. Du kan köra Kusto-frågor och använda arbets böcker för att skapa visuella rapporter.

- Du behöver inte längre slutföra Azure Active Directory (Azure AD) medgivande för att använda Windows Virtual Desktop. I den här uppdateringen autentiserar Azure AD-klienten på din Azure-prenumeration användarna och tillhandahåller Azure RBAC-kontroller för dina administratörer.

### <a name="powershell-support"></a>Stöd för PowerShell

Vi har lagt till nya AzWvd-cmdletar i modulen Azure PowerShell AZ med den här uppdateringen. Den här nya modulen stöds i PowerShell Core, som körs på .NET Core.

Installera modulen genom att följa anvisningarna i [Konfigurera PowerShell-modulen för Windows Virtual Desktop](powershell-module.md).

Du kan också se en lista över tillgängliga kommandon i [AzWvd PowerShell-referensen](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true).

Mer information om de nya funktionerna finns i [vårt blogg inlägg](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Ytterligare gatewayer

Vi har lagt till ett nytt Gateway-kluster i Sydafrika för att minska anslutnings fördröjningen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams på Windows Virtual Desktop (för hands version)

Vi har gjort några förbättringar av Microsoft Teams för Windows Virtual Desktop. Det viktigaste är att Windows Virtual Desktop har nu stöd för ljud-och visuell omdirigering för samtal. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de anropar med hjälp av ljud eller video. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare.

Mer information finns i [vårt blogg inlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="client-updates"></a>Klient uppdateringar

Ta en titt på de här artiklarna om du vill veta mer om uppdateringar för våra klienter för virtuella Windows-datorer och Fjärrskrivbordstjänster:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Webb](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om framtida planer i [Microsoft 365 översikt över virtuella Windows-datorer](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
