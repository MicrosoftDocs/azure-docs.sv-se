---
title: Kör Azure IoT Edge på Ubuntu Virtual Machines | Microsoft Docs
description: Azure IoT Edge installations anvisningar för Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e044e8102308fce4145d4aa6c887cefaa99be34
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629970"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Kör Azure IoT Edge på Ubuntu Virtual Machines

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter så små som Raspberry Pi eller lika stora som en industriell Server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet.

Mer information om hur IoT Edge runtime fungerar och vilka komponenter som ingår finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller stegen för att distribuera en virtuell dator med Ubuntu 18,04 LTS med Azure IoT Edge Runtime installerat och konfigurerat med en föranged enhets anslutnings sträng. Distributionen görs med hjälp av en [Cloud-Init-](../virtual-machines/linux/using-cloud-init.md
) baserad [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) som underhålls i projekt lagringen [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

Vid den första starten installerar Ubuntu 18,04 LTS Virtual Machine [den senaste versionen av Azure IoT Edge runtime via Cloud-Init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Den anger också en angiven anslutnings sträng innan körningen startar, så att du enkelt kan konfigurera och ansluta den IoT Edge enheten utan att behöva starta en SSH-eller fjärr skrivbords session. 

## <a name="deploy-using-deploy-to-azure-button"></a>Knappen distribuera med distribuera till Azure

[Knappen distribuera till Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) möjliggör strömlinjeformad distribution av [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) som finns på GitHub.  Det här avsnittet visar användningen av knappen distribuera till Azure som finns i [iotedge-VM-Deploy-](https://github.com/Azure/iotedge-vm-deploy) projektfilen.  


1. Vi distribuerar en Azure IoT Edge aktive rad virtuell Linux-dator med Azure Resource Manager mallen iotedge-VM-Deploy.  Börja genom att klicka på knappen nedan:

    [![Knappen Distribuera till Azure för iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Fyll i de tillgängliga formulär fälten i fönstret nyligen startade:

    > [!div class="mx-imgBorder"]
    > [![Skärmbild med mallen iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Prenumeration**: den aktiva Azure-prenumerationen för att distribuera den virtuella datorn till.

    **Resurs grupp**: en befintlig eller nyligen skapad resurs grupp som innehåller den virtuella datorn och dess associerade resurser.

    **DNS-etikett-prefix**: ett obligatoriskt värde som används för att använda den virtuella datorns värdnamn som prefix.

    **Admin-användar namn**: ett användar namn som kommer att tillhandahålla rot behörigheter för distribution.

    **Enhets anslutnings sträng**: en [enhets anslutnings sträng](./how-to-register-device.md) för en enhet som har skapats i den avsedda [IoT Hub](../iot-hub/about-iot-hub.md).

    **VM-storlek**: [storleken](../cloud-services/cloud-services-sizes-specs.md) på den virtuella dator som ska distribueras

    **Ubuntu OS-version**: den version av Ubuntu-operativsystem som ska installeras på den virtuella bas datorn.

    **Plats**: det [geografiska område](https://azure.microsoft.com/global-infrastructure/locations/) som används för att distribuera den virtuella datorn till, standardvärdet är platsen för den valda resurs gruppen.

    **Autentiseringstyp**: Välj **sshPublicKey** eller **lösen ord** beroende på dina inställningar.

    **Administratörs lösen ord eller nyckel**: värdet för den offentliga SSH-nyckeln eller värdet för lösen ordet beroende på valet av autentiseringstyp.

    När alla fält har fyllts i markerar du kryss rutan längst ned på sidan för att acceptera villkoren och väljer **köp** för att starta distributionen.

1. Verifiera att distributionen slutfördes korrekt.  En VR-resurs bör ha distribuerats till den valda resursgruppen.  Anteckna dator namnet. formatet bör vara i formatet `vm-0000000000000` . Notera även det associerade **DNS-namnet**, som ska ha formatet `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **DNS-namnet** kan hämtas från avsnittet **Översikt** i den nyligen distribuerade virtuella datorn i Azure-portalen.

    > [!div class="mx-imgBorder"]
    > [![Skärm bild som visar DNS-namnet för den virtuella iotedge-datorn](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Om du vill använda SSH till den här virtuella datorn efter installationen använder du det associerade **DNS-namnet** med kommandot:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Distribuera från Azure CLI

1. Kontrol lera att du har installerat Azure CLI IoT-tillägget med:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Om du använder Azure CLI på Skriv bordet startar du genom att logga in:

   ```azurecli-interactive
   az login
   ```

1. Om du har flera prenumerationer väljer du den prenumeration som du vill använda:
   1. Visa dina prenumerationer:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopiera SubscriptionID-fältet för den prenumeration du vill använda.

   1. Ange din arbets prenumeration med det ID som du kopierade:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Skapa en ny resurs grupp (eller ange en befintlig i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Skapa en ny virtuell dator:

    Om du vill använda en **authenticationType** av `password` , se exemplet nedan:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Om du vill autentisera med en SSH-nyckel kan du göra det genom att ange en **authenticationType** `sshPublicKey` och sedan ange värdet för SSH-nyckeln i **adminPasswordOrKey** -parametern.  Ett exempel på detta visas nedan.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Verifiera att distributionen slutfördes korrekt.  En VR-resurs bör ha distribuerats till den valda resursgruppen.  Anteckna dator namnet. formatet bör vara i formatet `vm-0000000000000` . Notera även det associerade **DNS-namnet**, som ska ha formatet `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **DNS-namnet** kan hämtas från JSON-formaterade utdata från föregående steg i avsnittet **utdata** som en del av den **offentliga SSH** -posten.  Värdet för den här posten kan användas för att använda SSH i till den nyligen distribuerade datorn.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS-namnet** kan också hämtas från **översikts** avsnittet på den nyligen distribuerade virtuella datorn i Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Skärm bild som visar DNS-namnet för den virtuella iotedge-datorn](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Om du vill använda SSH till den här virtuella datorn efter installationen använder du det associerade **DNS-namnet** med kommandot:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge enhet som har installerats med körnings miljön kan du [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge runtime-installationen kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

Om du vill öppna portar för att få åtkomst till den virtuella datorn via SSH eller andra inkommande anslutningar kan du läsa mer i Azure Virtual Machines-dokumentationen om hur [du öppnar portar och slut punkter till en virtuell Linux-dator](../virtual-machines/linux/nsg-quickstart.md)