---
title: Självstudie – Skapa ett in-Store Analytics-program i Azure IoT Central
description: I den här självstudien visas hur du skapar ett program för butiks analys i IoT Central. Du skapar den, anpassar den och lägger till sensor enheter.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: caa61170504c3ee4b7162cc06db4df16d1232395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014374"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Självstudie: skapa ett in-Store Analytics-program i Azure IoT Central

I självstudien visas lösnings verktyg för att skapa ett Azure IoT Central i Store Analytics-program. Exempel programmet är för ett detalj handels lager. Det är en lösning på det vanliga företaget som behöver övervaka och anpassa sig till användning och miljö förhållanden.

Exempel programmet som du skapar innehåller tre riktiga enheter: en Rigado kaskad 500-gateway och två RuuviTag-sensorer. I kursen visas även hur du använder den simulerade beläggnings sensorn som ingår i program mal len för testnings ändamål. Rigado C500-gatewayen fungerar som kommunikations hubb i ditt program. Den kommunicerar med sensorer i butiken och hanterar deras anslutningar till molnet. RuuviTag är en miljö sensor som tillhandahåller telemetri, inklusive temperatur, fuktighet och tryck. Den simulerade beläggnings sensorn ger ett sätt att spåra rörelse och närvaro i arbets områdena i en butik. 

I den här självstudien finns anvisningar för att ansluta Rigado-och RuuviTag-enheter till ditt program. Om du har en annan gateway och sensorer kan du fortfarande följa stegen för att bygga ditt program. I kursen visas också hur du skapar simulerade RuuviTag-sensorer. Med de simulerade sensorerna kan du skapa programmet om du inte har några riktiga enheter. 

Du utvecklar en lösning för utcheckning och villkors övervakning i tre delar:

* Skapa programmet och anslut enheterna för att övervaka villkor
* Anpassa instrument panelen för att aktivera operatörer för att övervaka och hantera enheter
* Konfigurera data export så att butiks hanterare kan köra analyser och visualisera insikter

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd Azure IoT Central **in-Store Analytics-** betalningsmall för att skapa ett Retail Store-program
> * Anpassa program inställningarna
> * Skapa och anpassa mallar för IoT-enheter
> * Ansluta enheter till ditt program
> * Lägg till regler och åtgärder för att övervaka villkor

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här själv studie serien behöver du:
* En Azure-prenumeration rekommenderas. Du kan välja att använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).
* Åtkomst till en gateway-enhet och två miljö sensorer (du kan också använda simulerade enheter som beskrivs i själv studie kursen)
* Enhets mallar för de enheter som du använder (mallar finns för alla enheter som används i självstudien)

## <a name="create-an-application"></a>Skapa ett program
I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du använder det här programmet i själv studie serien för att bygga en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars loggar du in med en Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

1. Välj **åter försäljning**.  På sidan detalj handel visas flera mallar för åter försäljning.

Skapa ett nytt program för BA-utcheckning i butiken:  

1. Välj programmallen **in-Store Analytics-check** . Den här mallen innehåller enhetsspecifika för alla enheter som används i självstudien utom RuuviTag-sensorer. Mallen tillhandahåller också en instrument panel för instrument panelen för övervakning av utcheckning och miljö förhållanden och enhets status. 

1. Du kan också välja ett eget **program namn**. Det här programmet baseras på ett fiktivt butiks lager med namnet contoso. Självstudien använder **program namnet** *contoso check*. Program mal len baseras på det fiktiva företaget Northwind. I den här självstudien använder du Contoso för att lära dig hur du anpassar programmet.

    > [!NOTE]
    > Om du använder ett eget **program namn** måste du fortfarande använda ett unikt värde för programmets **URL**.

1. Om du har en Azure-prenumeration anger du din *katalog, din Azure-prenumeration och region*. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../core/quick-deploy-iot-central.md).

