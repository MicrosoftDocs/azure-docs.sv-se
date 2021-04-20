---
title: Använda instrumentpanelen för regelefterlevnad i Azure Security Center
description: Lär dig hur du lägger till och tar bort regler från instrumentpanelen för regelefterlevnad i Security Center
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
ms.openlocfilehash: dab6b504fe026324251b7284fc3abdb52bb9911a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738962"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Anpassa uppsättningen standarder på instrumentpanelen för regelefterlevnad

Azure Security Center kontinuerligt jämför konfigurationen av dina resurser med kraven i branschstandarder, föreskrifter och benchmark-standarder. Instrumentpanelen **för regelefterlevnad** ger insikter om din efterlevnadsstatus baserat på hur du uppfyller specifika efterlevnadskrav.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hur representeras standarder för regelefterlevnad i Security Center?

Branschstandarder, regelstandarder och benchmarks representeras i Security Center instrumentpanel för regelefterlevnad. Varje standard är ett initiativ som definieras i Azure Policy.

Om du vill se efterlevnadsdata mappade som utvärderingar på instrumentpanelen lägger du till en efterlevnadsstandard för hanteringsgruppen eller prenumerationen från **sidan Säkerhetsprincip.** Mer information om Azure Policy och initiativ finns i [Arbeta med säkerhetsprinciper.](tutorial-security-policy.md)

När du har tilldelat en standard eller ett benchmark-mått till det valda omfånget visas standarden på instrumentpanelen för regelefterlevnad med alla associerade efterlevnadsdata mappade som utvärderingar. Du kan också ladda ned sammanfattningsrapporter för alla standarder som har tilldelats.

Microsoft spårar själva regelstandarderna och förbättrar automatiskt täckningen för vissa paket över tid. När Microsoft släpper nytt innehåll för initiativet visas det automatiskt på instrumentpanelen som nya principer som mappats till kontroller i standarden.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Vilka standarder för regelefterlevnad finns i Security Center?

Som standard har varje prenumeration **tilldelats Azure Security Benchmark.** Det här är Microsofts särskilda Azure-riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

Du kan också lägga till standarder som:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK Official och UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standarder läggs till på instrumentpanelen när de blir tillgängliga.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Lägga till en regelstandard på instrumentpanelen

Följande steg beskriver hur du lägger till ett paket för att övervaka din efterlevnad med någon av de regler som stöds.

> [!NOTE]
> Om du vill lägga till standarder på instrumentpanelen måste prenumerationen ha Azure Defender aktiverat. Dessutom har endast användare som är ägare eller principdeltagare de behörigheter som krävs för att lägga till efterlevnadsstandarder. 

1. Från Security Center sidopanelen väljer du Regelefterlevnad **för att öppna** instrumentpanelen för regelefterlevnad. Här kan du se de efterlevnadsstandarder som för närvarande är tilldelade till de valda prenumerationerna.   

1. Längst upp på sidan väljer du Hantera **efterlevnadsprinciper.** Sidan Principhantering visas.

1. Välj den prenumeration eller hanteringsgrupp som du vill hantera regelefterlevnadsstatusen för. 

    > [!TIP]
    > Vi rekommenderar att du väljer det högsta omfång som standarden gäller för, så att efterlevnadsdata aggregeras och spåras för alla kapslade resurser. 

1. Om du vill lägga till de standarder som är relevanta för din organisation klickar du **på Lägg till fler standarder.** 

1. På sidan **Lägg till standarder för regelefterlevnad** kan du söka efter någon av de tillgängliga standarderna, inklusive:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **NHS och UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Lägga till regelstandarder Azure Security Center på instrumentpanelen för regelefterlevnad](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Välj **Lägg** till och ange all nödvändig information för det specifika initiativet, till exempel omfång, parametrar och reparation.

1. Från Security Center sidopanelen väljer du Regelefterlevnad igen **för** att gå tillbaka till instrumentpanelen för regelefterlevnad.

    Din nya standard visas i din lista över branschstandarder & regler. 

    > [!NOTE]
    > Det kan ta några timmar innan en ny standard visas på instrumentpanelen för efterlevnad.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrumentpanel för regelefterlevnad" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Ta bort en standard från instrumentpanelen

Om någon av de tillhandahållna regelstandarderna inte är relevant för din organisation är det en enkel process att ta bort dem från användargränssnittet. På så sätt kan du anpassa instrumentpanelen för regelefterlevnad ytterligare och endast fokusera på de standarder som gäller för dig.

Så här tar du bort en standard:

1. I Security Center på menyn väljer du **Säkerhetsprincip**.

1. Välj den relevanta prenumeration som du vill ta bort en standard från.

    > [!NOTE]
    > Du kan ta bort en standard från en prenumeration, men inte från en hanteringsgrupp. 

    Sidan säkerhetsprincip öppnas. För den valda prenumerationen visas standardprincipen, bransch- och regelstandarder samt eventuella anpassade initiativ som du har skapat.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Ta bort en regelstandard från instrumentpanelen för regelefterlevnad i Azure Security Center":::

1. För den standard som du vill ta bort väljer du **Inaktivera**. Ett bekräftelsefönster visas.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Bekräfta att du verkligen vill ta bort den regelstandard som du har valt":::

1. Välj **Ja**. Standarden tas bort. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du **lägger till efterlevnadsstandarder** för att övervaka din efterlevnad av regel- och branschstandarder.

Relaterat material finns på följande sidor:

- [Benchmark för Azure-säkerhet](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Instrumentpanel för regelefterlevnad i Security Center](security-center-compliance-dashboard.md) – Lär dig hur du spårar och exporterar data om regelefterlevnad med Security Center och externa verktyg
- [Arbeta med säkerhetspolicyer](tutorial-security-policy.md)