---
title: Automatisk distribution för enhets grupper – Azure IoT Edge | Microsoft Docs
description: Använd automatiska distributioner i Azure IoT Edge för att hantera grupper av enheter baserat på delade Taggar
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3989ec4ca2b5c9d7385841604678791b20c1d102
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489990"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Automatiska distributioner och lager distribution hjälper dig att hantera och konfigurera moduler på ett stort antal IoT Edge enheter.

Azure IoT Edge tillhandahåller två sätt att konfigurera modulerna så att de körs på IoT Edge enheter. Den första metoden är att distribuera moduler per enhet. Du skapar ett distributions manifest och tillämpar det på en viss enhet efter namn. Den andra metoden är att distribuera moduler automatiskt till alla registrerade enheter som uppfyller en uppsättning definierade villkor. Du skapar ett distributions manifest och definierar sedan vilka enheter den gäller baserat på [taggar](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) i enheten.

Den här artikeln fokuserar på att konfigurera och övervaka flotta av enheter, gemensamt kallade *IoT Edge automatiska distributioner*. De grundläggande distributions stegen är följande:

1. En operator definierar en distribution som beskriver en uppsättning moduler och mål enheter. Varje distribution har ett distributions manifest som motsvarar den här informationen.
2. Tjänsten IoT Hub kommunicerar med alla riktade enheter för att konfigurera dem med de deklarerade modulerna.
3. Tjänsten IoT Hub hämtar status från IoT Edge enheter och gör dem tillgängliga för operatören.  En operatör kan till exempel se när en Edge-enhet inte har kon figurer ATS korrekt eller om en modul Miss lyckas under körningen.
4. När som helst har nya IoT Edge enheter som uppfyller mål villkoren kon figurer ATS för distributionen.

