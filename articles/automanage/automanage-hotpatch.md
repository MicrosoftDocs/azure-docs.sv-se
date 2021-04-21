---
title: Hotpatch för Windows Server Azure Edition (förhandsversion)
description: Lär dig hur Hotpatch för Windows Server Azure Edition fungerar och hur du aktiverar det
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830186"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch för nya virtuella datorer (förhandsversion)

Hotpatching är ett nytt sätt att installera uppdateringar på nya virtuella Windows Server Azure Edition-datorer (VM) som inte kräver någon omstart efter installationen. Den här artikeln innehåller information om Hotpatch för virtuella Windows Server Azure Edition-datorer, som har följande fördelar:
* Lägre påverkan på arbetsbelastningen med färre omstarter
* Snabbare distribution av uppdateringar eftersom paketen är mindre, installeras snabbare och har enklare korrigeringsorkestrering med Azure Update Manager
* Bättre skydd eftersom Hotpatch-uppdateringspaketen är begränsade till Windows-säkerhetsuppdateringar som installeras snabbare utan omstart

## <a name="how-hotpatch-works"></a>Så här fungerar hotpatch

Hotpatch fungerar genom att först upprätta en baslinje med Windows Update senaste kumulativa uppdateringen. Hotpatches släpps regelbundet (till exempel den andra tisdagen i månaden) som bygger på baslinjen. Hotpatches innehåller uppdateringar som inte kräver någon omstart. Med jämna mellanrum (med början var tredje månad) uppdateras baslinjen med en ny senaste kumulativ uppdatering.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Schema för hotpatch-exempel.":::

Det finns två typer av baslinjer: **Planerade baslinjer** **och oplanerade baslinjer**.
*  **Planerade baslinjer** släpps regelbundet, med hotpatch-versioner däremellan.  Planerade baslinjer innehåller alla uppdateringar i en jämförbar _senaste kumulativ uppdatering för_ den månaden och kräver en omstart.
    * I exempelschemat ovan visas fyra planerade baslinjeutgåningar under ett kalenderår (totalt fem i diagrammet) och åtta versioner av hotpatch.
* **Oplanerade baslinjer** släpps när en viktig uppdatering (till exempel en nolldagarskorrigering) släpps och den specifika uppdateringen inte kan släppas som en hotpatch.  När oplanerade baslinjer släpps ersätts en hotpatch-version med en oplanerad baslinje under den månaden.  Oplanerade baslinjer innehåller också alla uppdateringar i en jämförbar _senaste kumulativ uppdatering_ för den månaden och kräver även en omstart.
    * Exempelschemat ovan illustrerar två oplanerade baslinjer som skulle ersätta hotpatch-versionerna för dessa månader (det faktiska antalet oplanerade baslinjer under ett år är inte känt i förväg).

## <a name="regional-availability"></a>Regional tillgänglighet
Hotpatch är tillgängligt i alla globala Azure-regioner i förhandsversion. Azure Government regioner stöds inte i förhandsversionen.

## <a name="how-to-get-started"></a>Så här kommer du igång

