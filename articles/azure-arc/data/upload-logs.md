---
title: Ladda upp loggar till Azure Monitor
description: Ladda upp loggar för Azure Arc-aktiverade data tjänster till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92378144"
---
# <a name="upload-logs-to-azure-monitor"></a>Ladda upp loggar till Azure Monitor

Du kan regelbundet exportera loggar och sedan överföra dem till Azure. Genom att exportera och ladda upp loggar skapas även och uppdateras datakontrollant, SQL-hanterad instans och PostgreSQL för storskaliga Server grupper i Azure.

> [!NOTE] 
> Under för hands versions perioden kostar det inget att använda Azure Arc-aktiverade data tjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan ladda upp loggar måste du: 

1. [Skapa en Log Analytics-arbetsyta](#create-a-log-analytics-workspace)
1. [Tilldela variabler för ID och delad nyckel till miljövariabler](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Skapa en Log Analytics-arbetsyta genom att köra dessa kommandon för att skapa en Log Analytics arbets yta och ange åtkomst information till miljövariabler.

> [!NOTE]
> Hoppa över det här steget om du redan har en arbets yta.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Exempel på utdata:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Tilldela variabler för ID och delad nyckel till miljövariabler

Spara logg arbets ytans analys `customerId` som en miljö variabel som ska användas senare:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Det här kommandot returnerar de åtkomst nycklar som krävs för att ansluta till Log Analytics-arbetsytan:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exempel på utdata:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Spara primär nyckeln i en miljö variabel som ska användas senare:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Ange de slutgiltiga miljövariablerna och bekräfta

Ange URL: en för SPN-auktoritet i en miljö variabel:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Verifiera miljövariabler

Kontrol lera att alla miljövariabler som krävs är inställda om du vill:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Med miljövariablerna inställda kan du överföra loggar till logg arbets ytan. 

## <a name="upload-logs-to-azure-monitor"></a>Ladda upp loggar till Azure Monitor

 Om du vill ladda upp loggar för Azure Arc-aktiverade SQL-hanterade instanser och AzureArc-aktiverade PostgreSQL för storskaliga Server grupper kör du följande CLI-kommandon –

1. Logga in på Azure båg data Controller med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Följ anvisningarna för att ange namn området, administratörs användar namnet och lösen ordet. 

1. Exportera alla loggar till den angivna filen:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Ladda upp loggar till en Azure Monitor Log Analytics-arbetsyta:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Visa dina loggar i Azure Portal

När loggarna har laddats upp kan du köra fråga på dem med hjälp av loggfrågeutforskaren:

1. Öppna Azure Portal och Sök sedan efter din arbets yta efter namn i Sök fältet längst upp och markera den.
2. Välj loggar i den vänstra panelen.
3. Välj kom igång (eller Välj länkarna på sidan Komma igång om du vill veta mer om att Log Analytics om du är nybörjare).
4. Följ själv studie kursen för att lära dig mer om Log Analytics om det är första gången du använder Log Analytics.
5. Expandera Anpassade loggar längst ned i listan över tabeller så visas en tabell med namnet sql_instance_logs_CL.
6. Välj ögon ikonen bredvid tabellens namn.
7. Välj knappen Visa i Frågeredigeraren.
8. Nu har du en fråga i Frågeredigeraren som visar de senaste 10 händelserna i loggen.
9. Härifrån kan du experimentera med att köra frågor mot loggarna med frågeredigeraren, ange aviseringar med mera.

## <a name="automating-uploads-optional"></a>Automatisera uppladdningar (valfritt)

Om du vill överföra mått och loggar regelbundet kan du skapa ett skript och köra det på en timer med några minuters mellanrum. Nedan visas ett exempel på hur du automatiserar uppladdningar med hjälp av ett Linux-gränssnitts skript.

I din favorit text/kod redigerare lägger du till följande skript i filen och sparar som en körbar skript fil, till exempel. sh (Linux/Mac) eller. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Skapa skript filens körbara fil

```console
chmod +x myuploadscript.sh
```

Kör skriptet var 20: e minut:

```console
watch -n 1200 ./myuploadscript.sh
```

Du kan också använda en jobbschemaläggaren som cron eller Schemaläggaren för Windows eller en Orchestrator som Ansible, Puppet eller chef.

## <a name="next-steps"></a>Nästa steg

[Ladda upp mått och loggar till Azure Monitor](upload-metrics.md)

[Ladda upp användnings data, mått och loggar till Azure Monitor](upload-usage-data.md)

[Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md)

[Visa resursen Azure Arc data Controller i Azure Portal](view-data-controller-in-azure-portal.md)
