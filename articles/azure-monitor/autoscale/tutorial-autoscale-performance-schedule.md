---
title: Skala Azure-resurser automatiskt baserat på data eller schema
description: Skapa en autoskalningsinställning för en App Service-plan med hjälp av mätvärden och ett schema
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100628294"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Skapa en autoskalningsinställning för Azure-resurser baserat på prestanda eller ett schema

Med autoskalningsinställningar kan du lägga till/ta bort instanser av tjänster baserat på förinställda villkor. Du kan skapa de här inställningarna via portalen. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera en autoskalningsinställning. 

I den här självstudien kommer du att: 
> [!div class="checklist"]
> * skapa en webbapp i och en App Service-plan
> * konfigurera autoskalningsregler för ut- och inskalning baserat på antalet förfrågningar som en webbapp tar emot
> * utlösa en utskalningsåtgärd och se antalet förekomster öka
> * utlösa en inskalningsåtgärd och se antalet förekomster minska
> * Rensa dina resurser

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>skapa en webbapp i och en App Service-plan
1. Klicka på alternativet **Skapa en resurs** i det vänstra navigeringsfönstret.
2. Sök efter och välj objektet *Webbapp* och klicka på **Skapa**.
3. Välj ett appnamn, som *MyTestScaleWebApp*. Skapa den nya resursgruppen ”myResourceGroup” eller placera den i valfri resursgrupp.

