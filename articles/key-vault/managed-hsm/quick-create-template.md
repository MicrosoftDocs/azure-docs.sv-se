---
title: Azure snabb start – skapa en hanterad HSM med hjälp av en Azure Resource Manager mall
description: Snabb start visar hur du skapar Azure en Azure Key Vault hanterad HSM med Resource Manager-mall
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d9fb9e0221ad6a5749899c89bbd9dc5631e7a91c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213274"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Snabb start: skapa en Key Vault hanterad HSM med en Azure Resource Manager-mall

Hanterad HSM är en fullständigt hanterad moln tjänst med hög tillgänglighet och en standard som ger dig möjlighet att skydda krypterings nycklar för dina moln program med hjälp av **FIPS 140-2 nivå 3** -verifierade HSM: er.  

Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa en hanterad HSM.  En [Resource Manager-mall](../../azure-resource-manager/templates/overview.md) är en JSON-fil (JavaScript Object Notation) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Om du vill veta mer om hur du utvecklar Resource Manager-mallar kan du läsa mer i [Resource Manager-dokumentationen](../../azure-resource-manager/index.yml) och [mallreferensen](/azure/templates/microsoft.keyvault/allversions).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du ha följande objekt:

- En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
- Azure CLI-versionen 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggnings alternativ via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Skapa en hantera HSM

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

Den Azure-resurs som definierats i mallen:

* **Microsoft. nyckel valv/managedHSMs**: skapa en Azure Key Vault hanterad HSM.

Fler Azure Key Vault mall-exempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

För mallen krävs det objekt-ID som är kopplat till ditt konto. Du hittar det genom att använda Azure CLI- [AZ AD User show](/cli/azure/ad/user#az_ad_user_show) -kommandot och skicka din e-postadress till `--id` parametern. Du kan begränsa utdata till objekt-ID: t med `--query` parametern.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Du kan också behöva ditt klient-ID. För att hitta det använder du kommandot Azure CLI [AZ AD User show](/cli/azure/account#az_account_show) . Du kan begränsa utdata till klient-ID: t med `--query` parametern.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.

    Om den inte anges använder du standardvärdet för att skapa nyckel valvet och en hemlighet.

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    - **Plats**: Välj en plats. Till exempel **södra centrala USA**.
    - **managedHSMName**: Ange ett namn för din hanterade HSM.
    - **Klient-ID**: funktionen mall hämtar automatiskt klient-ID: t. Ändra inte standardvärdet.  Om det inte finns något värde anger du det klient-ID som du hämtade i [krav](#prerequisites).
    * **initialAdminObjectIds**: Ange det objekt-ID som du hämtade i [krav](#prerequisites).

3. Välj **Köp**. När nyckel valvet har distribuerats får du ett meddelande:

Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterad HSM. Den här hanterade HSM: en fungerar inte fullständigt förrän den är aktive rad. Se [aktivera din hanterade HSM](quick-create-cli.md#activate-your-managed-hsm) för att lära dig hur du aktiverar din HSM.

- Läs en [Översikt över hanterad HSM](overview.md)
- Lär dig mer om att [Hantera nycklar i en hanterad HSM](key-management.md)
- Granska [metod tips för hanterad HSM](best-practices.md)
