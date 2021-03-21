---
title: Undvik övermontering & obalanserade data med AutoML
titleSuffix: Azure Machine Learning
description: Identifiera och hantera vanliga fall GRO par av ML-modeller med Azure Machine Learning automatiserade maskin inlärnings lösningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96922626"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Förhindra överanpassning och obalanserade data med automatiserad maskin inlärning

Överanpassning och disbalanserade data är vanliga fall GRO par när du skapar maskin inlärnings modeller. Som standard tillhandahåller Azure Machine Learning s automatiserade maskin inlärning diagram och mått för att hjälpa dig att identifiera dessa risker och implementera bästa metoder för att minimera dem. 

## <a name="identify-over-fitting"></a>Identifiera överanpassning

Överanpassning i maskin inlärning sker när en modell passar inlärnings data för bra, och det kan leda till att det inte är korrekt förutsägelse på osett test data. Med andra ord har modellen helt enkelt bevisat vissa mönster och brus i tränings data, men är inte tillräckligt flexibel för att göra förutsägelser på verkliga data.

Tänk på följande utbildade modeller och deras motsvarande tåg-och test noggrannhet.

| Modell | Träna precision | Test noggrannhet |
|-------|----------------|---------------|
| A | 99,9 % | 95 % |
| B | 87% | 87% |
| C | 99,9 % | 45 % |

Med tanke på modell **A**, finns det en vanlig felbegrepp att om test precisionen för osett-data är lägre än den korrekta inlärningen är modellen överdrivet. Test precisionen bör dock alltid vara mindre än inlärnings precisionen och skillnaden för överanpassning jämfört med lämplig anpassning visas till *hur mycket* mindre exakt. 

När du jämför modeller **a** och **B** är modell **a** en bättre modell eftersom den har högre test precision, och även om test noggrannheten är något lägre vid 95% är det inte en stor skillnad som tyder på överanpassning. Du väljer inte modell **B** eftersom tågets och testernas noggrannhet är närmare tillsammans.

Modell **C** är ett tydligt Skift läge vid överanpassning. inlärnings precisionen är mycket hög men test noggrannheten är inte tillgänglig någonstans i närheten. Den här skillnaden är underkänd, men kommer från kännedom om ditt problem och dina data och vilka fel som kan uppstå.

## <a name="prevent-over-fitting"></a>Förhindra överanpassning

I de flesta egregious-fall förutsätter en överordnad modell att de kombinationer av funktions värden som visas under utbildningen alltid ger exakt samma utdata för målet.

Det bästa sättet att förhindra överanpassning är att följa ML Best-Practices, inklusive:

* Använda fler utbildnings data och ta bort statistisk kompensation
* Förhindra mål läckage
* Använda färre funktioner
* **Regulariseringshastigheten och dess parameter optimering**
* **Komplexitets begränsningar för modeller**
* **Korsvalidering**

I samband med automatisk ML är de första tre objekten ovan de **bästa metoderna som du implementerar**. De tre sista objekten med fet stil är **bästa metoder för automatisk ml-implementering** som standard för att skydda mot överanpassning. I andra inställningar än automatiserade ML är alla sex bästa metoder följande för att undvika överanpassnings modeller.

### <a name="best-practices-you-implement"></a>Metod tips som du implementerar

Att använda **mer data** är det enklaste och bästa möjliga sättet att förhindra överanpassning, och som en extra bonus ökar precisionen ofta. När du använder mer data blir det svårare för modellen att memorera exakta mönster, och det tvingas att uppnå lösningar som är mer flexibla för att hantera fler villkor. Det är också viktigt att känna igen **statistisk kompensation** för att säkerställa att dina utbildnings data inte innehåller isolerade mönster som inte finns i real tids förutsägelse data. Det här scenariot kan vara svårt att lösa eftersom det kanske inte finns överanvändning mellan dina tåg-och test uppsättningar, men det kan finnas överanvändning i jämförelse med Live test-data.

**Mål läckage** är ett liknande problem, där du kanske inte ser överanvändning mellan träna/test-uppsättningar, men i stället visas den i förutsägelse tiden. Mål läckage uppstår när din modell "fuskar" under utbildningen genom att ha åtkomst till data som den inte bör ha vid förutsägelse tiden. Om ditt problem till exempel är att förutsäga på måndagen är ett råvaru pris på fredag, men en av dina funktioner av misstag som innehåller data från torsdagar, som skulle vara data modellen kommer inte att ha vid förutsägelse, eftersom den inte kan se i framtiden. Mål läckage är ett enkelt misstag att sakna, men karakteriseras ofta av onormalt hög precision för ditt problem. Om du försöker förutsäga börs priset och tränat en modell med 95% noggrannhet, finns det troligen mål som läcker i dina funktioner.

Att **ta bort funktioner** kan också bidra till överanpassning genom att förhindra att modellen har för många fält att använda för att komma ihåg vissa mönster, vilket gör att det blir mer flexibelt. Det kan vara svårt att mäta kvantitativt, men om du kan ta bort funktioner och behålla samma precision har du förmodligen gjort modellen mer flexibel och minskat risken för överanpassning.

### <a name="best-practices-automated-ml-implements"></a>Metod tips automatiserad ML implementeras