> [!NOTE]
> Under förhandsversionsfasen kan du bara komma igång i Azure Portal med hjälp [av den här länken](https://aka.ms/AzureAutomanageHotPatch).

Följ dessa steg om du vill börja använda Hotpatch på en ny virtuell dator:
1.  Aktivera åtkomst till förhandsversionen
    * Åtkomstaktivering för förhandsversion krävs en gång per prenumeration.
    * Åtkomst till förhandsversionen kan aktiveras via API, PowerShell eller CLI enligt beskrivningen i följande avsnitt.
1.  Skapa en virtuell dator från Azure Portal
    * Under förhandsversionen måste du komma igång med den här [länken.](https://aka.ms/AzureAutomanageHotPatch)
1.  Ange information om virtuell dator
    * Kontrollera att _Windows Server 2019 Datacenter: Azure Edition_ är valt i listrutan Avbildning)
    * På fliken Hantering rullar du ned till avsnittet "Uppdateringar av gästoperativsystem". Hotpatching är inställt på På och Korrigeringsinstallation som standard är Azure-dirigerad korrigering.
    * Metodtips för automatisk användning av virtuella datorer är aktiverade som standard
1. Skapa en ny virtuell dator

## <a name="enabling-preview-access"></a>Aktivera åtkomst till förhandsversionen

### <a name="rest-api"></a>REST-API

I följande exempel beskrivs hur du aktiverar förhandsversionen för din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd ```Register-AzProviderFeature``` cmdleten för att aktivera förhandsversionen för din prenumeration.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Använd ```az feature register``` för att aktivera förhandsversionen för din prenumeration.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Korrigeringsinstallation

Under förhandsversionen aktiveras [automatisk uppdatering av virtuella datorer](../virtual-machines/automatic-vm-guest-patching.md) automatiskt för alla virtuella datorer som skapas med Windows Server _2019 Datacenter: Azure Edition_. Med automatisk uppdatering av vm-gäst aktiverad:
* Korrigeringar som klassificeras som kritiska eller säkerhet hämtas automatiskt och tillämpas på den virtuella datorn.
* Korrigeringar tillämpas under tider med låg belastning i den virtuella datorns tidszon.
* Korrigeringsorkestrering hanteras av Azure och korrigeringar tillämpas enligt [tillgänglighetsprinciperna](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching).
* Hälsan för virtuella datorer, som bestäms via plattformens hälsosignaler, övervakas för att identifiera felkorrigeringar.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hur fungerar automatisk gästkorrigering av virtuella datorer?

När [Automatisk uppdatering av vm-gäst](../virtual-machines/automatic-vm-guest-patching.md) är aktiverat på en virtuell dator laddas de tillgängliga kritiska och säkerhetskorrigeringarna ned och tillämpas automatiskt. Den här processen startar automatiskt varje månad när nya korrigeringar släpps. Utvärdering och installation av korrigeringar görs automatiskt och processen omfattar att starta om den virtuella datorn efter behov.

Med Hotpatch aktiverat på _Windows Server 2019 Datacenter:_ Virtuella Azure Edition-datorer levereras de flesta månatliga säkerhetsuppdateringar som hotpatches som inte kräver omstarter. De senaste kumulativa uppdateringarna som skickas under planerade eller oplanerade baslinjemånader kräver omstart av den virtuella datorn. Ytterligare kritiska korrigeringar eller säkerhetskorrigeringar kan också vara tillgängliga regelbundet, vilket kan kräva omstart av den virtuella datorn.

Den virtuella datorn utvärderas automatiskt med några dagars och flera gånger inom en 30-dagarsperiod för att fastställa tillämpliga korrigeringar för den virtuella datorn. Den här automatiska utvärderingen säkerställer att eventuella korrigeringar som saknas identifieras så snart som möjligt.

Korrigeringar installeras inom 30 dagar efter de månatliga korrigeringsuppdateringarna, enligt [principerna för tillgänglighet först.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching) Korrigeringar installeras endast under tider med låg belastning för den virtuella datorn, beroende på den virtuella datorns tidszon. Den virtuella datorn måste köras under tider med låg belastning för att korrigeringar ska installeras automatiskt. Om en virtuell dator stängs av under en regelbunden utvärdering utvärderas den virtuella datorn och tillämpliga korrigeringar installeras automatiskt vid nästa periodiska utvärdering när den virtuella datorn är påslagen. Nästa periodiska utvärdering sker vanligtvis inom några dagar.

Definitionsuppdateringar och andra korrigeringar som inte klassificeras som kritiska eller säkerhet installeras inte via automatisk gästkorrigering av virtuella datorer.

## <a name="understanding-the-patch-status-for-your-vm"></a>Förstå korrigeringsstatusen för den virtuella datorn

Om du vill visa korrigeringsstatus för den virtuella datorn går du till avsnittet Gäst **- och** värduppdateringar för den virtuella datorn i Azure Portal. Under avsnittet **Uppdateringar av gästoperativsystem** klickar du på Gå till Hotpatch (förhandsversion) för att visa den senaste korrigeringsstatusen för den virtuella datorn.

På den här skärmen visas Status för Hotpatch för den virtuella datorn. Du kan också kontrollera om det finns några tillgängliga korrigeringar för den virtuella datorn som inte har installerats. Enligt beskrivningen i avsnittet "Korrigeringsinstallation" ovan installeras alla säkerhetsuppdateringar [](../virtual-machines/automatic-vm-guest-patching.md) och kritiska uppdateringar automatiskt på den virtuella datorn med hjälp av automatisk gästkorrigering av virtuella datorer och inga extra åtgärder krävs. Korrigeringar med andra uppdateringsklassificering installeras inte automatiskt. De visas i stället i listan över tillgängliga korrigeringar under fliken Uppdateringsefterlevnad. Du kan också visa historiken för uppdateringsdistributioner på den virtuella datorn via Uppdateringshistorik. Uppdateringshistorik från de senaste 30 dagarna visas tillsammans med information om korrigeringsinstallationen.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch Management.":::

Med automatisk uppdatering av vm-gäster utvärderas den virtuella datorn regelbundet och automatiskt för tillgängliga uppdateringar. Dessa periodiska utvärderingar säkerställer att tillgängliga korrigeringar identifieras. Du kan visa resultatet av utvärderingen på skärmen Uppdateringar ovan, inklusive tiden för den senaste utvärderingen. Du kan också välja att utlösa en korrigeringsbedömning på begäran för den virtuella datorn när som helst med alternativet Utvärdera nu och granska resultatet när utvärderingen har slutförts.

Precis som på begäran-utvärdering kan du även installera korrigeringar på begäran för din virtuella dator med hjälp av alternativet Installera uppdateringar nu. Här kan du välja att installera alla uppdateringar under specifika korrigeringsklassificeringar. Du kan också ange uppdateringar som ska inkluderas eller undantas genom att tillhandahålla en lista över enskilda kunskapsbasartiklar. Korrigeringar som installeras på begäran installeras inte med hjälp av principer för tillgänglighet först och kan kräva fler omstarter och stilleståndstid för virtuella datorer för uppdateringsinstallation.

## <a name="supported-updates"></a>Uppdateringar som stöds

Hotpatch omfattar Windows-säkerhet uppdateringar och upprätthåller paritet med innehållet i säkerhetsuppdateringar som utfärdats till i den vanliga Windows Update-kanalen (icke-Hotpatch).

Det finns några viktiga saker att tänka på när du kör en virtuell Windows Server Azure Edition-dator med Hotpatch aktiverat. Omstarter krävs fortfarande för att installera uppdateringar som inte ingår i Hotpatch-programmet. Omstarter krävs också regelbundet när en ny baslinje har installerats. Dessa omstarter håller den virtuella datorn synkroniserad med icke-säkerhetskorrigeringar som ingår i den senaste kumulativa uppdateringen.
* Korrigeringar som för närvarande inte ingår i Hotpatch-programmet inkluderar icke-säkerhetsuppdateringar som släppts för Windows och andra uppdateringar än Windows-uppdateringar (till exempel .NET-korrigeringar).  De här typerna av korrigeringar måste installeras under en baslinjemånad och kräver en omstart.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-is-hotpatching"></a>Vad är hotpatching?

* Hotpatching är ett nytt sätt att installera uppdateringar på ett Windows Server 2019 Datacenter: Azure Edition VM i Azure som inte kräver någon omstart efter installationen. Det fungerar genom att korrigera den minnesbaserade koden för processer som körs utan att behöva starta om processen.

### <a name="how-does-hotpatching-work"></a>Hur fungerar hotpatching?

* Hotpatching fungerar genom att upprätta en baslinje med en Windows Update Senaste kumulativa uppdatering och bygger sedan på baslinjen med uppdateringar som inte kräver någon omstart för att gälla.  Baslinjen uppdateras regelbundet med en ny kumulativ uppdatering. Den kumulativa uppdateringen innehåller alla säkerhets- och kvalitetsuppdateringar och kräver en omstart.

### <a name="why-should-i-use-hotpatch"></a>Varför ska jag använda Hotpatch?

* När du använder Hotpatch på Windows Server 2019 Datacenter: Azure Edition får den virtuella datorn högre tillgänglighet (färre omstarter) och snabbare uppdateringar (mindre paket som installeras snabbare utan att behöva starta om processer). Den här processen resulterar i en virtuell dator som alltid är uppdaterad och säker.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Vilka typer av uppdateringar omfattas av Hotpatch?

* Hotpatch omfattar för närvarande Säkerhetsuppdateringar för Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>När får jag den första Hotpatch-uppdateringen?

* Uppdateringar av hotpatch släpps vanligtvis den andra tisdagen varje månad. Mer information finns nedan.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hur kommer schemat för hotpatch att se ut?

* Hotpatching fungerar genom att upprätta en baslinje med en Windows Update senaste kumulativa uppdateringen och bygger sedan på den baslinjen med Hotpatch-uppdateringar som släpps varje månad.  I förhandsversionen släpps baslinjer med början var tredje månad. Se bilden nedan för ett exempel på ett årligt schema med tre månader (inklusive exempel på oplanerade baslinjer på grund av nolldagarskorrigeringar).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Schema för hotpatch-exempel.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Behövs omstarter fortfarande för en virtuell dator som registrerats i Hotpatch?

* Omstarter krävs fortfarande för att installera uppdateringar som inte ingår i Hotpatch-programmet och krävs regelbundet efter att en baslinje (Windows Update Senaste kumulativa uppdateringen) har installerats. Den här omstarten synkroniserar den virtuella datorn med alla korrigeringar som ingår i den kumulativa uppdateringen. Baslinjer (som kräver en omstart) börjar i en takt på tre månader och ökar med tiden.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Påverkas mina program när en Hotpatch-uppdatering installeras?

* Eftersom Hotpatch korrigerar minneskoden för processer som körs utan att behöva starta om processen påverkas inte dina program av korrigeringsprocessen. Observera att detta är separat från eventuella prestanda- och funktionskonsekvenser av själva korrigeringen.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Kan jag inaktivera Hotpatch på min virtuella dator?

* Du kan inaktivera Hotpatch på en virtuell dator via Azure Portal.  Om du inaktiverar Hotpatch avregistreras den virtuella datorn från Hotpatch, vilket återställer den virtuella datorn till ett typiskt uppdateringsbeteende för Windows Server.  När du avregistrerar från Hotpatch på en virtuell dator kan du registrera den virtuella datorn igen när nästa Hotpatch-baslinje släpps.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Kan jag uppgradera från mitt befintliga Windows Server-operativsystem?

* Uppgradering från befintliga versioner av Windows Server (det vill säga Windows Server 2016 eller 2019 som inte är Azure-versioner) stöds inte för närvarande. Uppgradering till framtida versioner av Windows Server Azure Edition stöds.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Kan jag använda Hotpatch för produktionsarbetsbelastningar i förhandsversionen?

* Förhandsversioner är endast avsedda för testning och inte för användning i produktionsmiljöer.

### <a name="will-i-be-charged-during-the-preview"></a>Kommer jag att debiteras under förhandsversionen?

* Licensen för Windows Server Azure Edition är kostnadsfri under förhandsversionen. Kostnaden för en underliggande infrastruktur som har ställts in för din virtuella dator (lagring, beräkning, nätverk osv.) debiteras dock fortfarande för din prenumeration.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hur kan jag få felsökningsstöd för Hotpatching?

* Du kan skapa en [supportbiljett för teknisk support.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) För alternativet Tjänst söker du efter och väljer Virtuell **dator som kör Windows** under Compute. Välj **Azure-funktioner** som problemtyp och **Automatisk uppdatering av vm-gäst** för problemundertypen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Uppdateringshantering [här](../automation/update-management/overview.md).
* Läs mer om automatisk gästkorrigering av virtuella datorer [här](../virtual-machines/automatic-vm-guest-patching.md)
