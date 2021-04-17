---
title: Hantera enheterna i ditt Azure IoT Central program | Microsoft Docs
description: Som operatör kan du lära dig hur du hanterar enheter i ditt Azure IoT Central program. Lär dig hur du hanterar enskilda enheter och massimporter och massexporter av enheterna i ditt program.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2ea75adfb7c2d990cfa543270f245113e15e4ee2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389850"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt Azure IoT Central program

I den här artikeln beskrivs hur du som operatör hanterar enheter i Azure IoT Central program. Som operatör kan du:

- Använd sidan **Enheter för** att visa, lägga till och ta bort enheter som är anslutna till Azure IoT Central program.
- Importera och exportera enheter i grupp.
- Håll en uppdaterad inventering av dina enheter.
- Håll dina enhetsmetadata uppdaterade genom att ändra värdena som lagras i enhetsegenskaperna från dina vyer.
- Kontrollera beteendet för dina enheter genom att uppdatera en inställning på en specifik enhet från dina vyer.

Information om hur du hanterar anpassade grupper av enheter finns i [Självstudie: Använda enhetsgrupper för att analysera enhettelemetri.](tutorial-use-device-groups.md)

## <a name="view-your-devices"></a>Visa dina enheter

Så här visar du en enskild enhet:

1. Välj **Enheter** i det vänstra fönstret. Här visas en lista över alla enheter och dina enhetsmallar.

1. Välj en enhetsmall.

