---
title: Azure Cloud Services (klassisk) Def. LoadBalancerProbe-schema | Microsoft Docs
description: Kundens definierade LoadBalancerProbe är en hälso avsökning av slut punkter i roll instanser. Den kombineras med Web-eller Worker-roller i en tjänst definitions fil.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1fbbb49918ed83bd38e0c1f879ed31386cfb756b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934129"
---
# <a name="azure-cloud-services-classic-definition-loadbalancerprobe-schema"></a>Azure Cloud Services (klassisk) definition LoadBalancerProbe-schema

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Belastnings Utjämnings avsökningen är en kunddefinierad hälso avsökning av UDP-slutpunkter och slut punkter i roll instanser. `LoadBalancerProbe`Är inte ett fristående element. det kombineras med webb rollen eller arbets rollen i en tjänst definitions fil. En `LoadBalancerProbe` kan användas av mer än en roll.

Standard tillägget för tjänst definitions filen är. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funktionen för en belastnings Utjämnings avsökning
Azure Load Balancer ansvarar för att dirigera inkommande trafik till roll instanserna. Belastningsutjämnaren avgör vilka instanser som kan ta emot trafik genom att regelbundet räkna igenom varje instans för att fastställa hälso tillståndet för den instansen. Belastnings Utjämnings avsökningar varje instans flera gånger per minut. Det finns två olika alternativ för att tillhandahålla instans hälsa för belastningsutjämnaren – standard avsöknings avsökningen eller en anpassad belastnings Utjämnings avsökning, som implementeras genom att definiera LoadBalancerProbe i. csdef-filen.

Standard belastnings Utjämnings avsökningen använder gäst agenten i den virtuella datorn, som avlyssnar och svarar med ett HTTP 200-svar på OK när instansen är i tillståndet Ready (t. ex. om instansen inte är upptagen, återvinning, stoppas osv.). Om gäst agenten inte svarar med HTTP 200 OK markerar Azure Load Balancer instansen som svarar inte och slutar att skicka trafik till den instansen. Azure Load Balancer fortsätter att pinga instansen och om gäst agenten svarar med en HTTP 200 skickar Azure Load Balancer trafik till den instansen igen. När du använder en webb roll körs din webbplats kod vanligt vis i w3wp.exe som inte övervakas av Azure-infrastrukturen eller gäst agenten, vilket innebär att det inte finns några problem i w3wp.exe (t. ex. HTTP 500-svar) rapporteras inte till gäst agenten och belastningsutjämnaren vet inte att den instansen kan ta slut.

Avsökningen av den anpassade belastningsutjämnaren åsidosätter standard agent avsökningen och gör att du kan skapa en egen anpassad logik för att fastställa hälso tillståndet för roll instansen. Belastningsutjämnaren avsöker regelbundet din slut punkt (var 15: e sekund som standard) och instansen betraktas som en rotation om den svarar med en TCP-ACK eller HTTP 200 inom tids gränsen (standard 31 sekunder). Det kan vara användbart att implementera din egen logik för att ta bort instanser från belastnings Utjämnings rotation, till exempel returnera en status som inte är 200 om instansen är över 90% CPU. För webb roller som använder w3wp.exe innebär detta också att du får automatisk övervakning av webbplatsen, eftersom felen i din webbplats kod returnerar en status som inte är 200 för belastningsutjämnaren. Om du inte definierar en LoadBalancerProbe i. csdef-filen används standard beteendet för belastningsutjämnaren (som tidigare beskrivits).

Om du använder en anpassad avsökning för belastningsutjämnare måste du se till att din logik tar hänsyn till metoden RoleEnvironment. OnStop. När du använder standard belastnings Utjämnings avsökningen tas instansen bort från rotationen innan OnStop anropas, men en anpassad belastnings Utjämnings avsökning kan fortsätta att returnera en 200 OK under händelsen OnStop. Om du använder OnStop-händelsen för att rensa cacheminnet, stoppa tjänsten eller på annat sätt göra ändringar som kan påverka programmets körnings funktion måste du kontrol lera att den anpassade belastnings Utjämnings logiken tar bort instansen från rotationen.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Grundläggande tjänst definitions schema för en belastnings Utjämnings avsökning
 Det grundläggande formatet för en tjänst definitions fil som innehåller en belastnings Utjämnings avsökning är följande.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema element
`LoadBalancerProbes`Elementet i tjänst definitions filen innehåller följande element:

- [LoadBalancerProbes-element](#LoadBalancerProbes)
- [LoadBalancerProbe-element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> LoadBalancerProbes-element
`LoadBalancerProbes`Elementet beskriver insamlingen av belastnings Utjämnings avsökningar. Det här elementet är det överordnade elementet i [LoadBalancerProbe-elementet](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> LoadBalancerProbe-element
`LoadBalancerProbe`Elementet definierar hälso avsökningen för en modell. Du kan definiera flera belastnings Utjämnings avsökningar. 

I följande tabell beskrivs attributen för- `LoadBalancerProbe` elementet:

|Attribut|Typ|Beskrivning|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Krävs. Namnet på belastnings Utjämnings avsökningen. Namnet måste vara unikt.|
| `protocol`          | `string` | Krävs. Anger protokollet för slut punkten. Möjliga värden är `http` eller `tcp`. Om `tcp` har angetts krävs en mottagen ack för att avsökningen ska lyckas. Om `http` har angetts krävs ett 200 OK-svar från den angivna URI: n för att avsökningen ska lyckas.|
| `path`              | `string` | Den URI som används för att begära hälso status från den virtuella datorn. `path` krävs om `protocol` är inställt på `http` . Annars är det inte tillåtet.<br /><br /> Det finns inget standardvärde.|
| `port`              | `integer` | Valfritt. Porten för att kommunicera avsökningen. Detta är valfritt för alla slut punkter, eftersom samma port kommer att användas för avsökningen. Du kan också konfigurera en annan port för deras avsökning. Möjliga värden är mellan 1 och 65535.<br /><br /> Standardvärdet anges av slut punkten.|
| `intervalInSeconds` | `integer` | Valfritt. Intervallet, i sekunder, för hur ofta slut punkten för hälso status ska avsökas. Normalt är intervallet mindre än hälften av den tilldelade tids gränsen (i sekunder) som tillåter två fullständiga avsökningar innan instansen tas ur bruk.<br /><br /> Standardvärdet är 15, minimivärdet är 5.|
| `timeoutInSeconds`  | `integer` | Valfritt. Tids gränsen, i sekunder, som tillämpas på avsökningen där inget svar leder till att ytterligare trafik inte levereras till slut punkten. Med det här värdet kan slut punkter tas bort från rotationen snabbare eller långsammare än de vanligaste tiderna som används i Azure (som är standardvärdena).<br /><br /> Standardvärdet är 31, minimivärdet är 11.|

## <a name="see-also"></a>Se även
[Definitions schema för moln tjänst (klassisk)](schema-csdef-file.md)