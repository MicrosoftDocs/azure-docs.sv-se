---
title: Azure Service Bus – uppdatera meddelande enheter automatiskt
description: Den här artikeln visar hur du kan använda automatisk uppdatering av meddelande enheter i ett Service Bus namn område.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 932c7bb1235cb54aefe67253e38e1683187f4d2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581639"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Uppdatera meddelande enheter automatiskt i ett Azure Service Bus namn område 
Med autoskalning kan du använda rätt mängd resurser för att hantera belastningen på ditt program. Det gör att du kan lägga till resurser för att hantera ökad belastning och även spara pengar genom att ta bort resurser som är inaktiva. I [Översikt över autoskalning i Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) kan du läsa mer om funktionen för autoskalning i Azure Monitor. 

Service Bus Premium-meddelanden ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resurs behållaren kallas för en **meddelande enhet**. Läs mer om meddelande enheter i [Service Bus Premium-meddelanden](service-bus-premium-messaging.md). 

Genom att använda funktionen för automatisk skalning för Service Bus Premium-namnområden kan du ange ett minsta och högsta antal [meddelande enheter](service-bus-premium-messaging.md) och lägga till eller ta bort meddelande enheter automatiskt baserat på en uppsättning regler. 

Du kan till exempel implementera följande skalnings scenarier för Service Bus namn områden med funktionen för automatisk skalning. 

- Öka meddelande enheter för ett Service Bus-namnområde när processor användningen för namn området går över 75%. 
- Minska meddelande enheter för ett Service Bus-namnområde när processor användningen för namn området går under 25%. 
- Använd fler meddelande enheter under kontors tid och färre under timmar. 

Den här artikeln visar hur du automatiskt kan skala en Service Bus namnrymd (uppdatering av [meddelande enheter](service-bus-premium-messaging.md)) i Azure Portal. 

> [!IMPORTANT]
> Den här artikeln gäller endast **Premium** nivån för Azure Service Bus. 

## <a name="autoscale-setting-page"></a>Sida för autoskalningsinställning
Börja med att följa de här stegen för att navigera till sidan med **Inställningar för autoskalning** för din Service Bus-namnrymd.

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. I Sök fältet skriver du **Service Bus**, väljer **Service Bus** i list rutan och trycker på **RETUR**. 
1. Välj ditt **Premium-namnområde** från listan över namn områden. 
1. Växla till **skalnings** sidan. 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Sidan Service Bus namnrymd-skala":::

## <a name="manual-scale"></a>Manuell skalning 
Med den här inställningen kan du ange ett fast antal meddelande enheter för namn området. 

1. **På sidan autoskalningsinställning** väljer du **manuell skala** om den inte redan är markerad. 
1. För inställning av **meddelande enheter** väljer du antalet meddelande enheter i den nedrullningsbara listan.
1. Spara inställningen genom att välja **Spara** i verktygsfältet. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Skala meddelande enheter manuellt":::       


## <a name="custom-autoscale---default-condition"></a>Anpassad autoskalning – standard villkor
Du kan konfigurera automatisk skalning av meddelande enheter med hjälp av villkor. Det här skalnings villkoret körs när inget av de andra skalnings villkoren matchar. Du kan ange standard villkor på något av följande sätt:

- Skala baserat på ett mått (till exempel processor-eller minnes användning)
- Skala till ett angivet antal meddelande enheter

Du kan inte ange ett schema för autoskalning på en angiven dag eller ett visst datum intervall för ett standard villkor. Det här skalnings villkoret körs när inget av de andra skalnings villkoren med scheman matchar. 

### <a name="scale-based-on-a-metric"></a>Skala baserat på ett mått
Följande procedur visar hur du lägger till ett villkor för att automatiskt öka meddelande enheter (skala ut) när processor användningen är större än 75% och minska meddelande enheterna (skala in) när CPU-användningen är mindre än 25%. Stegvisa steg görs från 1 till 2, 2 till 4 och 4 till 8. På samma sätt görs minskningar från 8 till 4, 4 till 2 och 2 till 1. 

1. På sidan automatisk **skalnings inställning** väljer du **anpassad autoskalning** för alternativet **Välj hur du vill skala din resurs** . 
1. I **standard** avsnittet på sidan anger du ett **namn** för standard villkoret. Välj **Penn** ikonen för att redigera texten. 
1. Välj **skala baserat på ett mått** för **skalnings läge**. 
1. Välj **+ Lägg till en regel**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Standard-skala baserat på ett mått":::    
1. Följ dessa steg på sidan **skalnings regel** :
    1. Välj ett mått i list rutan **mått namn** . I det här exemplet är det **CPU**. 
    1. Välj en operatör och tröskelvärdes värden. I det här exemplet är de **större än** och **75** för **mått tröskelvärdet för att utlösa skalnings åtgärden**. 
    1. Välj en **åtgärd** i avsnittet **åtgärd** . I det här exemplet är den inställd på att **öka**. 
    1. Välj sedan **Lägg till**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Standard – skala ut om CPU-användningen är större än 75%":::       

        > [!NOTE]
        > Funktionen för autoskalning ökar meddelande enheterna för namn området om den totala processor användningen går över 75% i det här exemplet. Stegvisa steg görs från 1 till 2, 2 till 4 och 4 till 8. 
