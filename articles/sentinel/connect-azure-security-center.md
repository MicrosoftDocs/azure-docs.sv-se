---
title: Anslut Azure Defender-data till Azure Sentinel
description: Lär dig hur du ansluter Azure Defender-aviseringar från Azure Security Center och strömma dem till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633052"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Anslut Azure Defender-aviserings data från Azure Security Center

Använd Azure Defender Alert Connector för att mata in Azure Defender-aviseringar från [Azure Security Center](../security-center/security-center-introduction.md) och strömma dem till Azure Sentinel. 

## <a name="prerequisites"></a>Förutsättningar

- Användaren måste ha rollen som säkerhets läsare i prenumerationen på de loggar du strömmar.

- Du måste aktivera Azure Defender inom Azure Security Center. (Standard nivån finns inte längre och är inte längre ett licens krav.)

## <a name="connect-to-azure-defender"></a>Anslut till Azure Defender

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. I galleriet data anslutningar väljer du **Azure Defender-aviseringar från ASC** (kan fortfarande anropas Azure Security Center) och klickar på knappen **Öppna kopplings sidan** .

1. Under **konfiguration** klickar du på **Anslut** bredvid varje prenumeration vars aviseringar du vill strömma till Azure Sentinel. Knappen Anslut är bara tillgänglig om du har de behörigheter som krävs.

1. Du kan välja om du vill att aviseringarna från Azure Defender automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **aktive rad** för att aktivera standard analys regeln som automatiskt skapar incidenter från aviseringar. Du kan sedan redigera regeln under **analys** på fliken  **aktiva regler** .

1. Om du vill använda det relevanta schemat i Log Analytics för Azure Defender-aviseringar söker du efter **SecurityAlert**.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Defender till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
