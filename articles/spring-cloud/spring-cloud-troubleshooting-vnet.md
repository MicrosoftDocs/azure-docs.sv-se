---
title: Felsöka Azure våren Cloud i det virtuella nätverket
description: Fel söknings guide för Azure våren Cloud Virtual Network.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698237"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Felsöka Azure våren Cloud i virtuella nätverk

I det här dokumentet får du hjälp med att lösa olika problem som kan uppstå när du använder Azure våren Cloud i virtuella nätverk.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Jag har stött på ett problem med att skapa en Azure våren Cloud Service-instans

Om du vill skapa en instans av Azure våren Cloud måste du ha tillräcklig behörighet för att distribuera instansen till det virtuella nätverket.  Vår moln tjänst instans måste [bevilja Azure våren Cloud Service-behörighet till det virtuella nätverket](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Om du använder Azure Portal för att konfigurera Azure våren Cloud Service-instansen, verifierar Azure Portal behörigheten.

Om du vill konfigurera Azure våren Cloud Service-instansen med hjälp av [Azure CLI](/cli/azure/get-started-with-azure-cli), kontrollerar du att:

- Prenumerationen är aktiv.
- Platsen stöds av Azure våren Cloud.
- Resurs gruppen för instansen har redan skapats.
- Resurs namnet följer namngivnings regeln. Det får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet måste vara en bokstav. Det sista tecknet måste vara en bokstav eller en siffra. Värdet måste innehålla mellan 2 och 32 tecken.

Information om hur du konfigurerar Azure våren Cloud Service-instansen med hjälp av Resource Manager-mallen finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

### <a name="common-creation-issues"></a>Vanliga problem med att skapa

| Felmeddelande | Så här löser du |
|------|------|
| Resurser som skapats av Azure våren Cloud tilläts inte av någon princip. | Nätverks resurser kommer att skapas när du distribuerar Azure våren-molnet i ditt eget virtuella nätverk. Kontrol lera om du har [Azure policy](../governance/policy/overview.md) definierat för att blockera de här genereringarna. Det gick inte att skapa de resurser som har skapats i fel meddelandet. |
| Tillhandahållna undernät har associerats med routningstabeller. ta bort kopplingen mellan dem. | För närvarande finns det inte stöd för att distribuera Azure våren Cloud i undernät som är kopplade till befintliga routningstabeller, koppla bort dem och försöka igen. |
| Nödvändig trafik är inte allowlisted. | Se [kund ansvar för att köra Azure våren Cloud i VNet](spring-cloud-vnet-customer-responsibilities.md) för att säkerställa att nödvändig trafik är allowlisted. |

## <a name="my-application-cant-be-registered"></a>Det går inte att registrera mitt program

Det här problemet uppstår om ditt virtuella nätverk har kon figurer ATS med anpassade DNS-inställningar. I det här fallet är den privata DNS-zon som används av Azure våren Cloud ineffektiv. Lägg till Azure DNS IP-168.63.129.16 som överordnad DNS-server på den anpassade DNS-servern.

## <a name="other-issues"></a>Andra problem

[Felsök vanliga problem med Azure våren Cloud](./spring-cloud-troubleshoot.md).