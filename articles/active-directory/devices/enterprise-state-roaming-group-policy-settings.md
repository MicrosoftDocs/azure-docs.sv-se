---
title: Grupprincip-och MDM-inställningar för ESR-Azure Active Directory
description: Hanterings inställningar för Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252941"
---
# <a name="group-policy-and-mdm-settings"></a>grupprincip-och MDM-inställningar

Använd dessa inställningar för grup princip och hantering av mobila enheter (MDM) endast på företagsägda enheter eftersom dessa principer tillämpas på användarens hela enhet. Om du använder en MDM-princip för att inaktivera inställningar synkronisering för en personlig enhet påverkar den enhet som används negativt. Dessutom kommer andra användar konton på enheten också att påverkas av principen.

Företag som vill hantera nätverks växling för personliga (ej hanterade) enheter kan använda Azure Portal för att aktivera eller inaktivera roaming i stället för att använda grupprincip eller MDM.
I följande tabeller beskrivs de tillgängliga princip inställningarna.

> [!NOTE]
> Den här artikeln gäller Microsoft Edge äldre HTML-baserad webbläsare lanserad med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge krom-baserade webbläsaren som lanserades den 15 januari 2020. Mer information om hur synkronisering fungerar för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>MDM-inställningar

Inställningarna för MDM-principen gäller både Windows 10 och Windows 10 Mobile.  Support för Windows 10 Mobile finns bara för Microsoft-konto baserad nätverks växling via användarens OneDrive-konto. Se [enheter och slut punkter](enterprise-state-roaming-windows-settings-reference.md) för information om vilka enheter som stöds för Azure AD-baserad synkronisering.

| Name | Beskrivning |
| --- | --- |
| Tillåt anslutning till Microsoft-konto |Tillåter användare att autentisera med hjälp av en Microsoft-konto på enheten |
| Tillåt mina inställningar för synkronisering |Tillåter användare att flytta Windows-inställningar och appdata. Om du inaktiverar den här principen inaktive ras synkronisering och säkerhets kopior på mobila enheter |

## <a name="group-policy-settings"></a>Grupprincipinställningar

Grup princip inställningarna gäller för Windows 10-enheter som är anslutna till en Active Directory-domän. Tabellen innehåller även äldre inställningar som visas för att hantera synkroniseringsinställningar, men som inte fungerar för Enterprise State Roaming för Windows 10, som anges med "Använd inte" i beskrivningen.

De här inställningarna finns på: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Name | Beskrivning |
| --- | --- |
| Konton: blockera Microsoft-konton |Den här princip inställningen förhindrar att användare lägger till nya Microsoft-konton på den här datorn |
| Synkronisera inte |Förhindrar att användare flyttar Windows-inställningar och AppData |
| Synkronisera inte personanpassa |Inaktiverar synkronisering av tema gruppen |
| Synkronisera inte webb läsar inställningar |Inaktiverar synkronisering av Internet Explorer-gruppen |
| Synkronisera inte lösen ord |Inaktiverar synkronisering av lösen ords grupper |
| Synkronisera inte andra Windows-inställningar |Inaktiverar synkronisering av andra Windows-inställningar, grupp |
| Synkronisera inte Skriv bords anpassning |Använd inte; har ingen påverkan |
| Synkronisera inte med anslutningar med datapriser |Inaktiverar roaming i anslutningar med datapriser, till exempel mobilt 3G |
| Synkronisera inte appar |Använd inte; har ingen påverkan |
| Synkronisera inte appinställningar |Inaktiverar roaming av AppData |
| Synkronisera inte Start Inställningar |Använd inte; har ingen påverkan |

## <a name="next-steps"></a>Nästa steg

En översikt finns i [Översikt över företags tillstånds växling](enterprise-state-roaming-overview.md).
