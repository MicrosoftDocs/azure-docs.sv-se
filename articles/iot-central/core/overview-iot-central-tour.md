---
title: Ta en rundtur i Azure IoT Central-användargränssnittet | Microsoft Docs
description: Bekanta dig med de viktigaste områdena i Azure IoT Central användargränssnitt som du använder för att skapa, hantera och använda din IoT-lösning.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: bf60f512416007137e71119fa7474b1393099ebf
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718889"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ta en rundtur i Azure IoT Central-användargränssnittet

Den här artikeln ger en Azure IoT Central användargränssnitt. Du kan använda användargränssnittet för att skapa, hantera och använda ett IoT Central program och dess anslutna enheter.

## <a name="iot-central-homepage"></a>IoT Central startsida

På [IoT Central webbplats](https://aka.ms/iotcentral-get-started) kan du läsa mer om de senaste nyheterna och funktionerna på IoT Central, skapa nya program och se och starta dina befintliga program.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central startsida":::

### <a name="create-an-application"></a>Skapa ett program

I avsnittet **Skapa** kan du bläddra i listan över branschrelev relevanta IoT Central mallar eller börja från grunden med hjälp av en anpassad appmall.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central byggsida":::

Mer information finns i [snabbstarten Skapa Azure IoT Central ett](quick-deploy-iot-central.md) program.

### <a name="launch-your-application"></a>Starta ditt program

Du startar programmet IoT Central genom att gå till den URL som du valde när du skapade appen. Du kan också se en lista över alla program som du har åtkomst till i [IoT Central app manager](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central apphanteraren":::

## <a name="navigate-your-application"></a>Navigera i ditt program

När du är i IoT-programmet använder du det vänstra fönstret för att få åtkomst till olika funktioner. Du kan expandera eller komprimera det vänstra fönstret genom att välja ikonen med tre ikoner överst i fönstret:

> [!NOTE]
> Vilka objekt som visas i det vänstra fönstret beror på din användarroll. Läs mer om [att hantera användare och roller.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="vänster fönsterruta":::

  :::column-end:::
  :::column span="2":::
     **Instrumentpanelen** visar alla program och personliga instrumentpaneler. 
     
     **Med** enheter kan du hantera alla dina enheter.

     **Med enhetsgrupper** kan du visa och skapa samlingar av enheter som anges av en fråga. Enhetsgrupper används via programmet för att utföra massåtgärder.

     **Med** regler kan du skapa och redigera regler för att övervaka dina enheter. Regler utvärderas baserat på enhetsdata och utlöser anpassningsbara åtgärder.

     **Analytics** visar omfattande funktioner för att analysera historiska trender och korrelera olika telemetrier från dina enheter.

     **Med** jobb kan du hantera dina enheter i stor skala genom att köra massåtgärder.

     **Med enhetsmallar** kan du skapa och hantera egenskaperna för enheter som ansluter till ditt program.

     **Med dataexport** kan du konfigurera en löpande export till externa tjänster, till exempel lagring och köer.

     **Med** administration kan du hantera programmets inställningar, anpassning, fakturering, användare och roller.

     **Mina appar** kan du gå tillbaka till IoT Central apphanteraren.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Sök, hjälp, tema och support

Den översta menyn visas på varje sida:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central verktygsfält":::

* Om du vill söka efter enheter anger du ett **sökvärde.**
* Om du vill ändra språk eller tema för användargränssnittet väljer du **ikonen** Inställningar. Läs mer om [att hantera programinställningar](howto-manage-preferences.md)
* Om du behöver hjälp och support väljer du listrutan **Hjälp** för att visa en lista med resurser. Du kan [hämta information om ditt program](./howto-get-app-info.md) via länken Om **din** app. I ett program med den kostnadsfria prisplanen omfattar supportresurserna åtkomst till [livechatt](howto-show-hide-chat.md).
* Om du vill logga ut från programmet väljer du **ikonen** Konto.

Du kan välja mellan ett ljust eller ett mörkt tema för användargränssnittet:

> [!NOTE]
> Alternativet att välja mellan ljusa och mörka teman är inte tillgängligt om administratören har konfigurerat ett anpassat tema för programmet.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Skärmbild av IoT Central Välj ett tema.":::

### <a name="dashboard"></a>Instrumentpanel

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Skärmbild av IoT Central instrumentpanel.":::

* **Instrumentpanelen** är den första sidan som visas när du loggar in på IoT Central program. Du kan skapa och anpassa flera instrumentpaneler för program. Läs mer om att [lägga till paneler på instrumentpanelen](howto-add-tiles-to-your-dashboard.md)

* Personliga instrumentpaneler kan också skapas för att övervaka det du bryr dig om. Mer information finns i artikeln [Skapa Azure IoT Central personliga instrumentpaneler.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Enheter

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Skärmbild av sidan Enheter.":::

Den här sidan visar enheterna i IoT Central program grupperade efter _enhetsmall_.

* En enhetsmall definierar en typ av enhet som kan ansluta till ditt program.
* En enhet motsvarar antingen en verklig eller simulerad enhet i ditt program.

Mer information finns i [snabbstarten Övervaka](./quick-monitor-devices.md) dina enheter. 

### <a name="device-groups"></a>Enhetsgrupper

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Sidan Enhetsgrupp":::

På den här sidan kan du skapa och visa enhetsgrupper i IoT Central program. Du kan använda enhetsgrupper för att göra massåtgärder i ditt program eller för att analysera data. Mer information finns i artikeln [Använda enhetsgrupper i ditt Azure IoT Central program.](tutorial-use-device-groups.md)

### <a name="rules"></a>Regler
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Skärmbild av sidan Regler.":::

På den här sidan kan du visa och skapa regler baserat på enhetsdata. När en regel utlöses kan den utlösa en eller flera åtgärder, till exempel att skicka ett e-postmeddelande eller aktivera en webhook. Mer information finns i [självstudien Konfigurera](tutorial-create-telemetry-rules.md) regler.

### <a name="analytics"></a>Analys

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Skärmbild av sidan Analytics.":::

Analys visar omfattande funktioner för att analysera historiska trender och korrelera olika telemetri från dina enheter. Mer information finns i artikeln [Skapa analys för ditt Azure IoT Central program.](howto-create-analytics.md)

### <a name="jobs"></a>Jobb

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Sidan Jobb":::

På den här sidan kan du visa och skapa jobb som kan användas för massåtgärder för enhetshantering på dina enheter. Du kan uppdatera enhetsegenskaper, inställningar och köra kommandon mot enhetsgrupper. Mer information finns i artikeln [Köra ett jobb](howto-run-a-job.md).

### <a name="device-templates"></a>Enhetsmallar

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Skärmbild av enhetsmallar.":::

På sidan med enhetsmallar kan du visa och skapa enhetsmallar i programmet. Mer information finns i självstudien om att [definiera en ny enhetstyp i Azure IoT Central-programmet](howto-set-up-template.md).

### <a name="data-export"></a>Dataexport

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Dataexport":::

Med dataexport kan du konfigurera dataströmmar till externa system. Mer information finns i artikeln om att [exportera dina data i Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Skärmbild av IoT-administration.":::

På administrationssidan kan du konfigurera och anpassa IoT Central program. Här kan du ändra programnamn, URL, deming, hantera användare och roller, skapa API-token och exportera ditt program. Mer information finns i artikeln om att [administrera ditt Azure IoT Central-program](howto-administer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Azure IoT Central och är bekant med layouten i användargränssnittet så föreslår vi att du slutför snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
