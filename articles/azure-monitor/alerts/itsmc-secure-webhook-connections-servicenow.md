---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor-konfiguration med ServiceNow
description: Den här artikeln visar hur du ansluter dina ITSM-produkter/-tjänster med ServiceNow på säker export i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 8ca77c1fa1232f7aed88b0d6942d8a0085caf0d5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625745"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Anslut ServiceNow till Azure Monitor

Följande avsnitt innehåller information om hur du ansluter din ServiceNow-produkt och säker export i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att du uppfyller följande krav:

* Azure AD har registrerats.
* Du har den version av ServiceNow Event Management – ITOM (version New York eller senare) som stöds.
* [Programmet](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.2.0?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3Devent%2520management%2520connectors&sl=sh) är installerat på ServiceNow-instansen.

## <a name="configure-the-servicenow-connection"></a>Konfigurera ServiceNow-anslutningen

1. Använd länken https://(instans namn). service – nu. com/API/sn_em_connector/EM/inbound_event? source = azuremonitor URI för säker export definition.

2. Följ instruktionerna enligt versionen:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
