---
title: Azure IoT Edge för | Microsoft Docs
description: Lär dig mer om de system som stöds och utvecklingsverktyg från första part som hjälper dig att skapa IoT Edge moduler
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1070a5ddf298ad88100e7803635e970f9a314e52
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869589"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Förbered utvecklings- och testmiljön för IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge flyttar din befintliga affärslogik till enheter som arbetar vid gränsen. För att förbereda dina program och arbetsbelastningar så att [de IoT Edge som moduler](iot-edge-modules.md)måste du skapa dem som containrar. Den här artikeln innehåller vägledning om hur du konfigurerar utvecklingsmiljön så att du kan skapa en IoT Edge lösning. När du har ställt in din utvecklingsmiljö kan du lära dig hur du utvecklar [egna IoT Edge moduler.](module-development.md)

I alla IoT Edge finns det minst två datorer att tänka på. En är IoT Edge enhet (eller enheter) som kör IoT Edge modulen. Den andra är den utvecklingsdator som du använder för att skapa, testa och distribuera moduler. Den här artikeln fokuserar främst på utvecklingsdatorn. I testsyfte kan de två datorerna vara desamma. Du kan köra IoT Edge på utvecklingsdatorn och distribuera moduler till den.

## <a name="operating-system"></a>Operativsystem

Azure IoT Edge körs på en specifik uppsättning [operativsystem som stöds.](support.md) När du utvecklar IoT Edge kan du använda de flesta operativsystem som kan köra en containermotor. Containermotorn är ett krav på utvecklingsdatorn att bygga moduler som containrar och skicka dem till ett containerregister.

