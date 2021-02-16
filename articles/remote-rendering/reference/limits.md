---
title: Begränsningar
description: Kod begränsningar för SDK-funktioner
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f62a07ef7109c669c6abc90d4e1b4a2ee83ece23
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530254"
---
# <a name="limitations"></a>Begränsningar

Ett antal funktioner har storlek, antal eller andra begränsningar.

## <a name="azure-frontend"></a>Azure-frontend

Följande begränsningar gäller för klient dels-API: et (C++ och C#):
* Totalt antal [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) -instanser per process: 16.
* Totalt antal [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) -instanser per [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>Objekt

* Totalt antal tillåtna objekt av en enskild typ ([entitet](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)osv.): 16 777 215.
* Totalt antal tillåtna aktiva klipp plan: 8.

## <a name="geometry"></a>Geometri

* **Animering:** Animeringar är begränsade till animering av enskilda omvandlingar av [spel objekt](../concepts/entities.md). Skeletal-animeringar med skalnings-eller hörn funktioner stöds inte. Animerings spår från käll till gångs filen bevaras inte. I stället måste objekt Transformations animeringar drivas av klient koden.
* **Anpassade skuggningar:** Redigering av anpassade skuggningar stöds inte. Endast inbyggt [färg material](../overview/features/color-materials.md) eller PBR- [material](../overview/features/pbr-materials.md) kan användas.
* **Maximalt antal distinkt material** i en till gång: 65 535. Mer information om minskning av automatiskt material antal finns [i kapitlet om att minska material.](../how-tos/conversion/configure-model-conversion.md#material-de-duplication)
* **Högsta dimension för en enda textur**: 16 384 x 16 384. Större käll texturer minskas i storlek av konverterings processen.

### <a name="overall-number-of-polygons"></a>Totalt antal polygoner

Det tillåtna antalet polygoner för alla laddade modeller beror på storleken på den virtuella datorn som skickas till [sessionen hanterings REST API](../how-tos/session-rest-api.md):

| Server storlek | Maximalt antal polygoner |
|:--------|:------------------|
|standard| 20 000 000 |
|denaturering| ingen gräns |

Detaljerad information om den här begränsningen finns i avsnittet [Server storlek](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Plattforms begränsningar

**Windows 10 Desktop**

* Win32/x64 är den enda Win32-plattform som stöds. Win32/x86 stöds inte.

**HoloLens 2**

* [Kamera funktionen rendera från PV](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) stöds inte.