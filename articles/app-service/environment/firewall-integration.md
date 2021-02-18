---
title: Låsa utgående trafik
description: Lär dig hur du integrerar med Azure-brandväggen för att skydda utgående trafik inifrån en App Service-miljö.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594120"
---
# <a name="locking-down-an-app-service-environment"></a>Låsa en App Service-miljön

App Service-miljön (ASE) har ett antal externa beroenden som krävs för åtkomst till för att fungera korrekt. ASE finns i kundens Azure-Virtual Network (VNet). Kunderna måste tillåta ASE-beroende trafik, vilket är ett problem för kunder som vill låsa upp alla utgående data från sitt VNet.

Det finns ett antal inkommande slut punkter som används för att hantera en ASE. Inkommande hanterings trafik kan inte skickas via en brand Väggs enhet. Käll adresserna för den här trafiken är kända och publiceras i dokumentet för [App Service-miljön hanterings adresser](./management-addresses.md) . Det finns också en service-tagg med namnet AppServiceManagement som kan användas med nätverks säkerhets grupper (NSG: er) för att skydda inkommande trafik.

ASE utgående beroenden är nästan helt definierade med FQDN, som inte har statiska adresser bakom dem. Bristen på statiska adresser innebär att nätverks säkerhets grupper inte kan användas för att låsa utgående trafik från en ASE. Adresserna ändras ofta nog för att det inte går att skapa regler baserat på den aktuella upplösningen och använda den för att skapa NSG: er. 

Lösningen för att skydda utgående adresser är att använda en brand Väggs enhet som kan styra utgående trafik baserat på domän namn. Azure-brandväggen kan begränsa utgående HTTP-och HTTPS-trafik baserat på målets FQDN.  

## <a name="system-architecture"></a>Systemarkitektur

Distribution av en ASE med utgående trafik som går via en brand Väggs enhet kräver ändring av vägar i ASE-undernätet. Vägar använder en IP-nivå. Om du inte är försiktig med att definiera dina vägar kan du tvinga TCP-svars trafik till källa från en annan adress. När din svars adress skiljer sig från den adress trafik som skickades till, kallas problemet för asymmetrisk Routning och den kommer att avbryta TCP.

Det måste finnas definierade vägar så att inkommande trafik till ASE kan svara på samma sätt som trafiken kom ifrån. Vägar måste definieras för inkommande hanterings begär Anden och för inkommande program begär Anden.

Trafiken till och från en ASE måste följa följande konventioner

* Trafiken till Azure SQL, Storage och Event Hub stöds inte med hjälp av en brand Väggs enhet. Trafiken måste skickas direkt till tjänsterna. Det kan vara bra att konfigurera tjänst slut punkter för de tre tjänsterna. 
* Regler för routningstabellen måste definieras som skickar inkommande hanterings trafik tillbaka från den plats där den kom.
* Regler för routningstabellen måste definieras som skickar inkommande program trafik tillbaka från den plats där den kom. 
* All annan trafik som lämnar ASE kan skickas till din brand Väggs enhet med en väg tabell regel.

![ASE med anslutnings flöde för Azure-brandväggen][5]

## <a name="locking-down-inbound-management-traffic"></a>Låsa inkommande hanterings trafik

Om ASE-undernätet inte redan har en tilldelad NSG, skapar du ett. I NSG anger du den första regeln för att tillåta trafik från tjänst tag gen med namnet AppServiceManagement på portarna 454, 455. Regeln för att tillåta åtkomst från AppServiceManagement-taggen är den enda som krävs från offentliga IP-adresser för att hantera din ASE. De adresser som ligger bakom den service tag gen används bara för att administrera Azure App Service. Den hanterings trafik som flödar genom dessa anslutningar krypteras och skyddas med certifikat för autentisering. Vanlig trafik i den här kanalen innehåller sådant som kunders initierade kommandon och hälso avsökningar. 

ASE som görs via portalen med ett nytt undernät görs med en NSG som innehåller regeln Allow för AppServiceManagement-taggen.  

Din ASE måste också tillåta inkommande begär Anden från Load Balancer-taggen på port 16001. Förfrågningarna från Load Balancer på port 16001 är Keep Alive-kontroller mellan Load Balancer och ASE-klientens slut punkter. Om Port 16001 är blockerad, kommer din ASE att hamna i fel tillstånd.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurera Azure-brandväggen med din ASE 

De steg som krävs för att låsa utgående från din befintliga ASE med Azure Firewall är:

