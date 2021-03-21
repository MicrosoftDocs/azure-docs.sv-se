---
title: Azure DDoS Protection planera behörigheter
description: Lär dig hur du hanterar behörighet i en skydds plan.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806264"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Hantera DDoS Protections planer: behörigheter och begränsningar

En DDoS skydds plan fungerar mellan regioner och prenumerationer. Samma plan kan länkas till virtuella nätverk från andra prenumerationer i olika regioner över din klient organisation. Prenumerationen är associerad med den månatliga återkommande fakturan för planen, samt överförbruknings avgifter, om antalet skyddade offentliga IP-adresser överstiger 100. Mer information om DDoS-priser finns i [pris information](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md).

## <a name="permissions"></a>Behörigheter

För att kunna arbeta med DDoS-skydds planer måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Action                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/Read        | Läs en DDoS skydds plan              |
| Microsoft. Network/ddosProtectionPlans/Write       | Skapa eller uppdatera en DDoS-skydds plan  |
| Microsoft. Network/ddosProtectionPlans/Delete      | Ta bort en DDoS skydds plan            |
| Microsoft. Network/ddosProtectionPlans/JOIN/åtgärd | Delta i en DDoS skydds plan              |

För att aktivera DDoS-skydd för ett virtuellt nätverk måste ditt konto också tilldelas lämpliga [åtgärder för virtuella nätverk](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Azure Policy

Det krävs inte att skapa mer än en plan för de flesta organisationer. Det går inte att flytta en plan mellan prenumerationer. Om du vill ändra prenumerationen som en plan finns i måste du ta bort den befintliga planen och skapa en ny.

För kunder som har olika prenumerationer och som vill se till att en enda plan distribueras över sin klient för kostnads kontroll kan du använda Azure Policy för att [begränsa skapandet av Azure DDoS Protection standard-planer](https://aka.ms/ddosrestrictplan). Den här principen blockerar skapandet av eventuella DDoS planer, om inte prenumerationen tidigare har marker ATS som ett undantag. Den här principen visar också en lista över alla prenumerationer som har en DDoS-plan distribuerad men bör inte markeras som inkompatibel.


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att visa och konfigurera telemetri för din DDoS-skydds plan fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)
