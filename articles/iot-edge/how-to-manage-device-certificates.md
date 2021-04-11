---
title: Hantera enhets certifikat – Azure IoT Edge | Microsoft Docs
description: Skapa test certifikat, installera och hantera dem på en Azure IoT Edge enhet för att förbereda för produktions distribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3b6bd19d47658e5ad079f0b731cbafc866bb333
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045781"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Hantera certifikat på en IoT Edge enhet

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Alla IoT Edge-enheter använder certifikat för att skapa säkra anslutningar mellan körningen och de moduler som körs på enheten. IoT Edge enheter som fungerar som gateways använder samma certifikat för att ansluta till deras underordnade enheter också.

## <a name="install-production-certificates"></a>Installera produktionscertifikat

När du först installerar IoT Edge och etablerar enheten, konfigureras enheten med tillfälliga certifikat så att du kan testa tjänsten.
Dessa tillfälliga certifikat upphör att gälla om 90 dagar eller kan återställas genom att starta om datorn.
När du har flyttat till ett produktions scenario, eller om du vill skapa en gateway-enhet, måste du ange dina egna certifikat.
Den här artikeln visar stegen för att installera certifikat på dina IoT Edge enheter.

Läs mer om de olika typerna av certifikat och deras roller i [förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

>[!NOTE]
>Termen "rot certifikat utfärdare" som används i den här artikeln refererar till det offentliga utfärdade certifikat kedjan för din IoT-lösning. Du behöver inte använda certifikat roten för en insyndikerad certifikat utfärdare eller roten för organisationens certifikat utfärdare. I många fall är det faktiskt ett offentligt certifikat för certifikat utfärdare.

### <a name="prerequisites"></a>Förutsättningar

* En IoT Edge enhet.

  Om du inte har konfigurerat en IoT Edge enhet kan du skapa en på en virtuell Azure-dator. Följ stegen i en av snabb starts artiklarna för att [skapa en virtuell Linux-enhet](quickstart-linux.md) eller [skapa en virtuell Windows-enhet](quickstart.md).

* Ha ett certifikat från en rot certifikat utfärdare (CA), antingen självsignerat eller köpt från en betrodd kommersiell certifikat utfärdare som Baltimore, VeriSign, DigiCert eller GlobalSign.

  Om du inte har en rot certifikat utfärdare än, men vill testa IoT Edge funktioner som kräver produktions certifikat (till exempel Gateway-scenarier) kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Skapa produktions certifikat

Du bör använda din egen certifikat utfärdare för att skapa följande filer:

* Rotcertifikatutfärdare
* Enhetens CA-certifikat
* Privat nyckel för enhets certifikat utfärdare

I den här artikeln refererar vi till som *rot certifikat utfärdare* är inte den översta certifikat utfärdaren för en organisation. Det är den översta certifikat utfärdaren för IoT Edge scenariot, som IoT Edge Hub-modulen, användarattribut och eventuella underordnade enheter använder för att upprätta förtroende mellan varandra.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> För närvarande förhindrar en begränsning i libiothsm användningen av certifikat som upphör att gälla den 1 januari 2038.

:::moniker-end

Om du vill se ett exempel på dessa certifikat granskar du skripten som skapar demo certifikat i [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installera certifikat på enheten

Installera certifikat kedjan på den IoT Edge enheten och konfigurera IoT Edge runtime så att den refererar till de nya certifikaten.

Kopiera de tre certifikat-och nyckelfilerna till din IoT Edge-enhet. Du kan använda en tjänst som [Azure Key Vault](../key-vault/index.yml) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler. Om du har genererat certifikaten på själva enheten för IoT Edge kan du hoppa över det här steget och använda sökvägen till arbets katalogen.

Om du använder IoT Edge för Linux i Windows måste du använda SSH-nyckeln som finns i Azure IoT Edge- `id_rsa` filen för att autentisera fil överföringar mellan värd-OS och den virtuella Linux-datorn. Du kan göra en autentiserad SCP med följande kommando:

   ```powershell-interactive
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

   >[!NOTE]
   >Den virtuella Linux-datorns IP-adress kan frågas via `Get-EflowVmAddr` kommandot.

Om du använde exempel skripten för att [skapa demo certifikat](how-to-create-test-certificates.md), kopierar du följande filer till din IoT-Edge-enhet:

* Enhetens CA-certifikat: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privat nyckel för enhets certifikat utfärdare: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Rot certifikat utfärdare: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öppna konfigurations filen för IoT Edge Security daemon.

   * Linux och IoT Edge för Linux i Windows: `/etc/iotedge/config.yaml`

   * Windows med Windows-behållare: `C:\ProgramData\iotedge\config.yaml`

1. Ange **certifikat** egenskaperna i config. yaml till fil-URI-sökvägen till certifikatet och nyckelfilen på den IoT Edge enheten. Ta bort tecknen innan du tar bort dem från `#` certifikat egenskaperna för att ta bort kommentarer till de fyra raderna. Se till att det inte finns några föregående blank steg i raden **certifikat:** rad och att kapslade objekt är indragna med två blank steg. Exempel:

   * Linux och IoT Edge för Linux i Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

   * Windows med Windows-behållare:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

1. På Linux-enheter ser du till att användar **iotedge** har Läs behörighet för den katalog som innehåller certifikaten.

1. Om du har använt andra certifikat för IoT Edge på enheten tidigare tar du bort filerna i följande två kataloger innan du startar eller startar om IoT Edge:

   * Linux och IoT Edge för Linux i Windows: `/var/lib/iotedge/hsm/certs` och `/var/lib/iotedge/hsm/cert_keys`

   * Windows med Windows-behållare: `C:\ProgramData\iotedge\hsm\certs` och `C:\ProgramData\iotedge\hsm\cert_keys`

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Öppna konfigurations filen för IoT Edge Security daemon: `/etc/aziot/config.toml`

1. Hitta `trust_bundle_cert` parametern i början av filen. Ta bort kommentaren till den här raden och ange filens URI till rot certifikat utfärdarens certifikat på enheten.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Hitta `[edge_ca]` avsnittet i filen config. toml. Ta bort kommentarer till raderna i det här avsnittet och ange fil-URI-sökvägar för certifikatet och nyckelfilen på den IoT Edge enheten.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Se till att användar **iotedge** har Läs behörighet för den katalog som innehåller certifikaten.

1. Om du har använt andra certifikat för IoT Edge på enheten tidigare tar du bort filerna i följande två kataloger innan du startar eller startar om IoT Edge:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Anpassa certifikatets livstid

IoT Edge automatiskt genererar certifikat på enheten i flera fall, inklusive:

* Om du inte anger dina egna produktions certifikat när du installerar och etablerar IoT Edge, genererar IoT Edge Security Manager automatiskt ett **ca-certifikat för enhet**. Detta självsignerade certifikat är endast avsett för utvecklings-och testnings scenarier, inte produktion. Det här certifikatet upphör att gälla efter 90 dagar.
* IoT Edge Security Manager genererar också ett **ca-certifikat för arbets belastning** signerat av ENHETens CA-certifikat

Mer information om funktionen för de olika certifikaten på en IoT Edge-enhet finns i [förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

För dessa två automatiskt genererade certifikat kan du välja att ställa in **auto_generated_ca_lifetime_days** -flaggan i konfigurations filen för att konfigurera antalet dagar för certifikatens livs längd.

>[!NOTE]
>Det finns ett tredje automatiskt genererat certifikat som IoT Edge Security Manager skapar, **IoT Edge Hub-servercertifikat**. Det här certifikatet har alltid en livs längd på 90 dagar, men förnyas automatiskt innan det upphör att gälla. **Auto_generated_ca_lifetime_days** -värdet påverkar inte det här certifikatet.

När det angivna antalet dagar har löpt ut måste IoT Edge startas om för att återskapa enhetens CA-certifikat. Enhetens CA-certifikat förnyas inte automatiskt.

1. Om du vill konfigurera certifikatets giltighets tid till något annat än standardvärdet 90 dagar lägger du till värdet i dagar i avsnittet **certifikat** i konfigurations filen.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > För närvarande förhindrar en begränsning i libiothsm användningen av certifikat som upphör att gälla den 1 januari 2038.

1. Ta bort innehållet i `hsm` mappen för att ta bort eventuella tidigare genererade certifikat.

   * Linux och IoT Edge för Linux i Windows: `/var/lib/iotedge/hsm/certs` och `/var/lib/iotedge/hsm/cert_keys`

   * Windows med Windows-behållare: `C:\ProgramData\iotedge\hsm\certs` och `C:\ProgramData\iotedge\hsm\cert_keys`

1. Starta om tjänsten IoT Edge.

   * Linux och IoT Edge för Linux i Windows:

   ```bash
   sudo systemctl restart iotedge
   ```

   * Windows med Windows-behållare:

   ```powershell
   Restart-Service iotedge
   ```

1. Bekräfta livs längds inställningen.

   * Linux och IoT Edge för Linux i Windows:

   ```bash
   sudo iotedge check --verbose
   ```

   * Windows med Windows-behållare:

   ```powershell
   iotedge check --verbose
   ```

   Kontrol lera utdata från **produktions beredskap: certifikat** kontroll, som visar antalet dagar tills de automatiskt genererade ENHETens CA-certifikat upphör att gälla.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Nästa steg

Att installera certifikat på en IoT Edge enhet är ett nödvändigt steg innan du distribuerar din lösning i produktionen. Lär dig mer om hur du [förbereder för att distribuera IoT Edge-lösningen i produktionen](production-checklist.md).
