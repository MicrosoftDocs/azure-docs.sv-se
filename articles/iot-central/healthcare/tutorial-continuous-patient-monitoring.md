---
title: Självstudie – Skapa en kontinuerlig app för patient övervakning med Azure IoT Central | Microsoft Docs
description: I den här självstudien får du lära dig att bygga ett kontinuerligt program för patient övervakning med hjälp av Azure IoT Central programmallar.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719108"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Självstudie: Distribuera och genom gång av en kontinuerlig app-mall för patient övervakning

Den här självstudien visar dig som Solution Builder och hur du kommer igång genom att distribuera en IoT Central kontinuerlig övervaknings program mal len för patienter. Du lär dig hur du distribuerar och använder mallen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Programmall
> * Gå igenom program mal len

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration rekommenderas. Du kan också använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Skapa en Programmall

Gå till [webbplatsen för Azure IoT Central Application Manager](https://apps.azureiotcentral.com/). Välj **Bygg** i det vänstra navigerings fältet och välj sedan fliken **hälso vård** .

:::image type="content" source="media/app-manager-health.png" alt-text="Healthcre app-mall":::

Klicka på knappen **skapa app** för att börja skapa ditt program och logga sedan in med ett Microsoft personal-, arbets-eller skol konto. Det tar dig till sidan **nytt program** .

![Skapa program hälso vård](media/app-manager-health-create.png)

![Skapa fakturerings information för program hälso vård](media/app-manager-health-create-billinginfo.png)

Så här skapar du ditt program:

1. Azure IoT Central föreslår automatiskt ett program namn baserat på mallen som du har valt. Du kan acceptera namnet eller ange ett eget vänligt program namn, till exempel **kontinuerlig övervakning av patienter**. Azure IoT Central genererar också ett unikt URL-prefix åt dig baserat på programmets namn. Du är kostnads fri att ändra detta URL-prefix till något mer minnes värt om du vill.

2. Du kan välja om du vill skapa programmet med hjälp av den *kostnads fria* pris planen eller någon av *standard* pris avtalen. Program som du skapar med hjälp av den kostnads fria planen är kostnads fria i sju dagar innan de går ut och ger upp till fem kostnads fria enheter. Du kan när som helst flytta ett program från den kostnads fria planen till en standard pris plan innan den upphör att gälla. Om du väljer den kostnads fria planen måste du ange din kontakt information och välja om du vill få information och tips från Microsoft. Program som du skapar med hjälp av en standard plan stöder upp till två kostnads fria enheter och kräver att du anger din prenumerations information för Azure för fakturering.

3. Klicka på **skapa** längst ned på sidan för att distribuera programmet.

## <a name="walk-through-the-application-template"></a>Gå igenom program mal len

### <a name="dashboards"></a>Instrumentpaneler

När du har distribuerat app-mallen hamnar du först på **lamna instrument panel för patient övervakning**. Lamna sjukvård är ett fiktivt sjukhus system som innehåller två sjukhus: Sparbanken sjukhus och Burkville sjukhus. På sjukhusets instrument panel kan du:

* Se telemetri och egenskaper för enheten, till exempel enhetens **batteri nivå** eller dess **anslutnings** status.

* Visa **våningsplanet** och platsen för den smarta viktigare korrigerings enheten.

* **Reetablering** av den smarta viktiga korrigerings filen för en ny patient.

* Se ett exempel på en **instrument panel** för en leverantör som ett sjukhus lag kan se för att spåra sina patienter.

* Ändra enhetens **patient status** för att ange om enheten används för ett pågående eller ett fjärrscenario.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Status för patienter":::

Du kan också välja **gå till instrument panelen för en fjärran sluten patient** för att se instrument panelen för Burkville sjukhus. Den här instrument panelen innehåller en liknande uppsättning åtgärder, telemetri och information. Du kan också se flera enheter som används och välja att **Uppdatera den inbyggda program varan** på var och en.

:::image type="content" source="media/lamna-remote.png" alt-text="Instrument panel för fjärr operatör":::

### <a name="device-templates"></a>Enhetsmallar

Om du väljer **enhets mallar** visas de två enhets typerna i mallen:

* **Smart Vital-korrigering**: den här enheten representerar en korrigering som mäter olika viktiga tecken. Den används för att övervaka patienter i och utanför sjukhus. Om du väljer mallen ser du att korrigerings filen skickar både enhets data, till exempel batteri nivå och enhets temperatur samt patient hälso data som andnings frekvens och blod tryck.

* **Smart Knee-klammer**: den här enheten representerar en Knee-klammer som patienter använder vid återställning från en Knee-reoperation. Om du väljer den här mallen visas funktioner som enhets data, rörelse intervall och acceleration.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Mall för smart korrigering":::

### <a name="device-groups"></a>Enhetsgrupper

Använd enhets grupper för att logiskt gruppera en uppsättning enheter och sedan köra Mass frågor eller åtgärder på dem.

Om du väljer fliken enhets grupper visas en standard enhets grupp för varje enhets mall i programmet. Dessutom skapas två ytterligare exempel enhets grupper som kallas **etablera enheter** och **enheter med inaktuell inbyggd program vara**. Du kan använda dessa exempel enhets grupper som indata för att köra vissa av [jobben](#jobs) i programmet.

### <a name="rules"></a>Regler

Om du väljer **regler** visas de tre reglerna i mallen:

* **Klammerparentes temperatur hög**: den här regeln utlöses när den smarta Knee-klammerns enhets temperatur är större än 95 &deg; F över ett 5-minuters fönster. Använd den här regeln för att avisera patient-och sjukvårds teamet och fjärrlagra enheten.

* **Identifierad**: den här regeln utlöses om en patient sjunker. Använd den här regeln om du vill konfigurera en åtgärd för att distribuera ett drifts team för att hjälpa patienten som har fallit.

* **Korrigerings batteri låg**: den här regeln utlöses när batteri nivån på enheten hamnar under 10%. Använd den här regeln för att utlösa en avisering till patienten för att debitera enheten.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regler":::

### <a name="jobs"></a>Jobb

Med jobb kan du köra Mass åtgärder på en uppsättning enheter, med hjälp av [enhets grupper](#device-groups) som inmatade. Program mal len har två exempel jobb som en operatör kan köra:

* **Uppdatera Knee-inbyggd program vara**: det här jobbet söker efter enheter i enhets grupps **enheter med inaktuell inbyggd program vara** och kör ett kommando för att uppdatera enheterna till den senaste versionen av den inbyggda program varan Detta exempel jobb förutsätter att enheterna kan hantera ett **uppdaterings** kommando och sedan hämta de inbyggda program vara från molnet.  

* **Reprovision-enheter**: du har en uppsättning enheter som nyligen har returnerats till sjukhus. Det här jobbet hittar enheter i enhets gruppen **etablera enheter** och kör ett kommando för att etablera om dem för nästa uppsättning patienter.

### <a name="devices"></a>Enheter

Välj fliken **enheter** och välj sedan en instans av den **smarta Knee-klammern**. Det finns tre vyer för att utforska information om den enhet som du har valt. Dessa vyer skapas och publiceras när du skapar enhets mal len för enheten. Därför är dessa vyer konsekventa för alla enheter som du ansluter eller simulerar.

I vyn **instrument panel** får du en översikt över dubbelriktad telemetri och egenskaper från enheten.

På fliken **Egenskaper** kan du redigera moln egenskaper och egenskaper för läsa/skriva enhet.

På fliken **kommandon** kan du köra kommandon på enheten.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Knee-klammer":::

### <a name="data-export"></a>Dataexport

Med data export kan du exportera enhets data kontinuerligt till andra Azure-tjänster, inklusive [Azure API för FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort programmet genom att gå till **Administration > program inställningar** och klicka på **ta bort**.

:::image type="content" source="media/admin-delete.png" alt-text="Städade resurser":::

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar en instrument panel för provider som ansluter till ditt IoT Central-program.

> [!div class="nextstepaction"]
> [Bygg en instrument panel för Provider](tutorial-health-data-triage.md)