**Regulariseringshastigheten** är en process för att minimera en kostnads funktion för att beivra komplexa och över-anpassade modeller. Det finns olika typer av regulariseringshastigheten-funktioner, men i allmänhet tar de med sig en koefficient för modell effektiv storlek, varians och komplexitet. Med automatisk ML används L1 (lasso), L2 (Ridge) och ElasticNet (L1 och L2 samtidigt) i olika kombinationer med olika modell inställningar för en modell som styr överanpassningen. I enkla termer varierar automatiserade ML till hur mycket en modell är reglerad och väljer det bästa resultatet.

Med automatisk ML implementeras även explicita **begränsningar för modell komplexitet** för att förhindra överanpassning. I de flesta fall är den här implementeringen särskilt för besluts träd eller skogs-algoritmer, där det enskilda trädets max djup är begränsat och det totala antalet träd som används i skogs-eller Ensemble-teknikerna är begränsade.

**Kors validering (ka)** är en process där du tar många del mängder av dina fullständiga tränings data och tränar en modell på varje delmängd. Idén är att en modell kan få "fram" och ha stor precision med en delmängd, men genom att använda många del uppsättningar kan modellen inte uppnå denna höga noggrannhet varje gång. När du gör CV anger du en data uppsättning för en verifierings spärr, anger dina CV-vikningar (antal del mängder) och automatiserade ML tränar din modell och justerar de båda parametrarna för att minimera fel i validerings uppsättningen. En ka-vikning kan vara överdrivet, men genom att använda många av dem minskar sannolikheten att den slutliga modellen är överdrivet. Kompromissen är att ka resulterar i längre inlärnings tider och därmed högre kostnad, eftersom i stället för att träna en modell en gång, träna den en gång för varje *n* CV-deluppsättning. 

> [!NOTE]
> Kors validering är inte aktiverat som standard. den måste konfigureras i automatiserade ML-inställningar. Men när kors validering har kon figurer ATS och en verifierings data uppsättning har angetts automatiseras processen. Läs mer om [konfiguration av kors validering i Auto ml](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identifiera modeller med obalanserade data

Obalanserade data finns vanligt vis i data om klassificerings scenarier för maskin inlärning och refererar till data som innehåller ett oproportionerligt förhållande mellan observationerna i varje klass. Den här obalansen kan leda till en felaktigt uppfattad positiv effekt av en modells exakthet, eftersom indatan har en förskjutning mot en klass, vilket resulterar i att den tränade modellen imiterar den bias. 

Dessutom genererar automatiserade ML-körningar följande diagram automatiskt, som kan hjälpa dig att förstå rätthet i klassificeringen av din modell och identifiera modeller som kan påverkas av data som är fördelade.

Diagram| Beskrivning
---|---
[Förvirring mat ris](how-to-understand-automated-ml.md#confusion-matrix)| Utvärderar de korrekt klassificerade etiketterna mot de faktiska etiketterna i data. 
[Precision – återkalla](how-to-understand-automated-ml.md#precision-recall-curve)| Utvärderar förhållandet mellan korrekta etiketter mot förhållandet mellan hittade etikett instanser av data 
[ROC kurvor](how-to-understand-automated-ml.md#roc-curve)| Utvärderar förhållandet mellan korrekta etiketter mot förhållandet falskt positiva etiketter.

## <a name="handle-imbalanced-data"></a>Hantera förbalanserade data 

Som en del av målet att förenkla arbets flödet för Machine Learning **har automatiserad ml byggts med funktioner** som hjälper dig att hantera obalanserade data, till exempel 

- En **Vikt kolumn**: automatisk ml har stöd för en kolumn med vikter som indata, vilket gör att rader i data kan viktas upp eller ned, vilket kan användas för att göra en klass mer eller mindre viktig.

- Algoritmerna som används av automatiserad ML identifierar obalans när antalet prover i minoritets klassen är lika med eller mindre än 20% av antalet prover i den största klassen, där minoritets klass refererar till den med minsta möjliga exempel och majoritets klass refererar till den som har flest exempel. Därefter kommer AutoML att köra ett experiment med undersamplade data för att kontrol lera om klass vikter skulle åtgärda problemet och förbättra prestandan. Om det ger bättre prestanda genom det här experimentet tillämpas denna påföljd.

- Använd ett prestanda mått som fungerar bättre med obalanserade data. AUC_weighted är till exempel ett primärt mått som beräknar bidraget för varje klass baserat på det relativa antalet prover som representerar klassen, och därför är mer robust mot obalans.

Följande tekniker är ytterligare alternativ för att hantera sambalanserade data **utanför automatiserad ml**. 

- Omsampling till till och med klassen obalans, antingen genom att ta sampling av de mindre klasserna eller nedåt, och samtidigt ta bort de större klasserna. Dessa metoder kräver expertis för att bearbeta och analysera.

- Granska prestanda mått för obalanserade data. Till exempel är F1-poängen det harmoniska medelvärdet för precision och återkallande. Precision mäter en klassificerares exaktahet, där högre precision visar färre falska positiva identifieringar, medan återkallande mäter en klassificerares fullständighet, där högre åter kallelse visar färre falska negativa värden.

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk maskin inlärning:

+ Följ [själv studie kursen: träna en Regressions modell automatiskt med Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningarna för automatiskt utbildnings experiment:
  + [Använd de här stegen](how-to-use-automated-ml-for-ml-models.md)i Azure Machine Learning Studio.
  + [Använd de här stegen](how-to-configure-auto-train.md)med python SDK.


