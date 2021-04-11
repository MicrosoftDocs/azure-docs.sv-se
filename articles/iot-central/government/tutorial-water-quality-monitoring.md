---
title: 'Självstudie: skapa en app för övervakning av vatten kvalitet med Azure IoT Central'
description: 'Självstudie: Lär dig hur du skapar ett program för övervakning av vatten kvalitet med hjälp av Azure IoT Central programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017247"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Självstudie: skapa ett program för övervakning av vatten kvalitet i Azure IoT Central

Den här självstudien vägleder dig genom skapandet av ett program för övervakning av vatten kvalitet i Azure IoT Central. Du skapar programmet från program mal len för övervakning av Azure IoT Central **vatten kvalitet** .

I de här självstudierna får du lära dig att:

> [!div class="checklist"]
> * Använd mallen för **övervakning av vatten kvalitet** för att skapa ett program för övervakning av vatten kvalitet.
> * Utforska och anpassa en instrument panel för operatör.
> * Utforska en enhets mall för övervakning av vatten kvalitet.
> * Utforska simulerade enheter.
> * Utforska och konfigurera regler.
> * Konfigurera jobb.
> * Anpassa program anpassning med hjälp av vita etiketter.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du har en Azure-prenumeration för att slutföra den här kursen. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Skapa ett program för övervakning av vatten kvalitet i Azure IoT Central

I det här avsnittet använder du mallen för övervakning av Azure IoT Central **vatten kvalitet** för att skapa ett program för övervakning av vatten kvalitet.

