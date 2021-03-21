---
title: Introduktion till resurs modellen i Azure Service Fabric
description: Lär dig mer om Service Fabric resurs modell, en förenklad metod för att definiera Service Fabric nätprogram.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626917"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduktion till Service Fabric Resource Model

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

I Service Fabric resurs modellen beskrivs en enkel metod för att definiera resurser som utgör ett Service Fabric nätprogram. Enskilda resurser kan distribueras till alla Service Fabrics miljöer.  Service Fabric resurs modellen är också kompatibel med Azure Resource Manager-modellen. Följande typer av resurser stöds för närvarande i den här modellen:

- Program och tjänster
- Nätverk
- Gateways
- Hemligheter och hemligheter/värden
- Volymer

Varje resurs beskrivs i deklarativt i en resurs fil, vilket är ett enkelt YAML-eller JSON-dokument som beskriver nätappen och som tillhandahålls av Service Fabrics plattformen.

## <a name="applications-and-services"></a>Program och tjänster

En program resurs är enhets distribution, versions hantering och livs längd för ett nät program. Den består av en eller flera tjänst resurser som representerar en mikrotjänst. Varje tjänst resurs, i sin tur, består av ett eller flera kod paket som beskriver allt som behövs för att köra behållar avbildningen som är associerad med kod paketet.

![Appar och tjänster][Image1]

En tjänst resurs deklarerar följande:

- Behållarens namn, version och register
- PROCESSOR-och minnes resurser som krävs för varje behållare
- Nätverks slut punkter
- Referenser till andra resurser, till exempel nätverk, volymer och hemligheter 

Alla kod paket som definieras som en del av en tjänst resurs distribueras och aktive ras tillsammans som en grupp. Tjänst resursen beskriver också hur många instanser av tjänsten som ska köras och refererar även till andra resurser (t. ex. nätverks resurser) som den är beroende av.

Om ett nätprogram består av mer än en tjänst, är de inte garanterade att de körs tillsammans på samma nod. Vid en uppgradering av programmet resulterar det dessutom i att alla tjänster återställs till tidigare versioner vid en uppgradering av en enskild tjänst.

Som alluded tidigare kan livs cykeln för varje program instans hanteras separat. Till exempel kan en program instans uppgraderas oberoende av de andra program instanserna. Normalt behåller du antalet tjänster i ett program på ett ganska litet sätt, som de fler tjänsterna du använder i ett program, desto svårare blir det att hantera varje tjänst oberoende av varandra.

## <a name="networks"></a>Nätverk

Nätverks resursen är en individuellt distribuerad resurs, oberoende av ett program eller en tjänst resurs som kan referera till den som ett beroende. Den används för att skapa ett nätverk för dina program. Flera tjänster från olika program kan vara en del av samma nätverk.  Mer information finns i avsnittet om [nätverk i Service Fabric nätappar](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Den aktuella för hands versionen stöder bara en till en mappning mellan program och nätverk

![Nätverk och gateway][Image2]

## <a name="gateways"></a>Gateways
En gateway-resurs ansluter två nätverk och dirigerar trafik.  En gateway gör att tjänsterna kan kommunicera med externa klienter och ger en ingång i dina tjänster.  En gateway kan också användas för att ansluta ditt nätprogram till ditt eget, befintliga virtuella nätverk. Mer information finns i avsnittet om [nätverk i Service Fabric nätappar](service-fabric-mesh-networks-and-gateways.md).

![Nätverk och gateway][Image2]

## <a name="secrets"></a>Hemligheter

Hemligheter-resurser kan distribueras oberoende av ett program eller en tjänst resurs som kan referera till den som ett beroende. Den används för att på ett säkert sätt leverera hemligheter till dina program. Flera tjänster från olika program kan referera till värden av samma hemlighet.

## <a name="volumes"></a>Volymer

Behållare gör ofta temporära diskar tillgängliga. Temporära diskar är tillfälliga, så du får en ny temporär disk och förlorar informationen när en behållare kraschar. Det är också svårt att dela information på temporära diskar med andra behållare. Volymer är kataloger som monteras i dina behållar instanser som du kan använda för att spara tillstånd. -Volymer ger dig generell fil lagring och gör att du kan läsa och skriva filer med hjälp av normal disk-I/O-fil-API: er. Volym resursen är ett deklarativ sätt att beskriva hur en katalog monteras och lagrings utrymmet för den (antingen Azure Files volym eller Service Fabric tillförlitlig volym).  Mer information finns i avsnittet om att [lagra tillstånd](service-fabric-mesh-storing-state.md#volumes).

![Diagram visar en tjänst som flödar till en disk volym, som flödar till både Service Fabric tillförlitlig volym, sedan till en replikerad lokal disk och till Azure Files volym, sedan till nätverks lagring.][Image3]

## <a name="programming-models"></a>Programmeringsmodeller
Tjänst resursen kräver bara en behållar avbildning att köras, som refereras till i de kod paket som är associerade med resursen. Du kan köra valfri kod, skrivet på valfritt språk, med hjälp av eventuella ramverk inuti behållaren utan att behöva känna till eller använda Service Fabric maskor specifika API: er. 

Din program kod förblir portabel även utanför Service Fabric-nät och dina program distributioner är konsekventa oavsett vilket språk eller ramverk som används för att implementera dina tjänster. Om ditt program är ASP.NET Core, går eller bara en uppsättning processer och skript, är distributions modellen för Service Fabric-nätresursen densamma. 

## <a name="packaging-and-deployment"></a>Paketering och distribution

Service Fabric nät program som baseras på resurs modellen paketeras som Docker-behållare.  Service Fabric nät är en delad miljö med flera klienter och behållare ger dig en hög isolerings nivå.  Dessa program beskrivs med hjälp av ett JSON-format eller ett YAML-format (som sedan konverteras till JSON). När du distribuerar ett nätprogram till Azure Service Fabric nät är JSON som används för att beskriva programmet en Azure Resource Manager mall. Resurser mappas till Azure-resurser.  När du distribuerar ett nätprogram till ett Service Fabric kluster (fristående eller Azure-värdbaserade), är JSON som används för att beskriva programmet ett format som liknar en Azure Resource Manager mall.  När du har distribuerat nätappar kan de hanteras via HTTP-gränssnitt eller Azure CLI. 


## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
