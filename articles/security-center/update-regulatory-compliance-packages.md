---
title: Använda instrument panelen för kontroll av efterlevnad i Azure Security Center
description: Lär dig hur du lägger till och tar bort reglerande standarder från instrument panelen för kontroll av efterlevnad i Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381740"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Anpassa uppsättningen standarder på instrument panelen för regelefterlevnad

Azure Security Center jämför kontinuerligt konfigurationen av dina resurser med krav i bransch standarder, regler och benchmarks. **Instrument panelen** för övervakning av efterlevnad ger insikter om position utifrån hur du uppfyller särskilda krav för efterlevnad.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hur visas regler för regelefterlevnad i Security Center?

Bransch standarder, regelverks standarder och benchmarks-standarder representeras i Security Centers instrument panel för regler för efterlevnad. Varje standard är ett initiativ som definieras i Azure Policy.

Om du vill visa de kompatibilitets data som har mappats som utvärderingar på din instrument panel, lägger du till en efterlevnadsprincip i hanterings gruppen eller prenumerationen från sidan **säkerhets princip** . Mer information om Azure Policy och initiativ finns i [arbeta med säkerhets principer](tutorial-security-policy.md).

När du har tilldelat en standard eller benchmark till din valda omfattning visas standarden på instrument panelen för kontroll av efterlevnad med alla associerade efterlevnadsprinciper som har mappats som utvärderingar. Du kan också hämta sammanfattnings rapporter för någon av de standarder som har tilldelats.

Microsoft spårar själva regelverket och förbättrar automatiskt täckningen i några av paketen över tid. När Microsoft släpper ut nytt innehåll för initiativet visas det automatiskt i din instrument panel som nya principer som har mappats till kontroller i standarden.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Vilka regler för regelefterlevnad är tillgängliga i Security Center?

Som standard har alla prenumerationer tilldelats **Azures säkerhets mått** . Det här är de Microsoft-baserade, Azure-/regionsspecifika rikt linjerna för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).

Du kan också lägga till standarder som:

- NIST SP 800-53 R4
- SWIFT CSP-CSCF – v2020
- Storbritannien, officiella och UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standarder läggs till på instrument panelen när de blir tillgängliga.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Lägg till en regel standard på din instrument panel

Följande steg beskriver hur du lägger till ett paket för att övervaka efterlevnaden av en av de regler som stöds.

> [!NOTE]
> Om du vill lägga till standarder på din instrument panel måste Azure Defender vara aktiverat på prenumerationen. Det är också bara användare som är ägare eller policy deltagare som har de behörigheter som krävs för att lägga till efterlevnads standarder. 

1. Från Security Centerens marginal List väljer du regelefterlevnad för att öppna instrument **panelen för kontroll** av efterlevnad. Här kan du se de efterlevnads standarder som för närvarande är tilldelade till de valda prenumerationerna.   

1. Överst på sidan väljer du **Hantera efterlevnadsprinciper**. Sidan princip hantering visas.

1. Välj den prenumeration eller hanterings grupp som du vill hantera position för regelefterlevnad för. 

    > [!TIP]
    > Vi rekommenderar att du väljer det högsta omfånget som standarden gäller för, så att efterlevnadsprinciper sammanställs och spåras för alla kapslade resurser. 

1. Om du vill lägga till de standarder som är relevanta för din organisation klickar du på **Lägg till fler standarder**. 

1. På sidan **Lägg till regler för regelefterlevnad** kan du söka efter alla tillgängliga standarder, inklusive:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO och Storbritannien NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Lägga till reglerande standarder i Azure Security Center kontroll panelen för reglerad efterlevnad](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Välj **Lägg till** och ange all nödvändig information för det aktuella initiativet, till exempel omfång, parametrar och reparation.

1. Från Security Centerens marginal List väljer du **regler för efterlevnad** igen för att gå tillbaka till instrument panelen för kontroll av efterlevnad.

    Din nya standard visas i din lista över bransch & reglerande standarder. 

    > [!NOTE]
    > Det kan ta några timmar innan en nyligen tillagd standard visas på instrument panelen för efterlevnad.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrument panel för regler för efterlevnad" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Ta bort en standard från din instrument panel

Om någon av de angivna regel standarderna inte är relevanta för din organisation, är det enkelt att ta bort dem från användar gränssnittet. På så sätt kan du ytterligare anpassa instrument panelen för kontroll av efterlevnad och bara fokusera på de standarder som gäller för dig.

Så här tar du bort en standard:

1. Från Security Center menyn väljer du **säkerhets princip**.

1. Välj den relevanta prenumeration som du vill ta bort en standard från.

    > [!NOTE]
    > Du kan ta bort en standard från en prenumeration, men inte från en hanterings grupp. 

    Sidan säkerhets princip öppnas. För den valda prenumerationen visas standard principen, branscherna och regel standarderna och eventuella anpassade initiativ som du har skapat.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Ta bort en reglerande standard från instrument panelen för regler för efterlevnad i Azure Security Center":::

1. Välj **inaktivera** för den standard som du vill ta bort. Ett bekräftelse fönster visas.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Bekräfta att du verkligen vill ta bort den gällande lagen som du har valt":::

1. Välj **Ja**. Standard kommer att tas bort. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du **lägger till efterlevnads standarder** för att övervaka efterlevnaden av regler och bransch standarder.

För relaterat material, se följande sidor:

- [Benchmark för Azure-säkerhet](../security/benchmarks/introduction.md)
- [Instrument panel för gällande säkerhets Center](security-center-compliance-dashboard.md) – lär dig hur du spårar och exporterar dina regler för regelefterlevnad med Security Center och externa verktyg
- [Arbeta med säkerhetspolicyer](tutorial-security-policy.md)