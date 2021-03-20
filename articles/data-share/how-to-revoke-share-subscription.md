---
title: Återkalla en resurs prenumeration i Azure Data Share
description: Lär dig hur du återkallar en resurs prenumeration från en mottagare med Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87511861"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Återkalla en konsuments resurs prenumeration i Azure Data Share

Den här artikeln förklarar hur du återkallar en resurs prenumeration från en eller flera av dina användare med hjälp av Azure Data Share. Detta förhindrar en konsument från att utlösa fler ögonblicks bilder. Om konsumenten ännu inte har utlöst en ögonblicks bild, kommer de aldrig att ta emot data när resurs prenumerationen har återkallats. Om de tidigare har utlöst en ögonblicks bild, kommer de senaste data som de har kvar i sitt konto.

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I Azure Data Share navigerar du till den skickade resursen och väljer fliken **dela prenumerationer** .

![Återkalla resurs prenumeration](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Markera kryss rutorna bredvid mottagarna vars resurs prenumerationer som du vill ta bort och klicka sedan på **återkalla**. Konsumenten får inte längre uppdateringar av sina data.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [övervakar dina data resurser](how-to-monitor.md).