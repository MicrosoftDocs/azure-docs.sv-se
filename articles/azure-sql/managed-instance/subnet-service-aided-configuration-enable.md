---
title: Aktiverar konfiguration av tjänstens konfiguration för under nätverk för Azure SQL-hanterad instans
description: Aktiverar konfiguration av tjänstens konfiguration för under nätverk för Azure SQL-hanterad instans
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91618037"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Aktiverar konfiguration av tjänstens konfiguration för under nätverk för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Konfiguration av tjänstens konfiguration för under nätet ger automatisk hantering av nätverks konfiguration för undernät som är värdar för hanterade instanser. Med konfigurations användaren för tjänste under nätet förblir fullständig kontroll över åtkomsten till data (TDS-trafikflöden) medan den hanterade instansen tar ansvar för att säkerställa oavbrutet flöde av hanterings trafik för att uppfylla SLA.

Automatiskt konfigurerade nätverks säkerhets grupper och regler för routningstabellen är synliga för kunden och kommenteras med prefix _Microsoft. SQL-managedInstances_UseOnly_ _.

Konfiguration av tjänstens konfiguration aktive ras automatiskt när du aktiverar [under nätets delegering](../../virtual-network/subnet-delegation-overview.md) för `Microsoft.Sql/managedInstances` resurs leverantör.

> [!IMPORTANT] 
> När undernät – delegering är aktiverat kan du inte inaktivera det förrän du tar bort det sista virtuella klustret från under nätet. Mer information om hur du tar bort ett virtuellt kluster finns i följande [artikel](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Som tjänst-konfiguration av under nätet är en viktig funktion för att upprätthålla SLA, med början den 1 maj 2020, det går inte att distribuera hanterade instanser i undernät som inte är delegerade till resurs leverantören för en hanterad instans. Den 1 juli 2020 kommer alla undernät som innehåller hanterade instanser automatiskt att delegeras till resurs leverantören för hanterade instanser. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Aktivera undernät – delegering för nya distributioner
Om du vill distribuera en hanterad instans i till ett tomt undernät måste du delegera den till `Microsoft.Sql/managedInstances` Resource Provider enligt beskrivningen i följande [artikel](../../virtual-network/manage-subnet-delegation.md). _Observera att den refererade artikeln använder `Microsoft.DBforPostgreSQL/serversv2` resurs leverantören till exempel. Du måste använda `Microsoft.Sql/managedInstances` Resource Provider i stället._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Aktivera undernät – delegering för befintliga distributioner

Om du vill aktivera under näts delegering för din befintliga hanterade instans distribution måste du ta reda på det virtuella nätverkets undernät där det är placerat. 

För att lära dig detta kan du titta `Virtual network/subnet` på `Overview` Portal bladet för din hanterade instans.

Alternativt kan du köra följande PowerShell-kommandon för att lära dig detta. Ersätt **prenumerations-ID** med ditt PRENUMERATIONS-ID. Ersätt också **RG-Name** med resurs gruppen för din hanterade instans och Ersätt **mi-Name** med namnet på din hanterade instans.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

När du har hittat under nätet för hanterade instanser måste du delegera det till `Microsoft.Sql/managedInstances` Resource Provider enligt beskrivningen i följande [artikel](../../virtual-network/manage-subnet-delegation.md). _Observera att den refererade artikeln använder `Microsoft.DBforPostgreSQL/serversv2` resurs leverantören till exempel. Du måste använda `Microsoft.Sql/managedInstances` Resource Provider i stället._


> [!IMPORTANT]
> Aktivering av tjänstens konfiguration orsakar inte redundans eller avbrott i anslutningen för hanterade instanser som redan finns i under nätet.
