---
title: Hantera datafeeds i Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Lär dig hur du hanterar datafeeds som du har lagt till i mått Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184730"
---
# <a name="how-to-manage-your-data-feeds"></a>Så här gör du: hantera dina data matningar

Lär dig hur du hanterar dina registrerade datafeeds i Metrics Advisor. Den här artikeln vägleder dig genom hantering av datafeeds i Metrics Advisor.

## <a name="edit-a-data-feed"></a>Redigera en datafeed

> [!NOTE]
> Följande information kan inte ändras efter att en datafeed har skapats. 
> * Datafeed-ID
> * Genereringstid
> * Dimension
> * Källtyp
> * Precision

Endast administratören för en datafeed får göra ändringar i den. 

Pausa eller återaktivera en datafeed:

1. På sidan datafeed-lista klickar du på den åtgärd som du vill utföra på datafeeden.

2. På sidan information om data feed klickar du på knappen **status** brytare.

Så här tar du bort en datafeed: 

1. På sidan datafeed-lista klickar du på **ta bort** på datafeeden.

2. Klicka på **ta bort** på sidan information om data flöde.

När du ändrar Start tiden måste du verifiera schemat igen. Du kan ändra den genom att använda **Edit Parameters**.

##  <a name="backfill-your-data-feed"></a>Fyll i data matningen

Välj knappen  **Fyll** för att utlösa en omedelbar inmatning på en tidstämpel för att åtgärda en misslyckad inmatning eller åsidosätta befintliga data.
- Start tiden är inkluderad.
- Slut tiden är exklusiv.
- Avvikelse identifiering aktive ras på nytt i det valda intervallet.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Data flöde för bakfyllning":::

## <a name="manage-permission-of-a-data-feed"></a>Hantera behörighet för en datafeed

Åtkomst till arbets ytan styrs av Metric Advisor-resursen, som använder Azure Active Directory för autentisering. Ett annat lager med behörighets kontroll tillämpas på Mät data.

Med mått Advisor kan du bevilja behörigheter till olika grupper av personer i olika datafeeds. Det finns två typer av roller: 

- Administratör: som har fullständig behörighet att hantera en datafeed, inklusive ändra och ta bort.
- Visnings program: som har åtkomst till en skrivskyddad vy av datafeeden.
 

## <a name="advanced-settings"></a>Avancerade inställningar

Det finns flera valfria avancerade inställningar när du skapar en ny datafeed, de kan ändras på informations sidan för datafeed.

### <a name="ingestion-options"></a>Inmatnings alternativ

* **Tids förskjutning** för inhämtning: som standard matas data in enligt angiven kornig het. Till exempel kommer ett mått med en *daglig* tidsstämpel att matas in en dag efter tidsstämpeln. Du kan använda förskjutningen för att skjuta upp tiden för inmatningen med ett *positivt* tal eller gå vidare med ett *negativt* tal.

* **Max samtidighet**: Ange den här parametern om data källan har stöd för begränsad samtidighet. Lämna annars kvar standardvärdet.

* **Stoppa försök igen efter**: om data inmatningen har misslyckats görs ett nytt försök automatiskt inom en period. Början av perioden är den tidpunkt då den första data inmatningen skedde. Längden på perioden definieras enligt granularitet. Om du lämnar standardvärdet (-1) bestäms värdet enligt granularitet enligt nedan.
    
    | Precision       | Avbryt försök efter           |
    | :------------ | :--------------- |
    | Daglig, anpassad (>= 1 dag), varje vecka, varje månad, varje år     | 7 dagar          |
    | Varje timme, anpassad (< 1 dag)       | 72 timmar |

* Minsta **Återförsöksintervall**: du kan ange det minsta intervallet när du försöker hämta data från källan igen. Om du lämnar standardvärdet (-1) bestäms intervallet för återförsök enligt den granularitet som visas nedan.
    
    | Precision       | Minsta återförsöksintervall           |
    | :------------ | :--------------- |
    | Dagligen, anpassat (>= 1 dag), varje vecka, varje månad     | 30 minuter          |
    | Varje timme, anpassad (< 1 dag)      | 10 minuter |
    | Varje år | 1 dag          |
 
