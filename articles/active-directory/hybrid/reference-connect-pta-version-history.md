---
title: 'Direkt autentisering i Azure AD: versions historik | Microsoft Docs'
description: Den här artikeln innehåller alla versioner av Azure AD-direktautentisering
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333434"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD-direktautentisering: versions historik 
 
Agenterna som är installerade lokalt och som möjliggör direktautentisering uppdateras regelbundet för att tillhandahålla nya funktioner. Den här artikeln innehåller de versioner och funktioner som läggs till när nya funktioner lanseras. Direkt autentiserings agenter uppdateras automatiskt när en ny version släpps. 

Här är relaterade ämnen: 

- [Användar inloggning med Azure AD-direktautentisering](how-to-connect-pta.md) 
- [Installation av Azure AD-direktautentisering](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Versions status: 
04/09/2020: har släppts för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Stöd har lagts till för mål moln miljöer vid installation. Paketet kan fästas i en specifik moln miljö.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Versionsstatus 
1/22/2019: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd har lagts till för Service Bus pålitliga kanaler för att lägga till ett annat lager av anslutnings återhämtning för utgående anslutningar 
- Genomdriva TLS 1,2 under agent registreringen 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Versionsstatus 
4/10/2018: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd för WebSocket-anslutning 
- Ange TLS 1,2 som standard protokoll för agenten 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Versionsstatus 
1/31/2018: har släppts för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 
- En bugg har åtgärd ATS som orsakade minnes läckor i agenten. 
- Den Azure Service Bus versionen har uppdaterats, vilket innehåller en fel korrigering för anslutnings-timeout-problem. 
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Stöd har lagts till för WebSocket-baserade anslutningar mellan agenten och Azure AD-tjänster för att förbättra anslutnings återhämtning

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Versionsstatus 
11/25/2017: har släppts för nedladdning  
### <a name="fixed-issues"></a>Åtgärdade problem 
- Fasta buggar som är relaterade till DNS-cachen för standard scenarier för proxy 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Versionsstatus 
10/17/2017: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- Funktioner för DNS-cache har lagts till för utgående anslutningar för att lägga till återhämtning från DNS-problem 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Versionsstatus 
08/31/2017: har släppts för nedladdning  
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 
- GA-version av Azure AD-direktautentisering 

## <a name="next-steps"></a>Nästa steg

- [Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)