1. Aktivera tjänstens slut punkter till SQL, Storage och Event Hub i ASE-undernätet. Om du vill aktivera tjänstens slut punkter går du till nätverks portalen > undernät och väljer Microsoft. EventHub, Microsoft. SQL och Microsoft. Storage i list rutan tjänst slut punkter. När du har aktiverat tjänstens slut punkter för Azure SQL måste alla Azure SQL-beroenden som dina appar har kon figurer ATS med tjänstens slut punkter också. 

   ![Välj tjänst slut punkter][2]
  
1. Skapa ett undernät med namnet AzureFirewallSubnet i det virtuella nätverk där din ASE finns. Följ anvisningarna i dokumentationen för [Azure-brandväggen](../../firewall/index.yml) för att skapa din Azure-brandvägg.

1. Välj Lägg till program regel samling i Azure Firewall UI > regler > program regel samling. Ange ett namn, prioritet och ange Tillåt. I avsnittet FQDN-Taggar anger du ett namn, anger käll adresserna till * och väljer App Service-miljön FQDN-tagg och Windows Update. 
   
   ![Lägg till program regel][1]
   
1. I användar gränssnittet för Azure-brandväggen > regler > nätverks regel samling väljer du Lägg till nätverks regel samling. Ange ett namn, prioritet och ange Tillåt. I avsnittet regler under IP-adresser anger du ett namn, väljer ett protokoll för **alla**, anger * till käll-och mål adresser och anger portarna till 123. Med den här regeln kan systemet utföra klock synkronisering med NTP. Skapa en annan regel på samma sätt som port 12000 för att hjälpa prioritering eventuella system problem. 

   ![Lägg till NTP-nätverksanslutning][3]
   
1. I användar gränssnittet för Azure-brandväggen > regler > nätverks regel samling väljer du Lägg till nätverks regel samling. Ange ett namn, prioritet och ange Tillåt. I avsnittet regler under tjänst Taggar anger du ett namn, väljer ett protokoll för **alla**, anger * till käll adresser, väljer en service tag-AzureMonitor och anger portarna till 80 443. Med den här regeln kan systemet tillhandahålla Azure Monitor med hälso-och mät information.

   ![Lägg till en NTP-tjänstinstans för service tag-regler][6]
   
1. Skapa en routningstabell med hanterings adresserna från [App Service-miljön hanterings adresser]( ./management-addresses.md) med nästa hopp på Internet. Posterna i routningstabellen krävs för att undvika problem med asymmetrisk routning. Lägg till vägar för IP-adresserna som anges nedan i IP-adress beroenden med nästa hopp på Internet. Lägg till en virtuell installations väg i routningstabellen för 0.0.0.0/0 med nästa hopp som din Azure Firewall-privata IP-adress. 

   ![Skapa en routningstabell][4]
   
1. Tilldela routningstabellen som du har skapat till ditt ASE-undernät.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Distribuera din ASE bakom en brand vägg

Stegen för att distribuera ASE bakom en brand vägg är samma som att konfigurera befintliga ASE med en Azure-brandvägg, förutom att du behöver skapa ditt ASE-undernät och sedan följa föregående steg. Om du vill skapa din ASE i ett befintligt undernät måste du använda en Resource Manager-mall enligt beskrivningen i dokumentet om [att skapa din ASE med en Resource Manager-mall](./create-from-template.md).

## <a name="application-traffic"></a>Program trafik 

Ovanstående steg gör att dina ASE kan användas utan problem. Du måste fortfarande konfigurera saker för att passa dina program behov. Det finns två problem med program i en ASE som har kon figurer ATS med Azure-brandväggen.  

- Program beroenden måste läggas till i Azure-brandväggen eller i routningstabellen. 
- Vägar måste skapas för att program trafiken ska undvika problem med asymmetrisk routning

Om dina program har beroenden måste de läggas till i din Azure-brandvägg. Skapa program regler för att tillåta HTTP/HTTPS-trafik och nätverks regler för allt annat. 

Om du känner till adress intervallet som din program begär trafik kommer från, kan du lägga till det i routningstabellen som är tilldelat ditt ASE-undernät. Om adress intervallet är stort eller ospecificerat kan du använda en nätverks enhet som Application Gateway för att ge dig en adress som du kan lägga till i routningstabellen. Mer information om hur du konfigurerar en Application Gateway med din ILB-ASE finns i [integrera din ILB-ASE med en Application Gateway](./integrate-with-application-gateway.md)

Den här användningen av Application Gateway är bara ett exempel på hur du konfigurerar systemet. Om du följer den här sökvägen måste du lägga till en väg i ASE för under nätet, så att svars trafiken som skickas till Application Gateway skulle gå dit direkt. 

## <a name="logging"></a>Loggning 

