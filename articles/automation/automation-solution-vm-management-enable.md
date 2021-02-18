---
title: Aktivera Azure Automation Starta/stoppa virtuella datorer när de inte används
description: Den här artikeln beskriver hur du aktiverar Starta/stoppa virtuella datorer när de inte används funktionen för dina virtuella Azure-datorer.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593905"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Aktivera Starta/stoppa virtuella datorer när de inte används

Utför stegen i det här avsnittet i tur och ordning för att aktivera Starta/stoppa virtuella datorer när de inte används funktionen för virtuella datorer med ett nytt eller befintligt Automation-konto och länkade Log Analytics-arbetsytan. När du har slutfört installations processen konfigurerar du variablerna för att anpassa funktionen.

>[!NOTE]
>Om du vill använda den här funktionen med klassiska virtuella datorer behöver du ett klassiskt kör som-konto, som inte skapas som standard. Se [skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Skapa resurser för funktionen

1. Logga in på Azure- [portalen](https://portal.azure.com).
2. Sök efter och välj **Automation-konton**.
3. På sidan Automation-konton väljer du ditt Automation-konto i listan.
4. Från Automation-kontot väljer du **Starta/stoppa virtuell dator** under **relaterade resurser**. Härifrån kan du klicka på **Läs mer om och aktivera lösningen**. Om du redan har distribuerat funktionen kan du klicka på **Hantera lösningen** och söka efter den i listan.

   ![Aktivera från Automation-konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa resursen från valfri plats i Azure Portal genom att klicka på **skapa en resurs**. På sidan Marketplace skriver du ett nyckelord, till exempel **Starta** eller **Starta/stoppa**. När du börjar skriva filtreras listan baserat på det du skriver. Alternativt kan du skriva in ett eller flera nyckelord från det fullständiga namnet på funktionen och trycka på **RETUR**. Välj **Starta/stoppa virtuella datorer när de inte används** från Sök resultaten.

5. På sidan Starta/stoppa virtuella datorer när de inte används för den valda distributionen granskar du sammanfattnings informationen och klickar sedan på **skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Konfigurera funktionen

När resursen har skapats visas sidan Lägg till lösning. Du uppmanas att konfigurera funktionen innan du kan importera den till din Automation-prenumeration. Se [konfigurera starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management-config.md).

   ![Sidan för att lägga till hantering av virtuella datorer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Välj en Log Analytics arbets yta

1. På sidan Lägg till lösning väljer du **arbets yta**. Välj en Log Analytics arbets yta som är länkad till den Azure-prenumeration som används av Automation-kontot. 

2. Om du inte har en arbets yta väljer du **Skapa ny arbets yta**. Utför följande steg på sidan Log Analytics arbets yta:

   - Ange ett namn för den nya Log Analytics-arbetsytan, till exempel **ContosoLAWorkspace**.
   - Välj en **prenumeration** som du vill länka till genom att välja i list rutan, om det valda standardvärdet inte är tillämpligt.
   - För **resurs grupp** kan du skapa en ny resurs grupp eller välja en befintlig.
   - Välj en **plats**.
   - Välj en **pris nivå**. Välj alternativet **per GB (fristående)** . Azure Monitors loggar har uppdaterat [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och nivån per GB är det enda alternativet.

   > [!NOTE]
   > När du aktiverar funktioner stöds bara vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

3. När du har angett nödvändig information på sidan Log Analytics arbets yta klickar du på **skapa**. Du kan följa förloppet under **meddelanden** på menyn, som återgår till sidan Lägg till lösning när du är färdig.

## <a name="add-automation-account"></a>Lägg till Automation-konto

Öppna sidan Lägg till lösning igen och välj **Automation-konto**. Du kan välja ett befintligt Automation-konto som inte redan är kopplat till en Log Analytics-arbetsyta. Om du skapar en ny Log Analytics arbets yta kan du skapa ett nytt Automation-konto som du vill koppla till det. Välj ett befintligt Automation-konto eller klicka på **skapa ett Automation-konto** och ange namnet på Automation-kontot i fältet **namn** på sidan Lägg till Automation-konto.

Alla andra alternativ fylls i automatiskt baserat på den Log Analytics arbets ytan som valts. Du kan inte ändra de här alternativen. Ett Kör som-konto i Azure är standard metoden för autentisering för Runbooks som ingår i funktionen. 

När du klickar på **OK** verifieras konfigurations alternativen och automation-kontot skapas. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="define-feature-parameters"></a>Definiera funktions parametrar

1. På sidan Lägg till lösning väljer du **konfiguration**. Sidan parametrar visas.

    ![Parameter sidan för lösningen](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Ange ett värde för fältet **mål ResourceGroup namn** . Fältet definierar grupp namn som innehåller virtuella datorer för den funktion som ska hanteras. Du kan ange mer än ett namn och avgränsa namnen med kommatecken (värdena är inte Skift läges känsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Värdena lagras i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variablerna och.

    > [!IMPORTANT]
    > Standardvärdet för **mål ResourceGroup namn** är ett **&ast;** . Inställningen riktar sig mot alla virtuella datorer i en prenumeration. Om du inte vill att funktionen ska rikta in sig på alla virtuella datorer i din prenumeration måste du ange en lista över resurs grupp namn innan du väljer ett schema.
  
3. Ange ett värde för fältet **undantags lista för virtuell dator (sträng)** . Det här värdet är namnet på en eller flera virtuella datorer från mål resurs gruppen. Du kan ange mer än ett namn och avgränsa namnen med kommatecken (värdena är inte Skift läges känsliga). Användning av jokertecken stöds. Det här värdet lagras i `External_ExcludeVMNames` variabeln.
  
4. Använd fältet **schema** för att välja ett schema för hantering av virtuella datorer med funktionen. Välj start datum och-tid för ditt schema för att skapa ett återkommande dagligt schema med början vid den valda tiden. Det går inte att välja en annan region. Information om hur du konfigurerar schemat till din speciella tidszon när du har konfigurerat funktionen finns i [ändra scheman för start och avstängning](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Om du vill ta emot e-postaviseringar från en [Åtgärds grupp](../azure-monitor/alerts/action-groups.md)accepterar du standardvärdet **Ja** i fältet  **e-postaviseringar** och anger en giltig e-postadress. Om du väljer **Nej** men väljer ett senare datum som du vill ta emot e-postaviseringar kan du uppdatera åtgärds gruppen som skapas med giltiga e-postadresser avgränsade med kommatecken. 

6. Aktivera följande aviserings regler:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Skapa aviseringar

Starta/stoppa virtuella datorer när de inte används innehåller inte en fördefinierad uppsättning aviseringar. Granska [Skapa logg aviseringar med Azure Monitor](../azure-monitor/alerts/alerts-log.md) för att lära dig hur du skapar jobb misslyckade aviseringar som stöder dina DevOps eller operativa processer och procedurer.

## <a name="deploy-the-feature"></a>Distribuera funktionen

1. När du har konfigurerat de ursprungliga inställningar som krävs för funktionen klickar du på **OK** för att stänga sidan parametrar.

2. Klicka på **Skapa**. När alla inställningar har verifierats distribuerar funktionen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan följa förloppet under **meddelanden** på menyn.

    > [!NOTE]
    > Om du har en prenumeration på Azure Cloud Solution Provider (Azure CSP) efter att distributionen har slutförts går du till **variabler** under **delade resurser** i ditt Automation-konto och anger [External_EnableClassicVMs](automation-solution-vm-management.md#variables) variabeln till **falskt**. Detta stoppar lösningen från att leta efter klassiska VM-resurser.

## <a name="next-steps"></a>Nästa steg

* Om du vill ställa in funktionen läser du [Konfigurera stoppa/starta virtuella datorer under låg belastning](automation-solution-vm-management-config.md).
* Information om hur du löser funktions fel finns i [felsöka starta/stoppa virtuella datorer när de inte används problem](troubleshoot/start-stop-vm.md).