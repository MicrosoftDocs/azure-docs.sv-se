---
title: Ansluta Windows Virtual Desktop till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Windows Virtual Desktop data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380301"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Ansluta Windows Virtual Desktop data till Azure Sentinel

Den här artikeln beskriver hur du kan övervaka dina Windows Virtual Desktop (WVD)-miljöer med hjälp av Azure Sentinel.

Genom att övervaka Windows Virtual Desktop miljöer kan du till exempel tillhandahålla mer distansarbete med hjälp av virtualiserade skrivbord, samtidigt som din organisations säkerhetsposition upprätthålls.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Windows Virtual Desktop data i Azure Sentinel

Windows Virtual Desktop data i Azure Sentinel innehåller följande typer:


|Data  |Beskrivning  |
|---------|---------|
|**Windows-händelseloggar**     |  Windows-händelseloggar från WVD-miljön strömmas till en Azure Sentinel-aktiverad Log Analytics-arbetsyta på samma sätt som Windows-händelseloggar från andra Windows-datorer, utanför WVD-miljön. <br><br>Installera Log Analytics-agenten på din Windows-dator och konfigurera Windows-händelseloggarna så att de skickas till Log Analytics-arbetsytan.<br><br>Mer information finns i:<br>- [Installera Log Analytics-agenten på Windows-datorer](/azure/azure-monitor/agents/agent-windows)<br>- [Samla in datakällor för Windows-händelseloggar med Log Analytics-agenten](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Ansluta Windows-säkerhetshändelser](connect-windows-security-events.md)       |
|**Microsoft Defender för slutpunktsaviseringar (MDE)**     |  Om du vill konfigurera MDE för Windows Virtual Desktop använder du samma procedur som för andra Windows-slutpunkter. <br><br>Mer information finns i: <br>- [Konfigurera distribution av Microsoft Defender för slutpunkter](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Ansluta data från Microsoft 365 Defender till Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop diagnostik**     | Windows Virtual Desktop diagnostik är en funktion i paaS Windows Virtual Desktop tjänsten, som loggar information när någon som Windows Virtual Desktop rollen använder tjänsten. <br><br>Varje logg innehåller information om Windows Virtual Desktop roll var inblandad i aktiviteten, eventuella felmeddelanden som visas under sessionen, klientorganisationsinformation och användarinformation. <br><br>Diagnostikfunktionen skapar aktivitetsloggar för både användaråtgärder och administrativa åtgärder. <br><br>Mer information finns i [Använda Log Analytics för diagnostikfunktionen i Windows Virtual Desktop](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Ansluta Windows Virtual Desktop data

Börja mata in Windows Virtual Desktop data i Azure Sentinel genom att följa anvisningarna i Windows Virtual Desktop dokumentationen.

Mer information finns i Skicka [Windows Virtual Desktop data till Log Analytics-arbetsytan.](/azure/virtual-desktop/diagnostics-log-analytics)

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats kör du frågor i Azure Sentinel mot dina Log Analytics-data.

Se till exempelfrågor från [Windows Virtual Desktop dokumentationen](/azure/virtual-desktop/diagnostics-log-analytics).


Azure Sentinel innehåller även inbyggda frågor i området **Allmänna**  >  **loggar**  >  **för WINDOWS VIRTUAL DESKTOP:**

[![Windows Virtual Desktop inbyggda frågor i Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Nästa steg


Mer information finns i Azure Monitor [för Windows Virtual Desktop ordlistan](/azure/virtual-desktop/azure-monitor-glossary).
