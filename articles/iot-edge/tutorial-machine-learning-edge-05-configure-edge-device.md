---
title: 'Självstudie: Konfigurera en Azure IoT Edge enhet – Maskininlärning på IoT Edge'
description: I den här självstudien konfigurerar du en virtuell Azure-dator som kör Linux Azure IoT Edge en enhet som fungerar som en transparent gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376893"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Självstudie: Konfigurera en Azure IoT Edge enhet

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

I den här artikeln konfigurerar vi en virtuell Azure-dator som kör Linux för att Azure IoT Edge en enhet som fungerar som en transparent gateway. Med en transparent gatewaykonfiguration kan enheter ansluta till Azure IoT Hub via gatewayen utan att känna till att gatewayen finns. Samtidigt är en användare som interagerar med enheterna i IoT Hub om den mellanliggande gatewayenheten. Slutligen lägger vi till gränsanalys i vårt system genom att lägga till IoT Edge moduler i den transparenta gatewayen.

>[!NOTE]
>Begreppen i den här självstudien gäller för alla versioner av IoT Edge, men den exempelenhet som du skapar för att testa scenariot kör IoT Edge version 1.1.

Stegen i den här artikeln utförs vanligtvis av en molnutvecklare.

I det här avsnittet av självstudien lär du dig att:

> [!div class="checklist"]
>
> * Skapa certifikat så att din gatewayenhet kan ansluta säkert till nedströmsenheterna.
> * Skapa en IoT Edge enhet.
> * Skapa en virtuell Azure-dator för att simulera IoT Edge enhet.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln ingår i en serie för en självstudie om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit fram till den här artikeln direkt kan du läsa [den första](tutorial-machine-learning-edge-01-intro.md) artikeln i serien.

## <a name="create-certificates"></a>Skapa certifikat

För att en enhet ska fungera som en gateway måste den ansluta säkert till nedströmsenheter. Med IoT Edge kan du använda en PKI (Public Key Infrastructure) för att konfigurera säkra anslutningar mellan enheter. I det här fallet tillåter vi att en underordnad IoT-enhet ansluter till en IoT Edge som fungerar som en transparent gateway. För att upprätthålla rimlig säkerhet bör nedströmsenheten bekräfta identiteten för den IoT Edge enheten. Mer information om hur IoT Edge enheter använder certifikat finns i [Azure IoT Edge information om certifikatanvändning.](iot-edge-certs.md)

I det här avsnittet skapar vi de själv signerade certifikaten med hjälp av en Docker-avbildning som vi sedan skapar och kör. Vi valde att använda en Docker-avbildning för att slutföra det här steget eftersom det minskar antalet steg som krävs för att skapa certifikaten på Windows-utvecklingsdatorn. Information om vad vi har automatiserat med Docker-avbildningen finns i [Skapa democertifikat för att testa IoT Edge enhetsfunktioner.](how-to-create-test-certificates.md)

1. Logga in på den virtuella utvecklingsdatorn.
1. Skapa en ny mapp med sökvägen och namnet **c:\edgeCertificates**.

1. Om den inte redan körs startar du **Docker för Windows** från Windows-Start-menyn.

1. Öppna Visual Studio Code.

1. Välj **Arkiv**  >  **Öppna mapp** och välj sedan **C: source \\ \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. Högerklicka på **dockerfile** i Explorer-fönstret och välj **Skapa avbildning.** 

1. I dialogrutan godkänner du standardvärdet för avbildningens namn och tagg: **createcertificates: latest**.

    ![Skärmbild som visar hur du skapar certifikat Visual Studio Kod.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Vänta tills bygget har slutförts.

    > [!NOTE]
    > Du kan se en varning om att en offentlig nyckel saknas. Det är säkert att ignorera den här varningen. På samma sätt visas en säkerhetsvarning som rekommenderar att du kontrollerar eller återställer behörigheter för avbildningen, vilket är säkert att ignorera för den här avbildningen.

1. I terminalfönstret Visual Studio code kör du containern createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker frågar efter åtkomst till **\\ c:-enheten.** Välj **Dela den.**

1. Ange dina autentiseringsuppgifter när du tillfrågas.

1. När containern har körts söker du efter följande filer i **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ certs \\ azure-iot-test-only.root.ca.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device-full-chain.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pfx
    * c: \\ edgeCertificates \\ private \\ new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Ladda upp certifikat till Azure Key Vault

För att lagra våra certifikat på ett säkert sätt och göra dem tillgängliga från flera enheter laddar vi upp certifikaten till Azure Key Vault. Som du ser i föregående lista har vi två typer av certifikatfiler: PFX och PEM. Vi behandlar PFX-filen som Key Vault certifikat som ska överföras till Key Vault. PEM-filerna är oformaterad text och vi behandlar dem som Key Vault hemligheter. Vi använder den instans Key Vault som är associerad med den Azure Machine Learning som vi skapade genom att köra [Jupyter Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. Från [Azure Portal](https://portal.azure.com)går du till Azure Machine Learning arbetsyta.

1. På översiktssidan för Machine Learning söker du efter namnet på **Key Vault**.

    ![Skärmbild som visar kopiering av nyckelvalvsnamnet.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. På utvecklingsdatorn laddar du upp certifikaten till Key Vault. Ersätt **\<subscriptionId\>** och med din **\<keyvaultname\>** resursinformation.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Logga in på Azure om du uppmanas till det.

1. Skriptet körs i några minuter med utdata som visar de nya Key Vault posterna.

    ![Skärmbild som visar Key Vault skriptutdata.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Skapa en IoT Edge-enhet

För att ansluta Azure IoT Edge en enhet till en IoT-hubb skapar vi först en identitet för enheten i hubben. Vi tar anslutningssträngen från enhetsidentiteten i molnet och använder den för att konfigurera körningen på vår IoT Edge enhet. När en konfigurerad enhet ansluter till hubben kan vi distribuera moduler och skicka meddelanden. Vi kan också ändra konfigurationen för den fysiska IoT Edge genom att ändra dess motsvarande enhetsidentitet i IoT Hub.

I den här självstudien skapar vi den nya enhetsidentiteten med hjälp av Visual Studio Code. Du kan också utföra de här stegen med hjälp Azure Portal eller Azure CLI.

1. Öppna Visual Studio Code på utvecklingsdatorn.

1. Expandera **Azure IoT Hub** ram från Visual Studio Code **Explorer-vyn.**

1. Välj ellipsen och välj **Skapa IoT Edge Enhet.**

1. Ge enheten ett namn. För enkelhetens skull använder vi namnet **aaTurbofanEdgeDevice** så att den sorteras överst på enheterna i listan.

1. Den nya enheten visas i listan över enheter.

    ![Skärmbild som visar en vy över enheten i Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Distribuera en virtuell Azure-dator

Vi använder den Azure IoT Edge [Ubuntu-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) från Azure Marketplace för att skapa vår IoT Edge för den här självstudien. I Azure IoT Edge på Ubuntu-avbildningen installeras den IoT Edge körningen och dess beroenden vid start. Vi distribuerar den virtuella datorn med hjälp av:

- Ett PowerShell-skript, `Create-EdgeVM.ps1` .
- En Azure Resource Manager mall, `IoTEdgeVMTemplate.json` .
- Ett gränssnittsskript, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Aktivera programmatisk distribution

Om du vill använda avbildningen Azure Marketplace i en skriptad distribution måste vi aktivera programmatisk distribution för avbildningen.

1. Logga in på Azure-portalen.

1. Välj **Alla tjänster**.

1. I sökfältet anger och väljer du **Marketplace.**

1. I sökfältet på Marketplace anger och väljer du **Azure IoT Edge på Ubuntu**.

1. Välj **hyperlänken Kom igång** för att distribuera programmatiskt.

1. Välj knappen **Aktivera** och välj sedan **Spara.**

    ![Skärmbild som visar aktivering av programmatisk distribution för en virtuell dator.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Du ser ett meddelande om att det lyckades.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Kör sedan skriptet för att skapa den virtuella datorn för din IoT Edge enhet.

1. Öppna ett PowerShell-fönster och gå till **katalogen EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Kör skriptet för att skapa den virtuella datorn.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. När du uppmanas till det anger du värden för varje parameter. För prenumeration, resursgrupp och plats rekommenderar vi att du använder samma värden som du har för alla resurser i den här självstudien.

    * **Azure-prenumerations-ID:** Finns i Azure Portal.
    * **Resursgruppsnamn:** Namn som är lätt att komma ihåg för gruppering av resurser för den här självstudien.
    * **Plats:** Azure-plats där den virtuella datorn ska skapas. Till exempel westus2 eller northeurope. Mer information finns i alla [Azure-platser.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername:** Namnet på det administratörskonto som du ska använda för att logga in på den virtuella datorn.
    * **AdminPassword:** Lösenordet som ska anges för administratörens användarnamn på den virtuella datorn.

1. För att skriptet ska konfigurera den virtuella datorn loggar du in på Azure med de autentiseringsuppgifter som är associerade med den Azure-prenumeration som du använder.

1. Skriptet bekräftar informationen för att skapa den virtuella datorn. Välj **y** eller **Retur för** att fortsätta.

1. Skriptet körs i flera minuter eftersom det utför följande steg:

    * Skapar resursgruppen om den inte redan finns
    * Skapar den virtuella datorn
    * Lägger till NSG-undantag för den virtuella datorn för portarna 22 (SSH), 5671 (AMQP), 5672 (AMPQ) och 443 (TLS)
    * Installerar [Azure CLI](/cli/azure/install-azure-cli-apt)

1. Skriptet matar ut SSH-anslutningssträngen för anslutning till den virtuella datorn. Kopiera anslutningssträngen för nästa steg.

    ![Skärmbild som visar kopiering av SSH-anslutningssträngen för en virtuell dator.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

I följande avsnitt konfigurerar vi den virtuella Azure-dator som vi skapade. Det första steget är att ansluta till den virtuella datorn.

1. Öppna en kommandotolk och klistra in den SSH-anslutningssträng som du kopierade från skriptets utdata. Ange din egen information för användarnamn, suffix och region enligt de värden som du angav för PowerShell-skriptet i föregående avsnitt.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. När du uppmanas att verifiera värdens äkthet anger du **ja och** väljer **Retur.**

1. När du uppmanas till det anger du ditt lösenord.

1. Ubuntu visar ett välkomstmeddelande och sedan bör du se en uppmaning som `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Ladda ned Key Vault certifikat

Tidigare i den här artikeln laddade vi upp certifikat till Key Vault för att göra dem tillgängliga för vår IoT Edge och vår lövenhet. Lövenheten är en underordnad enhet som använder IoT Edge som gateway för att kommunicera med IoT Hub.

Vi hanterar lövenheten senare i självstudien. I det här avsnittet laddar du ned certifikaten till den IoT Edge enheten.

1. Logga in på Azure med Azure CLI från SSH-sessionen på den virtuella Linux-datorn.

    ```azurecli
    az login
    ```

1. Du uppmanas att öppna en webbläsare på en inloggningssida [för Microsoft-enheter](https://microsoft.com/devicelogin) och ange en unik kod. Du kan utföra de här stegen på den lokala datorn. Stäng webbläsarfönstret när du är klar med autentiseringen.

1. När du har autentiserat loggar den virtuella Linux-datorn in och visar en lista över dina Azure-prenumerationer.

1. Ange den Azure-prenumeration som du vill använda för Azure CLI-kommandon.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Skapa en katalog på den virtuella datorn för certifikaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Ladda ned de certifikat som du har lagrat i nyckelvalvet: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem och azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Uppdatera IoT Edge enhetskonfiguration

Den IoT Edge körningen använder filen /etc/iotedge/config.yaml för att bevara konfigurationen. Vi behöver uppdatera tre uppgifter i den här filen:

* **Enhetsanslutningssträng:** Anslutningssträngen från den här enhetens identitet i IoT Hub
* **Certifikat:** De certifikat som ska användas för anslutningar som görs med nedströmsenheter
* **Värdnamn:** Det fullständiga domännamnet (FQDN) för den virtuella IoT Edge enheten

Den Azure IoT Edge på Ubuntu-avbildningen som vi använde för att skapa den virtuella IoT Edge-datorn levereras med ett gränssnittsskript som uppdaterar filen config.yaml med anslutningssträngen.

1. I Visual Studio Code högerklickar du på IoT Edge och väljer sedan **Kopiera enhetsanslutningssträng.**

    ![Skärmbild som visar hur du kopierar anslutningssträngen från Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. I SSH-sessionen kör du kommandot för att uppdatera filen config.yaml med enhetens anslutningssträng.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Nu ska vi uppdatera certifikaten och värdnamnet genom att redigera filen config.yaml direkt.

1. Öppna filen config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Uppdatera certifikatavsnittet i filen config.yaml genom att ta bort den inledande och ange sökvägen så att filen **#** ser ut som i följande exempel:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Kontrollera att **certifikaten: raden** inte har något föregående blanksteg och att vart och ett av de kapslade certifikaten har indrag med två blanksteg.

    Om du högerklickar i nano klistras innehållet i Urklipp in på den aktuella markörpositionen. Om du vill ersätta strängen använder du tangentbordspilarna för att gå till den sträng som du vill ersätta, ta bort strängen och högerklicka för att klistra in från bufferten.

1. I Azure Portal du till den virtuella datorn. Kopiera DNS-namnet (FQDN för datorn) från **avsnittet** Översikt.

1. Klistra in FQDN i avsnittet hostname i filen config.yml. Kontrollera att namnet bara är gemener.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Spara och stäng filen genom att **välja Ctrl +X**, **Y** och **Ange**.

1. Starta om IoT Edge daemon.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Kontrollera status för IoT Edge daemon. Efter kommandot anger du **:q för** att avsluta.

    ```bash
    systemctl status iotedge
    ```

1. Om du ser fel (färgad text med prefixet " ERROR ") i \[ \] statusen undersöker du daemonloggar för detaljerad felinformation.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående artikel. Vänta med att rensa alla resurser tills du har slutfört den sista självstudien.

## <a name="next-steps"></a>Nästa steg

Vi har precis slutfört konfigurationen av en virtuell Azure-dator som IoT Edge transparent gateway. Vi började med att generera testcertifikat som vi laddade upp till Key Vault. Därefter använde vi ett skript och en Resource Manager-mall för att distribuera den virtuella datorn med Ubuntu Server 16.04 LTS + Azure IoT Edge-körningsavbildningen från Azure Marketplace. När den virtuella datorn är igång har vi anslutit via SSH. Sedan loggade vi in på Azure och hämtade certifikat från Key Vault. Vi har gjort flera uppdateringar av konfigurationen av IoT Edge runtime genom att uppdatera filen config.yaml.

Fortsätt till nästa artikel för att skapa IoT Edge moduler.

> [!div class="nextstepaction"]
> [Skapa och distribuera anpassade IoT Edge moduler](tutorial-machine-learning-edge-06-custom-modules.md)
