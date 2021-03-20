---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77198434"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-version i functions 1. x

I funktionerna 1. x använder Storage-utlösare och bindningar version 7.2.1 av Azure Storage SDK ([windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK och du binder till en lagrings-SDK-typ i funktions signaturen, kan funktions körningen rapportera att den inte kan bindas till den typen. Lösningen är att se till att projektet refererar till [windowsazure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