1. Välj **+ Lägg till en regel** igen och följ de här stegen på sidan **skalnings regel** :
    1. Välj ett mått i list rutan **mått namn** . I det här exemplet är det **CPU**. 
    1. Välj en operatör och tröskelvärdes värden. I det här exemplet är de **mindre än** och **25** för **mått tröskelvärdet för att utlösa skalnings åtgärder**. 
    1. Välj en **åtgärd** i avsnittet **åtgärd** . I det här exemplet är det inställt på **minska**. 
    1. Välj sedan **Lägg till** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Standard skala i om CPU-användningen är mindre än 25%":::       

        > [!NOTE]
        > Funktionen för autoskalning minskar meddelande enheterna för namn området om den totala processor användningen går under 25% i det här exemplet. Minskningar görs från 8 till 4, 4 till 2 och 2 till 1. 
1. Ange **lägsta** och **högsta** **antal meddelande** enheter.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Standard regel baserat på ett mått":::
1. Spara inställningen för autoskalning genom att välja **Spara** i verktygsfältet. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Skala till ett angivet antal meddelande enheter
Följ dessa steg om du vill konfigurera regeln för att skala namn området till att använda ett angivet antal meddelande enheter. Återigen tillämpas standard villkoret när inget av de andra skalnings villkoren matchar. 

1. På sidan automatisk **skalnings inställning** väljer du **anpassad autoskalning** för alternativet **Välj hur du vill skala din resurs** . 
1. I **standard** avsnittet på sidan anger du ett **namn** för standard villkoret. 
1. Välj **skala till vissa meddelande enheter** för **skalnings läge**. 
1. För **meddelande enheter** väljer du antalet standard meddelande enheter. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Standard-skala till vissa meddelande enheter":::       

## <a name="custom-autoscale---additional-conditions"></a>Anpassad autoskalning – ytterligare villkor
I föregående avsnitt visas hur du lägger till ett standard villkor för inställningen för autoskalning. I det här avsnittet visas hur du lägger till fler villkor i den automatiska skalnings inställningen. För dessa ytterligare icke-standardvillkor kan du ange ett schema som baseras på vissa dagar i en vecka eller ett datum intervall. 

### <a name="scale-based-on-a-metric"></a>Skala baserat på ett mått
1. På sidan automatisk **skalnings inställning** väljer du **anpassad autoskalning** för alternativet **Välj hur du vill skala din resurs** . 
1. Välj **Lägg till ett skalnings villkor** under **standard** blocket. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Anpassad – Lägg till en länk för skalnings villkor":::    
1. Ange ett **namn** för villkoret. 
1. Bekräfta att alternativet **skala baserat på ett mått** är markerat. 
1. Välj **+ Lägg till en regel** för att lägga till en regel för att öka meddelande enheterna när den totala processor användningen går över 75%. Följ stegen i avsnittet [standard villkor](#custom-autoscale---default-condition) . 
5. Ange **lägsta** och **högsta** **antal meddelande** enheter.
6. Du kan också ange ett **schema** för ett anpassat villkor (men inte på standard villkoret). Du kan antingen ange start-och slutdatum för villkoret (eller) välja särskilda dagar (måndag, tisdag osv.) i veckan. 
    1. Om du väljer **Ange start-/slutdatum** väljer du **tidszon**, **start datum och tid** och **slutdatum och tid** (som visas i följande bild) för att villkoret ska tillämpas. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Lägsta, högsta och standardvärden för antal meddelande enheter":::
    1. Om du väljer **upprepa vissa dagar** väljer du vecko dagar, tidszon, start tid och slut tid när villkoret ska gälla. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Upprepa vissa dagar":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Skala till ett angivet antal meddelande enheter
1. På sidan automatisk **skalnings inställning** väljer du **anpassad autoskalning** för alternativet **Välj hur du vill skala din resurs** . 
1. Välj **Lägg till ett skalnings villkor** under **standard** blocket. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Anpassad – Lägg till en länk för skalnings villkor":::    
1. Ange ett **namn** för villkoret. 
2. Välj alternativet **skala till vissa meddelande enheter** för **skalnings läge**. 
1. Välj antalet **meddelande enheter** i den nedrullningsbara listan. 
6. För **schemat** anger du antingen start-och slutdatum för villkoret (eller) och väljer särskilda dagar (måndag, tisdag osv.) på en vecka och gånger. 
    1. Om du väljer **Ange start-/slutdatum** väljer du **tids zonen**, **start datum och tid** och **slutdatum och slut tid** för villkoret. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="skala till särskilda meddelande enheter – start-och slutdatum":::        
    1. Om du väljer **upprepa vissa dagar** väljer du vecko dagar, tidszon, start tid och slut tid när villkoret ska gälla.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="skala till vissa meddelande enheter – upprepa vissa dagar":::

> [!IMPORTANT]
> Om du vill veta mer om hur inställningarna för autoskalning fungerar, särskilt hur de väljer en profil eller ett villkor och utvärderar flera regler, se [förstå inställningarna för autoskalning](../azure-monitor/autoscale/autoscale-understanding-settings.md).          

## <a name="next-steps"></a>Nästa steg
Läs mer om meddelande enheter i [Premium Messaging](service-bus-premium-messaging.md)

