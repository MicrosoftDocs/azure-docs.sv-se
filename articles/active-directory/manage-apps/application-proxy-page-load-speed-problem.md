---
title: Det tar för lång tid att läsa in Application Proxy-programmet | Microsoft Docs
description: Felsöka prestanda problem vid sid inläsning med Azure-AD-programproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cb11cc82ede3b51b5502730079618e136d3ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254482"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Det tar för lång tid att läsa in Application Proxy-programmet

Den här artikeln hjälper dig att förstå varför ett Azure AD-programproxy-program kan ta lång tid att läsa in. Det förklarar också vad du kan göra för att lösa problemet.

## <a name="overview"></a>Översikt
Även om dina program fungerar kan de uppleva lång latens. Nätverks sto pol Ogin kan göra att du kan förbättra hastigheten. En utvärdering av olika topologier finns i [dokumentet om nätverks överväganden](application-proxy-network-topology.md).

Förutom nätverk sto pol Ogin finns det för närvarande inga ytterligare rekommendationer för prestanda justering. När tjänsten Application Proxy expanderas kan det komma till ett Data Center som är fysiskt närmare. Närmare närhet kan hjälpa dig med svars tid. En lista över Azure-datacenter finns på [test sidan för svars tider](http://www.azurespeed.com/Azure/Latency). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback om platser för data Center i Application Proxy 
Det kan finnas Azure-datacenter som ännu inte innehåller programproxy, men som leder till en bättre tids fördröjning. Skicka data Center platsen till aadapfeedback@microsoft.com . Microsoft använder din feedback för expansions planer.

Microsoft arbetar med ytterligare funktioner för att förbättra svars tiden. Så snart dessa förbättringar är tillgängliga kommer dokumentationen att uppdateras.

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
