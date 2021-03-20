---
title: Vad är roaming för företags tillstånd i Azure Active Directory?
description: Enterprise State Roaming ger användare en enhetlig upplevelse på sina Windows-enheter
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77194287"
---
# <a name="what-is-enterprise-state-roaming"></a>Vad är Enterprise State Roaming?

Med Windows 10 kan [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) -användare få möjlighet att på ett säkert sätt synkronisera sina användar inställningar och program inställnings data till molnet. Enterprise State Roaming ger användare en enhetlig upplevelse på sina Windows-enheter och minskar tiden som krävs för att konfigurera en ny enhet. Enterprise State Roaming fungerar på samma sätt som den vanliga [synkroniseringen av konsument inställningar](https://go.microsoft.com/fwlink/?linkid=2015135) som infördes i Windows 8. Dessutom erbjuder Enterprise State Roaming:

* **Separering av företags-och konsument data** – organisationer har kontroll över sina data och det finns ingen blandning av företags data i ett moln konto eller konsument data i ett företags moln konto.
* **Förbättrad säkerhet** – data krypteras automatiskt innan användaren lämnar användarens Windows 10-enhet med hjälp av Azure Rights Management (Azure RMS) och data förblir krypterade i vila i molnet. Allt innehåll förblir krypterat i vila i molnet, förutom för namn områden, t. ex. namn på Inställningar och namn på Windows-appar.  
* **Bättre hantering och övervakning** – ger kontroll och insyn över vem som synkroniserar inställningarna i din organisation och på vilka enheter via Azure AD Portal-integreringen. 

Enterprise State Roaming finns i flera Azure-regioner. Du hittar den uppdaterade listan över tillgängliga regioner på sidan [Azure-tjänster per region](https://azure.microsoft.com/regions/#services) under Azure Active Directory.

| Artikel | Beskrivning |
| --- | --- |
| [Aktivera enterprise tillståndsväxling i Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming är tillgänglig för alla organisationer med en Premium Azure Active Directory-prenumeration (Azure AD). Mer information om hur du skaffar en Azure AD-prenumeration finns på produkt sidan för [Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Vanliga frågor och svar om inställningar och dataväxling](enterprise-state-roaming-faqs.md) |I den här artikeln besvaras några frågor som IT-administratörer kan ha om inställningar och synkronisering av AppData. |
| [Grup princip och MDM-inställningar för synkronisering av inställningar](enterprise-state-roaming-group-policy-settings.md) |Windows 10 tillhandahåller grupprincip-och princip inställningar för hantering av mobila enheter (MDM) för att begränsa inställningarna för synkronisering. |
| [Referens för Windows 10-växlingsinställningar](enterprise-state-roaming-windows-settings-reference.md) |En lista med inställningar som ska roamas och/eller säkerhets kopie ras i Windows 10. |
| [Felsökning](enterprise-state-roaming-troubleshooting.md) |Den här artikeln går igenom några grundläggande steg för fel sökning och innehåller en lista över kända problem. |

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar företags tillstånds växling finns i [Aktivera företags tillstånds växling](enterprise-state-roaming-enable.md).
