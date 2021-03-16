---
title: Azure IoT Edge utvecklings miljö | Microsoft Docs
description: Lär dig mer om de system och utvecklings verktyg från första part som hjälper dig att skapa IoT Edge moduler
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 517bcdab375db819b0942306df82e7285b9473b1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487708"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Förbered din utvecklings-och test miljö för IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge flyttar din befintliga affärs logik till enheter som arbetar i gränsen. För att förbereda program och arbets belastningar att köras som [IoT Edge moduler](iot-edge-modules.md)måste du skapa dem som behållare. Den här artikeln innehåller rikt linjer för hur du konfigurerar utvecklings miljön så att du kan skapa en IoT Edge-lösning. När du har konfigurerat utvecklings miljön kan du lära dig hur du [utvecklar dina egna IoT Edge moduler](module-development.md).

I en IoT Edge lösning finns det minst två datorer att tänka på. En är den IoT Edge enheten (eller enheterna), som kör modulen IoT Edge. Den andra är den utvecklings dator som du använder för att bygga, testa och distribuera moduler. Den här artikeln fokuserar främst på utvecklings datorn. De två datorerna kan vara identiska i test syfte. Du kan köra IoT Edge på utvecklings datorn och distribuera moduler till den.

## <a name="operating-system"></a>Operativsystem

Azure IoT Edge körs på en angiven uppsättning [operativ system som stöds](support.md). För att utveckla för IoT Edge kan du använda de flesta operativ system som kan köra en behållar motor. Behållar motorn är ett krav på utvecklings datorn för att bygga dina moduler som behållare och skicka dem till ett behållar register.

