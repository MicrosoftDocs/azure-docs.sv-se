---
title: Ytterligare nätverks krav för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Ytterligare nätverks krav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83a695792e78916bf79f4d4cf1394ae197c25fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671877"
---
# <a name="additional-network-requirements-for-large-instances"></a>Ytterligare nätverks krav för stora instanser

Du kan ha ytterligare nätverks krav som en del av en distribution av stora instanser av SAP HANA på Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Lägg till fler IP-adresser eller undernät

Använd antingen Azure Portal, PowerShell eller Azure CLI när du lägger till fler IP-adresser eller undernät.

Lägg till det nya IP-adressintervallet som ett nytt intervall i det virtuella nätverkets adress utrymme, i stället för att skapa ett nytt sammanställt intervall. Skicka den här ändringen till Microsoft. På så sätt kan du ansluta från det nya IP-adressintervallet till de stora instanser i HANA-enheten i klienten. Du kan öppna en support förfrågan för Azure för att få det nya adress utrymmet för virtuella nätverk tillagt. När du har fått en bekräftelse utför du nästa steg.

Om du vill skapa ytterligare ett undernät från Azure Portal, se [skapa ett virtuellt nätverk med hjälp av Azure Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network). Information om hur du skapar en från PowerShell finns i [skapa ett virtuellt nätverk med hjälp av PowerShell](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Lägg till virtuella nätverk

När du har anslutit ett eller flera virtuella Azure-nätverk kanske du vill ansluta ytterligare de som har åtkomst SAP HANA på Azure (stora instanser). Börja med att skicka in en support förfrågan för Azure. I denna förfrågan inkluderar du specifik information som identifierar den specifika Azure-distributionen. Ta även med intervallet för IP-adressutrymmet eller intervall för det virtuella Azure-nätverkets adress utrymme. SAP HANA på Microsoft Service Management får du den information du behöver för att ansluta de ytterligare virtuella nätverken och Azure-ExpressRoute. För varje virtuellt nätverk behöver du en unik auktoriseringsregel för att ansluta till ExpressRoute-kretsen till HANA-stora instanser.

## <a name="increase-expressroute-circuit-bandwidth"></a>Öka ExpressRoute-kretsens bandbredd

Kontakta SAP HANA på Microsoft Service Management. Om du får ett råd om att öka bandbredden för SAP HANA på Azure (stora instanser) ExpressRoute-kretsen skapar du en support förfrågan för Azure. (Du kan begära en ökning för en enda krets bandbredd upp till högst 10 Gbit/s.) Du får sedan ett meddelande när åtgärden har slutförts. du behöver inte göra något annat för att aktivera den här högre hastigheten i Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Lägg till en ytterligare ExpressRoute-krets

Kontakta SAP HANA på Microsoft Service Management. Om du uppmanas att lägga till ytterligare en ExpressRoute-krets skapar du en support förfrågan för Azure (inklusive en begäran att hämta auktoriseringsinformation för att ansluta till den nya kretsen). Innan du gör en begäran måste du definiera det adress utrymme som används i de virtuella nätverken. SAP HANA på Microsoft Service Management kan sedan ge auktorisering.

När den nya kretsen skapas och SAP HANA på Microsoft Service Management-konfigurationen är klar får du ett meddelande med den information du behöver för att fortsätta. Du kan inte ansluta virtuella Azure-nätverk till den här extra kretsen om de redan är anslutna till en annan SAP HANA på Azure (stor instans) ExpressRoute-kretsen i samma Azure-region.

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Om du vill ta bort ett undernät för virtuellt nätverk kan du använda Azure Portal, PowerShell eller Azure CLI. Om ditt virtuella Azure-nätverks IP-adressintervall eller adress utrymme var ett sammanställt intervall finns det ingen uppföljning för dig med Microsoft. (Observera dock att det virtuella nätverket fortfarande sprider det adress utrymme för BGP-routning som innehåller det borttagna under nätet.) Du kan ha definierat det virtuella adress intervallet eller adress utrymmet för Azure-nätverket som flera IP-adressintervall, varav de har tilldelats till ditt borttagna undernät. Var noga med att ta bort det från det virtuella nätverkets adress utrymme. Informera sedan SAP HANA om Microsoft Service Management för att ta bort den från de intervall som SAP HANA på Azure (stora instanser) tillåts kommunicera med.

Mer information finns i [ta bort ett undernät](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Mer information finns i [ta bort ett virtuellt nätverk](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

SAP HANA på Microsoft Service Management tar bort befintliga auktoriseringar på SAP HANA på Azure (stora instanser) ExpressRoute-kretsen. Det tar också bort det virtuella Azure-nätverkets IP-adressintervall eller adress utrymme för kommunikationen med HANA-stora instanser.

När du har tagit bort det virtuella nätverket öppnar du en support förfrågan för Azure för att tillhandahålla intervallet för IP-adressutrymmet eller intervall som ska tas bort.

För att se till att du tar bort allt tar du bort ExpressRoute-anslutningen, den virtuella Nätverksgatewayen, den virtuella nätverks-gatewayens offentliga IP-adress och det virtuella nätverket.

## <a name="delete-an-expressroute-circuit"></a>Ta bort en ExpressRoute-krets

Om du vill ta bort ytterligare SAP HANA på Azure (stora instanser) ExpressRoute-kretsen öppnar du en support förfrågan för Azure med SAP HANA på Microsoft Service Management. Begär att kretsen ska tas bort. I Azure-prenumerationen kan du ta bort eller behålla det virtuella nätverket, om det behövs. Du måste dock ta bort anslutningen mellan de stora instanserna i ExpressRoute-kretsen och den länkade virtuella Nätverksgatewayen.

## <a name="next-steps"></a>Nästa steg

- [Installera och konfigurera SAP HANA (stora instanser) i Azure](hana-installation.md)
