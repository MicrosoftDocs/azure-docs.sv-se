---
title: Schemalagda händelser för virtuella Linux-datorer i Azure
description: Schemalägg händelser med hjälp av Azure Metadata Service för virtuella Linux-datorer.
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: 1029790cbcfa86f988c2249d67640a642e529229
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674868"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Schemalagda händelser för virtuella Linux-datorer

Schemalagda händelser är en Azure-Metadata Service som ger din program tid att förbereda för underhåll av virtuella datorer. Den innehåller information om kommande underhålls händelser (till exempel omstart) så att ditt program kan förbereda sig för dem och begränsa avbrott. Den är tillgänglig för alla typer av Azure-Virtual Machines, inklusive PaaS och IaaS på både Windows och Linux. 

Information om Schemalagda händelser i Windows finns [schemalagda händelser för virtuella Windows-datorer](../windows/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgänglig i alla Azure-regioner. Se [version och region tillgänglighet](#version-and-region-availability) för senaste versions information.

## <a name="why-use-scheduled-events"></a>Varför ska jag använda Schemalagda händelser?

Många program kan dra nytta av tid för att förbereda för underhåll av virtuella datorer. Tiden kan användas för att utföra programspecifika uppgifter som förbättrar tillgängligheten, tillförlitligheten och service möjligheterna, inklusive: 

- Kontroll punkt och återställning.
- Anslutningen töms.
- Redundansväxling av primär replik.
- Borttagning från en belastningsutjämnare.
- Händelse loggning.
- Korrekt avstängning.

Med Schemalagda händelser kan ditt program identifiera när underhåll sker och utlösa aktiviteter för att begränsa dess påverkan.  

Schemalagda händelser innehåller händelser i följande användnings fall:

- [Plattform initierat underhåll](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json) (till exempel VM-omstart, direktmigrering eller minnes konserverings uppdateringar för värd)
- Den virtuella datorn körs på en [degraderad värd maskin vara](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) som förväntas sluta att fungera snart
- Underhåll av användaren (till exempel när en användare startar om eller distribuerar om en virtuell dator)
- Borttagning av instans av [virtuella datorer](../spot-vms.md) och [dekor skalnings uppsättningar](../../virtual-machine-scale-sets/use-spot.md) .

## <a name="the-basics"></a>Grunderna  

  Metadata Service visar information om att köra virtuella datorer genom att använda en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en nonroutable-IP-adress så att den inte exponeras utanför den virtuella datorn.

### <a name="scope"></a>Omfång
Schemalagda händelser levereras till:

- Fristående Virtual Machines.
- Alla virtuella datorer i en moln tjänst.
- Alla virtuella datorer i en tillgänglighets uppsättning.
- Alla virtuella datorer i en tillgänglighets zon. 
- Alla virtuella datorer i en placerings grupp för skalnings uppsättningar. 

> [!NOTE]
> För virtuella datorer i en tillgänglighets zon går de schemalagda händelserna till enskilda virtuella datorer i en zon.
> Om du till exempel har 100 virtuella datorer i en tillgänglighets uppsättning och det finns en uppdatering av någon av dem, kommer den schemalagda händelsen att gå till alla 100, medan om det finns 100 enskilda virtuella datorer i en zon, kommer händelsen bara att gå till den virtuella datorn som påverkas.

Därför bör du kontrol lera `Resources` fältet i händelsen för att identifiera vilka virtuella datorer som påverkas.

### <a name="endpoint-discovery"></a>Slut punkts identifiering
För virtuella VNET-aktiverade virtuella datorer är Metadata Service tillgängliga från en statisk nonroutable-IP-adress `169.254.169.254` . Den fullständiga slut punkten för den senaste versionen av Schemalagda händelser är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Om den virtuella datorn inte har skapats inom en Virtual Network, krävs standard fall för moln tjänster och klassiska virtuella datorer, men ytterligare logik krävs för att identifiera IP-adressen som ska användas. Information om hur du [identifierar värd slut punkten](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)finns i det här exemplet.

### <a name="version-and-region-availability"></a>Tillgänglighet för version och region
Den Schemalagda händelser tjänsten har versions hantering. Versioner är obligatoriska. den aktuella versionen är `2019-08-01` .

| Version | Versions typ | Regioner | Viktig information | 
| - | - | - | - | 
| 2019-08-01 | Allmän tillgänglighet | Alla | <li> Stöd har lagts till för EventSource |
| 2019-04-01 | Allmän tillgänglighet | Alla | <li> Stöd har lagts till för händelse Beskrivning |
| 2019-01-01 | Allmän tillgänglighet | Alla | <li> Stöd har lagts till för den virtuella datorns skalnings uppsättning EventType ' Terminate ' |
| 2017-11-01 | Allmän tillgänglighet | Alla | <li> Stöd har lagts till för VM-utavlägsning av händelse-Preempt för VM<br> | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Tog bort anpassningsprefix-understreck från resurs namn för virtuella IaaS-datorer<br><li>Krav för metadata-huvud tillämpas för alla begär Anden | 
| 2017-03-01 | Förhandsgranskning | Alla | <li>Första utgåvan |


> [!NOTE] 
> Tidigare för hands versioner av Schemalagda händelser som stöds {senaste} som API-version. Det här formatet stöds inte längre och kommer att bli inaktuellt i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser aktive ras för din tjänst första gången du gör en begäran om händelser. Du bör förvänta dig ett fördröjt svar i ditt första anrop på upp till två minuter.

Schemalagda händelser har inaktiverats för tjänsten om den inte gör en begäran i 24 timmar.

### <a name="user-initiated-maintenance"></a>Användar initierat underhåll
Användarinitierad VM-underhåll via Azure Portal, API, CLI eller PowerShell resulterar i en schemalagd händelse. Du kan sedan testa hanterings logiken för underhåll i ditt program och ditt program kan förbereda för användarinitierad underhåll.

Om du startar om en virtuell dator är en händelse med typen `Reboot` schemalagd. Om du distribuerar om en virtuell dator är en händelse med typen `Redeploy` schemalagd.

## <a name="use-the-api"></a>Använda API:et

### <a name="headers"></a>Sidhuvuden
När du frågar Metadata Service måste du ange rubriken `Metadata:true` för att se till att begäran inte oavsiktligt omdirigeras. `Metadata:true`Rubriken krävs för alla begär Anden om schemalagda händelser. Om du inte tar med rubriken i begäran resulterar det i en "felaktig begäran"-svar från Metadata Service.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att för närvarande inga händelser är schemalagda.
Om det finns schemalagda händelser innehåller svaret en händelse mat ris. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Händelse egenskaper
|Egenskap  |  Beskrivning |
| - | - |
| EventId | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ | Påverkar den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn är schemalagd att pausas några sekunder. CPU-och nätverks anslutningen kan vara pausad, men det finns ingen inverkan på minnet eller öppna filer.<li>`Reboot`: Den virtuella datorn är schemalagd för omstart (icke-beständigt minne går förlorad). <li>`Redeploy`: Den virtuella datorn är schemalagd att flyttas till en annan nod (tillfälliga diskar går förlorade). <li>`Preempt`: Den virtuella datorn håller på att tas bort (tillfälliga diskar går förlorade). <li> `Terminate`: Den virtuella datorn är schemalagd att tas bort. |
| ResourceType | Typ av resurs som den här händelsen påverkar. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som den här händelsen påverkar. Listan är garanterat att innehålla datorer från högst en [uppdaterings domän](../manage-availability.md), men den innehåller kanske inte alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen är schemalagd att starta efter den tid som anges i `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> `Completed`Det finns aldrig någon eller liknande status. Händelsen returneras inte längre när händelsen är färdig.
| NotBefore| Tid när den här händelsen kan starta. <br><br> Exempel: <br><ul><li> Mån, 19 Sep 2016 18:29:47 GMT  |
| Beskrivning | Beskrivning av den här händelsen. <br><br> Exempel: <br><ul><li> Underhåll pågår för värd servern. |
| EventSource | Händelsens initierare. <br><br> Exempel: <br><ul><li> `Platform`: Den här händelsen initieras av plattformen. <li>`User`: Den här händelsen initieras av användaren. |

### <a name="event-scheduling"></a>Händelse schemaläggning
Varje händelse schemaläggs en minimi period i framtiden baserat på händelse typen. Den här tiden visas i en händelse `NotBefore` egenskap. 

|Typ  | Minsta meddelande |
| - | - |
| Tina| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |
| Utfärdas | 30 sekunder |
| Terminate | [Användaren kan konfigureras](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 till 15 minuter |

> [!NOTE] 
> I vissa fall kan Azure förutsäga värd felen på grund av försämrad maskin vara och försöka undvika avbrott i tjänsten genom att schemalägga en migrering. Berörda virtuella datorer får en schemalagd händelse med en `NotBefore` som vanligt vis är några dagar i framtiden. Den faktiska tiden varierar beroende på riskbedömningen för förutsägande problem. Azure försöker ge 7 dagars varsel när så är möjligt, men den faktiska tiden varierar och kan vara mindre om förutsägelsen är att det finns en hög chans att maskin varan slutar fungera på ett överhäng. För att minimera risken för din tjänst om maskin varan Miss lyckas innan den systeminitierade migreringen, rekommenderar vi att du distribuerar den virtuella datorn på egen plats så snart som möjligt.

### <a name="polling-frequency"></a>Avsöknings frekvens

Du kan avsöka slut punkten för uppdateringar så ofta eller sällan som du vill. Men den längre tiden mellan begär Anden, desto mer tid kan du förlora för att reagera på en kommande händelse. De flesta händelser har 5 till 15 minuters förvarning, men i vissa fall kan meddelande om förhands meddelande vara så lite som 30 sekunder. För att säkerställa att du har så mycket tid som möjligt för att vidta åtgärder, rekommenderar vi att du avsöker tjänsten en gång per sekund.

### <a name="start-an-event"></a>Starta en händelse 

När du har lärt dig en kommande händelse och slutfört din logik för en korrekt avstängning kan du godkänna den utestående händelsen genom att `POST` ringa till metadata service med `EventId` . Anropet anger till Azure att det kan förkorta den minsta meddelande tiden (om möjligt). 

Följande JSON-exempel förväntas i `POST` begär ande texten. Begäran bör innehålla en lista över `StartRequests` . Var `StartRequest` och en innehåller `EventId` för den händelse som du vill påskynda:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-exempel
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Genom att bekräfta en händelse kan händelsen fortsätta för alla `Resources` i händelsen, inte bara den virtuella dator som bekräftar händelsen. Därför kan du välja att välja en ledare för att samordna bekräftelsen, vilket kan vara lika enkelt som den första datorn i `Resources` fältet.

## <a name="python-sample"></a>Python-exempel 

Följande exempel frågar Metadata Service för schemalagda händelser och godkänner varje utestående händelse:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Nästa steg 
- Titta på [schemalagda händelser på Azure fredagen](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) för att se en demonstration. 
- Granska Schemalagda händelser kod exempel i [Azure instance Metadata schemalagda händelser GitHub-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Läs mer om de API: er som är tillgängliga i [instance metadata service](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för virtuella Linux-datorer i Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json).
