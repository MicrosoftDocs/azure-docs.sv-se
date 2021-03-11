---
title: Skapa en virtuell Linux-dator i Azure från en mall
description: Så här använder du Azure CLI för att skapa en virtuell Linux-dator från en Resource Manager-mall
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554691"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Så här skapar du en virtuell Linux-dator med Azure Resource Manager mallar

Lär dig hur du skapar en virtuell Linux-dator med hjälp av en Azure Resource Manager-mall och Azure CLI från Azure Cloud Shell. Information om hur du skapar en virtuell Windows-dator finns i [skapa en virtuell Windows-dator från en Resource Manager-mall](../windows/ps-template.md).

Ett alternativ är att distribuera mallen från Azure Portal. Öppna mallen i portalen genom att välja knappen **distribuera till Azure** .

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Översikt över mallar

Azure Resource Manager mallar är JSON-filer som definierar infrastrukturen och konfigurationen för din Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om mallens format och hur du skapar den finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiera resurser i Azure Resource Manager-mallar).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Att skapa en virtuell Azure-dator innehåller vanligt vis två steg:

1. Skapa en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn.
1. Skapa en virtuell dator.

I följande exempel skapas en virtuell dator från en [Azure snabb starts mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Endast SSH-autentisering tillåts för den här distributionen. När du uppmanas till det anger du värdet för din egen offentliga SSH-nyckel, till exempel innehållet i *~/.ssh/id_rsa. pub*. Om du behöver skapa ett SSH-nyckelpar kan du läsa [så här skapar du och använder ett SSH-nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md). Här är en kopia av mallen:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Om du vill köra CLI-skriptet väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Det sista Azure CLI-kommandot visar den offentliga IP-adressen för den nyligen skapade virtuella datorn. Du behöver den offentliga IP-adressen för att ansluta till den virtuella datorn. Se nästa avsnitt i den här artikeln.

I föregående exempel angav du en mall som lagrats i GitHub. Du kan också hämta eller skapa en mall och ange den lokala sökvägen med `--template-file` parametern.

Här följer några ytterligare resurser:

- Information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/index.yml).
- Information om Azures virtuella dator scheman finns i [referens för Azure-mallar](/azure/templates/microsoft.compute/allversions).
- För att se fler exempel på mallar för virtuella datorer, se [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Du kan sedan använda SSH till den virtuella datorn som normalt. Ange en offentlig IP-adress från föregående kommando:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Nästa steg

I det här exemplet har du skapat en enkel virtuell Linux-dator. Om du vill ha fler Resource Manager-mallar som innehåller program ramverk eller skapa mer komplexa miljöer kan du bläddra i [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Om du vill veta mer om hur du skapar mallar kan du Visa JSON-syntaxen och egenskaperna för de resurs typer som du har distribuerat:

- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
