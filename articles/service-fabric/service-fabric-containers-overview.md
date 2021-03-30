---
title: Översikt över Service Fabric och behållare
description: En översikt över Service Fabric och användning av behållare för att distribuera program i mikrotjänster. Den här artikeln innehåller en översikt över hur behållare kan användas och de tillgängliga funktionerna i Service Fabric.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87091665"
---
# <a name="service-fabric-and-containers"></a>Service Fabric och behållare

## <a name="introduction"></a>Introduktion

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och containrar.

Service Fabric är Microsofts [behållar Orchestrator](service-fabric-cluster-resource-manager-introduction.md) för att distribuera mikrotjänster i ett kluster med datorer. Service Fabric fördelarna med de erfarenheter som har inlärts under de år som kör tjänster på Microsoft i enorm skala.

Mikrotjänster kan utvecklas på många sätt, t.ex. genom att använda [programmeringsmodeller för Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) eller distribuera [valfri kod](service-fabric-guest-executables-introduction.md). Eller, om du bara vill [distribuera och hantera behållare](service-fabric-containers-overview.md), är Service Fabric också ett bra alternativ.

Som standard distribuerar Service Fabric och aktiverar dessa tjänster som processer. Processer ger snabbast aktivering och högsta densitets användning av resurserna i ett kluster. Service Fabric kan även distribuera tjänster i behållar avbildningar. Du kan också blanda tjänster i processer och tjänster i behållare i samma program.

Om du vill gå direkt till och prova behållare på Service Fabric kan du prova med en snabb start, självstudier eller exempel:  

