---
title: Förstå versionshantering av enhetsmallar för dina Azure IoT Central-| Microsoft Docs
description: Iterera över dina enhetsmallar genom att skapa nya versioner och utan att påverka dina liveanslutna enheter
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715054"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny enhetsmallversion

*Den här artikeln gäller för lösningsbyggare och enhetsutvecklare.*

En enhetsmall innehåller ett schema som beskriver hur en enhet interagerar med IoT Central. Dessa interaktioner omfattar telemetri, egenskaper och kommandon. Både enheten och IoT Central förlitar sig på delad förståelse av det här schemat för att utbyta information. Du kan bara göra begränsade ändringar i schemat utan att bryta kontraktet. Därför kräver de flesta schemaändringar en ny version av enhetsmallen. Med versionshantering av enhetsmallen kan äldre enheter fortsätta med den schemaversion som de förstår, medan nyare eller uppdaterade enheter använder en senare schemaversion.

Schemat i en enhetsmall definieras i enhetsmodellen och dess gränssnitt. Enhetsmallar innehåller annan information, till exempel molnegenskaper, visningsanpassningar och vyer. Om du gör ändringar i de delar av enhetsmallen som inte definierar hur enheten utbyter data med IoT Central behöver du inte versionsversionsmallen.

Du måste alltid publicera en uppdaterad enhetsmall innan en operatör kan använda den. IoT Central förhindrar att du publicerar större ändringar i en enhetsmall utan att först skapa en version av mallen.

> [!NOTE]
> Mer information om hur du skapar en enhetsmall finns [i Konfigurera och hantera en enhetsmall](howto-set-up-template.md)

## <a name="versioning-rules"></a>Versionshanteringsregler

I det här avsnittet sammanfattas de versionsregler som gäller för enhetsmallar. Både enhetsmodeller och gränssnitt har versionsnummer. I följande kodfragment visas enhetsmodellen för en termostatenhet. Enhetsmodellen har ett enda gränssnitt. Du kan se versionsnumret i slutet av `@id` fältet.

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

Om du vill visa den här informationen IoT Central användargränssnittet väljer du **Visa identitet i** redigeraren för enhetsmallen:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Visa identiteten för ett gränssnitt för att se versionsnumret":::

I följande lista visas de regler som avgör när du måste skapa en ny version:

* När en enhetsmodell har publicerats kan du inte ta bort några gränssnitt, inte ens i en ny version av enhetsmallen.
* När en enhetsmodell har publicerats kan du lägga till ett gränssnitt om du skapar en ny version av enhetsmallen.
* När en enhetsmodell har publicerats kan du ersätta ett gränssnitt med en nyare version om du skapar en ny version av enhetsmallen. Om enhetsmallen sensor v1 till exempel använder termostatens v1-gränssnitt kan du skapa en sensor v2-enhetsmall som använder termostat v2-gränssnittet.
* När ett gränssnitt har publicerats kan du inte ta bort något av gränssnittsinnehållet, inte ens i en ny version av enhetsmallen.
* När ett gränssnitt har publicerats kan du lägga till objekt i innehållet i ett gränssnitt om du skapar en ny version av gränssnittet och enhetsmallen. Objekt som du kan lägga till i gränssnittet inkluderar telemetri, egenskaper och kommandon.
* När ett gränssnitt har publicerats kan du göra icke-schemaändringar av befintliga objekt i gränssnittet om du skapar en ny version av gränssnittet och enhetsmallen. Delar av ett gränssnittsobjekt som inte är schemadelar innehåller visningsnamnet och semantiktypen. Schemadelarna för ett gränssnittsobjekt som du inte kan ändra är namn, kapacitetstyp och schema.

I följande avsnitt går vi igenom några exempel på hur du ändrar enhetsmallar i IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Anpassa enhetsmallen utan versionshantering

Vissa element i dina enhetsfunktioner kan redigeras utan att du behöver skapa en version av enhetsmallen och gränssnitten. Några av dessa fält är till exempel visningsnamn, semantisk typ, minimivärde, maxvärde, decimaler, färg, enhet, visningsenhet, kommentar och beskrivning. Så här lägger du till en av dessa anpassningar:

1. Gå till **sidan Enhetsmallar.**
1. Välj den enhetsmall som du vill anpassa.
1. Välj **fliken** Anpassa.
1. Alla funktioner som definieras i din enhetsmodell visas här. Du kan redigera, spara och använda alla dessa fält utan att behöva versionsversionen av enhetsmallen. Om det finns fält som du vill redigera som är skrivskyddade måste du skapa en version av enhetsmallen för att ändra dem. Välj ett fält som du vill redigera och ange eventuella nya värden.
1. Välj **Spara**. Nu åsidosätter dessa värden allt som ursprungligen sparades i enhetsmallen och som används i hela programmet.

## <a name="version-a-device-template"></a>Versionsversion för en enhetsmall

När du skapar en ny version av enhetsmallen skapas ett utkast av mallen där enhetsmodellen kan redigeras. Alla publicerade gränssnitt förblir publicerade tills de har versionerna individuellt. Om du vill ändra ett publicerat gränssnitt skapar du först en ny version av enhetsmallen.

Versionsredigera endast enhetsmallen när du försöker redigera en del av enhetsmodellen som du inte kan redigera i avsnittet om anpassningar.

Versionsversion av en enhetsmall:

1. Gå till **sidan Enhetsmallar.**
1. Välj den enhetsmall som du försöker versions versionera.
1. Välj knappen **Version** överst på sidan och ge mallen ett nytt namn. IoT Central föreslår ett nytt namn som du kan redigera.
1. Välj **Skapa**.
1. Nu är enhetsmallen i utkastläge. Du kan se att dina gränssnitt fortfarande är låsta. Versionsversion för alla gränssnitt som du vill ändra.

## <a name="version-an-interface"></a>Version ett gränssnitt

Med versionshantering av ett gränssnitt kan du lägga till och uppdatera funktioner i det gränssnitt som du redan har skapat.

Så här versionsversionar du ett gränssnitt:

1. Gå till **sidan Enhetsmallar.**
1. Välj den enhetsmall som du har i utkastläge.
1. Välj det gränssnitt som är i publicerat läge som du vill versionsversiona och redigera.
1. Välj **knappen Version** längst upp på gränssnittssidan.
1. Välj **Skapa**.
1. Nu är gränssnittet i utkastläge. Du kan lägga till eller redigera funktioner i ditt gränssnitt utan att bryta befintliga anpassningar och vyer.

## <a name="migrate-a-device-across-versions"></a>Migrera en enhet mellan versioner

Du kan skapa flera versioner av enhetsmallen. Med tiden har du flera anslutna enheter som använder dessa enhetsmallar. Du kan migrera enheter från en version av enhetsmallen till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till **Enheter**-sidan.
1. Välj den enhet som du behöver migrera till en annan version.
1. Välj **Migrera:**  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Välj alternativet för att börja migrera en enhet":::
1. Välj enhetsmallen med det versionsnummer som du vill migrera enheten till och välj **Migrera**.

## <a name="next-steps"></a>Nästa steg

Om du är operatör eller lösningsbyggare är ett förslag på nästa steg att lära [dig hur du hanterar dina enheter.](./howto-manage-devices.md)

Om du är enhetsutvecklare är ett förslag på nästa steg att läsa om Azure IoT Edge [enheter och Azure IoT Central](./concepts-iot-edge.md).
