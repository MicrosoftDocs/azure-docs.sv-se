---
title: Konfigurera mått klassificerings aviseringar
titleSuffix: Azure Cognitive Services
description: Hur du konfigurerar dina Metrics Advisor-aviseringar med hjälp av hookar för e-post, webb-och Azure-DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420928"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Anvisningar: Konfigurera aviseringar och få meddelanden med hjälp av en Hook

När en avvikelse identifieras av Metrics Advisor utlöses ett aviserings meddelande baserat på aviserings inställningarna med hjälp av en Hook. En varnings inställning kan användas med flera identifierings konfigurationer, olika parametrar är tillgängliga för att anpassa aviserings regeln.

## <a name="create-a-hook"></a>Skapa en Hook

Mått Advisor stöder tre olika typer av krokar: e-posthook, Web Hook och Azure DevOps. Du kan välja den som fungerar för ditt speciella scenario.       

### <a name="email-hook"></a>E-posthook

> [!Note]
> Resurs administratörer för mått rådgivare måste konfigurera e-postinställningarna och mata in SMTP-relaterad information i mått rådgivare innan avvikelse aviseringar kan skickas. Administratörs-eller prenumerations administratören för resurs gruppen måste tilldela minst en *Administratörs roll för Cognitive Services mått rådgivare* på fliken åtkomst kontroll i mått Advisor-resursen. [Läs mer om konfiguration av e-postinställningar](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Följande parametrar är tillgängliga för att skapa en e-posthook: 

En e-posthook är kanalen för avvikelse aviseringar som skickas till e-postadresser som anges i avsnittet **e-post till** . Två typer av e-postaviseringar skickas: *datafeed inte tillgängliga* aviseringar och *incident rapporter* som innehåller en eller flera avvikelser. 

|Parameter |Beskrivning  |
|---------|---------|
| Name | Namn på e-posthooken |
| E-postmeddelande till| E-postadresser som skickar aviseringen till|
| Extern länk | Valfritt fält som möjliggör en anpassad omdirigering, t. ex. för fel söknings anteckningar. |
| Anpassad avvikelse aviserings rubrik | Rubrik mal len stöder `${severity}` , `${alertSettingName}` , `${datafeedName}` ,, `${metricName}` `${detectConfigName}` `${timestamp}` , `${topDimension}` `${incidentCount}` ,, `${anomalyCount}`

När du klickar på **OK** skapas en e-posthook. Du kan använda den i alla aviserings inställningar för att få avvikelse aviseringar. 

### <a name="web-hook"></a>Webb-Hook

> [!Note]
> * Använd metoden **post** -begäran.
> * Kunna för begär ande texten ser ut ungefär så här:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * När en webbhook skapas eller ändras, anropas API: et som ett test med en tom begär ande text. Ditt API måste returnera en 200 HTTP-kod.

En webbhook är start punkten för all information som är tillgänglig från tjänsten Metrics Advisor och anropar ett API för användaren som anges när en avisering utlöses. Alla aviseringar kan skickas via en Web Hook.

Om du vill skapa en webhook måste du lägga till följande information:

|Parameter |Beskrivning  |
|---------|---------|
|Slutpunkt     | API-adressen som ska anropas när en avisering utlöses.        |
|Användar namn/lösen ord | För autentisering till API-adressen. Lämna det här svart om autentisering inte behövs.         |
|Huvud     | Anpassade rubriker i API-anropet.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="fönstret Skapa webb-Hook.":::

När ett meddelande skickas via en webhook kan du använda följande API: er för att få information om aviseringen. Ange *tidsstämpel* och *ALERTSETTINGGUID* i din API-tjänst, som skickas till, och Använd sedan följande frågor: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor stöder också automatisk skapande av arbets objekt i Azure-DevOps för att spåra problem/buggar när avvikelser upptäcks. Alla aviseringar kan skickas via Azure DevOps-hookar.

Om du vill skapa en Azure DevOps-Hook måste du lägga till följande information

|Parameter |Beskrivning  |
|---------|---------|
| Name | Ett namn för hooken |
| Organisation | Organisationen som din DevOps tillhör |
| Project | Det aktuella projektet i DevOps. |
| Åtkomsttoken |  En token för autentisering till DevOps. | 

> [!Note]
> Du måste bevilja Skriv behörighet om du vill att mått Advisor ska skapa arbets objekt baserat på avvikelse aviseringar. När du har skapat hookar kan du använda dem i alla dina aviserings inställningar. Hantera dina hookar på sidan **Hook-inställningar** .

## <a name="add-or-edit-alert-settings"></a>Lägg till eller redigera aviserings inställningar

Gå till sidan med mått information för att hitta avsnittet **aviserings inställningar** i det nedre vänstra hörnet av mått informations sidan. Den visar alla aviserings inställningar som gäller för den valda identifierings konfigurationen. När en ny identifierings konfiguration skapas, finns det ingen varnings inställning och inga aviseringar kommer att skickas.  
Du kan ändra aviserings inställningarna med hjälp av ikonerna **Lägg till**, **Redigera** och **ta bort** .

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Meny alternativet aviserings inställningar.":::

Klicka på knappen **Lägg till** eller **Redigera** för att hämta ett fönster för att lägga till eller redigera dina aviserings inställningar.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Lägg till eller redigera aviserings inställningar":::

**Namn på varnings inställning**: namnet på den här varnings inställningen. Den visas i rubriken aviserings-e-postadress.

**Hookar**: listan över hookar som aviseringar ska skickas till.

Avsnittet som är markerat i skärm bilden ovan är inställningarna för en identifierings konfiguration. Du kan ange olika aviserings inställningar för olika identifierings konfigurationer. Välj mål konfigurationen med hjälp av den tredje List rutan i det här fönstret. 

### <a name="filter-settings"></a>Filterinställningar 

Följande är filter inställningar för en identifierings konfiguration.

**Avisering för** har fyra alternativ för att filtrera avvikelser:

* **Avvikelser i alla serier**: alla avvikelser tas med i aviseringen.         
* **Avvikelser i serie gruppen**: filtrera serier efter dimensions värden. Ange ett visst värde för vissa dimensioner. Avvikelser inkluderas bara i aviseringen när serien matchar det angivna värdet.       
* **Avvikelser i favorit serien**: endast serien som marker ATS som favorit tas med i aviseringen.        |
* **Avvikelser i de översta n i alla serier**: det här filtret gäller för det fall du bara bryr dig om serien vars värde är i de översta n. Vi ser tillbaka några tidsstämplar och kontrollerar om seriens värde i den här tidsstämpeln är i översta N. Om antalet i högsta n-antal är större än det angivna antalet tas avvikelsen med i en avisering.        |

**Filtrera avvikelse alternativ** är ett ytterligare filter med följande alternativ:

- **allvarlighets grad** : avvikelsen tas bara med när avvikelsen är inom det angivna intervallet.
- **Vilo läge** : Stoppa aviseringar tillfälligt för avvikelser under de följande N punkterna (punkt), när de utlöses i en avisering.
    - **vilo läges typ** : när värdet är **serien** är en utlöst avvikelse bara en vilo läges serie. För **mått** är ett utlöst avvikelse i vilo läge alla serier i måttet.
    - **vilo läges nummer** : antalet punkter (punkt) till vilo läge.
    - **återställning efter** fel: när det här alternativet har valts kommer en utlöst avvikelse bara att försätta nästa n efterföljande avvikelser. Om någon av följande data punkter inte är en avvikelse kommer vilo läget att återställas från den punkten. När det är omarkerat kommer en utlöst avvikelse att inledas nästa n punkter (punkt), även om efterföljande data punkter inte är avvikelser.
- **värde** (valfritt): Filtrera efter värde. Endast punkt värden som uppfyller villkoret, avvikelser tas med. Om du använder motsvarande värde för ett annat mått bör dimensions namnen för de två måtten vara konsekventa.

Avvikelser som inte har filtrerats ut skickas i en avisering.

### <a name="add-cross-metric-settings"></a>Lägg till inställningar för kors mått

Klicka på **+ Lägg till inställningar för kors mått** på sidan aviserings inställningar för att lägga till ett annat avsnitt.

**Operatorn** Selector är den logiska relationen för varje avsnitt för att avgöra om de skickar en avisering.


|Operator  |Beskrivning  |
|---------|---------|
|AND     | Skicka bara en avisering om en serie matchar varje aviserings avsnitt och alla data punkter är avvikelser. Om måtten har olika dimensions namn utlöses aldrig en avisering.         |
|ELLER     | Skicka aviseringen om minst ett avsnitt innehåller avvikelser.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Avsnittet operator för flera aviserings inställningar":::

## <a name="next-steps"></a>Nästa steg

- [Justera avvikelseidentifiering med hjälp av feedback](anomaly-feedback.md)
- [Diagnostisera en incident](diagnose-incident.md).
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)