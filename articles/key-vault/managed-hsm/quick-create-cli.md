---
title: Snabb start – etablera och aktivera en Azure-hanterad HSM
description: Snabb start visar hur du etablerar och aktiverar en hanterad HSM med Azure CLI
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91756831"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Snabb start: etablera och aktivera en hanterad HSM med Azure CLI

Azure Key Vault hanterad HSM är en fullständigt hanterad moln tjänst med hög tillgänglighet, en standard som är kompatibel med en enda klient som hjälper dig att skydda krypterings nycklar för dina moln program med hjälp av **FIPS 140-2 nivå 3** -verifierade HSM: er. Mer information om hanterad HSM får du genom att granska [översikten](overview.md). 

I den här snabb starten skapar du och aktiverar en hanterad HSM med Azure CLI. När du har gjort det kommer du att lagra en hemlighet.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI-versionen 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* En hanterad HSM i din prenumeration. Se [snabb start: etablera och aktivera en hanterad HSM med Azure CLI](quick-create-cli.md) för att etablera och aktivera en hanterad HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *ContosoResourceGroup* på *eastus2* -platsen.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Skapa en hanterad HSM

Att skapa en hanterad HSM är en process i två steg:
1. Etablera en hanterad HSM-resurs.
1. Aktivera din hanterade HSM genom att hämta säkerhets domänen.

### <a name="provision-a-managed-hsm"></a>Etablera en hanterad HSM

Använd `az keyvault create` kommandot för att skapa en hanterad HSM. Det här skriptet har tre obligatoriska parametrar: ett resurs grupps namn, ett HSM-namn och den geografiska platsen.

Du måste ange följande indata för att skapa en hanterad HSM-resurs:
- En resurs grupp där den kommer att placeras i din prenumeration.
- Azure-plats.
- En lista med inledande administratörer.

Exemplet nedan skapar en HSM med namnet **ContosoMHSM** i resurs gruppen  **ContosoResourceGroup**, som finns på platsen **östra USA 2** , med **den aktuella inloggade användaren** som administratör.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Det kan ta några minuter att skapa kommandot. När den har returnerats är du redo att aktivera din HSM.

Utdata från det här kommandot visar egenskaper för den hanterade HSM som du har skapat. De två viktigaste egenskaperna är:

* **namn**: i exemplet är namnet ContosoMHSM. Du kommer att använda det här namnet för andra Key Vault-kommandon.
* **hsmUri**: i exemplet är URI: n " https://contosohsm.managedhsm.azure.net ." Program som använder din HSM via sin REST API måste använda denna URI.

Ditt Azure-konto har nu behörighet att utföra alla åtgärder på den här hanterade HSM. Från och med har ingen annan behörighet.

### <a name="activate-your-managed-hsm"></a>Aktivera din hanterade HSM

Alla data Plans kommandon är inaktiverade tills HSM har Aktiver ATS. Du kommer inte att kunna skapa nycklar eller tilldela roller. Endast de angivna administratörer som tilldelades under kommandot CREATE kan aktivera HSM. Om du vill aktivera HSM måste du ladda ned [säkerhets domänen](security-domain.md).

För att aktivera din HSM behöver du:
- Minst 3 RSA-nyckelpar (max 10)
- Ange minsta antal nycklar som krävs för att dekryptera säkerhets domänen (kvorum)

Om du vill aktivera HSM skickar du minst 3 (maximalt 10) RSA-offentliga nycklar till HSM. HSM krypterar säkerhets domänen med dessa nycklar och skickar tillbaka den. När den här säkerhets domänen har hämtats är din HSM klar att användas. Du måste också ange kvorum, vilket är det minsta antalet privata nycklar som krävs för att dekryptera säkerhets domänen.

Exemplet nedan visar hur du kan använda  `openssl` för att generera tre självsignerade certifikat.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Skapa och lagra RSA-nyckelpar och säkerhets domän fil som genererats i det här steget på ett säkert sätt.

Använd `az keyvault security-domain download` kommandot för att hämta säkerhets domänen och aktivera hanterad HSM. Exemplet nedan använder 3 RSA-nyckelpar (endast offentliga nycklar krävs för det här kommandot) och anger kvorumet till 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Lagra säkerhets domän filen och RSA-nyckel paren på ett säkert sätt. Du behöver dem för haveri beredskap eller för att skapa en annan hanterad HSM som delar samma säkerhets domän, så att de kan dela nycklar.

När du har hämtat säkerhets domänen kommer HSM att vara i aktivt läge och redo att användas.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.

När du inte behöver resursgruppen längre kan du använda kommandot [az group delete](/cli/azure/group) till att ta bort resursgruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en hemlighet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över hanterad HSM](overview.md)
- Lär dig mer om att [Hantera nycklar i en hanterad HSM](key-management.md)
- Granska [metod tips för hanterad HSM](best-practices.md)
