---
title: Enhets anslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: dc0655aba424d29a4055f0d50a20057f22d084ed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015463"
---
# <a name="get-connected-to-azure-iot-central"></a>Ansluta till Azure IoT Central

*Den här artikeln gäller operatörer och enhets utvecklare.*

Den här artikeln beskriver hur enheter ansluter till ett Azure IoT Central-program. Innan en enhet kan utbyta data med IoT Central måste den:

- *Autentisera*. Autentisering med IoT Central programmet använder antingen en _SAS-token (signatur för delad åtkomst)_ eller ett _X. 509-certifikat_. X. 509-certifikat rekommenderas i produktions miljöer.
- *Registrera dig*. Enheter måste vara registrerade med IoT Central-programmet. Du kan visa registrerade enheter på sidan **enheter** i programmet.
- *Associera med en enhets mall*. I ett IoT Central program definierar enhets mallar det gränssnitt som operatörer använder för att visa och hantera anslutna enheter.

IoT Central stöder följande två scenarier för registrering av enheter:

- *Automatisk registrering*. Enheten registreras automatiskt första gången den ansluter. Det här scenariot ger OEM-tillverkare möjlighet att tillverka enheter som kan ansluta utan att först registreras. En OEM genererar lämpliga autentiseringsuppgifter för enheten och konfigurerar enheterna i fabriken. Alternativt kan du kräva en operatör för att godkänna enheten innan den börjar skicka data. I det här scenariot måste du konfigurera en 509-eller SAS- _gruppregistrering_ i ditt program.
- *Manuell registrering*. Operatörer registrerar antingen enskilda enheter på sidan **enheter** eller [importerar en CSV-fil](howto-manage-devices.md#import-devices) för att registrera enheter. I det här scenariot kan du använda X. 509-eller SAS- _gruppregistrering_, eller X. 509 eller SAS _individuell registrering_.

Enheter som ansluter till IoT Central bör följa *IoT plug and Play-konventionerna*. En av dessa konventioner är att en enhet ska skicka _modell-ID: t_ för den enhets modell som den implementerar när den ansluter. Modell-ID: t gör det möjligt för IoT Central programmet att associera enheten med rätt enhets mall.

IoT Central använder [Azure IoT Hub Device Provisioning-tjänsten (DPS)](../../iot-dps/about-iot-dps.md) för att hantera anslutnings processen. En enhet ansluter först till en DPS-slutpunkt för att hämta den information som krävs för att ansluta till ditt program. Internt använder ditt IoT Central-program en IoT-hubb för att hantera enhets anslutningar. Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

I den här artikeln beskrivs följande steg i enhets anslutningen:

- [Grupp registrering för X. 509](#x509-group-enrollment)
- [Registrering av SAS-grupp](#sas-group-enrollment)
- [Individuell registrering](#individual-enrollment)
- [Enhetsregistrering](#device-registration)
- [Koppla en enhet till en enhets mall](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Grupp registrering för X. 509

I en produktions miljö är användningen av X. 509-certifikat den rekommenderade mekanismen för enhets autentisering för IoT Central. Mer information finns i [enhets autentisering med X. 509 CA-certifikat](../../iot-hub/iot-hub-x509ca-overview.md).

Så här ansluter du en enhet med ett X. 509-certifikat till ditt program:

1. Skapa en *registrerings grupp* som använder typen **certifikat (X. 509)** för attestering.
1. Lägg till och verifiera ett mellanliggande eller rot-X. 509-certifikat i registrerings gruppen.
1. Generera ett löv certifikat från rot-eller mellanliggande certifikat i registrerings gruppen. Skicka löv certifikatet från enheten när det ansluter till ditt program.

Mer information finns i [så här ansluter du enheter med X. 509-certifikat](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Endast i testnings syfte

För testning kan du använda följande verktyg för att generera rot-, mellanliggande och enhets certifikat:

- [Verktyg för Azure IoT Device Provisioning enhets-SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): en samling Node.js verktyg som du kan använda för att generera och verifiera X. 509-certifikat och-nycklar.
- Om du använder en DevKit-enhet genererar detta [kommando rads verktyg](https://aka.ms/iotcentral-docs-dicetool) ett CA-certifikat som du kan lägga till i ditt IoT Central program för att verifiera certifikaten.
- [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): en samling PowerShell-och bash-skript för att:
  - Skapa en certifikat kedja.
  - Spara certifikaten som CER-filer som ska överföras till ditt IoT Central-program.
  - Använd verifierings koden från IoT Central programmet för att generera verifierings certifikatet.
  - Skapa löv certifikat för dina enheter med dina enhets-ID: n som en parameter till verktyget.

## <a name="sas-group-enrollment"></a>Registrering av SAS-grupp

Så här ansluter du en enhet med SAS-nyckel för enhet till ditt program:

1. Skapa en *registrerings grupp* som använder typen signatur för **signatur för delad åtkomst (SAS)** .
1. Kopiera gruppens primära eller sekundära nyckel från registrerings gruppen.
1. Använd Azure CLI för att skapa en enhets nyckel från grupp nyckeln:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Använd den genererade enhets nyckeln när enheten ansluter till ditt IoT Central-program.

## <a name="individual-enrollment"></a>Individuell registrering

Kunder ansluter enheter som var och en har sina egna autentiseringsuppgifter, använder enskilda registreringar. En enskild registrering är en post för en enskild enhet som tillåts ansluta. Enskilda registreringar kan använda antingen X. 509-löv certifikat eller SAS-token (från en fysisk eller virtuell Trusted Platform Module) som attesterings metoder. Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken. Mer information finns i [DPS individuell registrering](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> När du skapar en enskild registrering för en enhet har den företräde framför standard alternativen för grupp registrering i IoT Central programmet.

### <a name="create-individual-enrollments"></a>Skapa enskilda registreringar

IoT Central stöder följande mekanismer för attestering för enskilda registreringar:

- **Attestering av symmetrisk nyckel:** Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med DPS-instansen. Om du vill skapa en enskild registrering som använder symmetriska nycklar öppnar du sidan **enhets anslutning** för enheten, väljer **individuell registrering** som anslutnings metod och **signatur för delad åtkomst (SAS)** som mekanismen. Ange base64-kodade primära och sekundära nycklar och spara ändringarna. Använd **ID-omfånget**, **enhets-ID**: t och antingen den primära eller sekundära nyckeln för att ansluta din enhet.

    > [!TIP]
    > För testning kan du använda **openssl** för att generera base64-kodade nycklar: `openssl rand -base64 64`

- **X. 509-certifikat:** Om du vill skapa en enskild registrering med X. 509-certifikat, öppnar du sidan **enhets anslutning** , väljer **individuell registrering** som anslutnings metod och **certifikat (X. 509)** som mekanism. Enhets certifikat som används med en enskild registrerings post har ett krav på att utfärdaren och ämnet CN anges till enhets-ID: t.

    > [!TIP]
    > För testning kan du använda [verktyg för Azure IoT Device Provisioning-enheten SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) att generera ett självsignerat certifikat: `node create_test_cert.js device "mytestdevice"`

- **Trusted Platform Module (TPM) attestering:** En [TPM](../../iot-dps/concepts-tpm-attestation.md) är en typ av modul för maskin varu säkerhet. Att använda en TPM är ett av de säkraste sätten att ansluta en enhet. I den här artikeln förutsätter vi att du använder en diskret, inbyggd program vara eller integrerad TPM. Programvarubaserade TPM: er lämpar sig väl för prototyper eller testning, men de ger inte samma säkerhets nivå som diskreta, inbyggda program eller integrerade TPM: er. Använd inte programvaru-TPM: er i produktion. Om du vill skapa en enskild registrering som använder en TPM öppnar du sidan **enhets anslutning** , väljer **individuell registrering** som anslutnings metod och **TPM** som mekanism. Ange TPM-bekräftelse nyckeln och spara anslutnings informationen för enheten.

## <a name="device-registration"></a>Enhetsregistrering

Innan en enhet kan ansluta till ett IoT Central-program måste den registreras i programmet:

- Enheter kan registreras automatiskt när de ansluter först. Om du vill använda det här alternativet måste du använda antingen en grupp registrering för [X. 509](#x509-group-enrollment) eller [SAS-grupp](#sas-group-enrollment).
- En operatör kan importera en CSV-fil för att registrera en lista över enheter i programmet.
- En operatör kan registrera en enskild enhet manuellt på sidan **enheter** i programmet.

IoT Central gör det möjligt för OEM-tillverkare att tillverka enheter som kan registreras automatiskt. En OEM genererar lämpliga autentiseringsuppgifter för enheten och konfigurerar enheterna i fabriken. När en kund vänder sig till en enhet för första gången ansluter den till DPS, som sedan ansluter enheten automatiskt till rätt IoT Central-program. Alternativt kan du kräva en operatör för att godkänna enheten innan den börjar skicka data till programmet.

> [!TIP]
> På sidan **Administration > enhets anslutning** styr alternativet för **automatiskt godkännande** om en operatör måste godkänna enheten manuellt innan den kan börja skicka data.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Registrera enheter som använder X. 509-certifikat automatiskt

1. Generera löv certifikat för enheterna med hjälp av rot-eller mellanliggande certifikat som du har lagt till i din [X. 509-registrerings grupp](#x509-group-enrollment). Använd enhets-ID: na som `CNAME` i blad certifikaten. Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken.

1. Som en OEM-tillverkare blinkar varje enhet med ett enhets-ID, ett genererat X. 509-löv certifikat och värdet för programmets **ID-omfång** . Enhets koden bör också skicka modell-ID: t för den enhets modell som den implementerar.

1. När du växlar på en enhet ansluter den först till DPS för att hämta IoT Central anslutnings information.

1. Enheten använder informationen från DPS för att ansluta till, och registrera dig hos, ditt IoT Central-program.

IoT Central-programmet använder det modell-ID som skickas av enheten för att [koppla den registrerade enheten till en enhets mall](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Registrera automatiskt enheter som använder SAS-token

1. Kopiera gruppens primära nyckel från registrerings gruppen **SAS-IoT-Devices** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Grupp primär nyckel från SAS-IoT-enheter registrerings grupp":::

1. Använd `az iot central device compute-device-key` kommandot för att generera enhetens SAS-nycklar. Använd gruppens primära nyckel från föregående steg. Enhets-ID: t får innehålla bokstäver, siffror och `-` tecken:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Som en OEM-tillverkare blinkar varje enhet med enhets-ID, den genererade SAS-nyckeln för enheten och värdet för programmets **ID-omfång** . Enhets koden bör också skicka modell-ID: t för den enhets modell som den implementerar.

1. När du växlar på en enhet ansluter den först till DPS för att hämta IoT Central registrerings information.

1. Enheten använder informationen från DPS för att ansluta till, och registrera dig hos, ditt IoT Central-program.

IoT Central-programmet använder det modell-ID som skickas av enheten för att [koppla den registrerade enheten till en enhets mall](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Mass registrering av enheter i förväg

Om du vill registrera ett stort antal enheter med IoT Central programmet använder du en CSV-fil för att [Importera enhets-ID: n och enhets namn](howto-manage-devices.md#import-devices).

Om dina enheter använder SAS-token för att autentisera, [Exportera en CSV-fil från IoT Central-programmet](howto-manage-devices.md#export-devices). Den exporterade CSV-filen innehåller enhets-ID: n och SAS-nycklar.

Om dina enheter använder X. 509-certifikat för att autentisera, genererar du X. 509-löv certifikat för dina enheter med hjälp av rot-eller mellanliggande certifikat i som du laddade upp till din X. 509-registrerings grupp. Använd de enhets-ID: n som du har importerat som `CNAME` värde i löv certifikaten.

Enheter måste använda värdet för **ID-omfång** för ditt program och skicka ett modell-ID när de ansluter.

> [!TIP]
> Du kan hitta värdet för **ID-omfång** i **Administration > enhets anslutning**.

### <a name="register-a-single-device-in-advance"></a>Registrera en enskild enhet i förväg

Den här metoden är användbar när du experimenterar med IoT Central eller testning av enheter. Välj **+ nytt** på sidan **enheter** för att registrera en enskild enhet. Du kan använda enhets anslutningens SAS-nycklar för att ansluta enheten till IoT Central program. Kopiera _enhetens SAS-nyckel_ från anslutnings informationen för en registrerad enhet:

![SAS-nycklar för en enskild enhet](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Koppla en enhet till en enhets mall

IoT Central associerar automatiskt en enhet med en enhets mall när enheten ansluter. En enhet skickar ett [modell-ID](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) när den ansluter. IoT Central använder modell-ID: t för att identifiera enhets mal len för den aktuella enhets modellen. Identifierings processen fungerar på följande sätt:

1. Om enhets mal len redan har publicerats i IoT Central-programmet är enheten kopplad till enhets mal len.
1. Om enhets mal len inte redan har publicerats i IoT Central-programmet söker IoT Central efter enhets modellen i den [offentliga modellens lagrings plats](https://github.com/Azure/iot-plugandplay-models). Om IoT Central hittar modellen används den för att generera en grundläggande enhets mall.
1. Om IoT Central inte hittar modellen i den offentliga modellens lagrings plats markeras enheten som **associerad**. En operatör kan skapa en enhets mall för enheten och sedan migrera den anslutna enheten till den nya enhets mal len.

Följande skärm bild visar hur du visar modell-ID för en enhets mall i IoT Central. I en enhets mall väljer du en komponent och väljer sedan **Visa identitet**:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Skärm bild som visar modell-ID i termostat enhets mall.":::

Du kan visa [termostat-modellen](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) i den offentliga modellens lagrings plats. Definitionen av modell-ID: t ser ut så här:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Enhets status värden

När en riktig enhet ansluter till ditt IoT Central-program ändras enhetens status enligt följande:

1. Enhetens status **registreras** först. Den här statusen innebär att enheten skapas i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på sidan **enheter** .
    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** .

1. Enhetens status ändras till **etablerad** när enheten som anslöt till ditt IoT Central program med giltiga autentiseringsuppgifter Slutför etablerings steget. I det här steget använder enheten DPS för att automatiskt hämta en anslutnings sträng från den IoT Hub som används av ditt IoT Central-program. Enheten kan nu ansluta till IoT Central och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet blockeras kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har statusen **blockerad**. En operatör måste återställa enheten innan den kan återuppta sändning av data. När en operatör avblockerar en enhet återgår statusen till sitt tidigare värde, **registrerad** eller **etablerad**.

1. Om enhetens status **väntar på godkännande** innebär det att alternativet **Godkänn automatiskt** är inaktiverat. En operatör måste explicit godkänna en enhet innan den börjar skicka data. Enheter som inte är registrerade manuellt på sidan **enheter** , men som är anslutna med giltiga autentiseringsuppgifter, har enhets statusen **väntar på godkännande**. Operatörer kan godkänna dessa enheter från sidan **enheter** med knappen **Godkänn** .

1. Om enhetens status är **associerad** innebär det att enheten som ansluter till IoT Central saknar en associerad enhets mall. Den här situationen inträffar vanligt vis i följande scenarier:

    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** utan att ange enhets mal len.
    - En enhet registrerades manuellt på sidan **enheter** utan att ange enhets mal len. Enheten anslöt sedan med giltiga autentiseringsuppgifter.  

    Operatören kan koppla en enhet till en enhets mall från sidan **enheter** med knappen **migrera** .

## <a name="sdk-support"></a>SDK-support

Azure-enhetens SDK: er erbjuder det enklaste sättet för dig att implementera enhets koden. Följande enhets-SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub anslutning

All enhets kommunikation med IoT Hub använder följande IoT Hub anslutnings alternativ:

- [Meddelanden från enhet till moln](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Meddelanden från moln till enhet](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Enhets dubbla](../../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central enhets funktioner mappas till IoT Hub funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetri | Meddelanden från enhet till moln |
| Offline-kommandon | Meddelanden från moln till enhet |
| Egenskap | Enhetens dubbla rapporterade egenskaper |
| Egenskap (skrivbar) | Enhetens dubbla önskade och rapporterade egenskaper |
| Kommando | Direkta metoder |

### <a name="protocols"></a>Protokoll

Enhets-SDK: erna stöder följande nätverks protokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Information om dessa skillnads protokoll och vägledning om hur du väljer ett finns i [Välj ett kommunikations protokoll](../../iot-hub/iot-hub-devguide-protocols.md).

Om enheten inte kan använda något av de protokoll som stöds använder du Azure IoT Edge att göra protokoll konverteringen. IoT Edge stöder andra Intelligence-scenarier för att avlasta bearbetning från Azure IoT Central-programmet.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och din Azure-IoT Central krypteras. IoT Hub autentiserar varje begäran från en enhet som ansluts till någon av de enhets riktade IoT Hub slut punkterna. För att undvika att utbyta autentiseringsuppgifter via kabeln använder enheten signerade token för att autentisera. Mer information finns i, [kontrol lera åtkomst till IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Läs [metod tips](concepts-best-practices.md) för att utveckla enheter.
- Granska en exempel kod som visar hur du använder SAS-token i [Självstudier: skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md)
- Lär dig hur du [ansluter enheter med X. 509-certifikat med hjälp av Node.js Device SDK för IoT Central program](how-to-connect-devices-x509.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
- Läs om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md)
