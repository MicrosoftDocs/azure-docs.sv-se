---
title: Ladda upp användnings data till Azure Monitor
description: Överför användning Azure Arc-aktiverade data Services-data till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669516"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Ladda upp användnings data till Azure Monitor

Med jämna mellanrum kan du exportera användnings information. Export och uppladdning av den här informationen skapar och uppdaterar data styrenheten, SQL-hanterad instans och PostgreSQL för storskaliga Server grupper i Azure.

> [!NOTE] 
> Under för hands versions perioden kostar det inget att använda Azure Arc-aktiverade data tjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Vänta minst 24 timmar efter att du har skapat data styrenheten för Azure-bågen innan du laddar upp användnings data.

## <a name="create-service-principal-and-assign-roles"></a>Skapa tjänstens huvud namn och tilldela roller

Innan du fortsätter kontrollerar du att du har skapat den nödvändiga tjänstens huvud namn och tilldelats den till en lämplig roll. Mer information finns i:
* [Skapa tjänstens huvud namn](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Tilldela roller till tjänstens huvud namn](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Ladda upp användningsdata

Användnings information som inventering och Resursanvändning kan laddas upp till Azure på följande två sätt:

1. Logga in på data styrenheten. Ange värdena i prompten. 

   ```console
   azdata login
   ```

1. Exportera användnings data med hjälp av `azdata arc dc export` kommandot enligt följande:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Det här kommandot skapar en `usage.json` fil med alla Azure Arc-aktiverade data resurser, till exempel SQL-hanterade instanser och postgresql storskaliga instanser osv. som skapas på data styrenheten.

2. Överför användnings data med ```azdata upload``` kommandot

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatisera uppladdningar (valfritt)

Om du vill överföra mått och loggar regelbundet kan du skapa ett skript och köra det på en timer med några minuters mellanrum. Nedan visas ett exempel på hur du automatiserar uppladdningar med hjälp av ett Linux-gränssnitts skript.

I din favorit text/kod redigerare lägger du till följande skript i filen och sparar som en körbar skript fil, till exempel `.sh` (Linux/Mac) eller `.cmd` , `.bat` , eller `.ps1` .

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

[Ladda upp loggar till Azure Monitor](upload-logs.md)

[Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md)

[Visa resursen Azure Arc data Controller i Azure Portal](view-data-controller-in-azure-portal.md)
