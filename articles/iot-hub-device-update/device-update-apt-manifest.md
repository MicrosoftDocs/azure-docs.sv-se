---
title: Förstå enhets uppdatering för Azure IoT Hub APT-manifest | Microsoft Docs
description: Förstå hur enhets uppdatering för IoT Hub använder apt-manifestet för en paket-baserad uppdatering.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680095"
---
# <a name="device-update-apt-manifest"></a>APT-manifest för enhets uppdatering

APT-manifestet är en JSON-fil som beskriver en uppdaterings information som krävs av APT-uppdaterings hanteraren. Den här filen kan importeras till enhets uppdatering för IoT Hub precis som andra uppdateringar.

[Lär dig mer](import-update.md) om att importera uppdateringar till enhets uppdatering.

## <a name="overview"></a>Översikt

När ett APT-manifest levereras till en enhets uppdaterings agent som en uppdatering, kommer agenten att bearbeta manifestet och utföra de åtgärder som krävs. Dessa åtgärder omfattar att ladda ned och installera de paket som anges i manifest filen APT och deras beroenden.

Enhets uppdatering stöder hantering av APT-uppdateringstyp och APT-uppdateringar. Med det här stödet kan enhets uppdaterings agenten utvärdera de installerade Debian-paketen och uppdatera de nödvändiga paketen. 

## <a name="schema"></a>Schema

En manifest fil för APT är en JSON-fil med ett versions schema.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Exempel:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Namnet på det här APT-manifestet. Det kan vara det namn eller ID som är meningsfullt för dina scenarier. Till exempel `contoso-iot-edge`.

### <a name="version"></a>version

Ett versions nummer för det här APT-manifestet. Till exempel `1.0.0.0`.


### <a name="packages"></a>distributionspaket

En lista med objekt som innehåller Package-/regionsspecifika egenskaper.

#### <a name="name"></a>name

Paketets namn eller ID. Till exempel `iotedge`.

#### <a name="version"></a>version

Det önskade versions villkoret för paketet. Till exempel `1.0.8-2`.

För närvarande stöds endast det exakta versions numret. Versions numret är den önskade Debian-paket versionen i formatet [epok:] upstream_version [-debian_revision].

**epok** är ett osignerat heltal.

**upstream_version** kan innehålla alfanumeriska tecken, t. ex. ".", "+", "-" och "~". Det ska börja med en siffra.
> [!NOTE]
> "1.0.8" är lika med "1.0.8-0"

Motsvarar till exempel **`"name":"iotedge" and "version":"1.0.8-2"`** att installera ett paket med kommandot `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> Version svärdet innehåller inte ett likhets tecken

Om versionen utelämnas installeras den senaste tillgängliga versionen av det angivna paketet.

[Läs mer](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) om hur Debian-paket är versioner.

> [!NOTE]
> APT Package Manager ignorerar versions krav som anges av ett paket när beroende paket som ska installeras automatiskt löses. Om inte explicita versioner av beroende paket anges kommer de att använda den senaste, även om själva paketet kan ange ett strikt krav (=) i en viss version. Den här automatiska upplösningen kan leda till fel som rör ett ouppfyllda-beroende. [Läs mer](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Om du uppdaterar en version av Azure IoT Edge Security daemon bör du inkludera den önskade versionen av `iotedge` paketet och det beroende `libiothsm-std` paketet i apt-manifestet.
[Läs mer](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Ett apt-manifest kan användas för att uppdatera enhets uppdaterings agenten och dess beroenden. Visa en lista med namnet på enhets uppdaterings agenten och önskad version i apt-manifestet, t. ex. för alla andra paket. Detta apt-manifest kan sedan importeras och distribueras via enhets uppdateringen för IoT Hub pipelinen. 

## <a name="removing-packages"></a>Ta bort paket

Du kan också använda ett apt-manifest för att ta bort installerade paket från enheten. Ett enda apt-manifest kan användas för att ta bort, lägga till och uppdatera flera paket. Om du vill ta bort ett paket lägger du till ett minus tecken "-" efter paket namnet. Du bör inte inkludera ett versions nummer för de paket som du tar bort. Om du tar bort paket via ett apt-manifest tas inte dess beroenden och konfigurationer bort.

Exempel:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Detta apt-manifest tar bort paketet "foo" från den eller de enheter som det distribueras till. 

## <a name="recommended-value-for-installed-criteria"></a>Rekommenderat värde för installerade villkor

De installerade villkoren för ett APT-manifest är `<name>-<version>` där `<name>` namnet på apt-manifestet och `<version>` är versionen av apt-manifestet. Till exempel `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Rikt linjer för att skapa ett APT-manifest

När du skapar APT-manifestet finns det några rikt linjer som du bör tänka på:

- Se alltid till att APT-manifestet är en välformulerad JSON-fil
- Varje APT-manifest bör ha en unik version. Försök att komma igång med en standardiserad metod för att öka versionen av APT-manifestet, så att det passar för dina scenarier och enkelt kan följas
- När det kommer till det önskade läget för varje enskilt paket anger du det exakta namnet och versionen för det paket som du vill installera på enheten. Verifiera alltid värdena mot den paket lagrings plats som du tänker använda som källa för paketet
- Se till att paketen i APT-manifestet visas i den ordning som de ska installeras/tas bort
- Verifiera alltid installationen av paket på en testen het för att säkerställa att resultatet önskas
- När du installerar en specifik version av ett paket (till exempel `iotedge 1.0.9-1` ) är det bästa praxis att även ha i apt-manifestet att de explicita versionerna av de beroende paketen ska installeras (till exempel `libiothsm 1.0.9-1` )
- Kontrol lera alltid att ditt APT-manifest är kumulativt för att undvika att enheten får ett okänt tillstånd. En kumulativ uppdatering ser till att dina enheter har den önskade versionen av varje paket du bryr dig om även om enheten har hoppat över en APT uppdaterings distribution på grund av ett problem med installationen eller om den kopplas från

Exempel:

**Grundläggande APT-manifest**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**FELAKTIG UPPDATERING**

Den här uppdateringen inkluderar bar-paketet, men inte foo-paketet.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**FELFRI UPPDATERING**

Den här uppdateringen inkluderar foo-paketet och inkluderar även bar-paket.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera ny uppdatering](import-update.md)

