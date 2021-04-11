---
title: Felsöka VM Insights-gäst hälsa (för hands version)
description: Beskriver fel söknings steg som du kan vidta när du har problem med VM Insights-hälsa.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932785"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Felsöka VM Insights-gäst hälsa (för hands version)
I den här artikeln beskrivs fel söknings steg som du kan vidta när du har problem med VM Insights-hälsa.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Uppgraderings tillgängligt meddelande visas fortfarande efter att gäst hälsa har uppgraderats 

- Kontrol lera att den virtuella datorn körs i Global Azure. Arc-aktiverade servrar stöds inte ännu.
- Kontrol lera att den virtuella datorns region och operativ system version stöds enligt beskrivningen i [aktivera Azure Monitor for VMS gäst hälsa (för hands version)](vminsights-health-enable.md).
- Kontrol lera att gäst hälso tillägget har installerats med 0 avslutnings kod.
- Kontrol lera att Azure Monitor Agent-tillägget har installerats.
- Kontrol lera att systemtilldelad hanterad identitet har Aktiver ATS för den virtuella datorn.
- Kontrol lera att inga användare tilldelade hanterade identiteter har angetts för den virtuella datorn.
- Verifiera för virtuella Windows-datorer som är nationella *amerikansk engelska*. Lokalisering stöds inte för närvarande av Azure Monitor Agent.
- Kontrol lera att den virtuella datorn inte använder nätverks-proxyn. Azure Monitor-agenten stöder för närvarande inte proxyservrar.
- Verifiera att hälso utöknings agenten startades utan fel. Om agenten inte kan starta kan agentens tillstånd vara skadat. Ta bort innehållet i mappen agent tillstånd och starta om agenten.
  - För Linux: daemon är *vmGuestHealthAgent*. Mappen State är */var/opt/vmGuestHealthAgent/**
  - För Windows: tjänsten är en *gäst hälso agent för virtuella datorer*. Mappen State är _%programdata%\Microsoft\VMGuestHealthAgent \\ *_.
- Kontrol lera att den Azure Monitor agenten är ansluten till nätverket. 
  - Försök att pinga _<region> . handler.Control.Monitor.Azure.com_ från den virtuella datorn. Försök till exempel att pinga _westeurope.handler.Control.Monitor.Azure.com:443_ för en virtuell dator i westeurope.
- Kontrol lera att den virtuella datorn har en Association med en data insamlings regel i samma region som Log Analytics-arbetsytan.
  -  Se **skapa data insamlings regel (DCR)** i [Aktivera Azure Monitor for VMS gäst hälsa (för hands version)](vminsights-health-enable.md) för att säkerställa att filens struktur är korrekt. Var särskilt uppmärksam på förekomsten av *performanceCounters* data source-avsnittet som innehåller exempel på tre räknare och förekomst av *inputDataSources* -avsnittet i hälso tilläggs konfigurationen för att skicka räknare till tillägget.
-  Kontrol lera om det finns problem med tillägg för gäst hälsa i den virtuella datorn.
   -  För Linux: kontrol lera loggarna på _/var/log/Azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_.
   -  För Windows: kontrol lera loggar i _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent- \{ tillägget version} \* . log_.
-  Kontrol lera om den virtuella datorn innehåller Azure Monitor Agent fel.
   -  För Linux: kontrol lera loggarna på _/var/log/mdsd. *_.
   -  För Windows: kontrol lera loggarna på _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Fel meddelande om att inga data är tillgängliga 

![Inga data](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Kontrol lera att den virtuella datorn uppfyller konfigurations kraven

1. Kontrollera att den virtuella datorn är en virtuell Azure-dator. Azure Arc för servrar stöds inte för närvarande.
2. Kontrollera att den virtuella datorn kör ett [operativsystem som stöds](vminsights-health-enable.md?current-limitations.md).
3. Kontrollera att den virtuella datorn är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).
4. Kontrollera att Log Analytics-arbetsytan är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Kontrol lera att den virtuella datorn har registrerats korrekt
Verifiera att Azure Monitor Agent-tillägget och den virtuella gäst datorns hälso agent har allokerats på den virtuella datorn. Välj **tillägg** på den virtuella datorns meny i Azure Portal och kontrol lera att de två agenterna visas.

![VM-tillägg](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Kontrol lera att den tilldelade system identiteten är aktive rad på den virtuella datorn
Kontrol lera att systemets tilldelade identitet är aktive rad på den virtuella datorn. Välj **identitet** från den virtuella datorns meny i Azure Portal. Om användarens hanterade identitet är aktive rad, oavsett status för systemets hanterade identitet, kommer Azure Monitor Agent inte att kunna kommunicera med konfigurations tjänsten och gäst hälso tillägget fungerar inte.

![Tilldelad identitet](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verifiera data insamlings regel
Kontrol lera att data insamlings regeln som anger hälso tillägg som data källa är associerad med den virtuella datorn.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Fel meddelande för felaktig begäran på grund av otillräcklig behörighet
Det här felet indikerar att **Microsoft. WorkloadMonitor** Resource Provider inte registrerades i prenumerationen. Se [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) för information om hur du registrerar resurs leverantören. 

![Felaktig begäran](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Hälso tillstånd visas som "okänt" efter att gäst hälsa har Aktiver ATS.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Kontrol lera att prestanda räknare på Windows-noder fungerar korrekt 
Gäst hälsa är beroende av att agenten kan samla in prestanda räknare från noden. den grundläggande uppsättningen med prestanda räknar bibliotek kan bli skadad och måste kanske återskapas. Följ instruktionerna i [manuellt återskapa prestanda räknar biblioteks värden](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) för att återskapa prestanda räknarna.





## <a name="next-steps"></a>Nästa steg

- [Få en översikt över gäst hälso funktionen i VM Insights](vminsights-health-overview.md)