---
title: 'Självstudie: Konfigurera en Azure IoT Edge enhets maskin inlärning på IoT Edge'
description: I den här självstudien konfigurerar du en virtuell Azure-dator som kör Linux som en Azure IoT Edge enhet som fungerar som en transparent Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463093"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Självstudie: Konfigurera en Azure IoT Edge enhet

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

I den här artikeln konfigurerar vi en virtuell Azure-dator som kör Linux som en Azure IoT Edge-enhet som fungerar som en transparent Gateway. Med en transparent gateway-konfiguration kan enheter ansluta till Azure IoT Hub via gatewayen utan att veta att gatewayen finns. På samma gång är en användare som interagerar med enheterna i IoT Hub inte medveten om den mellanliggande gateway-enheten. Slutligen lägger vi till Edge Analytics i vårt system genom att lägga till IoT Edge moduler till den transparenta gatewayen.

>[!NOTE]
>Begreppen i den här självstudien gäller för alla versioner av IoT Edge, men exempel enheten som du skapar för att testa scenariot kör IoT Edge version 1,1.

Stegen i den här artikeln utförs vanligt vis av en molnbaserad utvecklare.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Skapa certifikat för att tillåta att din gateway-enhet ansluter säkert till dina underordnade enheter.
> * Skapa en IoT Edge enhet.
> * Skapa en virtuell Azure-dator för att simulera din IoT Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du läsa den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="create-certificates"></a>Skapa certifikat

För att en enhet ska fungera som en gateway måste den ansluta till underordnade enheter på ett säkert sätt. Med IoT Edge kan du använda en PKI (Public Key Infrastructure) för att konfigurera säkra anslutningar mellan enheter. I det här fallet är det möjligt att en underordnad IoT-enhet ansluter till en IoT Edge-enhet som fungerar som en transparent Gateway. För att upprätthålla rimlig säkerhet bör den underordnade enheten bekräfta identiteten för den IoT Edge enheten. Mer information om hur IoT Edge enheter använder certifikat finns i avsnittet [information om Azure IoT Edge certifikat användning](iot-edge-certs.md).

I det här avsnittet skapar vi de självsignerade certifikaten med hjälp av en Docker-avbildning som vi sedan skapar och kör. Vi valde att använda en Docker-avbildning för att slutföra det här steget eftersom det minskar antalet steg som krävs för att skapa certifikaten på Windows Development-datorn. Information om vad vi automatiserade med Docker-avbildningen finns i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md).

1. Logga in på din virtuella utvecklings dator.
1. Skapa en ny mapp med sökvägen och namnet **c:\edgeCertificates**.

1. Om den inte redan körs startar du **Docker för Windows** från Start-menyn i Windows.

1. Öppna Visual Studio Code.

1. Välj   >  **Öppna mapp** för filen och välj sedan **C: \\ Source \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. I **Explorer** -fönstret högerklickar du på **Dockerfile** och väljer **Bygg avbildning**.

