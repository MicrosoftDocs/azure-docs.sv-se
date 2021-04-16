---
title: Etablera en enhet med en virtuell TPM på en virtuell Linux-dator – Azure IoT Edge
description: Använd en simulerad TPM på en virtuell Linux-dator för att testa Azure Device Provisioning-tjänsten för Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481998"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Skapa och etablera en IoT Edge med en TPM i Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Den här artikeln visar hur du testar automatisk etablering på en Linux-IoT Edge-enhet med hjälp av en Trusted Platform Module (TPM). Du kan automatiskt etablera Azure IoT Edge enheter med [Device Provisioning Service](../iot-dps/index.yml). Om du inte är bekant med processen för automatisk etablering kan du granska [etableringsöversikten](../iot-dps/about-iot-dps.md#provisioning-process) innan du fortsätter.

Uppgifterna är följande:

1. Skapa en virtuell Linux-dator (VM) i Hyper-V med en simulerad Trusted Platform Module (TPM) för maskinvarusäkerhet.
1. Skapa en instans av IoT Hub Device Provisioning Service (DPS).
1. Skapa en enskild registrering för enheten.
1. Installera IoT Edge och anslut enheten till IoT Hub.

> [!TIP]
> Den här artikeln beskriver hur du testar DPS-etablering med hjälp av en TPM-simulator, men mycket av det gäller för fysisk TPM-maskinvara, till exempel [Infineon OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), en Azure Certified for IoT-enhet.
>
> Om du använder en fysisk enhet kan du gå vidare till avsnittet [Hämta etableringsinformation från en fysisk](#retrieve-provisioning-information-from-a-physical-device) enhet i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator med [Hyper-V aktiverat.](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Den här artikeln använder Windows 10 kör en virtuell Ubuntu Server-dator.
* En aktiv IoT Hub.

> [!NOTE]
> TPM 2.0 krävs när du använder TPM-attestation med DPS och kan bara användas för att skapa enskilda registreringar, inte gruppregistreringar.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Skapa en virtuell Linux-dator med en virtuell TPM

I det här avsnittet skapar du en ny virtuell Linux-dator på Hyper-V. Du konfigurerar den här virtuella datorn med en simulerad TPM för att testa hur automatisk etablering fungerar med IoT Edge.

### <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

Med en virtuell växel kan den virtuella datorn ansluta till ett fysiskt nätverk.

1. Öppna Hyper-V Manager på din Windows-dator.

2. I menyn **Åtgärder** väljer du **Hanteraren för virtuella växlar.**

3. Välj en **extern** virtuell växel och välj sedan **Skapa virtuell växel.**

4. Ge den nya virtuella växeln ett namn, till exempel **EdgeSwitch**. Kontrollera att anslutningstypen är inställd på **Externt nätverk och** välj ok. 

5. Ett popup-fönster varnar dig om att nätverksanslutningen kan avbrytas. Fortsätt genom att välja **Ja**.

Om det uppstår fel när du skapar den nya virtuella växeln ska du kontrollera att inga andra växlar använder Ethernet-adaptern och att inga andra växlar använder samma namn.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Ladda ned en diskavbildningsfil som ska användas för den virtuella datorn och spara den lokalt. Till exempel [Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/). Information om operativsystem som stöds för IoT Edge finns i Azure IoT Edge [som stöds.](support.md)

2. I Hyper-V Manager igen väljer du **Åtgärd**  >  **Ny**  >  **virtuell dator** på **menyn** Åtgärder.

3. Slutför guiden **Ny virtuell dator** med följande specifika konfigurationer:

   1. **Ange Generation:** Välj **Generation 2.** Virtuella datorer i generation 2 har kapslad virtualisering aktiverat, vilket krävs för att köra IoT Edge på en virtuell dator.
   2. **Konfigurera nätverk:** Ange värdet för **Anslutning till** den virtuella växel som du skapade i föregående avsnitt.
   3. **Installationsalternativ:** Välj **Installera ett operativsystem från en startbar avbildningsfil** och bläddra till diskavbildningsfilen som du sparade lokalt.

4. Välj **Slutför** i guiden för att skapa den virtuella datorn.

Det kan ta några minuter att skapa den nya virtuella datorn.

### <a name="enable-virtual-tpm"></a>Aktivera virtuell TPM

När den virtuella datorn har skapats öppnar du inställningarna för att aktivera den virtuella betrodda plattformsmodulen (TPM) som gör att du kan etablera enheten automatiskt.

1. I Hyper-V Manager högerklickar du på den virtuella datorn och väljer **Inställningar.**

2. Gå till **Security** (Säkerhet).

3. Avmarkera Aktivera **säker start.**

4. Markera **Aktivera Trusted Platform Module**.

5. Klicka på **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starta den virtuella datorn och samla in TPM-data

På den virtuella datorn skapar du ett verktyg som du kan använda för att hämta enhetens **registrerings-ID och** **bekräftelsenyckel.**

1. I Hyper-V Manager startar du den virtuella datorn och ansluter till den.

1. Slutför installationen genom att följa anvisningarna på den virtuella datorn och starta om datorn.

1. Logga in på den virtuella datorn och följ sedan stegen i Konfigurera en [Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och skapa Sdk för Azure IoT-enheter för C.

   >[!TIP]
   >I den här artikeln ska du kopiera och klistra in på den virtuella datorn, vilket inte är enkelt via Anslutningsapplikationen för Hyper-V Manager. Du kanske vill ansluta till den virtuella datorn via Hyper-V Manager en gång för att hämta dess IP-adress. Kör först `sudo apt install net-tools` och sedan `hostname -I` . Sedan kan du använda IP-adressen för att ansluta via SSH: `ssh <username>@<ipaddress>` .

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar information om enhetsetablering från TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Utdatafönstret visar enhetens **registrerings-ID och** **bekräftelsenyckeln**. Kopiera dessa värden för senare användning när du skapar en enskild registrering för din enhet.

När du har ditt registrerings-ID och din bekräftelsenyckel fortsätter du till [avsnittet Konfigurera IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Hämta etableringsinformation från en fysisk enhet

Om du använder en fysisk IoT Edge i stället för en virtuell dator skapar du ett verktyg som du kan använda för att hämta enhetens etableringsinformation.

1. Följ stegen i Konfigurera [en Linux-utvecklingsmiljö för att](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) installera och skapa Sdk för Azure IoT-enheter för C.

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar information om enhetsetablering från TPM-enheten.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopiera värdena för **Registrerings-ID och** **Bekräftelsenyckel.** Du använder dessa värden för att skapa en enskild registrering för din enhet i DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

När enhetsetableringstjänsten körs kopierar du värdet för **ID-omfång** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge körning.

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Hämta etableringsinformationen från den virtuella datorn och använd den för att skapa en enskild registrering i Enhetsetableringstjänsten.

När du skapar en registrering i DPS har du möjlighet att deklarera en första **enhetstvillingstillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner.](how-to-deploy-at-scale.md)

> [!TIP]
> I Azure CLI kan du [](/cli/azure/iot/dps/enrollment) skapa en registrering och använda flaggan **edge-enabled** för att ange att en enhet är en IoT Edge enhet.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

2. Under **Inställningar** väljer du **Hantera registreringar.**

3. Välj **Lägg till enskild** registrering och slutför sedan följande steg för att konfigurera registreringen:  

   1. För **Mekanism** väljer du **TPM**.

   2. Ange **bekräftelsenyckeln och** **registrerings-ID:t** som du kopierade från den virtuella datorn.

      > [!TIP]
      > Om du använder en fysisk TPM-enhet måste du fastställa bekräftelsenyckeln **,** som är unik för varje TPM-chip och hämtas från den TPM-kretstillverkare som är associerad med den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelsenyckeln.

   3. Ange ett ID för din enhet om du vill. Om du inte anger ett enhets-ID används registrerings-ID:t.

   4. Välj **Sant** för att deklarera att den här virtuella datorn är en IoT Edge enhet.

   5. Välj den länkade IoT Hub som du vill ansluta enheten till eller välj **Länka till ny IoT Hub**. Du kan välja flera hubbar så tilldelas enheten till en av dem enligt den valda tilldelningsprincipen.

   6. Lägg till ett taggvärde i **inledande enhetstvillingtillstånd** om du vill. Du kan använda taggar för att rikta in dig på grupper av enheter för moduldistribution. Mer information finns i Distribuera [IoT Edge moduler i stor skala.](how-to-deploy-at-scale.md)

   7. Välj **Spara**.

Nu när det finns en registrering för den här enheten kan IoT Edge-körningen automatiskt etablera enheten under installationen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i containrar och gör att du kan distribuera ytterligare containrar till enheten så att du kan köra kod vid gränsen. Installera IoT Edge på den virtuella datorn.

Följ stegen i Installera [Azure IoT Edge körtid och](how-to-install-iot-edge.md)gå sedan tillbaka till den här artikeln för att etablera enheten.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etableringsinformation

När körningen har installerats på enheten konfigurerar du den med den information den använder för att ansluta till Device Provisioning-tjänsten och IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Känna till ditt **DPS-ID-omfång** **och enhetsregistrerings-ID** som samlades in i föregående avsnitt.

1. Öppna konfigurationsfilen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Leta reda på avsnittet etableringskonfigurationer i filen. Avkommentera raderna för TPM-etablering och se till att andra etableringsrader har kommenterats bort.

   Raden `provisioning:` ska inte ha något föregående blanksteg och kapslade objekt ska dras in med två blanksteg.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Uppdatera värdena för `scope_id` och med `registration_id` din DPS- och enhetsinformation.

1. Du kan också använda `always_reprovision_on_startup` raderna eller för att konfigurera `dynamic_reprovisioning` enhetens återetableringsbeteende. Om en enhet är inställd på att etableras igen vid start försöker den alltid etablera med DPS först och sedan gå tillbaka till etableringssäkerhetskopiorna om det misslyckas. Om en enhet är inställd på att dynamiskt återetablera sig IoT Edge startar om och återetablera om en ometableringshändelse upptäcks. Mer information finns i [IoT Hub om etablering av enheter.](../iot-dps/concepts-device-reprovision.md)

1. Spara och stäng filen.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ta del av ditt **DPS-ID-omfång** **och enhetsregistrerings-ID** som samlades in i föregående avsnitt.

1. Öppna konfigurationsfilen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Leta reda på avsnittet etableringskonfigurationer i filen. Avkommentera raderna för TPM-etablering och se till att andra etableringsrader har kommenterats bort.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Uppdatera värdena för `id_scope` och med `registration_id` din DPS- och enhetsinformation.

1. Du kan också hitta avsnittet auto-reprovisioning mode (Läge för automatisk ometablering) i filen. Använd `auto_reprovisioning_mode` parametern för att konfigurera enhetens återetableringsbeteende till antingen `Dynamic` `AlwaysOnStartup` , eller `OnErrorOnly` . Mer information finns i [IoT Hub om etablering av enheter.](../iot-dps/concepts-device-reprovision.md)

1. Spara och stäng filen.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Ge IoT Edge åtkomst till TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Den IoT Edge måste ha åtkomst till TPM för att automatiskt etablera din enhet.

Du kan ge TPM åtkomst till IoT Edge genom att åsidosätta systeminställningarna så att `iotedge` tjänsten har rotprivilegier. Om du inte vill höja behörigheterna för tjänsten kan du också använda följande steg för att manuellt ge TPM-åtkomst.

1. Hitta filsökvägen till TPM-maskinvarumodulen på enheten och spara den som en lokal variabel.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Skapa en ny regel som ger IoT Edge åtkomst till tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öppna regelfilen.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopiera följande åtkomstinformation till regelfilen.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Spara och avsluta filen.

6. Utlös udev-systemet för att utvärdera den nya regeln.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kontrollera att regeln har tillämpats.

   ```bash
   ls -l /dev/tpm0
   ```

   Lyckade utdata visas på följande sätt:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Om du inte ser att rätt behörigheter har tillämpats kan du prova att starta om datorn för att uppdatera udev.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Den IoT Edge körningen förlitar sig på en TPM-tjänst som koordinatorns åtkomst till en enhets TPM. Den här tjänsten måste ha åtkomst till TPM för att automatiskt etablera din enhet.

Du kan ge åtkomst till TPM genom att åsidosätta systeminställningarna så att `aziottpm` tjänsten har rotprivilegier. Om du inte vill höja behörigheten för tjänsten kan du också använda följande steg för att ge TPM-åtkomst manuellt.

1. Leta upp filsökvägen till TPM-maskinvarumodulen på enheten och spara den som en lokal variabel.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Skapa en ny regel som ger IoT Edge åtkomst till tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öppna regelfilen.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopiera följande åtkomstinformation till regelfilen.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Spara och avsluta filen.

6. Utlös udev-systemet för att utvärdera den nya regeln.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kontrollera att regeln har tillämpats.

   ```bash
   ls -l /dev/tpm0
   ```

   Lyckade utdata visas på följande sätt:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Om du inte ser att rätt behörigheter har tillämpats kan du prova att starta om datorn för att uppdatera udev.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Starta IoT Edge och verifiera att installationen har lyckats

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Starta om IoT Edge-körningen så att den hämtar alla konfigurationsändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

Kontrollera att IoT Edge körs.

   ```bash
   sudo systemctl status iotedge
   ```

Granska daemonloggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Om du ser etableringsfel kan det vara så att konfigurationsändringarna inte har verkställts ännu. Försök att starta om IoT Edge daemon igen.

   ```bash
   sudo systemctl daemon-reload
   ```

Eller prova att starta om den virtuella datorn för att se om ändringarna börjar gälla på nytt.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Tillämpa de konfigurationsändringar som du har gjort på enheten.

   ```bash
   sudo iotedge config apply
   ```

Kontrollera att IoT Edge körs.

   ```bash
   sudo iotedge system status
   ```

Granska daemonloggar.

   ```cmd/sh
   sudo iotedge system logs
   ```

Om du ser etableringsfel kan det vara så att konfigurationsändringarna inte har verkställts ännu. Prova att starta om IoT Edge daemon.

   ```bash
   sudo systemctl daemon-reload
   ```

Eller prova att starta om den virtuella datorn för att se om ändringarna börjar gälla på nytt.
:::moniker-end
<!-- end 1.2 -->

Om körningen har startats kan du gå till IoT Hub och se att den nya enheten har etablerats automatiskt. Nu är enheten redo att köra IoT Edge moduler.

Lista moduler som körs.

```cmd/sh
iotedge list
```

Du kan kontrollera att den enskilda registrering som du skapade i Enhetsetableringstjänsten har använts. Gå till instansen av enhetsetableringstjänsten i Azure Portal. Öppna registreringsinformationen för den enskilda registrering som du skapade. Observera att statusen för registreringen har **tilldelats** och att enhets-ID:t visas.

## <a name="next-steps"></a>Nästa steg

Med DPS-registreringsprocessen kan du ange enhets-ID och enhetstvillingtaggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur [du distribuerar och övervakar IoT Edge moduler i stor skala med hjälp Azure Portal](how-to-deploy-at-scale.md) eller med Hjälp av Azure [CLI.](how-to-deploy-cli-at-scale.md)
