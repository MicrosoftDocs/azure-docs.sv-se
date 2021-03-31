---
title: Arbets flöde för arkitekturen för virtuella Windows Azure-datorer | Microsoft Docs
description: Den här artikeln innehåller en översikt över arbets flödes processerna när du distribuerar en tjänst.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 606510940460db963a2aa63deb57b6dba77de3ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700141"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Arbets flöde för klassisk virtuell dator arkitektur i Windows Azure 

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln innehåller en översikt över de arbets flödes processer som inträffar när du distribuerar eller uppdaterar en Azure-resurs, till exempel en virtuell dator. 

> [!NOTE]
>Azure har två olika distributions modeller för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen.

Följande diagram visar arkitekturen i Azure-resurser.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<Alt bilden om Azure Workflow>":::

## <a name="workflow-basics"></a>Grundläggande om arbets flöden
   
**A**. RDFE/FFE är kommunikations vägen från användaren till infrastruktur resursen. RDFE (RedDog Front End) är det offentligt exponerade API: t som är klient delen av Hanteringsportal och Service Management-API som Visual Studio, Azure MMC och så vidare.  Alla begär Anden från användaren går via RDFE. FFE (infrastruktur resurs klient) är det lager som översätter begär Anden från RDFE till Fabric-kommandon. Alla begär Anden från RDFE går genom FFE för att uppnå infrastruktur styrenheter.

**B**. Infrastruktur styrenheten ansvarar för att underhålla och övervaka alla resurser i data centret. Den kommunicerar med infrastruktur resurs värd agenter på Fabric-OS som skickar information, till exempel gäst operativ systemets version, tjänst paket, tjänst konfiguration och tjänst tillstånd.

**C**. Värd agenten finns på värd-OS och ansvarar för att konfigurera gäst operativ system och kommunicera med gästa Gent (WindowsAzureGuestAgent) för att uppdatera rollen mot ett avsett mål tillstånd och utföra pulsslags kontroller med gäst agenten. Om värd agenten inte får pulsslags svar i 10 minuter startar värd agenten om gäst operativ systemet.

**C2**. WaAppAgent ansvarar för att installera, konfigurera och uppdatera WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent ansvarar för följande:

1. Konfigurera gäst operativ systemet, inklusive brand väggar, ACL: er, LocalStorage-resurser, Service Pack och konfiguration och certifikat.
2. Konfigurera SID för det användar konto som rollen ska köras under.
3. Kommunikation mellan roll status och infrastruktur resurser.
4. Starta WaHostBootstrapper och övervaka den för att se till att rollen är i mål tillstånd.

**E**. WaHostBootstrapper ansvarar för:

1. Läsa roll konfigurationen och starta alla relevanta uppgifter och processer för att konfigurera och köra rollen.
2. Övervaka alla dess underordnade processer.
3. Höja StatusCheck-händelsen för roll värd processen.

**F**. IISConfigurator körs om rollen har kon figurer ATS som en fullständig IIS-webbroll. Den ansvarar för:

1. Starta standard-IIS-tjänsterna
2. Konfigurera omskrivnings modulen i webb konfigurationen
3. Konfigurera AppPool för den konfigurerade rollen i tjänst modellen
4. Konfigurera IIS-loggning så att den pekar på mappen DiagnosticStore LocalStorage
5. Konfigurera behörigheter och ACL: er
6. Webbplatsen finns i% roleroot%: \sitesroot\0 och AppPool pekar på den här platsen för att köra IIS. 

**G**. Start aktiviteter definieras av roll modellen och startas av WaHostBootstrapper. Start aktiviteter kan konfigureras att köras i bakgrunden asynkront och värden start program startar start åtgärden och fortsätter sedan till andra start aktiviteter. Start aktiviteter kan också konfigureras för att köras i enkelt läge (standard läge) som värden start program väntar på att start aktiviteten ska slutföras och returnera slut koden (0) innan den fortsätter till nästa start åtgärd.

**H**. De här uppgifterna är en del av SDK: n och definieras som plugin-program i rollens tjänst definition (. csdef). När de expanderas till Start aktiviteter är **DiagnosticsAgent** och **RemoteAccessAgent** unika eftersom de definierar två start aktiviteter, en vanlig och en som har en **/blockStartup** -parameter. Den normala start uppgiften definieras som en start aktivitet i bakgrunden så att den kan köras i bakgrunden medan själva rollen körs. Start uppgiften **/blockStartup** definieras som en enkel start aktivitet så att WaHostBootstrapper väntar tills den avslutas innan du fortsätter. **/BlockStartup** -aktiviteten väntar på att den normala aktiviteten ska slutföras och sedan avslutas och tillåts värden start program att fortsätta. Detta görs så att diagnostik-och RDP-åtkomst kan konfigureras innan roll processerna startar (detta görs via/blockStartup-aktiviteten). Detta innebär också att diagnostik-och RDP-åtkomst kan fortsätta att köras när värden start program har slutfört start åtgärderna (detta görs via normal uppgift).

