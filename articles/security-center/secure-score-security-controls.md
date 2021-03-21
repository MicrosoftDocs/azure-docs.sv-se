---
title: Säkerhetspoäng i Azure Security Center
description: Beskrivning av Azure Security Center säkra poäng och säkerhets kontroller
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: b19a7c156abf32e2a0f6d70717145a6ed5ab42ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099683"
---
# <a name="secure-score-in-azure-security-center"></a>Säkerhetspoäng i Azure Security Center

## <a name="introduction-to-secure-score"></a>Introduktion till säkra Poäng

Azure Security Center har två huvudsakliga mål: 

- för att hjälpa dig att förstå den aktuella säkerhets situationen
- för att hjälpa dig att effektivt och effektivt förbättra din säkerhet

Den centrala funktionen i Security Center som gör att du kan uppnå dessa mål är **säkra Poäng**.

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation efter säkerhets problem. Den sammanställer sedan alla resultat i en enda poäng så att du snabbt kan tala om din aktuella säkerhets situation: ju högre poäng, desto lägre är den identifierade risk nivån.

De säkra poängen visas på Azure Portal sidor som ett procent värde, men de underliggande värdena visas också tydligt:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Övergripande säkra poäng som visas i portalen":::

Du kan öka säkerheten genom att granska Security Centerens rekommendationer-sida för de utestående åtgärder som krävs för att öka dina poäng. Varje rekommendation innehåller anvisningar som hjälper dig att åtgärda det aktuella problemet.

Rekommendationerna är grupperade i **säkerhets kontroller**. Varje kontroll är en logisk grupp relaterade säkerhets rekommendationer och återspeglar dina sårbara angrepps ytor. Poängen ökar bara när du reparerar *alla* rekommendationer för en enskild resurs i en kontroll. Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

