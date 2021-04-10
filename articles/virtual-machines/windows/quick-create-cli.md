---
title: Snabb start – skapa en virtuell Windows-dator med hjälp av Azure CLI
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att skapa en virtuell Windows-dator
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556255"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Snabbstart: Skapa en virtuell Windows-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2016. För att se hur den virtuella datorn fungerar i praktiken ansluter du till den med RDP och installerar IIS-webbservern.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM*. I det här exemplet används *azureuser* för ett administrativt användar namn. 

Du måste ange ett lösen ord som uppfyller [lösen ords kraven för virtuella Azure-datorer](./faq.md#what-are-the-password-requirements-when-creating-a-vm
). I exemplet nedan uppmanas du att ange ett lösen ord på kommando raden. Du kan också lägga till `--admin-password` parametern med ett värde för ditt lösen ord. Användar namnet och lösen ordet kommer att användas senare när du ansluter till den virtuella datorn.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Skriv ned din egen `publicIpAddress` i utdataresultatet från din virtuella dator. Den här adressen används för att ansluta till den virtuella datorn i nästa steg.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard öppnas endast RDP-anslutningar när du skapar en virtuell Windows-dator i Azure. Använd [az vm open-port](/cli/azure/vm) för att öppna TCP port 80 för användning med IIS-webbservern:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en fjärrskrivbordssession från den lokala datorn. Ersätt IP-adressen med den virtuella datorns offentliga IP-adress. När du uppmanas att göra det anger du autentiseringsuppgifterna som användes när den virtuella datorn skapades:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

När IIS har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för IIS. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. I följande exempel visas standardwebbplatsen för IIS:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
