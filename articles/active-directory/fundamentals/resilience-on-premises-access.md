---
title: Bygg återhämtning i program åtkomst med Application Proxy
description: En guide för arkitekter och IT-administratörer om att använda Application Proxy för elastisk åtkomst till lokala program
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724668"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Bygg återhämtning i program åtkomst med Application Proxy

Application Proxy är en funktion i Azure AD som gör det möjligt för användare att komma åt lokala webb program från en fjärran sluten klient. Programproxyn innehåller både Application Proxy-tjänsten i molnet och Application Proxy-anslutningarna som körs på en lokal server. 

Användare kommer åt lokala resurser via en URL som publicerats via programproxyn. De omdirigeras till inloggnings sidan för Azure AD. Application Proxy-tjänsten i Azure AD skickar sedan en token till Application Proxy Connector i företags nätverket, som skickar token till den lokala Active Directory att den autentiserade användaren kan komma åt den lokala resursen. I diagrammet nedan visas [kopplingarna](../manage-apps/application-proxy-connectors.md) i en [kopplings grupp](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> När du publicerar dina program via Application Proxy måste du implementera [kapacitets planering och lämplig redundans för Application Proxy-anslutningarna](../manage-apps/application-proxy-connectors.md#capacity-planning).

![Arkitektur diagram för program y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Hur gör jag för att implementera programproxyn?

Information om hur du implementerar fjärråtkomst med Azure AD-programproxy finns i följande resurser.

* [Planera en distribution av programproxy](../manage-apps/application-proxy-deployment-plan.md)

* [Metod tips för hög tillgänglighet och belastnings utjämning](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Konfigurera proxyservrar](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Designa en strategi för elastisk åtkomst kontroll](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)