---
title: Azure Active Directory rapporterings fördröjning | Microsoft Docs
description: Lär dig mer om hur lång tid det tar för rapport händelser att visas i din Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0498ee1c57cfa661884fe3209d4e089b54996fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89231069"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory-rapporteringssvarstider

Svars tid är hur lång tid det tar för Azure Active Directory (Azure AD) rapporterings data att visas i [Azure Portal](https://portal.azure.com). Den här artikeln innehåller den förväntade svars tiden för de olika typerna av rapporter. 

## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två typer av aktivitets rapporter:

- [Inloggningar](concept-sign-ins.md) – ger information om användningen av hanterade program och användar inloggnings aktiviteter
- [Gransknings loggar](concept-audit-logs.md) – innehåller information om system aktivitet för användare och grupper, hanterade program och katalog aktiviteter

I följande tabell visas latens information för aktivitets rapporter. 

> [!NOTE]
> **Latens (95 percentil)** avser den tid som 95% av loggarna rapporteras och **latens (99 percentil)** avser den tid som 99% av loggarna rapporteras. 
>

| Rapport | Latens (95 percentil) |Latens (99 percentil)|
| :-- | --- | --- |
| Granskningsloggar | 2 min  | 5 min  |
| Inloggningar | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart kan jag se aktivitets data när de har skaffat en Premium-licens?

Om du redan har aktivitets data med din kostnads fria licens kan du se den direkt vid uppgraderingen. Om du inte har några data, tar det en eller två dagar innan data visas i rapporterna efter att du har uppgraderat till en Premium-licens.

## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två typer av säkerhetsrapporter:

- [Riskfyllda inloggningar](../identity-protection/overview-identity-protection.md) – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto. 
- [Användare som har flaggats för risk](../identity-protection/overview-identity-protection.md) – en riskfylld användare är en indikator för ett användar konto som kan ha komprometterats. 

I följande tabell visas svars tids informationen för säkerhets rapporter.

| Rapport | Minimum | Genomsnitt | Maximal |
| :-- | --- | --- | --- |
| Användare i riskzonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-detections"></a>Riskidentifieringar

Azure AD använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användar konton. Varje misstänkt misstänkt åtgärd lagras i en post som kallas för **identifiering av risker**.

I följande tabell visas svars informationen för risk identifieringar.

| Rapport | Minimum | Genomsnitt | Maximal |
| :-- | --- | --- | --- |
| Inloggningar från anonyma IP-adresser |5 minuter |15 minuter |2 timmar |
| Inloggningar från okända platser |5 minuter |15 minuter |2 timmar |
| Användare med läckta autentiseringsuppgifter |2 timmar |4 timmar |8 timmar |
| Omöjliga resor till ovanliga platser |5 minuter |1 timme |8 timmar  |
| Inloggningar från angripna enheter |2 timmar |4 timmar |8 timmar  |
| Inloggningar från IP-adresser med misstänkt aktivitet |2 timmar |4 timmar |8 timmar  |


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure AD-rapporter](overview-reports.md)
* [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
* [Azure Active Directory risk identifieringar](../identity-protection/overview-identity-protection.md)