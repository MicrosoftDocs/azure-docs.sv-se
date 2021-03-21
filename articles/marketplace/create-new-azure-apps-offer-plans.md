---
title: Skapa planer för ditt Azure Application-erbjudande
description: Lär dig hur du skapar planer för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370464"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Skapa planer för ditt Azure Application-erbjudande

Erbjudanden som säljs via Microsofts handels marknads plats måste ha minst en plan för att lista ditt erbjudande i den kommersiella marknads platsen. Du kan skapa flera olika planer med olika alternativ i samma erbjudande. Dessa planer (kallas ibland SKU: er) kan variera beroende på typ av plan (_lösnings mal len_ eller _hanterat program_), monetarisering eller mål grupp. Allmän vägledning om planer finns i [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md).

## <a name="create-a-plan"></a>Skapa en plan

1. Längst upp på fliken **plan översikt** väljer du **+ Skapa ny plan**.
1. I dialog rutan som visas i rutan **plan-ID** anger du ett unikt plan-ID. Detta ID visas för kunder i produkt-URL: en. Använd upp till 50 gemena alfanumeriska tecken, bindestreck eller under streck. Du kan inte ändra plan-ID när du har valt **skapa**.
1. Ange ett unikt namn för den här planen i rutan **planerat namn** . Kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Använd högst 50 tecken.
1. Välj **Skapa**.

## <a name="define-the-plan-setup"></a>Definiera plan konfigurationen

På fliken **plan konfiguration** kan du ange typ av plan, om den återanvänder den tekniska konfigurationen från ett annat abonnemang och vilka Azure-regioner som planen ska vara tillgänglig i. Dina svar på den här fliken kommer att påverka vilka fält som visas på andra flikar för den här planen.

### <a name="select-the-plan-type"></a>Välj typ av plan

- I listan **typ av plan** väljer du antingen **lösnings mal len** eller det **hanterade programmet**.

En plan för **lösnings mal len** hanteras helt av kunden. En **hanterad program** plan gör det möjligt för utgivare att hantera programmet för kundens räkning. Mer information om dessa två typer av prenumerationer finns i [typer av planer](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Använd teknisk konfiguration igen (valfritt)

Om du har skapat fler än en plan av samma typ i det här erbjudandet och den tekniska konfigurationen är identisk mellan dem kan du återanvända den tekniska konfigurationen från ett annat abonnemang. Den här inställningen kan inte ändras när den här planen har publicerats.

#### <a name="to-re-use-technical-configuration"></a>Så här använder du teknisk konfiguration igen

1. Välj den **här planen återanvänder den tekniska konfigurationen från en annan plan av samma typ** .
1. I listan som visas väljer du den bas plan som du vill använda.

> [!NOTE]
> När du återanvänder paket från ett annat abonnemang försvinner fliken **teknisk konfiguration** från den här planen. Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, används även för den här planen.

### <a name="select-azure-regions-clouds"></a>Välj Azure-regioner (moln)

Din plan måste göras tillgänglig i minst en Azure-region. När planen har publicerats och är tillgänglig i en speciell Azure-region kan du inte ta bort den regionen från ditt erbjudande.

#### <a name="azure-global-region"></a>Global Azure-region

Kryss rutan **Azure Global** är markerad som standard. Detta gör ditt abonnemang tillgängligt för kunder i alla globala Azure-regioner med marknads plats integrering. För hanterade program planer kan du välja med marknader som du vill att din plan ska vara tillgänglig för.

Ta bort ditt erbjudande från den här regionen genom att avmarkera kryss rutan **Azure Global** .

#### <a name="azure-government-region"></a>Azure Government Region

Den här regionen ger till gång till kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad entiteter, samt partner som är berättigade att betjäna dem. Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

##### <a name="to-select-the-azure-government-region"></a>Så här väljer du Azure Government Region

1. Markera kryss rutan **Azure Government** .
1. Under **Azure Government certifieringar** väljer du **+ Lägg till certifiering (max 100)**.
1. I rutorna som visas anger du ett namn och en länk till en certifiering.
1. Upprepa steg 2 och 3 om du vill lägga till ytterligare en certifiering.

Välj **Spara utkast** innan du fortsätter till nästa flik: planera lista.

## <a name="define-the-plan-listing"></a>Definiera plan listan

På fliken **plan lista** kan du konfigurera en lista med information om planen. På den här fliken visas detaljerad information som visar skillnaden mellan planer i samma erbjudande. Du kan definiera plan namn, Sammanfattning och beskrivning så som du vill att de ska visas på den kommersiella Marketplace.

1. I rutan **plan namn** visas namnet som du angav tidigare för den här planen här. Du kan ändra det när du vill. Det här namnet kommer att visas på den kommersiella marknads platsen som rubrik för ditt erbjudandes program varu plan och är begränsat till 100 tecken.
1. I rutan **plan Sammanfattning** anger du en kort sammanfattning av planen (inte erbjudandet). Den här sammanfattningen är begränsad till 100 tecken.
1. I rutan **plan Beskrivning** förklarar du vad som gör den här program varu planen unik och eventuella skillnader jämfört med andra planer i erbjudandet. Beskriv inte erbjudandet, bara planen. Beskrivningen får innehålla upp till 2 000 tecken.
1. Välj **Spara utkast** innan du fortsätter.

## <a name="next-steps"></a>Nästa steg

Gör något av följande:

- Om du konfigurerar en plan för lösnings mal len går du till [Konfigurera en plan för lösnings mal len](create-new-azure-apps-offer-solution.md).
- Om du konfigurerar en hanterad program plan går du till [Konfigurera en hanterad program plan](create-new-azure-apps-offer-managed.md).
