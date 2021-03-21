---
title: Konfigurera postgres motor Server-parametrar för din PostgreSQL-Server grupp på Azure-bågen
titleSuffix: Azure Arc enabled data services
description: Konfigurera postgres motor Server-parametrar för din PostgreSQL-Server grupp på Azure-bågen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311048"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Ange databasmotorinställningar för Azure Arc-aktiverad PostgreSQL-hyperskala

Det här dokumentet beskriver hur du konfigurerar anpassade värden (ej standardvärden) i PostgreSQL Hyperscale-servergruppens inställningar för databasmotorn. Mer information om vilka databas motor parametrar som kan anges och vad deras standardvärde är finns i PostgreSQL-dokumentationen [här](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> För hands versionen stöder inte inställning av följande parametrar: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

Det allmänna formatet för kommandot för att konfigurera databas motor inställningarna är:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>Visa aktuella anpassade värden

### <a name="with-azure-data-cli-azdata-command"></a>Med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] kommando

```console
azdata arc postgres server show -n <server group name>
```

Exempel:

```console
azdata arc postgres server show -n postgres01
```

Det här kommandot returnerar specifikationen för Server gruppen där du ser de parametrar som du har angett. Om det inte finns något engine\settings-avsnitt innebär det att alla parametrar körs på standardvärdet:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>Med kommandot kubectl

Följ stegen nedan.

1. Hämta typen av anpassad resurs definition för Server gruppen

   Kör följande:

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   Exempel:

   ```console
   azdata arc postgres server show -n postgres01
   ```

   Det här kommandot returnerar specifikationen för Server gruppen där du ser de parametrar som du har angett. Om det inte finns något engine\settings-avsnitt innebär det att alla parametrar körs på standardvärdet:

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   I resultatet av utdata söker du efter fältet `kind` och ställer in värdet, till exempel: `postgresql-12` .

2. Beskriv den anpassade Kubernetes-resursen som motsvarar din server grupp 

   Det allmänna formatet för kommandot är:

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   Exempel:

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   Om du har angett anpassade värden för motor inställningarna returneras de. Exempel:

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   Om du inte har angett anpassade värden för någon av motor inställningarna kommer avsnittet motor inställningar i `resultset` vara tomt som:

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>Ange anpassade värden för motor inställningar

Kommandona nedan anger parametrarna för koordinator-noden och arbetsnoderna i PostgreSQL-skalningen till samma värden. Det går inte att ange parametrar per roll i Server gruppen. Det innebär att det inte går att konfigurera en angiven parameter till en specifik nod i koordinator-noden och till ett annat värde för arbetsnoderna.

### <a name="set-a-single-parameter"></a>Ange en enskild parameter

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Exempel:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>Ange flera parametrar med ett enda kommando

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Exempel:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>Återställ en parameter till dess standardvärde

Om du vill återställa en parameter till standardvärdet anger du den utan att ange ett värde. 

Exempel:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>Återställ alla parametrar till standardvärdena

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Exempel:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Särskilda överväganden

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Ange en parameter vars värde innehåller ett kommatecken, blank steg eller specialtecken

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Exempel:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Skicka en miljö variabel i ett parameter värde

Miljövariabeln ska omslutas i "" ", så att den inte löses innan den anges.

Exempel: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att skala ut (lägga till arbetsnoder)](scale-out-postgresql-hyperscale-server-group.md) i Server gruppen
- Läs mer om att [skala upp eller ned (öka/minska minnes-och virtuella kärnor)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) i Server gruppen
