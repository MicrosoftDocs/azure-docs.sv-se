---
title: Övervaka en virtuell Azure-dator med Azure Monitor
description: Lär dig hur du samlar in och analyserar data för en virtuell Azure-dator i Azure Monitor.
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046932"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Snabb start: övervaka en virtuell Azure-dator med Azure Monitor
[Azure Monitor](../overview.md) börjar samla in data från virtuella Azure-datorer så snart de har skapats. I den här snabb starten tar du en kort genom gång av de data som samlas in automatiskt för en virtuell Azure-dator och hur du visar den i Azure Portal. Du kommer sedan att aktivera [VM Insights](../vm/vminsights-overview.md) för din virtuella dator som gör det möjligt för agenter på den virtuella datorn att samla in och analysera data från gäst operativ systemet, inklusive processer och deras beroenden.

För den här snabbstarten förutsätts det att du har en befintlig virtuell dator i Azure. Om du inte kan skapa en [virtuell Windows-dator](../../virtual-machines/windows/quick-create-portal.md) eller skapa en [virtuell Linux-dator](../../virtual-machines/linux/quick-create-cli.md) följer du snabb starten för den virtuella datorn.

Mer detaljerad information om övervaknings data som samlas in från Azure-resurser finns i [övervaka virtuella Azure-datorer med Azure Monitor](./monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Slutför övervakningen av en snabb start för Azure-resurser.
Slutför [övervakningen av en Azure-resurs med Azure Monitor](../essentials/quick-monitor-azure-resource.md) för att Visa översikts sidan, aktivitets loggen och måtten för en virtuell dator i din prenumeration. Virtuella Azure-datorer samlar in samma övervaknings data som andra Azure-resurser, men det är bara för den virtuella värddatorn. Resten av den här snabb starten fokuserar på att övervaka gäst operativ systemet och dess arbets belastningar.


## <a name="enable-vm-insights"></a>Aktivera VM Insights
Medan mått och aktivitets loggar samlas in för den virtuella värddatorn måste du ha en agent och viss konfiguration för att samla in och analysera övervaknings data från gäst operativ systemet och dess arbets belastningar. VM Insights installerar dessa agenter och ger ytterligare kraftfulla funktioner för övervakning av dina virtuella datorer.

1. Gå till menyn för den virtuella datorn.
2. Klicka antingen på **gå till insikter** från panelen på sidan **Översikt** eller klicka på **insikter** från menyn **övervakning** .

    ![Översiktssidan](media/quick-monitor-azure-vm/overview-insights.png)

3. Om VM Insights ännu inte har Aktiver ATS för den virtuella datorn klickar du på **Aktivera**. 

    ![Aktivera insikter](media/quick-monitor-azure-vm/enable-insights.png)

4. Om den virtuella datorn inte redan är kopplad till en Log Analytics arbets yta uppmanas du att välja en befintlig arbets yta eller skapa en ny. Välj standard som är en arbets yta med ett unikt namn i samma region som den virtuella datorn.

    ![Välj arbetsyta](media/quick-monitor-azure-vm/select-workspace.png)

5. Onboarding tar några minuter eftersom tillägg är aktiverade och agenter är installerade på den virtuella datorn. När det är klart får du ett meddelande om att insikter har distribuerats. Öppna VM Insights genom att klicka på **Azure Monitor** .

    ![Öppna Azure Monitor](media/quick-monitor-azure-vm/azure-monitor.png)

6. Du ser din virtuella dator med andra virtuella datorer i din prenumeration som har registrerats. Välj fliken **inte övervakad** om du vill visa virtuella datorer i din prenumeration som inte har publicerats.

    ![Kom igång](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurera arbetsyta
När du skapar en ny Log Analytics arbets yta måste den konfigureras för att samla in loggar. Den här konfigurationen behöver bara utföras en gång eftersom konfigurationen skickas till alla virtuella datorer som ansluter till den.

1. Välj **arbets ytans konfiguration** och välj sedan din arbets yta.

2. Välj **Avancerade inställningar**

    ![Avancerade inställningar i Log Analytics](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Insamling av data från virtuella Windows-datorer


2. Välj **Data** och sedan **Windows-händelseloggar**.

3. Lägg till en händelse logg genom att skriva in namnet på loggen.  Skriv **system** och välj sedan plus tecknet **+** .

4. Kontrollera allvarlighetsgraderna **Fel** och **Varning** i tabellen.

5. Spara konfigurationen genom att välja **Spara** längst upp på sidan.

### <a name="data-collection-from-linux-vm"></a>Insamling av data från virtuella Linux-datorer

1. Välj **data** och välj sedan **syslog**.

2. Lägg till en händelse logg genom att skriva in namnet på loggen.  Skriv **syslog** och välj sedan plus tecknet **+** .  

3. I tabellen avmarkerar du allvarlighets GRADS **information**, **meddelande** och **fel sökning**. 

4. Spara konfigurationen genom att välja **Spara** längst upp på sidan.

## <a name="view-data-collected"></a>Visa data som samlas in

7. Klicka på den virtuella datorn och välj sedan fliken **prestanda** under **övervaknings** menyn **insikter** . Visar en Välj grupp med prestanda räknare som har samlats in från gäst operativ systemet på den virtuella datorn. Rulla nedåt för att visa fler räknare och flytta musen över ett diagram för att Visa medelvärde och percentiler vid olika tidpunkter.

    ![Skärm bild som visar fönstret prestanda.](media/quick-monitor-azure-vm/performance.png)

9. Välj **Map** för att öppna funktionen Maps som visar de processer som körs på den virtuella datorn och deras beroenden. Välj **Egenskaper** för att öppna egenskaps rutan om den inte redan är öppen.

    ![Skärm bild som visar fönstret karta.](media/quick-monitor-azure-vm/map.png)

11. Expandera processerna för den virtuella datorn. Välj en av processerna för att visa dess information och för att markera dess beroenden.

    ![Skärm bild som visar fönstret karta med processer för en virtuell dator expanderad.](media/quick-monitor-azure-vm/processes.png)

12. Välj den virtuella datorn igen och välj sedan **logg händelser**. 

    ![Logg händelser](media/quick-monitor-azure-vm/log-events.png)

13. Du ser en lista över tabeller som lagras i Log Analytics arbets ytan för den virtuella datorn. Den här listan är olika beroende på om du använder en virtuell Windows-eller Linux-dator. Klicka på **händelse** tabellen. Detta inkluderar alla händelser från händelse loggen i Windows. Log Analytics öppnar med en enkel fråga för att hämta händelse logg poster.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du aktiverat VM Insights för en virtuell dator och konfigurerat Log Analytics-arbetsytan för att samla in händelser för gäst operativ systemet. Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](../logs/log-analytics-tutorial.md)