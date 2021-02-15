---
title: Ändra licens modell för en virtuell SQL-dator i Azure
description: Lär dig hur du växlar licensiering för en SQL Server VM i Azure från betala per användning för att få en egen licens genom att använda Azure Hybrid-förmån.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361561"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Ändra licensmodellen för en virtuell SQL-dator i Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Den här artikeln beskriver hur du ändrar licens modellen för en SQL Server virtuell dator (VM) i Azure med hjälp av [SQL IaaS agent-tillägget](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Översikt

Det finns tre licens modeller för en virtuell Azure-dator som är värd för SQL Server: betala per användning, Azure Hybrid-förmån (AHB) och hög tillgänglighet/haveri beredskap (HA/DR). Du kan ändra licens modellen för dina SQL Server VM med hjälp av Azure Portal, Azure CLI eller PowerShell. 

- Modellen " **betala per** användning" innebär att kostnaden för den virtuella Azure-datorn per sekund inkluderar kostnaden för den SQL Server licensen.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) gör att du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 
- Licens typen **ha/Dr** används för den [kostnads fria ha/Dr-repliken](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) i Azure. 

Azure Hybrid-förmån tillåter användningen av SQL Server licenser med Software Assurance ("kvalificerad licens") på virtuella Azure-datorer. Med Azure Hybrid-förmån debiteras kunderna inte för att använda en SQL Server-licens på en virtuell dator. Men de måste fortfarande betala för kostnaden för den underliggande moln beräkningen (det vill säga bas priset), lagringen och säkerhets kopieringarna. De måste också betala för I/O som är kopplade till deras användning av tjänsterna (om det är tillämpligt).

Enligt Microsofts [produkt villkor](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS): "kunder måste ange att de använder Azure SQL Database (hanterad instans, Elastisk pool och enkel databas), Azure Data Factory, SQL Server Integration Services eller SQL Server Virtual Machines under Azure Hybrid-förmån för SQL Server när du konfigurerar arbets belastningar i Azure."

Om du vill ange användningen av Azure Hybrid-förmån för SQL Server på virtuell Azure-dator och är kompatibel, har du tre alternativ:

- Etablera en virtuell dator med hjälp av en egen licens SQL Server-avbildning från Azure Marketplace. Det här alternativet är endast tillgängligt för kunder som har en Enterprise-avtal.
- Etablera en virtuell dator med hjälp av en SQL Server avbildning som du betalar per användning från Azure Marketplace och aktivera Azure Hybrid-förmån.
- Själv installera SQL Server på Azure VM, registrera manuellt [med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md)och aktivera Azure Hybrid-förmån.

Licens typen för SQL Server kan konfigureras när den virtuella datorn är etablerad eller när som helst efteråt. Växling mellan licens modeller medför ingen nedtid, startar inte om den virtuella datorn eller tjänsten SQL Server, lägger inte till några ytterligare kostnader och börjar gälla omedelbart. Aktiverings Azure Hybrid-förmån *minskar* i själva verket kostnaderna.

## <a name="prerequisites"></a>Förutsättningar

Att ändra licensierings modellen för SQL Server VM har följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- Ett [SQL Server VM](./create-sql-vm-portal.md) registrerat med [SQL IaaS agent-tillägget](./sql-server-iaas-agent-extension-automate-management.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) är ett krav för att använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Ändra licensmodell

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra licens modellen direkt från portalen: 

1. Öppna [Azure Portal](https://portal.azure.com) och öppna [resursen SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) för din SQL Server VM. 
1. Välj **Konfigurera** under **Inställningar**. 
1. Välj alternativet **Azure Hybrid-förmån** och markera kryss rutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **Använd** längst ned på sidan **Konfigurera** . 

![Azure Hybrid-förmån i portalen](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI för att ändra din licens modell.  

Ange följande värden för **licens typ**:
- `AHUB` för Azure Hybrid-förmån
- `PAYG` för betala per användning
- `DR` så här aktiverar du den kostnads fria HA/DR-replikeringen


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda PowerShell för att ändra din licens modell.

Ange följande värden för **licens typ**:
- `AHUB` för Azure Hybrid-förmån
- `PAYG` för betala per användning
- `DR` så här aktiverar du den kostnads fria HA/DR-replikeringen


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Kommentarer

- Azure Cloud Solution Provider (CSP)-kunder kan använda Azure Hybrid-förmån genom att först distribuera en virtuell dator där du betalar per användning och sedan konvertera den till en egen licens om de har en aktiv Software Assurance.
- Om du släpper din virtuella SQL-dator-resurs går du tillbaka till den hårdkodade licens inställningen för avbildningen. 
- Möjligheten att ändra licens modellen är en funktion i SQL IaaS agent-tillägget. Genom att distribuera en Azure Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med tillägget. Men kunder som själv installerar SQL Server måste [registrera sina SQL Server VM](sql-agent-extension-manually-register-single-vm.md)manuellt. 
- Om du lägger till en SQL Server VM i en tillgänglighets uppsättning måste den virtuella datorn skapas på nytt. Det innebär att alla virtuella datorer som läggs till i en tillgänglighets uppsättning återgår till standard licens typen betala per användning. Azure Hybrid-förmån måste aktive ras igen. 


## <a name="limitations"></a>Begränsningar

Att ändra licens modellen är:
   - Endast tillgängligt för kunder med [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Stöds endast för standard-och Enterprise-utgåvorna av SQL Server. Licens ändringar för Express, webb och utvecklare stöds inte. 
   - Stöds endast för virtuella datorer som distribueras via Azure Resource Managers modellen. Virtuella datorer som distribueras via den klassiska modellen stöds inte. 
   - Endast tillgängligt för offentliga eller Azure Government moln. 

> [!Note]
> Endast SQL Server Core-baserad licensiering med Software Assurance-eller prenumerations licenser är berättigade till Azure Hybrid-förmån. Om du använder Server + CAL-licensiering för SQL Server och du har Software Assurance kan du använda en egen licens för en Azure SQL Server avbildning av virtuella datorer för att utnyttja License Mobility för dessa servrar, men du kan inte utnyttja de andra funktionerna i Azure Hybrid-förmån. 

## <a name="known-errors"></a>Kända fel

Granska de vanligaste felen och lösningarna. 

**Det gick inte att hitta resursen Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> under resurs gruppen \<resource-group> .**

Det här felet uppstår när du försöker ändra licens modellen på ett SQL Server VM som inte har registrerats med SQL Server IaaS agent-tillägg:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Du måste registrera din prenumeration med resurs leverantören och sedan [registrera SQL Server VM med agent tillägget för SQL-IaaS](sql-agent-extension-manually-register-single-vm.md). 



## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)
* [Översikt över SQL IaaS agent-tillägg](./sql-server-iaas-agent-extension-automate-management.md)
