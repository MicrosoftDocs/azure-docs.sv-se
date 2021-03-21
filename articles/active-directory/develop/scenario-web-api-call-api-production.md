---
title: 'Flytta webb-API som anropar webb-API: er till produktion | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du flyttar ett webb-API som anropar webb-API: er till produktion.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675880"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Ett webb-API som anropar webb-API: er: flytta till produktion

När du har skaffat en token för att anropa webb-API: er finns det några saker att tänka på när du flyttar ditt program till produktion.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna för hur du anropar webb-API: er från ditt eget webb-API kan du vara intresse rad av följande självstudie, som beskriver koden som används för att bygga ett skyddat webb-API som anropar webb-API: er.

| Exempel | Plattform | Beskrivning |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-självstudie-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) kapitel 1 | ASP.NET Core webb-API, Desktop (WPF) | ASP.NET Core Web API-anrop Microsoft Graph som du anropar från ett WPF-program med hjälp av Microsoft Identity Platform. |
