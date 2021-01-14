---
title: Felsöka försämrad status på Azure Traffic Manager
description: Så här felsöker du Traffic Manager profiler när de visas som degraderad status.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183463"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Felsöka degraderat tillstånd på Azure Traffic Manager

Den här artikeln beskriver hur du felsöker en Azure Traffic Manager-profil som visar en degraderad status. Det första steget i att felsöka ett Azure-Traffic Manager degraderat tillstånd är att aktivera loggning.  Mer information hittar du i [Aktivera resurs loggar](./traffic-manager-diagnostic-logs.md) . I det här scenariot bör du tänka på att du har konfigurerat en Traffic Manager-profil som pekar på några av dina cloudapp.net-värdbaserade tjänster. Om hälso tillståndet för Traffic Manager visar **degraderad** status kan statusen för en eller flera slut punkter **försämras**:

![status för degraderad slut punkt](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Om hälso tillståndet för Traffic Manager visar **inaktiv** status kan båda slut punkterna vara **inaktiverade**:

![Status för inaktiva Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Förstå Traffic Manager avsökningar

* Traffic Manager anser att en slut punkt endast är ONLINE när avsökningen får ett HTTP 200-svar tillbaka från avsöknings Sök vägen. Om du returnerar en annan HTTP-svarskod måste du lägga till den svars koden till [förväntade status kod intervall](./traffic-manager-monitoring.md#configure-endpoint-monitoring) i Traffic Manager profilen.
* Ett 30 omdirigerings svar behandlas som ett haveri om du inte har angett en giltig svarskod i [förväntade status kod intervall](./traffic-manager-monitoring.md#configure-endpoint-monitoring) för din Traffic Manager profil. Traffic Manager avsöker inte omdirigerings målet.
* Certifikat fel ignoreras för HTTPs-avsökningar.
* Det faktiska innehållet i avsöknings Sök vägen spelar ingen roll, så länge som en 200 returneras. Avsökning av en URL till ett statiskt innehåll, t. ex. "/favicon.ico" är en vanlig teknik. Dynamiskt innehåll, som ASP-sidor, kan inte alltid returnera 200, även om programmet är felfritt.
* Ett bra tips är att ställa in avsöknings Sök vägen till något som har tillräckligt med logik för att avgöra om platsen är aktiv eller inte. I föregående exempel, genom att ange sökvägen till "/favicon.ico", testar du bara att w3wp.exe svarar. Den här avsökningen kanske inte indikerar att webb programmet är felfritt. Ett bättre alternativ är att ange en sökväg till något som "/PROBE.aspx" som har logik för att fastställa webbplatsens hälso tillstånd. Du kan till exempel använda prestanda räknare för processor användning eller mäta antalet misslyckade förfrågningar. Eller så kan du försöka få åtkomst till databas resurser eller sessionstillstånd för att kontrol lera att webb programmet fungerar.
* Om alla slut punkter i en profil är degraderade Traffic Manager behandlar alla slut punkter som felfria och dirigerar trafik till alla slut punkter. Det här beteendet säkerställer att problem med avsöknings mekanismen inte resulterar i ett fullständigt avbrott i tjänsten.

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka ett avsöknings fel behöver du ett verktyg som visar HTTP-statuskoden från avsöknings-URL: en. Det finns många tillgängliga verktyg som visar RAW HTTP-svaret.

* [Fiddler](https://www.telerik.com/fiddler)
* [klammerparentes](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Du kan också använda fliken nätverk i verktyget F12-felsökning i Internet Explorer för att Visa HTTP-svar.

I det här exemplet vill vi se svaret från vår avsöknings-URL: http: \/ /watestsdp2008r2.cloudapp.net:80/PROBE. I följande PowerShell-exempel visas problemet.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exempel på utdata:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Observera att vi har fått ett omdirigerings svar. Som tidigare nämnts anses en annan StatusCode som inte är 200. Traffic Manager ändrar slut punkts statusen till offline. Lös problemet genom att kontrol lera webbplats konfigurationen för att se till att rätt StatusCode kan returneras från avsöknings Sök vägen. Konfigurera om Traffic Manager-avsökningen så att den pekar på en sökväg som returnerar en 200.

Om avsökningen använder HTTPS-protokollet kan du behöva inaktivera certifikat kontroll för att undvika SSL/TLS-fel under testet. Följande PowerShell-uttryck inaktiverar certifikat validering för den aktuella PowerShell-sessionen:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Nästa steg

[Om Traffic Manager Traffic routing-metoder](traffic-manager-routing-methods.md)

[Vad är Traffic Manager](traffic-manager-overview.md)

[Molntjänster](/previous-versions/azure/jj155995(v=azure.100))

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Åtgärder i Traffic Manager (REST-API-referens)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager-cmdletar][1]

[1]: /powershell/module/az.trafficmanager