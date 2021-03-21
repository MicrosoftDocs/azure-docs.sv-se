---
title: Själv studie kurs för Micro-uppfyllelse Center-Mall | Microsoft Docs
description: En själv studie kurs om programmallen för Micro-uppfyllelse Center för Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: ab4d609b1638b47ad73b6f1143adee2870577305
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390478"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Självstudie: Distribuera och gå igenom en Programmall för Micro-uppfyllelse Center

I den här självstudien använder du programmallen Azure IoT Central Micro-uppfyllelse Center för att bygga en detalj handels lösning.

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Så här distribuerar du program mal len
> * Så här använder du program mal len

## <a name="prerequisites"></a>Förutsättningar
Du behöver en Azure-prenumeration för att kunna slutföra den här själv studie serien. Du kan välja att använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Skapa ett program 
I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du använder det här programmet i själv studie serien för att bygga en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .
1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den. Logga annars in med en Microsoft-konto:

   ![Skärm bild av dialog rutan Microsoft-konto inloggning](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

1. Välj **åter försäljning**.  På sidan detalj handel visas flera mallar för åter försäljning.

Skapa ett nytt program för Micro-uppfyllelse Center som använder för hands versions funktioner:  
1. Välj program mal len för **Micro-uppfyllelse Center** . Den här mallen innehåller enhets mallar för alla enheter som används i självstudien. Mallen tillhandahåller också en instrument panel för instrument panelen för att övervaka villkor inom ditt uppfyllande Center, samt villkoren för dina robot operatörer. 

    ![Skärm bild av Azure IoT Central Bygg din IoT-programsida](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Du kan också välja ett eget **program namn**. Program mal len baseras på det fiktiva företaget Northwind Traders. 

    >[!NOTE]
    >Om du använder ett eget program namn måste du fortfarande använda ett unikt värde för programmets URL.

1. Om du har en Azure-prenumeration anger du din katalog, din Azure-prenumeration och region. Om du inte har en prenumeration kan du aktivera den kostnads fria utvärderings versionen på 7 dagar och slutföra den nödvändiga kontakt informationen.  

    Mer information om kataloger och prenumerationer finns i snabb start för att [skapa ett program](../core/quick-deploy-iot-central.md) .

1. Välj **Skapa**.

    ![Skärm bild av sidan Azure IoT Central New Application](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Gå igenom programmet 

När du har distribuerat app-mallen ser du **instrument panelen för Northwind Traders-Micro-Support Center**. Northwind Traders är en fiktiv åter försäljare som har ett mikroutförande Center som hanteras i det här Azure IoT Central-programmet. På instrument panelen för operatören ser du information och telemetri om enheterna i den här mallen, tillsammans med en uppsättning kommandon, jobb och åtgärder som du kan vidta. Instrument panelen delas logiskt i två avsnitt. Till vänster kan du övervaka miljö villkoren i den uppfyllande strukturen och till höger kan du övervaka hälso tillståndet för en robot transport i anläggningen.  

Från instrument panelen kan du:
   * Se telemetri för enheter, till exempel antalet plockningar, antalet bearbetade beställningar och egenskaper, till exempel struktur systemets status.  
   * Visa våningsplanet och platsen för de automatiska transport företagen i den uppfyllande strukturen.
   * Utlös kommandon, till exempel återställning av kontroll systemet, uppdatering av den inbyggda program varan och omkonfiguration av nätverket.

     ![Skärm bild av den övre halvan av instrument panelen i Northwind Traders-instrumentpanelen.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Se ett exempel på instrument panelen som en operatör kan använda för att övervaka villkor inom uppfyllande Center. 
   * Övervaka hälso tillståndet för de nytto laster som körs på gateway-enheten inom uppfyllande Center.    

     ![Skärm bild av Botton-halvan av instrument panelen i Northwind Traders-instrumentpanelen.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Enhets mall
Om du väljer fliken enhets mallar ser du att det finns två olika enhets typer som är en del av mallen: 
   * **Robot frakt firma**: den här enhets mal len representerar definitionen för ett fungerande robot transport företag som har distribuerats i uppfyllande struktur och som utför lämpliga lagrings-och hämtnings åtgärder. Om du väljer mallen ser du att roboten skickar enhets data, till exempel temperatur-och axel position och egenskaper som status för robot transport. 
   * **Övervakning av struktur villkor**: den här enhets mal len representerar en enhets samling som gör att du kan övervaka miljö villkor, samt vilken gateway-enhet som är värd för olika Edge-arbetsbelastningar för att sätta ditt uppfyllande Center. Enheten skickar telemetridata, till exempel temperatur, antalet plockningar och antalet beställningar. Den skickar också information om tillståndet och hälsan för beräknings arbets belastningar som körs i din miljö. 

     ![Enhets mallar för Micro-uppfyllelse Center](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Om du väljer fliken enhets grupper ser du också att dessa enhets mallar automatiskt har enhets grupper som skapats för dem.

## <a name="rules"></a>Regler
På fliken **regler** visas en exempel regel som finns i program mal len för att övervaka temperatur villkoren för den automatiska transporten. Du kan använda den här regeln för att varna operatören om en speciell robot i anläggningen är överhettad och måste kopplas från för underhåll. 

Använd exempel regeln som inspiration för att definiera regler som passar dina affärs funktioner bättre.

![Skärm bild av fliken regler](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len. Gå till inställningar för **administrations**  >  **program** och välj **ta bort**.

![Skärm bild av sidan program inställningar för Micro-uppfyllelse Center](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om:

> [!div class="nextstepaction"]
> [lösnings arkitektur för Micro-uppfyllelse Center](./architecture-micro-fulfillment-center.md)
