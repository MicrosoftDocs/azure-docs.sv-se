---
title: Hotpatch för Windows Server Azure Edition (för hands version)
description: Lär dig hur Hotpatch för Windows Server Azure Edition fungerar och hur du aktiverar det
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 710e6902be6ebe28caaf40fb446e4ee7cd2bf4dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687574"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch för nya virtuella datorer (förhands granskning)

HotPatching är ett nytt sätt att installera uppdateringar på nya virtuella Windows Server Azure-datorer (VM) som inte kräver en omstart efter installationen. Den här artikeln beskriver information om Hotpatch för virtuella Windows Server Azure-datorer, som har följande fördelar:
* Lägre arbets belastnings påverkan med mindre omstarter
* Snabbare distribution av uppdateringar eftersom paketen är mindre, installera snabbare och har enklare uppdaterings dirigering med Azure Update Manager
* Bättre skydd eftersom Hotpatch uppdaterings paket är begränsade till Windows-säkerhetsuppdateringar som installeras snabbare utan att starta om

## <a name="how-hotpatch-works"></a>Så här fungerar hotpatch

Hotpatch fungerar genom att först upprätta en bas linje med en Windows Update senaste kumulativa uppdateringen. Hotpatches släpps regelbundet (till exempel den andra tisdagen i månaden) som bygger på den bas linjen. Hotpatches kommer att innehålla uppdateringar som inte kräver en omstart. Med jämna mellanrum (från och med tre månader) uppdateras bas linjen med en ny senast ackumulerad uppdatering.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Schema för Hotpatch-exempel.":::

Det finns två typer av bas linjer: **planerade bas linjer** och **oplanerade** nivåer.
*  **Planerade bas linjer** släpps på en vanlig takt med hotpatch-versioner på mellan.  Planerade bas linjer inkluderar alla uppdateringar i en jämförbar _senaste kumulativa uppdatering_ för den månaden, och kräver en omstart.
    * Exemplet ovan illustrerar fyra planerade bas linje versioner under ett kalender år (fem totalt i diagrammet) och åtta hotpatch-versioner.
* **Oplanerade bas linjer** släpps när en viktig uppdatering (till exempel en korrigering på noll dagar) släpps och den särskilda uppdateringen inte kan släppas som en Hotpatch.  När oplanerad bas linje släpps ersätts en hotpatch-version med en oplanerad bas linje under den månaden.  Oplanerade bas linjer innehåller även alla uppdateringar i en jämförbar _senaste kumulativ uppdatering_ för den månaden, och kräver även en omstart.
    * Exemplet ovan visar två oplanerade bas linjer som ersätter hotpatch-versionerna för dessa månader (det faktiska antalet oplanerade bas linjer under ett år som inte är känt i förväg).

## <a name="regional-availability"></a>Regional tillgänglighet
Hotpatch är tillgänglig i alla globala Azure-regioner i för hands versionen. Azure Government regioner stöds inte i förhands granskningen.

## <a name="how-to-get-started"></a>Så här kommer du igång

