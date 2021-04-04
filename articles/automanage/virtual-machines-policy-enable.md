---
title: Aktivera autohantering för virtuella datorer via Azure Policy
description: Lär dig hur du aktiverar Azure automanage för virtuella datorer via en inbyggd Azure Policy i Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714898"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Aktivera autohantering för virtuella datorer via Azure Policy

Om du vill aktivera autohantering för massor av virtuella datorer kan du göra det med hjälp av en inbyggd [Azure policy](..\governance\azure-management.md). Den här artikeln hjälper dig att hitta rätt princip och hur du tilldelar den för att aktivera autohantering i Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

> [!IMPORTANT]
> Följande Azure RBAC-behörighet krävs för att aktivera autohantering: **ägar** rollen eller **deltagare** tillsammans med **Administratörs roller för användar åtkomst** .


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Leta upp och tilldela principen

1. Gå till **princip** i Azure Portal
1. Gå till **definitions** fönstret
1. Klicka på list rutan **Kategorier** för att se tillgängliga alternativ
1. Välj alternativet **Aktivera autohantera – metod tips för virtuella Azure-datorer**
1. Nu kommer listan att uppdateras för att visa en inbyggd princip med ett namn som börjar med *Aktivera automanage...*
1. Klicka på det inbyggda princip namnet för *bästa praxis för virtuella Azure-datorer*
1. När du har klickat på principen kan du nu se fliken **definition**

    > [!NOTE]
    > Azure Policy definition används för att ange parametrar för automatisk hantering, t. ex. konfigurations profilen eller kontot. Den anger också filter som säkerställer att principen endast gäller för rätt virtuella datorer.

1. Klicka på knappen **tilldela** för att skapa en tilldelning
1. På fliken **grundläggande** fyller du i **omfattning** genom att ställa in *prenumerationen* och *resurs gruppen*

    > [!NOTE]
    > Med hjälp av omfånget kan du definiera vilka virtuella datorer som den här principen gäller för. Du kan ange program på prenumerations nivå eller resurs grupps nivå. Om du anger en resurs grupp, kommer alla virtuella datorer som för närvarande finns i den resurs gruppen eller eventuella framtida virtuella datorer som vi lägger till att hantera automatiskt aktive rad. 

1. Klicka på fliken **parametrar** och ange kontot för automatisk **hantering** och önskad **konfigurations profil** 
1. Granska inställningarna på fliken **Granska + skapa** .
1. Tillämpa tilldelningen genom att klicka på **skapa**
1. Visa dina tilldelningar på fliken **tilldelningar** bredvid **definition**

> [!NOTE]
> Det tar lite tid för principen att börja gälla de virtuella datorer som finns i resurs gruppen eller prenumerationen.


## <a name="next-steps"></a>Nästa steg 

Lär dig ett annat sätt att aktivera Azure automanage för virtuella datorer via Azure Portal. 

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)