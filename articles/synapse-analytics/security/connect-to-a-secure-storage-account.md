---
title: Anslut till ett säkert lagrings konto från din Azure Synapse-arbetsyta
description: I den här artikeln får du lära dig hur du ansluter till ett säkert lagrings konto från din Azure Synapse-arbetsyta
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674325"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Anslut till ett säkert Azure Storage-konto från din Synapse-arbetsyta

I den här artikeln får du lära dig hur du ansluter till ett säkert Azure Storage-konto från din Azure Synapse-arbetsyta. Du kan länka ett Azure Storage-konto till din Synapse-arbetsyta när du skapar din arbets yta. Du kan länka fler lagrings konton när du har skapat din arbets yta.


## <a name="secured-azure-storage-accounts"></a>Skyddade Azure Storage-konton
Azure Storage tillhandahåller en skiktad säkerhets modell som gör att du kan skydda och kontrol lera åtkomsten till dina lagrings konton. Du kan konfigurera IP-brandväggens regler för att bevilja trafik från valda offentliga IP-adressintervall åtkomst till ditt lagrings konto. Du kan också konfigurera nätverks regler för att bevilja trafik från valda virtuella nätverk åtkomst till ditt lagrings konto. Du kan kombinera IP brand Väggs regler som tillåter åtkomst från valda IP-adressintervall och nätverks regler som beviljar åtkomst från valda virtuella nätverk på samma lagrings konto. Dessa regler gäller för den offentliga slut punkten för ett lagrings konto. Du behöver inga åtkomst regler för att tillåta trafik från hanterade privata slut punkter som skapats i din arbets yta till ett lagrings konto. Lagrings brand Väggs regler kan användas för befintliga lagrings konton eller nya lagrings konton när du skapar dem. Du kan lära dig mer om hur du skyddar ditt lagrings konto [här](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse-arbetsytor och virtuella nätverk
När du skapar en Synapse-arbetsyta kan du välja att aktivera ett hanterat virtuellt nätverk som ska associeras med det. Om du inte aktiverar hanterat virtuellt nätverk för arbets ytan när du skapar den, finns din arbets yta i ett delat virtuellt nätverk, tillsammans med andra Synapse-arbetsytor som inte har ett hanterat virtuellt nätverk som är associerat med det. Om du har aktiverat hanterat virtuellt nätverk när du skapade arbets ytan, är din arbets yta kopplad till ett dedikerat virtuellt nätverk som hanteras av Azure Synapse. De här virtuella nätverken skapas inte i din kund prenumeration. Därför kan du inte bevilja trafik från dessa virtuella nätverk åtkomst till ditt skyddade lagrings konto med hjälp av nätverks regler som beskrivs ovan.  

## <a name="access-a-secured-storage-account"></a>Åtkomst till ett skyddat lagrings konto
Synapse fungerar från nätverk som inte kan ingå i dina nätverks regler. Följande måste göras för att ge åtkomst från din arbets yta till ditt säkra lagrings konto.

* Skapa en Azure dataSynapses-arbetsyta med ett hanterat virtuellt nätverk som är associerat med den och skapa hanterade privata slut punkter från den till det säkra lagrings kontot
* Ge din Azure Synapse-arbetsyta åtkomst till ditt säkra lagrings konto som en betrodd Azure-tjänst. Som betrodd tjänst använder Azure-Synapse stark autentisering för att ansluta till ditt lagrings konto på ett säkert sätt.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Skapa en Synapse-arbetsyta med ett hanterat virtuellt nätverk och skapa hanterade privata slut punkter till ditt lagrings konto
Du kan följa [de här stegen](./synapse-workspace-managed-vnet.md) för att skapa en Synapse-arbetsyta som har ett hanterat virtuellt nätverk som är associerat med det. När arbets ytan med ett associerat virtuellt nätverk har skapats kan du skapa en hanterad privat slut punkt för ditt säkra lagrings konto genom att följa stegen som anges [här](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Ge din Azure Synapse-arbetsyta åtkomst till ditt säkra lagrings konto som en betrodd Azure-tjänst
Analys funktioner som dedikerad SQL-pool och SQL-pool utan server använder en infrastruktur för flera innehavare som inte har distribuerats till det hanterade virtuella nätverket. För att trafik från dessa funktioner ska kunna komma åt det skyddade lagrings kontot måste du konfigurera åtkomst till ditt lagrings konto baserat på arbets ytans systemtilldelade identitet genom att följa stegen nedan.

I Azure Portal navigerar du till ditt skyddade lagrings konto. Välj **nätverk** i det vänstra navigerings fönstret. I avsnittet **resurs instanser** väljer du *Microsoft. Synapse/arbets ytor* som **resurs typ** och anger namnet på arbets ytan för **instans namnet**. Välj **Spara**.

![Nätverks konfiguration för lagrings konto.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Du bör nu kunna komma åt ditt skyddade lagrings konto från arbets ytan.


## <a name="next-steps"></a>Nästa steg

Läs mer om det [virtuella nätverket för hanterade arbets ytor](./synapse-workspace-managed-vnet.md).

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md).