Azure-brandväggen kan skicka loggar till Azure Storage-, Event Hub-eller Azure Monitor-loggar. Om du vill integrera din app med ett mål som stöds går du till Azure Firewall-portalen > diagnostikloggar och aktiverar loggarna för önskad destination. Om du integrerar med Azure Monitor loggar kan du se loggning för all trafik som skickas till Azure-brandväggen. Om du vill se den trafik som nekas öppnar du Log Analytics arbetsyte Portal > loggar och anger en fråga som 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Att integrera din Azure-brandvägg med Azure Monitor loggar är användbart när du först får ett program att fungera när du inte är medveten om alla program beroenden. Du kan lära dig mer om Azure Monitor loggar från [analysera loggdata i Azure Monitor](../../azure-monitor/logs/log-query-overview.md).
 
## <a name="dependencies"></a>Beroenden

Följande information krävs bara om du vill konfigurera en annan brand vägg än Azure-brandväggen. 

- Tjänst slut punkts tjänster som stöder tjänster måste konfigureras med tjänst slut punkter.
- IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik)
- FQDN HTTP/HTTPS-slutpunkter kan placeras i brand Väggs enheten.
- HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera med din ASE baserat på ett antal kvalificerare. 
- Linux-beroenden är bara ett problem om du distribuerar Linux-appar till din ASE. Om du inte distribuerar Linux-appar till dina ASE behöver dessa adresser inte läggas till i brand väggen. 

#### <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden 

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| Slutpunkt | Information |
|----------| ----- |
| \*: 123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| \*: 12000 | Den här porten används för viss system övervakning. Om den blockeras kommer vissa problem att vara svårare att prioritering, men ASE kommer att fortsätta att arbeta |
| 40.77.24.27:80 | Krävs för att övervaka och varna för ASE-problem |
| 40.77.24.27:443 | Krävs för att övervaka och varna för ASE-problem |
| 13.90.249.229:80 | Krävs för att övervaka och varna för ASE-problem |
| 13.90.249.229:443 | Krävs för att övervaka och varna för ASE-problem |
| 104.45.230.69:80 | Krävs för att övervaka och varna för ASE-problem |
| 104.45.230.69:443 | Krävs för att övervaka och varna för ASE-problem |
| 13.82.184.151:80 | Krävs för att övervaka och varna för ASE-problem |
| 13.82.184.151:443 | Krävs för att övervaka och varna för ASE-problem |

Med en Azure-brandvägg får du automatiskt allt nedan konfigurerat med FQDN-taggarna. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Jokertecken för HTTP/HTTPS 

| Slutpunkt |
|----------|
|gr-Prod- \* . cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
| \*. identity.azure.net:443 |
| \*. ctldl.windowsupdate.com:80 |
| \*. ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-beroenden 

| Slutpunkt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*. cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov beroenden

För ASE i US Gov regioner följer du anvisningarna i avsnittet [Konfigurera Azure-brandväggen med ditt ASE](#configuring-azure-firewall-with-your-ase) i det här dokumentet för att konfigurera en Azure-brandvägg med din ASE.

Om du vill använda en annan enhet än Azure-brandväggen i US Gov 

* Tjänst slut punkts tjänster som stöder tjänster måste konfigureras med tjänst slut punkter.
* FQDN HTTP/HTTPS-slutpunkter kan placeras i brand Väggs enheten.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera med din ASE baserat på ett antal kvalificerare.

Linux är inte tillgängligt i US Gov regioner och anges därför inte som en valfri konfiguration.

#### <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden ####

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| Slutpunkt | Information |
|----------| ----- |
| \*: 123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| \*: 12000 | Den här porten används för viss system övervakning. Om den blockeras kommer vissa problem att vara svårare att prioritering, men ASE kommer att fortsätta att arbeta |
| 40.77.24.27:80 | Krävs för att övervaka och varna för ASE-problem |
| 40.77.24.27:443 | Krävs för att övervaka och varna för ASE-problem |
| 13.90.249.229:80 | Krävs för att övervaka och varna för ASE-problem |
| 13.90.249.229:443 | Krävs för att övervaka och varna för ASE-problem |
| 104.45.230.69:80 | Krävs för att övervaka och varna för ASE-problem |
| 104.45.230.69:443 | Krävs för att övervaka och varna för ASE-problem |
| 13.82.184.151:80 | Krävs för att övervaka och varna för ASE-problem |
| 13.82.184.151:443 | Krävs för att övervaka och varna för ASE-problem |

#### <a name="dependencies"></a>Beroenden ####

| Slutpunkt |
|----------|
| \*. ctldl.windowsupdate.com:80 |
| \*. management.usgovcloudapi.net:80 |
| \*. update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*. management.usgovcloudapi.net:443 |
|\*. update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
