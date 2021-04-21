---
title: Hantera Azure Blockchain Service med hjälp av Azure CLI
description: Så här hanterar du Azure Blockchain Service med Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 63401f5ce5cd35f63915e03b7f0362811d2660ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768061"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Hantera Azure Blockchain Service med hjälp av Azure CLI

Förutom den här Azure Portal kan du använda Azure CLI för att hantera blockkedjemedlemmar och transaktionsnoder för Azure Blockchain Service.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kan du gå till [Installera Azure CLI.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

    Logga in med kommandot [az login](/cli/azure/reference-index#az_login) om du använder en lokal CLI-installation.

    ```azurecli
    az login
    ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera Azure CLI-tillägget.

    När du arbetar med tilläggsreferenser för Azure CLI måste du först installera tillägget.  Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu.  Läs mer om tillägg, bland annat hur du uppdaterar och avinstallerar dem, i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Installera tillägget [för Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) genom att köra följande kommando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Skapa blockkedjemedlem

Exempel [skapar en blockkedjemedlem i](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) Azure Blockchain Service som kör kvorumregisterprotokollet i ett nytt konsortium.

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
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser skapas. |
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockkedjemedlem. Namnet används för den offentliga slutpunktsadressen. Till exempel `myblockchainmember.blockchain.azure.com`. |
| **Plats** | Azure-region där blockkedjemedlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. |
| **lösenord** | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen, 1 versal, 1 siffra och 1 specialtecken som inte är nummertecken(#), procent(%), kommatecken(,), star(*), bakåtcitat( \` ), dubbla citattecken("), enkelt citattecken('), bindestreck(-) och semikolon(;)|
| **Protokollet** | Blockkedjeprotokoll. *Kvorumprotokollet* stöds för närvarande. |
| **Konsortium** | Namnet på det konsortium som ska anslutas eller skapas. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md). |
| **consortium-management-account-password** | Lösenordet för konsortiumkontot kallas även för lösenordet för medlemskontot. Lösenordet för medlemskontot används för att kryptera den privata nyckeln för det Ethereum-konto som har skapats för din medlem. Du använder medlemskontot och medlemskontots lösenord för konsortiumhantering. |
| **Sku** | Nivåtyp. *Standard* eller *Basic*. Använd *Basic-nivån* för utveckling, testning och konceptbevis. Använd *standardnivån* för distributioner i produktionsklass. Du bör också använda *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan Basic och Standard när medlemmen har skapats. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Ändra blockkedjemedlemslösenord eller brandväggsregler

Exempel [uppdaterar blockkedjemedlemmens](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)lösenord, lösenord för konsortiumhantering och brandväggsregel.

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
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser skapas. |
| **Namn** | Namn som identifierar din Azure Blockchain Service medlem. |
| **lösenord** | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen, 1 versal, 1 siffra och 1 specialtecken som inte är nummertecken(#), procent(%), kommatecken(,), star(*), bakåtcitat( \` ), dubbla citattecken("), enkelt citattecken('), bindestreck(-) och semikolon(;)|
| **consortium-management-account-password** | Lösenordet för ett konsortiumkonto kallas även för lösenordet för medlemskontot. Lösenordet för medlemskontot används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlemskontot och medlemskontolösenordet för konsortiumhantering. |
| **brandväggsregler** | Start- och slut-IP-adress för listan över tillåtna IP-adresser. |

## <a name="create-transaction-node"></a>Skapa transaktionsnod

[Skapa en transaktionsnod](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) i en befintlig blockkedjemedlem. Genom att lägga till transaktionsnoder kan du öka säkerhetsisoleringen och fördela belastningen. Du kan till exempel ha en slutpunkt för transaktionsnoder för olika klientprogram.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser skapas. |
| **Plats** | Azure-regionen för blockkedjemedlemmen. |
| **medlemsnamn** | Namn som identifierar din Azure Blockchain Service medlem. |
| **lösenord** | Lösenordet för transaktionsnoden. Använd lösenordet för grundläggande autentisering vid anslutning till den offentliga slutpunkten för transaktionsnoden. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen, 1 versal, 1 siffra och 1 specialtecken som inte är nummertecken(#), procent(%), kommatecken(,), star(*), bakåtcitat( ), dubbla citattecken("), enkelt \` citattecken('), dash(-) och semikolon(;)|
| **Namn** | Namn på transaktionsnod. |

## <a name="change-transaction-node-password"></a>Ändra transaktionsnodslösenord

Exempel uppdaterar [ett lösenord för transaktionsnoden.](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update)

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser finns. |
| **medlemsnamn** | Namn som identifierar din Azure Blockchain Service medlem. |
| **lösenord** | Lösenordet för transaktionsnoden. Använd lösenordet för grundläggande autentisering vid anslutning till den offentliga slutpunkten för transaktionsnoden. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen, 1 versal, 1 siffra och 1 specialtecken som inte är nummertecken(#), procent(%), kommatecken(,), star(*), bakåtcitat( ), dubbla citattecken("), enkelt \` citattecken('), dash(-) och semikolon(;)|
| **Namn** | Namn på transaktionsnod. |

## <a name="list-api-keys"></a>Lista API-nycklar

API-nycklar kan användas för nodåtkomst på liknande sätt som användarnamn och lösenord. Det finns två API-nycklar som stöder nyckelrotation. Använd följande kommando för att visa [en lista över dina API-nycklar.](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser finns. |
| **Namn** | Namnet på Azure Blockchain Service blockkedjemedlem |

## <a name="regenerate-api-keys"></a>Återskapa API-nycklar

Använd följande kommando för att [återskapa DINA API-nycklar.](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser finns. |
| **Namn** | Namnet på Azure Blockchain Service blockkedjemedlem. |
| **Nyckelnamn** | Ersätt \<keyValue\> med antingen key1, key2 eller båda. |

## <a name="delete-a-transaction-node"></a>Ta bort en transaktionsnod

Exempel [tar bort en blockkedjemedlemstransaktionsnod](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser finns. |
| **medlemsnamn** | Namnet på den Azure Blockchain Service blockkedjemedlem som även innehåller det transaktionsnodnamn som ska tas bort. |
| **Namn** | Namn på transaktionsnod som ska tas bort. |

## <a name="delete-a-blockchain-member"></a>Ta bort en blockkedjemedlem

Exempel [tar bort en blockkedjemedlem](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser finns. |
| **Namn** | Namnet på den Azure Blockchain Service blockkedjemedlem som ska tas bort. |

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
| **Roll** | Namnet på Azure AD-rollen. |
| **assignee** | Azure AD-användar-ID. Till exempel `user@contoso.com` |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockkedjemedlem eller transaktionsnod. |

**Exempel:**

Bevilja nodåtkomst för Azure AD-användare till **blockkedjemedlem:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exempel:**

Bevilja nodåtkomst för Azure AD-användare till **blockkedjetransaktionsnod:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Bevilja nodåtkomst för Azure AD-grupp eller programroll

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Roll** | Namnet på Azure AD-rollen. |
| **assignee-object-id** | Grupp-ID eller program-ID för Azure AD. |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockkedjemedlem eller transaktionsnod. |

**Exempel:**

Bevilja nodåtkomst för **programrollen**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Ta bort azure AD-nodåtkomst

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Roll** | Namnet på Azure AD-rollen. |
| **assignee** | Användar-ID för Azure AD. Till exempel `user@contoso.com` |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockkedjemedlem eller transaktionsnod. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du konfigurerar Azure Blockchain Service-transaktionsnoder med Azure Portal](configure-transaction-nodes.md).