1. Välj **Skapa**.

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central skapa program fakturerings information](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Anpassa program inställningar

Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program. I det här avsnittet väljer du ett fördefinierat program tema. Du kan också lära dig att skapa ett anpassat tema och uppdatera program avbildningen. Med ett anpassat tema kan du ange program webbläsarens färger, webb läsar ikonen och program logo typen som visas i huvud rubriken.

Så här väljer du ett fördefinierat program tema:

1. Välj **Inställningar** på huvud rubriken.

    ![Program inställningar för Azure IoT Central](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Välj ett nytt **tema**.

3. Välj **Spara**.

I stället för att använda ett fördefinierat tema kan du skapa ett anpassat tema. Om du vill använda en uppsättning exempel bilder för att anpassa programmet och slutföra självstudien kan du hämta [exempel avbildningarna för Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Skapa ett anpassat tema:

1. Expandera det vänstra fönstret, om det inte redan har expanderats.

    ![Vänster fönster i Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Välj **Administration > anpassa ditt program**.

1. Använd knappen **ändra** för att välja en bild att ladda upp som **program logo typ**. Du kan också ange ett värde för **alternativ text för logo typ**. 

1. Använd knappen **ändra** för att välja en **ikon för en webb läsar ikon** som visas på flikar i webbläsaren.

1. Du kan också ersätta standard **webbläsarens färger** genom att lägga till HTML-hexadecimala färg koder. Lägg till *#008575* i **sidhuvudet**.  Lägg till *#A1F3EA* för den **accenten**. 

1. Välj **Spara**. 

    ![Anpassad logo typ för Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    När du har sparat uppdaterar programmet webb läsar färgerna, logo typen i huvud rubriken och webb läsar ikonen. 

    ![Azure IoT Central uppdaterade program inställningar](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Så här uppdaterar du program avbildningen:

1. Välj **Administration > program inställningar**.

1. Använd knappen **Välj bild** för att välja en bild som ska laddas upp som program avbildning. Den här bilden visas i program panelen på sidan **Mina appar** i IoT Central Application Manager.

1. Välj **Spara**.

1. Du kan också navigera till vyn **Mina appar** på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Panelen program visar den uppdaterade program avbildningen.

    ![Azure IoT Central anpassa program avbildningen](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Skapa mallar för enheter
Som ett verktyg kan du skapa enhetsspecifika mallar som gör det möjligt för dig och program operatörerna att konfigurera och hantera enheter. Du skapar en mall genom att skapa en anpassad, genom att importera en befintlig mallfil eller genom att importera en mall från Azure IoT-katalogen. När du har skapat och anpassat en enhets mall använder du den för att ansluta riktiga enheter till ditt program. Du kan också använda en enhets mall för att generera simulerade enheter för testning.

Program mal len **BA-utcheckning i butiken** har enhetsspecifika för flera enheter.  Det finns enhets mallar för två av de tre enheter som du använder i programmet. Enhets mal len för RuuviTag ingår inte i programmallen **för BA-utcheckning i butiken** . I det här avsnittet lägger du till en enhets mall för RuuviTag-sensorer till ditt program.

Så här lägger du till en RuuviTag enhets mal len i programmet:

1. Välj **mallar för enheter** i den vänstra rutan.

1. Välj **+ ny** för att skapa en ny enhets mall.

1. Sök efter och välj mallen **RuuviTag** sensor Device i Azure IoT-katalogen. 

1. Välj **Nästa: Anpassa**.

    ![Skärm bild som visar knappen Nästa: anpassa.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Välj **Skapa**. Programmet lägger till enhets mal len RuuviTag.

1. Välj **enhetsspecifika** i det vänstra fönstret. Sidan visar alla enhetsspecifika mallar som ingår i program mal len och RuuviTag som du nyss lade till.

    ![Mall för Azure IoT Central RuuviTag sensor enhet](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Anpassa mallar för enheter
Du kan anpassa enhetens mallar i programmet på tre sätt. Först anpassar du de inbyggda inbyggda gränssnitten i dina enheter genom att ändra enhets funktionerna. Med en temperatur sensor kan du till exempel ändra information som visnings namnet för temperatur gränssnittet, data typen, mått enheterna och minsta och högsta drifts intervall. 

Sedan kan du anpassa dina enhets mallar genom att lägga till moln egenskaper. Moln egenskaper är inte en del av de inbyggda enhets funktionerna. Moln egenskaper är anpassade data som ditt Azure IoT Central-program skapar, lagrar och kopplar till dina enheter. Ett exempel på en moln egenskap kan vara ett beräknat värde eller metadata, till exempel en plats som du vill associera med en uppsättning enheter. 

Börja med att anpassa mallar för enheter genom att skapa anpassade vyer. Vyer ger operatörer möjlighet att visualisera telemetri och metadata för dina enheter, till exempel enhets mått och hälso tillstånd.

Här använder du de första två metoderna för att anpassa enhets mal len för dina RuuviTag-sensorer. Information om hur du skapar vyer för sensorer finns i snabb starten för att [lägga till en simulerad enhet i IoT Central programmet](../core/quick-create-simulated-device.md) .

Så här anpassar du de inbyggda gränssnitten för enhets mal len RuuviTag:

1. Välj **mallar för enheter** i den vänstra rutan. 

1. Välj mallen för RuuviTag-sensorer. 

1. Dölj det vänstra fönstret. I sammanfattningsvyn av mallen visas enhetens funktioner.

    ![Sammanfattnings vy för Azure IoT Central RuuviTag Device Template](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Välj **Anpassa** på menyn RuuviTag Device Template. 

1. Bläddra i listan med funktioner och hitta typen av `humidity` telemetri. Det är rad elementet med det redigerbara **visnings namn** svärdet för *fuktighet*.

I följande steg kan du anpassa typen av `humidity` telemetri för RuuviTag-sensorer. Du kan också anpassa några av de andra typerna av telemetri.

För `humidity` telemetri-typen gör du följande ändringar:

1. Välj **Expand** -kontrollen för att expandera schema informationen för raden.

1. Uppdatera **visnings namn** svärdet från *fuktighet* till ett anpassat värde, till exempel *relativ fuktighet*.

1. Ändra alternativet för **semantisk typ** från *ingen* till *fukt*.  Du kan också ange schema värden för typen fuktighets telemetri i den expanderade vyn schema. Med schema inställningarna kan du skapa detaljerade verifierings krav för de data som sensorer spårar. Du kan till exempel ange lägsta och högsta värden för drift intervall för ett specifikt gränssnitt.

1. Välj **Spara** för att spara ändringarna.

    ![Skärm bild som visar sidan Anpassa och markerar knappen Spara.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Så här lägger du till en moln egenskap till en enhets mall i ditt program:

1. Välj **moln egenskaper** på menyn RuuviTag Device Template.

1. Välj **Lägg till moln egenskap**. 

Ange följande värden för att skapa en anpassad egenskap som lagrar platsen för varje enhet:

1. Ange värde *platsen* för **visnings namnet**. Värdet kopieras automatiskt till fältet **namn** , vilket är ett eget namn för egenskapen. Du kan använda det kopierade värdet eller ändra det.

1. Välj *sträng* i list rutan **schema** . Med en sträng typ kan du associera en plats namn sträng med vilken enhet som helst baserat på mallen. Du kan till exempel associera ett utrymme i en butik med varje enhet. Om du vill kan du ange en **semantisk typ** för din egenskap till *plats*, och detta ställer automatiskt in **schemat** till en *punkt*. Det gör att du kan koppla GPS-koordinater till en enhet. 

1. Ange den **minsta längden** till *2*. 

1. Ställ in **Rensa tomt utrymme** på **på**.

1. Välj **Spara** för att spara din anpassade moln egenskap.

    ![Anpassning av mall för Azure IoT Central RuuviTag-enhet](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Välj **Publicera**. 

    Om du publicerar en enhets mall blir den synlig för program operatörer. När du har publicerat en mall använder du den för att generera simulerade enheter för testning eller för att ansluta riktiga enheter till ditt program. Om du redan har enheter som är anslutna till ditt program skickar en anpassad mall ändringarna av enheterna.

## <a name="add-devices"></a>Lägg till enheter
När du har skapat och anpassat mallar för enheter är det dags att lägga till enheter. 

I den här självstudien använder du följande uppsättning verkliga och simulerade enheter för att skapa programmet:
- En riktig Rigado C500-Gateway
- Två verkliga RuuviTag-sensorer
- En simulerad **beläggnings** sensor. Den simulerade sensorn ingår i program mal len så du behöver inte skapa den. 

> [!NOTE]
> Om du inte har några riktiga enheter kan du fortfarande slutföra den här självstudien genom att skapa simulerade RuuviTag-sensorer. Följande anvisningar innehåller steg för att skapa en simulerad RuuviTag. Du behöver inte skapa en simulerad Gateway.

Slutför stegen i följande två artiklar för att ansluta en riktig Rigado-gateway och RuuviTag-sensorer. När du är klar kan du gå tillbaka till den här självstudien. Eftersom du redan har skapat enhetsspecifika mallar i den här självstudien behöver du inte skapa dem igen i följande riktnings uppsättningar.

- Information om hur du ansluter en Rigado-Gateway finns i [ansluta en Rigado kaskad 500 till ditt Azure IoT Central-program](../core/howto-connect-rigado-cascade-500.md).
- Information om hur du ansluter RuuviTag-sensorer finns i [ansluta en RuuviTag-sensor till ditt Azure IoT Central-program](../core/howto-connect-ruuvi.md). Du kan också använda de här anvisningarna för att skapa två simulerade sensorer, om det behövs.

## <a name="add-rules-and-actions"></a>Lägg till regler och åtgärder
Som en del av att använda sensorer i ditt Azure IoT Central-program för att övervaka villkor, kan du skapa regler för att köra åtgärder när vissa villkor är uppfyllda. En regel är kopplad till en enhets mall och en eller flera enheter och innehåller villkor som måste uppfyllas utifrån enhetens telemetri eller händelser. En regel har också en eller flera associerade åtgärder. Åtgärderna kan omfatta att skicka e-postmeddelanden eller utlösa en webhook-åtgärd för att skicka data till andra tjänster. Program mal len **BA-utcheckning i butiken** innehåller några fördefinierade regler för enheterna i programmet.

I det här avsnittet skapar du en ny regel som kontrollerar den högsta relativa fuktighets nivån baserat på RuuviTag sensor telemetri. Du lägger till en åtgärd i regeln så att om fuktigheten överskrider det högsta antalet skickar programmet ett e-postmeddelande. 

Så här skapar du en regel: 

1. Expandera det vänstra fönstret.

1. Välj **regler**.

1. Välj **+ Ny**.

1. Ange *fuktighets nivån* som regelns namn. 

1. Välj enhets mal len RuuviTag i **omfång**. Regeln som du definierar gäller för alla sensorer som är baserade på den mallen. Du kan också skapa ett filter som endast tillämpar regeln på en definierad del av sensorerna. 

1. Välj `Relative humidity` som **telemetri**. Det är enhets funktionen som du har anpassat i föregående steg.

1. Välj `Is greater than` som **operator**. 

1. Ange en typisk övre fuktighets nivå inomhus för din miljö som **värde**. Ange till exempel *65*. Du har angett ett villkor för regeln som inträffar när relativ fuktighet i en RuuviTag verklig eller simulerad sensor överskrider detta värde. Du kan behöva justera värdet upp eller ned beroende på det normala fuktighets intervallet i din miljö.  

   ![Regel villkor för Azure IoT Central Lägg till](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Så här lägger du till en åtgärd i regeln:

1. Välj **+ e-post**. 

1. Ange ett *meddelande med hög fuktighet* som det egna **visnings namnet** för åtgärden. 

1. Ange den e-postadress som är kopplad till ditt konto i **till**. Om du använder ett annat e-postmeddelande måste adressen som du använder vara för en användare som har lagts till i programmet. Användaren måste också logga in och ut minst en gång.

1. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.

1. Välj **klar** för att slutföra åtgärden.

   ![Azure IoT Central lägga till åtgärder i regler](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Välj **Spara** för att spara och aktivera den nya regeln. 

    Inom några minuter ska det angivna e-postkontot börja ta emot e-post. Programmet skickar ett e-postmeddelande varje gången en sensor anger att fuktighets nivån överskrider värdet i ditt villkor.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Använd Azure IoT Central **in-Store Analytics-** betalningsmall för att skapa ett Retail Store-program
* Anpassa program inställningarna
* Skapa och anpassa mallar för IoT-enheter
* Ansluta enheter till ditt program
* Lägg till regler och åtgärder för att övervaka villkor

Nu när du har skapat ett övervaknings program för Azure IoT Central villkor, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Anpassa instrument panelen för operatören](./tutorial-in-store-analytics-customize-dashboard.md)
