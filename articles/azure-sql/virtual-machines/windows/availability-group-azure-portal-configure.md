---
title: Konfigurera en tillgänglighets grupp (Azure Portal)
description: Använd Azure Portal för att skapa Windows-redundansklustret, tillgänglighets gruppens lyssnare och den interna belastningsutjämnaren på ett SQL Server VM i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359514"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Använd Azure Portal för att konfigurera en tillgänglighets grupp (för hands version) för SQL Server på virtuell Azure-dator 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du använder [Azure Portal](https://portal.azure.com) för att konfigurera en tillgänglighets grupp för SQL Server på virtuella Azure-datorer. 

Använd Azure Portal för att skapa ett nytt kluster eller publicera ett befintligt kluster och skapa sedan tillgänglighets gruppen, lyssnaren och den interna belastningsutjämnaren. 

Den här funktionen finns för närvarande som en förhandsversion. 

Den här artikeln använder Azure Portal för att konfigurera tillgänglighets grupps miljön, men det går också att göra detta med hjälp av [PowerShell eller Azure CLI](availability-group-az-commandline-configure.md), [Azure snabb starts-mallar](availability-group-quickstart-template-configure.md)eller [manuellt](availability-group-manually-configure-tutorial.md) . 


## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera en tillgänglighets grupp som alltid använder Azure Portal måste du ha följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resurs grupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQL Server 2016 (eller senare) Enterprise Edition](./create-sql-vm-portal.md) i *samma* tillgänglighets uppsättning eller *olika* tillgänglighets zoner som har [registrerats med SQL IaaS agent-tillägget i fullständig hanterbarhets läge](sql-agent-extension-manually-register-single-vm.md) och använder samma domän konto för tjänsten SQL Server på varje virtuell dator.
- Två tillgängliga (används inte av någon entitet) IP-adresser. En är för den interna belastningsutjämnaren. Det andra är för tillgänglighets gruppens lyssnare i samma undernät som tillgänglighets gruppen. Om du använder en befintlig belastningsutjämnare behöver du bara en tillgänglig IP-adress för tillgänglighets gruppens lyssnare. 

## <a name="permissions"></a>Behörigheter

Du behöver följande konto behörigheter för att konfigurera tillgänglighets gruppen med hjälp av Azure Portal: 

- Ett befintligt domän användar konto som har behörighet att **skapa dator objekt** i domänen. Till exempel har ett domän administratörs konto vanligt vis tillräcklig behörighet (till exempel: account@domain.com ). _Detta konto bör också vara en del av den lokala administratörs gruppen på varje virtuell dator för att skapa klustret._
- Domän användar kontot som styr SQL Server. Detta bör vara samma konto för varje SQL Server VM som du vill lägga till i tillgänglighets gruppen. 

## <a name="configure-cluster"></a>Konfigurera kluster

Konfigurera klustret med hjälp av Azure Portal. Du kan antingen skapa ett nytt kluster, eller om du redan har ett befintligt kluster, kan du publicera det till agent tillägget för SQL-IaaS till för Portal hantering.


### <a name="create-a-new-cluster"></a>Skapa ett nytt kluster