I den här artikeln beskrivs varje komponent som ingår i att konfigurera och övervaka en distribution. En genom gång av hur du skapar och uppdaterar en-distribution finns i [distribuera och övervaka IoT Edge moduler i skala](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Distribution

En IoT Edge automatisk distribution tilldelar IoT Edge modul avbildningar som ska köras som instanser på en mål uppsättning IoT Edge enheter. Det fungerar genom att konfigurera ett IoT Edge distributions manifest som innehåller en lista med moduler med motsvarande initierings parametrar. En distribution kan tilldelas en enda enhet (baserat på enhets-ID) eller till en grupp av enheter (baserat på taggar). När en IoT Edge enhet tar emot ett distributions manifest laddas och installeras behållar avbildningarna från respektive behållares databaser och konfigurerar dem därefter. När en distribution har skapats kan en operatör övervaka distributions statusen för att se om de riktade enheterna är korrekt konfigurerade.

Endast IoT Edge enheter kan konfigureras med en distribution. Följande krav måste finnas på enheten innan den kan ta emot distributionen:

* Det grundläggande operativ systemet
* Ett hanterings system för behållare som Moby eller Docker
* Etablering av IoT Edge runtime

### <a name="deployment-manifest"></a>Distributionsmanifest

Ett distributions manifest är ett JSON-dokument som beskriver de moduler som ska konfigureras på mål IoT Edge enheter. Den innehåller konfigurations-metadata för alla moduler, inklusive nödvändiga systemmoduler (särskilt IoT Edge-agenten och IoT Edge Hub).  

Konfigurationens metadata för varje modul innehåller:

* Version
* Typ
* Status (till exempel igång eller stoppad)
* Starta om princip
* Avbildning och container Registry
* Vägar för indata och utdata

Om module-avbildningen lagras i ett privat behållar register, innehåller IoT Edge-agenten autentiseringsuppgifterna för registret.

### <a name="target-condition"></a>Mål villkor

Mål villkoret utvärderas kontinuerligt under hela livs längden för distributionen. Alla nya enheter som uppfyller kraven ingår och alla befintliga enheter som inte längre har tagits bort. Distributionen återaktiveras om tjänsten identifierar mål villkors ändringar.

Du kan till exempel ha en distribution med en Target condition-taggar. miljö = ' Prod '. När du startar distributionen finns det 10 produktions enheter. Modulerna har installerats på de här 10 enheterna. IoT Edge agent status visar totalt 10 enheter, 10 lyckade svar, 0 felsvar och 0 väntande svar. Nu lägger du till fem fler enheter med taggar. miljö = ' Prod '. Tjänsten identifierar ändringen och IoT Edge agentens status blir 15 enheter, 10 lyckade svar, 0 felsvar och 5 väntande svar när den distribueras till de fem nya enheterna.

Använd booleska villkor på enhetens dubbla taggar, enhetens dubbla rapporterade egenskaper eller deviceId för att välja mål enheter. Om du vill använda villkor med taggar måste du lägga till avsnittet "Taggar": {} i enheten i dubbla under samma nivå som egenskaper. [Lär dig mer om taggar i enhets dubbla](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på mål villkor:

* deviceId = ' linuxprod1 '
* Taggar. miljö = ' Prod '
* Taggar. Environment = ' Prod ' och taggar. location = ' väst '
* Taggar. Environment = ' Prod ' eller taggar. location = ' väst '
* Taggar. operator = ' John ' och taggar. miljö = ' Prod ' och inte deviceId = ' linuxprod1 '
* egenskaper. redevicemodel = ' 4000x '

Tänk på följande begränsningar när du skapar ett mål villkor:

* I enhet, kan du bara bygga ett mål villkor med hjälp av taggar, rapporterade egenskaper eller deviceId.
* Dubbla citat tecken tillåts inte i någon del av mål villkoret. Använd enkla citat tecken.
* Enkla citat tecken representerar värdena för mål villkoret. Därför måste du undanta det enkla citatet med ett annat enkelt citat tecken om det är en del av enhets namnet. Till exempel för att rikta in en enhet som kallas `operator'sDevice` , skriva `deviceId='operator''sDevice'` .
* Siffror, bokstäver och följande tecken tillåts i mål villkors värden: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en mål enhet i förhållande till andra distributioner. En distributions prioritet är ett positivt heltal, med större tal som anger högre prioritet. Om en IoT Edge enhet är riktad mot fler än en distribution gäller distributionen med högst prioritet.  Distributioner med lägre prioritet tillämpas inte och slås inte samman.  Om en enhet är riktad mot två eller flera distributioner med samma prioritet, gäller den senast skapade distributionen (bestäms av tids stämplingen för skapandet).

### <a name="labels"></a>Etiketter

Etiketter är sträng nyckel/värdepar som du kan använda för att filtrera och gruppera distributioner. En distribution kan ha flera etiketter. Etiketter är valfria och påverkar inte den faktiska konfigurationen för IoT Edge enheter.

### <a name="metrics"></a>Mått

Som standard rapporterar alla distributioner på fyra mått:

* **Riktad** visar de IoT Edge enheter som matchar villkoret för distributions målet.
* **Tillämpad** visar riktade IoT Edge enheter som inte är riktade till en annan distribution med högre prioritet.
* **Rapporten lyckades** visar de IoT Edge enheter som har rapporterat att modulerna har distribuerats korrekt.
* **Rapporterings fel** visar de IoT Edge enheter som har rapporterat att en eller flera moduler inte har distribuerats. Du kan undersöka felet genom att fjärrans luta till dessa enheter och Visa loggfilerna.

Du kan också definiera egna anpassade mått som hjälper dig att övervaka och hantera distributionen.

Mått tillhandahåller sammanfattande antal i de olika tillstånd som enheterna kan rapportera tillbaka till följd av att en distributions konfiguration har tillämpats. Mått kan fråga [edgeHub-modulens dubbla rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties), t. ex. *lastDesiredStatus* eller *lastConnectTime*. Exempel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Att lägga till egna mått är valfritt och påverkar inte den faktiska konfigurationen för IoT Edge enheter.

## <a name="layered-deployment"></a>Lager distribution

Lager distributioner är automatiska distributioner som kan kombineras tillsammans för att minska antalet unika distributioner som behöver skapas. Lager distributioner är användbara i scenarier där samma moduler återanvänds i olika kombinationer i många automatiska distributioner.

Lager distributioner har samma grundläggande komponenter som vilken automatisk distribution som helst. De riktar in enheter baserat på taggar i enheten är dubbla och ger samma funktioner kring etiketter, mått och status rapportering. Lager distributioner har också tilldelade prioriteter, men i stället för att använda prioritet för att avgöra vilken distribution som tillämpas på en enhet, avgör prioriteten hur flera distributioner rangordnas på en enhet. Om två lager distributioner till exempel har en modul eller en väg med samma namn, tillämpas den skiktade distributionen med högre prioritet medan den lägre prioriteten skrivs över.

System runtime-modulerna, edgeAgent och edgeHub är inte konfigurerade som en del av en lager distribution. Alla IoT Edge enheter som är riktade mot en lager distribution måste först använda en automatisk standard distribution. Den automatiska distributionen tillhandahåller den bas där lager distributioner kan läggas till.

En IoT Edge enhet kan bara använda en och endast en automatisk automatisk distribution, men den kan tillämpa flera lager automatiska distributioner. Alla lager distributioner som riktas mot en enhet måste ha en högre prioritet än den automatiska distributionen av enheten.

Anta till exempel följande scenario för ett företag som hanterar byggnader. De utvecklade IoT Edge modulerna för att samla in data från säkerhets kameror, rörelse sensorer och hissar. Alla deras byggnader kan dock inte använda alla tre modulerna. Med standard automatiska distributioner måste företaget skapa enskilda distributioner för alla kombinationer av moduler som deras byggnader behöver.

![Vanliga automatiska distributioner måste anpassas till alla moduler](./media/module-deployment-monitoring/standard-deployment.png)

Men när företaget växlar till lager automatiska distributioner upptäcker de att de kan skapa samma kombinationer för sina byggnader med färre distributioner som ska hanteras. Varje modul har sin egen lager distribution och enhets taggarna identifierar vilka moduler som läggs till i varje byggnad.

![Lager automatiska distributioner fören klar scenarier där samma moduler kombineras på olika sätt](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Modul, dubbel konfiguration

När du arbetar med lager distributioner kan du, avsiktligt eller på annat sätt, ha två distributioner med samma modul som är riktad mot en enhet. I dessa fall kan du bestämma om distributionen av högre prioritet ska skriva över modulen med dubbla eller lägga till den. Du kan till exempel ha en distribution som använder samma modul för att 100 olika enheter. Men 10 av enheterna finns på säkra anläggningar och behöver ytterligare konfiguration för att kunna kommunicera via proxyservrar. Du kan använda en lager distribution för att lägga till modulens dubbla egenskaper som gör det möjligt för de 10 enheterna att kommunicera säkert utan att skriva över den befintliga modulen dubbel information från bas distributionen.

Du kan lägga till modul dubbla önskade egenskaper i distributions manifestet. I en standard distribution kan du lägga till egenskaper i **egenskaperna. önskat** avsnitt i modulen, i en lager distribution kan du deklarera en ny delmängd av önskade egenskaper.

I en standard distribution kan du till exempel lägga till modulen simulerad temperatur sensor med följande önskade egenskaper som instruerar den att skicka data med 5 sekunders intervall:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

I en lager distribution som är riktad mot en eller flera av samma enheter kan du lägga till en egenskap som anger att den simulerade sensorn skickar 1000 meddelanden och sedan stoppar. Du vill inte skriva över de befintliga egenskaperna, så du skapar ett nytt avsnitt i önskade egenskaper `layeredProperties` som kallas, som innehåller den nya egenskapen:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

En enhet som har båda distributioner tillämpat kommer att avspegla följande egenskaper i modulen för den simulerade temperatur sensorn:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Om du ställer in `properties.desired` modulens fält i en lager distribution skrivs de önskade egenskaperna för modulen över i alla distributioner med lägre prioritet.

## <a name="phased-rollout"></a>Stegvis distribution

En stegvis distribution är en övergripande process där en operatör distribuerar ändringar till en utvidgad uppsättning IoT Edge enheter. Målet är att göra ändringar gradvis för att minska risken för att minska storleken på stora förändringar. Automatiska distributioner hjälper till att hantera stegvisa distributioner över en flotta av IoT Edge enheter.

En stegvis distribution körs i följande faser och steg:

1. Upprätta en test miljö för IoT Edge enheter genom att etablera dem och ställa in en enhets dubbla taggar som `tag.environment='test'` . Test miljön bör spegla produktions miljön som distributionen kommer att ta slut.
2. Skapa en distribution inklusive önskade moduler och konfigurationer. Mål villkoret ska vara mål för test IoT Edge enhets miljön.
3. Verifiera den nya modul konfigurationen i test miljön.
4. Uppdatera distributionen så att den innehåller en delmängd av produktions IoT Edge enheter genom att lägga till en ny tagg till mål villkoret. Kontrol lera också att prioriteten för distributionen är högre än andra distributioner som för närvarande är riktade till enheterna
5. Kontrol lera att distributionen har slutförts på de riktade IoT-enheterna genom att Visa distributions statusen.
6. Uppdatera distributionen så att den riktas mot alla återstående produktions IoT Edge enheter.

## <a name="rollback"></a>Återställning

Distributioner kan återställas om du får fel eller fel konfiguration. Eftersom en distribution definierar den absoluta modul konfigurationen för en IoT Edge enhet måste även en ytterligare distribution vara riktad till samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Om du tar bort en distribution tas inte modulerna bort från riktade enheter. Det måste finnas en annan distribution som definierar en ny konfiguration för enheterna, även om det är en tom distribution.

Utför återställningar i följande ordning:

1. Bekräfta att en andra distribution också är riktad mot samma enhets uppsättning. Om målet för återställningen är att ta bort alla moduler bör den andra distributionen inte innehålla några moduler.
2. Ändra eller ta bort mål villkors uttrycket för den distribution som du vill återställa så att enheterna inte längre uppfyller mål villkoret.
3. Kontrol lera att återställningen lyckades genom att Visa distributions statusen.
   * Den återställda distributionen bör inte längre Visa status för de enheter som återställdes.
   * Den andra distributionen bör nu innehålla distributions status för de enheter som återställdes.

## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).
* Lär dig mer om andra IoT Edge begrepp som [IoT Edge runtime](iot-edge-runtime.md) -och [IoT Edge-moduler](iot-edge-modules.md).
