---
title: Aktivera automanage för virtuella datorer via Azure Policy
description: Lär dig hur du Azure Automanage för virtuella datorer via en inbyggd Azure Policy i Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365203"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Aktivera automanage för virtuella datorer via Azure Policy

Om du vill aktivera automanage för många virtuella datorer kan du göra det med hjälp av en inbyggd [Azure Policy](..\governance\azure-management.md). Den här artikeln beskriver hur du hittar rätt princip och hur du tilldelar den för att aktivera automatisk Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> Konton för kostnadsfri utvärdering har inte åtkomst till de virtuella datorer som används i den här självstudien. Uppgradera till en prenumeration där du betalar per prenumeration.

> [!IMPORTANT]
> Följande Azure RBAC-behörighet krävs för att aktivera automatisk hantering: **ägarroll eller** **deltagare samt** **administratörsroller för användaråtkomst.**

## <a name="direct-link-to-policy"></a>Direktlänk till princip
Definitionen för automanage-principen finns i Azure Portal namnet [på Configure virtual machines to be onboarded to Azure Automanage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3). Om du klickar på den här länken går du direkt till steg 8 [i Hitta och tilldela principen](#locate-and-assign-the-policy) nedan.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Leta upp och tilldela principen

1. Gå **till Princip** i Azure Portal
1. Gå till **fönstret** Definitioner
1. Klicka på **listrutan** Kategorier för att se tillgängliga alternativ
1. Välj alternativet **Enable Automanage – Azure virtual machine best practices** (Aktivera automatisk hantering – bästa praxis för virtuella Azure-datorer)
1. Nu uppdateras listan för att visa en inbyggd princip med ett namn som börjar med *Aktivera automanage...*
1. Klicka på det *inbyggda principnamnet Enable Automanage - Azure virtual machine best practices* in (Aktivera automatisk hantering – virtuell Azure-dator)
1. När du klickar på principen kan du nu se **fliken** Definition

    > [!NOTE]
    > Den Azure Policy definitionen används för att ange automanage-parametrar som konfigurationsprofilen eller kontot. Den anger också filter som ser till att principen endast gäller för rätt virtuella datorer.

1. Klicka på **knappen Tilldela** för att skapa en tilldelning
1. På fliken **Grundläggande fyller** du i **Omfång genom** att ange Prenumeration *och* *Resursgrupp*

    > [!NOTE]
    > Med omfånget kan du definiera vilka virtuella datorer som principen gäller för. Du kan ange program på prenumerationsnivå eller resursgruppsnivå. Om du anger en resursgrupp kommer alla virtuella datorer som för närvarande finns i den resursgruppen eller eventuella framtida virtuella datorer som vi lägger till i den att automatiskt aktiveras.

1. Klicka på **fliken Parametrar** och ange **automanage-kontot och** önskad **konfigurationsprofil**
1. Granska **inställningarna på fliken** Granska + skapa
1. Tillämpa tilldelningen genom att klicka på **Skapa**
1. Visa dina tilldelningar på **fliken Tilldelningar** bredvid **Definition**

> [!NOTE]
> Det tar lite tid innan principen börjar gälla på de virtuella datorer som för närvarande finns i resursgruppen eller prenumerationen.


## <a name="next-steps"></a>Nästa steg

Lär dig ett annat sätt att Azure Automanage för virtuella datorer via Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)