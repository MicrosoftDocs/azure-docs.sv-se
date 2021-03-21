---
title: Vinkel-plugin-program för Application Insights JavaScript SDK
description: Hur du installerar och använder ett vinkel-plugin-program för Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737026"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Vinkel-plugin-program för Application Insights JavaScript SDK

Vinkel-plugin-programmet för Application Insights JavaScript SDK, aktiverar:

- Spårning av router-ändringar

> [!WARNING]
> Vinkel-plugin-programmet är inte ECMAScript 3 (ES3)-kompatibelt.

## <a name="getting-started"></a>Komma igång

Installera NPM-paket:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Grundläggande användning

Konfigurera en instans av Application Insights i inmatnings komponenten i din app:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om Java Script SDK finns i dokumentationen för [Application Insights JavaScript SDK](javascript.md)
- [Vinkel-plugin på GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
