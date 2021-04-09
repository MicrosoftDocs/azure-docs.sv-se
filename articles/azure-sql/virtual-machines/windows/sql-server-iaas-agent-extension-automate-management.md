---
title: Vad är SQL Server IaaS agent extension?
description: Den här artikeln beskriver hur SQL Server IaaS agent-tillägget hjälper till att automatisera hanteringen av vissa administrations uppgifter för SQL Server på virtuella Azure-datorer. Detta inkluderar funktioner som automatisk säkerhets kopiering, automatiserad uppdatering, Azure Key Vault integrering, licensierings hantering, lagrings konfiguration och central hantering av alla SQL Server VM instanser.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225497"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatisera hanteringen med SQL Server IaaS agent-tillägg
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS agent Extension (SqlIaasExtension) körs på SQL Server på Azure-Virtual Machines (VM) för att automatisera hanterings-och administrations uppgifter. 

Den här artikeln innehåller en översikt över tillägget. Om du vill installera SQL Server IaaS-tillägget till SQL Server på virtuella Azure-datorer kan du läsa artiklarna för [Automatisk installation](sql-agent-extension-automatic-registration-all-vms.md), [enskilda virtuella datorer](sql-agent-extension-manually-register-single-vm.md)eller [virtuella datorer i bulk](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Översikt

Med tillägget SQL Server IaaS-agent kan du integrera med Azure Portal och, beroende på hanterings läget, låsa upp ett antal funktions förmåner för SQL Server på virtuella Azure-datorer: 

- **Funktions förmåner**: tillägget låser upp ett antal funktioner för automatiserings funktioner, till exempel portal hantering, licens flexibilitet, automatisk säkerhets kopiering, automatiserad uppdatering och mer. Mer information finns i [funktions förmånerna](#feature-benefits) längre fram i den här artikeln. 

- **Kompatibilitet**: tillägget ger en förenklad metod för att uppfylla kravet på att meddela Microsoft att Azure Hybrid-förmån har Aktiver ATS som anges i produkt villkoren. Den här processen är en negation som behöver hantera licens registrerings formulär för varje resurs.  

- **Kostnads fri**: tillägget i alla tre hanterbarhets lägen är helt kostnads fritt. Det finns ingen ytterligare kostnad som är kopplad till tillägget eller med ändring av hanterings lägen. 

- **Förenklad licens hantering**: tillägget fören klar SQL Server licens hantering, och gör att du snabbt kan identifiera SQL Server virtuella datorer med Azure Hybrid-förmån aktiverat med hjälp av [Azure Portal](manage-sql-vm-portal.md), PowerShell eller Azure CLI: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS agent-tillägget samlar in data i Express syfte att ge kunderna valfria förmåner när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licens granskningar utan kundens medgivande. Se [SQL Server sekretess tillägg](/sql/sql-server/sql-server-privacy#non-personal-data) för mer information.


## <a name="feature-benefits"></a>Funktions förmåner 

Tillägget SQL Server IaaS agent låser upp ett antal funktions förmåner för att hantera din SQL Server VM. 

Följande tabell innehåller information om dessa fördelar: 


| Funktion | Beskrivning |
| --- | --- |
| **Portalhantering** | Låser upp [hantering i portalen](manage-sql-vm-portal.md), så att du kan visa alla dina SQL Server virtuella datorer på en plats, och så att du kan aktivera och inaktivera SQL-funktioner direkt från portalen. <br/> Hanterings läge: lightweight & full|  
| **Automatisk säkerhets kopiering** |Automatisera schemaläggning av säkerhets kopieringar för alla databaser antingen för standard instansen eller en [korrekt installerad](frequently-asked-questions-faq.md#administration) namngiven instans av SQL Server på den virtuella datorn. Mer information finns i [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines (Resource Manager)](automated-backup-sql-2014.md). <br/> Hanterings läge: fullständigt|
| **Automatiserad uppdatering** |Konfigurerar en underhålls period då viktiga Windows-och SQL Server säkerhets uppdateringar till den virtuella datorn kan ske, så att du kan undvika uppdateringar under hög belastnings tider för din arbets belastning. Mer information finns i [automatiserad uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](automated-patching.md). <br/> Hanterings läge: fullständigt|
| **Azure Key Vault-integrering** |Gör att du kan installera och konfigurera Azure Key Vault automatiskt på din SQL Server VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure-Virtual Machines (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Hanterings läge: fullständigt|
| **Visa disk användning i portalen** | Gör att du kan visa en grafisk representation av disk användningen av dina SQL-datafiler i Azure Portal.  <br/> Hanterings läge: fullständigt | 
| **Flexibel licensiering** | Spara pengar genom [sömlös över gång](licensing-model-azure-hybrid-benefit-ahb-change.md) från den tillkommande licensen (även kallat Azure Hybrid-förmån) till licens modellen betala per användning och tillbaka igen. <br/> Hanterings läge: lightweight & full| 
| **Flexibel version/utgåva** | Om du väljer att ändra [versionen](change-sql-server-version.md) [eller versionen](change-sql-server-edition.md) av SQL Server kan du uppdatera metadata i Azure Portal utan att behöva distribuera om hela SQL Server VM.  <br/> Hanterings läge: lightweight & full| 


## <a name="management-modes"></a>Hanterings lägen

Du kan välja att registrera ditt SQL IaaS-tillägg i tre hanterings lägen: 

- Med alternativet **Lightweight** mode kopieras binärfiler till den virtuella datorn, men agenten installeras inte och den SQL Server tjänsten startas inte om. Lightweight-läge stöder bara ändring av licens typ och version av SQL Server och ger begränsad Portal hantering. Använd det här alternativet för SQL Server virtuella datorer med flera instanser eller de som ingår i en instans av en redundanskluster (FCI). Läge för förenklad hantering är standard hanterings läget när du använder funktionen för [automatisk registrering](sql-agent-extension-automatic-registration-all-vms.md) , eller när en hanterings typ inte anges vid manuell registrering. Det går inte att påverka minne eller CPU när du använder Lightweight-läge och det finns ingen associerad kostnad. Vi rekommenderar att du först registrerar SQL Server VM i Lightweight-läge och sedan uppgraderar till fullständigt läge under en schemalagd underhålls period. 

- I **fullständigt** läge installeras SQL IaaS-agenten på den virtuella datorn för att leverera alla funktioner, men kräver omstart av SQL Server tjänst-och system administratörs behörighet. Använd den för att hantera en SQL Server VM med en enda instans. I fullständigt läge installeras två Windows-tjänster som har minimal påverkan på minne och CPU – dessa kan övervakas via aktivitets hanteraren. Det finns ingen kostnad för att använda läget fullständig hanterbarhet. 

- **Noagent** -läge är dedikerat till SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Det påverkar inte minne eller CPU när du använder noagent-läge. Det finns ingen kostnad för att använda noagent Managed mode, SQL Server startas inte om och en agent installeras inte på den virtuella datorn. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten genom att använda Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Installation

Registrera SQL Server VM med SQL Server IaaS agent-tillägget för att skapa den **virtuella SQL-datorns** _resurs_ i din prenumeration, som är en _separat_ resurs från den virtuella dator resursen. Om du avregistrerar SQL Server VM från tillägget tas den **virtuella SQL-datorns** _resurs_ bort, men den faktiska virtuella datorn tas inte bort.

Genom att distribuera en SQL Server VM Azure Marketplace-avbildning via Azure Portal registreras automatiskt SQL Server VM med tillägget. Men om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hård disk måste du registrera din SQL Server VM med SQL IaaS-tillägget för att kunna låsa upp funktions fördelarna. 

Genom att registrera tillägget i Lightweight-läge kopieras binärfilerna men installerar inte agenten på den virtuella datorn. Agenten installeras på den virtuella datorn när tillägget uppgraderas till fullständigt hanterings läge. 

Det finns tre sätt att registrera med tillägget: 
- [Automatiskt för alla aktuella och framtida virtuella datorer i en prenumeration](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuellt för en enskild virtuell dator](sql-agent-extension-manually-register-single-vm.md)
- [Manuellt för flera virtuella datorer i grupp](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Stöd för namngivna instanser

Tillägget SQL Server IaaS agent fungerar med en namngiven instans av SQL Server om det är den enda SQL Server instans som är tillgänglig på den virtuella datorn. Tillägget kan inte installeras på virtuella datorer som har flera namngivna SQL Server instanser om det inte finns någon standard instans på den virtuella datorn. 

Om du vill använda en namngiven instans av SQL Server distribuerar du en virtuell Azure-dator, installerar en enda namngiven SQL Server-instans och registrerar den sedan med [SQL IaaS-tillägget](sql-agent-extension-manually-register-single-vm.md).

Du kan också använda en namngiven instans med en Azure Marketplace SQL Server-avbildning genom att följa dessa steg: 

   1. Distribuera en SQL Server VM från Azure Marketplace. 
   1. [Avregistrera](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server VM från SQL IaaS agent-tillägget. 
   1. Avinstallera SQL Server helt i SQL Server VM.
   1. Installera SQL Server med en namngiven instans i SQL Server VM. 
   1. [Registrera den virtuella datorn med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Verifiera tilläggets status

Använd Azure Portal eller Azure PowerShell för att kontrol lera status för tillägget. 

### <a name="azure-portal"></a>Azure Portal

Kontrol lera att tillägget är installerat i Azure Portal. 

Välj **alla inställningar** i fönstret virtuell dator och välj sedan **tillägg**. Du bör se **SqlIaasExtension** -tillägget som visas.

![Status för SQL Server IaaS agent extension i Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Du kan också använda cmdleten **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Föregående kommando bekräftar att agenten är installerad och innehåller allmän statusinformation. Du kan hämta detaljerad statusinformation om automatisk säkerhets kopiering och korrigering med hjälp av följande kommandon:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Begränsningar

SQL IaaS agent-tillägget stöder bara: 

- SQL Server virtuella datorer som distribueras via Azure Resource Manager. SQL Server virtuella datorer som distribueras via den klassiska modellen stöds inte. 
- SQL Server virtuella datorer som distribueras till det offentliga molnet eller Azure Government molnet. Distributioner till andra privata eller offentliga moln stöds inte. 


## <a name="in-region-data-residency"></a>Placering för region data
Virtuell Azure SQL-dator och tillägget SQL IaaS-agent flyttar inte eller lagrar inte kunddata från den region där de distribueras.

## <a name="next-steps"></a>Nästa steg

Om du vill installera SQL Server IaaS-tillägget till SQL Server på virtuella Azure-datorer kan du läsa artiklarna för [Automatisk installation](sql-agent-extension-automatic-registration-all-vms.md), [enskilda virtuella datorer](sql-agent-extension-manually-register-single-vm.md)eller [virtuella datorer i bulk](sql-agent-extension-manually-register-vms-bulk.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server för azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Mer information finns i [vanliga frågor och svar](frequently-asked-questions-faq.md). 
