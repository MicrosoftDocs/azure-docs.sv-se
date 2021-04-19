---
title: Ansluta Microsoft Defender for Identity (tidigare Azure ATP)-data till Azure Sentinel| Microsoft Docs
description: Lär dig hur du strömmar loggar från Microsoft Defender for Identity (tidigare Azure Advanced Threat Protection) (ATP) till Azure Sentinel med ett enda klick.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714121"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Ansluta data från Microsoft Defender for Identity (tidigare Azure Advanced Threat Protection)

> [!IMPORTANT]
> Anslutningsappen Microsoft Defender for Identity data i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskriver hur du strömmar säkerhetsaviseringar [från Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) till Azure Sentinel. 

För att vidarebefordra hälsoaviseringar utöver säkerhetsaviseringar integrerar du Microsoft Defender for Identity med en Syslog-server. Mer information finns i [Microsoft Defender for Identity dokumentationen](/defender-for-identity/setting-syslog). 

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörigheter som global administratör eller säkerhetsadministratör
- Du måste vara en förhandsversion av Microsoft Defender for Identity och aktivera integrering mellan Microsoft Defender for Identity och Microsoft Cloud App Security. Mer information finns i [Microsoft Defender for Identity Integration](/cloud-app-security/mdi-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Ansluta till Microsoft Defender for Identity

Kontrollera att Microsoft Defender for Identity förhandsversionen [är aktiverad i nätverket](/azure-advanced-threat-protection/install-atp-step1).
Om Microsoft Defender for Identity distribueras och dina data matas in kan misstänkta aviseringar enkelt strömmas till Azure Sentinel. Det kan ta upp till 24 timmar innan aviseringarna börjar strömma till Azure Sentinel.


1. Om du Microsoft Defender for Identity till Azure Sentinel måste du först aktivera integrering mellan Microsoft Defender for Identity och Microsoft Cloud App Security. Information om hur du gör detta finns i [Microsoft Defender for Identity integration](/cloud-app-security/mdi-integration).

1. I Azure Sentinel väljer du **Dataanslutningsappar** och klickar sedan **på Microsoft Defender for Identity (förhandsversion)** panelen.

1. Du kan välja om du vill att aviseringarna från Microsoft Defender for Identity automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **Skapa incidenter** väljer **du** Aktivera för att aktivera standardregeln för analys som automatiskt skapar incidenter från aviseringar som genereras i den anslutna säkerhetstjänsten. Du kan sedan redigera den här regeln under **Analytics** och sedan **Aktiva regler.**

1. Klicka på **Anslut**.

1. Om du vill använda relevant schema i Log Analytics för Microsoft Defender for Identity-aviseringar söker du efter **SecurityAlert**.

> [!NOTE]
> Om aviseringarna är större än 30 KB Azure Sentinel att sluta visa fältet Entiteter i aviseringarna.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender for Identity till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur [du får insyn i dina data och potentiella hot.](quickstart-get-visibility.md)
- Kom igång [med att identifiera hot Azure Sentinel](tutorial-detect-threats-built-in.md).
