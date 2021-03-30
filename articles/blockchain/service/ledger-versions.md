---
title: Azure blockchain service-versioner, uppdatering, & uppgradering
description: Översikt över de redovisnings versioner som stöds i Azure blockchain-tjänsten. Inklusive principer för system korrigeringar och uppgraderingar.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807748"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioner som stöds av Azure blockchain service-redovisning

I Azure blockchain service används den Ethereum-baserade [kvorumkonfigurationen](https://www.goquorum.com/developers) för bearbetning av privata transaktioner i en grupp kända deltagare som identifieras som konsortier i Azure blockchain-tjänsten.

För närvarande stöder Azure blockchain-tjänsten [2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) och [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Versions hantering i kvorum görs via en större version, dels och korrigerings version. Om kvorumloggen till exempel är 2.0.1, kommer versions typen att kategoriseras på följande sätt:

|Större | Mindre  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure blockchain-tjänsten uppdaterar automatiskt korrigerings versioner av kvorum till befintliga aktiva medlemmar inom 30 dagar från det att de har gjorts tillgängliga från kvorum.

## <a name="availability-of-new-ledger-versions"></a>Tillgänglighet för nya redovisnings versioner

Azure blockchain-tjänsten tillhandahåller de senaste högre och lägre versionerna av kvorum-redovisningen inom 60 dagar som är tillgängliga från kvorumdisken. Det finns högst fyra mindre versioner av konsortier som du kan välja mellan när du konfigurerar en ny medlem och konsortiet. Det finns för närvarande inte stöd för att uppgradera från till en större eller mindre version. Om du till exempel kör version 2. x stöds inte en uppgradering till version 3. x för närvarande. Om du kör version 2,2 på samma sätt stöds inte en uppgradering till version 2,3 för närvarande.

## <a name="how-to-check-quorum-ledger-version"></a>Kontrol lera om det finns en version av kvorum redovisning

Du kan kontrol lera kvorumloggen på din Azure blockchain service-medlem genom att ansluta till noden med Geth eller Visa diagnostikloggar.

### <a name="using-geth"></a>Använda Geth

Anslut till din Azure blockchain service-nod med hjälp av Geth. Till exempel `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

När noden är ansluten rapporterar Geth den som liknar följande utdata:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Mer information om hur du använder Geth finns i [snabb start: Använd Geth för att ansluta till en Azure blockchain service Transaction-nod](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Använda diagnostikloggar

Om du aktiverar diagnostikloggar rapporteras kvorumloggen för Transaction Nodes. Följande nod i informations logg meddelandet innehåller exempelvis kvorumloggen.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Mer information om diagnostiska loggar finns i [övervaka Azure blockchain-tjänsten via Azure Monitor](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Så här kontrollerar du Genesis-filinnehållet

Om du vill kontrol lera Genesis-filinnehållet i blockchain-noden kan du använda följande Ethereum Java Script-API:

``` bash
admin.nodeInfo.protocols
```
Du kan anropa API: et med hjälp av en Geth-konsol eller ett Web3-bibliotek. Mer information om hur du använder Geth finns i [snabb start: Använd Geth för att ansluta till en Azure blockchain service Transaction-nod](connect-geth.md).

## <a name="next-steps"></a>Nästa steg

[Gränser i Azure blockchain-tjänsten](limits.md)
