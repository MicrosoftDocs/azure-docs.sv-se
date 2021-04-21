---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774579"
---
Det här felet kan inträffa när Azure File Sync-tjänsten inte kan nås från servern. Du kan felsöka det här felet genom att utföra följande steg:

1. Kontrollera att `FileSyncSvc.exe` Windows-tjänsten inte blockeras av brandväggen.
2. Kontrollera att port 443 är öppen för utgående anslutningar till Azure File Sync tjänsten. Du kan göra detta med `Test-NetConnection` cmdleten . Du hittar webbadressen för platshållaren `<azure-file-sync-endpoint>` nedan i dokumentet [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Se till att proxykonfigurationen är inställd som förväntat. Det kan du göra med cmdleten `Get-StorageSyncProxyConfiguration`. Mer information om konfiguration av proxykonfigurationen för Azure File Sync finns i [Proxy- och brandväggsinställningar för Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Använd Test-StorageSyncNetworkConnectivity för att kontrollera nätverksanslutningen till tjänstslutpunkterna. Mer information finns i Testa [nätverksanslutning till tjänstslutpunkter.](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)    

5. Kontakta nätverksadministratören för ytterligare hjälp med felsökning av nätverksanslutning.