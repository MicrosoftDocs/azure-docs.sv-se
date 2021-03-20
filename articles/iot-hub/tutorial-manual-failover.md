---
title: Självstudie – manuell redundansväxling av en Azure IoT Hub | Microsoft Docs
description: Självstudie – lär dig hur du utför en manuell redundansväxling av IoT-hubben till en annan region och bekräftar att den fungerar, och sedan återställer den till den ursprungliga regionen och markerar den igen.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3b6bc972f5c26c78ffff81f5bab8c2812cf2cb11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622920"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Självstudie: utföra manuell redundans för en IoT-hubb

Manuell redundans är en funktion i IoT Hub-tjänsten som gör att kunder kan utföra [redundans](https://en.wikipedia.org/wiki/Failover) för hubbens åtgärder från en primär region till motsvarande geoparade Azure-region. Manuell redundans kan utföras i händelse av ett regionalt haveri eller ett längre tjänstavbrott. Du kan även utföra en planerad redundans för att testa din haveriberedskap, men vi rekommenderar att du använder en IoT-testhubb i stället för en som körs i produktion. Funktionen manuell redundans erbjuds till kunder utan extra kostnad för IoT Hub som skapats efter 18 maj 2017.

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb. 
> * Utför en redundans. 
> * Se att hubben körs på den sekundära platsen.
> * Utför en återställning efter fel för att återställa IoT-hubbens åtgärder till den primära platsen. 
> * Bekräfta att hubben körs korrekt på rätt plats.

Mer information om manuell redundans och Microsoft-initierad redundans med IoT Hub finns i [haveri beredskap över flera regioner](iot-hub-ha-dr.md#cross-region-dr).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här självstudien använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Klicka på **+ Skapa en resurs** och välj **Internet of Things** (Sakernas Internet) och sedan **IoT Hub**.

   ![Skärmbild som visar skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-01.png)

3. Välj fliken **grundläggande** . Fyll i följande fält.

    **Prenumeration**: Välj den Azure-prenumeration som du vill använda.

    **Resursgrupp**: Klicka på **Skapa ny** och ange **ManlFailRG** för resursgruppens namn.

    **Region**: Välj en region nära dig. I den här självstudien används `West US 2`. Det går bara att utföra en redundans mellan geoparade Azure-regioner. Regionen geo-par med västra USA 2 är WestCentralUS.
    
   **Namn på IoT-hubb**: Ange ett namn för IoT-hubben. Hubbnamnet måste vara globalt unikt. 

   ![Skärmbild som visar Basics-fönsterruta (Grunder) för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klicka på **Granska + skapa**. (Den använder standardinställningarna för storlek och skala.) 

4. Granska informationen och klicka sedan på **Skapa** för att skapa IoT-hubben. 

   ![Skärmbild som visar det sista steget för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Utför en manuell redundans

Observera att det finns en gräns på två redundanser och två återställningar efter fel per dag för en IoT-hubb.

1. Klicka på **Resursgrupper** och välj sedan resursgruppen **ManlFailRG**. Klicka på din hubb i listan över resurser. 

1. Under **Inställningar** i fönstret IoT Hub klickar du på **redundans**.

   ![Skärmbild som visar fönsterrutan med egenskaper för IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

1. I fönstret manuell redundans visas den **aktuella platsen** och **platsen för redundans**. Den aktuella platsen anger alltid den plats där hubben är aktiv för närvarande. Platsen för redundans är den standardiserade [Azure geo-kopplade regionen](../best-practices-availability-paired-regions.md) som är länkad till den aktuella platsen. Du kan inte ändra platsvärdena. För den här självstudien är den aktuella platsen `West US 2` och platsen för redundans `West Central US` .

   ![Skärmbild som visar fönsterrutan för manuell redundans](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Klicka på **Starta redundans** överst i fönstret manuell redundans. 

1. I bekräftelse fönstret fyller du i namnet på din IoT Hub för att bekräfta att det är det som du vill redundansväxla. Klicka sedan på **redundans** för att starta redundansväxlingen.

   Den tid det tar att utföra manuell redundans är proportionell mot antalet enheter som är registrerade för din hubb. Om du till exempel har 100 000 enheter kan det ta 15 minuter, men om du har fem miljoner enheter kan det ta en timme eller längre.

   ![Skärm bild som visar bekräftelse fönstret för manuell redundans](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Medan den manuella redundansväxlingen körs visas en banderoll som anger att en manuell redundansväxling pågår. 

   ![Skärmbild som visar att manuell redundans pågår](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Om du stänger IoT Hubs fönstret och öppnar det igen genom att klicka på det i rutan resurs grupp visas en banderoll som visar att navet är i mitten av en manuell redundansväxling. 

   ![Skärm bild som visar IoT Hub redundansväxling pågår](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   När den är färdig, vänds de aktuella områdena och redundansväxlingen på sidan manuell redundans och hubben är aktiv igen. I det här exemplet är den aktuella platsen nu `WestCentralUS` och platsen för redundans är nu `West US 2` . 

   ![Skärmbild som visar att redundansen är klar](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   På sidan Översikt visas också en banderoll som anger att redundansväxlingen är klar och IoT Hub körs i `West Central US` .

   ![Skärm bild som visar att redundansväxlingen är klar på översikts Sidan](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Utföra en återställning efter fel 

När du har utfört en manuell redundans kan du växla tillbaka hubbens åtgärder till den ursprungliga primära regionen – det här kallas en återställning efter fel. Om du precis har utfört en redundans måste du vänta ungefär en timme innan du kan begära en återställning efter fel. Om du försöker utföra återställningen på en kortare tid visas ett felmeddelande.

En återställning efter fel utförs precis som en manuell redundans. Det här är stegen: 

1. För att utföra en återställning efter fel går du tillbaka till fönsterrutan för IoT Hub för din IoT-hubb.

2. Under **Inställningar** i fönstret IoT Hub klickar du på **redundans**. 

3. Klicka på **Starta redundans** överst i fönstret manuell redundans. 

4. I bekräftelse fönstret fyller du i namnet på din IoT Hub för att bekräfta att det är det som du vill återställa efter fel. Sedan initierar du återställning efter fel genom att klicka på OK. 

   ![Skärmbild på manuell begäran om återställning efter fel](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Banderollerna visas enligt beskrivning i avsnittet utföra en redundans. När återställningen är klar visas den igen `West US 2` som den aktuella platsen och `West Central US` som platsen för redundans enligt inställningen ursprungligen.

## <a name="clean-up-resources"></a>Rensa resurser 

Om du vill ta bort de resurser du skapade för den här självstudien tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas IoT-hubben och själva resursgruppen bort. 

1. Klicka på **Resursgrupper**. 

2. Leta upp och välj resursgruppen **ManlFailRG**. Klicka på det för att öppna det. 

3. Klicka på **Ta bort resursgrupp**. När du uppmanas anger du namnet på resursgruppen och klickar på **Ta bort** för att bekräfta. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar och utför en manuell redundans samt hur du begär en återställning efter fel genom att utföra följande uppgifter:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb. 
> * Utför en redundans. 
> * Se att hubben körs på den sekundära platsen.
> * Utför en återställning efter fel för att återställa IoT-hubbens åtgärder till den primära platsen. 
> * Bekräfta att hubben körs korrekt på rätt plats.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Hantera tillståndet för en IoT-enhet](tutorial-device-twins.md)
