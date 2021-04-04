---
title: Hantera DNS-zoner i Azure DNS-Azure Portal | Microsoft Docs
description: Du kan hantera DNS-zoner med hjälp av Azure Portal. Den här artikeln beskriver hur du uppdaterar, tar bort och skapar DNS-zoner på Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965807"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Hantera DNS-zoner i Azure Portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure-CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar DNS-zoner med hjälp av Azure Portal. Du kan också hantera dina DNS-zoner med plattforms oberoende [Azure CLI](dns-operations-dnszones-cli.md) eller Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. På menyn hubb navigerar du för att **skapa en resurs > nätverk > DNS-zon** för att öppna bladet **Skapa DNS-zon** .

    ![DNS-zon](./media/dns-operations-dnszones-portal/openzone650.png)

4. På bladet **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:


   | **Inställning** | **Värde** | **Information** |
   |---|---|---|
   |**Namn**|contoso.com|Namnet på DNS-zonen|
   |**Prenumeration**|[Din prenumeration]|Välj en prenumeration att skapa DNS-zonen i.|
   |**Resursgrupp**|**Skapa ny:** contosoDNSRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resurs grupper finns i översikts artikeln om [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) .|
   |**Plats**|USA, västra||

> [!NOTE]
> Resursgruppen refererar till platsen för resursgruppen och har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte.

## <a name="list-dns-zones"></a>Lista DNS-zoner

I Azure Portal navigerar du till **fler tjänster**  >  **nätverk**  >  **DNS-zoner**. Varje DNS-zon är en egen resurs, och information som antal post uppsättningar och namnservrar visas i den här vyn. Kolumn **namns servrarna** är inte i standardvyn. Om du vill lägga till det klickar du på **kolumner**, väljer **namnservrar** och klickar sedan på **Slutför**.

![lista DNS-zoner](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Navigera till en DNS-zon i portalen. På bladet **DNS-zon** klickar du på **ta bort zon**. Du uppmanas sedan att bekräfta att du vill ta bort DNS-zonen. Om du tar bort en DNS-zon raderas även alla poster som finns i zonen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du arbetar med din DNS-zon och poster genom att gå till [komma igång med Azure DNS med hjälp av Azure Portal](dns-getstarted-portal.md).