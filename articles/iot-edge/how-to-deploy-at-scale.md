---
title: Distribuera moduler i skala i Azure Portal-Azure IoT Edge
description: Använd Azure Portal för att skapa automatiska distributioner för grupper av IoT Edge enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: db27a466ca5f1370e8b43ceb472f5deeaba509f1
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200322"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera IoT Edge moduler i skala med hjälp av Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Skapa en **IoT Edge automatisk distribution** i Azure Portal för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](../iot-hub/iot-hub-automatic-device-management.md) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifiera enheter som använder Taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enheten med dubbla. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning.

Om du till exempel hanterar ett campus-nätverk kan du lägga till plats, rums typ och miljö etiketter på en enhet:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Mer information om enhets sammanflätade och taggar finns i [förstå och använda enhets uppIoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

IoT Edge tillhandahåller två olika typer av automatiska distributioner som du kan använda för att anpassa ditt scenario. Du kan skapa en standard *distribution*, som innehåller system runtime-moduler och eventuella ytterligare moduler och vägar. Varje enhet kan bara använda en distribution. Du kan också skapa en *lager distribution* som bara innehåller anpassade moduler och vägar, inte systemets körnings miljö. Många lager distributioner kan kombineras på en enhet, ovanpå en standard distribution. Mer information om hur de två typerna av automatisk distribution fungerar tillsammans finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Stegen för att skapa en distribution och en lager distribution är mycket lika. Eventuella skillnader anropas i följande steg.

1. I [Azure Portal](https://portal.azure.com)går du till din IoT Hub.
1. I menyn i den vänstra rutan väljer du **IoT Edge** under **Automatisk enhets hantering**.
1. I den övre stapeln väljer du **skapa distribution** eller **skapa lager distribution**.

Det finns fem steg för att skapa en distribution. Följande avsnitt beskriver var och en.

>[!NOTE]
>Stegen i den här artikeln visar den senaste schema versionen av IoT Edge agent och hubb. Schema version 1,1 släpptes tillsammans med IoT Edge version 1.0.10, och aktiverar modulens start ordning och väg prioritets funktioner.
>
>Om du distribuerar till en enhet som kör version 1.0.9 eller tidigare kan du redigera **körnings inställningarna** i **moduler** -steget i guiden för att använda schema version 1,0.

### <a name="step-1-name-and-label"></a>Steg 1: namn och etikett

1. Ge din distribution ett unikt namn som består av upp till 128 små bokstäver. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` .
1. Du kan lägga till etiketter som nyckel/värde-par som hjälper dig att spåra dina distributioner. Till exempel **HostPlatform** och **Linux**, eller **version** och **3.0.1**.
1. Välj **Nästa: moduler** för att gå vidare till steg två.

### <a name="step-2-modules"></a>Steg 2: moduler

Du kan lägga till upp till 50 moduler i en distribution. Om du skapar en distribution utan moduler tas alla aktuella moduler bort från mål enheterna.

I distributioner kan du hantera inställningarna för IoT Edge agent och IoT Edge Hub-moduler. Välj **körnings inställningar** för att konfigurera de två körnings modulerna. I skiktad distribution inkluderas inte runtime-modulerna så att de inte kan konfigureras.

Följ dessa steg om du vill lägga till anpassad kod som en modul eller lägga till en Azure Service-modul manuellt:

1. I avsnittet **container Registry inställningar** på sidan anger du autentiseringsuppgifterna för åtkomst till alla privata behållar register som innehåller dina modulblad.
1. I avsnittet **IoT Edge moduler** på sidan väljer du **Lägg till**.
1. Välj en av de tre typerna av moduler på den nedrullningsbara menyn:

   * **IoT Edge modul** – du anger modulens namn och URI för behållar avbildningen. Till exempel är avbildnings-URI: n för exempel SimulatedTemperatureSensor-modulen `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Om modulens avbildning lagras i ett privat behållar register, lägger du till autentiseringsuppgifterna på den här sidan för att få åtkomst till avbildningen.
   * **Marketplace-modul** – moduler som finns på Azure Marketplace. Vissa Marketplace-moduler kräver ytterligare konfiguration, så granska informationen i modulen i listan med [IoT Edge moduler för Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure Stream Analytics modul** – moduler som genereras från en Azure Stream Analytics arbets belastning.

1. Om det behövs upprepar du steg 2 och 3 för att lägga till ytterligare moduler i distributionen.

När du har lagt till en modul i en distribution kan du välja dess namn för att öppna sidan **uppdatera IoT Edge-modul** . På den här sidan kan du redigera inställningarna för modulen, miljövariabler, skapa alternativ, start ordning och modul. Om du har lagt till en modul från Marketplace kanske den redan har en del av de här parametrarna ifyllda. Mer information om de tillgängliga inställningarna för modulen finns i [konfiguration och hantering av moduler](module-composition.md#module-configuration-and-management).

Om du skapar en lager distribution kan du konfigurera en modul som finns i andra distributioner som riktar sig mot samma enheter. Om du vill uppdatera modulen dubbla utan att skriva över andra versioner öppnar du fliken med **dubbla inställningar** . Skapa en ny **modul med dubbla** egenskaper med ett unikt namn för ett underavsnitt i modulens två önskade egenskaper, till exempel `properties.desired.settings` . Om du definierar egenskaper inom bara `properties.desired` fältet skrivs de önskade egenskaperna för modulen som definierats i eventuella distributioner med lägre prioritet.

![Ställ in modul, delad egenskap för lager distribution](./media/how-to-deploy-monitor/module-twin-property.png)

Mer information om modulens dubbla konfiguration i lager distributioner finns i [lager distribution](module-deployment-monitoring.md#layered-deployment).

När du har konfigurerat alla moduler för en konfigurerad distribution väljer du **Nästa: vägar** för att gå vidare till steg tre.

### <a name="step-3-routes"></a>Steg 3: vägar

På fliken **vägar** definierar du hur meddelanden ska skickas mellan moduler och IoT Hub. Meddelanden skapas med hjälp av namn/värde-par.

Till exempel skulle en väg med en namn **väg** och ett värde **från/Messages/ \* till $upstream** ta emot meddelanden från alla moduler och skicka dem till din IoT-hubb.  

Parametrarna för **prioritet** och **tid till Live** är valfria parametrar som du kan ta med i en flödes definition. Med prioritets parametern kan du välja vilka vägar som ska bearbetas först eller vilka vägar som ska bearbetas sist. Prioriteten bestäms genom att ange ett nummer 0-9, där 0 är högsta prioritet. Med parametern Time to Live kan du deklarera hur länge meddelanden i den vägen ska vara kvar tills de bearbetas eller tas bort från kön.

Mer information om hur du skapar vägar finns i [deklarera vägar](module-composition.md#declare-routes).

Välj **Nästa: mått**.

### <a name="step-4-metrics"></a>Steg 4: mått

Mått tillhandahåller sammanfattande antal i de olika tillstånd som en enhet kan rapportera tillbaka till följd av att konfigurations innehåll tillämpas.

1. Ange ett namn för **måttets namn**.

1. Ange en fråga för **mått villkor**. Frågan baseras på IoT Edge Hub-modulens dubbla [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Måttet representerar antalet rader som returneras av frågan.

   Exempel:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Välj **Nästa: mål enheter**.

### <a name="step-5-target-devices"></a>Steg 5: mål enheter

Använd egenskapen taggar från dina enheter för att rikta in de specifika enheter som ska få den här distributionen.

Eftersom flera distributioner kan vara riktade till samma enhet bör du ge varje distribution ett prioritets nummer. Om det finns en konflikt är distributionen med högst prioritet (större värden anger högre prioritet) WINS. Om två distributioner har samma prioritets nummer, det som skapades senast.

Om flera distributioner riktar sig till samma enhet används bara den som har den högre prioriteten. Om flera lager distributioner riktar sig till samma enhet används alla. Men om några av egenskaperna dupliceras, som om det finns två vägar med samma namn, skrivs resten av med en lager med högre prioritet.

Alla lager distributioner riktade till en enhet måste ha en högre prioritet än den grundläggande distributionen för att kunna tillämpas.

1. Ange ett positivt heltal för distributions **prioriteten**.
1. Ange ett **mål villkor** för att avgöra vilka enheter som ska vara mål för distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Exempel: `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`.

Välj **Nästa: granska + skapa** för att gå vidare till det sista steget.

### <a name="step-6-review-and-create"></a>Steg 6: granska och skapa

Granska distributions informationen och välj sedan **skapa**.

Information om hur du övervakar distributionen finns i [övervaka IoT Edge distributioner](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla mål enheter. Du kan ändra följande inställningar och funktioner för en befintlig distribution:

* Mål villkor
* Anpassade mått
* Etiketter
* Taggar
* Önskade egenskaper

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Ändra mål villkor, anpassade mått och etiketter

1. I din IoT-hubb väljer du **IoT Edge** från menyn i den vänstra rutan.
1. Välj fliken **IoT Edge distributioner** och välj sedan den distribution som du vill konfigurera.
1. Välj fliken **mål villkor** . Ändra **mål villkoret** till att rikta in de avsedda enheterna. Du kan också justera **prioriteten**.  Välj **Spara**.

    Om du uppdaterar mål villkoret inträffar följande uppdateringar:

    * Om en enhet inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här distributionen är den högsta prioriteten för den enheten, tillämpas den här distributionen på enheten.
    * Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret avinstalleras distributionen och börjar med den näst högsta prioriterade distributionen.
    * Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i det aktuella läget, men hanteras inte som en del av distributionen längre. När den uppfyller mål villkoren för en annan distribution avinstallerar den distributionen och tar den nya.

1. Välj fliken **mått** och klicka på knappen **Redigera mått** . Lägg till eller ändra anpassade mått med hjälp av exempel-syntaxen som en guide. Välj **Spara**.

    ![Redigera anpassade mått i en distribution](./media/how-to-deploy-monitor/metric-list.png)

1. Välj fliken **Etiketter** och gör önskade ändringar och välj **Spara**.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution börjar alla distribuerade enheter med sin näst högsta prioriterade distribution. Om enheterna inte uppfyller mål villkoren för någon annan distribution, tas modulerna inte bort när distributionen tas bort.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge distributioner** .

   ![Visa IoT Edge distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryss rutan för att välja den distribution som du vill ta bort.
1. Välj **Ta bort**.
1. En prompt informerar dig om att den här åtgärden tar bort den här distributionen och återgår till det tidigare läget för alla enheter. En distribution med lägre prioritet kommer att gälla. Om ingen annan distribution är avsedd tas inga moduler bort. Om du vill ta bort alla moduler från enheten skapar du en distribution med noll moduler och distribuerar den till samma enheter. Fortsätt genom att välja **Ja**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).