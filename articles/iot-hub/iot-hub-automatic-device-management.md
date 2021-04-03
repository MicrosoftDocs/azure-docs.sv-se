---
title: Automatisk enhets hantering i skala med Azure IoT Hub | Microsoft Docs
description: Använd Azure IoT Hub automatiska konfigurationer för att hantera flera IoT-enheter och moduler
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545403"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatisk hantering av IoT-enheter och -moduler med Azure-portalen

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhets hantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna i hanteringen av stora enhets flottor. Med automatisk enhets hantering kan du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och sedan låta IoT Hub uppdatera enheterna när de kommer i omfång. Den här uppdateringen görs med hjälp av en _Automatisk enhets konfiguration_ eller _automatisk konfiguration av modulen_, som gör att du kan sammanfatta, hantera sammanslagning och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhets hantering fungerar genom att uppdatera en uppsättning enheter, dubbla eller moduler, med önskade egenskaper och rapportera en sammanfattning som baseras på dubbla rapporterade egenskaper.  Den introducerar en ny klass och JSON-dokument som kallas en *konfiguration* som består av tre delar:

* **Mål villkoret** definierar omfånget för enheten, dubbla eller modulerna som ska uppdateras. Mål villkoret anges som en fråga på dubbla Taggar och/eller rapporterade egenskaper.

* **Målets innehåll** definierar önskade egenskaper som ska läggas till eller uppdateras i den riktade enheten, med dubbla eller moduler. Innehållet innehåller en sökväg till avsnittet av önskade egenskaper som ska ändras.

* **Måtten** definierar antalet olika konfigurations tillstånd, till exempel **lyckad**, **pågående** och **fel**. Anpassade mått anges som frågor på dubbla rapporterade egenskaper.  System mått är standard mått som mäter dubbel uppdaterings status, t. ex. antalet värden som är riktade till varandra och antalet dubbla objekt som har uppdaterats.

Automatiska konfigurationer körs för första gången strax efter att konfigurationen har skapats och sedan var femte minut. Mått frågor körs varje gången den automatiska konfigurationen körs.

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

## <a name="create-a-configuration"></a>Skapa en konfiguration

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT-enhetens konfiguration**.

