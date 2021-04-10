---
title: Publicerings guide efter erbjudande typ – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs de erbjudande typer som är tillgängliga på Microsofts kommersiella marknads platser.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 3be967b611f6b75705a60bfa14523832b0652931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879232"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

I den här artikeln beskrivs de erbjudande typer som är tillgängliga i den kommersiella marknads platsen. *Erbjudande typen* definierar erbjudande strukturen, som innehåller metadata, artefakter och annat innehåll som presenteras i den kommersiella marknads platsen.

När du har [bestämt dig för ett publicerings alternativ](determine-your-listing-type.md)måste du välja en erbjudande typ innan du börjar skapa ditt erbjudande i Partner Center. Erbjudande typen motsvarar den typ av lösning, app eller tjänst erbjudande som du vill publicera, samt dess justering för Microsofts produkter och tjänster.

Du kan konfigurera en enda erbjudande typ på olika sätt för att aktivera olika publicerings alternativ, list alternativ, etablering eller prissättning. Publicerings alternativet och konfigurationen av erbjudande typen överensstämmer också med kravet på erbjudanden och tekniska krav.

Se till att läsa igenom butiken för onlinebutik och erbjuda typ av krav och de tekniska publicerings kraven innan du skapar erbjudandet.

## <a name="list-of-offer-types"></a>Lista över erbjudande typer

I följande tabell visas de kommersiella Marketplace-erbjudande typerna i Partner Center.

| **Erbjudande typ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Azure Application**](plan-azure-application-offer.md) | Det finns två typer av Azure-programplaner: _lösnings mal len_ och _hanterade program_. Båda plan typerna stöder automatisering av distribution och konfiguration av en lösning utöver en enskild virtuell dator (VM). Du kan automatisera processen med att tillhandahålla flera resurser, inklusive virtuella datorer, nätverk och lagrings resurser för att tillhandahålla komplexa lösningar, till exempel IaaS-lösningar. Båda typerna av abonnemang kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.<ul><li>**Lösnings mal len** planer är ett av de största sätten att publicera en lösning på den kommersiella marknads platsen. Lösningar för lösnings mal len är inte i ett särskilt lägen i den kommersiella marknads platsen, men de kan användas för att distribuera betalda VM-erbjudanden som debiteras via den kommersiella marknads platsen. Använd lösnings mal len plan typ när kunden ska hantera lösningen och transaktionerna debiteras genom en annan plan.</li><br><li>Med **hanterade program** planer kan du enkelt skapa och leverera fullständigt hanterade, färdiga program för dina kunder. De har samma funktioner som lösnings mal len planer, med några viktiga skillnader:</li><ul><li> Resurserna distribueras till en resurs grupp och hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen.</li><li>Som utgivare kan du ange kostnaden för kontinuerlig support för lösningen och transaktioner som stöds via den kommersiella Marketplace.</li></ul>Använd den hanterade program Plans typen när du eller din kund kräver att lösningen hanteras av en partner eller att du distribuerar en prenumerations-baserad lösning.</ul> |
| [**Azure-behållare**](marketplace-containers.md) | Använd typen av Azure Container-erbjudande när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service. |
| [**Virtuell Azure-dator**](marketplace-virtual-machines.md) | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. |
| [**Konsult tjänst**](./plan-consulting-service-offer.md) | Konsult tjänster hjälper till att ansluta kunder till tjänster som stöder och utökar användningen av Azure, Dynamics 365 eller Power Suite-tjänster.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Dynamics 365 Business Central, Dynamics 365 kund engagemang, Power Apps och finans-och verksamhets appar.|
| [**IoT Edge modul**](iot-edge-module.md) | Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge, och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| [**Hanterad tjänst**](./plan-managed-service-offer.md) | Du kan skapa hanterade tjänster och hantera kund delegerade prenumerationer eller resurs grupper via [Azure Lighthouse](../lighthouse/overview.md).|
| [**Power BI app** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Power BI och Microsoft 365.|
| [**Program vara som en tjänst**](plan-saas-offer.md) | Använd SaaS-typen (Software as a Service) för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. Information om krav för enkel inloggning för SaaS-erbjudanden finns i [Azure AD och transactable SaaS-erbjudanden på den kommersiella marknads platsen](azure-ad-saas.md). |


## <a name="next-steps"></a>Nästa steg

- Granska kraven för berättigande i motsvarande artikel för din erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren för varje onlinebutik för att få exempel på hur din lösning mappas till en erbjudande typ och konfiguration.