---
title: Automatisk enhets hantering i skala med Azure IoT Hub (CLI) | Microsoft Docs
description: Använd Azure IoT Hub automatiska konfigurationer för att hantera flera IoT-enheter eller moduler
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545420"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatisk hantering av IoT-enheter och -moduler med Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhets hantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna i hanteringen av stora enhets flottor. Med automatisk enhets hantering kan du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och sedan låta IoT Hub uppdatera enheterna när de kommer i omfång. Den här uppdateringen görs med hjälp av en _Automatisk enhets konfiguration_ eller _automatisk konfiguration av modulen_, som gör att du kan sammanfatta, hantera sammanslagning och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhets hantering fungerar genom att uppdatera en uppsättning enheter, dubbla eller moduler, med önskade egenskaper och rapportera en sammanfattning som baseras på dubbla rapporterade egenskaper.  Den introducerar en ny klass och JSON-dokument som kallas en *konfiguration* som består av tre delar:

* **Mål villkoret** definierar omfånget för enheten, dubbla eller modulerna som ska uppdateras. Mål villkoret anges som en fråga på enhetens dubbla Taggar och/eller rapporterade egenskaper.

* **Målets innehåll** definierar önskade egenskaper som ska läggas till eller uppdateras i den riktade enheten, med dubbla eller moduler. Innehållet innehåller en sökväg till avsnittet av önskade egenskaper som ska ändras.

* **Måtten** definierar antalet olika konfigurations tillstånd, till exempel **lyckad**, **pågående** och **fel**. Anpassade mått anges som frågor på dubbla rapporterade egenskaper.  System mått är standard mått som mäter dubbel uppdaterings status, t. ex. antalet värden som är riktade till varandra och antalet dubbla objekt som har uppdaterats.

Automatiska konfigurationer körs för första gången strax efter att konfigurationen har skapats och sedan var femte minut. Mått frågor körs varje gången den automatiska konfigurationen körs.

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 

* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Som minst måste din Azure CLI-version vara 2.0.70 eller högre. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 

* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementera dubbla

Automatisk enhets konfiguration kräver att enheten använder sig av dubbla för att synkronisera tillstånd mellan molnet och enheterna.  Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

