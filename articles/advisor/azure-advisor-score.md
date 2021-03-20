---
title: Optimera Azure-arbetsbelastningar med hjälp av Advisor-Poäng
description: Använd Azure Advisor Poäng för att få ut mesta möjliga av Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630130"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Optimera Azure-arbetsbelastningar med hjälp av Advisor-Poäng

## <a name="introduction-to-advisor-score"></a>Introduktion till Advisor-Poäng

Azure Advisor innehåller rekommendationer för bästa praxis för dina arbets belastningar. Dessa rekommendationer är anpassade och åtgärdade för att hjälpa dig:

* Förbättra position i dina arbets belastningar och optimera dina Azure-distributioner.
* Förhindra de vanligaste problemen proaktivt genom att följa bästa praxis.
* Utvärdera dina Azure-arbetsbelastningar mot de fem pelarna i [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Som grund funktion i Advisor kan Advisor-poängen hjälpa dig att uppnå dessa mål effektivt och effektivt.

För att få ut mesta möjliga av Azure är det viktigt att förstå var du befinner dig i din arbets belastnings optimerings resa. Du måste veta vilka tjänster eller resurser som konsumeras väl och vilka som inte är det. Dessutom vill du veta hur du prioriterar dina åtgärder, baserat på rekommendationer, för att maximera resultatet.

Det är också viktigt att spåra och rapportera förloppet som du gör i optimerings resan. Med Advisor-poängen kan du enkelt göra alla dessa saker med den nya spelifieringsprogram-upplevelsen.

Som din personliga moln konsult kan Azure Advisor kontinuerligt utvärdera din användnings telemetri och resurs konfiguration för att söka efter bransch bästa praxis. Advisor sammanställer sedan sina resultat till en enda poäng. Med den här poängen kan du snabbt ta reda på om du vidtar de åtgärder som krävs för att bygga pålitliga, säkra och kostnads effektiva lösningar.

Advisor-poängen består av en övergripande Poäng som kan delas upp i fem kategori resultat. En poäng för varje kategori av rådgivare representerar de fem pelarna i Well-Architecteds ramverket.

Du kan spåra förloppet med tiden genom att visa dina totala poäng-och kategori poäng med dagliga, veckovis och månatliga trender. Du kan också ange benchmarks för att hjälpa dig att uppnå dina mål.

 ![Skärm bild som visar sidan Advisor-poäng.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Tolka ett Advisor-Poäng

Advisor visar den övergripande Advisor-poängen och en analys av Advisor-kategorier i procent. En poäng på 100% i valfri kategori innebär att alla resurser som utvärderas av Advisor följer de metoder som rekommenderas av Advisor. I andra änden av spektrumet innebär resultatet 0% att ingen av dina resurser utvärderas av Advisor enligt rådgivarens rekommendationer. Med hjälp av dessa Poäng kärnor kan du enkelt uppnå följande flöde:

* **Advisor-Poäng** hjälper dig att få en bas linje för hur din arbets belastning eller dina prenumerationer bygger på en Advisor-poäng. Du kan också se de historiska trenderna för att förstå vad din trend är.
* **Poäng per kategori** för varje rekommendation visar vilka enastående rekommendationer som ger flest poäng. Dessa värden återspeglar både vikten av rekommendationen och den förutsägbara enkla implementeringen. De här faktorerna hjälper till att se till att du får ut det bästa värdet med din tid. De hjälper dig också med prioriteringen.
* **Resultat från kategori poängen** för varje rekommendation hjälper dig att prioritera dina reparations åtgärder för varje kategori.

Bidragen från varje rekommendation till kategori poängen visas tydligt på sidan Advisor- **Poäng** i Azure Portal. Du kan öka varje kategori poäng enligt procent andelen som visas i kolumnen **potentiella Poäng ökningar** . Det här värdet visar både vikt för rekommendationen inom kategorin och den förutsägbara enkla implementeringen för att hantera de potentiellt enklaste uppgifterna. Genom att fokusera på rekommendationerna med den största poängen kan du få ut så mycket som möjligt av tiden.

![Skärm bild som visar betygs effekten i Advisor.](./media/advisor-score-2.png)

Om några rekommendationer inte är relevanta för en enskild resurs kan du skjuta upp eller stänga av dessa rekommendationer. De kommer att uteslutas från Poäng beräkningen med nästa uppdatering. Advisor kommer också att använda den här ingången som ytterligare feedback för att förbättra modellen.

## <a name="how-is-an-advisor-score-calculated"></a>Hur beräknas ett Advisor-Poäng?

Advisor visar kategori poängen och den övergripande Advisor-poängen i procent. En poäng på 100% i valfri kategori innebär att alla resurser, *som utvärderas av Advisor*, följer de rekommenderade metoder som rådgivare rekommenderar. I andra änden av spektrumet innebär resultatet 0% att ingen av dina resurser, utvärderas av Advisor, följer Advisor-rekommendationer.

**Var och en av de fem kategorierna har ett högsta möjliga resultat på 100.** Den totala Advisor-poängen beräknas som en summa av varje tillämplig kategori poäng, dividerat med summan av de högsta potentiella poängen från alla tillämpliga kategorier. För de flesta prenumerationer innebär det att Advisor lägger till poängen från varje kategori och dividerar med 500. *Varje kategori Poäng beräknas bara om du använder resurser som utvärderas av Advisor*.

### <a name="advisor-score-calculation-example"></a>Exempel på Advisor Poäng beräkning

* **Poäng för enskild prenumeration:** Det här exemplet är det enkla medelvärdet av alla kategori resultat för Advisor för din prenumeration. Om Advisor-kategorin Scores är- **Cost** = 73, **tillförlitlighet** = 85, **drift kvalitet** = 77 och **prestanda** = 100 skulle Advisor-poängen vara (73 + 85 + 77 + 100)/(4x100) = 0,84% eller 84%.
* **Poäng för flera prenumerationer:** När flera prenumerationer har valts är de totala Advisor-poängen skapade, viktade sammanställda kategori resultat. Här sammanställs varje rådgivares kategori poäng baserat på resurser som används av prenumerationer. När Advisor har den viktade sammanställda kategori poängen gör Advisor en enkel genomsnitts beräkning för att ge dig en övergripande Poäng för prenumerationer.

### <a name="scoring-methodology"></a>Bedömnings metod

Beräkningen av Advisor-poängen kan sammanfattas i fyra steg:

1. Advisor beräknar *detalj handels kostnaden för påverkade resurser*. De här resurserna är de som finns i dina prenumerationer som har minst en rekommendation i Advisor.
1. Advisor beräknar *detalj handels kostnaden för utvärderade resurser*. Dessa resurser är de som övervakas av Advisor, oavsett om de har några rekommendationer eller inte.
1. För varje rekommendations typ beräknar Advisor det *felfria resurs förhållandet*. Den här kvoten är detalj handels kostnaden för påverkade resurser dividerat med detalj kostnaden för utvärderade resurser.
1. Advisor använder tre ytterligare vikter för det felfria resurs förhållandet i varje kategori:

   * Rekommendationer med större påverkan viktas tyngre än rekommendationer med lägre påverkan.
   * Resurser med långsiktiga rekommendationer kommer att räkna mer än dina poäng.
   * Resurser som du skjuter upp eller tar bort i Advisor tas helt och hållet bort från dina poäng beräkningar.

Advisor använder den här modellen på en klassificerings kategori nivå för att ge en rådgivare för varje kategori. **Säkerhet** använder en [säker Poäng](../security-center/secure-score-security-controls.md#introduction-to-secure-score) modell. Ett enkelt genomsnitt producerar den slutliga Advisor-poängen.

## <a name="advisor-score-faqs"></a>Vanliga frågor och svar om Advisor

### <a name="how-often-is-my-score-refreshed"></a>Hur ofta uppdateras min Poäng?

Poängen uppdateras minst en gång per dag.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Varför har vissa rekommendationer värdet Empty "-" i kolumnen Kategori score-effekt?

Advisor innehåller inte omedelbart nya rekommendationer eller rekommendationer med de senaste ändringarna i bedömnings modellen. Efter en kort utvärderings period är det vanligt vis några veckor som de ingår i poängen.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Varför påverkar kostnads poängen bättre för vissa rekommendationer även om de har lägre potentiella besparingar?

Din **kostnads** Poäng visar både dina potentiella besparingar från underutnyttjade resurser och den förutsägbara enkla implementeringen av dessa rekommendationer. Extra vikt används till exempel för påverkade resurser som har varit inaktiva under en längre tid, även om de potentiella besparingarna är lägre.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Varför har jag ingen Poäng för en eller flera kategorier eller prenumerationer?

Advisor genererar bara Poäng för de kategorier och prenumerationer som har resurser som utvärderas av Advisor.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Vad händer om en rekommendation inte är relevant?

Om du stänger av rekommendation från Advisor kommer den att utelämnas från beräkningen av dina poäng. När du stänger av rekommendationerna kan du också förbättra rekommendationernas kvalitet.

### <a name="why-did-my-score-change"></a>Varför har min poäng ändring?

Poängen kan ändras om du reparerar påverkade resurser genom att använda bästa praxis som rådgivare rekommenderar. Om du eller någon med behörigheter för din prenumeration har ändrat eller skapat nya resurser kan du också se fluktuationer i poängen. Poängen baseras på en kvot av de kostnads resurser som påverkas i förhållande till den totala kostnaden för alla resurser.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Hur beräknar Advisor detalj handels kostnaden för resurser i en prenumeration?

Advisor använder priserna för betala per användning som publicerats på [Azures priser](https://azure.microsoft.com/pricing/). De här priserna återspeglar inte några tillämpliga rabatter. Priserna multipliceras sedan med antalet användnings dagar den sista dagen då resursen allokerades. Om du utelämnar rabatter från beräkningen av resurs kostnaden blir Advisor jämförbar över alla prenumerationer, klienter och registreringar där rabatter kan variera.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Behöver jag se rekommendationerna i Advisor för att få poäng för mina Poäng?

Nej. Poängen visar om du använder bästa praxis som rådgivare rekommenderar, även om du antar dessa bästa praxis proaktivt och aldrig ser dina rekommendationer i Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Skiljer sig bedömnings metoden mellan arbets belastningar för produktions-och dev-test?

Nej, inte just nu. Men du kan stänga rekommendationer för enskilda resurser om dessa resurser används för utveckling och testning och rekommendationerna inte gäller.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Kan jag jämföra resultatet mellan en prenumeration med 100 resurser och en prenumeration med 100 000-resurser?

Bedömnings metoden är utformad för att styra antalet resurser i en prenumeration och tjänst kombination. Prenumerationer med färre resurser kan ha högre eller lägre poäng än prenumerationer med fler resurser.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Vad betyder det när jag ser "kommer snart" i kolumnen med Poäng påverkan?

Det här meddelandet innebär att rekommendationen är ny och vi arbetar med att ta med den till bedömnings Poäng modellen. När den här nya rekommendationen har beaktats i en poäng beräkning visas resultatet av din rekommendation.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Beror resultatet på hur mycket jag tillbringar på Azure?

Nej. Poängen är inte nödvändigt vis en reflektion av hur mycket du lägger på. Onödiga utgifter resulterar i ett lägre **kostnads** poäng.

## <a name="access-advisor-score"></a>Åtkomst Advisor-Poäng

Advisor-poängen är i offentlig för hands version i Azure Portal. I det vänstra fönstret, under avsnittet **Advisor** , se **Advisor-Poäng**.

![Skärm bild som visar start punkten för Advisor poäng.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer i Advisor](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
