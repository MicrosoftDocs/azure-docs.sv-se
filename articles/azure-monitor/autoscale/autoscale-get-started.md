---
title: Kom igång med autoskalning i Azure
description: Lär dig hur du skalar din resurs-webbapp, moln tjänst, virtuell dator eller skalnings uppsättning för virtuella datorer i Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 081e06fbd6506c76a068d7207e29efb66502a9dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726061"
---
# <a name="get-started-with-autoscale-in-azure"></a>Kom igång med autoskalning i Azure
I den här artikeln beskrivs hur du konfigurerar inställningarna för autoskalning för resursen i Microsoft Azure-portalen.

Azure Monitor autoskalning gäller enbart för [skalnings uppsättningar för virtuella datorer](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web Apps](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Upptäck inställningarna för automatisk skalning i din prenumeration

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Du kan identifiera alla resurser för vilka automatisk skalning är tillämpligt i Azure Monitor. Använd följande steg för att göra en steg-för-steg-genom gång:

1. Öppna [Azure Portal.][1]
1. Klicka på ikonen Azure Monitor i det vänstra fönstret.
  ![Öppna Azure Monitor][2]
1. Klicka på **autoskalning** för att visa alla resurser för vilka autoskalning är tillämpligt, tillsammans med deras aktuella status för autoskalning.
  ![Upptäck autoskalning i Azure Monitor][3]

Du kan använda filter fönstret överst för att begränsa listan till att välja resurser i en speciell resurs grupp, vissa resurs typer eller en speciell resurs.

För varje resurs hittar du aktuella instans antal och status för autoskalning. Status för autoskalning kan vara:

- **Inte konfigurerad**: du har inte aktiverat autoskalning än för den här resursen.
- **Aktive rad**: du har aktiverat autoskalning för den här resursen.
- **Inaktive rad**: du har inaktiverat autoskalning för den här resursen.

## <a name="create-your-first-autoscale-setting"></a>Skapa din första inställning för autoskalning

Nu ska vi gå igenom en enkel steg-för-steg-genom gång för att skapa din första inställning för automatisk skalning.

1. Öppna bladet för **autoskalning** i Azure Monitor och välj en resurs som du vill skala. (Följande steg använder en App Service plan som är associerad med en webbapp. Du kan [skapa din första ASP.NET-webbapp i Azure på fem minuter.][4])
1. Observera att det aktuella instans antalet är 1. Klicka på **Aktivera autoskalning**.
  ![Skalnings inställning för ny webbapp][5]
1. Ange ett namn för skalnings inställningen och klicka sedan på **Lägg till en regel**. Observera de skalnings regel alternativ som öppnas som ett kontext fönster på den högra sidan. Som standard anger detta alternativet att skala antalet instanser med 1 om resursens procent andel överstiger 70 procent. Låt standardvärdena vara kvar och klicka på **Lägg till**.
  ![Skapa skalnings inställning för en webbapp][6]
1. Nu har du skapat din första skalnings regel. Observera att UX rekommenderar bästa praxis och anger att "det rekommenderas att ha minst en skala i regeln". Så här gör du:

    a. Klicka på **Lägg till en regel**.

    b. Ange **operatorn** till **mindre än**.

    c. Ange **tröskelvärdet** till **20**.

    d. Ange **åtgärden** för att **minska antalet med**.

   Nu bör du ha en skalnings inställning som skalar ut/skalar i baserat på CPU-användning.
   ![Skala baserat på CPU][8]
1. Klicka på **Spara**.

Grattis! Nu har du skapat din första skalnings inställning för att Autoskala din webbapp baserat på CPU-användning.

> [!NOTE]
> Samma steg gäller för att komma igång med en skalnings uppsättning för virtuella datorer eller en moln tjänst roll.

## <a name="other-considerations"></a>Ytterligare överväganden
### <a name="scale-based-on-a-schedule"></a>Skala baserat på ett schema
Förutom skalning baserat på CPU kan du ställa in din skala på olika sätt för vissa dagar i veckan.

1. Klicka på **Lägg till ett skalnings villkor**.
1. Om du ställer in skalnings läget och reglerna är samma som standard villkoret.
1. Välj **upprepa vissa dagar** för schemat.
1. Välj dagar och start-/slut tid för när skalnings villkoret ska tillämpas.

![Skalnings villkor baserat på schema][9]
### <a name="scale-differently-on-specific-dates"></a>Skala annorlunda på vissa datum
Förutom skalning baserat på CPU kan du ställa in skalan på olika sätt för vissa datum.

1. Klicka på **Lägg till ett skalnings villkor**.
1. Om du ställer in skalnings läget och reglerna är samma som standard villkoret.
1. Välj **Ange start-/slutdatum** för schemat.
1. Välj start-/slutdatum och start-/slut tid för när skalnings villkoret ska tillämpas.

![Skalnings villkor baserat på datum][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visa resursens skalnings historik
När din resurs skalas upp eller ned loggas en händelse i aktivitets loggen. Du kan visa skalnings historiken för din resurs under de senaste 24 timmarna genom att växla till fliken **körnings historik** .

![Körningshistorik][11]

Om du vill visa hela skalnings historiken (i upp till 90 dagar) väljer du **Klicka här för att visa mer information**. Aktivitets loggen öppnas med autoskalning förvald för din resurs och kategori.

### <a name="view-the-scale-definition-of-your-resource"></a>Visa skalnings definitionen för din resurs
Autoskalning är en Azure Resource Manager resurs. Du kan visa skalnings definitionen i JSON genom att växla till fliken **JSON** .

![Skalnings definition][12]

Du kan göra ändringar i JSON direkt, om det behövs. Ändringarna visas när du har sparat dem.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Inaktivera autoskalning och skala dina instanser manuellt
Det kan finnas tillfällen när du vill inaktivera den aktuella skalnings inställningen och manuellt skala din resurs.

Klicka på knappen **inaktivera autoskalning** överst.
![Inaktivera autoskalning][13]

> [!NOTE]
> Det här alternativet inaktiverar konfigurationen. Du kan dock gå tillbaka till den när du har aktiverat autoskalning igen.

Nu kan du ange antalet instanser som du vill skala till manuellt.

![Ange manuell skalning][14]

Du kan alltid återgå till autoskalning genom att klicka på **Aktivera autoskalning** och sedan **Spara**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Dirigera trafik till felfria instanser (App Service)

<a id="health-check-path"></a>

När din Azure-webbapp skalas ut till flera instanser kan App Service utföra hälso kontroller på dina instanser för att dirigera trafik till de felfria instanserna. Mer information finns i [den här artikeln om App Service hälso kontroll](../../app-service/monitor-instances-health-check.md).

## <a name="moving-autoscale-to-a-different-region"></a>Flytta autoskalning till en annan region
I det här avsnittet beskrivs hur du flyttar Azure autoskalning till en annan region under samma prenumeration och resurs grupp. Du kan använda REST API för att flytta inställningar för autoskalning.
### <a name="prerequisite"></a>Förutsättning
1. Se till att prenumerationen och resurs gruppen är tillgängliga och att informationen i både käll-och mål regionerna är identiska.
1. Se till att Azure Autoscale är tillgängligt i den [Azure-region som du vill flytta till](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Flytta
Använd [REST API](/rest/api/monitor/autoscalesettings/createorupdate) för att skapa en autoskalningsinställning i den nya miljön. Den autoskalningsinställning som skapats i mål regionen är en kopia av den automatiska skalnings inställningen i käll regionen.

Det går inte att flytta [diagnostikinställningar](../essentials/diagnostic-settings.md) som har skapats i Association med inställningen för autoskalning i käll regionen. Du måste återskapa diagnostikinställningar i mål regionen när du har skapat inställningarna för autoförsäljning. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Lär dig mer om att flytta resurser i Azure-regioner
Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitets logg avisering för att övervaka alla åtgärder för autoskalning av motorn i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitets logg avisering för att övervaka alla misslyckade skalnings-i/skala ut-åtgärder i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png