---
title: 'Förbered mobilapp – anropa webb-API: er för produktion | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er. (Förbered appar för produktion.)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675965"
---
# <a name="prepare-mobile-apps-for-production"></a>Förbered mobila appar för produktion

Den här artikeln innehåller information om hur du förbättrar kvalitet och pålitlighet för mobilappen innan du flyttar den till produktion.

## <a name="handle-errors"></a>Hantera fel

När du förbereder en mobilapp för produktion kan flera fel tillstånd uppstå. De huvudsakliga fall som du hanterar är tysta haverier och återställningar för interaktion. Andra villkor som du bör ta hänsyn till omfattar inga nätverks situationer, tjänst avbrott, krav på administrativt godkännande och andra scenario-särskilda fall.

För varje MSAL-typ (Microsoft Authentication Library) kan du hitta exempel kod och wiki-innehåll som beskriver hur du hanterar fel tillstånd:

- [MSAL Android-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du provar ytterligare exempel finns i [Desktop och mobila offentliga klient program](sample-v2-code.md#desktop-and-mobile-public-client-apps).