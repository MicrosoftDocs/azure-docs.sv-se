---
title: Förstå versions hantering av enhets mallar för dina Azure IoT Central-appar | Microsoft Docs
description: Iterera över dina enhetsspecifika mallar genom att skapa nya versioner och utan att påverka dina Live-anslutna enheter
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995768"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny version av enhets mal len

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

En enhets mall innehåller ett schema som beskriver hur en enhet samverkar med IoT Central. Dessa interaktioner innehåller telemetri, egenskaper och kommandon. Både enheten och IoT Central programmet förlitar sig på en delad förståelse av schemat för att utbyta information. Du kan bara göra begränsade ändringar i schemat utan att behöva bryta kontraktet, det vill säga att de flesta schema ändringar kräver en ny version av enhets mal len. Versions hantering av enhets mal len låter äldre enheter fortsätta med den schema version som de förstår, medan nyare eller uppdaterade enheter använder en senare schema version.

Schemat i en enhets mall definieras i enhets modellen och dess gränssnitt. Enhets mallar innehåller annan information, till exempel moln egenskaper, visnings anpassningar och vyer. Om du gör ändringar i dessa delar av enhets mal len som inte definierar hur enheten utbyter data med IoT Central, behöver du inte version av mallen.

Du måste alltid publicera en uppdaterad enhets mall innan en operatör kan använda den. IoT Central hindrar dig från att publicera större ändringar i en enhets mall utan att först ha den första versionen av mallen.

> [!NOTE]
> Mer information om hur du skapar en enhets mall finns i [Konfigurera och hantera en enhets mall](howto-set-up-template.md)

## <a name="versioning-rules"></a>Versions regler

I det här avsnittet sammanfattas de versions regler som gäller för enhets mallar. Både enhets modeller och gränssnitt har versions nummer. I följande kodfragment visas enhets modellen för en termostat-enhet. Enhets modellen har ett enda gränssnitt. Du kan se versions numret i slutet av `@id` fältet.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Om du vill visa den här informationen i IoT Central användar gränssnitt väljer du **Visa identitet** i redigeraren för enhets mal len:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Visa identiteten för ett gränssnitt för att se versions numret":::

I följande lista visas de regler som avgör när du måste skapa en ny version:

* När en enhets modell har publicerats kan du inte ta bort några gränssnitt, även i en ny version av enhets mal len.
* När en enhets modell har publicerats kan du lägga till ett gränssnitt om du skapar en ny version av enhets mal len.
* När en enhets modell har publicerats kan du ersätta ett gränssnitt med en nyare version om du skapar en ny version av enhets mal len. Om till exempel den sensor v1-mallen använder termostat v1-gränssnittet, kan du skapa en sensor v2-enhets mal len som använder termostat v2-gränssnittet.
* När ett gränssnitt har publicerats kan du inte ta bort något gränssnitts innehåll, även i en ny version av enhets mal len.
* När ett gränssnitt har publicerats kan du lägga till objekt i innehållet i ett gränssnitt om du skapar en ny version av gränssnittet och enhets mal len. Objekt som du kan lägga till i gränssnittet omfattar telemetri, egenskaper och kommandon.
* När ett gränssnitt har publicerats kan du göra icke-schema ändringar i befintliga objekt i gränssnittet om du skapar en ny version av gränssnittet och enhets mal len. Icke-schema delar av ett gränssnitts objekt inkluderar visnings namnet och den semantiska typen. Schema delarna av ett gränssnitts objekt som du inte kan ändra är namn, kapacitets typ och schema.

I följande avsnitt får du stegvisa exempel på hur du ändrar enhetsspecifika i IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Anpassa enhets mal len utan versions hantering

Vissa delar av enhetens funktioner kan redige ras utan att du behöver version av enhets mal len och gränssnitten. Till exempel innehåller vissa av dessa fält visnings namn, semantisk typ, minimalt värde, högsta värde, decimaler, färg, enhet, visnings enhet, kommentar och beskrivning. För att lägga till en av dessa anpassningar:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du vill anpassa.
1. Välj fliken **Anpassa** .
1. Alla funktioner som definierats i din enhets modell visas här. Du kan redigera, Spara och använda alla dessa fält utan att behöva version av din enhets mall. Om det finns fält som du vill redigera som är skrivskyddade måste du version av enhets mal len för att ändra dem. Välj ett fält som du vill redigera och ange i alla nya värden.
1. Välj **Spara**. Dessa värden åsidosätter nu allt som ursprungligen sparades i enhets mal len och används i hela programmet.

## <a name="version-a-device-template"></a>Version en enhets mall

När du skapar en ny version av din enhets mall skapas en utkast version av mallen där enhets modellen kan redige ras. Alla publicerade gränssnitt förblir publicerade tills de är individuellt versioner. Om du vill ändra ett publicerat gränssnitt måste du först skapa en ny version av enhets mal len.

Den enda version som enhets mal len är när du försöker redigera en del av enhets modellen som du inte kan redigera i avsnittet anpassningar.

Till version av en enhets mall:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du försöker att köra en version av.
1. Välj knappen **version** överst på sidan och ge mallen ett nytt namn. IoT Central föreslår ett nytt namn som du kan redigera.
1. Välj **Skapa**.
1. Nu är din enhets mall i utkast läge. Du kan se att gränssnitten fortfarande är låsta. Versions gränssnitt som du vill ändra.

## <a name="version-an-interface"></a>Version ett gränssnitt

Med versions hanterings gränssnittet kan du lägga till, uppdatera och ta bort funktionerna i gränssnittet som du redan har skapat.

Till version ett gränssnitt:

1. Gå till **Device templates** -sidan.
1. Välj den enhets mall som du har i utkast läge.
1. Välj det gränssnitt som är i publicerat läge som du vill använda som version och redigera.
1. Välj knappen **version** överst på gränssnitts sidan.
1. Välj **Skapa**.
1. Nu är ditt gränssnitt i utkast läge. Du kan lägga till eller redigera funktioner i gränssnittet utan att behöva bryta befintliga anpassningar och vyer.

## <a name="migrate-a-device-across-versions"></a>Migrera en enhet över versioner

Du kan skapa flera versioner av enhets mal len. Med tiden har du flera anslutna enheter som använder dessa enhetsspecifika mallar. Du kan migrera enheter från en version av din enhets mall till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till **Enheter**-sidan.
1. Välj den enhet som du vill migrera till en annan version.
1. Välj **migrera**:  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Välj alternativet för att börja migrera en enhet":::
1. Välj enhets mal len med det versions nummer som du vill migrera enheten till och välj **migrera**.

## <a name="next-steps"></a>Nästa steg

Om du är en operatör eller Solution Builder är ett föreslaget nästa steg att lära dig [hur du hanterar dina enheter](./howto-manage-devices.md).

Om du är en enhets utvecklare är ett förslag till nästa steg att läsa om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md).
