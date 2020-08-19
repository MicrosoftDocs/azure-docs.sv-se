---
title: Utvärdera effekten av en ny Azure Policy-definition
description: Förstå den process som ska följas när du introducerar en ny princip definition i din Azure-miljö.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 243f04cc20e1fb7167306b925a0e494b34cf1267
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544710"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Utvärdera effekten av en ny Azure Policy-definition

Azure Policy är ett kraftfullt verktyg för att hantera dina Azure-resurser till företags standarder och uppfylla behoven för efterlevnad. När personer, processer eller pipeliner skapar eller uppdaterar resurser, granskar Azure Policy begäran. När princip definitions effekterna är [APPEND](./effects.md#deny) eller [DeployIfNotExists](./effects.md#deployifnotexists), ändrar principen begäran eller lägger till den. När princip definitions effekterna är [gransknings](./effects.md#audit) -eller [AuditIfNotExists](./effects.md#auditifnotexists)så gör principen att en aktivitets logg post skapas. När princip definitions resultatet är [nekat](./effects.md#deny), stoppar principen skapandet eller ändringen av begäran.

Dessa resultat är exakt så som du vill när du vet att principen är korrekt definierad. Det är dock viktigt att verifiera att en ny princip fungerar som avsett innan den gör det möjligt att ändra eller blockera arbete. Verifieringen måste se till att endast de avsedda resurserna bedöms vara icke-kompatibla och inga kompatibla resurser ingår felaktigt (kallas _falskt positiv_) i resultaten.

Den rekommenderade metoden för att verifiera en ny princip definition är att följa dessa steg:

- Definiera noggrant principen
- Granska dina befintliga resurser
- Granska nya eller uppdaterade resurs begär Anden
- Distribuera principen till resurser
- Kontinuerlig övervakning

## <a name="tightly-define-your-policy"></a>Definiera noggrant principen

Det är viktigt att förstå hur affärs principen implementeras som en princip definition och förhållandet mellan Azure-resurser med andra Azure-tjänster. Det här steget utförs genom [att identifiera kraven](../tutorials/create-custom-policy-definition.md#identify-requirements) och [fastställa resurs egenskaperna](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Men det är också viktigt att se bortom den smala definitionen av din affärs policy. Är ditt princip tillstånd till exempel "alla Virtual Machines måste..."? Vad gäller för andra Azure-tjänster som använder virtuella datorer, till exempel HDInsight eller AKS? När du definierar en princip måste vi fundera över hur den här principen påverkar resurser som används av andra tjänster.

Därför bör princip definitionerna vara så tätt definierade och fokuserade på resurserna och de egenskaper som du behöver för att utvärdera kompatibiliteten som möjligt.

## <a name="audit-existing-resources"></a>Granska befintliga resurser

Innan du tittar på att hantera nya eller uppdaterade resurser med den nya princip definitionen är det bäst att se hur det utvärderar en begränsad del av befintliga resurser, till exempel en resurs grupp för test. Använd [tvångs läget](./assignment-structure.md#enforcement-mode) _inaktiverat_ (DoNotEnforce) i princip tilldelningen för att förhindra att [påverkan](./effects.md) utlöses eller att aktivitets logg poster skapas.

Det här steget ger dig möjlighet att utvärdera resultatet av kompatibiliteten för den nya principen på befintliga resurser utan att påverka arbets flödet. Kontrol lera att inga kompatibla resurser är markerade som icke-kompatibla (_falskt positiva_) och att alla resurser som du förväntar dig inte är kompatibla markeras korrekt.
När den första del mängden av resurserna verifieras som förväntat expanderar du utvärderingen till alla befintliga resurser.

Utvärdering av befintliga resurser på det här sättet ger också möjlighet att åtgärda icke-kompatibla resurser innan den nya principen implementeras. Den här rensningen kan utföras manuellt eller via en [reparations uppgift](../how-to/remediate-resources.md) om princip definitions effekterna är _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Granska nya eller uppdaterade resurser

När du har verifierat att den nya princip definitionen rapporteras korrekt på befintliga resurser, är det dags att titta på effekten av principen när resurser skapas eller uppdateras. Om princip definitionen har stöd för Effect Parameterisering, använder du [audit](./effects.md#audit). Med den här konfigurationen kan du övervaka skapande och uppdatering av resurser för att se om den nya princip definitionen utlöser en post i Azure aktivitets loggen för en resurs som är icke-kompatibel utan att det påverkar befintliga arbete eller begär Anden.

Vi rekommenderar att både uppdatera och skapa nya resurser som matchar din princip definition för att se att _gransknings_ effekterna är korrekt utlösta när de förväntas. Vara på Lookout för resurs begär Anden som inte ska påverkas av den nya princip definitionen som utlöser _gransknings_ effekten.
Dessa resurser som påverkas är ett annat exempel på _falska positiva identifieringar_ och måste åtgärdas i princip definitionen före fullständig implementering.

I händelse av att princip definitionen ändras i den här test fasen rekommenderar vi att du startar validerings processen med granskningen av befintliga resurser. En ändring i princip definitionen för ett _falskt positivt resultat_ på nya eller uppdaterade resurser påverkar förmodligen också befintliga resurser.

## <a name="deploy-your-policy-to-resources"></a>Distribuera principen till resurser

När du har slutfört valideringen av den nya princip definitionen med både befintliga resurser och nya eller uppdaterade resurs begär Anden börjar du implementera principen. Vi rekommenderar att du skapar princip tilldelningen för den nya princip definitionen till en delmängd av alla resurser, till exempel en resurs grupp. När du har verifierat den första distributionen utökar du omfånget för principen till bredare och bredare nivåer, till exempel prenumerationer och hanterings grupper. Den här utökningen uppnås genom att du tar bort tilldelningen och skapar en ny i mål omfånget tills den har tilldelats till det fullständiga omfånget av resurser som är avsedda att omfattas av den nya princip definitionen.

Om det finns resurser som ska undantas från din nya princip definition under distributionen kan du adressera dem på något av följande sätt:

- Uppdatera princip definitionen så att den är mer explicit för att minska oavsiktlig påverkan
- Ändra omfånget för princip tilldelningen (genom att ta bort och skapa en ny tilldelning)
- Lägg till resurs gruppen i undantags listan för princip tilldelningen

Ändringar i omfånget (nivå eller undantag) bör kontrol leras fullständigt och kommuniceras med organisationer för säkerhet och efterlevnad för att säkerställa att det inte finns några luckor i täckningen.

## <a name="monitor-your-policy-and-compliance"></a>Övervaka din princip och efterlevnad

Att implementera och tilldela en princip definition är inte det sista steget. Övervaka kontinuerligt [efterlevnaden](../how-to/get-compliance-data.md) av resurser till din nya princip definition och lämpliga [Azure Monitor aviseringar och aviseringar](../../../azure-monitor/platform/alerts-overview.md) för när icke-kompatibla enheter identifieras. Vi rekommenderar också att du utvärderar princip definitionen och de relaterade tilldelningarna enligt schemat för att verifiera att princip definitionen uppfyller behoven för affärs principer och efterlevnad. Principer bör tas bort om de inte längre behövs. Principer behöver också uppdateras från tid till att de underliggande Azure-resurserna utvecklas och lägger till nya egenskaper och funktioner.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [princip definitions strukturen](./definition-structure.md).
- Lär dig mer om [princip tilldelnings strukturen](./assignment-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).