Om du redan har ett kluster kan du hoppa över det här avsnittet och gå till [publicera befintligt kluster](#onboard-existing-cluster) i stället. 

Om du inte redan har ett befintligt kluster kan du skapa det med hjälp av Azure Portal med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till resursen för [virtuella SQL-datorer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **hög tillgänglighet** under **Inställningar**. 
1. Välj **+ nytt redundanskluster för Windows Server** för att öppna sidan **Konfigurera Windows-redundanskluster** .  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Skapa ett nytt kluster genom att välja + nytt-kluster i portalen":::

1. Namnge klustret och ange ett lagrings konto som ska användas som moln vittne. Använd ett befintligt lagrings konto eller Välj **Skapa nytt** för att skapa ett nytt lagrings konto. Lagrings kontots namn måste vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemena bokstäver.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Ange namn, lagrings konto och autentiseringsuppgifter för klustret":::

1. Expandera **autentiseringsuppgifter för Windows Server-redundanskluster** för att ange [autentiseringsuppgifter](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) för SQL Server tjänst kontot, samt kluster operatören och start kontona om de skiljer sig från det konto som används för SQL Servers tjänsten. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Ange autentiseringsuppgifter för SQL-tjänstkontot, kluster operatörs konto och start konto för kluster":::

1. Välj de SQL Server virtuella datorer som du vill lägga till i klustret. Tänk på om en omstart krävs, och fortsätt med försiktighet. Endast virtuella datorer som är registrerade med SQL IaaS agent-tillägget i fullständigt hanterbarhets läge och finns på samma plats, domän och i samma virtuella nätverk som den primära SQL Server VM kommer att visas. 
1. Välj **Använd** för att skapa klustret. Du kan kontrol lera statusen för din distribution i **aktivitets loggen** som är tillgänglig från klock ikonen i det övre navigerings fältet. 
1. För att ett redundanskluster ska kunna stödjas av Microsoft måste det klara kluster valideringen. Anslut till den virtuella datorn med önskad metod (till exempel Remote Desktop Protocol (RDP)) och kontrol lera att klustret klarar verifieringen innan du fortsätter. Om du inte gör det så låter klustret vara i ett tillstånd som inte stöds. Du kan verifiera klustret med Klusterhanteraren för växling vid fel (FCM) eller följande PowerShell-kommando:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Publicera befintligt kluster

Om du redan har ett kluster som har kon figurer ATS i SQL Server VMs miljön kan du publicera det från Azure Portal.

Det gör du på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till resursen för [virtuella SQL-datorer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **hög tillgänglighet** under **Inställningar**. 
1. Välj **publicera befintlig Windows Server-redundanskluster** för att öppna sidan **Windows Server-redundanskluster** . 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Publicera ett befintligt kluster från sidan med hög tillgänglighet på din SQL-resurs för virtuella datorer":::

1. Granska inställningarna för klustret. 
1. Välj **tillämpa** på publicera kluster och välj sedan **Ja** när du vill fortsätta.

## <a name="create-availability-group"></a>Skapa tillgänglighets grupp

När klustret har skapats eller publicerats skapar du tillgänglighets gruppen med hjälp av Azure Portal. Det gör du på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till resursen för [virtuella SQL-datorer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **hög tillgänglighet** under **Inställningar**. 
1. Välj **+ ny Always on tillgänglighets grupp** för att öppna sidan **skapa tillgänglighets grupp** .

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Välj ny Always on tillgänglighets grupp för att öppna sidan Skapa tillgänglighets grupp.":::

1. Ange ett namn för tillgänglighets gruppen. 
1. Välj **Konfigurera lyssnare** för att öppna sidan **Konfigurera tillgänglighets grupps lyssnare** . 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Ange ett namn för tillgänglighets gruppen och konfigurera en lyssnare":::

1. Fyll i värdena och Använd antingen en befintlig belastningsutjämnare, eller Välj **Skapa ny** för att skapa en ny belastningsutjämnare.  Välj **Använd** för att spara inställningarna och skapa din lyssnare och belastningsutjämnare. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Fyll i värdena i formuläret för att skapa din nya lyssnare och belastningsutjämnare":::

1. Välj **+ Välj replik** för att öppna sidan **Konfigurera tillgänglighets grupp repliker** .
1. Välj de virtuella datorer som du vill lägga till i tillgänglighets gruppen och välj de tillgänglighets grupps inställningar som passar dina affärs behov bäst. Välj **Använd** för att spara inställningarna. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Välj virtuella datorer som ska läggas till i tillgänglighets gruppen och konfigurera inställningar som passar din verksamhet":::

1. Verifiera inställningarna för tillgänglighets gruppen och välj sedan **Använd** för att skapa din tillgänglighets grupp. 

Du kan kontrol lera statusen för din distribution i **aktivitets loggen** som är tillgänglig från klock ikonen i det övre navigerings fältet. 

  > [!NOTE]
  > Ditt **synkroniseringstillstånd** på sidan **hög tillgänglighet** i Azure Portal visas som **inte felfri** förrän du lägger till databaser i tillgänglighets gruppen. 


## <a name="add-database-to-availability-group"></a>Lägg till databas i tillgänglighets gruppen

Lägg till dina databaser i tillgänglighets gruppen när distributionen är klar. I stegen nedan används SQL Server Management Studio (SSMS), men du kan även använda [Transact-SQL eller PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . 

Följ dessa steg om du vill lägga till databaser i tillgänglighets gruppen med hjälp av SQL Server Management Studio:

1. Anslut till en av dina SQL Server virtuella datorer med den metod du föredrar, till exempel Anslutning till fjärrskrivbord (RDP). 
1. Öppna SQL Server Management Studio (SSMS).
1. Anslut till din SQL Server-instans. 
1. Expandera **alltid med hög tillgänglighet** i **Object Explorer**.
1. Expandera **tillgänglighets grupper**, högerklicka på din tillgänglighets grupp och välj att **lägga till databas..**..

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Högerklicka på tillgänglighets gruppen i Object Explorer och välj att lägga till databas":::

1. Följ anvisningarna för att välja de databaser som du vill lägga till i tillgänglighets gruppen. 
1. Välj **OK** för att spara inställningarna och lägga till din databas i tillgänglighets gruppen. 
1. När databasen har lagts till uppdaterar du **Object Explorer** för att bekräfta status för din databas som `synchronized` . 

När databaserna har lagts till kan du kontrol lera status för tillgänglighets gruppen i Azure Portal: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Kontrol lera status för tillgänglighets gruppen från sidan med hög tillgänglighet från Azure Portal efter att databaserna har synkroniserats":::

## <a name="add-more-vms"></a>Lägg till fler virtuella datorer

Följ dessa steg om du vill lägga till fler SQL Server virtuella datorer i klustret: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till resursen för [virtuella SQL-datorer](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **hög tillgänglighet** under **Inställningar**. 
1. Välj **Konfigurera Windows Server-redundanskluster** för att öppna sidan **Konfigurera Windows Server** -redundanskluster. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Välj Konfigurera Windows Server-redundanskluster för att lägga till virtuella datorer i klustret.":::

1. Expandera **autentiseringsuppgifter för Windows Server-redundanskluster** och ange i de konton som används för SQL Server tjänst, kluster operatör och start konton för kluster. 
1. Välj de SQL Server virtuella datorer som du vill lägga till i klustret. 
1. Välj **Använd**. 

Du kan kontrol lera statusen för din distribution i **aktivitets loggen** som är tillgänglig från klock ikonen i det övre navigerings fältet. 


## <a name="modify-availability-group"></a>Ändra tillgänglighets grupp 


Du kan **lägga till fler repliker** i tillgänglighets gruppen, **Konfigurera lyssnaren** eller **ta bort lyssnaren** från sidan med **hög tillgänglighet** i Azure Portal genom att välja ellipserna (...) bredvid tillgänglighets gruppen: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Välj ellipserna bredvid tillgänglighets gruppen och välj sedan Lägg till replik för att lägga till fler repliker i tillgänglighets gruppen.":::

## <a name="remove-cluster"></a>Ta bort kluster

Ta bort alla SQL Server virtuella datorer från klustret för att förstöra det och ta sedan bort de klustrade metadatana från SQL IaaS agent-tillägget. Du kan göra detta med hjälp av den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta först bort alla SQL Server virtuella datorer från klustret. Detta tar bort noderna från klustret fysiskt och förstör klustret:  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Om dessa är de enda virtuella datorerna i klustret kommer klustret att förstöras. Om det finns andra virtuella datorer i klustret, förutom SQL Server de virtuella datorer som har tagits bort, kommer de andra virtuella datorerna inte att tas bort och klustret kommer inte att förstöras. 

Ta sedan bort de klustrade metadatana från SQL-IaaS agent-tillägg: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta först bort alla SQL Server virtuella datorer från klustret. Detta tar bort noderna från klustret fysiskt och förstör klustret: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Om dessa är de enda virtuella datorerna i klustret kommer klustret att förstöras. Om det finns andra virtuella datorer i klustret, förutom SQL Server de virtuella datorer som har tagits bort, kommer de andra virtuella datorerna inte att tas bort och klustret kommer inte att förstöras. 


Ta sedan bort de klustrade metadatana från SQL-IaaS agent-tillägg: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Felsökning

Om du stöter på problem kan du kontrol lera distributions historiken och granska de vanliga felen samt deras upplösningar. 

### <a name="check-deployment-history"></a>Kontrol lera distributions historik

Ändringar i klustret och tillgänglighets gruppen via portalen görs via distributioner. Distributions historiken kan ge mer information om det uppstår problem med att skapa eller registrera klustret, eller med att skapa tillgänglighets gruppen.

Följ dessa steg om du vill visa loggarna för distributionen och kontrol lera distributions historiken:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din resurs grupp.
1. Välj **distributioner** under **Inställningar**.
1. Välj distribution av intresse för att lära dig mer om distributionen. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Välj den distribution du vill lära dig mer om." :::

### <a name="common-errors"></a>Vanliga fel

Granska följande vanliga fel och deras resolutioner. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Kontot som används för att starta SQL-tjänsten är inte ett domän konto

Det här är en indikation på att resurs leverantören inte kunde komma åt SQL Server tjänsten med de angivna autentiseringsuppgifterna. Några vanliga lösningar:
- Se till att domänkontrollanten körs.
- Validera de autentiseringsuppgifter som angavs i portalen matchar de i SQL Servers tjänsten. 


## <a name="next-steps"></a>Nästa steg


Mer information om tillgänglighets grupper finns i:

- [Översikt över tillgänglighets grupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Administration av en tillgänglighets grupp](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Övervakning av tillgänglighets grupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Transact-SQL-uttryck för tillgänglighets grupp ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [PowerShell-kommandon för tillgänglighets grupper](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Mer information om SQL Server virtuella datorer finns i: 

* [Översikt över SQL Server virtuella datorer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Viktig information för SQL Server virtuella datorer](../../database/doc-changes-updates-release-notes.md)
* [Vanliga frågor och svar om SQL Server virtuella datorer](frequently-asked-questions-faq.md)
