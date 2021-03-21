---
title: Hämta loggar för att felsöka Azure Arc-aktiverade data tjänster
description: Lär dig hur du hämtar loggfiler från en datakontrollant för att felsöka Azure Arc-aktiverade data tjänster.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234061"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Hämta loggar för att felsöka Azure Arc-aktiverade data tjänster

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter behöver du:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Mer information finns i [Installera klient verktyg för att distribuera och hantera Azure Arc Data Services](./install-client-tools.md).
* Ett administratörs konto för att logga in på data styrenheten Azure Arc-aktiverad.

## <a name="get-log-files"></a>Hämta loggfiler

Du kan hämta tjänst loggar för alla poddar eller vissa poddar i fel söknings syfte. Ett sätt är att använda standard Kubernetes-verktyg som `kubectl logs` kommandot. I den här artikeln använder du [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] verktyget, som gör det enklare att hämta alla loggar samtidigt.

1. Logga in på data styrenheten med ett administratörs konto.

   ```console
   azdata login
   ```

2. Dumpa loggarna genom att köra följande kommando:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Exempel:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Datakontrollanten skapar loggfilerna i den aktuella arbets katalogen i en under katalog som kallas `logs` . 

## <a name="options"></a>Alternativ

`azdata arc dc debug copy-logs`Kommandot ger följande alternativ för att hantera utdata:

* Spara loggfilerna i en annan katalog med hjälp av `--target-folder` parametern.
* Komprimera filerna genom att utelämna `--skip-compress` parametern.
* Utlös och inkludera minnes dum par genom att utesluta `--exclude-dumps` . Vi rekommenderar inte den här metoden om Microsoft Support har begärt minnes dum par. Att hämta en minnes dumpning kräver att inställningen datakontrollant `allowDumps` är inställd på `true` när data styrenheten skapas.
* Filtrera för att samla in loggar för bara en speciell Pod ( `--pod` ) eller container ( `--container` ) efter namn.
* Filtrera för att samla in loggar för en angiven anpassad resurs genom att skicka `--resource-kind` `--resource-name` parametrarna och. `resource-kind`Parametervärdet måste vara ett av de anpassade resurs definitions namnen. Du kan hämta dessa namn med hjälp av kommandot `kubectl get customresourcedefinition` .

Med dessa parametrar kan du ersätta `<parameters>` i följande exempel: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Exempel:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Följande mapphierarki är ett exempel. Det är ordnat efter Pod namn, sedan container och sedan efter katalogpartition i behållaren.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Nästa steg

[azdata Arc DC debug Copy-Logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
