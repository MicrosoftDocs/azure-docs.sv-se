---
title: Ladda upp mått till Azure Monitor
description: Ladda upp mått för Azure Arc-aktiverade data tjänster till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575683"
---
# <a name="upload-metrics-to-azure-monitor"></a>Ladda upp mått till Azure Monitor

Du kan regelbundet exportera övervaknings mått och sedan ladda upp dem till Azure. Export och uppladdning av data skapar också och uppdaterar data styrenhet, SQL-hanterad instans och PostgreSQL för storskaliga Server grupper i Azure.

> [!NOTE] 
> Under för hands versions perioden kostar det inget att använda Azure Arc-aktiverade data tjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att du har skapat den nödvändiga tjänstens huvud namn och tilldelats den till en lämplig roll. Mer information finns i:
* [Skapa tjänstens huvud namn](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Tilldela roller till tjänstens huvud namn](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Ladda upp mått

Med Azure Arc Data Services kan du överföra dina mått till Azure Monitor så att du kan samla in och analysera mått, generera aviseringar, skicka meddelanden eller utlösa automatiserade åtgärder. 

Genom att skicka dina data till Azure Monitor kan du också lagra mått data utanför platsen och i stor skala, vilket möjliggör långsiktig lagring av data för avancerad analys.

Om du har flera webbplatser som har Azure Arc Data Services kan du använda Azure Monitor som en central plats för att samla in alla dina loggar och mått på dina webbplatser.

## <a name="set-final-environment-variables-and-confirm"></a>Ange de slutgiltiga miljövariablerna och bekräfta

Ange URL: en för SPN-auktoritet i en miljö variabel:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Kontrol lera att alla miljövariabler som krävs är inställda om du vill:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Ladda upp mått till Azure Monitor

Om du vill överföra mått för Azure Arc-aktiverade SQL-hanterade instanser och Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper kör du följande CLI-kommandon:

1. Logga in på data styrenheten med `azdata` .
 
1. Exportera alla mått till den angivna filen:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Ladda upp mått till Azure Monitor:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Vänta minst 30 minuter efter att de Azure Arc-aktiverade data instanserna har skapats för den första överföringen.
   >
   >Se till att `upload` måtten omedelbart efter `export` Azure Monitor bara accepterar mått under de senaste 30 minuterna. [Läs mer](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


Om du ser några fel som indikerar att "det inte går att hämta mått" under exporten, kontrollerar du om data insamling har angetts till `true` genom att köra följande kommando:

```console
azdata arc dc config show
```

Titta under avsnittet "säkerhet"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Kontrol lera om- `allowNodeMetricsCollection` och- `allowPodMetricsCollection` egenskaperna har angetts till `true` .

## <a name="view-the-metrics-in-the-portal"></a>Visa måtten på portalen

När dina mått har överförts kan du visa dem från Azure Portal.
> [!NOTE]
> Observera att det kan ta några minuter innan de överförda data bearbetas innan du kan visa måtten i portalen.


Om du vill visa dina mått i portalen använder du den här länken för att öppna portalen: <https://portal.azure.com> Sök sedan efter din databas instans efter namn i Sök fältet:

Du kan visa processor användningen på sidan Översikt eller om du vill ha mer detaljerade mått som du kan klicka på mått från den vänstra navigerings panelen

Välj SQL Server som mått namn område:

Välj det mått som du vill visualisera (du kan också välja flera):

Ändra frekvensen till de senaste 30 minuterna:

> [!NOTE]
> Du kan bara ladda upp måtten för de senaste 30 minuterna. Azure Monitor avvisar mått som är äldre än 30 minuter.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Allmänna rikt linjer för att exportera och ladda upp användning, mått

Åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i Azure Arc-aktiverade data tjänster loggas för fakturerings-och övervaknings syfte. Det finns bakgrunds tjänster som övervakar för dessa CRUD-åtgärder och beräknar förbrukningen på lämpligt sätt. Den faktiska beräkningen av användning eller förbrukning sker enligt schema och görs i bakgrunden. 

Under för hands versionen sker den här processen natt. Den allmänna vägledningen är att bara överföra användningen en gång per dag. När användnings informationen exporteras och överförs flera gånger under samma 24-timmarsperiod, uppdateras bara resurs lagret i Azure Portal, men inte resursanvändningen.

För att ladda upp mått accepterar Azure Monitor bara de senaste 30 minuterna data ([Läs mer](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Rikt linjerna för att ladda upp mått är att överföra måtten direkt efter att du har skapat export filen så att du kan visa hela data uppsättningen i Azure Portal. Om du till exempel exporterade måtten till 2:00 PM och körde kommandot upload på 2:50 PM. Eftersom Azure Monitor bara accepterar data under de senaste 30 minuterna, kanske du inte ser några data i portalen. 

## <a name="next-steps"></a>Nästa steg

[Ladda upp loggar till Azure Monitor](upload-logs.md)

[Ladda upp användnings data, mått och loggar till Azure Monitor](upload-usage-data.md)

[Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md)

[Visa resursen Azure Arc data Controller i Azure Portal](view-data-controller-in-azure-portal.md)
