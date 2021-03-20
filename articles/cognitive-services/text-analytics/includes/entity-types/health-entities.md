---
title: Namngivna entiteter för hälso vård
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599345"
---
[Textanalys för hälso](../../how-tos/text-analytics-for-health.md) processer och extrahera insikter från ostrukturerade medicinska data. Tjänsten identifierar och underlagar medicinska koncept, tilldelar kontroller till koncept, härleder semantiska relationer mellan koncept och länkar dem till gemensamma medicinska Ontologies.

Textanalys för hälsa upptäcker medicinska begrepp i följande kategorier. Endast engelsk text stöds i den här för hands versionen och endast en enda modell version är tillgänglig.

| Kategori  | Beskrivning  |
|---------|---------|
| [UPPBYGGNAD](#anatomy) | begrepp som samlar in information om brödtext och anatomiska system, platser, platser eller regioner. |
 | [DEMOGRAFI](#demographics) | koncept som samlar in information om kön och ålder. |
 | [UNDERSÖKNING](#examinations) | begrepp som samlar in information om diagnostiska procedurer och tester. |
 | [ALLMÄNNA ATTRIBUT](#general-attributes) | begrepp som innehåller mer information om andra begrepp från kategorierna ovan. |
 | [GENOMICS](#genomics) | begrepp som samlar in information om gener och varianter. |
 | [HEALTHCARE](#healthcare) | begrepp som samlar in information om administrativa händelser, vård miljöer och sjukvårds yrkes personal. |
 | [MEDICINSKA VILLKOR](#medical-condition) | begrepp som samlar in information om diagnoser, symtom eller loggar. |
 | [LÄKEMEDEL](#medication) | begrepp som samlar in information om medicin, inklusive namn på medicin, klasser, dosering och administrerings väg. |
 | [FAVORIT](#social) | begrepp som samlar in information om medicinskt relevanta sociala aspekter som familje relation. |
 | [HANTERING](#treatment) | begrepp som samlar in information om terapeutiska procedurer. |

Du hittar mer information och exempel nedan.

## <a name="anatomy"></a>Uppbyggnad

### <a name="entities"></a>Entiteter

**BODY_STRUCTURE** -Body-system, anatomiska platser eller regioner och kropps webbplatser. Till exempel arm, knee, buk, näsa, lever, huvud, andnings system, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Ett exempel på Body Structure-entiteten.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Ett expanderat exempel på entiteten Body Structure.":::

## <a name="demographics"></a>Demografi

### <a name="entities"></a>Entiteter

**Ålder** – alla ålders villkor och-fraser, inklusive för patienter, familje medlemmar och andra. Till exempel 40-årets gamla, 51 Yo, 3 månader gamla, vuxna, spädbarn, äldre, unga, små, mellanliggande.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Ett exempel på en ålders enhet.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Ett annat exempel på en ålders enhet.":::


**Kön** -villkor som avslöjar ärende kön. Till exempel hane, hona, Kvinna, gentleman, fru.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Ett exempel på en köns entitet.":::

## <a name="examinations"></a>Undersökningar

### <a name="entities"></a>Entiteter

**EXAMINATION_NAME** – diagnostiska procedurer och tester, inklusive viktiga tecken och mått på brödtext. Till exempel, MRI, ECG, HIV-test, hemoglobin, antal buffertar, skala system som *Bristol pall Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Ett exempel på en examen-enhet.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Ett annat exempel på en entitet för undersöknings namn.":::

## <a name="general-attributes"></a>Allmänna attribut

### <a name="entities"></a>Entiteter

**Datum** – fullständigt datum som avser medicinska villkor, undersökningar, behandling, medicin eller administrativa händelser.

**Riktning** – riktade termer som kan vara knutna till en brödtext, medicinskt tillstånd, undersökning eller behandling, t. ex.: Left, sido, versal, i efterhand.

**Frekvens** – beskriver hur ofta ett hälso tillstånd, en undersökning, en behandling eller ett sjukdoms fel inträffar eller ska ske.

**MEASUREMENT_VALUE** – värdet för en undersökning eller en mätning av medicinska villkor.

**MEASUREMENT_UNIT** – mått enheten som är relaterad till en undersökning eller ett läkar villkor.

**RELATIONAL_OPERATOR** fraser som uttrycker den kvantitativa relationen mellan en entitet och viss ytterligare information.

 Tidstemporala villkor som avser start och/eller längd (varaktighet) för medicinska villkor, undersökningar, behandling, medicin eller administrativa händelser. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entiteter

**GENE_OR_PROTEIN** – alla omnämnanden av namn och symboler för människo gener samt kromosomer och delar av kromosomer och proteiner. Till exempel MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Ett exempel på en gen organisation.":::

**Variant** – alla omnämnanden av gen variationer och mutationer. Till exempel `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Sjukvård

### <a name="entities"></a>Entiteter
  
**ADMINISTRATIVE_EVENT** – händelser som relaterar till sjukvårds systemet men av en administrativ/halv administrativ natur. Till exempel registrering, upptagande, utvärdering, studie post, överföring, urladdning, sjukhus vistelser, sjukhus vistelser. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Ett exempel på en hälsoenhet för hälso vårds händelser.":::

**CARE_ENVIRONMENT** – en miljö eller en plats där patienter ges. Till exempel nöd rum, läkares kontor, Cardio-enhet, Hospice, sjukhus.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Den här skärm bilden visar ett exempel på en sjukvårds miljö enhet.":::

**HEALTHCARE_PROFESSION** – en läkare som är hälsovård eller icke-licensierad. Till exempel tandläkare, pathologist, neurologist, radiologist, pharmacist, näringsverksamhet, fysisk Therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Den här skärm bilden visar ett annat exempel på en sjukvårds miljö enhet.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Ett annat exempel på en sjukvårds miljö enhet.":::

## <a name="medical-condition"></a>Medicinska villkor

### <a name="entities"></a>Entiteter

**Diagnos** – sjukdom, Syndrome, förgiftning. Till exempel bröst cancer, Alzheimers, HTN, CHF, ryggradens skada.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Ett exempel på en entitet för medicinskt tillstånd.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Ett annat exempel på en entitet för medicinskt tillstånd.":::

**SYMPTOM_OR_SIGN** – ämnes-eller objektiva bevis på sjukdom eller andra diagnoser. Till exempel är Chest smärta,, dizziness, Rash, SOB, buken mjuk, bra bowela ljud och nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Ett exempel på ett hälso tillstånds tecken eller en symptom enhet.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Ett annat exempel på ett hälso tillstånds tecken eller en symptom enhet.":::

**CONDITION_QUALIFIER** – kvalitativa villkor som används för att beskriva ett hälso tillstånd. Alla följande under Kategorier betraktas som kvalificerare:

1.  Tidsrelaterade uttryck: de är termer som beskriver tids dimensionen kvalitativt, till exempel plötslig, akut, kronisk, värnar sedan länge. 
2.  Kvalitets uttryck: de är termer som beskriver "natur" i det medicinska tillståndet, till exempel att bränna, skärpa.
3.  Allvarlighets GRADS uttryck: svåra, vissa, en bit, okontrollerad.
4.  Extensivity-uttryck: lokal, brännvidd, spridning.
5.  Strål uttryck: strålar, strålning.
6.  Villkors skala: i vissa fall karakteriseras ett villkor av en skala, vilket är en begränsad sorterad lista med värden. Till exempel patienter med steg III Pancreatic Cancer.
7.  Villkors kurs: ett villkor som relaterar till en kurs eller ett villkors förlopp, till exempel förbättring, förvärrande, upplösning och åter betalning. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Ett exempel på ett condition Qualifier-attribut och en diagnostisk entitet.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Ett annat exempel på ett condition Qualifier-attribut och en diagnostisk entitet.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Ett exempel på ett condition Qualifier-attribut med ett symtom på symtom och medicin.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Den här skärm bilden visar ett annat exempel på ett condition Qualifier-attribut med en diagnostisk entitet.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Den här skärm bilden visar ytterligare ett exempel på ett condition-kvalificerat attribut med en diagnostisk entitet.":::

## <a name="medication"></a>Läkemedel

### <a name="entities"></a>Entiteter

**MEDICATION_CLASS** – en uppsättning medications som har en liknande mekanism som åtgärd, ett relaterat åtgärds läge, en liknande kemisk struktur och/eller används för att behandla samma sjukdom. Till exempel ACE inhibitor, opioid, antibiotika, lacker.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Ett exempel på en entitet för en medicin klass.":::

**MEDICATION_NAME** – medicin omnämnande, inklusive namn på upphovsrättsskyddade varumärken och namn som inte är varumärken. Till exempel ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Ett exempel på en entitet för namn på medicin.":::

**Dosering** -mängd som har beställts. Till exempel integrera natrium klo RID lösning *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Ett exempel på ett attribut för en medicin-dosering.":::

**MEDICATION_FORM** -form av medicin. Till exempel lösning, Pill, kapsel, surfplatta, korrigering, gel, inklistring, skum, spray, droppar, grädde, grädde.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Ett exempel på ett medicin Formulär-attribut.":::

**MEDICATION_ROUTE** – administrations metoden för medicin. Till exempel, oralt, vaginal, IV, epidural, därför aktuell, inandning.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Ett exempel på ett medicin Route-attribut.":::

## <a name="social"></a>Socialt

### <a name="entities"></a>Entiteter

**FAMILY_RELATION** – omnämnande av ämnets familje släktingar. Till exempel far, dotter, syskon, överordnade objekt.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Skärm bild som visar ett annat exempel på ett attribut för behandlings tid.":::

## <a name="treatment"></a>Hantering

### <a name="entities"></a>Entiteter

**TREATMENT_NAME** – terapeutiska procedurer. Till exempel Knee Replacement, benmärg Transplant, TAVI, diet.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Ett exempel på en entitet för behandlings namn.":::
