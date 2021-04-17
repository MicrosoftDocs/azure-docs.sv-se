---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Bekanta dig med de viktigaste områdena i Azure IoT Central som du använder för att skapa, hantera och använda din IoT-lösning.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ff795281b3471d5c6f57397edf5a52e0df9bbd32
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589136"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet

Den här artikeln beskriver Microsoft Azure IoT Central-användargränssnittet. Du kan använda gränssnittet till att skapa, hantera och använda en Azure IoT Central-lösning och dess anslutna enheter.

## <a name="iot-central-homepage"></a>IoT Central startsida

Startsidan [IoT Central där](https://aka.ms/iotcentral-get-started) du kan lära dig mer om de senaste nyheterna och funktionerna som är tillgängliga på IoT Central, skapa nya program och se och starta dina befintliga program.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central startsida":::

### <a name="create-an-application"></a>Skapa ett program

I avsnittet Skapa kan du bläddra i listan över branschrelevensmallar IoT Central börja från grunden med hjälp av en anpassad appmall.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central build-sida":::

Mer information finns i [snabbstarten Skapa Azure IoT Central ett](quick-deploy-iot-central.md) program.

### <a name="launch-your-application"></a>Starta ditt program

Du startar ditt IoT Central genom att gå till den URL som du valde när du skapade appen. Du kan också se en lista över alla program som du har åtkomst till i [IoT Central app manager](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central apphanteraren":::

## <a name="navigate-your-application"></a>Navigera i ditt program

När du är i IoT-programmet använder du det vänstra fönstret för att komma åt de olika områdena. Du kan expandera eller komprimera det vänstra fönstret genom att välja ikonen med tre punkter överst i fönstret:

> [!NOTE]
> Vilka objekt som visas i det vänstra fönstret beror på din användarroll. Läs mer om [att hantera användare och roller.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="vänstra rutan":::

  :::column-end:::
  :::column span="2":::
     **Instrumentpaneler** visar alla program och personliga instrumentpaneler. 
     
     **Med** enheter kan du hantera dina anslutna enheter – verkliga och simulerade.

     **Med enhetsgrupper** kan du visa och skapa samlingar av enheter som anges av en fråga. Enhetsgrupper används via programmet för att utföra massåtgärder.

     **Med** regler kan du skapa och redigera regler för att övervaka dina enheter. Regler utvärderas baserat på enhettelemetri och utlöser anpassningsbara åtgärder.

     **Med** Analys kan du visa telemetri från dina enheter grafiskt.

     **Med** jobb kan du hantera dina enheter i stor skala genom att köra massåtgärder.

     **Enhetsmallar** är den plats där du skapar och hanterar egenskaperna för de enheter som ansluter till ditt program.

     **Med dataexport** kan du konfigurera en löpande export till externa tjänster, till exempel lagring och köer.

     **Administration** är den plats där du kan hantera programmets inställningar, anpassning, fakturering, användare och roller.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Sök, hjälp, tema och support

Den översta menyn visas på varje sida:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central verktygsfält":::

* Om du vill söka efter enheter anger du **ett sökvärde.**
* Om du vill ändra gränssnittsspråk eller tema väljer du **ikonen** Inställningar. Läs mer om [att hantera dina programinställningar](howto-manage-preferences.md)
* Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. Du kan [hämta information om ditt program](./howto-get-app-info.md) via länken Om **din** app. I ett program med den kostnadsfria prisplanen omfattar supportresurserna åtkomst till [livechatt](howto-show-hide-chat.md).
* Om du vill logga ut från programmet väljer du **ikonen** Konto.

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

> [!NOTE]
> Alternativet att välja mellan ljust och mörkt tema är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Skärmbild av IoT Central Välj ett tema.":::

### <a name="dashboard"></a>Instrumentpanel

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Skärmbild av IoT Central instrumentpanel.":::

* Instrumentpanelen är den första sidan som visas när du loggar in på ditt Azure IoT Central program. Du kan skapa och anpassa flera instrumentpaneler för program. Läs mer om att [lägga till paneler på instrumentpanelen](howto-add-tiles-to-your-dashboard.md)

* Personliga instrumentpaneler kan också skapas för att övervaka det du bryr dig om. Mer information finns i artikeln [Skapa Azure IoT Central personliga instrumentpaneler.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Enheter

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Skärmbild av sidan Enheter.":::

På utforskningssidan visas de _enheter_ som finns i ditt Azure IoT Central-program grupperade efter _enhetsmall_. 

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program.
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program.

Mer information finns i [snabbstarten Övervaka](./quick-monitor-devices.md) dina enheter. 

### <a name="device-groups"></a>Enhetsgrupper

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Sidan Enhetsgrupp":::

Enhetsgrupp är en samling relaterade enheter. Du använder enhetsgrupper för att utföra massåtgärder i ditt program. Mer information finns i artikeln [Använda enhetsgrupper i ditt Azure IoT Central program.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regler
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Skärmbild av sidan Regler.":::

På sidan Regler kan du definiera regler baserat på enheternas telemetri, tillstånd eller händelser. När en regel utlöses kan den utlösa en eller flera åtgärder – till exempel att skicka ett e-postmeddelande, meddela ett externt system via webhook-aviseringar osv. Mer information finns i [självstudien Konfigurera](tutorial-create-telemetry-rules.md) regler. 

### <a name="analytics"></a>Analys

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Skärmbild av sidan Analytics.":::

På analyssidan kan du visa telemetri från dina enheter grafiskt i en tidsserie. Mer information finns i artikeln [Skapa analys för ditt Azure IoT Central program.](howto-create-analytics.md)

### <a name="jobs"></a>Jobb

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Sidan Jobb":::

På sidan Jobb kan du köra massåtgärder på dina enheter. Du kan uppdatera enhetsegenskaper, inställningar och köra kommandon mot enhetsgrupper. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md).

### <a name="device-templates"></a>Enhetsmallar

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Skärmbild av enhetsmallar.":::

På sidan med enhetsmallar skapar och hanterar du enhetsmallarna i programmet. En enhetsmall anger enhetsegenskaper som:

* Telemetri-, tillstånds- och händelsemått
* Egenskaper
* Kommandon
* Vyer

Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](howto-set-up-template.md). 

### <a name="data-export"></a>Dataexport

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Dataexport":::

Med dataexport kan du konfigurera dataströmmar till externa system. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Skärmbild av IoT-administration.":::

På administrationssidan kan du konfigurera och anpassa IoT Central program. Här kan du ändra programnamn, URL, deming, hantera användare och roller, skapa API-token och exportera ditt program. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
