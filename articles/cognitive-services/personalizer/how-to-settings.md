---
title: Konfigurera Personanpassning
description: Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91292617"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurera en inlärnings slinga

Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.

Konfigurera inlärnings-slingan på sidan **konfiguration** i Azure Portal för den här personanpassa resursen.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Planera konfigurations ändringar

Eftersom en del konfigurations ändringar [återställer din modell](#settings-that-include-resetting-the-model)bör du planera dina konfigurations ändringar.

Om du planerar att använda [lärlings läget](concept-apprentice-mode.md), se till att granska din anpassnings konfiguration innan du växlar till lärlings läge.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Inställningar som inkluderar återställning av modellen

Följande åtgärder utlöser en omskolning av modellen med hjälp av data som är tillgängliga upp till de senaste 2 dagarna.

* Annan
* Utforskning

Om du vill [Rensa](how-to-manage-model.md) alla data använder du sidan **modell-och inlärnings inställningar** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurera förmåner för feedback-slingan

Konfigurera tjänsten för din inlärnings slingas användning av förmåner. Ändringar av följande värden kommer att återställa den aktuella personanpassa modellen och träna den med de senaste två dagarnas data.

> [!div class="mx-imgBorder"]
> ![Konfigurera belönings värden för feedback-slingan](media/settings/configure-model-reward-settings.png)

|Värde|Syfte|
|--|--|
|Vänte tid för belöning|Anger efter hur lång tid som Personanpassaren samlar in belönings värden för ett rang samtal, från det ögonblick då rang anropet inträffar. Det här värdet anges genom att fråga: "hur länge ska personanpassas vänta på förmåner-samtal?" Alla ersättningar som anländer efter att det här fönstret loggas men inte används för inlärning.|
|Standard belöning|Om inget belönings samtal tas emot av en Personanpassare under fönstret för fördröjnings vänte tid som är associerat med ett rang samtal, tilldelar Personanpassaren standard belöningen. Som standard är standard belöningen noll (0) i de flesta fall.|
|Belönings agg regering|Om flera fördelar tas emot för samma rang-API-anrop används denna agg regerings metod: **Sum** eller **tidigast**. Tidigaste plockning av den tidigaste poängen och tar bort resten. Detta är användbart om du vill ha en unik belöning mellan kanske dubbla samtal. |

När du har ändrat dessa värden måste du se till att välja **Spara**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Konfigurera utforskning så att inlärnings slingan kan anpassas

Anpassningar kan identifiera nya mönster och anpassa sig till användar beteende ändringar över tid genom att utforska alternativ i stället för att använda den tränade modellens förutsägelse. Värdet för **prospektering** avgör hur stor procent andel rang samtal besvaras med utforskning.

Ändringar av det här värdet kommer att återställa den aktuella personanpassa modellen och träna den med de senaste två dagarnas data.

![Värdet för prospektering avgör hur stor procent andel rang samtal besvaras med utforskning](media/settings/configure-exploration-setting.png)

När du har ändrat det här värdet ska du se till att välja **Spara**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurera modell uppdaterings frekvens för modell träning

**Modell uppdaterings frekvensen** anger hur ofta modellen tränas.

|Inställning för frekvens|Syfte|
|--|--|
|1 minut|Uppdaterings frekvensen på en minut är användbar när du **felsöker** ett programs kod med hjälp av personanpassa, gör demonstrationer eller interaktivt testar maskin inlärnings aspekter.|
|15 minuter|Hög modell uppdaterings frekvens är användbart i situationer där du vill **Spåra ändringar** i användar beteenden. Exempel på detta är webbplatser som körs på direktsända nyheter, virus innehåll eller Live Product-budgivning. Du kan använda en frekvens på 15 minuter i dessa scenarier. |
|1 timme|För de flesta användnings fall är en lägre uppdaterings frekvens effektiv.|

![Modell uppdaterings frekvens anger hur ofta en ny personanpassa modell ska omtränas.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

När du har ändrat det här värdet ska du se till att välja **Spara**.

## <a name="data-retention"></a>Datakvarhållning

**Data lagrings period** anger hur många dagars personanpassare som ska behålla data loggar. Tidigare data loggar krävs för att utföra [offline-utvärderingar](concepts-offline-evaluation.md)som används för att mäta effektiviteten hos personanpassare och optimera inlärnings policyn.

När du har ändrat det här värdet ska du se till att välja **Spara**.



## <a name="next-steps"></a>Nästa steg

[Lär dig hur du hanterar din modell](how-to-manage-model.md)
