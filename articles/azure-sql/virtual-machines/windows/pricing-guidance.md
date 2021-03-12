---
title: Pris vägledning & hanterings kostnader
description: Innehåller metod tips för att välja rätt SQL Server prissättnings modell för virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d980b92bc4effc58ef84ef6ec70c3908f575e484
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615609"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Pris vägledning för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller pris vägledning för [SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md). Det finns flera alternativ som påverkar kostnaden och det är viktigt att välja rätt bild som balanserar kostnader med affärs krav.

> [!TIP]
> Om du bara behöver ta reda på en kostnads uppskattning för en speciell kombination av SQL Servers version och storlek på virtuell dator (VM), kan du läsa sidan med priser för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Välj din plattform och SQL Server utgåva från listan **operativ system/program vara** .
>
> ![Användar gränssnitt på prissättnings sida för virtuell dator](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> Du kan också använda [pris kalkylatorn](https://azure.microsoft.com/pricing/#explore-cost) för att lägga till och konfigurera en virtuell dator. 

## <a name="free-licensed-sql-server-editions"></a>Gratis licensierade SQL Server-versioner

Om du vill utveckla, testa eller bygga ett koncept bevis kan du använda den fritt licensierade **SQL Server Developers versionen**. Den här versionen har alla funktioner i SQL Server Enterprise Edition, så att du kan bygga och testa alla typer av program. Du kan dock inte köra Developer Edition i produktion. En SQL Server Developer version av virtuella datorer debiteras bara för kostnaden för den virtuella datorn eftersom det inte finns några associerade SQL Server licens kostnader.

Om du vill köra en förenklad arbets belastning i produktion (<4 kärnor <1 GB minne, <10 GB/databas), använder du den fritt licensierade **SQL Server Express versionen**. En SQL Server Express version av VM debiteras också för kostnaden för den virtuella datorn.

För dessa arbets belastningar för utveckling/testning och lightweight-produktion kan du också spara pengar genom att välja en mindre VM-storlek som matchar dessa arbets belastningar. DS1v2 kan vara ett bra val i vissa situationer.

Information om hur du skapar en virtuell Azure-dator som kör SQL Server 2017 med någon av dessa avbildningar finns i följande länkar:

| Plattform | Fritt licensierade bilder |
|---|---|
| Windows Server 2016 | [SQL Server 2017-utvecklare virtuell Azure-dator](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017-utvecklare virtuell Azure-dator](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017-utvecklare virtuell Azure-dator](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017-utvecklare virtuell Azure-dator](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Betalade SQL Server-versioner

Om du har en icke-lättviktig produktions arbets belastning använder du någon av följande SQL Server-versioner:

| SQL Server-utgåva | Arbetsbelastning |
|-----|-----|
| Webb | Små webbplatser |
| Standard | Små till medel stora arbets belastningar |
| Stora företag | Stora eller verksamhets kritiska arbets belastningar|

Du har två alternativ att betala för SQL Server-licensiering för dessa versioner: *betala per användning* eller *ta med din egen licens (BYOL)*.

## <a name="pay-per-usage"></a>Betala per användning

Om du **betalar SQL Server licens per användning** (även kallat **betala** per användning) innebär det att kostnaden för den virtuella Azure-datorn per sekund omfattar kostnaden för SQL Server licensen. Du kan se prissättningen för de olika SQL Server-versionerna (Web, standard, Enterprise) på sidan med priser för Azure Virtual Machines för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Kostnaden är samma för alla versioner av SQL Server (2012 SP3 till 2019). Licens kostnaden per sekund beror på antalet VM-virtuella processorer.

Att betala SQL Server licensiering per användning rekommenderas för:

- **Tillfälliga eller periodiska arbets belastningar**. Till exempel en app som behöver stödja en händelse under ett par månader varje år eller affärs analys på måndagar.

- **Arbets belastningar med okänd livs längd eller skalning**. Till exempel en app som kanske inte krävs under några månader, eller som kan kräva mer eller mindre beräknings kraft, beroende på efter frågan.

Om du vill skapa en virtuell Azure-dator som kör SQL Server 2017 med någon av dessa avbildningar enligt principen betala per användning, se följande länkar:

| Plattform | Licensierade bilder |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> När du skapar en SQL Server virtuell dator i Azure Portal, visar fönstret **Välj en storlek** en uppskattad kostnad. Det är viktigt att Observera att denna uppskattning bara är beräknings kostnaderna för att köra den virtuella datorn tillsammans med eventuella OS-licensierings kostnader (Windows eller Linux-operativsystem från tredje part).
>
> ![Välj bladet VM-storlek](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Det omfattar inte ytterligare SQL Server licensierings kostnader för versionerna Web, standard och Enterprise. För att få den mest exakta pris beräkningen väljer du operativ systemet och SQL Server Edition på sidan med priser för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Nu kan du ändra licensierings modellen från betala per användning för att ta med din egen licens (BYOL) och tillbaka. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Bring your own license (BYOL)

**Genom att ta med din egen SQL Server licens via licensmobilitet**, som även kallas **BYOL**, innebär det att använda en befintlig SQL Server volym licens med Software Assurance i en virtuell Azure-dator. En SQL Server VM som bara använder BYOL avgifter för kostnaden för att köra den virtuella datorn, inte för SQL Server-licensiering, eftersom du redan har köpt licenser och Software Assurance via ett volym licensierings program eller via en moln lösnings partner (CSP).

> [!NOTE]
> BYOL-avbildningarna är för närvarande endast tillgängliga för virtuella Windows-datorer. Du kan dock installera SQL Server manuellt på en virtuell Linux-dator. Se rikt linjerna i [SQL Server på vanliga frågor och svar om Linux-datorer](../linux/frequently-asked-questions-faq.md).

Att ta med din egen SQL Server-licens via Licensmobilitet rekommenderas för:

- **Kontinuerliga arbets belastningar**. Till exempel en app som behöver stödja affärs åtgärder dygnet runt.

- **Arbets belastningar med känd livs längd och skala**. Till exempel en app som krävs för hela året och vilken efter frågan som har beräknats.

Om du vill använda BYOL med en SQL Server VM måste du ha en licens för SQL Server Standard eller företags-och [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), vilket är ett obligatoriskt alternativ genom vissa volym licensierings program och ett valfritt köp med andra. Den pris nivå som tillhandahålls via volym licensierings program varierar beroende på typ av avtal och kvantitet och eller åtagande att SQL Server. Men som en tumregel, har en egen licens för kontinuerliga produktions arbets belastningar följande fördelar:

| BYOL-förmån | Beskrivning |
|-----|-----|
| **Kostnadsbesparingar** | [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) erbjuder upp till 55% besparingar. Mer information finns i [switch Licensing Model](licensing-model-azure-hybrid-benefit-ahb-change.md) |
| **Fri passiv sekundär replik** | En annan fördel med att använda din egen licens är den [kostnads fria licensieringen för en passiv sekundär replik](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server för hög tillgänglighet. Detta klipper på hälften av licens kostnaden för en hög tillgänglig SQL Server distribution (till exempel med Always on Availability groups). Rättigheterna för att köra passiv sekundär tillhandahålls via Fail-Over servrar Software Assurance-förmånen. |

Om du vill skapa en virtuell Azure-dator som kör SQL Server 2017 med någon av de här framgångs licens avbildningarna, se de virtuella datorerna som har åtgärd ATS med {BYOL}:

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Meddela oss inom 10 dagar hur många SQL Server licenser du använder i Azure. Länkarna till föregående bilder innehåller instruktioner om hur du gör detta.

> [!NOTE]
> Nu kan du ändra licensierings modellen från betala per användning för att ta med din egen licens (BYOL) och tillbaka. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Minska kostnaderna

För att undvika onödiga kostnader väljer du en optimal storlek på den virtuella datorn och funderar på tillfälliga avstängningar för icke-kontinuerliga arbets belastningar.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> Den virtuella datorn har rätt storlek

Licens kostnaden för SQL Server är direkt relaterad till antalet virtuella processorer. Välj en VM-storlek som matchar dina förväntade behov för processor, minne, lagring och I/O-bandbredd. En fullständig lista över alternativ för dator storlek finns i [Windows VM-storlekar](../../../virtual-machines/sizes.md) och [virtuella Linux-storlekar](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Det finns nya maskin storlekar som fungerar bra med vissa typer av SQL Server arbets belastningar. De här datorernas storlekar behåller höga nivåer av minne, lagring och I/O-bandbredd, men de har lägre virtualiserat antal kärnor. Tänk till exempel följande exempel:

| Storlek på virtuell dator | Virtuella processorer | Minne | Maximalt antal diskar | Maximalt I/O-dataflöde | SQL Server licensierings kostnader | Totalkostnad (Compute + Licensing) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS eller 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 IOPS eller 768 MB/s | 75 % lägre | 57 % lägre |

> [!IMPORTANT]
> Det här är ett exempel på en tidpunkt. De senaste specifikationerna finns i artiklar om dator storlek och på sidan med Azure-priser för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) och [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

I det tidigare exemplet kan du se att specifikationerna för **Standard_DS14v2** och **Standard_DS14-4v2** är identiska förutom virtuella processorer. Suffixet **-4v2** i slutet av **Standard_DS14-4v2-** datorns storlek anger antalet aktiva virtuella processorer. Eftersom SQL Server licensierings kostnader är knutna till antalet virtuella processorer, minskar detta avsevärt kostnaden för den virtuella datorn i scenarier där de extra virtuella processorer inte behövs. Det här är ett exempel och det finns många maskin storlekar med begränsade virtuella processorer som identifieras med det här suffixet. Mer information finns i blogg inlägget [om nya Azure VM-storlekar för mer kostnads effektivt databas arbete](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Stäng av den virtuella datorn när det är möjligt

Om du använder en arbets belastning som inte körs kontinuerligt bör du överväga att stänga av den virtuella datorn under de inaktiva perioderna. Betala endast för det du använder.

Om du till exempel bara försöker använda SQL Server på en virtuell Azure-dator vill du inte betala avgifter genom att oavsiktligt lämna det som körs i veckor. En lösning är att använda [funktionen för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL Server VM automatisk avstängning](./media/pricing-guidance/sql-vm-auto-shutdown.png)

Automatisk avstängning är en del av en större uppsättning liknande funktioner som tillhandahålls av [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

För andra arbets flöden bör du överväga att automatiskt stänga av och starta om virtuella Azure-datorer med en skript lösning, till exempel [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Att stänga av och avallokera den virtuella datorn är det enda sättet att undvika avgifter. Om du bara stoppar eller använder energi alternativ för att stänga av den virtuella datorn debiteras det fortfarande användnings kostnader.

## <a name="next-steps"></a>Nästa steg

Allmän pris vägledning för Azure finns i [förhindra oväntade kostnader med fakturering och kostnads hantering i Azure](../../../cost-management-billing/cost-management-billing-overview.md). De senaste priserna för Azure Virtual Machines, inklusive SQL Server, finns på prissättnings sidan för Azure Virtual Machines för virtuella [Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) och [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

En översikt över SQL Server på Azure Virtual Machines finns i följande artiklar:

- [Översikt över SQL Server på virtuella Windows-datorer](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Översikt över SQL Server på Linux virtuella datorer](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
