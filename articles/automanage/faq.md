---
title: Vanliga frågor och svar om Azure automanage för Virtual Machines
description: Svar på vanliga frågor om Azure automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0b4e116210cf68dc672122ad4ddc98f85067f3b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688016"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Vanliga frågor och svar om Azure automanage för virtuella datorer

Den här artikeln innehåller svar på några av de vanligaste frågorna om [Azure automanage för virtuella datorer](automanage-virtual-machines.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka in en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du **Hämta support** på [Sidan Support för Azure](https://azure.microsoft.com/support/options/).


## <a name="azure-automanage-for-virtual-machines"></a>Azure automanage för virtuella datorer

**Vilka är alla krav som krävs för att aktivera Azure automanage?**

Följande är förutsättningar för att aktivera Azure automanage:
- [Windows Server-versioner](automanage-windows-server.md#supported-windows-server-versions) och [Linux-distributioner](automanage-linux.md#supported-linux-distributions-and-versions) som stöds
- Virtuella datorer måste finnas i en region som stöds
- Användaren måste ha rätt behörighet
- Endast icke-skala uppsättning virtuella datorer
- Automanage stöder inte sandbox-prenumerationer just nu

**Vilken Azure RBAC-behörighet krävs för att aktivera autohantering?**

Om du aktiverar automanage på en virtuell dator med ett befintligt konto för autohantering, behöver du deltagar rollen till resurs gruppen där den virtuella datorn finns.

Om du använder ett nytt automanage-konto när du aktiverar måste du antingen ha ägar rollen eller ha rollen deltagare och administratör för användar åtkomst till prenumerationen.


**Vilka regioner stöds?**

En fullständig lista över regioner som stöds finns [här](./automanage-virtual-machines.md#supported-regions).


**Vilka funktioner hanterar Azure automatiskt?**

Hantera automatiskt registrering, konfiguration och övervakning under hela livs cykeln för den virtuella datorn de tjänster som listas [här](automanage-virtual-machines.md).

**Fungerar Azure automanage med Azure Arc-aktiverade virtuella datorer?**

Autohantering stöder för närvarande inte Arc-aktiverade virtuella datorer.

**Kan jag anpassa konfigurationer på Azure automanage?**

Kunder kan anpassa inställningar för vissa tjänster, t. ex. Azure Backup kvarhållning, via konfigurations inställningar. En fullständig lista över inställningar som kan ändras finns i vår dokumentation [här](automanage-virtual-machines.md#customizing-an-environment-using-preferences).


**Fungerar Azure autohantering med både virtuella Linux-och Windows-datorer?**

Ja, se de [Windows Server-versioner](automanage-windows-server.md#supported-windows-server-versions) och [Linux-distributioner](automanage-linux.md#supported-linux-distributions-and-versions)som stöds.


**Kan jag selektivt tillämpa autohantering endast på en uppsättning virtuella datorer?**

Funktionen Hantera kan aktive ras med Klicka och punkt-enkelhet på valda nya och befintliga virtuella datorer. Den automatiska hanteringen kan också inaktive ras när som helst.


**Stöder Azure automatiska hantering av virtuella datorer i en skalnings uppsättning för virtuella datorer?**

Nej, Azure automanage stöder för närvarande inte virtuella datorer i en skalnings uppsättning för virtuella datorer.


**Hur mycket hanterar Azure autokostnaden?**

Automatisk Azure-hantering är tillgängligt utan extra kostnad i den offentliga för hands versionen. Anslutna Azure-resurser, till exempel Azure Backup, kommer att kosta.


**Kan jag använda automanage via Azure policy?**

Ja, vi har en inbyggd princip som automatiskt tillämpar automatisk hantering för alla virtuella datorer i det definierade omfånget. Du kommer också att ange miljö konfigurationen (DevTest eller produktion) tillsammans med ditt konto för automatisk hantering. Läs mer om hur du aktiverar autohantering via Azure policy [här](virtual-machines-policy-enable.md).


**Vad är ett konto för autohantering?**

Kontot för automatisk hantering är en MSI (Hanterad tjänstidentitet) som tillhandahåller säkerhets kontexten eller den identitet under vilken de automatiserade åtgärderna sker.


**Påverkas eventuella ytterligare virtuella datorer förutom de virtuella datorer jag valt när de aktiverar autohantering?**

Om den virtuella datorn är länkad till en befintlig Log Analytics arbets yta, kommer vi att återanvända den arbets ytan för att tillämpa dessa lösningar: Ändringsspårning, inventering och Uppdateringshantering. Alla virtuella datorer som är anslutna till den arbets ytan kommer att ha dessa lösningar aktiverade.


**Kan jag ändra miljön för min virtuella dator?**

För tillfället måste du inaktivera autohantering för den virtuella datorn och sedan återaktivera autohantering med önskad miljö och preferenser.


**Om min virtuella dator redan har kon figurer ATS för en tjänst, t. ex. Uppdateringshantering, kommer automatiskt att hantera den automatiskt?**
Nej, den automatiska hanteringen kommer inte att konfigurera om den. Vi börjar övervaka resurserna som är kopplade till tjänsten för drift.


**Varför har den virtuella datorn statusen Misslyckad i automanage-portalen?**

Om du ser status som *misslyckad* kan du felsöka distributionen via resurs gruppen som den virtuella datorn finns i. Gå till **resurs grupper**, välj din resurs grupp, klicka på **distributioner** och se statusen *misslyckades* där, med fel information.

**Hur kan jag få hjälp med fel sökning av autohantering?**

Du kan använda en [ärende biljett för teknisk support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). För **tjänst** alternativet söker du efter och väljer *hantera automatisk hantering* under avsnittet *övervakning och hantering* .


## <a name="next-steps"></a>Nästa steg

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)