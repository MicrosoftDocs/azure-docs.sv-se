---
title: Aviserings beteende i SMS i åtgärds grupper
description: SMS-meddelande format och svara på SMS-meddelanden för att avbryta prenumerationen, prenumerera på eller be om hjälp.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: 3ca776a1869874042a6a97cdd59dc00d3a917d33
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621958"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Aviserings beteende i SMS i åtgärds grupper

## <a name="overview"></a>Översikt 
Med åtgärds grupper kan du konfigurera en lista med åtgärder. Dessa grupper används när du definierar aviseringar. Se till att en viss åtgärds grupp meddelas när aviseringen utlöses. En av de åtgärder som stöds är SMS; SMS-meddelanden har stöd för dubbelriktad kommunikation. En användare kan svara på ett SMS för att:

- **Avbryt prenumeration från aviseringar:** En användare kan avbryta prenumerationen på alla SMS-aviseringar för alla åtgärds grupper eller en enda åtgärds grupp.
- **Prenumerera på aviseringar igen:** En användare kan prenumerera på alla SMS-aviseringar för alla åtgärds grupper eller en enda åtgärds grupp.  
- **Hjälp om begäran:** En användare kan be om mer information om SMS. De omdirigeras till den här artikeln.

Den här artikeln beskriver beteendet hos SMS-aviseringar och svars åtgärder som användaren kan utföra baserat på användarens språk:

## <a name="receiving-an-sms-alert"></a>Ta emot en SMS-avisering
En SMS-mottagare som kon figurer ATS som en del av en åtgärds grupp får ett SMS när en avisering utlöses. SMS innehåller följande information:
* ShortName för den åtgärds grupp som aviseringen skickades till
* Rubriken på aviseringen

| REPLY | Description |
| ----- | ----------- |
| Tillåt `<Action Group Short name>` | Inaktiverar ytterligare SMS från åtgärds gruppen |
| Använd `<Action Group Short name>` | Aktiverar SMS från åtgärds gruppen igen |
| Stanna | Inaktiverar ytterligare SMS från alla åtgärds grupper |
| HAR | Aktiverar SMS från alla åtgärds grupper igen |
| HJÄLP | Ett svar skickas till användaren med en länk till den här artikeln. |

>[!NOTE]
>Om en användare har avbrutit prenumerationen på SMS-aviseringar, men sedan läggs till i en ny åtgärds grupp. de får SMS-aviseringar för den nya åtgärds gruppen, men de är fortfarande avbrutna från alla tidigare åtgärds grupper.

## <a name="next-steps"></a>Nästa steg
Få en [Översikt över aktivitets logg aviseringar](../platform/alerts-overview.md) och lär dig hur du får aviseringar  
Läs mer om [begränsning av SMS-hastighet](alerts-rate-limiting.md)  
Läs mer om [Åtgärds grupper](../platform/action-groups.md)