1. Gå till [Start sidan för Azure-IoT Central](https://aka.ms/iotcentral).

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den. Annars loggar du in med en Microsoft-konto:

    ![Logga in på ditt organisations konto](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Välj **bygge** i fönstret längst till vänster i Azure IoT Central och välj fliken **myndigheter** . I fönstret myndigheter visas flera myndighets programmallar.

    ![Myndighets program mallar](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Välj program mal len **övervakning av vatten kvalitet** . Den här program mal len innehåller en enhets mall för vatten kvalitet, simulerade enheter, en instrument panel för operatör och förkonfigurerade övervaknings regler.

1. Välj **Skapa app**. Fönstret **ny program** öppnas och visar följande element:

    * **Program namn**: som standard är program namnet övervakning av **vatten kvalitet** följt av en unik ID-sträng som Azure IoT Central genererar. Om du vill kan du ange ett visnings namn eller ändra program namnet senare.
    * **URL**: du kan ange valfri URL som du vill ha eller ändra URL-värdet senare.
    * Om du har en Azure-prenumeration anger du värden för **katalog**, **Azure-prenumeration** och **region**. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på sju dagar** och slutföra den nödvändiga kontakt informationen.

    Mer information om kataloger och prenumerationer finns i snabb start för att [skapa ett program](../core/quick-deploy-iot-central.md) .

1. Välj knappen **skapa** längst ned till vänster på sidan.

    ![Sidan Azure IoT Central New-Application](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Fakturerings information för Azure IoT Central New-Application](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Nu har du skapat ett program för övervakning av vatten kvalitet med hjälp av mallen för övervakning av Azure IoT Central **vatten kvalitet** .

Det nya programmet levereras med följande förkonfigurerade komponenter:

* Instrument panel för operatör
* Enhets mallar för övervakning av vatten kvalitet
* Simulerade enheter för övervakning av vatten kvalitet
* Regler och jobb
* Anpassning som använder vita etiketter

Du kan när som helst ändra ditt program.

Utforska sedan programmet och gör några anpassningar.

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatören

När du har skapat programmet öppnas fönstret **bred vatten kvalitet** för hela världen.

   ![Instrument panelen för övervakning av vatten kvalitet](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för användning av operatörer. Men innan du försöker anpassa bör du först utforska instrument panelen.

Alla data som visas i instrument panelen baseras på simulerade enhets data, som beskrivs i nästa avsnitt.

Instrument panelen innehåller följande typer av paneler:

* **Bild panel för Wide World vatten verktyg**: den första panelen i det övre vänstra hörnet på instrument panelen är en bild som visar det fiktiva verktyget Wide World. Du kan anpassa panelen för att använda en egen bild eller så kan du ta bort panelen.

* **KPI-paneler i genomsnitts pH**: KPI-paneler som **genomsnitts pH under de senaste 30 minuterna** visas överst i instrument panelens fönster. Du kan anpassa KPI-paneler och ange var och en till en annan typ och ett tidsintervall.

* **Områdes karta för vatten övervakning**: Azure IoT Central använder Azure Maps, som du direkt kan ange i programmet för att visa enhetens plats. Du kan också mappa plats information från ditt program till din enhet och sedan använda Azure Maps för att visa information om en karta. Hovra över kartan och prova kontrollerna.

* **Termiskt diagram för tillägg för pH-distribution**: du kan välja olika visualiserings diagram för att Visa telemetri på det sätt som passar bäst för ditt program.

* **Linje diagram över kritiska kvalitets indikatorer**: du kan visualisera enhetens telemetri ritas som ett linje diagram under ett tidsintervall.  

* **Diagram över koncentration av kemiska agenter**: du kan visualisera telemetri för enheter i ett stapeldiagram.

* **Åtgärds knapp**: instrument panelen innehåller en panel för åtgärder som en operatör kan initiera direkt från instrument panelen för övervakning. Att återställa en enhets egenskaper är ett exempel på sådana åtgärder.

* Panel för **egenskaps listor**: instrument panelen har flera egenskaps paneler som representerar tröskel information, enhetens hälso information och underhålls information.

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Som ett verktyg kan du anpassa vyer på instrument panelen för användning av operatörer.

1. Välj **Redigera** för att anpassa den **breda instrument panels fönstret för vatten kvalitet** . Du kan anpassa instrument panelen genom att välja kommandon på **Redigera** -menyn. När instrument panelen är i redigerings läge kan du lägga till nya paneler, eller så kan du konfigurera de befintliga filerna.

    ![Redigera din instrument panel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Välj **+ nytt** för att skapa en ny instrument panel som du kan konfigurera. Du kan ha flera instrument paneler och kan navigera bland dem från instrument panels menyn.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Utforska en enhets mall för övervakning av vatten kvalitet

En enhets mall i Azure IoT Central definierar funktionerna för en enhet. Tillgängliga funktioner är telemetri, egenskaper och kommandon. Som ett verktyg kan du definiera enhetsspecifika i Azure IoT Central som representerar funktionerna i de anslutna enheterna. Du kan också skapa simulerade enheter för att testa din enhets mall och program.

Programmet för övervakning av vatten kvalitet som du har skapat kommer att ha en enhets mall för övervakning av vatten kvalitet.

Så här visar du enhets mal len:

1. Välj **enhetsspecifika** i fönstret längst till vänster i ditt program i Azure IoT Central.
1. Välj **övervakaren för vatten kvalitet** i listan med enhetsspecifika enheter. Enhets mal len öppnas.

    ![Enhets mal len](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Anpassa enhets mal len

Öva på att anpassa följande inställningar för enhets mal len:

1. Från menyn enhets mall väljer du **Anpassa**.
1. Gå till typen av typ **av telemetri.**
1. Ändra värdet för **visnings namn** till **rapporterad temperatur**.
1. Ändra mått enheten eller ange **minsta värde** och **högsta värde**.
1. Välj **Spara**.

#### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap

1. Från menyn enhets mall väljer du **moln egenskap**.
1. Om du vill lägga till en ny moln egenskap väljer du **+ Lägg till moln egenskap**. I Azure IoT Central kan du lägga till en egenskap som är relevant för en enhet men som inte förväntas skickas av enheten. Ett exempel på en sådan egenskap är ett tröskelvärde för aviseringar som är speciellt för installations-, till gångs information eller underhålls information.
1. Välj **Spara**.

### <a name="explore-views"></a>Utforska vyer

Enhets mal len för övervakning av vatten kvalitet levereras med fördefinierade vyer. Vyerna definierar hur operatörer ser enhets data och anger moln egenskaper. Utforska vyer och praxis att göra ändringar.

  ![Vyer för enhets mal len](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Om du gör några ändringar ska du se till att välja **publicera** för att publicera enhets mal len.

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

1. Välj **+ nytt** för att skapa en ny enhets mall och följ proceduren för att skapa.
1. Skapa en anpassad enhets mall eller Välj en enhets mall från katalogen Azure IoT-enheter.

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

Programmet för övervakning av vatten kvalitet som du skapade från program mal len har två simulerade enheter. Dessa enheter mappas till enhets mal len för kontroll av vatten kvalitet.

### <a name="view-the-devices"></a>Visa enheterna

1. Välj **enheter** i fönstret längst till vänster i ditt program.

   ![Enheter](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Välj en simulerad enhet.

    ![Välj enhet 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. På fliken **moln egenskaper** ändrar du **tröskelvärdet för surhets grad (pH)** från **8** till **9**.
1. Utforska fliken **enhets egenskaper** och fliken **enhets instrument panel** .

> [!NOTE]
> Alla flikar har kon figurer ATS från **vyer för enhets mal len**.

### <a name="add-new-devices"></a>Lägg till nya enheter

På fliken **enheter** väljer du **+ ny** för att lägga till en ny enhet.

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler som automatiskt övervakar telemetri från enheten. Dessa regler utlöser en åtgärd när något av deras villkor är uppfyllt. En möjlig åtgärd är att skicka e-postaviseringar. Andra möjligheter är en åtgärd för att automatisera en åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Programmet för övervakning av vatten kvalitet som du skapade har två förkonfigurerade regler.

### <a name="view-rules"></a>Visa regler

1. Välj **regler** i fönstret längst till vänster i ditt program.

   ![Regler](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Välj **hög pH-avisering**, som är en av de förkonfigurerade reglerna i programmet.

   ![Varnings regeln för hög pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **Varnings regeln för hög pH** har kon figurer ATS för att kontrol lera villkoret för syrahalt (pH) är större än 8.

Lägg sedan till en e-poståtgärd i regeln:

1. Välj **+ e-post**.
1. I rutan **visnings namn** anger du **hög pH-avisering**.
1. I rutan **till** anger du den e-postadress som är kopplad till ditt Azure IoT Central-konto.
1. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
1. Välj **klar** för att slutföra åtgärden.
1. Välj **Spara** för att spara och aktivera den nya regeln.

Inom några minuter bör du få ett e-postmeddelande när det konfigurerade villkoret är uppfyllt.

> [!NOTE]
> Programmet skickar ett e-postmeddelande varje gången ett villkor är uppfyllt. Välj **inaktivera** för en regel för att sluta ta emot automatiserad e-post från regeln.
  
Om du vill skapa en ny regel väljer du **regler** i fönstret längst till vänster i programmet och väljer sedan **+ ny**.

## <a name="configure-jobs"></a>Konfigurera jobb

Med Azure IoT Central-jobb kan du utlösa uppdateringar till enhets-eller moln egenskaper på flera enheter. Du kan också använda jobb för att utlösa enhets kommandon på flera enheter. Azure IoT Central automatiserar arbets flödet åt dig.

1. Välj **jobb** i fönstret längst till vänster i ditt program.
1. Välj **+ ny** och konfigurera ett eller flera jobb.

## <a name="customize-your-application"></a>Anpassa ditt program

Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

1. Välj **Administration**  >  **Anpassa ditt program**.
1. Under **program logo typ** väljer du **ändra** för att välja den bild som ska laddas upp som logo typ.
1. Under **webb läsar ikon** väljer du **ändra** för att välja den bild som visas på flikar i webbläsaren.
1. Under **webb läsar färger** kan du ersätta standardvärdena med HTML-hexadecimala färg koder.
1. Välj **Inställningar** om du vill ändra värdet för **tema**.

   ![Anpassa ditt program](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Uppdatera program avbildningen

1. Välj inställningar för **administrations**  >  **program**.

1. Använd knappen **Välj bild** för att välja en bild som ska laddas upp som program avbildning.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda programmet tar du bort programmet med följande steg:

1. Öppna fliken **Administration** i fönstret längst till vänster i ditt program.
1. Välj **program inställningar** och klicka på knappen **ta bort** .

    ![Ta bort ditt program](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om 

> [!div class="nextstepaction"]
> [koncept för övervakning av vatten kvalitet](./concepts-waterqualitymonitoring-architecture.md).