3. Välj **Lägg till enhets konfiguration** eller **Lägg till modul konfiguration**.

   ![Lägg till enhets konfiguration eller konfiguration av modul](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Det finns fem steg för att skapa en konfiguration. Följande avsnitt beskriver var och en. 

### <a name="name-and-label"></a>Namn och etikett

1. Ge konfigurationen ett unikt namn som är upp till 128 gemener. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` .

2. Lägg till etiketter som hjälper dig att spåra dina konfigurationer. Etiketter är **namn**, **värdepar** som beskriver konfigurationen. Exempel: `HostPlatform, Linux` eller `Version, 3.0.1`.

3. Välj **Nästa** för att gå vidare till nästa steg. 

### <a name="specify-settings"></a>Ange inställningar

I det här avsnittet definieras det innehåll som ska ställas in i den riktade enheten eller modulen. Det finns två indata för varje uppsättning inställningar. Den första är den dubbla sökvägen, som är sökvägen till JSON-avsnittet i de dubbla önskade egenskaperna som ska anges.  Den andra är det JSON-innehåll som ska infogas i avsnittet. 

Du kan till exempel ange den dubbla sökvägen till `properties.desired.chiller-water` och sedan ange följande JSON-innehåll: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Ange den dubbla sökvägen och innehållet](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Du kan också ange enskilda inställningar genom att ange hela den dubbla sökvägen och ange värdet utan hakparenteser. Till exempel, med den dubbla sökvägen `properties.desired.chiller-water.temperature` , anger du innehållet till `66` . Skapa sedan en ny, dubbel inställning för egenskapen tryck. 

Om två eller flera konfigurationer riktar sig till samma dubbla sökväg gäller innehållet från den högsta prioritets konfigurationen (prioritet definieras i steg 4).

Om du vill ta bort en befintlig egenskap anger du egenskap svärdet till `null` .

Du kan lägga till ytterligare inställningar genom att välja **Lägg till enhet, konfigurera** eller **lägga till en modul med dubbla inställningar**.

### <a name="specify-metrics-optional"></a>Ange mått (valfritt)

Mått tillhandahåller sammanfattande antal av de olika tillstånd som en enhet eller modul kan rapportera tillbaka efter att ha använt konfigurations innehåll. Du kan till exempel skapa ett mått för väntande inställnings ändringar, ett mått för fel och ett mått för lyckade inställningar.

Varje konfiguration kan ha upp till fem anpassade mått. 

1. Ange ett namn för **måttets namn**.

2. Ange en fråga för **mått villkor**.  Frågan baseras på enhetens dubbla rapporterade egenskaper.  Måttet representerar antalet rader som returneras av frågan.

Exempel:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Du kan inkludera en sats som konfigurationen tillämpade, till exempel: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Om du skapar ett mått för att rapportera om konfigurerade moduler väljer du `moduleId` från `devices.modules` . Exempel:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Målenheter

Använd egenskapen taggar från dina enheter för att rikta in dig på specifika enheter eller moduler som ska ta emot den här konfigurationen. Du kan också ange dubbla rapporterade egenskaper.

Automatisk enhets konfiguration kan bara användas för att rikta in enhets dubbla taggar, och automatiska webbplatskonfigurationer kan bara användas med dubbla Taggar i modulen. 

Eftersom flera konfigurationer kan vara riktade till samma enhet eller modul, behöver varje konfiguration ett prioritets nummer. Om det skulle finnas en konflikt, är konfigurationen med högst prioritet WINS. 

1. Ange ett positivt heltal för konfigurations **prioriteten**. Det högsta numeriska värdet anses vara högsta prioritet. Om två konfigurationer har samma prioritets nummer, den som skapades senast. 

2. Ange ett **mål villkor** för att avgöra vilka enheter eller moduler som ska användas med den här konfigurationen. Villkoret baseras på dubbla taggar eller dubbla rapporterade egenskaper och ska matcha uttrycks formatet. 

   För automatisk enhets konfiguration kan du ange bara taggen eller den rapporterade egenskapen till mål. Exempel: `tags.environment='test'` eller `properties.reported.chillerProperties.model='4000x'`. Du kan ange `*` att alla enheter ska vara riktade. 
   
   För automatisk konfiguration av module använder du en fråga för att ange taggar eller rapporterade egenskaper från modulerna som är registrerade i IoT Hub. Exempel: `from devices.modules where tags.environment='test'` eller `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Jokertecken kan inte användas för att rikta alla moduler. 

3. Välj **Nästa** för att gå vidare till det sista steget.

### <a name="review-configuration"></a>Granska konfigurationen

Granska konfigurations informationen och välj sedan **Skicka**.

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande steg för att visa information om en konfiguration och övervaka de enheter som kör den:

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT-enhetens konfiguration**.

3. Granska konfigurations listan. Du kan visa följande information för varje konfiguration:

   * **ID** – namnet på konfigurationen.

   * **Mål villkor** – den fråga som används för att definiera riktade enheter eller moduler.

   * **Prioritet** – prioritets numret som tilldelats konfigurationen.

   * **Skapelse tid** : tidsstämpeln från när konfigurationen skapades. Den här tidsstämpeln används för att avbryta relationer när två konfigurationer har samma prioritet. 

   * **System mått** – mått som beräknas av IoT Hub och inte kan anpassas av utvecklare. Mål anger antalet dubbla enheter som matchar mål villkoret. Använder det angivna antalet enheter som har ändrats av konfigurationen, vilket kan inkludera partiella ändringar i händelsen om en separat, högre prioritets konfiguration också har ändrats. 

   * **Anpassade mått** – mått som har angetts av utvecklaren som frågor mot dubbla rapporterade egenskaper.  Upp till fem anpassade mått kan definieras per konfiguration. 
   
4. Välj den konfiguration som du vill övervaka.  

5. Granska konfigurations informationen. Du kan använda flikar för att visa detaljerad information om de enheter som har tagit emot konfigurationen.

   * **Mål villkor** – de enheter eller moduler som matchar mål villkoret. 

   * **Mått** – en lista över system mått och anpassade mått.  Du kan visa en lista över enheter eller moduler som räknas för varje mått genom att välja måttet i list rutan och sedan välja **Visa enheter** eller **Visa moduler**.

   * **Enhetens dubbla inställningar** eller **modul, dubbla** inställningar – de dubbla inställningar som anges av konfigurationen. 

   * **Konfigurations etiketter** – nyckel/värde-par som används för att beskriva en konfiguration.  Etiketter har ingen påverkan på funktioner. 

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration replikeras ändringarna omedelbart till alla riktade enheter eller moduler. 

Om du uppdaterar mål villkoret inträffar följande uppdateringar:

* Om en grupp inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här konfigurationen är den högsta prioriteten för den dubbla, används den här konfigurationen. 

* Om en dubbel som kör den här konfigurationen inte längre uppfyller mål villkoret, kommer inställningarna från konfigurationen att tas bort och den dubbla kommer att ändras av den näst högsta prioritets konfigurationen. 

* Om en dubbel som kör den här konfigurationen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för några andra konfigurationer, tas inställningarna från konfigurationen bort och inga andra ändringar görs på den dubbla. 

Använd följande steg för att ändra en konfiguration: 

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT-enhetens konfiguration**. 

3. Välj den konfiguration som du vill ändra. 

4. Gör uppdateringar av följande fält: 

   * Mål villkor 
   * Etiketter 
   * Prioritet 
   * Mått

4. Välj **Spara**.

5. Följ stegen i [övervaka en konfiguration](#monitor-a-configuration) för att se att ändringarna distribueras. 

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration tar alla enheter med den näst högsta prioritets konfigurationen. Om enheten är flätad inte uppfyller mål villkoret för någon annan konfiguration, tillämpas inga andra inställningar. 

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT-enhetens konfiguration**. 

3. Använd kryss rutan för att välja den konfiguration som du vill ta bort. 

4. Välj **Ta bort**.

5. Du uppmanas att bekräfta genom att bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar och övervakar IoT-enheter i stor skala. Följ dessa länkar om du vill veta mer om hur du hanterar Azure-IoT Hub:

* [Hantera dina IoT Hub-enhetsidentiteter i grupp](iot-hub-bulk-identity-mgmt.md)
* [Övervaka din IoT Hub](monitor-iot-hub.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)

Om du vill utforska med hjälp av IoT Hub Device Provisioning Service för att aktivera Zero-Touch, just-in-Time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](../iot-dps/index.yml)