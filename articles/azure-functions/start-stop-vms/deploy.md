---
title: Distribuera start/stoppa VM v2 (för hands version)
description: Den här artikeln beskriver hur du distribuerar funktionen starta/stoppa virtuella datorer v2 (för hands version) för dina virtuella Azure-datorer i din Azure-prenumeration.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112207"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Distribuera start/stoppa VM v2 (för hands version)

Utför stegen i det här avsnittet i tur och ordning för att installera funktionen starta/stoppa virtuella datorer v2 (för hands version). När du har slutfört installations processen konfigurerar du scheman för att anpassa den efter dina behov.

## <a name="deploy-feature"></a>Distribuera funktion

Distributionen initieras [från organisationen starta](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)/stoppa VM v2 GitHub. Även om den här funktionen är avsedd att hantera alla dina virtuella datorer i din prenumeration i alla resurs grupper från en enda distribution i prenumerationen kan du installera en annan instans av den baserat på drifts modellen eller organisationens krav. Den kan också konfigureras för central hantering av virtuella datorer över flera prenumerationer.

För att förenkla hanteringen och borttagningen rekommenderar vi att du distribuerar starta/stoppa VM v2 (för hands version) till en dedikerad resurs grupp.

> [!NOTE]
> För hands versionen stöder för närvarande inte att du anger ett befintligt lagrings konto eller en Application Insights resurs.

