---
title: Konfigurera aviseringar och arbeta med mått i Azure VMware-lösningen
description: Lär dig hur du använder aviseringar för att ta emot meddelanden. Lär dig också hur du arbetar med mått för att få djupare insikt i ditt privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: f021662658399111187e9963fc5caec434fabf4a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096841"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Konfigurera Azure-aviseringar i Azure VMware-lösning 

I den här artikeln får du lära dig hur du konfigurerar [Azure-åtgärds grupper](/azure/azure-monitor/alerts/action-groups) i [Microsoft Azure aviseringar](/azure/azure-monitor/alerts/alerts-overvie) för att ta emot meddelanden om utlösta händelser som du definierar. Du lär dig också att använda [Azure Monitor mått](/azure/azure-monitor/essentials/data-platform-metrics) för att få djupare insikt i ditt privata moln i Azure VMware-lösningen.


## <a name="supported-metrics-and-activities"></a>Mått och aktiviteter som stöds

Följande mått visas genom Azure Monitor mått.

| **Signal namn**                                                         | **Signal typ** | **Övervaka tjänst** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Total kapacitet för data lager disk                                           | Metric          | Plattform            |
| Procent data lager disk som används                                          | Metric          | Plattform            |
| Processorprocentandel                                                          | Metric          | Plattform            |
| Genomsnittligt effektivt minne                                                | Metric          | Plattform            |
| Genomsnittlig minnes kapacitet                                                 | Metric          | Plattform            |
| Genomsnittligt totalt minne                                                    | Metric          | Plattform            |
| Genomsnittlig minnes användning                                                    | Metric          | Plattform            |
| Använd data lager disk                                                     | Metric          | Plattform            |
| Alla administrativa åtgärder                                           | Aktivitetslogg    | Administrativ      |
| Registrera Microsoft. AVS Resource Provider. (Microsoft. AVS/privateClouds) | Aktivitetslogg    | Administrativ      |
| Skapa eller uppdatera en PrivateCloud. (Microsoft. AVS/privateClouds)          | Aktivitetslogg    | Administrativ      |
| Ta bort en PrivateCloud. (Microsoft. AVS/privateClouds)                    | Aktivitetslogg    | Administrativ      |

## <a name="configure-an-alert-rule"></a>Konfigurera en varnings regel
1. Från ditt privata moln i Azure VMware-lösningen väljer du **övervaknings**  >  **aviseringar** och sedan **ny varnings regel**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Skärm bild som visar var du kan konfigurera en varnings regel i ditt privata moln i Azure VMware-lösningen." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   En ny konfigurations skärm öppnas där du kommer att:
   - Definiera omfattningen
   - Konfigurera ett villkor
   - Konfigurera åtgärds gruppen
   - Definiera aviserings regel informationen
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Skärm bild som visar fönstret Skapa aviserings regel." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. Under **omfång** väljer du den mål resurs som du vill övervaka. Som standard har Azure VMware-lösningens privata moln från den plats där du öppnade aviserings menyn definierats.

1. Under **villkor** väljer du **Lägg till villkor**, och i fönstret som öppnas väljer du den signal som du vill skapa för varnings regeln. 

   I vårt exempel har vi valt **procent data lager disk som används**, vilket är relevant från ett [Azure VMware-lösningens SLA](https://aka.ms/avs/sla) -perspektiv. 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Skärm bild som visar fönstret Konfigurera signal logik med fördefinierade signal namn."::: 

1. Definiera den logik som ska utlösa aviseringen och välj sedan **slutförd**. 

   I vårt exempel har endast **tröskelvärdet** och **frekvensen för utvärderingen** justerats. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Skärm bild som visar informationen för den valda signal logiken."::: 

1. Under **åtgärder** väljer du **Lägg till åtgärds grupper**. Åtgärds gruppen definierar *hur* meddelandet tas emot och *vem som* tar emot det.   Du kan ta emot aviseringar via e-post, SMS, [Azure Mobile App-push-meddelanden](https://azure.microsoft.com/features/azure-portal/mobile-app/) eller röst meddelanden.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Skärm bild som visar de befintliga åtgärds grupperna och var du skapar en ny åtgärds grupp.":::

1. I fönstret som öppnas väljer du **skapa åtgärds grupp**.

   >[!TIP]
   > Du kan också använda en befintlig åtgärds grupp.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Skärm bild som visar de åtgärds grupper som ska väljas för aviseringen."::: 

 

 
1. I fönstret som öppnas går du till fliken **grundläggande** och ger åtgärds gruppen ett namn och ett visnings namn.

1. Välj fliken **meddelanden** , Välj en **meddelande typ** och ett **namn**. Välj sedan **OK**.

   Vårt exempel baseras på e-postavisering.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Skärm bild som visar e-post, SMS-meddelanden, push-och röst inställningar för aviseringen." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Valfritt Konfigurera **åtgärder** om du vill vidta förebyggande åtgärder och få meddelanden om händelsen. Välj en tillgänglig **Åtgärds typ** och välj sedan **Granska + skapa**. 
   - Automation Runbook-rutiner
   - Azure Functions – för anpassad händelse driven kod körning utan Server
   - ITSM – integrera med en tjänst leverantör som ServiceNow för att skapa en biljett
   - Logic app – för mer komplex arbets flödes dirigering
   - Webhooks – för att utlösa en process i en annan tjänst

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Skärm bild som visar fönstret Skapa åtgärds grupp med fokus på list rutan åtgärds typ." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. Under **aviserings regel detaljerna** anger du ett namn, en beskrivning, en resurs grupp för att lagra aviserings regeln, allvarlighets grad. Välj sedan **skapa aviserings regel**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Skärm bild som visar information om varnings regeln."::: 
 
   Varnings regeln är synlig och kan hanteras från Azure Portal.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Skärm bild som visar den nya varnings regeln i fönstret regler." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Så snart ett mått når tröskelvärdet som det definieras i en varnings regel uppdateras menyn **aviseringar** och visas.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Skärm bild som visar aviseringen när det angivna tröskelvärdet har uppnåtts." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   Beroende på den konfigurerade åtgärds gruppen får du ett meddelande via det konfigurerade mediet. I vårt exempel har vi konfigurerat e-post.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Skärm bild av en Azure Monitor-avisering med fel strängen och händelsen datum och tid utlöstes."::: 

## <a name="work-with-metrics"></a>Arbeta med mått

1. Välj **övervaknings** mått från ditt privata moln i Azure VMware-lösningen  >  . Välj sedan det mått du vill ha i list rutan.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Skärm bild som visar fönstret mått och ett fokus på den nedrullningsbara mått List rutan." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Du kan ändra diagrammets parametrar, till exempel **tidsintervallet** eller **tids kornig het**. 

   Andra alternativ är:
   - **Detaljgranska i loggar** och fråga data i den relaterade Log Analytics-arbetsytan
   - **Fäst det här diagrammet** på en Azure-instrumentpanel för bekvämlighet.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Skärm bild som visar alternativ för tidsintervall och tids granularitet för mått." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat en varnings regel för ditt privata moln i Azure VMware-lösningen kanske du vill lära dig ännu mer om:
- [Azure Monitor-statistik](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure Monitor-aviseringar](/azure/azure-monitor/alerts/alerts-overview)
- [Azure-åtgärds grupper](/azure/azure-monitor/alerts/action-groups)

Du kan också fortsätta med någon av de andra [Azure VMware-lösningarna för VMware-lösningar](index.yml) .





