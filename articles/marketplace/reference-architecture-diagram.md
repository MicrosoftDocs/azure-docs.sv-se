---
title: Referens arkitektur diagram | Azure Marketplace
description: Så här skapar du ett referens arkitektur diagram för ett erbjudande i Microsofts kommersiella marknads platser.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604499"
---
# <a name="reference-architecture-diagram"></a>Referens arkitektur diagram

Referens arkitektur diagrammet är en modell som representerar den infrastruktur som ditt erbjudande förlitar sig på. För Azure IP-lösningar bör diagrammet också visa hur ditt erbjudande använder Microsofts moln tjänster enligt de tekniska kraven för IP Co-försäljning. Den är inte avsedd att utvärdera arkitekturens kvalitet. Den är utformad för att visa hur din lösning använder Microsoft-tjänster.

Referens arkitektur diagrammet kan skapas via flera verktyg. Vi rekommenderar Microsoft Visio eftersom det finns flera stenciler som illustrerar Azure Architecture-modeller.

En bra utgångs punkt för att skapa referens arkitektur diagram är att utnyttja [Azures arkitektur modeller](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Typiska komponenter i ett referens arkitektur diagram

- Moln tjänster som är värdar för och interagerar med ditt erbjudande, inklusive de som använder Azure-resurser
- Data anslutningar, data lager och data tjänster som används av ditt erbjudande
- Moln tjänster som används för att kontrol lera säkerhet, autentisering och användare av erbjudandet
- Användar gränssnitt och andra tjänster som visar erbjudandet för användare
- Hybrid anslutningar och lokala anslutningar och integreringar eller en kombination av båda

Den här skärm bilden visar ett exempel på ett referens arkitektur diagram.

[![Den här bilden är ett exempel på ett arkitektur diagram i en gemensam försäljning.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Detta exempel på en referens till arkitektur diagrammet är till för en vertikal bransch chattrobot som kan integreras med intranät platser för att hjälpa till med prognoser för efter frågan via en Machine Learning-algoritm. I den här lösningen används data från olika ERP-system. Roboten är utformad för att åtgärda frågor om när en säljare kan genomföra möjliga leverans datum för en beställning.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera samförsäljning för ett erbjudande för handels platser](commercial-marketplace-co-sell.md)