Om din utvecklings dator inte kan köra Azure IoT Edge fortsätter du i den här artikeln om du vill veta mer om [testnings verktyg](#testing-tools) som hjälper dig att testa och felsöka lokalt.

Utvecklings datorns operativ system behöver inte matcha operativ systemet på din IoT Edge-enhet. Behållar operativ systemet måste dock vara konsekvent mellan utvecklings datorn och IoT Edge enheten. Du kan till exempel utveckla moduler på en Windows-dator och distribuera dem till en Linux-enhet. Windows-datorn måste köra Linux-behållare för att skapa modulerna för Linux-enheten.

## <a name="container-engine"></a>Container motor

Det centrala konceptet IoT Edge är att du kan fjärrdistribuera din affärs-och moln logik till enheter genom att paketera den i behållare. Om du vill bygga behållare behöver du en behållar motor på din utvecklings dator.

Den enda behållar motor som stöds för IoT Edge enheter i produktion är Moby. En behållar motor som är kompatibel med det öppna container initiativet, som Docker, kan skapa IoT Edge module-avbildningar.

## <a name="development-tools"></a>Utvecklingsverktyg

Både Visual Studio och Visual Studio Code har tillägg för tillägg som hjälper dig att utveckla IoT Edge lösningar. Dessa tillägg innehåller språkspecifika mallar som hjälper dig att skapa och distribuera nya IoT Edge scenarier. Azure IoT Edge tillägg för Visual Studio och Visual Studio Code hjälper dig att koda, bygga, distribuera och felsöka dina IoT Edge-lösningar. Du kan skapa en hel IoT Edge-lösning som innehåller flera moduler och tilläggen uppdaterar automatiskt en mall för distributions manifestet med varje ny modul. Med tilläggen kan du också hantera dina IoT-enheter från Visual Studio eller Visual Studio Code. Distribuera moduler till en enhet, övervaka status och Visa meddelanden när de anländer till IoT Hub. Båda tilläggen använder [IoT EdgeHub dev-verktyget](#iot-edgehub-dev-tool) för att aktivera lokal körning och fel sökning av moduler på utvecklings datorn också.

Om du föredrar att utveckla med andra redigerare eller från CLI innehåller verktyget Azure IoT Edge dev kommandon så att du kan utveckla och testa från kommando raden. Du kan skapa nya IoT Edge scenarier, skapa modulblad, köra moduler i en simulator och övervaka meddelanden som skickas till IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-tillägg

Azure IoT Edge-tillägget för Visual Studio Code innehåller IoT Edge modulblad som bygger på programmeringsspråk, inklusive C, C#, Java, Node.js och python samt Azure Functions i C#.

Mer information och hämtning finns i [Azure IoT Tools för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Förutom IoT Edge tillägg kan det vara bra att installera ytterligare tillägg för utveckling. Du kan till exempel använda [Docker-stöd för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för att hantera dina avbildningar, behållare och register. Alla språk som stöds har dessutom tillägg för Visual Studio-kod som kan hjälpa dig när du utvecklar moduler.

#### <a name="prerequisites"></a>Förutsättningar

Mallarna för vissa språk och tjänster har förutsättningar som krävs för att bygga projektmappen på din utvecklings dator med Visual Studio Code.

| Modulmall | Förutsättning |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Ange JAVA_HOME miljö variabel](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modul Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019-tillägg

Azure IoT Edge Tools för Visual Studio tillhandahåller en mall för IoT Edge modul som skapats på C# och C.

Mer information och hämtning finns i [Azure IoT Edge Tools för Visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) eller [Azure IoT Edge Tools för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge dev Tool

Verktyget Azure IoT Edge dev fören klar IoT Edge utveckling med kommando rads förmågor. Det här verktyget innehåller CLI-kommandon för att utveckla, felsöka och testa moduler. Verktyget IoT Edge dev fungerar med ditt utvecklings system, oavsett om du har installerat beroenden manuellt på din dator eller använder IoT Edge dev-behållaren.

Mer information och kom igång finns i [IoT Edge dev-verktyget wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testverktyg

Det finns flera test verktyg som hjälper dig att simulera IoT Edge enheter eller felsöka moduler på ett mer effektivt sätt. I följande tabell visas en jämförelse mellan verktygen och enskilda avsnitt beskriver varje verktyg mer specifikt.

Endast IoT Edge runtime stöds för produktions distributioner, men med följande verktyg kan du simulera eller enkelt skapa IoT Edge enheter för utvecklings-och testnings ändamål. Dessa verktyg är inte ömsesidigt uteslutande, men kan användas tillsammans för att få en fullständig utvecklings upplevelse.

| Verktyg | Kallas även | Plattformar som stöds | Bäst för |
| ---- | ------------- | ------------------- | --------- |
| Verktyget IoT EdgeHub dev  | iotedgehubdev | Windows, Linux, macOS | Simulera en enhet för att felsöka moduler. |
| IoT Edge dev-behållare | iotedgedev | Windows, Linux, macOS | Utveckla utan att installera beroenden. |
| IoT Edge körning i en behållare | iotedgec | Windows, Linux, macOS, ARM | Testning på en enhet som kanske inte har stöd för körnings miljön. |
| Behållare för IoT Edge enhet | toolboc/Azure-IoT-Edge-Device-container | Windows, Linux, macOS, ARM | Testa ett scenario med många IoT Edge enheter i stor skala. |

### <a name="iot-edgehub-dev-tool"></a>Verktyget IoT EdgeHub dev

Verktyget Azure IoT EdgeHub dev tillhandahåller en lokal utvecklings-och fel söknings miljö. Verktyget hjälper dig att starta IoT Edge moduler utan IoT Edge runtime så att du kan skapa, utveckla, testa, köra och felsöka IoT Edge moduler och lösningar lokalt. Du behöver inte skicka avbildningar till ett behållar register och distribuera dem till en enhet för testning.

Verktyget IoT EdgeHub dev har utformats för att fungera tillsammans med kod tilläggen Visual Studio och Visual Studio, samt med verktyget IoT Edge dev. Den stöder inre loop-utveckling samt yttre sling-testning, så att de även integreras med DevOps-verktygen.

Mer information och installera finns i [verktyget Azure IoT EdgeHub dev](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge dev-behållare

Azure IoT Edge dev-behållaren är en Docker-behållare som har alla beroenden som du behöver för att IoT Edge utveckling. Den här behållaren gör det enkelt att komma igång med det språk som du vill utveckla i, inklusive C#, python, Node.js och Java. Allt du behöver installera är en behållar motor, som Docker eller Moby, för att hämta behållaren till din utvecklings dator.

Mer information finns i [Azure IoT Edge dev container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge körning i en behållare

IoT Edge runtime i en behållare ger en fullständig körning som tar din enhets anslutnings sträng som en miljö variabel. Med den här behållaren kan du testa IoT Edge moduler och scenarier på ett system som kanske inte stöder körning av program vara internt, till exempel macOS. Alla moduler som du distribuerar startas utanför runtime-behållaren. Om du vill att körningen och alla distribuerade moduler ska finnas i samma behållare, bör du överväga IoT Edge enhets behållaren i stället.

Mer information finns i [köra Azure IoT Edge i en behållare](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Behållare för IoT Edge enhet

Den IoT Edge enhets containern är en fullständig IoT Edge enhet som är redo att startas på en dator med en behållar motor. Enhets containern innehåller IoT Edge Runtime och en behållar motor själva. Varje instans av behållaren är en helt funktionell själv etablerings IoT Edge enhet. Enhets behållaren stöder fjärrfelsökning av moduler, så länge det finns en nätverks väg till modulen. Enhets containern är lämplig för att snabbt skapa ett stort antal IoT Edge enheter för att testa scenarier med stor skala eller Azure-pipelines. Det stöder också distribution till Kubernetes via Helm.

Mer information finns i [Azure IoT Edge enhets behållare](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-verktyg

När du är redo att utveckla lösningar för omfattande produktions scenarier kan du dra nytta av moderna DevOps-principer, inklusive automatisering, övervakning och strömlinjeformade program teknik processer. IoT Edge har tillägg som stöder DevOps-verktyg, inklusive Azure DevOps, Azure DevOps Projects och Jenkins. Om du vill anpassa en befintlig pipeline eller använda ett annat DevOps-verktyg som CircleCI eller TravisCI, kan du göra det med CLI-funktionerna som ingår i IoT Edge dev-verktyget.

Mer information, vägledning och exempel finns i följande sidor:

* [Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps starter](how-to-devops-starter.md)
* [Azure IoT Edge Jenkins-plugin](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub lagrings platsen](https://github.com/toolboc/IoTEdge-DevOps)