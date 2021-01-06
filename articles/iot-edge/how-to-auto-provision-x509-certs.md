---
title: Etablera enheter automatiskt med DPS med X. 509-certifikat – Azure IoT Edge | Microsoft Docs
description: Använd X. 509-certifikat för att testa automatisk enhets etablering för Azure IoT Edge med enhets etablerings tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 385a67e117bf0cf9508b81d014e3accac4725744
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914917"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Skapa och etablera en IoT Edge-enhet med X. 509-certifikat

Med [Azure-IoT Hub Device Provisioning service (DPS)](../iot-dps/index.yml)kan du automatiskt etablera IoT Edge enheter med X. 509-certifikat. Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](../iot-dps/about-iot-dps.md#provisioning-process) översikten innan du fortsätter.

Den här artikeln visar hur du skapar en registrering av enhets etablerings tjänsten med X. 509-certifikat på en IoT Edge enhet med följande steg:

* Generera certifikat och nycklar.
* Skapa antingen en enskild registrering för en enhet eller en grupp registrering för en uppsättning enheter.
* Installera IoT Edge Runtime och registrera enheten med IoT Hub.

Att använda X. 509-certifikat som en mekanism för attestering är ett utmärkt sätt att skala produktion och förenkla enhets etablering. Normalt är X. 509-certifikat ordnade i en certifikat kedja med förtroende. Från och med ett självsignerat eller betrott rot certifikat signerar varje certifikat i kedjan nästa lägre certifikat. Det här mönstret skapar en delegerad kedja av förtroende från rot certifikatet nedåt genom varje mellanliggande certifikat till det slutliga "löv"-certifikatet som är installerat på en enhet.

## <a name="prerequisites"></a>Krav

* En aktiv IoT Hub.
* En fysisk eller virtuell enhet som IoT Edge enheten.
* Den senaste versionen av [git](https://git-scm.com/download/) är installerad.
* En instans av IoT Hub Device Provisioning Service i Azure som är länkad till din IoT Hub.
  * Om du inte har en instans av enhets etablerings tjänsten följer du anvisningarna i [konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * När du har installerat enhets etablerings tjänsten kopierar du värdet för **ID-omfång** från översikts sidan. Du använder det här värdet när du konfigurerar IoT Edge Runtime.

## <a name="generate-device-identity-certificates"></a>Generera enhets identitets certifikat

Enhetens identitets certifikat är ett löv certifikat som ansluter via en certifikat kedja till det översta X. 509 certifikat utfärdarens certifikat (CA). Enhetens identitets certifikat måste ha sitt eget namn (CN) inställt på det enhets-ID som du vill att enheten ska ha i IoT-hubben.

Enhets identitets certifikat används bara för att tillhandahålla IoT Edge enheten och autentisera enheten med Azure IoT Hub. De signerar inte certifikaten, till skillnad från de CA-certifikat som den IoT Edge enheten presenterar för moduler eller löv enheter för verifiering. Mer information finns i avsnittet [Azure IoT Edge information om certifikat användning](iot-edge-certs.md).

När du har skapat enhets identitets certifikatet bör du ha två filer: en. cer-eller. PEM-fil som innehåller den offentliga delen av certifikatet och en CER-eller PEM-fil med certifikatets privata nyckel. Om du planerar att använda grupp registrering i DPS behöver du också den offentliga delen av ett mellanliggande eller rotcertifikatutfärdarcertifikat i samma certifikat kedja.

Du behöver följande filer för att konfigurera automatisk etablering med X. 509:

* Enhetens identitets certifikat och dess privata nyckel certifikat. Enhetens identitets certifikat överförs till DPS om du skapar en enskild registrering. Den privata nyckeln skickas till IoT Edge Runtime.
* Ett fullständigt kedje certifikat, som minst måste ha enhets identitet och mellanliggande certifikat. Det fullständiga kedje certifikatet skickas till IoT Edge Runtime.
* Ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat från certifikat kedjan. Det här certifikatet överförs till DPS om du skapar en grupp registrering.

> [!NOTE]
> För närvarande förhindrar en begränsning i libiothsm användningen av certifikat som upphör att gälla den 1 januari 2038.

### <a name="use-test-certificates"></a>Använd test certifikat

Om du inte har någon certifikat utfärdare som är tillgänglig för att skapa nya identitets certifikat och vill testa det här scenariot innehåller Azure IoT Edge git-lagringsplats skript som du kan använda för att generera test certifikat. Dessa certifikat är endast utformade för utvecklings testning och får inte användas i produktion.

Om du vill skapa test certifikat följer du stegen i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). Slutför de två avsnitten som krävs för att ställa in skript för generering av certifikat och för att skapa ett rot certifikat för certifikat utfärdare. Följ sedan stegen för att skapa ett enhets identitets certifikat. När du är klar bör du ha följande certifikat kedja och nyckel par:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Du behöver båda dessa certifikat på den IoT Edge enheten. Om du kommer att använda enskild registrering i DPS laddar du upp filen. cert. pem. Om du ska använda grupp registrering i DPS behöver du också ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat i samma certifikat kedja för att ladda upp. Om du använder demo certifikat använder du `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certifikatet för grupp registrering.

## <a name="create-a-dps-individual-enrollment"></a>Skapa en DPS-individuell registrering

Använd dina genererade certifikat och nycklar för att skapa en enskild registrering i DPS för en enskild IoT Edge enhet. Enskilda registreringar tar den offentliga delen av enhetens identitets certifikat och matchar certifikatet på enheten.

Om du vill etablera flera IoT Edge enheter följer du stegen i nästa avsnitt och [skapar en DPS-gruppregistrering](#create-a-dps-group-enrollment).

När du skapar en registrering i DPS har du möjlighet att deklarera en **första enhets dubbla tillstånd**. I enheten är det enkelt att ange taggar för att gruppera enheter efter eventuella mått som du behöver i din lösning, t. ex. region, miljö, plats eller enhets typ. De här taggarna används för att skapa [automatiska distributioner](how-to-deploy-at-scale.md).

Mer information om registreringar i Device Provisioning-tjänsten finns i [Hantera enhets registreringar](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > I Azure CLI kan du skapa en [registrering](/cli/azure/ext/azure-iot/iot/dps/enrollment) eller en [registrerings grupp](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) och använda den **Edge-aktiverade** flaggan för att ange att en enhet, eller en grupp av enheter, är en IoT Edge enhet.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

1. Under **Inställningar** väljer du **Hantera registreringar**.

1. Välj **Lägg till enskild registrering** och slutför sedan följande steg för att konfigurera registreringen:  

   * **Mekanism**: Välj **X. 509**.

   * **Primär Certificate. pem-eller. cer-fil**: Ladda upp den offentliga filen från enhetens identitets certifikat. Om du använde skripten för att skapa ett test certifikat väljer du följande fil:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub enhets-ID**: Ange ett ID för din enhet om du vill. Du kan använda enhets-ID: n för att rikta in en enskild enhet för modul distribution. Om du inte anger något enhets-ID används det egna namnet (CN) i X. 509-certifikatet.

   * **IoT Edge enhet**: Välj **Sant** om du vill deklarera att registreringen är för en IoT Edge enhet.

   * **Välj de IoT-hubbar som enheten kan tilldelas**: Välj den länkade IoT-hubben som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda allokeringsregeln.

   * **Första enhets status**: Lägg till ett taggvärde som ska läggas till på enheten, med ett värde om du vill. Du kan använda taggar för att ange mål grupper för enheter för automatisk distribution. Till exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen. Fortsätt till avsnittet [installera IoT Edge runtime](#install-the-iot-edge-runtime) för att konfigurera din IoT Edge-enhet.

## <a name="create-a-dps-group-enrollment"></a>Skapa en DPS-gruppregistrering

Använd dina genererade certifikat och nycklar för att skapa en grupp registrering i DPS för flera IoT Edge enheter. Grupp registreringar använder ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat från certifikat kedjan som används för att generera enskilda enhets identitets certifikat.

Om du vill etablera en enda IoT Edge enhet i stället följer du stegen i föregående avsnitt och [skapar en DPS-individuell registrering](#create-a-dps-individual-enrollment).

När du skapar en registrering i DPS har du möjlighet att deklarera en **första enhets dubbla tillstånd**. I enheten är det enkelt att ange taggar för att gruppera enheter efter eventuella mått som du behöver i din lösning, t. ex. region, miljö, plats eller enhets typ. De här taggarna används för att skapa [automatiska distributioner](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Verifiera ditt rot certifikat

När du skapar en registrerings grupp kan du välja att använda ett verifierat certifikat. Du kan verifiera ett certifikat med DPS genom att bevisa att du har ägande rätt till rot certifikatet. Mer information finns i [så här gör du en certifikats 509 för X. ca-certifikat](../iot-dps/how-to-verify-certificates.md).

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

1. Välj **certifikat** på menyn till vänster.

1. Välj **Lägg** till för att lägga till ett nytt certifikat.

1. Ange ett eget namn för certifikatet och bläddra sedan till CER-eller PEM-filen som representerar den offentliga delen av ditt X. 509-certifikat.

   Om du använder demonstrations certifikaten laddar du upp `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certifikatet.

1. Välj **Spara**.

1. Ditt certifikat ska nu visas på sidan **certifikat** . Välj den för att öppna certifikat informationen.

1. Välj **generera verifierings kod** och kopiera sedan den genererade koden.

1. Oavsett om du har fått ditt eget CA-certifikat eller använder demonstrations certifikaten, kan du använda verifierings verktyget som finns i IoT Edge-lagringsplatsen för att kontrol lera att innehavaren är avsedd. Verifierings verktyget använder ditt CA-certifikat för att signera ett nytt certifikat som har den angivna verifierings koden som ämnes namn.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. På sidan samma certifikat information i Azure Portal laddar du upp det nyligen genererade verifierings certifikatet.

1. Välj **Verifiera**.

### <a name="create-enrollment-group"></a>Skapa registrerings grupp

Mer information om registreringar i Device Provisioning-tjänsten finns i [Hantera enhets registreringar](../iot-dps/how-to-manage-enrollments.md).

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

1. Under **Inställningar** väljer du **Hantera registreringar**.

1. Välj **Lägg till registrerings grupp** och slutför sedan följande steg för att konfigurera registreringen:

   * **Grupp namn**: Ange ett minnes minnes minnes namn för den här grupp registreringen.

   * **Attesterings typ**: Välj **certifikat**.

   * **IoT Edge enhet**: Välj **Sant**. För en grupp registrering måste alla enheter vara IoT Edge enheter eller så kan ingen av dem vara.

   * **Certifikat typ**: Välj **ca-certifikat** om du har ett verifierat CA-certifikat lagrat med DPS eller **mellanliggande certifikat** om du vill ladda upp en ny fil för bara denna registrering.

   * **Primärt certifikat**: om du valde CA-certifikat i det sista avsnittet väljer du ditt certifikat i list rutan. Om du väljer mellanliggande certifikat laddar du upp den offentliga filen från ett CA-certifikat i certifikat kedjan som användes för att generera enhets identitets certifikat.

   * **Välj de IoT-hubbar som enheten kan tilldelas**: Välj den länkade IoT-hubben som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda allokeringsregeln.

   * **Första enhets status**: Lägg till ett taggvärde som ska läggas till på enheten, med ett värde om du vill. Du kan använda taggar för att ange mål grupper för enheter för automatisk distribution. Till exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen. Fortsätt till nästa avsnitt för att konfigurera din IoT Edge-enhet.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Komponenterna körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod i kanten.

Följ stegen i [installera Azure IoT Edge runtime](how-to-install-iot-edge.md)och gå sedan tillbaka till den här artikeln för att etablera enheten.

X. 509-etablering med DPS stöds bara i IoT Edge version 1.0.9 eller senare.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etablerings information

När körningen har installerats på enheten konfigurerar du enheten med den information som används för att ansluta till enhets etablerings tjänsten och IoT Hub.

Ha följande information redo:

* Värdet för DPS **-ID-omfånget** . Du kan hämta det här värdet från sidan Översikt i DPS-instansen i Azure Portal.
* Certifikat kedjas filen för enhets identitet på enheten.
* Enhetens identitets nyckel fil på enheten.
* Ett valfritt registrerings-ID. Om detta inte anges hämtas ID: t från det egna namnet i enhetens identitets certifikat.

### <a name="linux-device"></a>Linux-enhet

1. Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Hitta konfigurations avsnittet för etablering i filen. Ta bort kommentarer till raderna för etablering av DPS symmetrisk nyckel och se till att alla andra etablerings rader är kommenterade.

   `provisioning:`Raden ska inte ha något föregående blank steg, och kapslade objekt bör dras av två blank steg.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Uppdatera värdena för `scope_id` , `identity_cert` och `identity_pk` med din DPS-och enhets information.

   När du lägger till X. 509-certifikatet och nyckelinformation i filen config. yaml ska Sök vägarna anges som fil-URI: er. Till exempel:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Ange en `registration_id` för enheten om du vill, eller lämna den här raden kommenterad om du vill registrera enheten med CN-namnet för identitets certifikatet.

1. Starta om IoT Edge runtime så att den hämtar alla konfigurations ändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="windows-device"></a>Windows-enhet

1. Öppna ett PowerShell-fönster i administratörs läge. Se till att använda en AMD64-session av PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Standardvärdet för manuell etablering med Windows-behållare är att använda- `-DpsX509` flaggan för att använda automatisk etablering med X. 509-certifikatautentisering.

   Ersätt plats hållarnas värden för `{scope_id}` , `{identity cert chain path}` och `{identity key path}` med lämpliga värden från DPS-instansen och fil Sök vägarna på enheten.

   Lägg till `-RegistrationId {registration_id}` om du vill ange enhets-ID: t som något annat än namnet på identitets certifikatets CN-namn.

   Lägg till `-ContainerOs Linux` parametern om du använder Linux-behållare i Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Config. yaml-filen lagrar ditt certifikat och viktig information som fil-URI: er. Men kommandot Initialize-IoTEdge hanterar den här formateringen för dig, så att du kan ange den absoluta sökvägen till certifikatet och nyckelfilen på enheten.

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startats kan du gå till IoT Hub och börja distribuera IoT Edge moduler till enheten.

Du kan kontrol lera att den enskilda registrering som du skapade i enhets etablerings tjänsten användes. Navigera till din enhets etablerings tjänst instans i Azure Portal. Öppna registrerings informationen för den enskilda registrering som du har skapat. Observera att statusen för registreringen är **tilldelad** och att enhets-ID visas.

Använd följande kommandon på enheten för att kontrol lera att körningen har installerats och startats.

### <a name="linux-device"></a>Linux-enhet

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
systemctl status iotedge
```

Undersök tjänst loggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista med moduler som körs.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-enhet

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök tjänst loggar.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler som körs.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Med registrerings processen för enhets etablerings tjänsten kan du ange enhets-ID och enhets dubbla taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in enskilda enheter eller grupper av enheter med automatisk enhets hantering. Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med hjälp av Azure Portal](how-to-deploy-at-scale.md) eller [med hjälp av Azure CLI](how-to-deploy-cli-at-scale.md).
