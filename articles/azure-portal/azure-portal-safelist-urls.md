---
title: Lägga till Azure-portalens URL:er i listan över säkra URL:er i brandväggen eller proxyservern
description: 'Lägg till dessa URL: er till proxyservern kringgå för kommunikation med Azure Portal och dess tjänster'
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745884"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Lägga till Azure-portalens URL:er i listan över säkra URL:er i brandväggen eller proxyservern

Du kan konfigurera lokala säkerhets enheter för att kringgå säkerhets begränsningar för Azure Portal-URL: er. Den här konfigurationen kan förbättra prestandan och anslutningen mellan ditt lokala nätverk och ditt lokala nätverk och Azure-molnet.

Nätverks administratörer distribuerar ofta proxyservrar, brand väggar eller andra enheter. De här enheterna skyddar och ger kontroll över hur användare får åtkomst till Internet. Regler som är utformade för att skydda användare kan ibland blockera eller sakta ned giltig affärs relaterad Internet trafik. Trafiken omfattar kommunikation mellan dig och Azure. För att optimera anslutningen mellan ditt nätverk och Azure Portal och dess tjänster rekommenderar vi att du lägger till Azure Portal webb adresser i din SAFELIST.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal-URL: er för att kringgå proxyserver

URL-slutpunkterna till SAFELIST för Azure Portal är bara för det Azure-moln där organisationen har distribuerats. Om du vill tillåta nätverks trafik till dessa slut punkter för att kringgå begränsningar väljer du ditt moln. Lägg sedan till listan med URL: er till proxyservern eller brand väggen.

#### <a name="public-cloud"></a>[Offentligt moln](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Amerikanskt myndighets moln](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Kinas regerings moln](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Trafik till dessa slut punkter använder standard-TCP-portar för HTTP (80) och HTTPS (443).
>
>
