---
title: Central hantering av Azure-brandvägg
description: Läs mer om central hantering av Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084678"
---
# <a name="azure-firewall-central-management"></a>Central hantering av Azure-brandvägg

Om du hanterar flera brand väggar vet du att kontinuerlig ändring av brand Väggs regler gör det svårt att synkronisera dem. Centrala IT-team behöver ett sätt att definiera grundläggande brand Väggs principer och använda dem i flera affär senheter. Samtidigt vill DevOps-team skapa sina egna lokala, härledda brand Väggs principer för bättre flexibilitet.

Azure Firewall Manager kan hjälpa till att lösa dessa problem.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager är en tjänst för nätverks säkerhets hantering som tillhandahåller Central säkerhets policy och väg hantering för molnbaserade säkerhetsperimetern. Det gör det enkelt för IT-avdelningarna för företag att centralt definiera regler för nätverks-och program nivå för trafik filtrering över flera Azure Firewall-instanser. Du kan spänna över olika Azure-regioner och-prenumerationer i Hubbs-och eker-arkitekturer för trafik styrning och skydd. Det ger också DevOps bättre flexibilitet med härledda lokala brand Väggs säkerhets principer som implementeras i organisationer.

### <a name="firewall-policy"></a>Brand Väggs princip

En brand Väggs princip är en Azure-resurs som innehåller NAT-, nätverks-och program regel samlingar och hot informations inställningar. Det är en global resurs som kan användas i flera Azure Firewall-instanser i *säkra virtuella hubbar* och *virtuella hubb nätverk*. Nya principer kan skapas från grunden eller ärvas från befintliga principer. Arv gör att DevOps kan skapa lokala brand Väggs principer överst i organisationen som har tilldelats bas principen. Principer fungerar mellan regioner och prenumerationer.
 
Du kan skapa brand Väggs principer och associationer med Azure Firewall Manager. Du kan dock också skapa och hantera en princip med hjälp av REST API, mallar, Azure PowerShell och CLI. När du har skapat en princip kan du associera den med en brand vägg i ett virtuellt WAN-nav och göra den till en *säker virtuell hubb* och/eller en brand vägg i ett virtuellt nätverk som gör det möjligt för IT- *hubben Virtual Network*.

### <a name="pricing"></a>Prissättning

Principer faktureras baserat på brand Väggs associationer. En princip med noll eller en brand Väggs koppling är kostnads fri. En princip med flera brand Väggs associationer debiteras enligt ett fast pris. Mer information finns i [priser för Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Azure Firewall Management-partner

Följande ledande lösningar från tredje part stöder Azure Firewall Central Management med hjälp av vanliga Azure REST API: er. Var och en av dessa lösningar har sina egna unika egenskaper och egenskaper:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Nästa steg

Mer information om Azure Firewall Manager finns i [Vad är Azure Firewall Manager?](../firewall-manager/overview.md)