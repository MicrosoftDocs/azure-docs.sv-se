---
title: Reagera på plugin-programmet för Application Insights JavaScript SDK
description: Installera och använda reagera-plugin-program för Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 8b24ee9f620b50056ceb466ef5bdc38da97682a9
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490751"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Reagera på plugin-programmet för Application Insights JavaScript SDK

Reagera på plugin-programmet för Application Insights JavaScript SDK, aktiverar:

- Spårning av väg ändringar
- Användnings statistik för reagerar på komponenter

## <a name="getting-started"></a>Komma igång

Installera NPM-paket:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Grundläggande användning

Initiera en anslutning till Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Sätt upp komponenten med komponenten med högre ordning för att aktivera Application Insights:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Konfiguration

| Name    | Standardvärde | Beskrivning                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| historik | null    | Reagerar på routerkonfiguration. Mer information finns i [paket dokumentationen för reakta router](https://reactrouter.com/web/api/history). Information om hur du kommer åt historik-objektet utanför komponenterna finns i [vanliga frågor och svar om att reagera på routern](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Reagera på användnings spårning för komponenter

Om du vill Instrumenta olika användnings spårning för reagerande komponenter använder du `withAITracking` komponenten med högre ordning.

Det kommer att mäta tiden från `ComponentDidMount` händelsen genom `ComponentWillUnmount` evenemanget. För att göra detta mer korrekt kommer det dock att ta bort tiden då användaren var inaktiv `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Om du vill se det här måttet i Azure Portal måste du gå till Application Insights resurs, välja fliken "mått" och konfigurera de tomma diagrammen för att visa det anpassade mått namnet "" reagera komponent aktive tid (sekunder) ", Välj agg regering (sum, AVG osv.) för ditt mått och tillämpa Split är" komponent namn ".

![Skärm bild av diagram som visar det anpassade måttet "" reagera komponent aktive tid (sekunder) "dela efter" komponent namn "](./media/javascript-react-plugin/chart.png)

Du kan också köra anpassade frågor för att dela upp Application Insights data för att generera rapporter och visualiseringar enligt dina krav. I Azure Portal navigerar du till Application Insights resurs väljer du Analytics på den översta menyn på fliken Översikt och kör frågan.

![Skärm bild av anpassade metriska frågeresultat.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Det kan ta upp till 10 minuter innan nya anpassade mått visas i Azure-portalen.

## <a name="using-react-hooks"></a>Använda reakta hookar

[Reakta Hooks](https://reactjs.org/docs/hooks-reference.html) är en metod för tillstånds-och livs cykel hantering i ett reagerar program utan att behöva förlita sig på klassbaserade reakta-komponenter. Det Application Insights reagera-plugin-programmet ger ett antal Hook-integrationer som fungerar på samma sätt som den högre ordningens komponent metod.

### <a name="using-react-context"></a>Använda reagera på kontext

De reakta Hookarna för Application Insights är utformade för att använda en [Reaktad kontext](https://reactjs.org/docs/context.html) som innehåller aspekt för den. Om du vill använda kontext initierar du Application Insights som ovan och importerar sedan objektet context:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Den här kontext leverantören gör Application Insights tillgänglig som en `useContext` Hook i alla underordnade komponenter i den.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric`Hooken replikerar funktionerna i den `withAITracking` högre order komponenten, utan att lägga till ytterligare en komponent i komponent strukturen. Hooken tar två argument, först är Application Insights-instansen (som kan hämtas från `useAppInsightsContext` hooken) och en identifierare för komponenten för spårning (till exempel dess namn).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Den fungerar som den högre beställnings komponenten, men svarar på hookar av livs cykel händelser i stället för en komponents livs cykel. Hooken måste uttryckligen anges för användar händelser om det finns behov av att köra vissa interaktioner.

### `useTrackEvent`

`useTrackEvent`Kroken används för att spåra eventuella anpassade händelser som ett program kan behöva spåra, till exempel en knapp eller något annat API-anrop. Det tar fyra argument:
-   Application Insights instans (som kan hämtas från `useAppInsightsContext` hooken).
-   Namn för händelsen.
-   Händelse data objekt som kapslar in de ändringar som ska spåras.
-   skipFirstRun (valfritt) för att hoppa över anrop av `trackEvent` anrop vid initiering. Standardvärdet är inställt på `true`.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const [cart, setCart] = useState([]);
    const trackCheckout = useTrackEvent(appInsights, "Checkout", cart);
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated", cart);
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button></li>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button></li>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button></li>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button></li>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}

export default MyComponent;
```

När hooken används kan en data nytto Last tillhandahållas för att lägga till ytterligare data till händelsen när den lagras i Application Insights.

## <a name="react-error-boundaries"></a>Reagera på fel gränser

[Fel gränser](https://reactjs.org/docs/error-boundaries.html) ger ett sätt att på ett smidigt sätt hantera ett undantag när det inträffar i ett reagerar program, och när det här felet inträffar är det troligt att undantaget måste loggas. Testplugin-programmet för Application Insights ger en fel avgränsnings komponent som automatiskt kommer att logga felet när det inträffar.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

`AppInsightsErrorBoundary`Två propor måste skickas till den, den `ReactPlugin` instans som skapas för programmet och en komponent som ska renderas när ett fel uppstår. När ett ohanterat fel inträffar `trackException` anropas med den information som angetts för fel gränser och `onError` komponenten visas.

## <a name="sample-app"></a>Exempelapp

Kolla in [Application Insights reagera på demo](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Java Script SDK i [Application Insights JavaScript SDK-dokumentationen](javascript.md).
- Information om Kusto-frågespråket och frågor om data i Log Analytics finns i [Översikt över logg frågor](../../azure-monitor/logs/log-query-overview.md).