Dina resurser bör etableras på några minuter. Använd webbappen och motsvarande App Service-plan i resten av den här självstudien.

   ![Skapa en ny App Service](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navigera till inställningarna för automatisk skalning
1. Välj alternativet **Övervaka** i det vänstra navigeringsfönstret. När sidan har lästs in väljer du fliken **Autoskalning**.
2. Här visas en lista med de resurser i din prenumeration som har stöd för automatisk skalning. Identifiera den App Service-plan du skapade tidigare i självstudien och klicka på den.

    ![Skärm bild som visar Azure Portal med bildskärm och sedan autoskalning markerat.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Klicka på knappen **Aktivera autoskalning** i autoskalningsinställningarna.

I följande steg får du hjälp att fylla autoskalningsskärmen så att den ser ut som på bilden nedan:

   ![Spara autoskalningsinställningen](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Konfigurera en standardprofil
1. Ange ett **Namn** för autoskalningsinställningen.
2. Se till att **Skalningsläge** är inställt på ”Skala till ett specifikt antal instanser” i standardprofilen.
3. Ange antalet instanser till **1**. Den här inställningen garanterar att när inga andra profiler är aktiva återställer standardprofilen instansantalet till 1.

   ![Skärm bild som visar sidan för autoskalning med ett namn som har angetts för inställningen.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Skapa profil för upprepning

1. Klicka på länken **Add a scale condition** (Lägg till ett skalningsvillkor) under standardprofilen.

2. Redigera fältet **Namn** för profilen till ”Monday to Friday profile”.

3. Se till att **Skalningsläge** är satt till Skala baserat på ett mått.

4. För **Instansgränser** anger du 1 för **Minst**, 2 för **Maximalt** och 1 som **Standard**. Den här inställningen garanterar att profilen inte automatiskt skalar om Service-planen så att den har färre än 1 instans eller fler än 2 instanser. Om profilen inte har tillräckligt med data för ett beslut används standardantalet instanser (i det här fallet 1).

5. Välj Upprepa specifika dagar för **Schema**.

6. Ange att profilen ska upprepas måndag-fredag från 09.00 PST till 18.00 PST. Den här inställningen garanterar att profilen bara är aktiv och giltig från 09.00 till 18:00, måndag-fredag. Vid andra tider är det standardprofilen som används för autoskalningsinställningar.

## <a name="create-a-scale-out-rule"></a>Skapa en utskalningsregel

1. I ”Monday to Friday profile”.

2. Klicka på länken **Lägg till en regel**.

3. Ange Annan resurs som **Måttkälla**. Sätt **Resurstyp** till App Services och **Resurs** till webbappen du skapade tidigare i självstudien.

4. Sätt **Tidsmängd** till Summa, **Måttnamn** till Förfrågningar och **Tidsintervallstatistik** till Summa.

5. Sätt **Operator** till Större än, **Tröskelvärde** till 10 och **Varaktighet** till 5 minuter.

6. Sätt **Åtgärd** till Öka antal med, **Antal instanser** till 1 och **Nedkylningstid** till 5 minuter.

7. Klicka på knappen **Lägg till** .

Den här regeln säkerställer att om din webbapp tar emot fler än 10 förfrågningar inom 5 minuter eller mindre så läggs ytterligare en instans till i din App Service-plan för att hantera belastningen.

   ![Skapa en utskalningsregel](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Skapa en inskalningsregel
Vi rekommenderar att du alltid använder en inskalningsregel tillsammans med en utskalningsregel. Om du använder båda så undviker du att överetablera resurser. Överetablering innebär att du har flera instanser igång än vad som behövs för att hantera den aktuella belastningen. 

1. I ”Monday to Friday profile”.

2. Klicka på länken **Lägg till en regel**.

3. Ange Annan resurs som **Måttkälla**. Sätt **Resurstyp** till App Services och **Resurs** till webbappen du skapade tidigare i självstudien.

4. Sätt **Tidsmängd** till Summa, **Måttnamn** till Förfrågningar och **Tidsintervallstatistik** till Genomsnitt.

5. Sätt **Operator** till Mindre än, **Tröskelvärde** till 5 och **Varaktighet** till 5 minuter.

6. Sätt **Åtgärd** till Minska antal med, **Antal instanser** till 1 och **Nedkylningstid** till 5 minuter.

7. Klicka på knappen **Lägg till** .

    ![Skapa en inskalningsregel](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Spara** autoskalningsinställningen.

    ![Spara autoskalningsinställningen](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Lös ut utskalningsåtgärden
Om utskalningsvillkoret i inställningen vi just skapade ska lösas ut måste webbappen ta emot fler än 10 förfrågningar på mindre än 5 minuter.

1. Öppna ett webbläsarfönster och navigera till den webbapp du skapade tidigare i självstudien. Du hittar URL:en för webbappen i Azure Portal genom att gå till Web App-resursen och klicka på knappen **Bläddra** på fliken Översikt.

2. Läs in sidan fler än 10 gånger i snabb följd.

3. Välj alternativet **Övervaka** i det vänstra navigeringsfönstret. När sidan har lästs in väljer du fliken **autoskalning** .

4. Välj App Service-planen du använt i den här självstudien från listan.

5. Klicka på fliken **Körningshistorik** i autoskalningsinställningarna.

6. Du ser ett diagram som återger antalet instanser för App Service-planen över tid.

7. Om några minuter bör instansantalet öka från 1 till 2.

8. Under diagrammet visas aktivitetsloggposter för varje skalningsåtgärd som utförs med den här autoskalningsinställningen.

## <a name="trigger-scale-in-action"></a>Lös ut inskalningsåtgärden
Inskalningsvillkoret i inställningen löses ut om webbappen tar emot färre än 5 förfrågningar under 5 minuter. 

1. Kontrollera att inga förfrågningar skickas till webbappen.

2. Läs in Azure Portal.

3. Välj alternativet **Övervaka** i det vänstra navigeringsfönstret. När sidan har lästs in väljer du fliken **autoskalning** .

4. Välj App Service-planen du använt i den här självstudien från listan.

5. Klicka på fliken **Körningshistorik** i autoskalningsinställningarna.

6. Du ser ett diagram som återger antalet instanser för App Service-planen över tid.

7. Om några minuter bör instansantalet minska från 2 till 1. Processen tar minst 100 minuter.  

8. Under diagrammet visas motsvarande uppsättning aktivitetsloggposter för varje skalningsåtgärd som utförs med den här autoskalningsinställningen.

    ![Visa inskalningsåtgärder](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Rensa resurser

1. Klicka på **Alla resurser** på menyn till vänster på Azure Portal och välj sedan webbappen du skapade i den här självstudien.

2. Klicka på **Ta bort** på resurssidan, bekräfta borttagningen genom att skriva **Ja** i textrutan och klicka sedan på **Ta bort**.

3. Välj App Service-planresursen och klicka på **Ta bort**.

4. Bekräfta borttagningen genom att skriva **Ja** i textrutan och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du:  
> [!div class="checklist"]
> * skapat en webbapp och en App Service-plan
> * konfigurerat autoskalningsregler för ut- och inskalning baserat på antalet förfrågningar som en webbapp tar emot
> * löst ut en utskalningsåtgärd och sett antalet förekomster öka
> * löst ut en inskalningsåtgärd och sett antalet förekomster minska
> * rensat dina resurser.


Om du vill veta mer om autoskalningsinställningar kan du fortsätta till [översikten över automatisk skalning](../autoscale/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Arkivera dina övervakningsdata](../essentials/platform-logs-overview.md)

