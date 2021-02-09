---
title: Övervakare av nätverksprestanda Solution service-anslutning – Azure Log Analytics
description: Använd tjänst anslutningens övervaknings funktion i Övervakare av nätverksprestanda för att övervaka nätverks anslutningen till en slut punkt som har en öppen TCP-port.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 5d7c7068c4a8669cdff782267030d38ac0f73584
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832937"
---
# <a name="service-connectivity-monitor"></a>Övervakare av tjänstanslutning

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda till den nya anslutnings övervakaren](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Du kan använda tjänst anslutnings övervakarens funktion i [övervakare av nätverksprestanda](network-performance-monitor.md) för att övervaka nätverks anslutningen till en slut punkt som har en öppen TCP-port. Sådana slut punkter omfattar webbplatser, SaaS-program, PaaS-program och SQL-databaser. 

Du kan utföra följande funktioner med tjänst anslutnings övervakaren: 

- Övervaka nätverks anslutningen till dina program och nätverks tjänster från flera avdelnings kontor eller platser. Program och nätverks tjänster omfattar Microsoft 365, Dynamics CRM, interna branschspecifika program och SQL-databaser.
- Använd inbyggda tester för att övervaka nätverks anslutningen till Microsoft 365-och Dynamics 365-slutpunkter. 
- Fastställ svars tid, nätverks fördröjning och paket förlust som uppstått vid anslutning till slut punkten.
- Ta reda på om dåliga program prestanda beror på nätverket eller på grund av problem med program leverantörens slut.
- Identifiera aktiva punkter i nätverket som kan orsaka dåliga program prestanda genom att Visa svars tiden för varje hopp på en Topology-karta.


![Övervakare av tjänstanslutning](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguration 
Öppna konfigurationen för Övervakare av nätverksprestanda genom att öppna [övervakare av nätverksprestanda lösning](network-performance-monitor.md) och välja **Konfigurera**.

![Konfigurera Övervakare av nätverksprestanda](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurera Log Analytics agenter för övervakning
Aktivera följande brand Väggs regler på noderna som används för övervakning så att lösningen kan identifiera topologin från noderna till tjänstens slut punkt: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Skapa övervaknings test för tjänst anslutning 

Börja skapa dina tester för att övervaka nätverks anslutningen till tjänstens slut punkter.

1. Välj fliken **Övervakare för tjänst anslutning** .
2. Välj **Lägg till test** och ange test namnet och beskrivningen. Du kan skapa maximalt 450 test per arbets yta. 
3. Välj typ av test:<br>

    * Välj **webb** för att övervaka anslutning till en tjänst som svarar på http/S-begäranden, till exempel outlook.office365.com eller Bing.com.<br>
    * Välj **nätverk** för att övervaka anslutning till en tjänst som svarar på TCP-begäranden, men som inte svarar på http/S-begäranden, till exempel en SQL Server, FTP-server eller SSH-port. 
    * Exempel: om du vill skapa ett webbtest till ett Blob Storage-konto väljer du **webb** och anger mål som *yourstorageaccount*. blob.Core.Windows.net. På samma sätt kan du skapa tester för annan tabell lagring, Queue Storage och Azure Files med [den här länken.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Om du inte vill utföra nätverks mått, till exempel nätverks svars tid, paket förlust och identifiering av topologi, avmarkerar du kryss rutan **utför nätverks mått** . Håll det markerat för att få ut högsta nytta av kapaciteten. 
5. I **mål** anger du den URL/FQDN/IP-adress som du vill övervaka nätverks anslutningen till.
6. I **port nummer** anger du Port numret för mål tjänsten. 
7. I **test frekvens** anger du ett värde för hur ofta du vill att testet ska köras. 
8. Välj de noder från vilka du vill övervaka nätverks anslutningen till tjänsten. Se till att antalet agenter som lagts till per test är mindre än 150. Alla agenter kan testa maximalt 150 slut punkter/agenter.

    >[!NOTE]
    > För Windows Server-baserade noder använder funktionen TCP-baserade förfrågningar för att utföra nätverks måtten. För Windows-klientbaserade noder använder funktionen ICMP-baserade förfrågningar för att utföra nätverks måtten. I vissa fall blockerar mål programmet inkommande ICMP-baserade förfrågningar när noderna är Windows-baserade klientbaserade. Lösningen kan inte utföra nätverks mått. Vi rekommenderar att du använder Windows Server-baserade noder i sådana fall. 

9. Om du inte vill skapa hälso händelser för de objekt du väljer rensar du **Aktivera hälso övervakning i de mål som omfattas av det här testet**. 
10. Välj övervaknings villkor. Du kan ange anpassade tröskelvärden för att skapa hälso tillstånds händelser genom att ange tröskelvärdena. När värdet för villkoret hamnar ovanför det valda tröskelvärdet för det valda nätverket eller under nätverks paret genereras en hälso händelse. 
11. Välj **Spara** för att spara konfigurationen. 

    ![Testa konfigurationer för övervakning av tjänst anslutnings övervakare](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Genomgång 

Gå till vyn Övervakare av nätverksprestanda instrument panel. Om du vill få en översikt över hälso tillståndet för de olika test som du har skapat tittar du på **tjänst anslutnings övervaknings** sidan. 

![Tjänst anslutnings övervaknings sida](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Välj panelen för att visa information om testerna på sidan **tester** . I tabellen till vänster kan du Visa tidpunkts hälsa och värde för svars tid för tjänsten, nätverks fördröjning och paket förlust för alla tester. Använd kontrollen nätverks tillstånds kontroll för att Visa nätverks ögonblicks bilden vid en annan tidpunkt tidigare. Välj testet i den tabell som du vill undersöka. I diagrammen i fönstret till höger kan du Visa den historiska trenden för värdena förlust, svars tid och svars tid. Välj länken **testa information** för att Visa prestandan från varje nod.

![Övervaknings test för tjänst anslutning](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

I vyn **testnoder** kan du studera nätverks anslutningen från varje nod. Välj den nod som har prestanda försämring. Det här är den nod där programmet observeras att köra långsamt.

Ta reda på om dåliga program prestanda beror på nätverket eller ett problem på programproviderns slut genom att observera korrelationen mellan programmets svars tid och nätverks fördröjningen. 

* **Program problem:** En insamling i svars tiden men konsekvens i nätverks fördröjningen antyder att nätverket fungerar bra och att problemet kan bero på ett problem i programmets slut. 

    ![Problem med övervakning av tjänst anslutnings program](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Nätverks problem:** En insamling i svars tid tillsammans med en motsvarande insamling i nätverks fördröjningen innebär att ökningen av svars tiden kan bero på en ökning av nätverks fördröjningen. 

    ![Nätverks problem med tjänst anslutnings övervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

När du har fastställt att problemet beror på nätverket väljer du länken Topology View ( **topologi** ) för att identifiera problematiska-hoppet på Topology-kartan. Ett exempel visas i följande bild. Från 105-MS totalt svars tid mellan noden och program slut punkten, 96 MS beror på hoppet som marker ATS i rött. När du har identifierat problematiska-hoppet kan du vidta lämpliga åtgärder. 

![Topologi för övervaknings slut punkt för tjänst anslutning](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostik 

Följ dessa steg om du ser en avvikelse:

* Om tjänstens svars tid, nätverks förlust och svars tid visas som NA kan en eller flera av följande orsaker vara orsaken:

    - Programmet är inte tillgängligt.
    - Noden som används för att kontrol lera nätverks anslutningen till tjänsten är avstängd.
    - Målet som angavs i test konfigurationen är felaktigt.
    - Noden har ingen nätverks anslutning.

* Om en giltig svars tid för tjänsten visas men både nätverks förlust och svars tid visas som NA, kan en eller flera av följande orsaker vara orsaken:

    - Om noden som används för att kontrol lera nätverks anslutningen till tjänsten är en Windows-klientdator blockerar antingen mål tjänsten ICMP-begäranden eller så blockerar den nätverks brand väggen ICMP-begäranden som kommer från noden.
    - Kryss rutan **utför nätverks mätningar** är tom i test konfigurationen. 

* Om svars tiden för tjänsten är saknas men både nätverks förlust och svars tid är giltiga, kanske mål tjänsten inte är ett webb program. Redigera test konfigurationen och välj test typ som **nätverk** i stället för **webben**. 

* Om programmet körs långsamt avgör du om dåliga program prestanda beror på nätverket eller ett problem på program leverantörens slut.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-URL: er för amerikanska myndighets kunder
För amerikanska myndigheter i Virginia-regionen är endast DOD-URL: er inbyggda NPM. Kunder som använder GCC-URL: er måste skapa anpassade tester och lägga till varje URL individuellt.

| Fält | GCC |
|:---   |:--- |
| Office 365-portalen och delad | portal.apps.mil |
| Office 365-autentisering och identitet | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Nästa steg
[Sök i loggar](../log-query/log-query-overview.md) om du vill visa detaljerade data poster för nätverks prestanda.
