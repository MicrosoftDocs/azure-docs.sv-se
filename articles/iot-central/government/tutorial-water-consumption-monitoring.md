---
title: 'Självstudie: skapa en app för övervakning av vatten förbrukning med Azure IoT Central'
description: 'Självstudie: Lär dig att skapa ett program för övervakning av vatten förbrukning med hjälp av Azure IoT Central programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587204"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Självstudie: skapa ett program för övervakning av vatten förbrukning med Azure IoT Central

I den här självstudien får du lära dig hur du skapar ett program för övervakning av Azure IoT Central vatten förbrukning med hjälp av program mal len för övervakning av Azure-IoT Central

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Använd övervaknings mal len Azure IoT Central vattenförbrukning för att skapa ett program för övervakning av vatten förbrukning.
> * Utforska och anpassa instrument panelen för operatorn.
> * Utforska enhets mallar.
> * Utforska simulerade enheter.
> * Utforska och konfigurera regler.
> * Konfigurera jobb.
> * Anpassa din program anpassning genom att använda vit etikettering.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Skapa en app för övervakning av vatten förbrukning med Azure IoT Central

I det här avsnittet använder du övervaknings mal len Azure IoT Central vattenförbrukning för att skapa ett program för övervakning av vatten förbrukning i Azure IoT Central.

Så här skapar du ett nytt program för övervakning av Azure IoT Central vatten förbrukning:

1. Gå till webbplatsen för [Azure IoT Central start sida](https://aka.ms/iotcentral) .

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den. Logga annars in med en Microsoft-konto.

    ![Ange ditt organisationskonto](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Välj **bygge** i den vänstra rutan och välj fliken **myndigheter** . Sidan **myndigheter** visar flera myndighets programmallar.

   ![Bygg app-mallar för myndigheter](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Välj program mal len för **övervakning av vattenförbrukning** .
Den här mallen innehåller en exempel enhets mall för vattenförbrukning, en simulerad enhet, en instrument panel för operatör och förkonfigurerade övervaknings regler.

1. Välj **skapa app** för att öppna formuläret för att skapa **nya program** med följande fält:
    * **Program namn**: som standard använder programmet övervakning av *vatten förbrukning* följt av en unik ID-sträng som Azure IoT Central genererar. Du kan också välja ett eget program namn. Du kan också ändra program namnet senare.
    * **URL**: Azure IoT Central skapar automatiskt en URL baserat på programmets namn. Du kan välja att uppdatera URL: en till dina önskemål. Du kan också ändra URL: en senare.
    * Om du har en Azure-prenumeration anger du din **katalog**, din **Azure-prenumeration** och **plats** information. Om du inte har någon prenumeration kan du välja alternativet **7 dagars kostnads fri utvärderings version** och slutföra den nödvändiga kontakt informationen.

    Mer information om kataloger och prenumerationer finns i [skapa en snabb start för program](../core/quick-deploy-iot-central.md).

1. Välj **Skapa** längst ned på sidan.

    ![Sidan Azure IoT Central nytt program](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Sidan information om Azure IoT Central fakturering](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Nu har du skapat en app för övervakning av dricks-appar med hjälp av mallen Azure IoT Central vattenförbruknings övervakning.

Programmet för övervakning av vatten förbrukning levereras med förkonfigurerade:

* Exempel instrument panels instrument paneler.
* Exempel på fördefinierade vatten flöden och ventiler för ventilbaserade enheter.
* Simulerade vatten flöden och smarta ventil enheter.
* Regler och jobb.
* Exempel anpassning med hjälp av vita etiketter.

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatören

När du har skapat programmet öppnas exemplet **Wide World vatten konsumtions instrument panel** .

   ![Instrument panel för övervakning av vatten förbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för operatörer. Nu ska vi titta på instrument panelen innan du försöker anpassa den.

> [!NOTE]
> Alla data som visas på instrument panelen baseras på simulerade enhets data, som vi ska utforska i nästa avsnitt.
  
Instrument panelen består av olika typer av paneler:

* **Bild panel för Wide World vatten verktyg**: den första panelen i instrument panelen är en bild panel med ett fiktivt vatten verktyg för hela världen. Du kan anpassa panelen genom att infoga en egen bild eller ta bort den.
* KPI-panelen i **genomsnitts flöde**: KPI-panelen är konfigurerad att visa som ett exempel *på genomsnittet under de senaste 30 minuterna*. Du kan anpassa KPI-panelen och ange den som en annan typ och ett tidsintervall.
* **Enhets kommando paneler**: de här panelerna innehåller panelerna **Stäng ventil**, **öppen ventil** och **Ställ in ventil position** . Om du väljer kommandon går du till kommando sidan simulerad enhet. I Azure IoT Central är ett *kommando* en *enhets kapacitets* typ. Vi går igenom det här konceptet senare i avsnittet "enhets mal len" i den här självstudien.
* **Områdes mappning för vatten distribution**: kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kart panelen visar enhetens plats. Hovra över kartan och testa kontrollerna över kartan, t. ex. *Zooma in*, *Zooma ut* eller *expandera*.

    ![Instrument panels karta för instrument förbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Linje diagram över **Genomsnittligt flödes schema** och **miljö villkors linje diagram**: du kan visualisera en eller flera enhets telemetrivärden ritade som ett linje diagram över ett önskat tidsintervall.
* **Diagram över genomsnittlig ventil tryck termisk karta**: du kan välja termisk karta visualiserings typ för de enhets telemetridata som du vill visa distribuerat över ett tidsintervall med ett färg index.
* **Återställ innehålls panel för varnings tröskelvärden**: du kan inkludera innehålls paneler för anrop till åtgärd och bädda in en länk till en åtgärds sida. I det här fallet tar återställnings aviserings tröskelvärdet dig till program **jobben**, där du kan köra uppdateringar av enhets egenskaper. Vi går igenom det här alternativet senare i avsnittet "Konfigurera jobb" i den här självstudien.
* **Egenskaps paneler**: instrument panelen visar **ventiler drifts information**, **flödes tröskelvärden för aviseringar** och **underhålls informations** paneler.

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Som ett verktyg kan du anpassa vyer på instrument panelen för operatörer.

1. Välj **Redigera** för att anpassa den **bred förbruknings instrument panelen på webben**. Du kan anpassa instrument panelen genom att välja **Redigera** -menyn. När instrument panelen är i **redigerings** läge kan du lägga till nya paneler eller så kan du konfigurera den.

     ![Redigera instrumentpanel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Välj **+ nytt** för att skapa en ny instrument panel och konfigurera den från grunden. Du kan ha flera instrument paneler och du kan flytta mellan dina instrument paneler på instrument panelens meny.

## <a name="explore-the-device-template"></a>Utforska enhetens mall

En enhets mall i Azure IoT Central definierar funktionen hos en enhet, som kan vara telemetri, egenskap eller kommando. Som ett verktyg kan du definiera en eller flera enhetsspecifika mallar i Azure IoT Central som representerar funktionerna för de enheter som du ansluter till.

Programmet för övervakning av vattenförbrukning innehåller två referens enhets mallar som representerar en *flödes mätare* och en *Smart ventil* -enhet.

Så här visar du enhets mal len:

1. Välj **enhetsspecifika** i det vänstra fönstret i ditt program i Azure IoT Central. I listan **mallar för enheter** visas två mallar för enheter, **Smart ventil** och **flödes mätare**.

   ![Enhets mall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Välj en enhets mall för **flödes mätare** och bekanta dig med enhets funktionerna.

     ![Flödes mätare för enhets mal len](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Anpassa enhets mal len

Så här anpassar du enhets mal len:

1. Gå till **Anpassa** på menyn **mallar på enheten** .
1. Hitta `Temperature` typen av telemetri.
1. Uppdatera **visnings namnet** för `Temperature` till `Reported temperature` .
1. Uppdatera mått enheten eller ange det **minsta värdet** och **Max värdet**.
1. Välj **Spara** för att spara ändringarna.

### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap

1. Gå till **moln egenskaper** på menyn **mallar på enheten** .
1. Lägg till en ny moln egenskap genom att välja **+ Lägg till moln egenskap**.
    I Azure IoT Central kan du lägga till en egenskap som är relevant för enheten. Som exempel kan en moln egenskap vara en aviserings tröskel som är speciell för ett installations fält, till gångs information eller annan underhålls information.
1. Välj **Spara** för att spara ändringarna.

### <a name="views"></a>Vyer

Enhets mal len för vattenförbruknings övervakning innehåller fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhets data, men även ange moln egenskaper.

  ![Vyer för enhets mal len](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicera

Om du har gjort några ändringar ska du se till att **publicera** enhets mal len.

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

Välj **+ nytt** för att skapa en ny enhets mall och följ proceduren för att skapa.
Du kan skapa en anpassad enhets mall från början, eller så kan du välja en enhets mall från Azures enhets katalog.

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I Azure IoT Central kan du skapa simulerade enheter för att testa enhetens mall och program. Programmet för övervakning av vattenförbrukning har två simulerade enheter som är kopplade till mallarna **flödes mätare** och **Smart ventil** -enhet.

### <a name="view-the-devices"></a>Visa enheterna

1. Välj **enheter**  >  **alla enheter** i den vänstra rutan.

   ![Fönstret alla enheter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Välj **Smart ventil 1**.

    ![Smart ventil 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. På fliken **kommandon** ser du de tre enhets kommandona (**Stäng ventil**, **öppen ventil** och **Ställ in ventil position**) som är funktioner som definierats i mallen **Smart ventil** enhet.

1. Utforska fliken **enhets egenskaper** och fliken **enhets instrument panel** .

> [!NOTE]
> Observera att alla flikar konfigureras från vyerna för enhets mal len.

### <a name="add-new-devices"></a>Lägg till nya enheter

Lägg till nya enheter genom att välja **+ ny** på fliken **enheter** .

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för att automatiskt övervaka enhets telemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan vara att skicka e-postaviseringar eller utlösa en åtgärd från Microsoft-automatisering eller en webhook-åtgärd för att skicka data till andra tjänster.

Det program för övervakning av vatten förbrukning som du har skapat har tre förkonfigurerade regler.

### <a name="view-rules"></a>Visa regler

1. Välj **regler** i det vänstra fönstret.

   ![Regel fönster](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Välj **hög pH-avisering**, som är en av de förkonfigurerade reglerna i programmet.

     ![Hög pH-avisering](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert`Regeln är konfigurerad att kontrol lera mot villkoret `Acidity (pH)` `greater than` `Max flow threshold` . Högsta flödes tröskel är en moln egenskap som definierats i mallen **Smart ventil** enhet. Värdet för `Max flow threshold` anges per enhets instans.

Nu ska vi skapa en e-poståtgärd.

Så här lägger du till en åtgärd i regeln:

1. Välj **+ e-post**.
1. Ange **hög pH-avisering** som det egna **visnings namnet** för åtgärden.
1. Ange den e-postadress som är kopplad till ditt Azure IoT Central-konto i **till**.
1. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
1. Välj **klar** för att slutföra åtgärden.
1. Välj **Spara** för att spara och aktivera den nya regeln.

Inom några minuter bör du få ett e-postmeddelande när det angivna villkoret är uppfyllt.

> [!NOTE]
> Programmet skickar ett e-postmeddelande varje gången ett villkor är uppfyllt. Välj **inaktivera** om du vill inaktivera regeln för att sluta ta emot e-post från den automatiserade regeln.
  
Så här skapar du en ny regel:

* Välj **+ nytt** på fliken **regler** i det vänstra fönstret.

## <a name="configure-jobs"></a>Konfigurera jobb

I Azure IoT Central kan du använda jobb för att utlösa enhets-eller moln egenskaps uppdateringar på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhets kommandon på flera enheter. Azure IoT Central automatiserar arbets flödet åt dig.

1. Välj **jobb** i det vänstra fönstret.
1. Välj **+ ny** och konfigurera ett eller flera jobb.

## <a name="customize-your-application"></a>Anpassa ditt program

Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

1. Välj **Administration**  >  **Anpassa ditt program**.
1. Om du vill välja en bild som ska överföras som **program logo typ** väljer du knappen **ändra** .
1. Välj knappen **ändra** om du vill välja en bild i **webbläsarens ikon** som visas i webbläsarens flikar.
1. Du kan också ersätta standard **webbläsarens färger** genom att lägga till HTML-hexadecimala färg koder.

   ![Val för program logo typ, webb läsar ikon och webb läsar färger](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Du kan också ändra program avbildningar genom att välja inställningar för **administrations**  >  **program**. Välj en bild som ska laddas upp som program avbildning genom att välja knappen **Välj bild** .
1. Slutligen kan du även ändra **temat** genom att välja **inställnings** ikonen i det övre högra hörnet i programmet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det.

1. Välj **Administration** i det vänstra fönstret av ditt Azure IoT Central-program.
1. Välj **program inställningar** och välj sedan **ta bort** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om 

> [!div class="nextstepaction"]
> [Koncept för övervakning av vattenförbrukning](./concepts-waterconsumptionmonitoring-architecture.md).
