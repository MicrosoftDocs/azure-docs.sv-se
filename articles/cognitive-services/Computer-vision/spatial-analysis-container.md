---
title: Så här installerar och kör du behållaren för rums analys – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Med behållaren för rums analys kan du identifiera personer och avstånd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d257a77940b460bf8be64e3f8376353a859365f7
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284727"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Installera och kör behållaren för rums analys (förhands granskning)

Med behållaren för rums analys kan du analysera direktuppspelad video i real tid för att förstå spatiala relationer mellan människor, deras rörelse och interaktioner med objekt i fysiska miljöer. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> för standard nivån S1 i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att köra behållaren för rums analys. Du kommer att använda din nyckel och slut punkt senare.


### <a name="spatial-analysis-container-requirements"></a>Krav för rums analys behållare

Om du vill köra rums analys behållaren behöver du en beräknings enhet med en [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). Vi rekommenderar att du använder [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) med GPU-acceleration, men behållaren körs på en annan stationär dator som uppfyller minimi kraven. Vi kommer att referera till den här enheten som värddator.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-enhet](#tab/azure-stack-edge)

Azure Stack Edge är en maskinvaru-som-tjänst-lösning och en AI-aktiverad Edge-baserad data behandlings enhet med funktioner för nätverks data överföring. Detaljerade anvisningar för förberedelse och installation finns i [Azure Stack Edge-dokumentationen](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Stationär dator](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Minsta maskinvarukrav

* 4 GB system-RAM
* 4 GB GPU-minne
* PROCESSOR med 8 kärnor
* 1 NVIDIA Tesla T4-GPU
* 20 GB HDD-utrymme

#### <a name="recommended-hardware"></a>Rekommenderad maskinvara

* 32 GB system-RAM
* 16 GB GPU-minne
* PROCESSOR med 8 kärnor
* 2 NVIDIA Tesla T4-GPU: er
* 50 GB SSD-utrymme

I den här artikeln ska du hämta och installera följande program varu paket. Värddatorn måste kunna köra följande (se nedan för instruktioner):

* [NVIDIA grafik driv rutiner](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) och [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Konfigurationer för [NVIDIA MP](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Multi-process service).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) och [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) Runtime.

#### <a name="azure-vm-with-gpu"></a>[Virtuell Azure-dator med GPU](#tab/virtual-machine)
I vårt exempel kommer vi att använda en [virtuell NC-serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med en K80-GPU.

---

| Krav | Beskrivning |
|--|--|
| Kamera | Behållaren för rums analys är inte knuten till ett speciellt kamera märke. Kamera enheten behöver: stöd för Real-Time Streaming Protocol (RTSP) och H. 264-kodning, är tillgänglig för värddatorn och kan strömmas vid 15FPS och 1080p-upplösning. |
| Linux-operativsystem | [Ubuntu Desktop 18,04-LTS](http://releases.ubuntu.com/18.04/) måste vara installerat på värddatorn.  |


## <a name="request-approval-to-run-the-container"></a>Begär godkännande för att köra behållaren

Fyll i och skicka [formuläret för begäran](https://aka.ms/csgate) för att begära godkännande för att köra behållaren.

Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det och skickar ett meddelande till dig med ett beslut.

> [!IMPORTANT]
> * I formuläret måste du använda en e-postadress som är kopplad till ett Azure-prenumerations-ID.
> * Den Visuellt innehåll resurs som du använder för att köra behållaren måste ha skapats med det godkända Azure-prenumerations-ID: t.

När du har godkänt kan du köra behållaren efter att ha laddat ned den från Microsoft Container Registry (MCR), som beskrivs längre fram i artikeln.

Du kommer inte att kunna köra behållaren om din Azure-prenumeration inte har godkänts.

## <a name="set-up-the-host-computer"></a>Konfigurera värddatorn

Vi rekommenderar att du använder en Azure Stack Edge-enhet för värddatorn. Klicka på **stationär dator** om du konfigurerar en annan enhet eller **virtuell dator** om du använder en virtuell dator.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-enhet](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Konfigurera beräkning på den Azure Stack Edge-portalen 
 
Rums analys använder Compute-funktionerna i Azure Stack Edge för att köra en AI-lösning. Om du vill aktivera beräknings funktionerna ser du till att: 

* Du har [anslutit och aktiverat](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) din Azure Stack Edge-enhet. 
* Du har ett Windows-klientsystem som kör PowerShell 5,0 eller senare för att få åtkomst till enheten.  
* Om du vill distribuera ett Kubernetes-kluster måste du konfigurera Azure Stack Edge-enheten via det **lokala användar gränssnittet** på [Azure Portal](https://portal.azure.com/): 
  1. Aktivera Compute-funktionen på Azure Stack Edge-enheten. Om du vill aktivera beräkning går du till **beräknings** sidan i webb gränssnittet för din enhet. 
  2. Välj ett nätverks gränssnitt som du vill aktivera för beräkning och klicka sedan på **Aktivera**. Detta skapar en virtuell växel på enheten i nätverks gränssnittet.
  3. Lämna IP-adresserna för Kubernetes-testnoden och IP-adresserna för Kubernetes externa tjänster tomma.
  4. Klicka på **Applicera**. Den här åtgärden kan ta ungefär två minuter. 

![Konfigurera beräkning](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Konfigurera en Edge Compute-roll och skapa en IoT Hub resurs

I [Azure Portal](https://portal.azure.com/)navigerar du till Azure Stack Edge-resursen. På sidan **Översikt** eller navigerings listan klickar du på knappen Edge Compute **Kom igång** . I panelen  **Konfigurera Edge Compute**   klickar du på **Konfigurera**. 

![Länk](media/spatial-analysis/configure-edge-compute-tile.png)

På sidan **Konfigurera Edge Compute**   väljer du en befintlig IoT Hub eller väljer att skapa en ny. Som standard används en standard pris nivå (S1) för att skapa en IoT Hub resurs. Om du vill använda en kostnads fri nivå IoT Hub resurs skapar du en och markerar den. IoT Hub resursen använder samma prenumeration och resurs grupp som används av Azure Stack Edge-resursen 

Klicka på **Skapa**. Det kan ta några minuter att skapa en IoT Hub resurs. När IoT Hub resurs har skapats, kommer den **Konfigurera Edge Compute** -panelen att uppdateras för att visa den nya konfigurationen. Bekräfta att Edge Compute-rollen har kon figurer ATS genom att välja **Visa konfiguration** på panelen **Konfigurera beräkning**   .

När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. Azure IoT Edge runtime körs redan på IoT Edge enheten.

> [!NOTE]
> * För närvarande stöds endast Linux-plattformen för IoT Edge enheter. Information om hur du felsöker Azure Stack Edge-enheten finns i artikeln om [loggning och fel sökning](spatial-analysis-logging.md) .
> * Mer information om hur du konfigurerar en IoT Edge-enhet för att kommunicera via en proxyserver finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Aktivera MPS på Azure Stack Edge 

1. Kör en Windows PowerShell-session som administratör. 

2. Kontrol lera att tjänsten Windows Remote Management körs på klienten. Använd följande kommando i PowerShell-terminalen 
    
    ```powershell
    winrm quickconfig
    ```
    
    Om du ser varningar om ett brand Väggs undantag kontrollerar du nätverks anslutnings typen och läser [Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) -dokumentationen.

3. Tilldela en variabel till enhetens IP-adress. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Använd följande kommando för att lägga till en enhets IP-adress i klientens lista över betrodda värdar: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Starta en Windows PowerShell-session på enheten. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Ange lösen ordet när du uppmanas till det. Använd samma lösen ord som används för att logga in på det lokala webb gränssnittet. Standard lösen ordet för det lokala webb gränssnittet är `Password1` .

Skriv `Start-HcsGpuMPS` för att starta MPS-tjänsten på enheten. 

Information om hur du felsöker Azure Stack Edge-enheten finns i [felsöka Azure Stack Edge-enheten](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Stationär dator](#tab/desktop-machine)

Följ de här anvisningarna om värddatorn inte är en Azure Stack Edge-enhet.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Installera NVIDIA CUDA Toolkit och nvidia graphics-drivrutiner på värddatorn

Använd följande bash-skript för att installera de nödvändiga nvidia graphics-drivrutinerna och CUDA Toolkit.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Starta om datorn och kör följande kommando.

```bash
nvidia-smi
```

Du bör se följande utdata.

![NVIDIA driv rutins utdata](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Installera Docker CE och NVIDIA-docker2 på värddatorn

Installera Docker CE på värddatorn.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installera *NVIDIA-Docker-2-* programpaketet.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Aktivera NVIDIA-MPS på värddatorn

> [!TIP]
> * Installera inte MP om din GPU Compute-kapacitet är mindre än 7. x (för Volta). Se [CUDA-kompatibilitet](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) för referens. 
> * Kör MPS-instruktionerna från ett terminalfönster på värddatorn. Inte i Docker-behållar instansen.

För bästa prestanda och användning konfigurerar du värddatorns GPU (er) för [NVIDIA multiprocess service (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Kör MPS-instruktionerna från ett terminalfönster på värddatorn.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Konfigurera Azure IoT Edge på värddatorn

Om du vill distribuera behållaren för rums analys på värddatorn skapar du en instans av en [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) -tjänst som använder standard pris nivån (S1) eller kostnads fri (F0). 

Använd Azure CLI för att skapa en instans av Azure IoT Hub. Ersätt parametrarna där det är lämpligt. Du kan också skapa Azure-IoT Hub på [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Se [region support](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) för tillgängliga regioner.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Du måste installera [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) version 1.0.9. Följ de här stegen för att ladda ned rätt version:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopiera den genererade listan.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installera den offentliga nyckeln för Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Uppdatera paket listorna på enheten.

```bash
sudo apt-get update
```

Installera 1.0.9-versionen:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registrera sedan värddatorn som en IoT Edge enhet i IoT Hub-instansen med hjälp av en [anslutnings sträng](../../iot-edge/how-to-register-device.md).

Du måste ansluta IoT Edge-enheten till Azure-IoT Hub. Du måste kopiera anslutnings strängen från den IoT Edge enhet som du skapade tidigare. Du kan också köra kommandot nedan i Azure CLI.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

På värddatorn är öppen  `/etc/iotedge/config.yaml` för redigering. Ersätt `ADD DEVICE CONNECTION STRING HERE` med anslutnings strängen. Spara och stäng filen. Kör det här kommandot för att starta om IoT Edge tjänsten på värddatorn.

```bash
sudo systemctl restart iotedge
```

Distribuera behållaren för rums analys som en IoT-modul på värddatorn, antingen från [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) eller [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows). Om du använder portalen ställer du in bild-URI: en till platsen för Azure Container Registry. 

Använd stegen nedan för att distribuera behållaren med hjälp av Azure CLI.

#### <a name="azure-vm-with-gpu"></a>[Virtuell Azure-dator med GPU](#tab/virtual-machine)

En virtuell Azure-dator med en GPU kan också användas för att köra spatial analys. Exemplet nedan använder en virtuell [NC-serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med en K80-GPU.

#### <a name="create-the-vm"></a>Skapa den virtuella datorn

Öppna guiden [skapa en virtuell dator](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) i Azure Portal.

Ge den virtuella datorn ett namn och välj den region som ska vara (USA) västra USA 2. Se till att du anger `Availability Options` "ingen infrastruktur redundans krävs". Se bilden nedan för den fullständiga konfigurationen och nästa steg för att hitta rätt VM-storlek. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Konfigurations information för den virtuella datorn." lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Om du vill hitta storleken på den virtuella datorn väljer du "Se alla storlekar" och visar sedan listan för "icke-Premium Storage VM-storlekar", som visas nedan.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Storlekar på virtuella datorer." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Välj sedan antingen **för nc6** eller **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="Val av kampanj" lightbox="media/spatial-analysis/promotional-selection.png":::

Skapa sedan den virtuella datorn. När du har skapat navigerar du till den virtuella dator resursen i Azure Portal och väljer `Extensions` i det vänstra fönstret. Fönstret tillägg visas med alla tillgängliga tillägg. Välj `NVIDIA GPU Driver Extension` , klicka på Skapa och Slutför guiden.

När tillägget har tillämpats går du till den virtuella datorns huvud sida i Azure Portal och klickar på `Connect` . Den virtuella datorn kan nås via SSH eller RDP. RDP är användbart eftersom det kommer att aktivera visning av visualiserade fönster (förklaras senare). Konfigurera RDP-åtkomst genom att följa [dessa steg](../../virtual-machines/linux/use-remote-desktop.md) och öppna en fjärr skrivbords anslutning till den virtuella datorn.

### <a name="verify-graphics-drivers-are-installed"></a>Kontrol lera att grafik driv rutinerna är installerade

Kör följande kommando för att kontrol lera att grafik driv rutinerna har installerats. 

```bash
nvidia-smi
```

Du bör se följande utdata.

![NVIDIA driv rutins utdata](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Installera Docker CE och NVIDIA-docker2 på den virtuella datorn

Kör följande kommandon en i taget för att installera Docker CE och NVIDIA-docker2 på den virtuella datorn.

Installera Docker CE på värddatorn.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Installera *NVIDIA-Docker-2-* programpaketet.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Nu när du har konfigurerat och konfigurerat den virtuella datorn följer du stegen nedan för att konfigurera Azure IoT Edge. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Konfigurera Azure IoT Edge på den virtuella datorn

Om du vill distribuera behållaren för rums analys på den virtuella datorn skapar du en instans av en [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) -tjänst som använder standard pris nivån (S1) eller kostnads fri (F0).

Använd Azure CLI för att skapa en instans av Azure IoT Hub. Ersätt parametrarna där det är lämpligt. Du kan också skapa Azure-IoT Hub på [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Se [region support](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) för tillgängliga regioner.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Du måste installera [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) version 1.0.9. Följ de här stegen för att ladda ned rätt version:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopiera den genererade listan.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installera den offentliga nyckeln för Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Uppdatera paket listorna på enheten.

```bash
sudo apt-get update
```

Installera 1.0.9-versionen:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registrera sedan den virtuella datorn som en IoT Edge enhet i IoT Hub-instansen med hjälp av en [anslutnings sträng](../../iot-edge/how-to-register-device.md).

Du måste ansluta IoT Edge-enheten till Azure-IoT Hub. Du måste kopiera anslutnings strängen från den IoT Edge enhet som du skapade tidigare. Du kan också köra kommandot nedan i Azure CLI.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Öppna den virtuella datorn  `/etc/iotedge/config.yaml` för redigering. Ersätt `ADD DEVICE CONNECTION STRING HERE` med anslutnings strängen. Spara och stäng filen. Kör det här kommandot för att starta om IoT Edge tjänsten på den virtuella datorn.

```bash
sudo systemctl restart iotedge
```

Distribuera behållaren för rums analys som en IoT-modul på den virtuella datorn, antingen från [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) eller [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows). Om du använder portalen ställer du in bild-URI: en till platsen för Azure Container Registry. 

Använd stegen nedan för att distribuera behållaren med hjälp av Azure CLI.

---

### <a name="iot-deployment-manifest"></a>Distributions manifest för IoT

För att effektivisera behållar distributionen på flera värddatorer kan du skapa en distributions manifest fil för att ange alternativ för att skapa behållare och miljövariabler. Du hittar ett exempel på ett distributions manifest [för Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [andra Station ära datorer](https://go.microsoft.com/fwlink/?linkid=2152270)och [virtuell Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) på GitHub.

I följande tabell visas de olika miljövariabler som används av IoT Edge-modulen. Du kan också ställa in dem i distributions manifestet som länkas ovan med hjälp av `env` attributet i `spatialanalysis` :

| Inställningsnamn | Värde | Beskrivning|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Statusinformation Utförlig | Loggnings nivå, Välj ett av de två värdena|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Ändra inte|
| ARCHON_PERF_MARKER| falskt| Ange värdet sant för prestanda loggning, annars ska detta vara falskt| 
| ARCHON_NODES_LOG_LEVEL | Statusinformation Utförlig | Loggnings nivå, Välj ett av de två värdena|
| OMP_WAIT_POLICY | PASSIVA | Ändra inte|
| QT_X11_NO_MITSHM | 1 | Ändra inte|
| APIKEY | din API-nyckel| Samla in det här värdet från Azure Portal från din Visuellt innehåll-resurs. Du hittar den i avsnittet **nyckel och slut punkt** för din resurs. |
| FAKTURERING | Slut punkts-URI| Samla in det här värdet från Azure Portal från din Visuellt innehåll-resurs. Du hittar den i avsnittet **nyckel och slut punkt** för din resurs.|
| VILLKOREN | godkänt | Värdet måste anges till *acceptera* för att behållaren ska kunna köras |
| Hur | : 1 | Värdet måste vara detsamma som utdata från `echo $DISPLAY` värddatorn. Azure Stack gräns enheter har ingen visning. Den här inställningen gäller inte|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Lägg till den här miljövariabeln om din GPU **inte** är T4 eller NVIDIA 2080 ti|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Lägg till den här miljövariabeln om din GPU **inte** är T4 eller NVIDIA 2080 ti|
| KEY_ENV | Krypterings nyckel för ASE | Lägg till den här miljövariabeln om Video_URL är en fördunklade-sträng |
| IV_ENV | Initierings vektor | Lägg till den här miljövariabeln om Video_URL är en fördunklade-sträng|

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

När du har uppdaterat distributions manifestet för [Azure Stack Edge-enheter](https://go.microsoft.com/fwlink/?linkid=2142179), [en stationär dator eller en](https://go.microsoft.com/fwlink/?linkid=2152270) [virtuell Azure-dator med en GPU](https://go.microsoft.com/fwlink/?linkid=2152189) med dina egna inställningar och val av åtgärder kan du använda följande [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) -kommando för att distribuera behållaren på värddatorn som en IoT Edge modul.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parameter  |Beskrivning  |
|---------|---------|
| `--hub-name` | Ditt Azure IoT Hub namn. |
| `--content` | Namnet på distributions filen. |
| `--target-condition` | IoT Edge enhets namn för värddatorn. |
| `-–subscription` | Prenumerations-ID eller namn. |

Det här kommandot startar distributionen. Gå till sidan för Azure IoT Hub-instansen i Azure Portal för att se distributions statusen. Statusen kan visas som *417 – enhetens distributions konfiguration har inte angetts* förrän enheten har hämtat behållar avbildningarna och börjar köras.

## <a name="validate-that-the-deployment-is-successful"></a>Verifiera att distributionen har slutförts

Det finns flera sätt att verifiera att behållaren körs. Leta upp *körnings statusen* i **inställningarna för IoT Edge modulen** för modulen för Spatial analys i Azure IoT Hub-instansen på Azure Portal. Kontrol lera att det **önskade värdet** och det **rapporterade värdet** för *körnings statusen* *körs*.

![Exempel på distributions verifiering](./media/spatial-analysis/deployment-verification.png)

När distributionen är klar och behållaren körs börjar **värddatorn** att skicka händelser till Azure-IoT Hub. Om du har använt `.debug` -versionen av åtgärderna visas ett visualisera fönster för varje kamera som du har konfigurerat i distributions manifestet. Nu kan du definiera de rader och zoner som du vill övervaka i distributions manifestet och följa anvisningarna för att distribuera igen. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Konfigurera de åtgärder som utförs av rums analys

Du måste använda åtgärder för [spatial analys](spatial-analysis-operations.md) för att konfigurera behållaren för att använda anslutna kameror, konfigurera åtgärder med mera. För varje kamera enhet som du konfigurerar genererar åtgärder för rums analys en utdataström av JSON-meddelanden som skickas till din instans av Azure-IoT Hub.

## <a name="use-the-output-generated-by-the-container"></a>Använd de utdata som genereras av behållaren

Om du vill börja använda de utdata som genereras av behållaren kan du läsa följande artiklar:

*    Använd Azure Event Hub SDK för ditt valda programmeringsspråk för att ansluta till Azure IoT Hub-slutpunkten och ta emot händelserna. Mer information finns i [läsa meddelanden från enhet till moln från den inbyggda slut punkten](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
*    Konfigurera meddelanderoutning på Azure-IoT Hub för att skicka händelserna till andra slut punkter eller spara händelserna till Azure Blob Storage osv. Mer information finns i [IoT Hub](../../iot-hub/iot-hub-devguide-messages-d2c.md) meddelanderoutning. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Köra rums analys med en inspelad videofil

Du kan använda rums analys med både inspelad eller live video. Om du vill använda rums analys för inspelade videor kan du prova att spela in en videofil och spara den som en MP4-fil. Skapa ett Blob Storage-konto i Azure eller Använd ett befintligt. Uppdatera sedan följande Blob Storage-inställningar i Azure Portal:
    1. Ändra **säker överföring som krävs** för **inaktive rad**
    2. Ändra **Tillåt att BLOB offentlig åtkomst** har **Aktiver ATS**

Navigera till **container** -avsnittet och skapa en ny behållare eller Använd en befintlig. Ladda sedan upp video filen till behållaren. Expandera fil inställningarna för den överförda filen och välj **generera SAS**. Se till att du ställer in **utgångs datumet** tillräckligt länge för att kunna ta del av test perioden. Ange **tillåtna protokoll** till *http* (*https* stöds inte).

Klicka på **skapa SAS-token och URL** och kopiera BLOB SAS-URL: en. Ersätt den `https` med `http` och testa webb adressen i en webbläsare som stöder videouppspelning.

Ersätt `VIDEO_URL` i distributions manifestet för din [Azure Stack Edge-enhet](https://go.microsoft.com/fwlink/?linkid=2142179), [stationär dator](https://go.microsoft.com/fwlink/?linkid=2152270)eller virtuell [Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) med den URL som du har skapat för alla grafer. Ange `VIDEO_IS_LIVE` till `false` och distribuera om behållaren för rums analys med det uppdaterade manifestet. Se exemplet nedan.

Modulen för spatial analys kommer att börja använda video filen och kommer att spelas upp kontinuerligt automatiskt.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Felsökning

Om det uppstår problem när du startar eller kör behållaren, se [telemetri och fel sökning](spatial-analysis-logging.md) för steg för vanliga problem. Den här artikeln innehåller också information om hur du skapar och samlar in loggar och samlar in systemets hälsa.

## <a name="billing"></a>Fakturering

Behållaren för rums analys skickar fakturerings information till Azure med hjälp av en Visuellt innehåll resurs på ditt Azure-konto. Användning av spatial analys i offentlig för hands version är för närvarande kostnads fri. 

Azure Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till avläsnings-/fakturerings slut punkten. Du måste göra det möjligt för behållarna att kommunicera fakturerings information med fakturerings slut punkten hela tiden. Cognitive Services behållare skickar inte kunddata, till exempel videon eller bilden som analyseras, till Microsoft.


## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra behållaren för rums analys. Sammanfattningsvis:

* Rums analys är en Linux-behållare för Docker.
* Behållar avbildningar hämtas från Microsoft Container Registry.
* Behållar avbildningar körs som IoT-moduler i Azure IoT Edge.
* Så här konfigurerar du behållaren och distribuerar den på en värddator.

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webb program inventering](spatial-analysis-web-app.md)
* [Konfigurera åtgärder för rums analys](spatial-analysis-operations.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för kamera placering](spatial-analysis-camera-placement.md)
* [Guide för zon-och linje placering](spatial-analysis-zone-line-placement.md)