[Snabb start: Distribuera ett Linux container-program till Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Snabb start: Distribuera ett Windows-behållar program till Service Fabric](service-fabric-quickstart-containers.md)  
[Använd en befintlig .NET-app i container](service-fabric-host-app-in-a-container.md)  
[Exempel på container för Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Vad är behållare

Behållare löser problemet med att köra program på ett tillförlitligt sätt i olika data behandlings miljöer genom att tillhandahålla en miljö som gör att programmet kan köras i. Behållare omsluter ett program och alla dess beroenden, till exempel bibliotek och konfigurationsfiler, till en egen isolerad ruta som innehåller allt som behövs för att köra program varan inuti behållaren. Oavsett var behållaren körs, har programmet inuti det alltid allt det behöver för att kunna köras, till exempel rätt versioner av dess beroende bibliotek, konfigurationsfiler och allt annat som krävs för att köra.

Behållare körs direkt ovanpå kerneln och har en isolerad vy av fil systemet och andra resurser. Ett program i en behållare har ingen kunskap om andra program eller processer utanför dess behållare. Varje program och dess körning, beroenden och system bibliotek körs inuti en behållare med fullständig, privat åtkomst till behållarens egna isolerade vy av operativ systemet. Förutom att göra det enkelt att tillhandahålla alla dina programs beroenden som krävs för att köra i olika dator miljöer, är säkerhet och resurs isolering viktiga fördelar med att använda behållare med Service Fabric – som annars kör tjänster i en process.

Jämfört med virtuella datorer har behållare följande fördelar:

* **Liten**: behållare använder ett enda lagrings utrymme och lager versioner och uppdateringar för att öka effektiviteten.
* **Snabbt**: behållare behöver inte starta ett helt operativ system, så de kan starta mycket snabbare, vanligt vis på några sekunder.
* **Portabilitet**: en program avbildning med behållare kan hamnas för att köras i molnet, lokalt, i virtuella datorer eller direkt på fysiska datorer.
* **Resurs styrning**: en behållare kan begränsa de fysiska resurser som den kan använda på värden.

### <a name="container-types-and-supported-environments"></a>Behållar typer och miljöer som stöds

Service Fabric stöder behållare i både Linux och Windows och stöder isolerings läget för Hyper-V i Windows.

#### <a name="docker-containers-on-linux"></a>Docker-behållare på Linux

Docker innehåller API: er för att skapa och hantera behållare ovanpå Linux-kernel-behållare. Docker Hub är en central lagrings plats för att lagra och hämta behållar avbildningar.
En Linux-baserad självstudie finns i [skapa ditt första Service Fabric behållar program i Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server-containrar

Windows Server 2016 och senare innehåller två olika typer av behållare som skiljer sig mellan olika isolerings nivåer. Windows Server-behållare och Docker-behållare liknar varandra eftersom båda har ett namn område och fil system isolering, samtidigt som kärnan delas med värden som de körs på. På Linux har denna isolering traditionellt tillhandahållits av cgroups och namnrum och Windows Server-behållare fungerar på samma sätt.

Windows-behållare med stöd för Hyper-V ger mer isolering och säkerhet eftersom ingen container delar operativ systemets kernel med någon annan behållare eller med värden. Med den här högre säkerhets isolerings nivån är Hyper-V-aktiverade behållare riktade till potentiellt skadliga, flera klient scenarier.
En Windows-baserad själv studie kurs finns i [skapa ditt första Service Fabric behållar program i Windows](service-fabric-get-started-containers.md).

Följande bild visar de olika typerna av tillgängliga virtualiseringslösningar och isolerings nivåer.
![Service Fabric-plattform][Image1]

## <a name="scenarios-for-using-containers"></a>Scenarier för att använda behållare

Här är typiska exempel där en behållare är ett bra val:

* **IIS-lyft och Shift**: du kan använda en befintlig [ASP.NET MVC](https://www.asp.net/mvc) -app i en behållare i stället för att migrera den till ASP.net Core. Dessa ASP.NET MVC-appar är beroende av Internet Information Services (IIS). Du kan paketera dessa program i behållar avbildningar från den förskapade IIS-avbildningen och distribuera dem med Service Fabric. Se [behållar avbildningar på Windows Server](/virtualization/windowscontainers/quick-start/quick-start-windows-server) för information om Windows-behållare.

* **Blanda behållare och Service Fabric mikrotjänster**: Använd en befintlig behållar avbildning för en del av ditt program. Du kan till exempel använda nginx- [behållaren](https://hub.docker.com/_/nginx/) för webb klient delen av ditt program och tillstånds känsliga tjänster för att få en mer intensiv beräkning på Server sidan.

* **Minska effekten av "störningar på grannar"-tjänster**: du kan använda resurs styrnings kapaciteten för behållare för att begränsa vilka resurser som en tjänst använder på en värd. Om tjänsterna kan förbruka många resurser och påverka prestanda för andra (till exempel en långvarig, fråga-liknande åtgärd) bör du överväga att placera dessa tjänster i behållare som har resurs styrning.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-stöd för containrar

Service Fabric stöder distribution av Docker-behållare på Linux och Windows Server-behållare på Windows Server 2016 och senare, tillsammans med stöd för isolerings läget för Hyper-V.

> [!NOTE]
> Behållare stöds inte på lokala Service Fabric-klusternoder (inga Linux-kluster på Onebox behållaravbildningen, eller Windows-kluster på lokala Service Fabric installationer).

Service Fabric tillhandahåller en [program modell](service-fabric-application-model.md) där en behållare representerar en program värd där flera tjänst repliker placeras. Service Fabric stöder också ett [scenario för körbara filer](service-fabric-guest-executables-introduction.md) där du inte använder de inbyggda Service Fabric programmerings modeller, men i stället paketera ett befintligt program, skrivet med valfritt språk eller ramverk, inuti en behållare. Det här scenariot är vanliga användnings fall för behållare.

Du kan också köra [Service Fabric tjänster i en behållare](service-fabric-services-inside-containers.md). Stöd för att köra Service Fabric tjänster i behållare är för närvarande begränsat.

Service Fabric innehåller flera behållar funktioner som hjälper dig att bygga program som består av mikrotjänster i behållare, till exempel:

* Distribution och aktivering av container avbildning.
* Resurs styrning, inklusive inställning av resurs värden som standard i Azure-kluster.
* Autentisering av databas.
* Container port som värd för port mappning.
* Identifiering och kommunikation från behållare till behållare.
* Möjlighet att konfigurera och ange miljövariabler.
* Möjlighet att ange säkerhets referenser för behållaren.
* Ett urval av olika nätverks lägen för behållare.

En omfattande översikt över container support på Azure, till exempel hur du skapar ett Kubernetes-kluster med Azure Kubernetes service, hur du skapar ett privat Docker-register i Azure Container Registry och mer finns i [Azure for containers](../containers/index.yml).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Support Service Fabric tillhandahåller för att köra behållare. Nu ska vi gå igenom exempel på var och en av funktionerna för att visa hur du använder dem.

[Skapa din första Service Fabric-containerapp i Linux](service-fabric-get-started-containers-linux.md)  
[Skapa din första Service Fabric-containerapp i Windows](service-fabric-get-started-containers.md)  
[Läs mer om Windows-behållare](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