1. I den högra rutan på sidan **Enheter visas** en lista över enheter som skapats från enhetsmallen. Välj en enskild enhet för att se enhetens enhetsinformationssida:

    ![Sidan Enhetsinformation](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Lägg till en enhet

Så här lägger du till en enhet Azure IoT Central ditt program:

1. Välj **Enheter** i det vänstra fönstret.

1. Välj den enhetsmall som du vill skapa en enhet från.

1. Välj + **Ny.**

1. Aktivera **eller inaktivera växlingsknappen** **Simulerad.**  En riktig enhet är för en fysisk enhet som du ansluter till Azure IoT Central program. En simulerad enhet har exempeldata som genereras åt dig av Azure IoT Central.

1. Välj **Skapa**.

1. Den här enheten visas nu i enhetslistan för den här mallen. Välj enheten för att se sidan med enhetsinformation som innehåller alla vyer för enheten.

## <a name="import-devices"></a>Importera enheter

Om du vill ansluta ett stort antal enheter till ditt program kan du massimporta enheter från en CSV-fil. Du hittar en CSV-exempelfil på [lagringsplatsen För Azure-exempel.](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/bulk-upload-devices) CSV-filen bör innehålla följande kolumnrubriker:

| Kolumn | Beskrivning 
| - | - | 
| IOTC_DEVICEID | Enhets-ID:t är en unik identifiering som den här enheten använder för att ansluta. Enhets-ID:t kan innehålla bokstäver, siffror `-` och tecken utan blanksteg. |
| IOTC_DEVICENAME | Valfritt. Enhetsnamnet är ett eget namn som visas i hela programmet. Om inget anges är detta samma som enhets-ID:t.   |



Massregistrera enheter i ditt program:

1. Välj **Enheter** i det vänstra fönstret.

1. På den vänstra panelen väljer du den enhetsmall som du vill mass skapa enheterna för.

    > [!NOTE]
    > Om du inte har någon enhetsmall ännu kan du importera enheter under **Alla enheter och** registrera dem utan en mall. När enheterna har importerats kan du migrera dem till en mall.

1. Välj **Importera**.

    ![Importåtgärd](./media/howto-manage-devices/bulkimport1a.png)


1. Välj den CSV-fil som innehåller listan över enhets-ID:er som ska importeras.

1. Enhetsimporten startar när filen har laddats upp. Du kan spåra importstatusen på panelen Enhetsåtgärder. Den här panelen visas automatiskt när importen startar eller så kan du komma åt den via klockikonen i det övre högra hörnet.

1. När importen är klar visas ett meddelande på panelen Enhetsåtgärder.

    ![Importen lyckades](./media/howto-manage-devices/bulkimport3a.png)

Om enhetsimporten misslyckas visas ett felmeddelande på panelen Enhetsåtgärder. En loggfil som samlar in alla fel genereras som du kan ladda ned.

## <a name="migrate-devices-to-a-template"></a>Migrera enheter till en mall

Om du registrerar enheter genom att starta importen under **Alla** enheter skapas enheterna utan någon association till enhetsmallen. Enheter måste associeras med en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:

1. Välj **Enheter** i det vänstra fönstret.

1. I den vänstra panelen väljer du **Alla enheter:**

    ![Enheter som inte är associerade](./media/howto-manage-devices/unassociateddevices1a.png)

1. Använd filtret i rutnätet för att avgöra om värdet i kolumnen **Enhetsmall** är **Ej associerat** för någon av dina enheter.

1. Välj de enheter som du vill associera med en mall:

1. Välj **Migrera:**

    ![Associera enheter](./media/howto-manage-devices/unassociateddevices2a.png)

1. Välj mallen i listan över tillgängliga mallar och välj **Migrera**.

1. De valda enheterna är associerade med den enhetsmall som du har valt.

## <a name="export-devices"></a>Exportera enheter

Om du vill ansluta en riktig IoT Central måste du ha dess anslutningssträng. Du kan exportera enhetsinformation i grupp för att få den information du behöver för att skapa enhetsanslutningssträngar. Exportprocessen skapar en CSV-fil med enhetsidentitet, enhetsnamn och nycklar för alla valda enheter.

Massexport av enheter från ditt program:

1. Välj **Enheter** i det vänstra fönstret.

1. I den vänstra rutan väljer du den enhetsmall som du vill exportera enheterna från.

1. Välj de enheter som du vill exportera och välj sedan **åtgärden** Exportera.

    ![Exportera](./media/howto-manage-devices/export1a.png)

1. Exportprocessen startar. Du kan spåra statusen med hjälp av panelen Enhetsåtgärder.

1. När exporten är klar visas ett meddelande om att det lyckades tillsammans med en länk för att ladda ned den genererade filen.

1. Välj länken **Ladda ned** fil för att ladda ned filen till en lokal mapp på disken.

    ![Exporten lyckades](./media/howto-manage-devices/export2a.png)

1. Den exporterade CSV-filen innehåller följande kolumner: enhets-ID, enhetsnamn, enhetsnycklar och tumavtryck för X509-certifikat:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Mer information om anslutningssträngar och anslutning av verkliga enheter till ditt IoT Central program finns i [Enhetsanslutning i Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Ta bort en enhet

Så här tar du bort en verklig eller simulerad enhet från ditt Azure IoT Central program:

1. Välj **Enheter** i det vänstra fönstret.

1. Välj enhetsmallen för den enhet som du vill ta bort.

1. Använd filterverktygen för att filtrera och söka efter dina enheter. Markera kryssrutan bredvid de enheter som ska tas bort.

1. Välj **Ta bort**. Du kan spåra statusen för den här borttagningen i panelen Enhetsåtgärder.

## <a name="change-a-property"></a>Ändra en egenskap

Molnegenskaper är enhetsmetadata som är associerade med enheten, till exempel ort och serienummer. Molnegenskaper finns bara i IoT Central och synkroniseras inte till dina enheter. Skrivbara egenskaper styr beteendet för en enhet och gör att du kan ställa in tillståndet för en enhet via fjärrstyrning, till exempel genom att ställa in måltemperaturen för en termostatenhet.  Enhetsegenskaper anges av enheten och är skrivskyddade inom IoT Central. Du kan visa och uppdatera egenskaper i **enhetsinformationens** vyer för din enhet.

1. Välj **Enheter** i det vänstra fönstret.

1. Välj enhetsmallen för den enhet vars egenskaper du vill ändra och välj målenheten.

1. Välj den vy som innehåller egenskaper för din enhet. I den här vyn kan du ange värden **och** välja Spara överst på sidan. Här ser du de egenskaper som din enhet har och deras aktuella värden. Molnegenskaper och skrivbara egenskaper har redigerbara fält, medan enhetsegenskaper är skrivskyddade. För skrivbara egenskaper kan du se deras synkroniseringsstatus längst ned i fältet. 

1. Ändra egenskaperna till de värden du behöver. Du kan ändra flera egenskaper i taget och uppdatera dem på samma gång.

1. Välj **Spara**. Om du har sparat skrivbara egenskaper skickas värdena till enheten. När enheten bekräftar ändringen för den skrivbara egenskapen återgår statusen till **synkroniserad**. Om du har sparat en molnegenskap uppdateras värdet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt Azure IoT Central program är nästa steg att lära dig hur du[konfigurerar regler](howto-configure-rules.md) för dina enheter.