### <a name="fill-gap-when-detecting"></a>Fyll mellanrum vid identifiering: 

> [!NOTE]
> Den här inställningen påverkar inte data källan och kommer inte att påverka de data diagram som visas på portalen. Automatisk ifyllning sker endast under avvikelse identifiering.

Vissa tids serier är inte kontinuerliga. När det saknas data punkter använder Metric Advisor det angivna värdet för att fylla dem före avvikelse identifiering för bättre precision.
Alternativen är: 

* Använder värdet från föregående faktiska data punkt. Detta används som standard.
* Använda ett angivet värde.

### <a name="action-link-template"></a>Mall för åtgärds länk: 

Mall för åtgärds länkar används för att fördefiniera http-URL: er som kan användas för att definiera, som består av plats hållare,,, `%datafeed` `%metric` `%timestamp` `%detect_config` och `%tagset` . Du kan använda mallen för att omdirigera från en avvikelse eller en incident till en viss URL för att öka detalj nivån.

:::image type="content" source="../media/action-link-template.png" alt-text="Mall för åtgärds länk" lightbox="../media/action-link-template.png":::

När du har fyllt i åtgärds länken klickar **du på gå till åtgärds länk** i incident listans åtgärds alternativ och på snabb menyn i incident trädet. Ersätt plats hållarna i åtgärds länk mal len med motsvarande värden för avvikelsen eller incidenten.

| Platshållare | Exempel | Kommentar |
| ---------- | -------- | ------- |
| `%datafeed` | - | Datafeed-ID |
| `%metric` | - | Mått-ID |
| `%detect_config` | - | Identifiera konfigurations-ID |
| `%timestamp` | - | Tidsstämpel för en avvikelse eller slut tid för en beständig incident |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Dimensions värden för en incidents avvikande eller Top avvikelse.   <br> `filterVal`Används för att filtrera ut matchande värden inom hakparenteserna.   |

Fler

* Om mall för åtgärds länkar är `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * Åtgärds länken `https://action-link/metric/1234?detectConfigId=2345` går till avvikelser eller incidenter under måttet `1234` och identifierar konfigurationen `2345` .

* Om mall för åtgärds länkar är `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * Åtgärds länken `https://action-link?Dim1=Val1&Dim2=Val2` är när avvikelsen är `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * Åtgärds länken `https://action-link?Dim2=Val2` är när avvikelsen är `{ "Dim1": "", "Dim2": "Val2" } ` , eftersom den `[Dim1=***&]` ignoreras för den tomma dimensions värde strängen. 

* Om mall för åtgärds länkar är `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * Åtgärds länken `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` är när avvikelsen är `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * Åtgärds länken skulle vara `https://action-link?filter=Name/Dim2 eq 'Val2'` när avvikelsen har `{ "Dim1": "", "Dim2": "Val2" }` `[Name/Dim1 eq '***' and ]` överhoppats för den tomma dimensions värde strängen. 
   
### <a name="data-feed-not-available-alert-settings"></a>Aviserings inställningarna "datafeed är inte tillgänglig"

En datafeed anses vara inte tillgänglig om inga data matas in från källan inom den Respitperiod som anges från den tidpunkt då datafeeden börjar inmatning. En avisering utlöses i det här fallet.

Om du vill konfigurera en avisering måste du först [skapa en Hook](alerts.md#create-a-hook) . Aviseringar skickas via den konfigurerade hooken.

* **Respitperiod**: inställningen för respitperiod används för att avgöra när du ska skicka en avisering om inga data punkter matas in. Referens punkten är tidpunkten för första inmatningen. Om en inmatning Miss lyckas fortsätter mått Advisor att försöka med ett intervall som anges av granularitet. Om den fortsätter att Miss förfaller efter respitperioden skickas en avisering.

* **Automatisk vilo läge**: när det här alternativet är inställt på noll utlöser varje tidsstämpel med *ej tillgänglig* en avisering. Om en annan inställning än noll anges, utlöses inte kontinuerliga tidsstämplar efter den första tidsstämpeln som *inte är tillgänglig* enligt inställningen som angetts.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
- [Justera avvikelseidentifiering med hjälp av feedback](anomaly-feedback.md)
- [Diagnostisera en incident](diagnose-incident.md).
