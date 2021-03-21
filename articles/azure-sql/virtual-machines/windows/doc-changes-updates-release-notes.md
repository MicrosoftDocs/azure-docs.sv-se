---
title: Dokumentations ändringar för SQL Server på Azure Virtual Machines
description: Lär dig mer om de nya funktionerna och förbättringarna för olika versioner av SQL Server på Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325731"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Dokumentations ändringar för SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Med Azure kan du distribuera en virtuell dator (VM) med en avbildning av SQL Server inbyggd. Den här artikeln sammanfattar dokumentations ändringarna som är associerade med nya funktioner och förbättringar i de senaste versionerna av [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="october-2020"></a>Oktober 2020

| Ändringar | Information |
| --- | --- |
| **DNN för AG** | Nu kan du konfigurera ett [distribuerat nätverks namn (DNN)-lyssnare)](availability-group-distributed-network-name-dnn-listener-configure.md) för SQL Server 2019 CU8 och senare ersätta den traditionella [VNN-lyssnaren](availability-group-overview.md#connectivity)genom att negera behovet av ett Azure Load Balancer.   | 

## <a name="september-2020"></a>September 2020

| Ändringar | Information |
| --- | --- |
| **Automatisk tilläggs registrering** | Nu kan du aktivera funktionen för [automatisk registrering](sql-agent-extension-automatic-registration-all-vms.md) för att automatiskt registrera alla SQL Server virtuella datorer som redan har distribuerats till din prenumeration med [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). Detta gäller för alla befintliga virtuella datorer och registrerar också automatiskt alla SQL Server virtuella datorer som lagts till i framtiden.   | 


## <a name="august-2020"></a>Augusti 2020

| Ändringar | Information |
| --- | --- |
| **Konfigurera AG i portalen** | Nu kan du [Konfigurera din tillgänglighets grupp via Azure Portal](availability-group-azure-portal-configure.md). Den här funktionen är för närvarande en för hands version och distribueras så om din önskade region inte är tillgänglig, kom tillbaka snart. | 


## <a name="july-2020"></a>Juli 2020


| Ändringar | Information |
| --- | --- |
| **Migrera logg till Ultra disk** | Lär dig hur du kan [migrera logg filen till en extremt disk](storage-migrate-to-ultradisk.md) för att utnyttja hög prestanda och låg latens. | 
| **Skapa AG med Azure PowerShell** | Nu är det möjligt att förenkla skapandet av en tillgänglighets grupp med hjälp av [Azure PowerShell](availability-group-az-commandline-configure.md) och Azure CLI. | 


## <a name="june-2020"></a>Juni 2020

| Ändringar | Information |
| --- | --- |
| **Namn på distribuerat nätverk (DNN)** | SQL Server 2019 i Windows Server 2016 + för hands versions hantering av trafik till din FCI (failover Cluster instance) med hjälp av ett [distribuerat nätverks namn](./failover-cluster-instance-distributed-network-name-dnn-configure.md) i stället för att använda Azure Load Balancer. Detta stöd fören klar och effektiviserar anslutningen till din lösning för hög tillgänglighet (HA) i Azure. | 
| **FCI med Azure delade diskar** | Nu kan du distribuera din instans av [redundanskluster (FCI)](failover-cluster-instance-overview.md) med hjälp av [Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Omorganisera FCI-dokument** | Dokumentationen runt [kluster instanserna för redundanskluster med SQL Server på virtuella Azure-datorer](failover-cluster-instance-overview.md) har skrivits om och organiserats om för tydlighets skull. Vi har avgränsat en del konfigurations innehåll, t. ex. [metod tips för kluster konfiguration](hadr-cluster-best-practices.md), hur du förbereder en [virtuell dator för en SQL Server FCI](failover-cluster-instance-prepare-vm.md)och hur du konfigurerar [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Maj 2020 

| Ändringar | Information |
| --- | --- |
| **Azure SQL-serien** | SQL Server på Azure Virtual Machines är nu en del av [produkter i Azure SQL-serien](../../azure-sql-iaas-vs-paas-what-is-overview.md). Kolla vårt [nya utseende](../index.yml)! Ingenting har ändrats i produkten, men dokumentationen syftar till att göra Azure SQL-produktnyckeln enklare. | 


## <a name="january-2020"></a>Januari 2020

| Ändringar | Information |
| --- | --- |
| **Azure Government support** | Nu är det möjligt att registrera SQL Server virtuella datorer med SQL IaaS agent-tillägget för virtuella datorer som finns i [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) molnet. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Ändringar | Information |
 --- | --- |
| **Kostnads fri DR-replikering i Azure** | Du kan vara värd för en [kostnads fri passiv instans](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) för haveri beredskap i Azure för din lokala SQL Server instans om du har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Mass registrering av SQL IaaS-tillägg** | Du kan nu [Mass registrera](sql-agent-extension-manually-register-vms-bulk.md) SQL Server virtuella datorer med [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). | 
|**Prestandaoptimerad lagrings konfiguration** | Du kan nu [helt anpassa lagrings konfigurationen](storage-configuration.md#new-vms) när du skapar en ny SQL Server VM. |
|**Premium-filresurs för FCI** | Nu kan du skapa en instans av ett kluster för växling vid fel med hjälp av en [Premium-filresurs](failover-cluster-instance-premium-file-share-manually-configure.md) i stället för den ursprungliga [Lagringsdirigerings](failover-cluster-instance-storage-spaces-direct-manually-configure.md)metoden. 
| **Dedikerad Azure-värd** | Du kan köra din SQL Server VM på den [dedikerade Azure-värden](dedicated-host.md). | 
| **SQL Server VM migrering till en annan region** | Använd Azure Site Recovery för att [migrera SQL Server VM från en region till en annan](move-sql-vm-different-region.md). |
|  **Nya installations lägen för SQL-IaaS** | Nu är det möjligt att installera SQL Server IaaS-tillägget i [lättviktigt läge](sql-server-iaas-agent-extension-automate-management.md) för att undvika att starta om tjänsten SQL Server.  |
| **Ändring av SQL Servers utgåva** | Nu kan du ändra [egenskapen version](change-sql-server-edition.md) för SQL Server VM. |
| **Ändringar av SQL IaaS agent-tillägget** | Du kan [Registrera dina SQL Server VM med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md) med hjälp av de nya SQL-IaaS lägena. Den här funktionen omfattar [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) -avbildningar.|
| **Hämta egna licens bilder med hjälp av Azure Hybrid-förmån** | Med en egen licens avbildning som distribueras från Azure Marketplace kan du nu byta [licens typ till betala per](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)användning.| 
| **Ny SQL Server VM hantering i Azure Portal** | Det finns nu ett sätt att hantera dina SQL Server VM i Azure Portal. Mer information finns i [hantera SQL Server virtuella datorer i Azure Portal](manage-sql-vm-portal.md).  | 
| **Utökat stöd för SQL Server 2008 och 2008 R2** | [Utöka stödet](sql-server-2008-extend-end-of-support.md) för SQL Server 2008 och SQL Server 2008 R2 genom att migrera *som* en virtuell Azure-dator. | 
| **Anpassad avbildnings support** | Nu kan du installera [SQL Server IaaS-tillägget](sql-server-iaas-agent-extension-automate-management.md#installation) för anpassade OS-och SQL Server-avbildningar, som erbjuder begränsade funktioner i [flexibel licensiering](licensing-model-azure-hybrid-benefit-ahb-change.md). När du registrerar en anpassad avbildning med SQL IaaS agent-tillägget anger du licens typen "AHUB". Annars Miss kommer registreringen. | 
| **Stöd för namngiven instans** | Du kan nu använda [tillägget SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) med en namngiven instans, om standard instansen har avinstallerats på rätt sätt. | 
| **Portal förbättring** | Azure Portals upplevelsen för att distribuera en SQL Server VM har förbättringar för att förbättra användbarhet. Mer information finns i [snabb start](sql-vm-create-portal-quickstart.md) och mer utförlig [instruktions guide](create-sql-vm-portal.md) för att distribuera en SQL Server VM.|
| **Portal förbättring** | Det är nu möjligt att ändra licensierings modellen för en SQL Server VM från betala per användning för att få en egen licens genom att använda [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model).|
| **Förenkling av tillgänglighets grupps distribution till en SQL Server VM via Azure CLI** | Nu är det enklare än någonsin att distribuera en tillgänglighets grupp till en SQL Server VM i Azure. Du kan använda [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) för att skapa Windows-redundanskluster, en intern belastningsutjämnare och tillgänglighets grupps lyssnare, allt från kommando raden. Mer information finns i [använda Azure CLI för att konfigurera en Always on-tillgänglighets grupp för SQL Server på en virtuell Azure-dator](./availability-group-az-commandline-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Ändringar | Information |
| --- | --- |
|  **Ny resurs leverantör för ett SQL Server kluster** | En ny Resource Provider (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definierar metadata för Windows-redundansklustret. När du ansluter en SQL Server VM till *SqlVirtualMachineGroups* startar du WSFC-tjänsten (Windows Server failover Cluster) och ansluter den virtuella datorn till klustret.  |
| **Automatiserad installation av en tillgänglighets grupps distribution med Azure snabb starts mallar** |Nu kan du skapa Windows-redundansklustret, ansluta SQL Server virtuella datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med hjälp av två Azure snabb starts mallar. Mer information finns i [använda Azures snabb starts mallar för att konfigurera en tillgänglighets grupp som alltid är tillgänglig för SQL Server på en virtuell Azure-dator](availability-group-quickstart-template-configure.md). | 
| **Automatisk registrering för SQL IaaS agent-tillägget** | SQL Server virtuella datorer som distribueras efter den här månaden registreras automatiskt med det nya SQL IaaS agent-tillägget. SQL Server virtuella datorer som distribuerats före den här månaden behöver registreras manuellt. Mer information finns i [Registrera en SQL Server virtuell dator i Azure med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md).|
|**Nytt SQL IaaS agent-tillägg** |  En ny Resource Provider (Microsoft. SqlVirtualMachine) ger bättre hantering av dina SQL Server virtuella datorer. Mer information om hur du registrerar dina virtuella datorer finns i [Registrera en SQL Server virtuell dator i Azure med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md). |
|**Växla licensierings modell** | Nu kan du växla mellan modellerna betala per användning och hämta egna licenser för din SQL Server VM med hjälp av Azure CLI eller PowerShell. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server virtuell dator i Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Etablera SQL Server på en virtuell Windows-dator](create-sql-vm-portal.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](migrate-to-vm-from-sql-server.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Metod tips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Program mönster och utvecklings strategier för SQL Server på Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Etablera SQL Server på en virtuell Linux-dator](../linux/sql-vm-create-portal-quickstart.md)
* [Vanliga frågor och svar (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server på Linux dokumentation](/sql/linux/sql-server-linux-overview)