Mer information finns i [så här beräknas din säkra Poäng](secure-score-security-controls.md#how-your-secure-score-is-calculated) nedan. 

## <a name="how-your-secure-score-is-calculated"></a>Så här beräknas din säkra Poäng 

Bidraget för varje säkerhets kontroll till den övergripande säkra poängen visas tydligt på sidan rekommendationer.

[![Den förbättrade säkra poängen introducerar säkerhets kontroller](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

För att få alla möjliga punkter för en säkerhets kontroll måste alla dina resurser följa alla säkerhets rekommendationer i säkerhets kontrollen. Security Center har till exempel flera rekommendationer om hur du skyddar dina hanterings portar. Du måste åtgärda dem för att göra en skillnad på dina säkra poäng.

Säkerhets kontrollen "tillämpa system uppdateringar" har till exempel en högsta poäng på sex punkter, som du kan se i knapp beskrivningen för kontrollens potentiella öknings värde:

[![Säkerhets kontrollen tillämpa system uppdateringar](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Den maximala poängen för den här kontrollen, tillämpa system uppdateringar, är alltid 6. I det här exemplet finns det 50 resurser. Vi delar upp max poängen med 50 och resultatet är att varje resurs bidrar med 0,12 punkter. 

* **Potentiell ökning** (0,12 x 8 Felaktiga resurser = 0,96) – de återstående punkterna som är tillgängliga för dig inom kontrollen. Om du reparerar alla rekommendationer i den här kontrollen kommer poängen att öka med 2% (i det här fallet 0,96 punkter avrundade uppåt till 1 punkt). 
* **Nuvarande Poäng** (0,12 x 42 friska resurser = 5,04) – aktuell Poäng för den här kontrollen. Varje kontroll bidrar till den totala poängen. I det här exemplet bidrar kontrollen 5,04 punkter till den aktuella säkra summan.
* **Högsta poäng** – det maximala antalet poäng som du kan få genom att slutföra alla rekommendationer inom en kontroll. Den maximala poängen för en kontroll anger den relativa betydelsen av kontrollen. Använd Max poäng värden för att prioritering problemen. 


### <a name="calculations---understanding-your-score"></a>Beräkningar – förstå dina Poäng

|Metric|Formel och exempel|
|-|-|
|**Säkerhets kontrollens aktuella Poäng**|<br>![Ekvation för att beräkna en säkerhets kontrolls Poäng](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Varje enskild säkerhets kontroll bidrar till säkerhets poängen. Varje resurs som påverkas av en rekommendation inom kontrollen bidrar till kontrollens aktuella resultat. Den aktuella poängen för varje kontroll är ett mått på statusen för resurserna *i* kontrollen.<br>![Knapp beskrivningar som visar de värden som används när du beräknar säkerhets kontrollens aktuella Poäng](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>I det här exemplet skulle max poängen på 6 divideras med 78 eftersom det är summan av de felfria och felaktiga resurserna.<br>6/78 = 0,0769<br>Om du multiplicerar det med antalet felfria resurser (4) resulterar det i den aktuella poängen:<br>0,0769 * 4 = **0,31**<br><br>|
|**Säkerhetspoäng**<br>Enstaka prenumeration|<br>![Ekvation för att beräkna en prenumerations säkra Poäng](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Säker Poäng för enskild prenumeration med alla kontroller aktiverade](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>I det här exemplet finns det en enda prenumeration med alla säkerhets kontroller som är tillgängliga (en potentiell högsta poäng på 60 punkter). Poängen visar 28 punkter av en möjlig 60 och de återstående 32 punkterna visas i siffrorna "potentiella Poäng ökning" i säkerhets kontrollerna.<br>![Lista över kontroller och potentiella Poäng ökningar](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Säkerhetspoäng**<br>Flera prenumerationer|<br>![Ekvation för att beräkna säkra Poäng för flera prenumerationer](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>När du beräknar de kombinerade poängen för flera prenumerationer innehåller Security Center en *vikt* för varje prenumeration. De relativa vikterna för dina prenumerationer bestäms av Security Center baserat på faktorer som antalet resurser.<br>Den aktuella poängen för varje prenumeration beräknas på samma sätt som för en enskild prenumeration, men sedan tillämpas vikten på det sätt som visas i ekvationen.<br>När du visar flera prenumerationer utvärderar säkra poäng alla resurser i alla aktiverade principer och grupperar deras kombinerade påverkan på varje säkerhets kontrolls maximala poäng.<br>![Säkra Poäng för flera prenumerationer med aktiverade kontroller](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Den kombinerade poängen är **inte** ett medelvärde. i stället är det utvärderat position av status för alla resurser i alla prenumerationer.<br>Om du går till sidan rekommendationer och lägger till tillgängliga tillgängliga punkter, kommer du att se att det är skillnaden mellan den aktuella poängen (24) och den maximala poängen som är tillgänglig (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Vilka rekommendationer ingår i de säkra Poäng beräkningarna?

Det är bara inbyggda rekommendationer som påverkar de säkra poängen.

Rekommendationer som har flaggats som **förhands granskning** ingår inte i beräkningarna av dina säkra poäng. De bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

Ett exempel på en förhands gransknings rekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Rekommendation med förhands gransknings flaggan":::

## <a name="improve-your-secure-score"></a>Förbättra dina säkerhetspoäng

Du kan förbättra dina säkra poäng genom att åtgärda säkerhets rekommendationerna från listan över rekommendationer. Du kan åtgärda varje rekommendation manuellt för varje resurs, eller genom att använda **snabb korrigering!** alternativ (när det är tillgängligt) för att tillämpa en reparation för en rekommendation till en grupp med resurser snabbt. Mer information finns i [åtgärda rekommendationer](security-center-remediate-recommendations.md).

Ett annat sätt att förbättra dina poäng och se till att användarna inte skapar resurser som negativt påverkar poängen är att konfigurera alternativen Genomdriv och neka på relevanta rekommendationer. Läs mer i [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Säkerhets kontroller och deras rekommendationer

I tabellen nedan visas säkerhets kontrollerna i Azure Security Center. För varje kontroll kan du se det maximala antalet punkter som du kan lägga till i dina säkra poäng om du reparerar *alla* rekommendationer som anges i kontrollen för *alla* dina resurser. 

Uppsättningen med säkerhets rekommendationer som medföljer Security Center är anpassad till de tillgängliga resurserna i varje organisations miljö. Rekommendationerna kan anpassas ytterligare genom att [inaktivera principer](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) och [undanta vissa resurser från en rekommendation](exempt-resource.md). 
 
Vi rekommenderar att varje organisation noggrant granskar sina tilldelade Azure Policy initiativ. 

> [!TIP]
> Mer information om hur du kan granska och redigera dina initiativ finns i [arbeta med säkerhets principer](tutorial-security-policy.md). 

Även om Security Centerens standard säkerhets initiativ baseras på bransch bästa praxis och standarder, finns det scenarier där de inbyggda rekommendationerna nedan kanske inte passar din organisation helt. Därför är det ibland nödvändigt att ändra standard initiativet – utan att kompromissa med säkerheten – så att den är anpassad till organisationens egna principer. bransch standarder, reglerande standarder och benchmarks som du är skyldig att uppfylla.<br><br>
<div class="foo">

<style type="text/css"> . TG {kant linje-komprimera: minimera; kant linje avstånd: 0;}. TG TD {kant linje färg: svart; kant linje format: solid; kant linje bredd: 1px; teckensnitts familj: Arial, sans-serif; font-size: 14px; spill: Hidden; utfyllnad: 10px 5px; Word-Break: normal;}. TG {kant linje färg: Black; kant linje format: solid; kant linje bredd: 1px; teckensnitts familj: Arial, sans-serif; font-size: 18px; font-weight: normal; spill: Hidden; utfyllnad: 10px 5px; ord-Break: normal;}. TG. TG-cly1 {text-align: Left; vertikalt-align: mitten}. TG. TG-lboi {kant linje färg: Ärv; text – justera: vänster; lodrätt justerad: mitten} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Vanliga frågor och svar om säker Poäng

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Om jag bara har tre av fyra rekommendationer i en säkerhets kontroll, kommer mina säkra poäng att ändras?
Nej. Den ändras inte förrän du reparerar alla rekommendationer för en enskild resurs. För att få den högsta poängen för en kontroll måste du åtgärda alla rekommendationer för alla resurser.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Om en rekommendation inte gäller mig och jag inaktiverar den i principen, kommer min säkerhets kontroll att uppfyllas och mina säkra resultat uppdateras?
Ja. Vi rekommenderar att du inaktiverar rekommendationer när de inte är tillämpliga i din miljö. Instruktioner för hur du inaktiverar en speciell rekommendation finns i [inaktivera säkerhets principer](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Om en säkerhets kontroll ger mig noll punkter mot mina säkra poäng, ska jag ignorera den?
I vissa fall visas en kontroll över max poängen som är större än noll, men effekten är noll. När den stegvisa poängen för att lösa resurserna är försumbar, avrundas den till noll. Ignorera inte de här rekommendationerna eftersom de fortfarande ger säkerhets förbättringar. Det enda undantaget är kontrollen "ytterligare bästa praxis". Att åtgärda dessa rekommendationer ökar inte dina poäng, men det förbättrar den övergripande säkerheten.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs säkra poäng och de säkerhets kontroller som det introducerar. Information om relaterade material finns i följande artiklar:

- [Lär dig mer om de olika elementen i en rekommendation](security-center-recommendations.md)
- [Lär dig hur du åtgärdar rekommendationer](security-center-remediate-recommendations.md)
- [Visa GitHub-baserade verktyg för att arbeta program mässigt med säkra Poäng](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Få åtkomst till och spåra dina säkra Poäng](secure-score-access-and-track.md)