---
title: Etablera enhet med en virtuell TPM på en virtuell Linux-dator – Azure IoT Edge
description: Använd en simulerad TPM på en virtuell Linux-dator för att testa Azure Device Provisioning-tjänsten för Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9356da9516ea17243be8e0e5519ec4b19e06db8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200863"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Skapa och etablera en IoT Edge enhet med en TPM på Linux

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Den här artikeln visar hur du testar automatisk etablering på en Linux IoT Edge-enhet med hjälp av en Trusted Platform Module (TPM). Du kan etablera Azure IoT Edge-enheter automatiskt med [enhets etablerings tjänsten](../iot-dps/index.yml). Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](../iot-dps/about-iot-dps.md#provisioning-process) översikten innan du fortsätter.

:::moniker range=">=iotedge-2020-11"
> [!NOTE]
> För närvarande stöds inte automatisk etablering med TPM-autentisering i IoT Edge version 1,2.
:::moniker-end

Uppgifterna är följande:

1. Skapa en virtuell Linux-dator (VM) i Hyper-V med en simulerad Trusted Platform Module (TPM) för maskin varu säkerhet.
1. Skapa en instans av IoT Hub Device Provisioning Service (DPS).
1. Skapa en enskild registrering för enheten.
1. Installera IoT Edge Runtime och Anslut enheten till IoT Hub.

> [!TIP]
> Den här artikeln beskriver hur du testar DPS-etablering med hjälp av en TPM-simulator, men det är mycket som gäller för fysisk TPM-maskinvara som [INFINEON OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), en Azure-certifierad för IoT-enheter.
>
> Om du använder en fysisk enhet kan du gå vidare till avsnittet [Hämta etablerings information från en fysisk enhet](#retrieve-provisioning-information-from-a-physical-device) i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklings dator med [Hyper-V aktiverat](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Den här artikeln använder Windows 10 som kör en virtuell Ubuntu-Server.
* En aktiv IoT Hub.

> [!NOTE]
> TPM 2,0 krävs när du använder TPM-attestering med DPS och kan endast användas för att skapa enskilda, inte grupper, registreringar.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Skapa en virtuell Linux-dator med en virtuell TPM

I det här avsnittet skapar du en ny virtuell Linux-dator på Hyper-V. Du konfigurerar den här virtuella datorn med en simulerad TPM för att testa hur automatisk etablering fungerar med IoT Edge.

### <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

En virtuell växel gör att den virtuella datorn kan ansluta till ett fysiskt nätverk.

1. Öppna Hyper-V Manager på Windows-datorn.

2. I menyn **åtgärder** väljer du **hanteraren för virtuella växlar**.

3. Välj en **extern** virtuell växel och välj sedan **Skapa virtuell växel**.

4. Ge din nya virtuella växel ett namn, till exempel **EdgeSwitch**. Kontrol lera att anslutnings typen är inställd på **externt nätverk** och välj sedan **OK**.

5. Ett popup-fönster varnar dig om att nätverks anslutningen kan avbrytas. Fortsätt genom att välja **Ja**.

Om du ser fel när du skapar den nya virtuella växeln ser du till att inga andra växlar använder Ethernet-adaptern och att inga andra växlar använder samma namn.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Ladda ned en disk avbildnings fil som ska användas för den virtuella datorn och spara den lokalt. Till exempel [Ubuntu server 18,04](http://releases.ubuntu.com/18.04/). Information om operativ system som stöds för IoT Edge enheter finns i [Azure IoT Edge system som stöds](support.md).

2. I Hyper-V Manager igen väljer du **åtgärd**  >  **ny**  >  **virtuell dator** på menyn **åtgärder** .

3. Slutför **guiden Ny virtuell dator** med följande konfigurationer:

   1. **Ange generation**: Välj **generation 2**. Virtuella datorer i generation 2 har kapslad virtualisering aktiverat, vilket krävs för att köra IoT Edge på en virtuell dator.
   2. **Konfigurera nätverk**: Ange värdet för **anslutning** till den virtuella växel som du skapade i föregående avsnitt.
   3. **Installations alternativ**: Välj **Installera ett operativ system från en startbar avbildnings fil** och bläddra till disk avbildnings filen som du sparade lokalt.

4. Klicka på **Slutför** i guiden för att skapa den virtuella datorn.

Det kan ta några minuter att skapa den nya virtuella datorn.

### <a name="enable-virtual-tpm"></a>Aktivera virtuell TPM

När den virtuella datorn har skapats öppnar du dess inställningar för att aktivera den virtuella Trusted Platform Module (TPM) som gör att du kan etablera enheten automatiskt.

1. I Hyper-V Manager högerklickar du på den virtuella datorn och väljer **Inställningar**.

2. Gå till **Security** (Säkerhet).

3. Avmarkera **Aktivera säker start**.

4. Markera **aktivera Trusted Platform Module**.

5. Klicka på **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starta den virtuella datorn och samla in TPM-data

Bygg ett verktyg som du kan använda för att hämta enhetens **registrerings-ID** och **bekräftelse nyckel** på den virtuella datorn.

1. Starta den virtuella datorn i Hyper-V Manager och Anslut till den.

1. Slutför installations processen genom att följa anvisningarna i den virtuella datorn och starta om datorn.

1. Logga in på den virtuella datorn och följ stegen i [Konfigurera en Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och bygga Azure IoT-enhetens SDK för C.

   >[!TIP]
   >I den här artikeln ska du kopiera och klistra in på den virtuella datorn, vilket inte är enkelt via anslutnings programmet för Hyper-V Manager. Du kanske vill ansluta till den virtuella datorn via Hyper-V Manager en gång för att hämta dess IP-adress. Kör först `sudo apt install net-tools` och sedan `hostname -I` . Sedan kan du använda IP-adressen för att ansluta via SSH: `ssh <username>@<ipaddress>` .

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar din enhets etablerings information från TPM: en.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. I fönstret Utdata visas enhetens **registrerings-ID** och **bekräftelse nyckeln**. Kopiera de här värdena för användning senare när du skapar en enskild registrering för enheten.

När du har registrerings-ID och bekräftelse nyckel fortsätter du till avsnittet [konfigurera IoT Hub Device Provisioning service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Hämta etablerings information från en fysisk enhet

Om du använder en fysisk IoT Edge enhet i stället för en virtuell dator kan du bygga ett verktyg som du kan använda för att hämta enhetens etablerings information.

1. Följ stegen i [Konfigurera en Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och bygga Azure IoT-enhetens SDK för C.

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar din enhets etablerings information från TPM-enheten.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopiera värdena för **registrerings-ID** och **bekräftelse nyckel**. Du använder dessa värden för att skapa en enskild registrering för enheten i DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa anvisningarna i [konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har installerat enhets etablerings tjänsten kopierar du värdet för **ID-omfång** från översikts sidan. Du använder det här värdet när du konfigurerar IoT Edge Runtime.

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Hämta etablerings informationen från den virtuella datorn och Använd den för att skapa en enskild registrering i Device Provisioning-tjänsten.

När du skapar en registrering i DPS har du möjlighet att deklarera en **första enhets dubbla tillstånd**. I enheten är det enkelt att ange taggar för att gruppera enheter efter eventuella mått som du behöver i din lösning, t. ex. region, miljö, plats eller enhets typ. De här taggarna används för att skapa [automatiska distributioner](how-to-deploy-at-scale.md).

> [!TIP]
> I Azure CLI kan du skapa en [registrering](/cli/azure/ext/azure-iot/iot/dps/enrollment) och använda den **Edge-aktiverade** flaggan för att ange att en enhet är en IoT Edge enhet.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

2. Under **Inställningar** väljer du **Hantera registreringar**.

3. Välj **Lägg till enskild registrering** och slutför sedan följande steg för att konfigurera registreringen:  

   1. För **mekanism** väljer du **TPM**.

   2. Ange **bekräftelse nyckeln** och **registrerings-ID: t** som du kopierade från den virtuella datorn.

      > [!TIP]
      > Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelse nyckeln**, som är unik för varje TPM-chip och hämtas från TPM-kretsen som är kopplad till den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelse nyckeln.

   3. Ange ett ID för din enhet om du vill. Om du inte anger något enhets-ID används registrerings-ID: t.

   4. Välj **Sant** om du vill deklarera att den virtuella datorn är en IoT Edge enhet.

   5. Välj den länkade IoT Hub som du vill ansluta enheten till, eller Välj **Länka till ny IoT Hub**. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda tilldelnings principen.

   6. Lägg till ett tagg-värde till den **första enheten** i ett läge om du vill. Du kan använda taggar för att ange mål grupper för enheter för modul distribution. Mer information finns i [distribuera IoT Edge moduler i skala](how-to-deploy-at-scale.md).

   7. Välj **Spara**.

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Komponenterna körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod i kanten. Installera IoT Edge runtime på den virtuella datorn.

Följ stegen i [installera Azure IoT Edge runtime](how-to-install-iot-edge.md)och gå sedan tillbaka till den här artikeln för att etablera enheten.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etablerings information

När körningen har installerats på enheten konfigurerar du enheten med den information som används för att ansluta till enhets etablerings tjänsten och IoT Hub.

1. Känna till ditt DPS **-ID-omfång** och enhets **registrerings-ID** som samlades in i föregående avsnitt.

1. Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Hitta konfigurations avsnittet för etablering i filen. Ta bort kommentarer till linjerna för TPM-etablering och se till att alla andra etablerings rader är kommenterade.

   `provisioning:`Raden ska inte ha något föregående blank steg, och kapslade objekt bör dras av två blank steg.

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

   Du kan också använda `always_reprovision_on_startup` raderna eller om `dynamic_reprovisioning` du vill konfigurera enhetens etablerings beteende. Om en enhet har ställts in för att etablera vid start försöker den alltid etableras med DPS först och sedan återgår till etablerings säkerhets kopieringen om det inte går. Om en enhet är inställd på att dynamiskt Ometablera sig själv startas IoT Edge om och reetableras om en reetablerings händelse upptäcks. Mer information finns i [IoT Hub metoder för att etablera enheter](../iot-dps/concepts-device-reprovision.md).

1. Uppdatera värdena för `scope_id` och `registration_id` med din DPS-och enhets information.

## <a name="give-iot-edge-access-to-the-tpm"></a>Ge IoT Edge åtkomst till TPM: en

Den IoT Edge körningen måste ha åtkomst till TPM för att automatiskt etablera enheten.

Du kan ge TPM-åtkomst till den IoT Edge körningen genom att åsidosätta systeminställningarna så att `iotedge` tjänsten har rot privilegier. Om du inte vill höja behörigheten till tjänsten kan du också använda följande steg för att ange TPM-åtkomst manuellt.

1. Hitta fil Sök vägen till modulen TPM-maskinvara på enheten och spara den som en lokal variabel.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Skapa en ny regel som ger IoT Edge runtime-åtkomst till tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öppna regel filen.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopiera följande åtkomst information till regel filen.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Spara och avsluta filen.

6. Utlös udev-systemet för att utvärdera den nya regeln.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kontrol lera att regeln har tillämpats.

   ```bash
   ls -l /dev/tpm0
   ```

   Lyckade utdata visas på följande sätt:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Om du inte ser att rätt behörigheter har tillämpats kan du prova att starta om datorn för att uppdatera udev.

## <a name="restart-the-iot-edge-runtime"></a>Starta om IoT Edge runtime

Starta om IoT Edge runtime så att den hämtar alla konfigurations ändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

Kontrol lera att IoT Edge runtime körs.

   ```bash
   sudo systemctl status iotedge
   ```

Om du ser etablerings fel kan det bero på att konfigurations ändringarna inte har börjat tillämpas ännu. Försök att starta om IoT Edge daemon igen.

   ```bash
   sudo systemctl daemon-reload
   ```

Eller prova att starta om den virtuella datorn för att se om ändringarna börjar gälla på en ny start.

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startats kan du gå till din IoT Hub och se att den nya enheten har automatiskt tillhandahållits. Nu är enheten redo att köra IoT Edge moduler.

Kontrol lera status för IoT Edge daemon.

```cmd/sh
systemctl status iotedge
```

Granska daemon-loggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista med moduler som körs.

```cmd/sh
iotedge list
```

Du kan kontrol lera att den enskilda registrering som du skapade i enhets etablerings tjänsten användes. Navigera till din enhets etablerings tjänst instans i Azure Portal. Öppna registrerings informationen för den enskilda registrering som du har skapat. Observera att statusen för registreringen är **tilldelad** och att enhets-ID visas.

## <a name="next-steps"></a>Nästa steg

Med DPS-registreringen kan du ange enhets-ID och enhets dubbla taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in enskilda enheter eller grupper av enheter med automatisk enhets hantering. Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med hjälp av Azure Portal](how-to-deploy-at-scale.md) eller [med hjälp av Azure CLI](how-to-deploy-cli-at-scale.md).
