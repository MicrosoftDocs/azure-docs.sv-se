---
title: Namngivna entiteter för hälso vård
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 00c1c8ddab9214bf7698c21b05c24afa36ec20d9
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147381"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Textanalys för hälso kategorier, entiteter och attribut

[Textanalys för hälsa](../../how-tos/text-analytics-for-health.md) upptäcker medicinska begrepp i följande kategorier.  (Observera att endast engelsk text stöds i förhands granskningen av behållaren och att endast en enda modell version finns i varje behållar avbildning.)


| Kategori  | Beskrivning  |
|---------|---------|
| [UPPBYGGNAD](#anatomy) | begrepp som samlar in information om brödtext och anatomiska system, platser, platser eller regioner. |
 | [DEMOGRAFI](#demographics) | koncept som samlar in information om kön och ålder. |
 | [UNDERSÖKNING](#examinations) | begrepp som samlar in information om diagnostiska procedurer och tester. |
 | [GENOMICS](#genomics) | begrepp som samlar in information om gener och varianter. |
 | [HEALTHCARE](#healthcare) | begrepp som samlar in information om administrativa händelser, skötsel miljöer och hälso vårds yrken. |
 | [MEDICINSKA VILLKOR](#medical-condition) | begrepp som samlar in information om diagnoser, symtom eller tecken. |
 | [LÄKEMEDEL](#medication) | begrepp som samlar in information om medicin, inklusive namn på medicin, klasser, dosering och administrerings väg. |
 | [FAVORIT](#social) | begrepp som samlar in information om medicinskt relevanta sociala aspekter som familje relation. |
 | [HANTERING](#treatment) | begrepp som samlar in information om terapeutiska procedurer. |
  
Varje kategori kan innehålla två begrepps grupper:

* **Entiteter** – villkor som fångar in medicinska koncept som diagnos, namn på medicin eller behandlings namn.  *Bronchitis* är till exempel en diagnos och *aspirin* är ett medicin namn.  Omnämnanden i den här gruppen kan vara länkade till ett UMLS-koncept-ID.
* **Attribut** – fraser som ger mer information om den identifierade entiteten, till exempel *allvarlig* är en villkors kvalificerare för *bronchitis* eller *81 mg* är en dosering för *aspirin*.  Omnämnanden i den här kategorin kommer inte att länkas till ett UMLS-koncept-ID.

Dessutom identifierar tjänsten relationer mellan de olika begreppen, inklusive relationer mellan attribut och entiteter, till exempel *riktning* på *brödtext* eller *dosering* till *medicin namn* och relationer mellan entiteter, till exempel i förkortnings identifiering.

## <a name="anatomy"></a>Uppbyggnad

### <a name="entities"></a>Entiteter

**BODY_STRUCTURE** -Body-system, anatomiska platser eller regioner och kropps webbplatser. Till exempel arm, knee, buk, näsa, lever, huvud, andnings system, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Ett exempel på Body Structure-entiteten.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Ett expanderat exempel på entiteten Body Structure.":::

### <a name="attributes"></a>Attribut

**Riktnings** riktnings villkor, till exempel: Left, sido, versaler, framåt, som kännetecknar en brödtext.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Ett exempel på ett riktat attribut.":::

### <a name="supported-relations"></a>Relationer som stöds

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demografi

### <a name="entities"></a>Entiteter

**Ålder** – alla ålders villkor och-fraser, inklusive de som tillhör en patient, familje medlemmar och andra. Till exempel 40-årets gamla, 51 Yo, 3 månader gamla, vuxna, spädbarn, äldre, unga, små, mellanliggande.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Ett exempel på en ålders enhet.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Ett annat exempel på en ålders enhet.":::


**Kön** -villkor som avslöjar ärende kön. Till exempel hane, hona, Kvinna, gentleman, fru.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Ett exempel på en köns entitet.":::

### <a name="attributes"></a>Attribut

**RELATIONAL_OPERATOR** fraser som uttrycker förhållandet mellan en demografisk entitet och ytterligare information.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Ett exempel på en Relations operator.":::

## <a name="examinations"></a>Undersökningar

### <a name="entities"></a>Entiteter

**EXAMINATION_NAME** – diagnostiska procedurer och tester. Till exempel, MRI, ECG, HIV-test, hemoglobin, antal buffertar, skala system som *Bristol pall Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Ett exempel på en examen-enhet.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Ett annat exempel på en entitet för undersöknings namn.":::

### <a name="attributes"></a>Attribut

**Riktning** – riktade termer som kännetecknar en undersökning.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Ett exempel på ett Direction-attribut med en undersöknings namn enhet.":::

**MEASUREMENT_UNIT** – kontroll enheten. Till exempel i *hemoglobin > 9,5 g/dl* är termen *g/dl* enheten för *hemoglobin* -testet.

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Ett exempel på ett mått enhets attribut med en undersöknings namn enhet.":::

**MEASUREMENT_VALUE** – värdet för undersökningen. I till exempel *hemoglobin > 9,5 g/dl* är termen *9,5* värdet för *hemoglobin* -testet.

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Ett exempel på ett mått värde attribut med en undersöknings namn enhet.":::

**RELATIONAL_OPERATOR** – fraser som uttrycker förhållandet mellan en undersökning och ytterligare information. Till exempel det obligatoriska mått svärdet för en mål undersökning.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Ett exempel på en Relations operator med en test namns enhet.":::

**Tid** – temporala villkor som avser början och/eller längden (varaktighet) för en undersökning. Till exempel när testet har inträffat.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Ett exempel på ett Time-attribut med en test namns enhet.":::

### <a name="supported-relations"></a>Relationer som stöds

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entiteter

**Gen** – alla omnämnanden av gener. Till exempel MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Ett exempel på en gen organisation.":::

**Variant** – alla omnämnanden av gen variationer. Till exempel c. 524C>T, (MTRR): r.1462_1557del96
  
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

### <a name="attributes"></a>Attribut

**CONDITION_QUALIFIER** kvalitets villkor som används för att beskriva ett hälso tillstånd. Alla följande under Kategorier betraktas som kvalificerare:

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

**Riktnings** riktnings termer som kännetecknar ett hälso tillstånd för brödtext.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Ett exempel på ett Direction-attribut med en entitet för medicinskt tillstånd.":::

**Frekvens** – hur ofta ett hälso tillstånd inträffade, inträffar eller ska ske.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Ett exempel på ett frekvens-attribut med en entitet för medicinskt tillstånd.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Ett annat exempel på ett Direction-attribut med ett symtom-eller signerat entitet.":::

**MEASUREMENT_UNIT** – enheten som kännetecknar ett hälso tillstånd. I t. ex. *1,5 x2x1 cm tumor* är termen *cm* mått enhet för *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Ett exempel på ett mått enhets attribut med en entitet för medicinskt tillstånd.":::

**MEASUREMENT_VALUE** – det värde som kännetecknar ett hälso tillstånd. I *1,5 x2x1 cm tumor* är till exempel termen *1,5 x2x1* mått värde för *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Skärm bild som visar ett exempel på ett Direction-attribut med en symptom-eller signerad entitet.":::

**RELATIONAL_OPERATOR** fraser som uttrycker relationen mellan medicinska villkor ytterligare information. Till exempel tid eller mått värde. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Skärm bild som visar ett annat exempel på ett Direction-attribut med en symptom-eller signerad entitet.":::

 Tidstemporala villkor som avser start och/eller längd (varaktighet) för ett läkar villkor. Till exempel när ett problem påbörjades (start) eller när ett sjukdoms fel inträffar.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Skärm bild visar ytterligare ett exempel på ett Direction-attribut med ett symtom eller en signera entitet.":::

### <a name="supported-relations"></a>Relationer som stöds

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Läkemedel

### <a name="entities"></a>Entiteter

**MEDICATION_CLASS** – en uppsättning medications som har en liknande mekanism som åtgärd, ett relaterat åtgärds läge, en liknande kemisk struktur och/eller används för att behandla samma sjukdom. Till exempel ACE inhibitor, opioid, antibiotika, lacker.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Ett exempel på en entitet för en medicin klass.":::

**MEDICATION_NAME** – medicin omnämnande, inklusive namn på upphovsrättsskyddade varumärken och namn som inte är varumärken. Till exempel Advil, ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Ett exempel på en entitet för namn på medicin.":::

### <a name="attributes"></a>Attribut

**Dosering** -mängd som har beställts. Till exempel integrera natrium klo RID lösning *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Ett exempel på ett attribut för en medicin-dosering.":::

**Frekvens** – hur ofta en medicin ska vidtas.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Ett exempel på ett attribut för en medicin frekvens.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Ett annat exempel på ett attribut för en medicin frekvens.":::

**MEDICATION_FORM** -form av medicin. Till exempel lösning, Pill, kapsel, surfplatta, korrigering, gel, inklistring, skum, spray, droppar, grädde, grädde.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Ett exempel på ett medicin Formulär-attribut.":::

**MEDICATION_ROUTE** – administrations metoden för medicin. Till exempel, oralt, vaginal, IV, epidural, därför aktuell, inandning.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Ett exempel på ett medicin Route-attribut.":::

**RELATIONAL_OPERATOR** fraser som uttrycker förhållandet mellan medicin och ytterligare information. Till exempel det obligatoriska mått svärdet.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Skärm bild som visar ett exempel på ett Relations operator attribut med en medicin-enhet.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Skärm bild som visar ett annat exempel på ett Relations operator attribut med en medicin-enhet.":::

### <a name="supported-relations"></a>Relationer som stöds

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>Socialt

### <a name="entities"></a>Entiteter

**FAMILY_RELATION** – omnämnande av ämnets familje släktingar. Till exempel far, dotter, syskon, överordnade objekt.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Skärm bild som visar ett annat exempel på ett attribut för behandlings tid.":::

## <a name="treatment"></a>Hantering

### <a name="entities"></a>Entiteter

**TREATMENT_NAME** – terapeutiska procedurer. Till exempel Knee Replacement, benmärg Transplant, TAVI, diet.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Ett exempel på en entitet för behandlings namn.":::

### <a name="attributes"></a>Attribut

**Riktnings** riktnings termer som kännetecknar en behandling.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Skärm bild som visar ett exempel på ett attribut för behandlings riktning.":::

**Frekvens** – hur ofta en behandling sker eller ska ske.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Skärm bild som visar ett annat exempel på ett attribut för behandlings riktning.":::
 
**RELATIONAL_OPERATOR** fraser som uttrycker relationen mellan behandling och ytterligare information.  Till exempel hur lång tid som skickats från föregående procedur.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Ett exempel på ett attribut för en behandlings Relations operator.":::

 Tidstemporala villkor som gäller för behandlingens start och/eller längd (varaktighet). Till exempel datumet då behandlingen angavs.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Skärm bild som visar ett exempel på ett attribut för behandlings tid.":::

### <a name="supported-relations"></a>Relationer som stöds

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
