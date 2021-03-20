---
title: Inbyggt reakta plugin-program för Application Insights JavaScript SDK
description: Så här installerar och använder du det inbyggda reakta-plugin-programmet för Application Insights Java Script SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593591"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Inbyggt reakta plugin-program för Application Insights JavaScript SDK

Det interna reakta-plugin-programmet för Application Insights JavaScript SDK samlar in enhets information, som standard samlar plugin-programmet automatiskt in:

- **Unikt enhets-ID** (kallas även installations-ID.)
- **Enhets modell namn** (t. ex. iPhone X, Samsung Galaxy-vikning, Huawei P30 Pro osv.)
- **Enhets typ** (till exempel telefonlur, surfplatta osv.)

## <a name="requirements"></a>Krav

Du måste använda en version >= 2.0.0 av `@microsoft/applicationinsights-web` . Det här plugin-programmet fungerar bara i reagera-ursprungliga appar. Den fungerar inte med [appar som använder ramverket exponeras](https://docs.expo.io/), därför fungerar det inte med skapa reagera inbyggd app.

## <a name="getting-started"></a>Komma igång

Installera och länka paketet [reakta-Native-Device-info](https://www.npmjs.com/package/react-native-device-info) . Håll `react-native-device-info` paketet uppdaterat för att samla in de senaste enhets namnen med hjälp av din app.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Initierar plugin-programmet

Om du vill använda det här plugin-programmet måste du konstruera plugin-programmet och lägga till det som ett `extension` till din befintliga Application Insights-instans.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Nästa steg

- Läs mer om Java Script SDK i [Application Insights JavaScript SDK-dokumentationen](javascript.md).
- Information om Kusto-frågespråket och frågor om data i Log Analytics finns i [Översikt över logg frågor](../../azure-monitor/logs/log-query-overview.md).
