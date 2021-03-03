---
title: Konfigurera blockchain Data Manager med Azure CLI – Azure blockchain-tjänsten
description: Skapa och hantera en blockchain-Data Manager för Azure blockchain-tjänsten med hjälp av Azure CLI
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 867a51b60afa56005bbb297b345f8a9260160ab8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722644"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurera Blockchain Data Manager med Azure-portalen

Konfigurera blockchain Data Manager för Azure blockchain-tjänsten för att avbilda blockchain-data till ett Azure Event Grid-ämne.

Om du vill konfigurera en blockchain Data Manager instans:

* Skapa en blockchain Manager-instans
* Skapa en inmatare för en Azure blockchain service Transaction-nod
* Skapa ett utdata till ett Azure Event Grid ämne
* Lägg till ett blockchain-program
* Starta en instans

## <a name="prerequisites"></a>Förutsättningar

* Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) och logga in med `az login` .
* Slutför [snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md). *Standard* nivån för Azure blockchain service rekommenderas när du använder blockchain Data Manager.
* Skapa ett [Event Grid ämne](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelse hanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Skapa instans

En blockchain Data Manager-instans övervakar en Azure blockchain service Transaction-nod. En instans fångar alla RAW-block och rå transaktions data från Transaction-noden. Blockchain Data Manager publicerar ett **RawBlockAndTransactionMsg** -meddelande som är en supermängd av information som returneras från web3. ETH [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) och [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) -frågor.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resurs grupp namn där du vill skapa blockchain Data Manager-instansen. |
| name | Namnet på blockchain Data Manager-instansen. |
| resurs typ | Resurs typen för en blockchain Data Manager-instans är **Microsoft. blockchain/Watcher**. |
| är-fullständig-objekt | Anger att egenskaper innehåller alternativ för övervaknings resursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för övervaknings resursen. Kan skickas som en sträng eller en fil.  |

### <a name="create-instance-examples"></a>Skapa instans exempel

Exempel på JSON-konfiguration för att skapa en blockchain Manager-instans i regionen **USA, östra** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där du skapar övervaknings resursen |
| properties | Egenskaper som ska anges när övervaknings resursen skapas |

Skapa en blockchain Data Manager-instans med namnet *prewatcher* med en JSON-sträng för konfiguration.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Skapa en blockchain Data Manager-instans med namnet *prewatcher* med en JSON-konfigurationsfil.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Skapa inmatade

En indatamängd ansluter blockchain Data Manager till en Azure blockchain service Transaction-nod. Endast användare med åtkomst till Transaction-noden kan skapa en anslutning.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resurs grupp namn där du vill skapa en inmatad resurs. |
| name | Inmatade namn. |
| namnområde | Använd **Microsoft. blockchain** -Providerns namn område. |
| resurs typ | Resurs typen för en blockchain Data Manager **indata är indata.** |
| parent | Sökvägen till den Övervakare som indatamängden är kopplad till. Till exempel **bevakare/** Watcher. |
| är-fullständig-objekt | Anger att egenskaper innehåller alternativ för inmatad resurs. |
| properties | JSON-formaterad sträng som innehåller egenskaper för inmatad resurs. Kan skickas som en sträng eller en fil. |

### <a name="input-examples"></a>Inmatade exempel

Konfigurations-JSON-exempel för att skapa en inmatad resurs i regionen *USA, östra* som är ansluten till \<Blockchain member\> .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där du vill skapa den angivna resursen. |
| inputType | Ledger-typ för Azure blockchain-tjänstemedlem. **Ethereum** stöds för närvarande. |
| resourceId | Transaction-nod som indatamängden är ansluten till. Ersätt \<Subscription ID\> , \<Resource group\> och \<Blockchain member\> med värdena för Transaction Node-resursen. Indatamängden ansluter till noden standard transaktion för Azure blockchain-tjänstemedlemen. |

Skapa ett *inmatat värde med namnet mina* invärden för *prewatcher* med en JSON-sträng för konfiguration.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Skapa ett indata med namnet mina *indata* för en *Watcher* -fil med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Skapa utdata

En utgående anslutning skickar blockchain-data till Azure Event Grid. Du kan skicka blockchain-data till ett enda mål eller skicka blockchain-data till flera mål. Blockchain Data Manager har stöd för flera Event Grid ämnen utgående anslutningar för en specifik blockchain Data Manager-instans.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resurs grupp namn där du vill skapa en utgående resurs. |
| name | Namn på utdata. |
| namnområde | Använd **Microsoft. blockchain** -Providerns namn område. |
| resurs typ | Resurs typen för en blockchain Data Manager utdata är **utdata**. |
| parent | Sökvägen till den Övervakare som utdata är kopplad till. Till exempel **bevakare/** Watcher. |
| är-fullständig-objekt | Anger att egenskaper innehåller alternativ för utgående resurs. |
| properties | JSON-formaterad sträng som innehåller egenskaper för utgående resurs. Kan skickas som en sträng eller en fil. |

### <a name="output-examples"></a>Exempel på utdata

Konfigurations-JSON-exempel för att skapa en utgående resurs i regionen *USA, östra* som är ansluten till ett event Grid-ämne med namnet \<event grid topic\> .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där du vill skapa utdata-resursen. |
| outputType | Typ av utdata. **EventGrid** stöds för närvarande. |
| resourceId | Resurs som resultatet är anslutet till. Ersätt \<Subscription ID\> , \<Resource group\> och \<Blockchain member\> med värdena för Event Grid-resursen. |

Skapa utdata med namnet mina *utdata* för *prewatcher* som ansluter till ett event Grid-ämne med hjälp av en JSON-konfigurationsfil.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Skapa utdata med namnet mina *utdata* för *prewatcher* som ansluter till ett event Grid-ämne med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Lägg till blockchain-program

Om du lägger till ett blockchain-program avkodar Data Manager blockchain händelse-och egenskaps tillstånd för programmet. Annars skickas endast rå block och rå transaktions information. Blockchain Data Manager identifierar också kontrakts adresser när kontraktet distribueras. Du kan lägga till flera blockchain-program till en blockchain Data Manager-instans.


> [!IMPORTANT]
> Blockchain-program som deklarerar [mat ris typer](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) eller [mappnings typer](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) stöds för närvarande inte fullt ut. Egenskaper som är deklarerade som matris-eller mappnings typer avkodas inte i *ContractPropertiesMsg* -eller *DecodedContractEventsMsg* -meddelanden.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resurs grupp namn där du vill skapa program resursen. |
| name | Namnet på programmet. |
| namnområde | Använd **Microsoft. blockchain** -Providerns namn område. |
| resurs typ | Resurs typen för ett blockchain Data Manager-program är **artefakter**. |
| parent | Sökvägen till den Övervakare som programmet är associerat med. Till exempel **bevakare/** Watcher. |
| är-fullständig-objekt | Anger att egenskaper innehåller alternativ för program resursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för program resursen. Kan skickas som en sträng eller en fil. |

### <a name="blockchain-application-examples"></a>Blockchain program exempel

Konfigurations-JSON-exempel för att skapa en program resurs i regionen *USA, östra* som övervakar ett smart kontrakt som definieras av kontrakt ABI och bytekod.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där program resursen ska skapas. |
| artifactType | Typ av program. **EthereumSmartContract** stöds för närvarande. |
| abiFileUrl | URL för ABI JSON-fil för smart kontrakt. Mer information om hur du skaffar kontrakts ABI och skapar en URL finns i [Hämta kontrakt ABI och bytekod](data-manager-portal.md#get-contract-abi-and-bytecode) och [skapa kontrakt ABI och bytekod URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL för smart kontrakts distribuerad bytekod-JSON-fil. Mer information om hur du hämtar det smarta kontraktets distribuerade bytekod och skapar en URL finns i [Hämta kontrakt ABI och bytekod](data-manager-portal.md#get-contract-abi-and-bytecode) och [skapa kontrakt ABI och bytekod URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Obs: blockchain Data Manager kräver den **distribuerade bytekod**. |
| queryTargetTypes | Publicerade meddelande typer. Ange **ContractProperties** publicerar *ContractPropertiesMsg* meddelande typ. Ange **ContractEvents** publicerar *DecodedContractEventsMsg* meddelande typ. Obs: *RawBlockAndTransactionMsg* -och *RawTransactionContractCreationMsg* -meddelande typer publiceras alltid. |

Skapa ett program med namnet ' Mina *program* för *bevakare* som övervakar ett smart kontrakt som definieras av en JSON-sträng.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Skapa ett program med namnet mina *program* för *tittare* som bevakar ett smart kontrakt som DEFINIERAts med en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Starta instans

När den körs övervakar en blockchain Manager-instans blockchain-händelser från de definierade indata och skickar data till de definierade utdatana.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beskrivning |
|-----------|-------------|
| åtgärd | Använd **Start** för att köra bevakaren. |
| kompatibilitet | Resurs-ID för övervakare. Ersätt \<Subscription ID\> , \<Resource group\> och \<Watcher name\> med värdena för övervaknings resursen.|

### <a name="start-instance-example"></a>Exempel på Start instans

Starta en blockchain Data Manager-instans med namnet *Watcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Stoppa instans

Stoppa en blockchain Data Manager-instans.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beskrivning |
|-----------|-------------|
| åtgärd | Använd **stopp** för att stoppa bevakaren. |
| kompatibilitet | Namn på övervakare. Ersätt \<Subscription ID\> , \<Resource group\> och \<Watcher name\> med värdena för övervaknings resursen. |

### <a name="stop-watcher-example"></a>Exempel på STOP Watcher

Stoppa en instans med namnet *Watcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Ta bort instans

Ta bort en blockchain Data Manager instans.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resurs grupp namn för den Övervakare som ska tas bort. |
| name | Namnet på den Övervakare som ska tas bort. |
| resurs typ | Resurs typen för en blockchain Data Manager övervakare är **Microsoft. blockchain/Watcher**. |

### <a name="delete-instance-example"></a>Ta bort instans exempel

Ta bort en instans med namnet *prewatcher* i resurs gruppen *myRG* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Nästa steg

Testa nästa självstudie och skapa en blockchain transaktions meddelande Utforskare med blockchain Data Manager och Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Använda Blockchain Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md)
