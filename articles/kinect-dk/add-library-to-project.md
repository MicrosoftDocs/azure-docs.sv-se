---
title: Lägg till Azure Kinect-biblioteket i Visual Studio-projektet
description: Lär dig hur du lägger till Azure Kinect NuGet-paketet i Visual Studio-projektet.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359620"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Lägg till Azure Kinect-biblioteket i Visual Studio-projektet

Den här artikeln vägleder dig genom processen att lägga till Azure Kinect NuGet-paketet i Visual Studio-projektet.

## <a name="install-azure-kinect-nuget-package"></a>Installera Azure Kinect NuGet-paketet

Så här installerar du Azure Kinect NuGet-paketet:

1. Detaljerade anvisningar för hur du installerar ett NuGet-paket i Visual Studio finns i [snabb start: installera och använda ett paket i Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Om du vill lägga till paketet kan du använda Package Manager-gränssnittet genom att högerklicka på referenser och välja Hantera NuGet-paket från Solution Explorer.
3. Välj [NuGet.org](https://www.nuget.org) som paket källa, Välj fliken Bläddra och Sök efter `Microsoft.Azure.Kinect.Sensor` .
4. Välj det paketet i listan och installera.

## <a name="use-azure-kinect-nuget-package"></a>Använd Azure Kinect NuGet-paket

När paketet har lagts till inkluderar Lägg till huvud fil till käll koden, till exempel:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Nu är du redo att bygga ditt första program](build-first-app.md)