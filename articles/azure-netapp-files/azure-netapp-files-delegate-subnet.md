---
title: Delegera ett undernät till Azure NetApp Files | Microsoft Docs
description: Lär dig hur du delegerar ett undernät till Azure NetApp Files. Ange det delegerade under nätet när du skapar en volym.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bb3d1fd49c2623ff6dcbe8a19ae8c8ca3b46425a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006584"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegera ett undernät till Azure NetApp Files 

Du måste delegera ett undernät till Azure NetApp Files.   När du skapar en volym kan behöva du ange det delegerade undernätet.

## <a name="considerations"></a>Överväganden

* Guiden för att skapa ett nytt undernät har som standard en /24-nätverksmask som tillhandahåller 251 tillgängliga IP-adresser. Det räcker med en/28-nätverks mask, som ger 11 användbara IP-adresser, för tjänsten.
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).   
   Med Azure kan du skapa flera delegerade undernät i ett VNet.  Försök att skapa en ny volym kommer dock att Miss lyckas om du använder mer än ett delegerat undernät.  
   Du kan bara ha ett enda delegerat undernät i ett VNet. Ett NetApp-konto kan distribuera volymer i flera virtuella nätverk, vart och ett har sitt eget delegerade undernät.  
* Du kan inte ange en grupp eller en tjänstslutpunkt i delegerade undernät. Det leder till att delegeringen av undernätet misslyckas.
* Åtkomst till en volym från ett globalt peer-kopplat virtuellt nätverk stöds inte för närvarande.
* [Användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (UDR) och nätverks säkerhets grupper (NSG: er) stöds inte i delegerade undernät för Azure NetApp Files. Du kan dock använda UDR och NSG: er till andra undernät, även inom samma VNet som under nätet delegerat till Azure NetApp Files.  
   Azure NetApp Files skapar en system väg till det delegerade under nätet. Vägen visas i **effektiva vägar** i routningstabellen om du behöver den för fel sökning.

## <a name="steps"></a>Steg

1.  Gå till bladet **virtuella nätverk** i Azure Portal och välj det virtuella nätverk som du vill använda för Azure NetApp Files.    

1. Välj **Undernät** på bladet Virtuella nätverk och klicka på knappen **+Undernät**. 

1. Skapa ett nytt undernät att använda för Azure NetApp Files genom att fylla i följande obligatoriska fält på sidan Lägg till undernät:
    * **Namn**: Ange under nätets namn.
    * **Adress intervall**: Ange IP-adressintervall.
    * **Under näts delegering**: Välj **Microsoft. NetApp/Volumes**. 

      ![Delegering av undernät](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Du kan också skapa och delegera ett undernät när du [skapar en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Nästa steg

* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Läs om integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)