> [!NOTE]
> Under förhands gransknings fasen kan du bara komma igång i Azure Portal med hjälp av [den här länken](https://aka.ms/AzureAutomanageHotPatch).

Följ dessa steg om du vill börja använda Hotpatch på en ny virtuell dator:
1.  Aktivera för hands versions åtkomst
    * Åtkomst till för hands versionen av en gång krävs per prenumeration.
    * För hands versions åtkomst kan aktive ras via API, PowerShell eller CLI enligt beskrivningen i följande avsnitt.
1.  Skapa en virtuell dator från Azure Portal
    * Under för hands versionen måste du komma igång med [den här länken](https://aka.ms/AzureAutomanageHotPatch).
1.  Ange information om virtuell dator
    * Kontrol lera att _Windows Server 2019 Data Center: Azure Edition_ är markerat i list rutan bild)
    * Rulla ned till avsnittet "gäst operativ system uppdateringar" på fliken hantering. Du ser HotPatching inställt på på och installation av korrigering som standard till Azure-dirigerad uppdatering.
    * Automatiskt hantering av metod tips för virtuella datorer aktive ras som standard
1. Skapa din nya virtuella dator

## <a name="enabling-preview-access"></a>Aktivera för hands versions åtkomst

### <a name="rest-api"></a>REST-API

I följande exempel beskrivs hur du aktiverar för hands versionen av din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd ```Register-AzProviderFeature``` cmdleten för att aktivera för hands versionen av din prenumeration.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Använd ```az feature register``` för att aktivera för hands versionen av din prenumeration.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Installation av korrigering

Under för hands versionen aktive ras [Automatisk uppdatering av virtuella gäst datorer](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) automatiskt för alla virtuella datorer som skapats med _Windows Server 2019 Data Center: Azure Edition_. Med automatisk korrigering av VM-gäster aktiverat:
* Korrigeringar som klassificeras som kritiska eller säkerhet hämtas automatiskt och tillämpas på den virtuella datorn.
* Uppdateringar tillämpas under låg belastnings tider i den virtuella datorns tidszon.
* Uppdaterings dirigeringen hanteras av Azure och korrigeringarna tillämpas efter [principer för tillgänglighets första](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching).
* Hälso tillstånd för virtuella datorer, som fastställs genom plattformens hälso signaler, övervakas för att identifiera korrigerings fel.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hur fungerar automatisk uppdatering av virtuella gäst datorer?

När [Automatisk uppdatering av virtuella gäst datorer](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) är aktive rad på en virtuell dator laddas de tillgängliga viktiga och säkerhets korrigeringarna ned och tillämpas automatiskt. Den här processen startar automatiskt varje månad när nya korrigeringar lanseras. Korrigering av korrigering och installation sker automatiskt, och processen omfattar att starta om den virtuella datorn efter behov.

Med Hotpatch aktiverat på _Windows Server 2019 Data Center: virtuella Azure_ -datorer, levereras de flesta månatliga säkerhets uppdateringar som hotpatches som inte kräver omstarter. De senaste kumulativa uppdateringarna som skickats på planerade eller oplanerade bas linje månader kräver omstarter av virtuella datorer. Ytterligare kritiska uppdateringar eller säkerhets korrigeringar kan också vara tillgängliga regelbundet, vilket kan kräva omstart av virtuella datorer.

Den virtuella datorn bedöms automatiskt med några dagars mellanrum och flera gånger under en 30-dagarsperiod för att fastställa de tillämpliga korrigeringarna för den virtuella datorn. Den här automatiska utvärderingen säkerställer att eventuella korrigeringar som saknas upptäcks så snart som möjligt.

Korrigeringsfiler installeras inom 30 dagar från de månatliga korrigerings versionerna, efter de principer som är [tillgängliga för första](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)gången. Korrigeringsfiler installeras bara vid låg belastnings tider för den virtuella datorn, beroende på den virtuella datorns tidszon. Den virtuella datorn måste köras under låg belastnings tid för att korrigerings program ska installeras automatiskt. Om en virtuell dator stängs av under en periodisk utvärdering, kommer den virtuella datorn att bedömas och de tillämpliga korrigeringarna installeras automatiskt vid nästa periodiska bedömning när den virtuella datorn påbörjas. Nästa periodiska bedömning sker vanligt vis inom några dagar.

Definitions uppdateringar och andra korrigeringar som inte klassificeras som kritiska eller säkerhet installeras inte via automatisk uppdatering av gäst datorer.

## <a name="understanding-the-patch-status-for-your-vm"></a>Förstå uppdaterings statusen för din virtuella dator

Om du vill visa korrigerings status för den virtuella datorn går du till avsnittet **gäst + värd uppdateringar** för den virtuella datorn i Azure Portal. Under avsnittet **uppdateringar av gäst operativ system** klickar du på på gå till Hotpatch (för hands version) om du vill visa den senaste korrigerings statusen för den virtuella datorn.

På den här skärmen ser du Hotpatch-statusen för den virtuella datorn. Du kan också se om det finns några tillgängliga korrigeringsfiler för den virtuella datorn som inte har installerats. Som beskrivs i avsnittet "Installera korrigering" ovan installeras alla säkerhets-och kritiska uppdateringar automatiskt på den virtuella datorn med hjälp av [Automatisk uppdatering av virtuella gäst datorer](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) och inga extra åtgärder krävs. Korrigeringar med andra uppdaterings klassificeringar installeras inte automatiskt. De visas i stället i listan över tillgängliga korrigeringar på fliken "uppdatera efterlevnad". Du kan också visa historiken för uppdaterings distributioner på den virtuella datorn via uppdaterings historiken. Uppdaterings historiken från de senaste 30 dagarna visas, tillsammans med information om korrigerings installation.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hantering av Hotpatch.":::

Med den automatiska korrigeringen av den virtuella gästen uppdateras din virtuella dator regelbundet och beräknas automatiskt för tillgängliga uppdateringar. Dessa periodiska utvärderingar ser till att de tillgängliga korrigeringarna upptäcks. Du kan visa resultatet av utvärderingen på skärmen uppdateringar ovan, inklusive tidpunkten för den senaste utvärderingen. Du kan också välja att utlösa en utvärdering på begäran för din virtuella dator när som helst med hjälp av alternativet "utvärdera nu" och granska resultaten när utvärderingen är klar.

På samma sätt som du bedömer på begäran kan du också installera korrigeringsfiler på begäran för din virtuella dator med alternativet Installera uppdateringar nu. Här kan du välja att installera alla uppdateringar under vissa uppdaterings klassificeringar. Du kan också ange uppdateringar som ska tas med eller undantas genom att tillhandahålla en lista över enskilda kunskaps bas artiklar. Korrigeringsfiler som är installerade på begäran installeras inte med hjälp av principer för tillgänglighets principer och kan kräva ytterligare omstarter och VM-stillestånd för uppdaterings installation.

## <a name="supported-updates"></a>Uppdateringar som stöds

Hotpatch täcker Windows säkerhets uppdateringar och bibehåller paritet med innehållet i säkerhets uppdateringar som utfärdats till i den vanliga (icke-Hotpatch) Windows Update-kanalen.

Det finns några viktiga överväganden för att köra en virtuell Windows Server Azure-version med Hotpatch aktiverat. Omstarter krävs fortfarande för att installera uppdateringar som inte ingår i Hotpatch-programmet. Omstarter krävs också regelbundet efter att en ny bas linje har installerats. Dessa omstarter behåller den virtuella datorn synkroniserad med icke-säkerhets korrigeringar som ingår i den senaste kumulativa uppdateringen.
* Korrigeringar som för närvarande inte ingår i Hotpatch-programmet inkluderar icke-säkerhetsuppdateringar som publicerats för Windows och uppdateringar som inte är Windows-uppdateringar (till exempel .NET-korrigeringsfiler).  De här typerna av korrigeringar måste installeras under en bas linje och kräver en omstart.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-is-hotpatching"></a>Vad är HotPatching?

* HotPatching är ett nytt sätt att installera uppdateringar på ett Windows Server 2019 Data Center: Azure Edition VM i Azure som inte kräver en omstart efter installationen. Det fungerar genom att korrigera InMemory-koden för att köra processer utan att behöva starta om processen.

### <a name="how-does-hotpatching-work"></a>Hur fungerar HotPatching?

* HotPatching fungerar genom att upprätta en bas linje med en Windows Update senaste kumulativa uppdateringen och bygger sedan på den bas linjen med uppdateringar som inte kräver att en omstart börjar gälla.  Bas linjen uppdateras regelbundet med en ny ackumulerad uppdatering. Den kumulativa uppdateringen innehåller alla säkerhets-och kvalitets uppdateringar och kräver en omstart.

### <a name="why-should-i-use-hotpatch"></a>Varför ska jag använda Hotpatch?

* När du använder Hotpatch på Windows Server 2019 Data Center: Azure-utgåva kommer den virtuella datorn att ha högre tillgänglighet (färre omstarter) och snabbare uppdateringar (mindre paket som installeras snabbare utan att behöva starta om processer). Den här processen resulterar i en virtuell dator som alltid är uppdaterad och säker.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Vilka typer av uppdateringar omfattas av Hotpatch?

* Hotpatch täcker för närvarande Windows-säkerhetsuppdateringar.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>När får jag den första Hotpatch-uppdateringen?

* Hotpatch-uppdateringar publiceras vanligt vis den andra tisdagen varje månad. Mer information finns nedan.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Vad kommer Hotpatch-schemat att se ut?

* HotPatching fungerar genom att upprätta en bas linje med en Windows Update senaste kumulativa uppdateringen och bygger sedan på den bas linjen med uppdateringar av Hotpatch lanseras månads vis.  Under förhands granskningen frigörs bas linjer som börjar var tredje månad. Se bilden nedan för ett exempel på ett årligt tre månaders schema (inklusive exempel på oplanerade bas linjer på grund av noll-dagars korrigeringar).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Schema för Hotpatch-exempel.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Behöver omstarter fortfarande för en virtuell dator som registrerats i Hotpatch?

* Omstarter krävs fortfarande för att installera uppdateringar som inte ingår i Hotpatch-programmet, och de krävs regelbundet när en bas linje (Windows Update senaste kumulativa uppdateringen) har installerats. Den här omstarten håller den virtuella datorn synkroniserad med alla korrigeringsfiler som ingår i den kumulativa uppdateringen. Bas linjer (som kräver en omstart) börjar på en tre månaders takt och ökar med tiden.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Påverkas mina program när en Hotpatch-uppdatering installeras?

* Eftersom Hotpatch patchar in minnes koden för processer som körs utan att behöva starta om processen, påverkas inte dina program av korrigerings processen. Observera att detta skiljer sig från alla potentiella prestanda-och funktions konsekvenser i själva korrigerings filen.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Kan jag stänga av Hotpatch på den virtuella datorn?

* Du kan stänga av Hotpatch på en virtuell dator via Azure Portal.  Om du inaktiverar Hotpatch avregistreras den virtuella datorn från Hotpatch, vilket återställer den virtuella datorn till vanliga uppdaterings beteenden för Windows Server.  När du avregistrerar från Hotpatch på en virtuell dator kan du registrera den virtuella datorn igen när nästa Hotpatch-bas linje släpps.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Kan jag uppgradera från mitt befintliga Windows Server-operativsystem?

* Det finns inte stöd för att uppgradera från befintliga versioner av Windows Server (dvs. Windows Server 2016 eller 2019 icke-Azure-versioner). Uppgradering till framtida versioner av Windows Server Azure Edition stöds.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Kan jag använda Hotpatch för produktions arbets belastningar under för hands versionen?

* För hands versioner är endast avsedda för testning och inte för användning i produktions miljöer.

### <a name="will-i-be-charged-during-the-preview"></a>Kommer jag att debiteras under för hands versionen?

* Licensen för Windows Server Azure Edition är kostnads fri under för hands versionen. Kostnaden för en underliggande infrastruktur som har kon figurer ATS för din virtuella dator (lagring, beräkning, nätverk osv.) kommer dock fortfarande att debiteras till din prenumeration.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hur kan jag få hjälp med fel sökning av HotPatching?

* Du kan använda en [ärende biljett för teknisk support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). För tjänst alternativet söker du efter och väljer den **virtuella dator som kör Windows** under Compute. Välj **Azure-funktioner** för problem typen och **Automatisk uppdatering av gäst datorer** för problem under typen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om Azure Uppdateringshantering [här](https://docs.microsoft.com/azure/automation/update-management/overview).
* Läs mer om automatisk uppdatering av gäst datorer [här](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching)