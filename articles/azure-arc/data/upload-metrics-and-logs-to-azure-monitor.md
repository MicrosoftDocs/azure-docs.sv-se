---
title: Ladda upp användnings data, mått och loggar till Azure Monitor
description: Överför resurs inventering, användnings data, mått och loggar till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 61ac4c979445ef48b5986ec3793a9880cedc837a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650260"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Ladda upp användnings data, mått och loggar till Azure Monitor

Med jämna mellanrum kan du exportera användnings information för fakturerings syfte, övervaka mått och loggar och sedan ladda upp den till Azure. Export och uppladdning av någon av dessa tre typer av data kommer också att skapa och uppdatera datakontrollanten, SQL-hanterad instans och PostgreSQL skalnings resurser för Server grupper i Azure.

> [!NOTE] 
> Under för hands versions perioden kostar det inget att använda Azure Arc-aktiverade data tjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Innan du kan ladda upp användnings data, mått eller loggar måste du:

* Installera verktygen 
* [Registrera `Microsoft.AzureArcData` resurs leverantören](#register-the-resource-provider) 
* [Skapa huvudnamn för tjänsten](#create-service-principal)

## <a name="install-tools"></a>Installera verktygen

De verktyg som krävs är: 
* Azure CLI (AZ) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Se [installations verktyg](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Innan du laddar upp mått eller användar data till Azure måste du se till att din Azure-prenumeration har `Microsoft.AzureArcData` resurs leverantören registrerad.

Verifiera resurs leverantören genom att köra följande kommando:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Om resurs leverantören för närvarande inte är registrerad i din prenumeration kan du registrera den. Registrera den genom att köra följande kommando.  Det kan ta ett par minuter innan kommandot har slutförts.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Tjänstens huvud namn används för att överföra användnings-och mät data.

Följ dessa kommandon för att skapa mått för att ladda upp tjänstens huvud namn:

> [!NOTE]
> Att skapa ett huvud namn [för tjänsten kräver vissa behörigheter i Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Uppdatera följande exempel om du vill skapa ett huvud namn för tjänsten. Ersätt `<ServicePrincipalName>` med namnet på tjänstens huvud namn och kör kommandot:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Om du skapade tjänstens huvud namn tidigare och behöver bara hämta de aktuella autentiseringsuppgifterna, kör du följande kommando för att återställa autentiseringsuppgifterna.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Om du till exempel vill skapa ett huvud namn för tjänsten `azure-arc-metrics` kör du följande kommando

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Exempel på utdata:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Spara `appId` värdena, `password` och `tenant` i en miljö variabel för senare användning. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

När du har skapat tjänstens huvud namn tilldelar du tjänstens huvud namn till rätt roll. 

## <a name="assign-roles-to-the-service-principal"></a>Tilldela roller till tjänstens huvud namn

Kör det här kommandot för att tilldela tjänstens huvud namn till `Monitoring Metrics Publisher` rollen i prenumerationen där dina databas instans resurser finns:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Du måste använda dubbla citat tecken för roll namn när du kör från en Windows-miljö.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Exempel på utdata:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Med tjänstens huvud namn tilldelat till en lämplig roll kan du fortsätta med att överföra mått eller användar data. 

## <a name="upload-logs-metrics-or-user-data"></a>Ladda upp loggar, statistik eller användar data

De olika stegen för att ladda upp loggar, statistik eller användar data varierar beroende på vilken typ av information som du överför. 

[Ladda upp loggar till Azure Monitor](upload-logs.md)

[Ladda upp mått till Azure Monitor](upload-metrics.md)

[Ladda upp användnings data till Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Allmänna rikt linjer för att exportera och ladda upp användning, mått

Åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i Azure Arc-aktiverade data tjänster loggas för fakturerings-och övervaknings syfte. Det finns bakgrunds tjänster som övervakar för dessa CRUD-åtgärder och beräknar förbrukningen på lämpligt sätt. Den faktiska beräkningen av användning eller förbrukning sker enligt schema och görs i bakgrunden. 

Under för hands versionen sker den här processen natt. Den allmänna vägledningen är att bara överföra användningen en gång per dag. När användnings informationen exporteras och överförs flera gånger under samma 24-timmarsperiod, uppdateras bara resurs lagret i Azure Portal, men inte resursanvändningen.

För att ladda upp mått accepterar Azure Monitor bara de senaste 30 minuterna data ([Läs mer](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Rikt linjerna för att ladda upp mått är att överföra måtten direkt efter att du har skapat export filen så att du kan visa hela data uppsättningen i Azure Portal. Om du till exempel exporterade måtten till 2:00 PM och körde kommandot upload på 2:50 PM. Eftersom Azure Monitor bara accepterar data under de senaste 30 minuterna, kanske du inte ser några data i portalen. 

## <a name="next-steps"></a>Nästa steg

[Läs mer om tjänstens huvud namn](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md)

[Visa resursen Azure Arc data Controller i Azure Portal](view-data-controller-in-azure-portal.md)