Om utvecklingsdatorn inte kan köra Azure IoT Edge kan du fortsätta i [](#testing-tools) den här artikeln för att lära dig mer om testverktyg som hjälper dig att testa och felsöka lokalt.

Operativsystemet på utvecklingsdatorn behöver inte matcha operativsystemet för din IoT Edge enhet. Containeroperativsystemet måste dock vara konsekvent mellan utvecklingsdatorn och den IoT Edge enheten. Du kan till exempel utveckla moduler på en Windows-dator och distribuera dem till en Linux-enhet. Windows-datorn måste köra Linux-containrar för att skapa modulerna för Linux-enheten.

## <a name="container-engine"></a>Containermotor

Det centrala konceptet med IoT Edge är att du via fjärrlagring kan distribuera affärs- och molnlogik till enheter genom att paketera den i containrar. Om du vill skapa containrar behöver du en containermotor på utvecklingsdatorn.

Den enda containermotor som stöds för IoT Edge enheter i produktion är Moby. Alla containermotorer som är kompatibla med Open Container Initiative, t.ex. Docker, kan dock skapa IoT Edge-modulavbildningar.

## <a name="development-tools"></a>Utvecklingsverktyg

Både Visual Studio och Visual Studio Code har tillägg som hjälper dig att utveckla IoT Edge lösningar. Dessa tillägg innehåller språkspecifika mallar som hjälper dig att skapa och distribuera nya IoT Edge scenarier. Med Azure IoT Edge tillägg för Visual Studio och Visual Studio Code kan du koda, skapa, distribuera och felsöka dina IoT Edge lösningar. Du kan skapa en hel IoT Edge som innehåller flera moduler, och tilläggen uppdaterar automatiskt en mall för distributionsmanifestet med varje nytt modultillägg. Med tilläggen kan du också hantera dina IoT-enheter inifrån Visual Studio eller Visual Studio Code. Distribuera moduler till en enhet, övervaka status och visa meddelanden när de tas emot IoT Hub. Båda tilläggen använder [IoT EdgeHub-utvecklingsverktyget](#iot-edgehub-dev-tool) för lokal körning och felsökning av moduler på utvecklingsdatorn.

Om du föredrar att utveckla med andra redigerare eller från CLI innehåller Azure IoT Edge dev-verktyget kommandon så att du kan utveckla och testa från kommandoraden. Du kan skapa nya IoT Edge scenarier, skapa modulavbildningar, köra moduler i en simulator och övervaka meddelanden som skickas till IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-tillägg

Tillägget Azure IoT Edge för Visual Studio Code innehåller IoT Edge-modulmallar som bygger på programmeringsspråk som C, C#, Java, Node.js och Python samt Azure-funktioner i C#.

Mer information och nedladdning finns i Azure IoT Tools [för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Förutom tilläggen IoT Edge kan det vara bra att installera ytterligare tillägg för utveckling. Du kan till exempel använda [Docker-stöd för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för att hantera avbildningar, containrar och register. Dessutom har alla större språk som stöds tillägg för Visual Studio Code som kan vara till hjälp när du utvecklar moduler.

#### <a name="prerequisites"></a>Förutsättningar

Modulmallarna för vissa språk och tjänster har förutsättningar som krävs för att skapa projektmapparna på utvecklingsdatorn med hjälp Visual Studio Code.

| Modulmall | Förutsättning |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Ange JAVA_HOME miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modulgenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio tillägget 2017/2019

Verktygen Azure IoT Edge för Visual Studio tillhandahåller en IoT Edge modulmall som bygger på C# och C.

Mer information och nedladdning finns i [Azure IoT Edge Tools för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) [eller Azure IoT Edge Tools för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge dev-verktyg

Verktyget Azure IoT Edge dev förenklar IoT Edge utveckling med kommandoradsförmågor. Det här verktyget innehåller CLI-kommandon för att utveckla, felsöka och testa moduler. Verktyget IoT Edge dev fungerar med ditt utvecklingssystem, oavsett om du har installerat beroendena manuellt på datorn eller använder IoT Edge dev-containern.

Mer information och för att komma igång finns i [IoT Edge dev tool wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testverktyg

Det finns flera testverktyg som hjälper dig att IoT Edge enheter eller felsökningsmoduler mer effektivt. I följande tabell visas en jämförelse på hög nivå mellan verktygen och i de enskilda avsnitten beskrivs varje verktyg mer specifikt.

Endast den IoT Edge körningen stöds för produktionsdistributioner, men med följande verktyg kan du simulera eller enkelt skapa IoT Edge enheter för utveckling och testning. Dessa verktyg är inte ömsesidigt uteslutande, men de kan fungera tillsammans för en fullständig utvecklingsupplevelse.

| Verktyg | Kallas även | Plattformar som stöds | Bäst för |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub-utvecklingsverktyg  | iotedgehubdev | Windows, Linux, macOS | Simulera en enhet för att felsöka moduler. |
| IoT Edge dev-container | iotedgedev | Windows, Linux, macOS | Utveckla utan att installera beroenden. |
| IoT Edge i en container | iotedgec | Windows, Linux, macOS, ARM | Testning på en enhet som kanske inte stöder körningen. |
| IoT Edge enhetscontainer | toolboc/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Testa ett scenario med många IoT Edge enheter i stor skala. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub-utvecklingsverktyg

Med utvecklingsverktyget Azure IoT EdgeHub får du en lokal utvecklings- och felsökningsupplevelse. Verktyget hjälper dig att IoT Edge moduler utan IoT Edge runtime så att du kan skapa, utveckla, testa, köra och felsöka IoT Edge moduler och lösningar lokalt. Du behöver inte skicka avbildningar till ett containerregister och distribuera dem till en enhet för testning.

IoT EdgeHub-utvecklingsverktyget har utformats för att fungera tillsammans med Visual Studio- och Visual Studio Code-tilläggen, samt med IoT Edge dev-verktyget. Den stöder utveckling av inre loopar samt testning av yttre loopar, så den kan även integreras med DevOps-verktygen.

Mer information och hur du installerar finns i [Azure IoT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge dev-container

Containern Azure IoT Edge dev är en Docker-container som har alla beroenden som du behöver för IoT Edge utveckling. Den här containern gör det enkelt att komma igång med det språk du vill utveckla i, inklusive C#, Python, Node.js och Java. Allt du behöver installera är en containermotor, till exempel Docker eller Moby, för att hämta containern till utvecklingsdatorn.

Mer information finns i [Azure IoT Edge dev container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge i en container

Den IoT Edge körningen i en container ger en fullständig körning som tar enhetens anslutningssträng som en miljövariabel. Med den här containern kan du IoT Edge moduler och scenarier i ett system som kanske inte stöder den interna körningen, till exempel macOS. Alla moduler som du distribuerar startas utanför körningscontainern. Om du vill att körningen och eventuella distribuerade moduler ska finnas i samma container kan du överväga IoT Edge enhetscontainern i stället.

Mer information finns i Köra [Azure IoT Edge i en container.](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)

### <a name="iot-edge-device-container"></a>IoT Edge enhetscontainer

Enhetscontainern IoT Edge är en komplett IoT Edge som är redo att startas på valfri dator med en containermotor. Enhetscontainern innehåller IoT Edge och en containermotor. Varje instans av containern är en fullt fungerande självetablering IoT Edge enhet. Enhetscontainern stöder fjärrfelsökning av moduler, så länge det finns en nätverksväg till modulen. Enhetscontainern är bra för att snabbt skapa ett stort IoT Edge enheter för att testa scenarier i stor skala eller Azure Pipelines. Den stöder även distribution till kubernetes via Helm.

Mer information finns i [Azure IoT Edge enhetscontainer](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-verktyg

När du är redo att utveckla skalningslösningar för omfattande produktionsscenarier kan du dra nytta av moderna DevOps-principer, inklusive automatisering, övervakning och effektiviserade processer för programvaruutveckling. IoT Edge har tillägg som stöder DevOps-verktyg som Azure DevOps, Azure DevOps Projects och Jenkins. Om du vill anpassa en befintlig pipeline eller använda ett annat DevOps-verktyg som CircleCI eller CircleCI kan du göra det med DE CLI-funktioner som ingår i IoT Edge dev-verktyget.

Mer information, vägledning och exempel finns på följande sidor:

* [Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps Starter](how-to-devops-starter.md)
* [IoT Edge DevOps GitHub-lagringsplatsen](https://github.com/toolboc/IoTEdge-DevOps)