---
title: Felsöka problem med nätverks anslutningen – Azure Event Grid | Microsoft Docs
description: Den här artikeln innehåller information om fel sökning av problem med nätverks anslutningen med Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96339753"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Felsök anslutnings problem – Azure Event Grid

Det finns olika orsaker till att klient program inte kan ansluta till ett Event Grid ämne/domän. De anslutnings problem som du upplever kan vara permanenta eller tillfälliga. Om problemet inträffar hela tiden (permanent) kanske du vill kontrol lera din organisations brand Väggs inställningar, inställningar för IP-brandvägg, service märken, privata slut punkter och mycket annat. För tillfälliga problem kan du köra kommandon för att kontrol lera ignorerade paket och hämta nätverks spår för att felsöka problemen.

Den här artikeln innehåller tips för fel sökning av anslutnings problem med Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Felsök problem med permanent anslutning

Om programmet inte kan ansluta till Event Grid alls följer du stegen i det här avsnittet för att felsöka problemet.

### <a name="check-if-theres-a-service-outage"></a>Kontrol lera om det finns ett tjänst avbrott

Sök efter återställningen av Azure Event Grid tjänsten på [status webbplatsen för Azure-tjänsten](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Kontrol lera om portarna som krävs för att kommunicera med Event Grid inte blockeras av organisationens brand vägg

Kontrol lera att portarna som används vid kommunikation med Azure Event Grid inte är blockerade i din organisations brand vägg. Se följande tabell för utgående portar som du måste öppna för att kommunicera med Azure Event Grid.

| Protokoll | Portar |
| -------- | ----- |
| HTTPS    | 443   |

Här är ett exempel kommando som kontrollerar om 443-porten är blockerad.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

I Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Kontrol lera att IP-adresser tillåts i företags brand väggen

När du arbetar med Azure måste du ibland tillåta vissa IP-adressintervall eller URL: er i företagets brand vägg eller proxy för att få åtkomst till alla Azure-tjänster som du använder eller försöker använda. Kontrol lera att trafiken tillåts på IP-adresser som används av Event Grid. För IP-adresser som används av Azure Event Grid: se [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519) och [service tag-AzureEventGrid](network-security.md#service-tags).

[Azure IP-intervall och service märken – offentliga moln](https://www.microsoft.com/download/details.aspx?id=56519) dokument listar också IP-adresser **efter region**. Du kan tillåta adress intervall för **ämnets region** och den **kopplade regionen** i företagets brand vägg eller proxy. För en kopplad region för en region, se [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Det gick inte att lägga till nya IP-adresser i AzureEventGrid service tag, även om det inte är vanligt. Det är därför klokt att göra en veckovis kontroll av tjänst taggarna.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Kontrol lera att AzureEventGrid service tag är tillåtet i dina nätverks säkerhets grupper

Om ditt program körs i ett undernät och om det finns en associerad nätverks säkerhets grupp, kontrollerar du om antingen Internet-utgående tillåts eller om AzureEventGrid service tag är tillåtet. Se [tjänst Taggar](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Kontrol lera inställningarna för IP-brandvägg för ditt ämne/domän

Kontrol lera att den offentliga IP-adressen för den dator där programmet körs inte har blockerats av EventGrid ämne/domän-IP-brandvägg.

Som standard är Event Grid ämnen/domäner tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

IP-brandväggens regler tillämpas på Event Grid ämne/domän nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Event Grid ämne/domän avvisas som förbjuden. Svaret nämns inte IP-regeln.

Mer information finns i [Konfigurera IP-brandvägg för en Azure Event Grid ämne/domän](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Hitta IP-adresserna som blockeras av IP-brandväggen

Aktivera diagnostikloggar för Event Grid ämne/domän [Aktivera diagnostikloggar](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). IP-adressen för den anslutning som nekas visas.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Kontrol lera om EventGrid ämne/domän kan nås enbart med en privat slut punkt

Om Event Grid ämnet/domänen är konfigurerad att vara åtkomlig enbart via privat slut punkt, kontrollerar du att klient programmet har åtkomst till avsnittet/domänen över den privata slut punkten. Kontrol lera att klient programmet körs i ett undernät och att det finns en privat slut punkt för Event Grid ämne/domän i det under nätet.

Med [tjänsten Azure Private Link](../private-link/private-link-overview.md) kan du komma åt Azure Event Grid över en **privat slut punkt** i det virtuella nätverket. En privat slut punkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Konfigurera privata slut punkter](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Felsök problem med tillfälliga anslutningar

Om det uppstår tillfälliga anslutnings problem går du igenom följande avsnitt för fel söknings tips.

### <a name="run-the-command-to-check-dropped-packets"></a>Kör kommandot för att kontrol lera ignorerade paket

När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget härifrån. [](/sysinternals/downloads/psping)

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Du kan använda motsvarande kommandon om du använder andra verktyg som `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs.

### <a name="service-upgradesrestarts"></a>Tjänst uppgraderingar/omstarter

Tillfälliga anslutnings problem kan uppstå på grund av uppgraderingar och omstarter av Server dels tjänsten. När de inträffar kan följande inträffa:

- Det kan finnas en minskning av inkommande meddelanden/begär Anden.
- Logg filen kan innehålla fel meddelanden.
- Programmen kan vara frånkopplade från tjänsten under några sekunder.
- Begär Anden kan begränsas tillfälligt.

Genom att fånga de här tillfälliga felen, och sedan försöka igen, ser du till att din kod är elastisk mot de här tillfälliga problemen.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/).