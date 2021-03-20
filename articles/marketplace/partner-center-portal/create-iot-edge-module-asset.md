---
title: Förbered din IoT Edge module tekniska till gångar – Azure Marketplace
description: Lär dig mer om tekniska och konfigurations krav din Sakernas Internet (IoT) Edge-modul tekniska till gångar måste uppfylla innan du kan publicera dem på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93346782"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Förbered din IoT Edge module tekniska till gångar

Den här artikeln beskriver de krav som din Sakernas Internet (IoT) Edge-modulens tekniska till gångar måste uppfylla innan de publiceras i Azure Marketplace.

## <a name="get-started"></a>Kom igång

En IoT Edge modul är en Docker-kompatibel behållare som körs på en IoT Edge enhet.

- Mer information om IoT Edge moduler finns i [förstå Azure IoT Edge moduler](../../iot-edge/iot-edge-modules.md).
- För att komma igång med din IoT Edge modul utveckling, se [utveckla egna IoT Edge-moduler](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Tekniska krav

Din IoT Edge-modul måste uppfylla följande tekniska krav för att certifieras och publiceras på Azure Marketplace.

### <a name="platform-support"></a>Plattformssupport

Din IoT Edge-modul måste ha stöd för något av följande plattforms alternativ:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Nivå 1-plattformar som stöds av IoT Edge

Modulen måste ha stöd för alla plattformar på nivå 1 som stöds av IoT Edge (som anges i [Azure IoT Edge support](../../iot-edge/support.md)). Vi rekommenderar det här alternativet eftersom det ger en bättre kund upplevelse. Moduler som uppfyller dessa villkor visas. En modul som använder detta plattforms alternativ måste:

- Ange en senaste tagg och en versions tagg (till exempel 1.0.1) som är manifestbaserade taggar som skapats med [GitHub manifest-Tool](https://github.com/estesp/manifest-tool).

- Använd fliken erbjudande lista i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) för att lägga till en länk under avsnittet **användbara länkar** i [Azure IoT Edge certifierad enhets katalog](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>En delmängd av nivå 1-plattformar som stöds av IoT Edge

Modulen måste ha stöd för en delmängd (minst en) på nivå 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge support](../../iot-edge/support.md)). En modul som använder detta plattforms alternativ måste:

- Ange en senaste tagg och en versions tagg (till exempel 1.0.1) som är manifest-taggar som skapats med GitHub [-manifestet,](https://github.com/estesp/manifest-tool) om mer än en plattform stöds. Manifest taggar är valfria endast när en plattform stöds.
- Använd fliken erbjudande lista i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) för att lägga till en länk under avsnittet **användbara länkar** till minst en IoT Edge enhet från den [Azure IoT Edge certifierade enhets katalogen](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Det här är en bild av avsnittet erbjudande lista i Partner Center":::

### <a name="device-dimensions"></a>Enhets dimensioner

IoT Edge modulens dimensioner (till exempel processor, RAM, lagring och GPU) på riktade IoT Edge enheter måste uppfylla följande krav:

- Modulen måste fungera med minst en IoT Edge enhet från den [Azure IoT Edge certifierade enhets katalogen](https://catalog.azureiotsolutions.com/).

- De minsta maskin varu kraven måste vara dokumenterade som det sista stycket i beskrivningen av erbjudandet (under fliken erbjudande lista i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace)). Alternativt kan du också ange de rekommenderade maskin varu kraven om de skiljer sig avsevärt. Lägg till exempel till följande avsnitt i slutet av din erbjudande Beskrivning:

Kopiera HTML-texten eller använd motsvarande RTF-funktioner i redigerings fönstret.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfiguration

Modulen måste innehålla standard konfigurations inställningar för att distribuera till en IoT Edge enhet så enkelt som möjligt. Den här informationen kan anges på sidan **teknisk konfiguration** för planen i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). Behållaren kan också innehålla IoT Edge module SDK för att aktivera kommunikation med Edge Hub och IoT Hub.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-moduler måste kunna starta med de standardinställningar som finns på sidan **teknisk konfiguration** för planen i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). Följande standardinställningar är tillgängliga:

