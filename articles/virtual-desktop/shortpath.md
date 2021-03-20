---
title: RDP-Shortpath för Windows Virtual Desktop (för hands version)
titleSuffix: Azure
description: Konfigurera RDP-Shortpath (för hands version) för Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571598"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>RDP-Shortpath för Windows Virtual Desktop (för hands version)

> [!IMPORTANT]
> RDP-Shortpath är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP-Shortpath är en funktion i Windows Virtual Desktop som upprättar en direkt UDP-baserad transport mellan fjärr skrivbords klienten och sessionens värd. RDP använder den här transporten för att leverera fjärr skrivbord och RemoteApp samtidigt som erbjuder bättre tillförlitlighet och konsekvent svars tid.

## <a name="key-benefits"></a>Viktiga fördelar

* RDP Shortpath-transport baseras på en mycket effektiv  [URCP (Universal Rate Control Protocol)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP förbättrar UDP med aktiv övervakning av nätverks förhållandena och ger en rättvis och fullständig länk användning. URCP arbetar med låg fördröjning och förlust nivåer efter behov av fjärr skrivbord. URCP uppnår bästa prestanda genom att dynamiskt lära sig använda nätverks parametrar och tillhandahålla protokoll med en hastighets kontroll funktion.
* RDP-Shortpath upprättar den direkta anslutningen mellan fjärr skrivbords klienten och sessionsvärdservern. Direkt anslutning minskar beroendet av Windows-gatewayer för virtuella datorer, förbättrar anslutningens tillförlitlighet och ökar bandbredden som är tillgänglig för varje användarsession.
* Borttagningen av ytterligare relä minskar tiden för fördröjningen, vilket förbättrar användar upplevelsen med latens känsliga program och indatametoder.
* RDP-Shortpath ger stöd för att [konfigurera QoS-prioritet (Quality of Service)](./rdp-quality-of-service-qos.md) för RDP-anslutningar via DSCP-märken (Differentiated Services Code Point)
* Med RDP Shortpath transport kan du [begränsa utgående nätverks trafik](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) genom att ange en begränsnings nivå för varje session.

## <a name="connection-security"></a>Anslutningssäkerhet

RDP-Shortpath utökar RDP multi-transport-funktionerna. Den ersätter inte omvänd Connect-transport utan kompletterar den. Alla de första sessions koordinatorerna hanteras via Windows-infrastrukturen för virtuella datorer.

UDP-port 3390 används endast för inkommande Shortpath-trafik som autentiseras över omvänd Connect-transport. RDP Shortpath-lyssnare ignorerar alla anslutnings försök till lyssnaren om de inte matchar den omvända Connect-sessionen.

RDP-Shortpath använder en TLS-anslutning mellan klienten och sessionens värd med hjälp av sessionens värddators certifikat. Som standard genereras certifikatet som används för RDP-kryptering självt av operativ systemet under distributionen. Om du vill kan kunderna distribuera centralt hanterade certifikat som utfärdats av utfärdaren av företags certifikat utfärdaren. Mer information om certifikat konfigurationer finns i [Windows Server-dokumentationen](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath-anslutnings ordning

När du har installerat [Omvänd Connect-transport](./network-connectivity.md)upprättar klienten och sessionen RDP-anslutningen och förhandla multi-transport-funktioner. Ytterligare steg som beskrivs nedan:

1. Sessions-värden skickar listan över privata och offentliga IPv4-och IPv6-adresser till klienten.
2. Klienten startar bakgrunds tråden för att upprätta en parallell UDP-baserad transport direkt till en av värdens IP-adresser.
3. Medan klienten avavsöker de angivna IP-adresserna fortsätter den första anslutnings etableringen över den omvända anslutningen för att säkerställa ingen fördröjning i användar anslutningen.
4. Om klienten har direkt insikter och brand Väggs konfigurationen är korrekt upprättar klienten en säker TLS-anslutning med Session Host.
5. När du har etablerat Shortpath-transporten flyttar RDP alla dynamiska virtuella kanaler (DVCs), inklusive fjärrgrafik, inmatade och omdirigering av enheter till den nya transporten.
6. Om en brand vägg eller nätverkstopologi hindrar klienten från att upprätta direkt UDP-anslutning fortsätter RDP med en omvänd Connect-transport.

Diagrammet nedan ger en översikt över RDP-Shortpath nätverks anslutning.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagram över RDP-Shortpath nätverks anslutningar" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Krav

För att stödja RDP-Shortpath, behöver Windows Virtual Desktop-klienten en direkt rad med information till sessionens värd. Du kan få en direkt rad med detaljerad information genom att använda någon av följande tekniker:

* [ExpressRoute privat peering](../expressroute/expressroute-circuit-peerings.md)
* [Plats-till-plats-VPN (IPsec-baserad)](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Punkt-till-plats-VPN (IPsec-baserad)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Tilldelning av offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)

Om du använder andra VPN-typer för att ansluta till det virtuella Azure-nätverket rekommenderar vi att du använder UDP-baserad VPN för bästa möjliga resultat. Även om majoriteten av de TCP-baserade VPN-lösningarna kapslar in alla IP-paket, inklusive UDP, lägger de till ärvda kostnader för TCP-överbelastnings kontroll som skulle sänka RDP-prestandan.

Den direkta insikten innebär att brand väggar inte blockerar UDP-port 3390 och klienten kan ansluta direkt till sessionens värd.

## <a name="enabling-rdp-shortpath-preview"></a>Aktivera för hands versionen av RDP-Shortpath

Om du vill delta i för hands versionen av RDP-Shortpath måste du aktivera RDP Shortpath-lyssnare på sessionen. Du kan aktivera RDP-Shortpath på valfritt antal sessionsbaserade värdar som används i din miljö. Det finns inget krav på att aktivera RDP-Shortpath på alla värdar i poolen.
Om du vill aktivera Shortpath-lyssnare måste du konfigurera följande register värden:

> [!WARNING]
> Allvarliga problem kan uppstå om du ändrar registret på ett felaktigt sätt med Registereditorn eller med en annan metod. Dessa problem kan kräva att du installerar om operativ systemet. Microsoft kan inte garantera att dessa problem går att lösa. Du ändrar registret på egen risk.

1. Starta Regedit.exe på sessionsvärdservern och gå sedan till följande plats:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Skapa ett nytt **DWORD** -värde med namnet **fUseUdpPortRedirector** och ange det till **1** (decimal)
3. Skapa ett nytt **DWORD** -värde med namnet **UdpPortNumber** och ställ in det på **3390** (decimal)
4. Avsluta Registereditorn.
5. Starta om värd för session

Du kan också köra följande cmdlets i ett upphöjd PowerShell-fönster för att ange följande register värden:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Du kan också använda PowerShell för att konfigurera grup princip

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Konfigurera Windows Defender-brandväggen med avancerad säkerhet

Om du vill tillåta inkommande nätverks trafik för RDP-Shortpath använder du noden Windows Defender-brandväggen med avancerad säkerhet i MMC-snapin-modulen grupprincip hantering för att skapa brand Väggs regler.

1. Öppna konsolen grupprinciphantering till [Windows Defender-brandväggen med avancerad säkerhet](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. I navigerings fönstret väljer du **regler för inkommande trafik**.
3. Välj **åtgärd** och välj sedan **ny regel**.
4. På sidan **regeltyp** i guiden ny inkommande regel väljer du **anpassad** och väljer sedan **Nästa**.
5. På sidan **program** väljer du **den här program Sök vägen** och skriver "% SystemRoot% \system32\svchost.exe" och väljer sedan **Nästa**.
6. På sidan **protokoll och portar** väljer du typ av UDP-protokoll. Välj "vissa portar" i den **lokala porten** och skriv in 3390.
7. På sidan **omfattning** kan du ange att regeln endast gäller nätverks trafik till eller från IP-adresserna som anges på den här sidan. Konfigurera efter behov för din design och välj sedan **Nästa**.
8. På sidan **åtgärd** väljer du **Tillåt anslutningen** och väljer sedan **Nästa**.
9. På sidan **profil** väljer du de nätverks plats typer som regeln gäller och väljer sedan **Nästa**.
10. På sidan **namn** anger du ett namn och en beskrivning för regeln och väljer sedan **Slutför**.

Du kan kontrol lera att den nya regeln matchar skärm bilderna nedan: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="skärm bild av fliken Allmänt för brand Väggs konfiguration för RDP Shortpath-nätverks anslutningar" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Skärm bild av fliken program och tjänster för brand Väggs konfiguration för RDP Shortpath-nätverks anslutningar" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Skärm bild av fliken protokoll och portar för brand Väggs konfiguration för RDP Shortpath-nätverks anslutningar" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Du kan också använda PowerShell för att konfigurera Windows-brand väggen:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Använda PowerShell för att konfigurera Windows Defender-brandväggen

Du kan också använda PowerShell för att konfigurera grup princip

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Konfigurerar Azure nätverks säkerhets grupp

Om du vill tillåta åtkomst till RDP-Shortpath lyssnare över nätverks säkerhets gränser måste du konfigurera Azure nätverks säkerhets gruppen så att den tillåter inkommande UDP-port 3390.
Följ [dokumentationen för nätverks säkerhets gruppen](../virtual-machines/windows/nsg-quickstart-portal.md) för att skapa en inkommande säkerhets regel som tillåter trafik med följande parametrar:

* **Källa**  -  **Valfritt** eller IP-intervallet där klienterna är bosatta
* **Käll port intervall** - **\***
* **Mål**  -  **Alla**
* **Mål ports intervall**  -  **3390**
* **Protokoll**  -  **UDP**
* **Åtgärd**  -  **Tillåt**
* Du kan också ändra **prioriteten**. Prioriteten påverkar ordningen i vilken reglerna tillämpas: det lägre numeriska värdet, som är det tidigare regeln tillämpas.
* **Namn** -- **RDP-Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Inaktivera RDP-Shortpath för ett speciellt undernät

Om du behöver blockera vissa undernät från att använda RDP Shortpath-transporten kan du konfigurera ytterligare nätverks säkerhets grupper som anger käll-IP-intervallen.

## <a name="verifying-the-connectivity"></a>Verifiera anslutningen

### <a name="using-connection-information-dialog"></a>Dialog rutan Använd anslutnings information

Kontrol lera att anslutningarna använder RDP-Shortpath genom att öppna dialog rutan anslutnings information genom att klicka på antenn ikonen i verktygsfältet anslutning.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Bild av Anslutning till fjärrskrivbords fältet":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Bild av dialog rutan Anslutning till fjärrskrivbord information":::

### <a name="using-event-logs"></a>Använda händelse loggar

Så här kontrollerar du att sessionen använder RDP Shortpath-transport:

1. Anslut till den virtuella datorns Station ära dator med hjälp av Windows Virtual Desktop-klienten.
2. Starta Loggboken och navigera till följande nod: program- **och tjänst loggar > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Ta reda på om RDP Shortpath transport används genom att leta efter händelse-ID 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Använda Log Analytics för att verifiera Shortpath-anslutningen

Om du använder [Azure Log Analytics](./diagnostics-log-analytics.md)kan du övervaka anslutningar genom att fråga [WVDConnections-tabellen](/azure/azure-monitor/reference/tables/wvdconnections). En kolumn med namnet UdpUse anger om Windows Virtual Desktop RDP stack använder UDP-protokoll för aktuell användar anslutning.
Möjliga värden är:

* **0** – användarens anslutning använder inte RDP-Shortpath
* **1** – användarens anslutning använder RDP-Shortpath
  
I följande lista med frågor kan du granska anslutnings information. Du kan köra den här frågan i [Log Analytics Frågeredigeraren](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query). Ersätt `userupn` med UPN för den användare som du vill söka efter varje fråga.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Felsökning

### <a name="verify-shortpath-listener"></a>Verifiera Shortpath-lyssnare

Verifiera att UDP-lyssnaren är aktive rad genom att använda följande PowerShell-kommando på sessionen värd:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Om aktive rad visas utdata som följande

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Om det finns en konflikt kan du identifiera processen som använder porten med hjälp av följande kommando

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Inaktivera RDP-Shortpath

I vissa fall kan du behöva inaktivera RDP Shortpath-transport. Du kan inaktivera RDP-Shortpath med hjälp av grup principen.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Inaktivera RDP-Shortpath på klienten

Om du vill inaktivera RDP-Shortpath för en speciell klient kan du använda följande grupprincip för att inaktivera UDP-stödet:

1. Kör **gpedit. msc** på klienten.
2. Gå till **dator konfiguration > administrativa mallar > Windows-komponenter > Fjärrskrivbordstjänster > anslutning till fjärrskrivbord-klient**.
3. Ange inställningen **"inaktivera UDP på klienten"** till **aktive rad**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Inaktivera RDP-Shortpath på sessionens värd

Om du vill inaktivera RDP-Shortpath för en angiven sessionsnyckel kan du inaktivera UDP-stödet genom att använda följande grupprincip:

1. På sessionsvärdservern kör **gpedit. msc**.
2. Gå till **dator konfiguration > administrations mallar > Windows-komponenter > Fjärrskrivbordstjänster > anslutning till fjärrskrivbord värd > anslutningar**.
3. Ange inställningen **"Välj RDP transport Protocols** till **TCP**

## <a name="public-preview-feedback"></a>Offentlig för hands versions feedback

Vi vill gärna höra om dina upplevelser med den här offentliga för hands versionen!
* [Använd det här formuläret](https://aka.ms/RDPShortpathFeedback)för frågor, förfrågningar, kommentarer och annan feedback.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om anslutning till virtuella Windows-datorer i Windows kan du läsa om [nätverks anslutning i Windows Virtual Desktop](network-connectivity.md).
* För att komma igång med tjänst kvalitet (QoS) för Windows Virtual Desktop, se [implementera tjänst kvalitet (QoS) för Windows Virtual Desktop](rdp-quality-of-service-qos.md).