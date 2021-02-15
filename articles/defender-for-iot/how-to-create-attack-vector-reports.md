---
title: Skapa angrepps vektor rapporter
description: Angrepps vektor rapporter ger en grafisk representation av en sårbarhets kedja av enheter som går att utnyttja.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e9960fc2120add845be8feda9bafdef95a9b5f94
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522333"
---
# <a name="attack-vector-reporting"></a>Rapportering om angrepps vektor

## <a name="about-attack-vector-reports"></a>Om angrepps vektor rapporter

Angrepps vektor rapporter ger en grafisk representation av en sårbarhets kedja av enheter som går att utnyttja. Dessa sårbarheter kan ge en angripare åtkomst till viktiga nätverks enheter. Angrepps vektor simulatorn beräknar angrepps vektorer i real tid och analyserar alla angrepps vektorer för ett särskilt mål.

Genom att arbeta med angrepps vektorn kan du utvärdera effekterna av minsknings aktiviteter i angrepps ordningen. Du kan till exempel bestämma om en system uppgradering ska störa angriparens sökväg genom att bryta angrepps kedjan eller om en annan angrepps Sök väg är kvar. Den här informationen hjälper dig att prioritera reparationer och mildrande aktiviteter.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Visa dina aviseringar i kontroll centret.":::

> [!NOTE]
> Administratörer och säkerhets analyser kan utföra de procedurer som beskrivs i det här avsnittet.

## <a name="create-an-attack-vector-report"></a>Skapa en angrepps vektor rapport

Så här skapar du en simulering av attack vektor:

1. Lägg till en simulering genom att välja :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="plus tecken":::på sido menyn.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simuleringen av attack vektorn.":::

2. Ange simulerings egenskaper:

   - **Namn**: namn på simulering.

   - **Högsta antal vektorer**: det högsta antalet vektorer i en enda simulering.

   - **Visa i enhets karta**: Visa angrepps vektorn som ett filter på enhets kartan.

   - **Alla käll enheter**: angrepps vektorn kommer att betrakta alla enheter som en attack källa.

   - **Attack källa**: angrepps vektorn kommer endast att betrakta de angivna enheterna som en attack källa.

   - **Alla mål enheter**: angrepps vektorn kommer att betrakta alla enheter som ett angrepps mål.

   - **Angrepps mål**: angrepps vektorn kommer endast att betrakta de angivna enheterna som ett angrepps mål.

   - **Exkludera enheter**: angivna enheter kommer att uteslutas från simuleringen av attack vektorn.

   - **Uteslut undernät**: angivna undernät kommer att uteslutas från simuleringen av attack vektorn.

3. Välj **Lägg till simulering**. Simuleringen läggs till i listan simuleringar.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Lägg till en ny simulering.":::

4. Välj :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: om du vill redigera simuleringen.

   Välj :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: om du vill ta bort simuleringen.

   Välj :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: om du vill markera simuleringen som en favorit.

5. En lista över angrepps vektorer visas och innehåller vektor Poäng (från 100), angrepps källans enhet och angrepps mål enhet. Välj en speciell attack för grafisk depresentation av angrepps vektorer.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Angrepps vektorer.":::

## <a name="next-steps"></a>Nästa steg

[Rapportering om angrepps vektor](how-to-create-attack-vector-reports.md)


