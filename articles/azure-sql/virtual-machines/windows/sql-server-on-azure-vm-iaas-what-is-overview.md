---
title: Översikt över SQL Server i Azure Windows Virtual Machines | Microsoft Docs
description: Lär dig hur du kör fullständiga versioner av SQL Server på Azure Virtual Machines i molnet utan att behöva hantera lokal maskin vara.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356811"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Vad är SQL Server på Azure Virtual Machines (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) kan du använda fullständiga versioner av SQL Server i molnet utan att behöva hantera lokal maskin vara. SQL Server virtuella datorer (VM: ar) fören klar också licens kostnaderna när du betalar per användning.

Virtuella Azure-datorer körs i många olika [geografiska regioner](https://azure.microsoft.com/regions/) runtom i världen. De också erbjuder även en mängd olika [datorstorlekar](../../../virtual-machines/sizes.md). I bildgalleriet för virtuella datorer kan du skapa en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Detta gör virtuella datorer till ett utmärkt alternativ för många olika SQL Server arbets belastningar.

Om du inte har använt SQL Server på virtuella Azure-datorer kan du titta närmare på den *SQL Server på Azures översikt över virtuella Azure-datorer* från vår djupgående [Azure SQL-video serie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Automatiserade uppdateringar

SQL Server på Azure Virtual Machines kan använda [Automatisk uppdatering](automated-patching.md) för att schemalägga en underhålls period för att installera viktiga Windows-och SQL Server uppdateringar automatiskt.

## <a name="automated-backups"></a>Automatiserade säkerhetskopieringar

SQL Server på Azure Virtual Machines kan dra nytta av [Automatisk säkerhets kopiering](automated-backup.md), som regelbundet skapar säkerhets kopior av databasen till Blob Storage. Du kan också använda den här tekniken manuellt. Mer information finns i [Använda Azure Storage för säkerhetskopiering och återställning av SQL Server](azure-storage-sql-server-backup-restore-use.md).

Azure erbjuder också en säkerhets kopierings lösning i företags klass för SQL Server som körs i virtuella Azure-datorer. En fullständigt hanterad säkerhets kopierings lösning som stöder Always on-tillgänglighetsgrupper, långsiktig kvarhållning, tidpunkts återställning och central hantering och övervakning. Mer information finns i [Azure Backup för SQL Server i virtuella Azure-datorer](../../../backup/backup-azure-sql-database.md).
  

## <a name="high-availability"></a>Hög tillgänglighet

Om du behöver hög tillgänglighet bör du överväga att konfigurera SQL Server-tillgänglighetsgrupper. Detta omfattar flera instanser av SQL Server på Azure Virtual Machines i ett virtuellt nätverk. Du kan konfigurera din hög tillgänglighets lösning manuellt, eller så kan du använda mallar i Azure Portal för automatisk konfiguration. En översikt över alla alternativ för hög tillgänglighet finns i [hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Prestanda

Virtuella datorer i Azure erbjuder olika datorstorlekar för att uppfylla olika arbetsbelastningar. SQL Server virtuella datorer ger också automatisk lagrings konfiguration, som är optimerad för dina prestanda krav. Mer information om hur du konfigurerar lagring för SQL Server virtuella datorer finns i [lagrings konfiguration för SQL Server virtuella datorer](storage-configuration.md). Du kan finjustera prestanda genom att se [metod tips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Kom igång med SQL Server virtuella datorer

För att komma igång väljer du en avbildning av en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server.

> [!TIP]
> Mer information om hur du förstår priser för SQL Server avbildningar finns i [pris vägledning för SQL Server på Azure-Virtual Machines](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Betala per användning
Följande tabell innehåller en matris med SQL Server-avbildningar som använder användningsbaserad betalning.

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Information om tillgängliga SQL Server på Linux avbildningar av virtuella datorer finns i [Översikt över SQL Server på Azure-Virtual Machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Det går nu att ändra så att licensieringsmodellen för en SQL Server VM som betalas per användning ska använda din egen licens. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Bring your own license
Du kan även använda din egen licens (Bring your own license, BYOL). I det här scenariot betalar du bara för den virtuella datorn utan ytterligare avgifter för SQL Server-licensiering.  När du använder din egen licens kan du spara pengar långsiktigt för kontinuerliga arbetsbelastningar under produktion. Kraven för att använda det här alternativet finns i [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md#byol) (Prisvägledning för virtuella SQL Server Azure-datorer).

Om du vill ta med din egen licens kan du antingen konvertera en befintlig SQL Server VM för betalning per användning eller distribuera en avbildning med hjälp av den fasta **{BYOL}**. Mer information om hur du byter licensierings modell mellan betala per användning och BYOL finns i [så här ändrar du licensierings modellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard  BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise-BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise-BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise-BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Det går att distribuera en äldre avbildning av SQL Server som inte är tillgänglig i Azure Portal med PowerShell. Om du vill visa alla tillgängliga avbildningar med hjälp av PowerShell använder du följande kommando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Mer information om hur du distribuerar virtuella SQL Server-datorer med hjälp av PowerShell finns i [How to provision SQL Server virtual machines with Azure PowerShell](create-sql-vm-powershell.md) (Etablera virtuella SQL Server-datorer med Azure PowerShell).


### <a name="connect-to-the-vm"></a>Anslut till VM:en
När du har skapat din virtuella SQL Server-dator ansluter du till den från ett program eller ett verktyg som SQL Server Management Studio (SSMS). Mer information finns i [ansluta till en SQL Server virtuell dator på Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrera dina data
Om du har en befintlig databas vill du flytta den till den nyligen etablerade SQL Server VM. En lista över migreringsalternativ och anvisningar finns i [Migrera en databas till SQL Server på en virtuell dator i Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Skapa och hantera Azure SQL-resurser med Azure Portal

Azure Portal tillhandahåller en enda sida där du kan hantera [alla dina Azure SQL-resurser](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) , inklusive dina virtuella SQL-datorer.

För att komma åt sidan **Azure SQL-resurser** väljer du **azure SQL** på Azure Portal-menyn eller söker efter och väljer **Azure SQL** från vilken sida som helst.

![Sök efter Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL är ett snabbt och enkelt sätt att få åtkomst till alla dina Azure SQL-databaser, elastiska pooler, logiska servrar, hanterade instanser och virtuella datorer. Azure SQL är inte en tjänst eller resurs. 

Om du vill hantera befintliga resurser väljer du önskat objekt i listan. Om du vill skapa nya Azure SQL-resurser väljer du **+ Lägg till**. 

![Skapa Azure SQL-resurs](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

När du har valt **+ Lägg till**, Visa ytterligare information om de olika alternativen genom att välja **Visa information** på valfri panel.

![panel information om databaser](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Mer information finns i:

- [Skapa en enkel databas](../../database/single-database-create-quickstart.md)
- [Skapa en elastisk pool](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Skapa en hanterad instans](../../managed-instance/instance-create-quickstart.md)
- [Skapa en virtuell SQL Server virtuell dator](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM uppdaterings princip för avbildning
Azure underhåller en avbildning av en virtuell dator för varje kombination av operativsystem, version och utgåva som stöds. Det betyder att avbildningar uppdateras med tiden och att äldre avbildningar tas bort. Mer information finns i avsnittet **Avbildningar** i [Virtuella SQL Server-datorer, vanliga frågor och svar](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>CEIP (Customer Experience Improvement Program)
CEIP (Customer Experience Improvement Program) är aktiverat som standard. Det skickar periodvisa rapporter till Microsoft för att hjälpa att förbättra SQL Server. Det krävs inga hanteringsaktiviteter för CEIP om du inte vill inaktivera den efter etablering. Du kan anpassa eller inaktivera CEIP genom att ansluta till den virtuella datorn med Fjärrskrivbord. Kör sedan verktyget **Fel- och användningsrapportering för SQL Server**. Inaktivera rapporteringen genom att följa anvisningarna. Mer information om datainsamling finns i [SQL Server-sekretesspolicyn](/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Relaterade produkter och tjänster
### <a name="windows-virtual-machines"></a>Virtuella Windows-datorer
* [Översikt över Azure Virtual Machines](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Storage
* [Introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Nätverk
* [Översikt över Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../../virtual-network/public-ip-addresses.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server dokumentation](/sql/index)
* [Jämförelse med Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med SQL Server på Azure Virtual Machines:

* [Skapa en virtuell SQL Server-dator på Azure Portal](sql-vm-create-portal-quickstart.md)

Få svar på vanliga frågor om SQL Server virtuella datorer:

* [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](frequently-asked-questions-faq.md)

Visa referens arkitekturer för att köra program på N-nivå på SQL Server i IaaS

* [Windows N-Tier-program på Azure med SQL Server](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Köra ett program på N-nivå i flera Azure-regioner för hög tillgänglighet](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)