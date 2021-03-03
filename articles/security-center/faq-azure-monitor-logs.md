---
title: Azure Security Center vanliga frågor och svar om befintliga Log Analyticss agenter
description: Vanliga frågor och svar är svar på kunder som redan använder Log Analytics agenten och som överväger Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701968"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Vanliga frågor och svar om kunder som redan använder Azure Monitor loggar<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Åsidosätter Security Center befintliga anslutningar mellan virtuella datorer och arbets ytor?

Om en virtuell dator redan har Log Analytics-agenten installerad som ett Azure-tillägg åsidosätter Security Center inte den befintliga arbets ytans anslutning. I stället använder Security Center den befintliga arbets ytan. Den virtuella datorn kommer att skyddas förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den arbets yta som den rapporterar till. 

En Security Center lösning installeras på den arbets yta som valts på skärmen data insamling om den inte redan finns, och lösningen tillämpas bara på de relevanta virtuella datorerna. När du lägger till en lösning distribueras den automatiskt som standard till alla Windows-och Linux-agenter som är anslutna till din Log Analytics-arbetsyta. Med [lösnings mål](../azure-monitor/insights/solution-targeting.md) kan du tillämpa ett omfång på dina lösningar.

> [!TIP]
> Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg) installerar Security Center inte Log Analytics agenten och säkerhets övervakningen är begränsad.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installerar Security Center lösningar på mina befintliga Log Analytics arbets ytor? Vilka är fakturerings konsekvenserna?
När Security Center identifierar att en virtuell dator redan är ansluten till en arbets yta som du har skapat, kan Security Center aktivera lösningar på den här arbets ytan enligt pris konfigurationen. Lösningarna tillämpas bara på relevanta virtuella Azure-datorer, via [lösnings mål](../azure-monitor/insights/solution-targeting.md), så att faktureringen förblir densamma.

- **Azure Defender av** – Security Center installerar lösningen "SecurityCenterFree" på arbets ytan. Du debiteras inte.
- **Azure Defender på** – Security Center installerar säkerhets lösningen på arbets ytan.

   ![Lösningar på standard arbets ytan](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Jag har redan arbets ytor i min miljö, kan jag använda dem för att samla in säkerhets data?
Om en virtuell dator redan har Log Analytics-agenten installerad som ett Azure-tillägg, Security Center använda den befintliga anslutna arbets ytan. En Security Center lösning installeras på arbets ytan om den inte redan finns, och lösningen tillämpas bara på relevanta virtuella datorer via [lösnings mål](../azure-monitor/insights/solution-targeting.md).

När Security Center installerar Log Analytics agent på virtuella datorer används standard arbets ytorna som skapats av Security Center om Security Center inte pekar på en befintlig arbets yta.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Jag har redan säkerhets lösning på mina arbets ytor. Vilka är fakturerings konsekvenserna?
Gransknings lösningen för säkerhets & används för att aktivera **Azure Defender för servrar**. Om säkerhets & gransknings lösningen redan är installerad på en arbets yta använder Security Center den befintliga lösningen. Faktureringen har inte ändrats.