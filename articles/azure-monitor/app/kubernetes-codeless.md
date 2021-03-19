---
title: Övervaka program på Azure Kubernetes service (AKS) med Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor integreras sömlöst med ditt program som körs på Kubernetes och gör att du kan hitta problem med dina appar på en gång.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87075297"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Övervakning av program vara med noll Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  För närvarande kan du aktivera övervakning av Java-appar som körs på Kubernetes utan att instrumentera din kod – Använd den [fristående Java-agenten](./java-in-process-agent.md). Lösningen för att sömlöst aktivera program övervakning är i Works för andra språk, och använder SDK: erna för att övervaka dina appar som körs på AKS: [ASP.net Core](./asp-net-core.md), [ASP.net](./asp-net.md), [Node.js](./nodejs.md), [Java Script](./javascript.md)och [python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Program övervakning utan att behöva instrumentera koden
För närvarande kan du aktivera program övervakning utan att behöva instrumentera koden med Java. Använd SDK: er för att övervaka program på andra språk. 

## <a name="java"></a>Java
När den är aktive rad samlar Java-agenten automatiskt in en mängd begär Anden, beroenden, loggar och mått från de mest använda biblioteken och ramverken.

Följ [de detaljerade anvisningarna](./java-in-process-agent.md) för att övervaka Java-appar som körs i Kubernetes-appar, samt andra miljöer. 

## <a name="other-languages"></a>Andra språk

För program på andra språk rekommenderar vi för närvarande användning av SDK: er:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Monitor](../overview.md) och [Application Insights](./app-insights-overview.md)
* Få en översikt över [distribuerad spårning](./distributed-tracing.md) och se vilken [program karta](./app-map.md?tabs=net) som kan utföras för ditt företag