1. I dialog rutan godkänner du standardvärdet för avbildningens namn och tagg: **createcertificates: senaste**.

    ![Skärm bild som visar hur du skapar certifikat i Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Vänta tills skapandet har slutförts.

    > [!NOTE]
    > Du kan se en varning om en offentlig nyckel som saknas. Det är säkert att ignorera den här varningen. På samma sätt ser du en säkerhets varning som rekommenderar att du kontrollerar eller återställer behörigheter för din avbildning, vilket är säkert att ignorera för avbildningen.

1. I terminalfönstret i Visual Studio Code kör du behållaren createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker kommer att uppmanas att få åtkomst till enheten **c: \\** . Välj **dela den**.

1. Ange dina autentiseringsuppgifter när du uppmanas till det.

1. När behållaren har körts klart kontrollerar du att följande filer finns i **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ certifikat \\ Azure-IoT-test-Only. root. ca. cert. pem
    * c: \\ edgeCertificates- \\ certifikat \\ New-Edge-Device-full-Chain. cert. pem
    * c: \\ edgeCertificates- \\ certifikat \\ New-Edge-Device. cert. pem
    * c: \\ edgeCertificates- \\ certifikat \\ New-Edge-Device. cert. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Ladda upp certifikat till Azure Key Vault

För att lagra våra certifikat säkert och göra dem tillgängliga från flera enheter laddar vi upp certifikaten till Azure Key Vault. Som du kan se i föregående lista finns det två typer av certifikatfiler: PFX och PEM. Vi behandlar PFX-filen som Key Vault certifikat som ska överföras till Key Vault. PEM-filerna är oformaterad text och vi behandlar dem som Key Vault hemligheter. Vi använder Key Vault-instansen som är kopplad till arbets ytan Azure Machine Learning som vi skapade genom att köra [antecknings böckerna för Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. Från [Azure Portal](https://portal.azure.com)går du till arbets ytan Azure Machine Learning.

1. På sidan Översikt i arbets ytan Machine Learning söker du efter namnet på **Key Vault**.

    ![Skärm bild som visar kopiering av nyckel valvets namn.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Överför certifikaten till Key Vault på din utvecklings dator. Ersätt **\<subscriptionId\>** och **\<keyvaultname\>** med din resursinformation.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Logga in på Azure om du uppmanas till det.

1. Skriptet körs i några minuter med utdata som visar de nya Key Vault posterna.

    ![Skärm bild som visar Key Vault utdata från skriptet.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Skapa en IoT Edge-enhet

För att ansluta en Azure IoT Edge-enhet till en IoT-hubb skapar vi först en identitet för enheten i hubben. Vi tar anslutnings strängen från enhetens identitet i molnet och använder den för att konfigurera körningen på vår IoT Edge-enhet. När en konfigurerad enhet ansluter till hubben kan vi distribuera moduler och skicka meddelanden. Vi kan också ändra konfigurationen för den fysiska IoT Edge enheten genom att ändra motsvarande enhets identitet i IoT Hub.

I den här självstudien skapar vi den nya enhets identiteten med hjälp av Visual Studio Code. Du kan också utföra dessa steg med hjälp av Azure Portal eller Azure CLI.

1. Öppna Visual Studio Code på utvecklings datorn.

1. Expandera **Azure IoT Hub** -ramen från Visual Studio Code **Explorer** -vyn.

1. Välj ellipsen och välj **skapa IoT Edge enhet**.

1. Ge enheten ett namn. För enkelhetens skull använder vi namnet **aaTurbofanEdgeDevice** så att det sorteras överst på listade enheter.

1. Den nya enheten visas i listan över enheter.

    ![Skärm bild som visar en vy av enheten i Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Distribuera en virtuell Azure-dator

Vi använder [Azure IoT Edge på Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) -avbildning från Azure Marketplace för att skapa vår IoT Edge-enhet för den här självstudien. Azure IoT Edge avbildningen på Ubuntu installerar den senaste IoT Edge-körningen och dess beroenden vid start. Vi distribuerar den virtuella datorn med hjälp av:

- Ett PowerShell-skript, `Create-EdgeVM.ps1` .
- En Azure Resource Manager mall, `IoTEdgeVMTemplate.json` .
- Ett gränssnitts skript, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Aktivera program distribution

Om du vill använda avbildningen från Azure Marketplace i en skriptad distribution måste du aktivera program distribution för avbildningen.

1. Logga in på Azure-portalen.

1. Välj **Alla tjänster**.

1. Skriv och välj **Marketplace** i Sök fältet.

1. I Sök fältet i Marketplace anger och väljer du **Azure IoT Edge på Ubuntu**.

1. Välj länken **Kom igång** för att distribuera program mässigt.

1. Välj knappen **Aktivera** och välj sedan **Spara**.

    ![Skärm bild som visar aktivering av program mässig distribution för en virtuell dator.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Du ser ett meddelande om att det är klart.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Kör sedan skriptet för att skapa den virtuella datorn för din IoT Edge-enhet.

1. Öppna ett PowerShell-fönster och gå till **EdgeVM** -katalogen.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Kör skriptet för att skapa den virtuella datorn.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Ange värden för varje parameter när du uppmanas till det. För prenumeration, resurs grupp och plats rekommenderar vi att du använder samma värden som du har för alla resurser i den här självstudien.

    * **Azure-prenumerations-ID**: hittades i Azure Portal.
    * **Resurs grupp namn**: minnes värt namn för att gruppera resurserna i den här självstudien.
    * **Plats**: Azure-plats där den virtuella datorn kommer att skapas. Till exempel westus2 eller europanorra. Mer information finns på alla [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: namnet på det administratörs konto som du använder för att logga in på den virtuella datorn.
    * **AdminPassword**: lösen ordet som ska anges för administratörs användar namnet på den virtuella datorn.

1. För skriptet för att konfigurera den virtuella datorn loggar du in på Azure med de autentiseringsuppgifter som är associerade med den Azure-prenumeration som du använder.

1. Skriptet bekräftar informationen för att skapa den virtuella datorn. Välj **y** eller **RETUR** för att fortsätta.

1. Skriptet körs i flera minuter eftersom det utför följande steg:

    * Skapar resurs gruppen om den inte redan finns
    * Skapar den virtuella datorn
    * Lägger till NSG undantag för den virtuella datorn för port 22 (SSH), 5671 (AMQP), 5672 (AMPQ) och 443 (TLS)
    * Installerar [Azure CLI](/cli/azure/install-azure-cli-apt)

1. Skriptet matar ut SSH-anslutningssträngen för att ansluta till den virtuella datorn. Kopiera anslutnings strängen för nästa steg.

    ![Skärm bild som visar hur du kopierar SSH-anslutningssträngen för en virtuell dator.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

De följande flera avsnitten konfigurerar den virtuella Azure-dator vi skapade. Det första steget är att ansluta till den virtuella datorn.

1. Öppna en kommando tolk och klistra in SSH-anslutningssträngen som du kopierade från skriptets utdata. Ange din egen information för användar namn, suffix och region enligt de värden som du angav i PowerShell-skriptet i föregående avsnitt.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. När du uppmanas att verifiera värdens äkthet anger du **Ja** och väljer **RETUR**.

1. Ange ditt lösen ord när du uppmanas till det.

1. Ubuntu visar ett välkomst meddelande, och sedan bör du se en prompt som `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Ladda ned Key Vault certifikat

Tidigare i den här artikeln laddade vi upp certifikat till Key Vault för att göra dem tillgängliga för vår IoT Edge-enhet och vår löv enhet. Löv enheten är en underordnad enhet som använder IoT Edge enhet som en gateway för att kommunicera med IoT Hub.

Vi kommer att hantera löv enheten senare i självstudien. I det här avsnittet laddar du ned certifikaten till den IoT Edge enheten.

1. Från SSH-sessionen på den virtuella Linux-datorn loggar du in på Azure med Azure CLI.

    ```azurecli
    az login
    ```

1. Du uppmanas att öppna en webbläsare på en [Microsoft-enhets inloggnings](https://microsoft.com/devicelogin) sida och ange en unik kod. Du kan utföra de här stegen på den lokala datorn. Stäng webbläsarfönstret när du är klar med autentiseringen.

1. När du har autentiserat loggas den virtuella Linux-datorn in och du får en lista över dina Azure-prenumerationer.

1. Ange den Azure-prenumeration som du vill använda för Azure CLI-kommandon.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Skapa en katalog på den virtuella datorn för certifikaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Hämta de certifikat som du har lagrat i nyckel valvet: New-Edge-Device-full-Chain. cert. PEM, New-Edge-Device. Key. pem och Azure-IoT-test-Only. root. ca. cert. pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Uppdatera IoT Edge enhets konfiguration

Den IoT Edge körningen använder filen/etc/iotedge/config.yaml för att spara konfigurationen. Vi måste uppdatera tre delar av informationen i den här filen:

* **Enhets anslutnings sträng**: anslutnings strängen från den här enhetens identitet i IoT Hub
* **Certifikat**: certifikat som ska användas för anslutningar som görs med underordnade enheter
* **Värdnamn**: det fullständigt kvalificerade domän namnet (FQDN) för den virtuella datorns IoT Edge enhet

Den Azure IoT Edge på Ubuntu-avbildningen som vi använde för att skapa den virtuella IoT Edge-datorn levereras med ett gränssnitts skript som uppdaterar filen config. yaml med anslutnings strängen.

1. Högerklicka på den IoT Edge enheten i Visual Studio Code och välj sedan **Kopiera enhets anslutnings sträng**.

    ![Skärm bild som visar hur du kopierar anslutnings strängen från Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Kör kommandot i SSH-sessionen för att uppdatera filen config. yaml med enhets anslutnings strängen.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Sedan uppdaterar vi certifikat och värdnamn genom att direkt redigera filen config. yaml.

1. Öppna filen config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Uppdatera avsnittet certifikat i filen config. yaml genom att ta bort det inledande **#** och ange sökvägen så att filen ser ut som i följande exempel:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Se till att det inte finns några föregående blank steg på raden **certifikat:** och att vart och ett av de kapslade certifikaten är indragna med två blank steg.

    Om du högerklickar på nano klistras innehållet i Urklipp till den aktuella markör positionen. Om du vill ersätta strängen använder du piltangenterna för att gå till den sträng som du vill ersätta, ta bort strängen och högerklicka sedan för att klistra in från bufferten.

1. Gå till den virtuella datorn i Azure Portal. Kopiera DNS-namnet (FQDN för datorn) från **översikts** avsnittet.

1. Klistra in FQDN i hostname-avsnittet i filen config. yml. Kontrol lera att namnet är gemener.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Spara och Stäng filen genom att välja **CTRL + X**, **Y** och **RETUR**.

1. Starta om IoT Edge daemon.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Kontrol lera status för IoT Edge daemon. Efter kommandot anger du **: q** för att avsluta.

    ```bash
    systemctl status iotedge
    ```

1. Om du ser fel (färgade text med " \[ fel \] ") i status granskar du daemon-loggar för detaljerad fel information.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du har slutfört den sista självstudien innan du rensar några resurser.

## <a name="next-steps"></a>Nästa steg

Vi har precis slutfört konfigurationen av en virtuell Azure-dator som en IoT Edge transparent Gateway. Vi startade genom att generera test certifikat som vi laddade upp till Key Vault. Därefter använde vi en skript-och Resource Manager-mall för att distribuera den virtuella datorn med Ubuntu-servern 16,04 LTS + Azure IoT Edge runtime-avbildningen från Azure Marketplace. Med den virtuella datorn igång ansluter vi via SSH. Sedan loggade vi in till Azure och hämtade certifikat från Key Vault. Vi har gjort flera uppdateringar av konfigurationen av IoT Edge runtime genom att uppdatera filen config. yaml.

Fortsätt till nästa artikel för att bygga IoT Edge moduler.

> [!div class="nextstepaction"]
> [Skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md)
