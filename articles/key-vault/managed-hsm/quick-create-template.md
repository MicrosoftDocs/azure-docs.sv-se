---
title: Azure snabbstart – Skapa en hanterad HSM med en Azure Resource Manager mall
description: Snabbstart som visar hur du skapar Azure Azure Key Vault Managed HSM med hjälp Resource Manager mall
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 33c262c61d50b45663a627e40ea186f1f0dcde41
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532961"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa en Key Vault HSM med en Azure Resource Manager mall

Managed HSM är en fullständigt hanterad molntjänst som uppfyller standardkrav för en enskild klientorganisation och som gör att du kan skydda kryptografiska nycklar för dina molnprogram med **FIPS 140-2 Level 3-verifierade** HSM:er.  

Den här snabbstarten fokuserar på processen att distribuera en Resource Manager för att skapa en hanterad HSM.  En [Resource Manager-mall](../../azure-resource-manager/templates/overview.md) är en JSON-fil (JavaScript Object Notation) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Om du vill veta mer om hur du utvecklar Resource Manager-mallar kan du läsa mer i [Resource Manager-dokumentationen](../../azure-resource-manager/index.yml) och [mallreferensen](/azure/templates/microsoft.keyvault/allversions).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln måste du ha följande:

- En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
- Azure CLI version 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du gå [till Installera Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggningsalternativ via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Skapa en hanterad HSM

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

Azure-resursen som definierats i mallen:

* **Microsoft.KeyVault/managedHSM:** skapa en Azure Key Vault HSM.

Fler Azure Key Vault mallexempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

Mallen kräver det objekt-ID som är associerat med ditt konto. Du hittar den genom att använda kommandot Azure CLI [az ad user show](/cli/azure/ad/user#az_ad_user_show) och skicka din e-postadress till `--id` parametern . Du kan begränsa utdata till objekt-ID:t endast med `--query` parametern .

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Du kan också behöva ditt klientorganisations-ID. Du hittar den genom att använda kommandot Azure CLI [az ad user show.](/cli/azure/account#az_account_show) Du kan begränsa utdata till klientorganisations-ID med `--query` parametern .

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.

    Om det inte anges använder du standardvärdet för att skapa nyckelvalvet och en hemlighet.

    - **Prenumeration:** Välj en Azure-prenumeration.
    - **Resursgrupp:** Välj **Skapa ny,** ange ett unikt namn för resursgruppen och klicka sedan på **OK.**
    - **Plats:** Välj en plats. Till exempel USA, **södra centrala.**
    - **managedHSMName:** Ange ett namn för din hanterade HSM.
    - **Klientorganisations-ID:** Mallfunktionen hämtar automatiskt ditt klientorganisations-ID; ändra inte standardvärdet.  Om det inte finns något värde anger du det klientorganisations-ID som du hämtade [i Krav.](#prerequisites)
    * **initialAdminObjectIds:** Ange det objekt-ID som du hämtade [i Krav.](#prerequisites)

3. Välj **Köp**. När nyckelvalvet har distribuerats får du ett meddelande:

Azure-portalen används för att distribuera mallen. Förutom de här Azure Portal kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en hanterad HSM. Den här hanterade HSM:en fungerar inte fullt ut förrän den har aktiverats. Mer [information om hur du aktiverar din HSM](quick-create-cli.md#activate-your-managed-hsm) finns i Aktivera din hanterade HSM.

- Läs en [översikt över Managed HSM](overview.md)
- Lär dig mer [om att hantera nycklar i en hanterad HSM](key-management.md)
- Läs [metodtips för Managed HSM](best-practices.md)
