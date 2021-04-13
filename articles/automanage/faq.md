---
title: Azure Automanage vanliga frågor och svar om virtuella datorer
description: Svar på vanliga frågor om Azure Automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363367"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Vanliga frågor och svar om Azure Automanage virtuella datorer

Den här artikeln innehåller svar på några av de vanligaste frågorna [om Azure Automanage för virtuella datorer](automanage-virtual-machines.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen [på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till Azure-supportsidan](https://azure.microsoft.com/support/options/)och väljer **Få support.**


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage för virtuella datorer

**Vilka är alla krav som krävs för att Azure Automanage?**

Följande är förutsättningar för att aktivera Azure Automanage:
- [Windows Server-versioner och](automanage-windows-server.md#supported-windows-server-versions) [Linux-distributioner som stöds](automanage-linux.md#supported-linux-distributions-and-versions)
- Virtuella datorer måste finnas i en region som stöds
- Användaren måste ha rätt behörigheter
- Virtuella datorer utan skalningsuppsättning
- Automanage stöder inte Sandbox-prenumerationer just nu

**Vilken Azure RBAC-behörighet krävs för att aktivera automatisk hantering?**

Om du aktiverar Automanage på en virtuell dator med ett befintligt konto för automatisk hantering måste du ha rollen Deltagare i resursgruppen där den virtuella datorn finns.

Om du använder ett nytt automanagekonto när du aktiverar det måste du antingen ha rollen Ägare eller ha rollen Deltagare + Administratör för användaråtkomst till prenumerationen.


**Vilka regioner stöds?**

En fullständig lista över regioner som stöds finns [här.](./automanage-virtual-machines.md#supported-regions)


**Vilka funktioner automatiseras Azure Automanage?**

Automanage registrerar, konfigurerar och övervakar under livscykeln för den virtuella datorn de tjänster som anges [här](automanage-virtual-machines.md).

**Fungerar Azure Automanage med Azure Arc-aktiverade virtuella datorer?**

Automanage stöder för närvarande inte Arc-aktiverade virtuella datorer.

**Kan jag anpassa konfigurationer på Azure Automanage?**

Kunder kan anpassa inställningar för specifika tjänster, till exempel Azure Backup kvarhållning, via konfigurationsinställningar. En fullständig lista över inställningar som kan ändras finns i vår dokumentation [här.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Fungerar Azure Automanage både virtuella Linux- och Windows-datorer?**

Ja, se Windows [Server-versioner som stöds](automanage-windows-server.md#supported-windows-server-versions) och [Linux-distributioner.](automanage-linux.md#supported-linux-distributions-and-versions)


**Kan jag tillämpa automanage selektivt på en uppsättning virtuella datorer?**

Automanage kan aktiveras med enkel klickning och peka på valda nya och befintliga virtuella datorer. Automanage kan också inaktiveras när som helst.


**Stöder Azure Automanage virtuella datorer i en VM-skalningsuppsättning?**

Nej, Azure Automanage stöder för närvarande inte virtuella datorer i en VM-skalningsuppsättning.


**Hur mycket kostar Azure Automanage kostnad?**

Azure Automanage tillgänglig utan extra kostnad i den offentliga förhandsversionen. Kopplade Azure-resurser, till Azure Backup, medför kostnader.


**Kan jag använda automanage via Azure Policy?**

Ja, vi har en inbyggd princip som automatiskt tillämpar automanage på alla virtuella datorer inom ditt definierade omfång. Du anger också miljökonfigurationen (DevTest eller Produktion) tillsammans med ditt automanagekonto. Läs mer om hur du aktiverar automanage via Azure Policy [här.](virtual-machines-policy-enable.md)


**Vad är ett automanagekonto?**

Automanage-kontot är en MSI (hanterad tjänstidentitet) som tillhandahåller säkerhetskontexten eller identiteten som de automatiserade åtgärderna utförs under.


**Påverkar det ytterligare virtuella datorer förutom de virtuella datorer som jag har valt när du aktiverar automanage?**

Om den virtuella datorn är länkad till en befintlig Log Analytics-arbetsyta återanvänder vi arbetsytan för att tillämpa följande lösningar: Ändringsspårning, Inventering och Uppdateringshantering. Alla virtuella datorer som är anslutna till arbetsytan kommer att ha dessa lösningar aktiverade.


**Kan jag ändra miljön för min virtuella dator?**

För stunden måste du inaktivera Automatisk manage för den virtuella datorn och sedan återaktivera Automanage med önskad miljö och inställningar.


**Om min virtuella dator redan har konfigurerats för en tjänst, Uppdateringshantering, kommer den då att konfigureras om automatiskt?**
Nej, automanage konfigurerar inte om den. Vi börjar övervaka de resurser som är associerade med tjänsten för avdrift.


**Varför har min virtuella dator statusen Misslyckad i portalen För automatisk användning?**

Om du ser statusen *Misslyckad* kan du felsöka distributionen på några olika sätt:
* Gå till **Resursgrupper,** välj din resursgrupp, klicka på **Distributioner** och se *statusen* Misslyckades där tillsammans med felinformation.
* Gå till **Prenumerationer,** välj din resursgrupp, klicka på **Distributioner** och se *statusen Misslyckades* där tillsammans med felinformation.
* Du kan också gå till aktivitetsloggen för en virtuell dator, som innehåller en post för "Skapa eller uppdatera tilldelningar av konfigurationsprofil". Detta kan också innehålla mer information om distributionen.

**Hur kan jag få felsökningssupport för automanage?**

Du kan skapa en [supportbiljett för teknisk support.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) För alternativet **Tjänst** söker du efter och väljer *Automanage* under *avsnittet Övervakning och* hantering.


## <a name="next-steps"></a>Nästa steg

Prova att aktivera Automanage för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)