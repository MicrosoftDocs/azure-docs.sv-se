---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "77123222"
---
Azure File Sync agent uppdateras regelbundet för att lägga till nya funktioner och för att åtgärda problem. Vi rekommenderar att du konfigurerar Microsoft Update för att hämta uppdateringar för Azure File Sync-agenten när de är tillgängliga.

#### <a name="major-vs-minor-agent-versions"></a>Huvud versioner jämfört med mindre agent versioner
* Större agent versioner innehåller ofta nya funktioner och har ett ökande antal som den första delen av versions numret. Till exempel: \* 2. \* .\*\*
* Mindre agent versioner kallas även för "Patches" och släpps oftare än viktiga versioner. De innehåller ofta fel korrigeringar och mindre förbättringar men inga nya funktioner. Till exempel: \* \* . 3.\*\*

#### <a name="upgrade-paths"></a>Uppgradera sökvägar
Det finns fyra godkända och testade sätt att installera Azure File Sync agent-uppdateringar. 
1. **Önskat Konfigurera Microsoft Update att automatiskt hämta och installera agent uppdateringar.**  
    Vi rekommenderar alltid att du tar varje Azure File Sync uppdatering för att se till att du har åtkomst till de senaste korrigeringarna för Server agenten. Microsoft Update gör den här processen sömlös genom att automatiskt hämta och installera uppdateringar åt dig.
2. **Använd AfsUpdater.exe för att ladda ned och installera agent uppdateringar.**  
    AfsUpdater.exe finns i agentens installations katalog. Dubbelklicka på den körbara filen för att ladda ned och installera agent uppdateringar. 
3. **Korrigera en befintlig Azure File Sync agent med hjälp av en Microsoft Update korrigerings fil eller en. msp-fil. Det senaste Azure File Sync uppdaterings paketet kan laddas ned från [Microsoft Updates katalogen](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Om du kör en. msp-fil uppgraderas Azure File Sync installationen med samma metod som används automatiskt av Microsoft Update i föregående uppgraderings Sök väg. När en Microsoft Update-korrigering används utförs en uppgradering på plats av en Azure File Sync-installation.
4. **Hämta det nyaste installations programmet för Azure File Sync agent från [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Om du vill uppgradera en befintlig Azure File Sync agent installation avinstallerar du den äldre versionen och installerar sedan den senaste versionen från det nedladdade installations programmet. Server registreringen, Sync-grupperna och andra inställningar underhålls av installations programmet för Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Automatisk agent livs cykel hantering
Med agent version 6 har File Sync-teamet infört en funktion för automatisk uppgradering av agent. Du kan välja något av två lägen och ange en underhålls period där uppgraderingen görs på servern. Den här funktionen är utformad för att hjälpa dig med agentens livs cykel hantering genom att antingen tillhandahålla en guardrail som hindrar agenten från att upphöra att gälla eller som gör att det inte går att använda den aktuella inställningen.
1. **Standardvärdet** försöker förhindra att agenten upphör att gälla. Inom 21 dagar från det bokförda utgångs datumet för en agent kommer agenten att försöka själv uppgradera. Ett försök görs att uppgradera en gång om veckan inom 21 dagar före förfallo datum och i det valda underhålls fönstret. **Det här alternativet eliminerar inte behovet av att göra vanliga Microsoft Update-korrigeringsfiler.**
1. Alternativt kan du välja att agenten automatiskt ska uppgraderas automatiskt så snart en ny agent version blir tillgänglig (för närvarande inte gäller för klustrade servrar). Den här uppdateringen sker under den valda underhålls perioden och gör att servern kan dra nytta av nya funktioner och förbättringar så fort de blir allmänt tillgängliga. Detta är den rekommenderade, kostnads fria inställningen som ger större agent versioner samt uppdateringar av vanliga uppdateringar till servern. Varje agent som lanseras är i allmän kvalitet. Om du väljer det här alternativet kommer Microsoft att starta den senaste agent versionen. Klustrade servrar undantas. När flygningen är klar kommer agenten också att bli tillgänglig på [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) -aka.MS/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Ändra inställningen för automatisk uppgradering

I följande anvisningar beskrivs hur du ändrar inställningarna när du har slutfört installations programmet, om du behöver göra ändringar.

Öppna en PowerShell-konsol och navigera till den katalog där du installerade Sync-agenten och importera sedan Server-cmdletarna. Som standard ser detta ut ungefär så här:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Du kan köra `Get-StorageSyncAgentAutoUpdatePolicy` för att kontrol lera den aktuella princip inställningen och avgöra om du vill ändra den.

Om du vill ändra den aktuella princip inställningen till det fördröjda uppdaterings spåret kan du använda:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Om du vill ändra den aktuella princip inställningen till det omedelbara uppdaterings spåret kan du använda:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Agentens livs cykel och ändrings hantering garanterar
Azure File Sync är en moln tjänst som kontinuerligt introducerar nya funktioner och förbättringar. Det innebär att en viss Azure File Sync agent version bara kan stödjas under en begränsad tid. Följande regler garanterar att du har tillräckligt med tid och meddelande för att hantera agent uppdateringar/uppgraderingar i ändrings hanterings processen för att under lätta distributionen:

- Huvud agent versioner stöds i minst sex månader från datumet för den första versionen.
- Vi garanterar att vi överlappar minst tre månader mellan supporten för större agent versioner. 
- Varningar utfärdas för registrerade servrar med en snart inaktuell agent minst tre månader före förfallo datum. Du kan kontrol lera om en registrerad Server använder en äldre version av agenten under avsnittet registrerade servrar i en tjänst för synkronisering av lagring.
- Livs längden för en del agent version är kopplad till den associerade huvud versionen. Till exempel när agent version 3,0 lanseras, agent version 2. \* kommer att ställas in så att det upphör att gälla tillsammans.

> [!Note]
> Om du installerar en agent version med en förfallo varning visas en varning, men det lyckas. Det finns inte stöd för att försöka installera eller ansluta till en utgånget agent version och kommer att blockeras.
