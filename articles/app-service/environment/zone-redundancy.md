---
title: Tillgänglighets zon stöd för App Service miljöer
description: Lär dig hur du distribuerar dina App Service miljöer så att dina appar är zoner-redundanta.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624733"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Tillgänglighets zon stöd för App Service miljöer

App Service miljöer (ASE) kan distribueras till Tillgänglighetszoner (AZ).  Kunder kan distribuera en intern belastningsutjämnare (ILB) ASE till en speciell AZ i en Azure-region. Om du fäster ILB-ASE på en specifik AZ fästs de resurser som används av en ILB-ASE på den angivna AZ eller distribueras i en redundant zon.  

En ILB-ASE som uttryckligen distribueras till en AZ betraktas som en zonindelade-resurs eftersom ILB-ASE fästs i en specifik zon. Följande ILB ASE-beroenden kommer att fästas i den angivna zonen:

- den interna belastnings Utjämnings-IP-adressen för ASE
- de beräknings resurser som används av ASE för att hantera och köra webb program

Fjärrlagringsplatsen för webb program som distribueras på en zonindelade ILB-ASE använder Zone-redundant lagring (ZRS).

Om inte stegen som beskrivs i den här artikeln följs, distribueras ILB ASE inte automatiskt på zonindelade sätt. Du kan inte fästa en extern ASE med en offentlig IP-adress till en speciell tillgänglighets zon. 

Zonindelade ILB ASE kan skapas i någon av följande regioner:

- Australien, östra
- Kanada, centrala
- Central US
- East US
- USA, östra 2
- USA, östra 2 (EUAP)
- Frankrike, centrala 
- Japan, östra
- Europa, norra
- Europa, västra
- Sydostasien
- Storbritannien, södra
- USA, västra 2

Program som distribueras på en zonindelade ILB-ASE fortsätter att köra och betjäna trafik på ASE även om andra zoner i samma region drabbas av ett avbrott.  Det är möjligt att icke-körnings beteenden, inklusive; skalning av program tjänst, program skapande, program konfiguration och program publicering kan fortfarande påverkas från ett avbrott i andra tillgänglighets zoner. Den zon-fästa distributionen av en zonindelade ILB-ASE garanterar bara fortsatt drift tid för redan distribuerade program.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Så här distribuerar du en App Service-miljön i en tillgänglighets zon ##

Zonindelade ILB-ASE måste skapas med ARM-mallar. När en zonindelade ILB-ASE har skapats via en ARM-mall kan den visas och interagera med via Azure Portal och CLI.  En ARM-mall behövs bara för den första skapandet av en zonindelade ILB-ASE.

Den enda förändring som krävs i en ARM-mall för att ange en zonindelade ILB-ASE är den nya ***Zones _ Zones** _-egenskapen. Egenskapen _ *_Zones_** måste anges till värdet "1", "2" eller "3" beroende på den logiska tillgänglighets zon som ILB-ASE ska fästas på.

I exempel ARM-kodfragmentet nedan visas egenskapen ny ***zon*** som anger att ILB-ASE ska fästas i zon 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Om du vill göra din app-zon redundant måste du distribuera två zonindelade ILB-ASE. De två zonindelade ILB-ASE måste vara i olika tillgänglighets zoner. Du måste sedan distribuera dina appar till var och en av ILB-ASE. När dina appar har skapats måste du konfigurera en belastnings Utjämnings lösning. Den rekommenderade lösningen är att distribuera en [zon redundant Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) överordnade för zonindelade ILB-ASE. 

## <a name="in-region-data-residency"></a>I region data placering ##

ILB ASE som distribueras i en tillgänglighets zon lagrar bara kund information inom den region där zonindelade ILB ASE har distribuerats. Både webbplats filens innehåll och de inställningar för kunder som anges och hemligheter som lagras i App Service kvarstår i den region där zonindelade ILB-ASE har distribuerats.

Kunderna ser till att en enskild regions data placerings genom att följa stegen som beskrivs tidigare i avsnittet "så här distribuerar du en App Service-miljön i en tillgänglighets zon". Genom att konfigurera en App Service-miljön enligt dessa steg, uppfyller en App Service-miljön som distribueras i en tillgänglighets zon i placering krav för region data, inklusive de som anges i [Azure Säkerhetscenter](https://azuredatacentermap.azurewebsites.net/).

Kunder kan verifiera att en App Service-miljön är korrekt konfigurerad för att lagra data i en enda region genom att följa dessa steg: 

1. Använd [Resursläsaren](https://resources.azure.com)och navigera till arm-resursen för App Service-miljön.  ASE visas under *providers/Microsoft. Web/hostingEnvironments*.
2. Om det finns en *zon* egenskap i vyn av arm JSON-syntaxen och den innehåller en enda värdefull JSON-matris med värdet "1", "2" eller "3", är ASE zonally distribuerad och kund information finns kvar i samma region.
2. Om det inte finns någon *zon* egenskap, eller om egenskapen inte har något giltigt zonnamn som tidigare har angetts, så är ASE inte zonally distribuerad och kund information lagras inte exklusivt i samma region.
