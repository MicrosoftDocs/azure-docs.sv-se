---
title: DNS-analys lösning i Azure Monitor | Microsoft Docs
description: Konfigurera och Använd DNS-analys-lösningen i Azure Monitor för att samla in insikter om säkerhet, prestanda och åtgärder i DNS-infrastrukturen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: f01a1204aefbcaabe0ddac254b24bc014c3d5a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654534"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Samla in insikter om din DNS-infrastruktur med DNS-analys för hands versions lösning

![DNS-analys symbol](./media/dns-analytics/dns-analytics-symbol.png)

Den här artikeln beskriver hur du konfigurerar och använder Azure DNS Analytics-lösningen i Azure Monitor för att samla in insikter om säkerhet, prestanda och åtgärder i DNS-infrastrukturen.

DNS-analys hjälper dig att:

- Identifiera klienter som försöker lösa skadliga domän namn.
- Identifiera inaktuella resurs poster.
- Identifiera ofta efterfrågade domän namn och pratsam DNS-klienter.
- Visa begär ande belastning på DNS-servrar.
- Visa dynamiska DNS-registrerings fel.

Lösningen samlar in, analyserar och korrelerar Windows DNS-analys och gransknings loggar och andra relaterade data från dina DNS-servrar.

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten källa** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](../agents/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../vm/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in DNS-information från Direct Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](../agents/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten Operations Manager hanterings grupp. En direkt anslutning från Operations Manager agent till Azure Monitor krävs inte. Data vidarebefordras från hanterings gruppen till Log Analytics-arbetsytan. |
| [Azure Storage-konto](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Inga | Azure Storage används inte av lösningen. |

### <a name="data-collection-details"></a>Information om data insamling

Lösningen samlar in DNS-inventering och DNS-säkerhetsrelaterade data från DNS-servrarna där en Log Analytics-agent är installerad. Dessa data överförs sedan till Azure Monitor och visas i instrument panelen för lösningen. Lagerrelaterade data, till exempel antalet DNS-servrar, zoner och resurs poster, samlas in genom att köra DNS PowerShell-cmdletar. Data uppdateras var två: e dag. Händelse-relaterade data samlas in nära real tid från de [analytiska och gransknings loggar](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) som tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguration

Använd följande information för att konfigurera lösningen:

- Du måste ha en [Windows](../agents/agent-windows.md) -eller [Operations Manager](../agents/om-agents.md) -agent på varje DNS-server som du vill övervaka.
- Du kan lägga till DNS-analys-lösningen till din Log Analytics-arbetsyta från [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](solutions.md).

Lösningen börjar samla in data utan att behöva ytterligare konfiguration. Du kan dock använda följande konfiguration för att anpassa data insamling.

### <a name="configure-the-solution"></a>Konfigurera lösningen

I arbets ytan Log Analytics i Azure Portal väljer du **arbets ytans Sammanfattning** och klickar sedan på panelen **DNS-analys** . På instrument panelen för lösningen klickar du på **konfiguration** för att öppna sidan DNS-analys konfiguration. Det finns två typer av konfigurations ändringar som du kan göra:

- **Allowlisted domän namn**. Lösningen bearbetar inte alla söknings frågor. Den underhåller en tillåten av domänsuffix. Sök frågor som matchar domän namn som matchar domänsuffix i denna tillåten bearbetas inte av lösningen. Om du inte bearbetar allowlisted domän namn kan du optimera data som skickas till Azure Monitor. Standard-tillåten innehåller populära offentliga domän namn, till exempel www.google.com och www.facebook.com. Du kan visa den fullständiga standard listan genom att bläddra.

  Du kan ändra listan för att lägga till valfritt domänsuffix som du vill visa Sök insikter för. Du kan också ta bort alla domänsuffix som du inte vill visa Sök insikter för.

- **Tröskelvärde för pratsam-klient**. DNS-klienter som överskrider tröskelvärdet för antalet uppslags förfrågningar är markerade på bladet **DNS-klienter** . Standard tröskelvärdet är 1 000. Du kan redigera tröskelvärdet.

    ![Allowlisted domän namn](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Hanteringspaket

Om du använder Microsoft Monitoring Agent för att ansluta till din Log Analytics-arbetsyta installeras följande hanterings paket:

- Microsoft DNS data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Om din Operations Manager hanterings grupp är ansluten till arbets ytan Log Analytics installeras följande hanterings paket i Operations Manager när du lägger till den här lösningen. Det finns ingen obligatorisk konfiguration eller underhåll av dessa hanterings paket:

- Microsoft DNS data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Konfiguration av Microsoft System Center Advisor DNS-analys (Microsoft.IntelligencePack.Dns.Configuration)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../agents/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Använd DNS-analys-lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Panelen DNS innehåller antalet DNS-servrar där data samlas in. Den innehåller också antalet förfrågningar som klienterna har gjort för att lösa skadliga domäner under de senaste 24 timmarna. När du klickar på panelen öppnas lösningens instrument panel.

![DNS-analys panel](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Instrumentpanel för lösningen

Instrument panelen för lösningen visar sammanfattande information om de olika funktionerna i lösningen. Den innehåller också länkar till den detaljerade vyn för analys och diagnostik av kriminal tekniska. Som standard visas data under de senaste sju dagarna. Du kan ändra datum-och tidsintervallet med hjälp av **kontrollen för datum-och tids markering**, som du ser i följande bild:

![Kontroll för tids markering](./media/dns-analytics/dns-time.png)

Instrument panelen för lösningen visar följande blad:

**DNS-säkerhet**. Rapporterar DNS-klienter som försöker kommunicera med skadliga domäner. Genom att använda Microsoft Threat Intelligence-flöden kan DNS-analys identifiera klient-IP-adresser som försöker komma åt skadliga domäner. I många fall kan skadlig kod som infekteras av skadlig kod "ringa ut" till "kommando-och kontroll"-centret i den skadliga domänen genom att matcha det skadliga domän namnet.

![Bladet DNS-säkerhet](./media/dns-analytics/dns-security-blade.png)

När du klickar på en klient-IP i listan öppnas loggs ökningen och visar Sök detaljerna för respektive fråga. I följande exempel har DNS-analys identifierat att kommunikationen har gjorts med en [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Loggs öknings resultat som visar IRCBot](./media/dns-analytics/ircbot.png)

Informationen hjälper dig att identifiera:

- Klient-IP som initierade kommunikationen.
- Domän namn som matchar den skadliga IP-adressen.
- IP-adresser som domän namnet matchar.
- Skadlig IP-adress.
- Problemets allvarlighets grad.
- Orsak för Blocklisting av den skadliga IP-adressen.
- Identifierings tid.

**Efterfrågade domäner**. Visar de vanligaste domän namnen som efter frågas av DNS-klienterna i din miljö. Du kan visa listan över alla domän namn som efter frågas. Du kan också öka detalj nivån för Sök begär Anden för ett särskilt domän namn i loggs ökningen.

![Bladet domän frågad](./media/dns-analytics/domains-queried-blade.png)

**DNS-klienter**. Rapporterar klienter som *bryter mot tröskelvärdet* för antalet frågor under den valda tids perioden. Du kan visa listan över alla DNS-klienter och information om de frågor som gjorts av dem i loggs ökning.

![Bladet DNS-klienter](./media/dns-analytics/dns-clients-blade.png)

**Dynamiska DNS-registreringar**. Rapporterar fel vid namn registrering. Alla registrerings fel för adress [resurs poster](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A och AAAA) är markerade tillsammans med de klient-IP: er som gjorde registrerings förfrågningarna. Du kan sedan använda den här informationen för att hitta rotor saken till registrerings felet genom att följa dessa steg:

1. Hitta den zon som är auktoritativ för det namn som klienten försöker uppdatera.

1. Använd lösningen för att kontrol lera inventerings informationen för zonen.

1. Kontrol lera att den dynamiska uppdateringen för zonen är aktive rad.

1. Kontrol lera om zonen har kon figurer ATS för säker dynamisk uppdatering eller inte.

    ![Bladet dynamiska DNS-registreringar](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Begär Anden om namn registrering**. Den övre panelen visar en trend linje om lyckade och misslyckade DNS-begäranden om dynamiska uppdateringar. Den nedre panelen visar de 10 främsta klienter som skickar misslyckade DNS-begäranden till DNS-servrarna, sorterade efter antalet fel.

![Bladet namn registrerings begär Anden](./media/dns-analytics/name-reg-req-blade.png)

**Exempel på DDI Analytics-frågor**. Innehåller en lista över de vanligaste Sök frågorna som hämtar rå Analytics-data direkt.


![Exempelfrågor](./media/dns-analytics/queries.png)

Du kan använda dessa frågor som utgångs punkt för att skapa egna frågor för anpassad rapportering. Frågorna länkar till sidan DNS-analys loggs sökning där resultatet visas:

- **Lista över DNS-servrar**. Visar en lista över alla DNS-servrar med tillhör ande FQDN, domän namn, skogs namn och Server-IP-adresser.
- **Lista över DNS-zoner**. Visar en lista över alla DNS-zoner med det associerade zon namnet, status för dynamisk uppdatering, namnservrar och status för DNSSEC-signering.
- **Oanvända resurs poster**. Visar en lista över alla oanvända/inaktuella resurs poster. Den här listan innehåller resurs post namnet, resurs post typen, den associerade DNS-servern, postens generations tid och zonnamn. Du kan använda den här listan för att identifiera de DNS-resursposter som inte längre används. Utifrån den här informationen kan du ta bort dessa poster från DNS-servrarna.
- **Fråga om inläsning av DNS-servrar**. Visar information så att du kan få ett perspektiv på DNS-belastningen på dina DNS-servrar. Den här informationen kan hjälpa dig att planera kapaciteten för servrarna. Du kan gå till fliken **mått** om du vill ändra vyn till en grafisk visualisering. Den här vyn hjälper dig att förstå hur DNS-belastningen distribueras på dina DNS-servrar. Den visar trender för DNS-frågor för varje server.

    ![DNS-servrar fråga logg Sök Resultat](./media/dns-analytics/dns-servers-query-load.png)

- **Inläsning av DNS-zoner fråga**. Visar DNS-zonen – fråga per sekund-statistik för alla zoner på DNS-servrarna som hanteras av lösningen. Klicka på fliken **mått** om du vill ändra vyn från detaljerade poster till en grafisk visualisering av resultaten.
- **Konfigurations händelser**. Visar alla ändrings händelser för DNS-konfiguration och associerade meddelanden. Du kan sedan filtrera dessa händelser baserat på tid för händelsen, händelse-ID, DNS-server eller aktivitets kategori. Data kan hjälpa dig att granska ändringar som gjorts på vissa DNS-servrar vid vissa tidpunkter.
- **DNS-analytisk logg**. Visar alla analys händelser på alla DNS-servrar som hanteras av lösningen. Du kan sedan filtrera dessa händelser baserat på händelsens tid, händelse-ID, DNS-server, klient-IP som gjorde söknings frågan och uppgifts kategorin fråga. DNS-serverns analys händelser aktiverar aktivitets spårning på DNS-servern. En analys händelse loggas varje gången servern skickar eller tar emot DNS-information.

### <a name="search-by-using-dns-analytics-log-search"></a>Sök med DNS-analys loggs ökning

På sidan loggs ökning kan du skapa en fråga. Du kan filtrera Sök resultaten genom att använda fasett-kontroller. Du kan också skapa avancerade frågor för att transformera, filtrera och rapportera resultatet. Börja med följande frågor:

1. I **rutan Sök fråga** skriver `DnsEvents` du för att visa alla DNS-händelser som genererats av DNS-servrarna som hanteras av lösningen. Resultatet visar logg data för alla händelser som rör Sök frågor, dynamiska registreringar och konfigurations ändringar.

    ![Loggs ökning för DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Om du vill visa loggdata för Sök frågor väljer du **LookUpQuery** som under **typs** filter från fasett-kontrollen till vänster. En tabell med en lista över alla uppslags frågans händelser för den valda tids perioden visas.

    b. Om du vill visa loggdata för dynamiska registreringar väljer du **DynamicRegistration** som under **typs** filter från fasett-kontrollen till vänster. En tabell med en lista över alla dynamiska registrerings händelser för den valda tids perioden visas.

    c. Om du vill visa logg data för konfigurations ändringar väljer du **ConfigurationChange** som under **typs** filter från fasett-kontrollen till vänster. En tabell som visar alla konfigurations ändrings händelser för den valda tids perioden visas.

1. I **rutan Sök fråga** skriver `DnsInventory` du för att visa alla DNS-lagerrelaterade data för DNS-servrarna som hanteras av lösningen. Resultatet visar logg data för DNS-servrar, DNS-zoner och resurs poster.

    ![Loggs ökning för DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Felsökning

Vanliga fel söknings steg:

1. DNS-sökningar saknas – för att felsöka det här problemet kan du försöka med att återställa konfigurationen eller läsa in konfigurations sidan en gång i portalen. För att återställa, ändra bara en inställning till ett annat värde och ändra sedan tillbaka till det ursprungliga värdet och spara konfigurationen.

## <a name="suggestions"></a>Förslag

Om du vill ge feedback kan du gå till [sidan Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) och publicera idéer för DNS-analys funktioner att arbeta med. 

## <a name="next-steps"></a>Nästa steg

[Fråga loggar](../logs/log-query-overview.md) om du vill visa detaljerade DNS-loggfiler.