1. Öppna webbläsaren och gå till [GitHub-organisationen](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)för att starta/stoppa virtuella datorer v2.
1. Välj distributions alternativet baserat på Azure Cloud-miljön som dina virtuella Azure-datorer skapas i. Den anpassade Azure Resource Manager distributions sidan öppnas i Azure Portal.
1. Logga in på [Azure Portal](https://portal.azure.com)om du uppmanas till det.
1. Ange följande värden:

    |Name |Värde |
    |-----|------|
    |Region |Välj en region nära dig för nya resurser.|
    |Namnet på resursgruppen |Ange namnet på den resurs grupp som ska innehålla de enskilda resurserna för start/stoppa virtuella datorer. |
    |Resurs grupps region |Ange region för resurs gruppen. Välj till exempel **USA, centrala**.|
    |Namn på Azure-Funktionsapp |Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att säkerställa att det är unikt i Azure Functions. |
    |Application Insights namn |Ange namnet på Application Insights-instansen som ska innehålla analyser för start/stoppa virtuella datorer. |
    |Application Insights region |Ange regionen för Application Insights-instansen.|
    |Lagrings konto namn |Ange namnet på det Azure Storage konto där du vill lagra starta/stoppa telemetri för virtuella datorer. |
    |E-postadress |Ange en eller flera e-postadresser som ska ta emot status meddelanden, avgränsade med kommatecken (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Starta/stoppa distributions konfiguration för VM-mallar":::

1. Välj **Granska + skapa** längst ned på sidan.
1. Välj **skapa** för att starta distributionen.
1. Välj klockikonen (meddelanden) längst upp på skärmen för att se distributionsstatus. **Distribution pågår** ska nu visas. Vänta tills distributionen är klar.
1. Välj **Gå till resursgrupp** i meddelandefönstret. Du bör se en skärm som liknar följande:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Starta/stoppa VM-mall distribution resurs lista":::

## <a name="enable-multiple-subscriptions"></a>Aktivera flera prenumerationer

När distributionen av starta/stoppa har slutförts utför du följande steg för att aktivera starta/stoppa VM v2 (för hands version) för att vidta åtgärder över flera prenumerationer.

1. Kopiera värdet för det Azure Funktionsapp namn som du angav under distributionen.

1. I portalen navigerar du till din sekundära prenumeration. Välj prenumerationen och välj sedan **Access Control (IAM)**

1. Välj **Lägg till** och välj sedan **Lägg till roll tilldelning**.

1. Välj **deltagar** rollen i list rutan **roll** .

1. Ange Azure Function-programmets namn i fältet **Välj** . Välj funktions namnet i resultatet.

1. Välj **Spara** för att genomföra ändringarna.

## <a name="configure-schedules-overview"></a>Konfigurera översikt över scheman

För att hantera Automation-metoden för att styra start och stopp av dina virtuella datorer, konfigurerar du en eller flera av de inkluderade Logic Apps utifrån dina krav.

- Schemalagda start-och stopp åtgärder baseras på ett schema som du anger mot Azure Resource Manager och klassiska virtuella datorer.**ststv2_vms_Scheduled_start** och **ststv2_vms_Scheduled_stop** konfigurera den schemalagda starten och stoppa.

- Sekvenserade-start-och stopp åtgärder baseras på ett schema som riktar sig mot virtuella datorer med fördefinierade sekvensering-taggar. Endast två namngivna Taggar stöds – **sequencestart** och **sequencestop**. **ststv2_vms_Sequenced_start** och **ststv2_vms_Sequenced_stop** konfigurera sekvenserad start och stopp.

    > [!NOTE]
    > Det här scenariot stöder endast Azure Resource Manager virtuella datorer.

- Autostopp – den här funktionen används bara för att utföra en stopp åtgärd mot både Azure Resource Manager och klassiska virtuella datorer baserat på dess CPU-belastning. Det kan också vara en schemalagd-baserad *åtgärd*, som skapar aviseringar på virtuella datorer och baserat på villkoret, som aviseringen utlöses för att utföra åtgärden stoppa.**ststv2_vms_AutoStop** konfigurerar funktionen för automatisk avslutning.

Om du behöver ytterligare scheman kan du duplicera ett av de Logic Apps som anges med alternativet **klona** i Azure Portal.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Välj alternativet klona för att duplicera en Logic app":::

## <a name="scheduled-start-and-stop-scenario"></a>Schemalagt start-och stopp scenario

Utför följande steg för att konfigurera den schemalagda start-och stopp åtgärden för Azure Resource Manager och klassiska virtuella datorer. Du kan till exempel konfigurera **ststv2_vms_Scheduled_start** schema för att starta dem i morgon när du befinner dig på kontoret och stoppa alla virtuella datorer i en prenumeration när du lämnar jobbet på kvällen baserat på **ststv2_vms_Scheduled_stop** schemat.

Det finns stöd för att konfigurera Logi Kap par för att bara starta de virtuella datorerna.

För varje scenario kan du rikta åtgärden mot en eller flera prenumerationer, en eller flera resurs grupper och ange en eller flera virtuella datorer i en lista över inkluderingar eller undantag. Du kan inte ange dem tillsammans i samma Logic-app.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå sedan till **Logic Apps**.

1. I listan över Logi Kap par kan du Konfigurera schemalagd start genom att välja **ststv2_vms_Scheduled_start**. Om du vill konfigurera schemalagt stopp väljer du **ststv2_vms_Scheduled_stop**.

1. Välj **Logic App Designer** i det vänstra fönstret.

1. När Logic Apps Designer visas går du till fönstret designer och väljer **upprepning** för att konfigurera Logic app-schemat. Mer information om de olika upprepnings alternativen finns i [Schemalägg återkommande aktivitet](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurera upprepnings frekvensen för Logic app":::

1. I fönstret designer väljer du **funktion-försök** att konfigurera mål inställningarna. I begär ande texten, om du vill hantera virtuella datorer i alla resurs grupper i prenumerationen, ändrar du begär ande texten som visas i följande exempel.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Ange flera prenumerationer i `subscriptions` matrisen med varje värde avgränsat med kommatecken som i följande exempel.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    I begär ande texten kan du, om du vill hantera virtuella datorer för vissa resurs grupper, ändra begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en resurs grupp eller mer om det behövs.

    Det här exemplet visar även en virtuell dator. Du kan undanta den virtuella datorn genom att ange den virtuella datorns resurs Sök väg eller jokertecken.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Här kommer åtgärden att utföras på alla virtuella datorer förutom det virtuella dator namnet börjar med AZ och BZ i båda prenumerationerna.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    I begär ande texten, om du vill hantera en speciell uppsättning virtuella datorer i prenumerationen, ändrar du begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en virtuell dator om det behövs.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Sekvenserat start-och stopp scenario

I en miljö som innehåller två eller flera komponenter på flera Azure Resource Manager virtuella datorer i en arkitektur för distribuerade program, är det viktigt med stöd för den sekvens i vilken komponenter startas och stoppas.

1. I listan över Logi Kap par kan du konfigurera sekvenserad start genom att välja **ststv2_vms_Sequenced_start**. Om du vill konfigurera sekvenserat stopp väljer du **ststv2_vms_Sequenced_stop**.

1. Välj **Logic App Designer** i det vänstra fönstret.

1. När Logic Apps Designer visas går du till fönstret designer och väljer **upprepning** för att konfigurera Logic app-schemat. Mer information om de olika upprepnings alternativen finns i [Schemalägg återkommande aktivitet](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurera upprepnings frekvensen för Logic app":::

1. I fönstret designer väljer du **funktion-försök** att konfigurera mål inställningarna. I begär ande texten, om du vill hantera virtuella datorer i alla resurs grupper i prenumerationen, ändrar du begär ande texten som visas i följande exempel.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Ange flera prenumerationer i `subscriptions` matrisen med varje värde avgränsat med kommatecken som i följande exempel.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    I begär ande texten kan du, om du vill hantera virtuella datorer för vissa resurs grupper, ändra begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en resurs grupp om det behövs.

    I det här exemplet visas även en virtuell dator med dess resurs Sök väg jämfört med exemplet för schemalagd start/stopp, som använde jokertecken.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    I begär ande texten, om du vill hantera en speciell uppsättning virtuella datorer i en prenumeration, ändrar du begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en virtuell dator om det behövs.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scenario för automatiskt stopp

Starta/stoppa VM v2 (för hands version) kan hjälpa till att hantera kostnaden för att köra Azure Resource Manager och klassiska virtuella datorer i din prenumeration genom att utvärdera datorer som inte används under perioder med låg belastning, t. ex. efter timmar, och stänga ned dem automatiskt om processor belastningen är lägre än en angiven procent andel.

Följande mått på aviserings egenskaper i begär ande texten stöder anpassning:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Mer information om hur Azure Monitor mått aviseringar fungerar och hur du konfigurerar dem finns i [mått varningar i Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. I listan över Logi Kap par kan du konfigurera automatisk stoppning genom att välja **ststv2_vms_AutoStop**.

1. Välj **Logic App Designer** i det vänstra fönstret.

1. När Logic Apps Designer visas går du till fönstret designer och väljer **upprepning** för att konfigurera Logic app-schemat. Mer information om de olika upprepnings alternativen finns i [Schemalägg återkommande aktivitet](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurera upprepnings frekvensen för Logic app":::

1. I fönstret designer väljer du **funktion-försök** att konfigurera mål inställningarna. I begär ande texten, om du vill hantera virtuella datorer i alla resurs grupper i prenumerationen, ändrar du begär ande texten som visas i följande exempel.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    I begär ande texten kan du, om du vill hantera virtuella datorer för vissa resurs grupper, ändra begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en resurs grupp om det behövs.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    I begär ande texten, om du vill hantera en speciell uppsättning virtuella datorer i prenumerationen, ändrar du begär ande texten som visas i följande exempel. Varje resurs Sök väg som anges måste avgränsas med ett kommatecken. Du kan ange en virtuell dator om det behövs.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Nästa steg

Information om hur du övervakar status för dina virtuella Azure-datorer som hanteras av funktionen starta/stoppa virtuella datorer v2 (för hands version) och utför andra hanterings uppgifter finns i artikeln [Hantera starta/stoppa virtuella datorer](manage.md) .