---
title: Ta bort Azure Sentinel | Microsoft Docs
description: Ta bort din Azure Sentinel-instans.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90885833"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Ta bort Azure Sentinel från din arbetsyta

Om du inte längre vill använda Azure Sentinel förklarar den här artikeln hur du tar bort den från din arbets yta.

## <a name="how-to-remove-azure-sentinel"></a>Så här tar du bort Azure Sentinel

Följ den här processen för att ta bort Azure Sentinel från din arbets yta:

1. Gå till **Azure Sentinel**, följt av **inställningarna** och välj fliken **ta bort Azure Sentinel**.

1. Innan du tar bort Azure Sentinel ska du använda kryss rutorna för att berätta varför du tar bort den.

1. Välj **ta bort Azure Sentinel från din arbets yta**.
    
    ![Ta bort SecurityInsights-lösningen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Vad händer i bakgrunden?

När du tar bort lösningen tar Azure Sentinel upp till 48 timmar innan den första fasen av borttagnings processen slutförs.

När från kopplingen har identifierats börjar offboarding-processen.

**Konfigurationen av dessa anslutningar tas bort:**
-   Office 365

-   AWS

-   Säkerhets aviseringar för Microsoft-tjänster: Microsoft Defender för identitet (*tidigare Azure ATP*) Microsoft Cloud App Security inklusive Cloud Discovery Shadow IT repor ting, Azure AD Identity Protection, Microsoft Defender för slut punkt (*tidigare Microsoft Defender ATP*), Azure Defender-aviseringar från Azure Security Center

-   Hotinformation

-   Vanliga säkerhets loggar (inklusive CEF-baserade loggar, Barracuda och syslog) (om du får Azure Defender-aviseringar från Azure Security Center kommer dessa loggar att fortsätta att samlas in.)

-   Säkerhets händelser i Windows (om du får Azure Defender-aviseringar från Azure Security Center kommer dessa loggar att fortsätta att samlas in.)

Inom de första 48 timmarna går det inte längre att komma åt data-och analys reglerna (inklusive automatiserings konfiguration i real tid) eller fråga i Azure Sentinel.

**Efter 30 dagar tas dessa resurser bort:**

-   Incidenter (inklusive undersöknings-metadata)

-   Analysregler

-   Bokmärken

Dina spelböcker, sparade arbetsböcker, sparade jaktfrågor och anteckningsböcker tas inte bort. **Vissa kan brytas på grund av borttagna data. Du kan ta bort dem manuellt.**

När du har tagit bort tjänsten finns det en respitperiod på 30 dagar då du kan återaktivera lösningen och dina data och analys regler återställs, men de konfigurerade anslutningarna som kopplades från måste återanslutas.

> [!NOTE]
> Om du tar bort lösningen fortsätter prenumerationen att vara registrerad med Azure Sentinel-resursprovidern. **Du kan ta bort den manuellt.**




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du tar bort Azure Sentinel-tjänsten. Om du ändrar dig och vill installera det igen:
- Kom igång [med att använda Azure Sentinel](quickstart-onboard.md).
