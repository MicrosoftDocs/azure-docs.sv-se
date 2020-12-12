---
title: Automatisk registrering med SQL IaaS agent-tillägg
description: Lär dig hur du aktiverar funktionen för automatisk registrering för att automatiskt registrera alla tidigare och framtida SQL Server virtuella datorer med SQL IaaS agent-tillägget med hjälp av Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 1ef7943586123a1870ed9a2d0c21aa8b5fd38c1c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360007"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatisk registrering med SQL IaaS agent-tillägg
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Aktivera funktionen för automatisk registrering i Azure Portal att automatiskt registrera alla aktuella och framtida SQL Server på Azure-Virtual Machines (VM) med [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md) i lättviktigt läge. 

I den här artikeln lär du dig att aktivera funktionen för automatisk registrering. Alternativt kan du [Registrera en enskild virtuell dator](sql-agent-extension-manually-register-single-vm.md)eller [Registrera dina virtuella datorer i bulk](sql-agent-extension-manually-register-vms-bulk.md) med SQL IaaS agent-tillägget. 

## <a name="overview"></a>Översikt

Att registrera din SQL Server VM med [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md) för att låsa upp en fullständig funktions uppsättning av förmåner. 

När automatisk registrering har Aktiver ATS körs ett jobb dagligen för att identifiera om SQL Server har installerats på alla oregistrerade virtuella datorer i prenumerationen. Det gör du genom att kopiera binärfilerna för SQL IaaS-agenttjänsten till den virtuella datorn och sedan köra ett engångs verktyg som söker efter registrerings data filen SQL Server. Om SQL Server Hive identifieras, registreras den virtuella datorn med tillägget i lättviktigt läge. Om det inte finns någon SQL Server Hive i registret tas binärfilerna bort.

När automatisk registrering har Aktiver ATS för en prenumeration registreras alla aktuella och framtida virtuella datorer som har SQL Server installerat med tillägget SQL IaaS agent i Lightweight-läge. Du måste fortfarande [Uppgradera till fullständigt hanterings läge manuellt](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) för att kunna dra nytta av den fullständiga funktions uppsättningen. 

> [!IMPORTANT]
> SQL IaaS agent-tillägget samlar in data i Express syfte att ge kunderna valfria förmåner när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licens granskningar utan kundens medgivande. Se [SQL Server sekretess tillägg](/sql/sql-server/sql-server-privacy#non-personal-data) för mer information.

## <a name="prerequisites"></a>Förutsättningar

Om du vill registrera SQL Server VM med tillägget måste du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/) och minst [deltagar roll](../../../role-based-access-control/built-in-roles.md#all) behörigheter.
- En virtuell dator med Azure resurs modell [Windows Server 2008 R2 (eller senare)](../../../virtual-machines/windows/quick-create-portal.md) med [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) distribuerad till det offentliga molnet eller Azure Government molnet. Windows Server 2008 stöds inte. 


## <a name="enable"></a>Aktivera

Följ dessa steg om du vill aktivera automatisk registrering av SQL Server virtuella datorer i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resurs sidan för [**virtuella SQL-datorer**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **automatisk SQL Server VM registreringen** för att öppna sidan för **automatisk registrering** . 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Välj automatisk SQL Server VM registreringen för att öppna sidan för automatisk registrering":::

1. Välj din prenumeration i list rutan. 
1. Läs igenom villkoren och godkänn genom att välja **Jag accepterar**. 
1. Välj **Registrera** för att aktivera funktionen och registrera automatiskt alla aktuella och framtida SQL Server virtuella datorer med agent tillägget för SQL-IaaS. Detta kommer inte att starta om SQL Server-tjänsten på någon av de virtuella datorerna. 

## <a name="disable"></a>Inaktivera

Använd [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/install-az-ps) för att inaktivera funktionen för automatisk registrering. När funktionen automatisk registrering är inaktive rad måste SQL Server virtuella datorer som läggs till i prenumerationen registreras manuellt med SQL IaaS agent-tillägget. Det går inte att avregistrera befintliga SQL Server virtuella datorer som redan har registrerats.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill inaktivera automatisk registrering med Azure CLI kör du följande kommando: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Om du vill inaktivera automatisk registrering med Azure PowerShell kör du följande kommando: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Aktivera för flera prenumerationer

Du kan aktivera funktionen för automatisk registrering för flera Azure-prenumerationer med hjälp av PowerShell. 

Det gör du på följande sätt:

1. Spara [skriptet](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) till en `.ps1` fil, till exempel `EnableBySubscription.ps1` . 
1. Navigera till den plats där du sparade skriptet med hjälp av en administrativ kommando tolk eller PowerShell-fönster. 
1. Anslut till Azure ( `az login` ).
1. Kör skriptet och skicka i SubscriptionIds som parametrar som   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Exempel: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Misslyckade registrerings fel lagras i `RegistrationErrors.csv` samma katalog som du sparade och körde `.ps1` skriptet från. 

## <a name="next-steps"></a>Nästa steg

Uppgradera hanterbarhets läget till [full](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) för att dra nytta av den fullständiga funktions uppsättningen som du fått av SQL IaaS agent-tillägget. 
