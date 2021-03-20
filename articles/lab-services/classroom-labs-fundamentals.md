---
title: Grundläggande arkitektur i Azure Lab Services | Microsoft Docs
description: Den här artikeln beskriver de grundläggande resurser som används av labb tjänster och grundläggande arkitektur i ett labb.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: 71d59e8bcf7b4078255d6b119e9d62f366e46033
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437328"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Grundläggande arkitektur i Azure Lab Services

Azure Lab Services är en SaaS-lösning (program vara som en tjänst), vilket innebär att de resurser som krävs av labb tjänsterna hanteras åt dig. Den här artikeln beskriver de grundläggande resurser som används av labb tjänster och grundläggande arkitektur i ett labb.  

Azure Lab Services tillhandahåller ett par områden som gör att du kan använda dina egna resurser tillsammans med labb tjänster.  Mer information om hur du använder virtuella datorer i ditt eget nätverk finns i så här peer-koppla [ett virtuellt nätverk](how-to-connect-peer-virtual-network.md).  Information om hur du återanvänder bilder från ett delat avbildnings Galleri finns i så här [ansluter du ett delat avbildnings Galleri](how-to-attach-detach-shared-image-gallery.md).

Nedan visas den grundläggande arkitekturen i ett klass rums labb.  Labb kontot finns i din prenumeration. De virtuella student datorerna, tillsammans med de resurser som krävs för att stödja de virtuella datorerna, finns i en prenumeration som ägs av Lab-tjänsterna. Nu ska vi prata om vad som finns i labb tjänstens prenumerationer i detalj.

![grundläggande arkitektur för labb](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Värdbaserade resurser

De resurser som krävs för att köra ett klass rums labb finns i en av de Microsoft-hanterade Azure-prenumerationerna.  Resurser innehåller en mall för virtuella datorer för lärare, virtuell dator för varje elev och nätverksrelaterade objekt, till exempel en belastningsutjämnare, ett virtuellt nätverk och en nätverks säkerhets grupp.  Dessa prenumerationer övervakas för misstänkt aktivitet.  Det är viktigt att Observera att denna övervakning görs externt för de virtuella datorerna via VM-tillägg eller nätverks mönster övervakning.  Om [Stäng av från koppling](how-to-enable-shutdown-disconnect.md) är aktiverat är ett diagnostiskt tillägg aktiverat på den virtuella datorn. Tillägget gör det möjligt för labb tjänster att informeras om händelsen från RDP-session (Remote Desktop Protocol).

## <a name="virtual-network"></a>Virtual Network

Varje labb isoleras av ett eget virtuellt nätverk.  Om labbet har ett [peer-kopplat virtuellt nätverk](how-to-connect-peer-virtual-network.md), isoleras varje labb av sitt eget undernät.  Eleverna ansluter till sin virtuella dator via en belastningsutjämnare.  Inga virtuella student datorer har en offentlig IP-adress; de har bara en privat IP-adress.  Anslutnings strängen för studenten blir belastnings utjämningens offentliga IP-adress och en slumpmässig port mellan 49152 och 65535.  Inkommande regler för belastningsutjämnaren vidarebefordrar anslutningen, beroende på operativ systemet, till antingen port 22 (SSH) eller port 3389 (RDP) för den aktuella virtuella datorn. En NSG förhindrar yttre trafik på andra portar.

## <a name="access-control-to-the-virtual-machines"></a>Åtkomst kontroll till de virtuella datorerna

Labb tjänster hanterar studentens möjlighet att utföra åtgärder som att starta och stoppa på sina virtuella datorer.  Den styr också åtkomsten till den virtuella datorns anslutnings information.

Labb tjänster hanterar också registreringen av studenter till tjänsten. Det finns för närvarande två olika åtkomst inställningar: begränsade och icke-begränsade. Mer information finns i artikeln [Hantera labb användare](how-to-configure-student-usage.md#send-invitations-to-users) . Begränsad åtkomst innebär att Lab-tjänster verifierar att eleverna läggs till som användare innan de tillåts åtkomst. Ej begränsad innebär att alla användare kan registreras så länge de har registrerings länken och det finns kapacitet i labbet. Icke-begränsad kan vara användbart för Hackathon-händelser.

Elevens virtuella datorer som finns i klass rummets labb labb har ett användar namn och lösen ord som har skapats av skaparen av labbet.  Dessutom kan skaparen av labbet tillåta registrerade studenter att välja sina egna lösen ord vid första inloggningen.  

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga funktioner i labb tjänster finns i [Azure Lab Services begrepp](classroom-labs-concepts.md) och [Azure Lab Services översikt](classroom-labs-overview.md).