- Standard **vägar**
- Standardmodul, **dubbla önskade egenskaper**
- Standardvariabler för **miljön**
- Standard **alternativ för att skapa behållare**

I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds Server) lägger du till en parameter som standardvärdet. Det här värdet är versaler och omges av hakparenteser. I det här exemplet skulle du ställa in följande standard miljö variabel:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurations dokumentation

Alla konfigurations inställningar för en IoT Edge modul måste vara tydligt dokumenterade. Du måste till exempel dokumentera hur de ska använda sina vägar, ha två önskade egenskaper, miljövariabler, createOptions och så vidare. Du måste antingen ange en länk till din dokumentation eller göra den till en del av ditt erbjudande eller din plan beskrivning. Du kan ange den här informationen på sidan **erbjudande lista** och **prenumeration** på [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Taggar och versions hantering

Kunderna måste enkelt kunna distribuera en modul och automatiskt hämta uppdateringar från Marketplace (i ett utvecklings scenario). De måste också kunna använda och låsa en exakt version som de har testat (i ett produktions scenario).

IoT Edge moduler måste uppfylla följande krav för att möta kunderna och publicera dem på Marketplace.

- Inkludera den senaste taggen manifest som pekar på den senaste versionen på alla plattformar som stöds.
- Gör versions Taggar i formatet X. Y. Z, där X, Y och Z är heltal.
- Inkludera en "version"-tagg, till exempel 1.0.1, som pekar mot en angiven version på alla plattformar som stöds.
- Uppdatera inte "version"-taggar, till exempel 1.0.1, eftersom de inte får ändras.

> [!NOTE]
> Om du vill kan versions hantering inkludera Taggar med rullande version, till exempel 2,0 och 1,0. Detta stöder underhåll av flera huvud versioner parallellt.

### <a name="telemetry"></a>Telemetri

Moduler som använder IoT module SDK måste ange den unika modulens identifierare till PublisherId. OfferId. SkuId för telemetri. En unik identifierare hjälper Azure Marketplace att identifiera antalet instanser som körs.

Använd någon av följande metoder i IoT-modulens SDK: er för att ange ProductInfo till den här identifieraren:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

För moduler som inte använder sig av IoT module SDK är mindre exakta insikter tillgängliga via partner Center, till exempel antalet hämtningar.

### <a name="security"></a>Säkerhet

IoT Edge moduler måste undvika [privilegierade moduler](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Fråga i stället efter minsta privilegie rad åtkomst till värden som möjligt.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Att inkludera IoT module SDK är inte ett krav för certifiering. Men även i IoT module SDK kan du få en bättre användar upplevelse. Till exempel för att stödja routning eller sändning av meddelanden till molnet.

IoT module SDK krävs för att hämta telemetridata om antalet instanser som körs.

## <a name="recertification-process"></a>Återcertifierings process

Partners meddelas när det finns en större ändring som påverkar deras moduler, till exempel:

- Stöd mat ris för nivå 1/båge stöds av IoT Edge
- IoT module SDK
- IoT Edge-körning
- Rikt linjer för certifiering av IoT Edge modul

Partner måste uppdatera och certifiera om sina erbjudanden genom att publicera dem på nytt i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

Ditt erbjudande kan också recertifieras om du uppdaterar det, till exempel lägga till nya avbildnings taggar.

## <a name="host-module-in-azure-container-registry"></a>Värdbaserad modul i Azure Container Registry

För att ladda upp din IoT Edge-modul till Azure Marketplace måste du först vara värd för den i en [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Modulen måste innehålla alla Taggar som du vill publicera, inklusive de avbildnings taggar som en manifest-tagg refererar till. Mer information finns i självstudien [skapa ett Azure Container Registry och skicka en behållar avbildning](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett IoT Edge-modulerbjudande](azure-iot-edge-module-creation.md)