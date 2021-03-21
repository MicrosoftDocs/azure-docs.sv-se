---
title: Installera Azure spatiala ankare för ett C++/WinRT HoloLens-program
description: Konfigurera ett C++/WinRT HoloLens-projekt för att använda Azures ankare för spatialdata
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95507003"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Konfigurera Azure spatiala ankare i ett C++/WinRT HoloLens-projekt

## <a name="requirements"></a>Krav

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) installerat med **universell Windows-plattform utvecklings** arbets belastning och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** .

## <a name="configuring-a-project"></a>Konfigurera ett projekt

Azures spatiala ankare för HoloLens-och C++-/WinRT distribueras med hjälp av [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet-paketet.

Följ anvisningarna [här](/nuget/consume-packages/install-use-packages-visual-studio) för att använda Visual Studios NuGet Package Manager för att installera [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet-paketet i projektet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: skapa och hitta ankare i C++/WinRT](./create-locate-anchors-cpp-winrt.md)