Automatisk konfiguration av moduler kräver att modulerna är uppdelade för att synkronisera tillstånd mellan molnet och modulerna. Mer information finns i [förstå och använda modul dubbla i IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Använd taggar för att rikta in sig på dubbla

Innan du skapar en konfiguration måste du ange vilka enheter eller moduler som du vill påverka. Azure IoT Hub identifierar enheter och använder taggar i enheten, och identifierar moduler med hjälp av taggar i modulen. Varje enhet eller moduler kan ha flera taggar, och du kan definiera dem på ett sätt som passar din lösning. Om du till exempel hanterar enheter på olika platser lägger du till följande taggar på en enhet:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definiera mål innehåll och mått

Mål innehålls-och mått frågor anges som JSON-dokument som beskriver enhetens dubbla eller modulens dubbla önskade egenskaper för att ange och rapporterade egenskaper som ska mätas.  Om du vill skapa en automatisk konfiguration med Azure CLI sparar du mål innehållet och måtten lokalt som. txt-filer. Du använder fil Sök vägarna i ett senare avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande mål innehålls exempel för automatisk enhets konfiguration:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Automatisk konfiguration av moduler fungerar ungefär på samma sätt, men du är mål `moduleContent` i stället för `deviceContent` .

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Här följer exempel på mått frågor:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Mått frågor för moduler liknar även frågor för enheter, men du väljer för `moduleId` från `devices.modules` . Exempel: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Skapa en konfiguration

Du konfigurerar mål enheter genom att skapa en konfiguration som består av målets innehåll och mått. 

Använd följande kommando för att skapa en konfiguration:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-ID** – namnet på den konfiguration som ska skapas i IoT-hubben. Ge konfigurationen ett unikt namn som är upp till 128 gemener. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` .

* --**Etiketter** – Lägg till etiketter som hjälper dig att spåra konfigurationen. Etiketter är namn, värdepar som beskriver din distribution. Det kan till exempel vara `HostPlatform, Linux` eller `Version, 3.0.1`

* --**innehålls** intern JSON eller fil Sök väg till det mål innehåll som ska anges som dubbla önskade egenskaper. 

* --**hubb-namn** – namnet på den IoT-hubb där konfigurationen ska skapas. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

* --**mål villkor** – ange ett mål villkor för att avgöra vilka enheter eller moduler som ska användas med den här konfigurationen. För automatisk enhets konfiguration baseras villkoret på enhetens dubbla taggar eller enhet med dubbla önskade egenskaper och ska matcha uttrycks formatet. Exempel: `tags.environment='test'` eller `properties.desired.devicemodel='4000x'`. För automatisk konfiguration av en modul baseras villkoret på modulens dubbla taggar eller modulens dubbla önskade egenskaper.. Exempel: `from devices.modules where tags.environment='test'` eller `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**Priority** – ett positivt heltal. I händelse av att två eller flera konfigurationer är riktade till samma enhet eller modul gäller konfigurationen med det högsta numeriska värdet för prioritet.

* --**mått** – Sök väg till mått frågor. Mått tillhandahåller sammanfattande antal av de olika tillstånd som en enhet eller modul kan rapportera tillbaka efter att ha använt konfigurations innehåll. Du kan till exempel skapa ett mått för väntande inställnings ändringar, ett mått för fel och ett mått för lyckade inställningar. 

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande kommando för att visa innehållet i en konfiguration:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-ID** – namnet på konfigurationen som finns i IoT Hub.

* --**hubb-namn** – namnet på den IoT-hubb där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

Granska konfigurationen i kommando fönstret. Egenskapen **mått** visar ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** – ett system mått som anger hur många enheter som är dubblare eller moduler i IoT Hub som matchar mål villkoret.

* **appliedCount** – ett system mått anger antalet enheter eller moduler som har använt mål innehållet.

* **Ditt anpassade mått** – alla mått som du har definierat är användar mått.

Du kan visa en lista med enhets-ID: n, modul-ID: n eller objekt för varje mått med hjälp av följande kommando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-ID** – namnet på den distribution som finns i IoT Hub.

* --**Metric-ID** – namnet på det mått som du vill se listan över enhets-ID: n eller modul-ID: t för `appliedCount` .

* --**hubb-namn** – namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]` .

* --**mått-Type** -Metric-typen kan vara `system` eller `user` .  System mått är `targetedCount` och `appliedCount` . Alla andra mått är användar mått.

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration replikeras ändringarna omedelbart till alla mål enheter. 

Om du uppdaterar mål villkoret inträffar följande uppdateringar:

* Om en grupp inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här konfigurationen är den högsta prioriteten för den dubbla, används den här konfigurationen. 

* Om en dubbel som kör den här konfigurationen inte längre uppfyller mål villkoret, kommer inställningarna från konfigurationen att tas bort och den dubbla kommer att ändras av den näst högsta prioritets konfigurationen. 

* Om en dubbel som kör den här konfigurationen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för några andra konfigurationer, tas inställningarna från konfigurationen bort och inga andra ändringar görs på den dubbla. 

Använd följande kommando för att uppdatera en konfiguration:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-ID** – namnet på konfigurationen som finns i IoT Hub.

* --**hubb-namn** – namnet på den IoT-hubb där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]` .

* --**Ange** – uppdatera en egenskap i konfigurationen. Du kan uppdatera följande egenskaper:

    * targetCondition – till exempel `targetCondition=tags.location.state='Oregon'`

    * Etiketter 

    * prioritet

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration, kommer alla enheter som är dubbla eller moduler att ta med den näst högsta prioritets konfigurationen. Om det finns dubbla som inte uppfyller mål villkoren för någon annan konfiguration, tillämpas inga andra inställningar. 

Använd följande kommando för att ta bort en konfiguration:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-ID** – namnet på konfigurationen som finns i IoT Hub.

* --**hubb-namn** – namnet på den IoT-hubb där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]` .

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar och övervakar IoT-enheter i stor skala. Följ dessa länkar om du vill veta mer om hur du hanterar Azure-IoT Hub:

* [Hantera dina IoT Hub-enhetsidentiteter i grupp](iot-hub-bulk-identity-mgmt.md)
* [Övervaka din IoT Hub](monitor-iot-hub.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)

Om du vill utforska med hjälp av IoT Hub Device Provisioning Service för att aktivera Zero-Touch, just-in-Time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](../iot-dps/index.yml)