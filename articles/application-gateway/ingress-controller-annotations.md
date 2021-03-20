---
title: Application Gateway ingångs kontroll anteckningar
description: Den här artikeln innehåller dokumentation om anteckningarna som är aktuella för Application Gateway ingress-kontrollanten.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397458"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anteckningar för Application Gateway ingångs kontroll 

## <a name="introductions"></a>Introduktioner

Kubernetes ingress-resursen kan kommenteras med godtyckliga nyckel/värde-par. AGIC är beroende av kommentarer till program Application Gateway funktioner som inte kan konfigureras via ingångs YAML. Ingångs anteckningar tillämpas på alla HTTP-inställningar, backend-pooler och lyssnare som härletts från en ingress-resurs.

## <a name="list-of-supported-annotations"></a>Lista över anteckningar som stöds

För att en ingångs resurs ska kunna observeras av AGIC **måste den kommenteras** `kubernetes.io/ingress.class: azure/application-gateway` . AGIC kommer bara att fungera med den ingående resursen i fråga.

| Antecknings nyckel | Värdetyp | Standardvärde | Tillåtna värden
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` sekunder | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` sekunder | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefix för Server dels Sök väg

Den här anteckningen tillåter att Server dels Sök vägen som anges i en ingress-resurs skrivs om med prefix som anges i den här anteckningen. Detta gör det möjligt för användare att exponera tjänster vars slut punkter skiljer sig från slut punkts namn som används för att exponera en tjänst i en ingress-resurs.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
I exemplet ovan har vi definierat en ingress-resurs med namnet `go-server-ingress-bkprefix` med en anteckning `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` . Anteckningen instruerar Application Gateway att skapa en HTTP-inställning, vilket kommer att ha en åsidosättning av sökvägar för sökvägen `/hello` till `/test/` .

> [!NOTE] 
> I ovanstående exempel har vi bara en definierad regel. Anteckningarna är dock tillämpliga för hela ingångs resursen, så om en användare har definierat flera regler, skulle sökvägen för Server delen vara inställd för var och en av de angivna Sök vägarna. Det innebär att om en användare vill ha olika regler med olika sökvägar för prefix (även för samma tjänst) måste de definiera olika ingångs resurser.

## <a name="tls-redirect"></a>TLS-omdirigering

Application Gateway [kan konfigureras](./redirect-overview.md) för automatisk omdirigering av http-URL: er till deras https-motsvarigheter. När den här anteckningen är tillgänglig och TLS har kon figurer ATS, skapar Kubernetes ingress Controller en [regel för vidarebefordran med en omdirigerings konfiguration](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) och tillämpar ändringarna på din Application Gateway. Den omdirigering som skapas är HTTP `301 Moved Permanently` .

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Anslutningen töms

`connection-draining`: Med den här anteckningen kan användarna ange om anslutnings tömning ska aktive ras.
`connection-draining-timeout`: Den här anteckningen gör det möjligt för användare att ange en tids gräns efter vilken Application Gateway kommer att avsluta begär anden till slut punkten för tömning av Server delen.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Med den här anteckningen kan du ange om du vill aktivera cookie-baserad tillhörighet.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Timeout för begäran

Den här anteckningen gör det möjligt att ange tids gränsen för begäran i sekunder efter vilken Application Gateway Miss kan utföra begäran om inget svar tas emot.

### <a name="usage"></a>Användning

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Exempel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Använd privat IP

Med den här anteckningen kan vi ange om den här slut punkten ska exponeras för den privata IP-adressen för Application Gateway.

> [!NOTE]
> * Application Gateway stöder inte flera IP-adresser på samma port (exempel: 80/443). Ingress med anteckningen `appgw.ingress.kubernetes.io/use-private-ip: "false"` och en annan med `appgw.ingress.kubernetes.io/use-private-ip: "true"` på gör `HTTP` att AGIC inte kan uppdatera Application Gateway.
> * För Application Gateway som inte har en privat IP-adress `appgw.ingress.kubernetes.io/use-private-ip: "true"` ignoreras ingressen. Detta återspeglas i styrenhets loggarna och inkommande händelser för dessa ingress med `NoPrivateIP` varning.


### <a name="usage"></a>Användning
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Backend-protokoll

Med den här anteckningen kan vi ange det protokoll som Application Gateway bör använda vid pratar med poddar. Protokoll som stöds: `http` , `https`

> [!NOTE]
> * Även om självsignerade certifikat stöds på Application Gateway, för närvarande endast stöd för AGIC `https` när poddar använder certifikat som har signerats av en välkänd certifikat utfärdare.
> * Se till att inte använda port 80 med HTTPS och port 443 med HTTP på poddar.

### <a name="usage"></a>Användning
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Exempel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```