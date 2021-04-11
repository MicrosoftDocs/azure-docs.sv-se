---
title: Uppgradera till Azure AD-programproxy | Microsoft Docs
description: Välj vilken proxyserver som är bäst om du uppgraderar från Microsoft Forefront eller Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52cb4480e8b7820d656b8ab66f12486c2062dca5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443499"
---
# <a name="compare-remote-access-solutions"></a>Jämför lösningar för fjärråtkomst

Azure Active Directory-programproxy är en av två lösningar för fjärråtkomst som Microsoft erbjuder. Det andra är Webbprogramproxy, den lokala versionen. Dessa två lösningar ersätter tidigare produkter som Microsoft erbjuder: Microsoft Forefront Threat Management Gateway (TMG) och Unified Access Gateway (UAG). Använd den här artikeln för att förstå hur dessa fyra lösningar jämförs med varandra. Om du fortfarande använder de inaktuella TMG-eller UAG-lösningarna kan du använda den här artikeln för att planera migreringen till en av programproxyn. 


## <a name="feature-comparison"></a>Jämför funktioner

Använd den här tabellen för att förstå hur Threat Management Gateway (TMG), Unified Access Gateway (UAG), Webbprogramproxy (WAP) och Azure AD-programproxy (AP) jämför med varandra.

| Funktion | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Certifikatsautentisering | Ja | Ja | - | - |
| Selektiv publicering av webb läsar appar | Ja | Ja | Ja | Ja |
| Förautentisering och enkel inloggning | Ja | Ja | Ja | Ja | 
| Layer 2/3-brandvägg | Ja | Ja | - | - |
| Vidarebefordra proxy-funktioner | Ja | - | - | - |
| VPN-funktioner | Ja | Ja | - | - |
| Stöd för omfattande protokoll | - | Ja | Ja, om det körs över HTTP | Ja, om det körs över HTTP eller via Fjärrskrivbordsgateway |
| Fungerar som ADFS-proxyserver | - | Ja | Ja | - |
| En portal för program åtkomst | - | Ja | - | Ja |
| Översättning av svars text länk | Ja | Ja | - | Ja | 
| Autentisering med sidhuvud | - | Ja | - | Ja, med PingAccess | 
| Säkerhet i moln skala | - | - | - | Ja | 
| Villkorlig åtkomst | - | Ja | - | Ja |
| Inga komponenter i demilitariserad-zonen (DMZ) | - | - | - | Ja |
| Inga inkommande anslutningar | - | - | - | Ja |

I de flesta fall rekommenderar vi Azure AD-programproxy som modern lösning. Webbprogramproxy föredras bara i scenarier som kräver en proxyserver för AD FS och du kan inte använda anpassade domäner i Azure Active Directory. 

Azure AD-programproxy ger unika fördelar jämfört med liknande produkter, inklusive:

- Utöka Azure AD till lokala resurser
   - Säkerhet och skydd i moln skala
   - Funktioner som villkorlig åtkomst och Multi-Factor Authentication är lätta att aktivera
- Inga komponenter i demilitariserad-zonen
- Inga inkommande anslutningar krävs
- En sida med mina appar som användarna kan gå till för alla sina program, inklusive Microsoft 365, Azure AD Integrated SaaS-appar och dina lokala webbappar. 


## <a name="next-steps"></a>Nästa steg

- [Använd Azure AD-programproxy för att ge säker fjärråtkomst till lokala program](application-proxy.md)