**I**. WaWorkerHost är standard värd processen för normala arbets roller. Denna värd process är värd för alla rollens DLL-filer och start punkts kod, till exempel OnStart och kör.

**J**. WaIISHost är värd processen för roll start punkt kod för webb roller som använder fullständig IIS. Den här processen läser in den första DLL som finns som använder klassen **RoleEntryPoint** och kör koden från den här klassen (OnStart, Run, OnStop). Alla **RoleEnvironment** -händelser (till exempel StatusCheck och ändrade) som skapas i klassen RoleEntryPoint aktive ras i den här processen.

**K**. W3WP är den vanliga IIS-arbetsprocessen som används om rollen har kon figurer ATS för att använda fullständig IIS. Detta kör AppPool som har kon figurer ATS från IISConfigurator. Alla RoleEnvironment-händelser (till exempel StatusCheck och ändrade) som skapas här genereras i den här processen. Observera att RoleEnvironment-händelser kommer att utlösas på båda platserna (WaIISHost och w3wp.exe) om du prenumererar på händelser i båda processerna.

## <a name="workflow-processes"></a>Arbets flödes processer

1. En användare gör en begäran, som att ladda upp ". cspkg"-och ". cscfg"-filer, meddela en resurs att stoppa eller göra en konfigurations ändring och så vidare. Detta kan göras via Azure Portal eller ett verktyg som använder Service Management-API, till exempel funktionen för att publicera Visual Studio. Den här begäran går till RDFE för att göra allt prenumerations relaterat arbete och sedan skicka begäran till FFE. Resten av de här arbets flödes stegen är att distribuera ett nytt paket och starta det.
2. FFE hittar rätt resurspool (baserat på kund uppgifter, som tillhörighets grupp eller geografisk plats plus indata från infrastruktur resursen, till exempel dator tillgänglighet) och kommunicerar med huvud infrastruktur styrenheten i den poolen.
3. Infrastruktur styrenheten hittar en värd som har tillgängliga processor kärnor (eller skapar en ny värd). Tjänst paketet och konfigurationen kopieras till värden och infrastruktur styrenheten kommunicerar med värd agenten på värd operativ systemet för att distribuera paketet (konfigurera DIP, portar, gäst operativ system och så vidare).
4. Värd agenten startar gäst operativ systemet och kommunicerar med gäst agenten (WindowsAzureGuestAgent). Värden skickar pulsslag till gästen för att se till att rollen fungerar mot målets tillstånd.
5. WindowsAzureGuestAgent konfigurerar gäst operativ systemet (brand vägg, ACL: er, LocalStorage och så vidare), kopierar en ny XML-konfigurationsfil till c:\Config och startar sedan WaHostBootstrapper-processen.
6. För fullständiga IIS-webbroller startar WaHostBootstrapper IISConfigurator och anger att den tar bort alla befintliga AppPools för webb rollen från IIS.
7. WaHostBootstrapper läser **Start** åtgärderna från E:\RoleModel.xml och börjar köra start åtgärder. WaHostBootstrapper väntar tills alla enkla start aktiviteter har slutförts och returnerat meddelandet "lyckades".
8. För fullständiga IIS-webbroller instruerar WaHostBootstrapper IISConfigurator att konfigurera IIS-AppPool och pekar på platsen `E:\Sitesroot\<index>` , där `<index>` är ett nollbaserat index i det antal element som `<Sites>` definierats för tjänsten.
9. WaHostBootstrapper kommer att starta värd processen beroende på roll typen:
    1. **Arbets roll**: WaWorkerHost.exe startas. WaHostBootstrapper kör metoden OnStart (). När den har returnerat börjar WaHostBootstrapper att köra Run ()-metoden och markerar sedan rollen som klar och placerar den i belastnings Utjämnings rotationen (om InputEndpoints har definierats). WaHostBootsrapper hamnar sedan i en slinga för att kontrol lera rollens status.
    2. **Fullständig IIS-webbroll**: aIISHost har startats. WaHostBootstrapper kör metoden OnStart (). När den har returnerats börjar metoden Run () att köras. därefter märks rollen som färdig och den läggs till i belastningsutjämnaren för belastnings utjämning. WaHostBootsrapper hamnar sedan i en slinga för att kontrol lera rollens status.
10. Inkommande webb förfrågningar till en fullständig IIS-webbroll utlöser IIS för att starta W3WP-processen och betjäna begäran, samma som i en lokal IIS-miljö.

## <a name="log-file-locations"></a>Logg fils platser

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Den här loggen innehåller ändringar i tjänsten inklusive start, stopp och nya konfigurationer. Om tjänsten inte ändras kan du se till att en stor del av tiden i logg filen förväntas.
- C:\Logs\WaAppAgent.Log.  
Den här loggen innehåller status uppdateringar och pulsslags meddelanden och uppdateras var 2-3: e sekund.  Den här loggen innehåller en historisk vy av instansens status och meddelar dig när instansen inte har statusen klar.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-loggar**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Händelse loggar i Windows**

`D:\Windows\System32\Winevt\Logs`
