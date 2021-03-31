---
title: Hantera Azure blockchain-tjänsten med hjälp av Azure CLI
description: Hantera Azure blockchain-tjänsten med Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87170854"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Hantera Azure blockchain-tjänsten med hjälp av Azure CLI

Förutom Azure Portal kan du använda Azure CLI för att hantera blockchain-medlemmar och Transaction-noder för Azure blockchain-tjänsten.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

    Logga in med kommandot [az login](/cli/azure/reference-index#az-login) om du använder en lokal CLI-installation.

    ```azurecli
    az login
    ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera Azure CLI-tillägget.

    När du arbetar med tilläggsreferenser för Azure CLI måste du först installera tillägget.  Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu.  Läs mer om tillägg, bland annat hur du uppdaterar och avinstallerar dem, i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Installera [tillägget för Azure blockchain-tjänsten](/cli/azure/ext/blockchain/blockchain) genom att köra följande kommando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Skapa blockchain-medlem

Exempel [skapar en blockchain-medlem](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) i Azure blockchain-tjänsten som kör kvorumet redovisnings protokoll i ett nytt konsortium.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`. |
| **sökvägen** | Azure-region där blockchain-medlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. |
| **lösenord** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;)|
| **protokollhanterare** | Blockchain-protokoll. För närvarande stöds *kvorum* protokoll. |
| **Consortium** | Namnet på konsortiet att ansluta till eller skapa. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md). |
| **konsortiet-Management-Account-Password** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier. |
| **SKU** | Nivå typ. *Standard* eller *Basic*. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Du bör också använda *standard* nivån om du använder blockchain Data Manager eller när du skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Ändra blockchain-medlems lösen ord eller brand Väggs regler

Exempel [uppdaterar en blockchain-medlems](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)lösen ord, konsortiets hanterings lösen ord och brand Väggs regel.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Namn som identifierar din Azure blockchain service-medlem. |
| **lösenord** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;)|
| **konsortiet-Management-Account-Password** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier. |
| **brand vägg – regler** | Start-och slut-IP-adress för listan över tillåtna IP-adresser. |

## <a name="create-transaction-node"></a>Skapa Transaction-nod

[Skapa en Transaction-nod](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) i en befintlig blockchain-medlem. Genom att lägga till Transaction Nodes kan du öka säkerhets isoleringen och distribuera belastningen. Du kan till exempel ha en Transaction Node-slutpunkt för olika klient program.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **sökvägen** | Blockchain-medlemmens Azure-region. |
| **medlems namn** | Namn som identifierar din Azure blockchain service-medlem. |
| **lösenord** | Lösen ordet för Transaction-noden. Använd lösen ordet för grundläggande autentisering vid anslutning till den offentliga slut punkten för Transaction-noden. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;)|
| **Namn** | Nodnamn för transaktion. |

## <a name="change-transaction-node-password"></a>Ändra lösen ord för Transaction Node

Exempel [uppdaterar ett](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) lösen ord för Transaction Node.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **medlems namn** | Namn som identifierar din Azure blockchain service-medlem. |
| **lösenord** | Lösen ordet för Transaction-noden. Använd lösen ordet för grundläggande autentisering vid anslutning till den offentliga slut punkten för Transaction-noden. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;)|
| **Namn** | Nodnamn för transaktion. |

## <a name="list-api-keys"></a>Visa lista över API-nycklar

API-nycklar kan användas för Node-åtkomst liknande användar namn och lösen ord. Det finns två API-nycklar som stöder nyckel rotation. Använd följande kommando för att [lista dina API-nycklar](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain service-blockchain medlem |

## <a name="regenerate-api-keys"></a>Återskapa API-nycklar

Använd följande kommando för att [återskapa dina API-nycklar](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem. |
| **keyName** | Ersätt \<keyValue\> med antingen KEY1, key2 eller båda. |

## <a name="delete-a-transaction-node"></a>Ta bort en Transaction-nod

Exempel [tar bort en blockchain-medlems transaktions nod](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **medlems namn** | Namnet på Azure blockchain service-blockchain medlem som också innehåller det transaktions nodnamn som ska tas bort. |
| **Namn** | Nodnamn som ska tas bort. |

## <a name="delete-a-blockchain-member"></a>Ta bort en blockchain-medlem

Exempel [tar bort en blockchain-medlem](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på den Azure blockchain-tjänst blockchain-medlem som ska tas bort. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Bevilja åtkomst för Azure AD-användare

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person** | Användar-ID för Azure AD. Till exempel `user@contoso.com` |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

**Exempel:**

Bevilja noden åtkomst för Azure AD-användare till blockchain- **medlemmen**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exempel:**

Bevilja Node-åtkomst för Azure AD-användare till blockchain- **transaktions nod**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Bevilja Node-åtkomst för Azure AD-grupp eller-program roll

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person-objekt-ID** | Azure AD-grupp-ID eller program-ID. |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

**Exempel:**

Bevilja noden åtkomst för **program rollen**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Ta bort åtkomst till Azure AD-nod

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person** | Användar-ID för Azure AD. Till exempel `user@contoso.com` |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar noder i Azure blockchain service Transactions med Azure Portal](configure-transaction-nodes.md).
