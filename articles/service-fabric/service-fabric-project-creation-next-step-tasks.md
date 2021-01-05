---
title: Nästa steg för att skapa Service Fabric projekt
description: Lär dig mer om programprojektet som du nyss skapade i Visual Studio.  Lär dig hur du skapar tjänster med hjälp av självstudier och lär dig mer om att utveckla tjänster för Service Fabric.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760443"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Ditt Service Fabric program och nästa steg
Ditt Azure Service Fabric-program har skapats. Den här artikeln innehåller ett antal resurser, viss mer information som du kanske är intresse rad av och potentiella [Nästa steg](#next-steps).

Nya användare kan hitta [självstudier, genom gångar och exempel på](#get-started-with-tutorials-walk-throughs-and-samples) användbara. Det kan också vara användbart att undersöka [strukturen för det skapade applikations projektet](#the-application-project). Här ingår också beskrivningar av Service Fabric [programmerings modeller](#learn-more-about-the-programming-models), [tjänst kommunikation](#learn-about-service-communication), [program säkerhet](#learn-about-configuring-application-security)och [program livs cykel](#learn-about-the-application-lifecycle).

Mer erfarna användare kan hitta avsnittet Service Fabric [bästa praxis](#learn-about-best-practices) som är användbart för att lära dig hur du kan dra nytta av plattforms-och struktur program med maximal effektivitet.

Om du har frågor eller feedback, eller om du vill rapportera ett problem, kan du läsa [motsvarande avsnitt](#have-questions-or-feedback--need-to-report-an-issue).

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Kom igång med självstudier, anvisningar och exempel
Är du redo att sätta igång?  

Arbeta i självstudien om .NET-program. Lär dig hur du [skapar en app](service-fabric-tutorial-create-dotnet-app.md) med en ASP.net Core frontend och en tillstånds känslig Server del, [distribuerar programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md) till ett kluster, [konfigurerar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)och konfigurerar [övervakning och diagnostik](service-fabric-tutorial-monitoring-aspnet.md).

Alternativt kan du prova någon av följande steg-för-steg-anvisningar och skapa din första...
- [C# Reliable Services-tjänsten i Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors-tjänsten i Windows](service-fabric-reliable-actors-get-started.md) 
- [Körbar gäst tjänst i Windows](quickstart-guest-app.md) 
- [Windows-containerprogrammet](service-fabric-get-started-containers.md) 

Du kanske också är intresse rad av att prova våra [exempel program](/samples/browse/?products=azure).

## <a name="the-application-project"></a>Programprojektet
Varje nytt program innehåller ett program projekt. Det kan finnas ett eller två ytterligare projekt, beroende på vilken typ av tjänst som valts.

Programprojektet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publicerings profiler (lokal, 5-nods lokal och moln) som du kan använda för att hantera inställningar för att arbeta med olika miljöer, till exempel inställningar som är relaterade till en kluster slut punkt och om du vill utföra uppgraderings distributioner som standard.
* Tre program parameter-filer (samma som ovan) som du kan använda för att underhålla programkonfigurationer som är specifika för miljön, till exempel antalet partitioner som ska skapas för en tjänst. Lär dig hur du [konfigurerar ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).
* Ett distributions skript som du kan använda för att distribuera programmet från kommando raden eller som en del av en automatiserad pipeline för kontinuerlig integrering och distribution. Lär dig mer om att [distribuera program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md).
* Applikations manifestet som beskriver programmet. Du hittar manifestet under mappen ApplicationPackageRoot Läs mer om [program-och tjänst manifest](service-fabric-application-model.md).

## <a name="learn-more-about-the-programming-models"></a>Lär dig mer om programmerings modeller
Service Fabric erbjuder flera olika sätt att skriva och hantera dina tjänster.  Här är en översikt och konceptuell information om [tillstånds lösa och tillstånds känsliga Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [behållare](service-fabric-containers-overview.md), [körbara gäst program](service-fabric-guest-executables-introduction.md)och [tillstånds lösa och tillstånds känsliga ASP.net Core tjänster](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Läs mer om tjänst kommunikation
Ett Service Fabric program består av olika tjänster, där varje tjänst utför en specialiserad uppgift. Dessa tjänster kan kommunicera med varandra och det kan finnas klient program utanför klustret som ansluter till och kommunicerar med tjänster. Lär dig hur du [konfigurerar kommunikation med och mellan dina tjänster](service-fabric-connect-and-communicate-with-services.md) i Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Läs om hur du konfigurerar program säkerhet
Du kan skydda program som körs i klustret under olika användar konton. Service Fabric skyddar också resurserna som används av program vid tidpunkten för distributionen under användar konton, till exempel filer, kataloger och certifikat. Detta gör att program körs, även i en delad värd miljö, säkrare från varandra.  Lär dig hur du [konfigurerar säkerhets principer för ditt program](service-fabric-application-runas-security.md).

Ditt program kan innehålla känslig information, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text. Lär dig hur du [hanterar hemligheter i ditt program](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Läs om programmets livs cykel
Precis som med andra plattformar går ett Service Fabric-program vanligt vis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. [Den här artikeln](service-fabric-application-lifecycle.md) innehåller en översikt över API: erna och hur de används av de olika rollerna under faserna i Service Fabric programmets livs cykel.

## <a name="learn-about-best-practices"></a>Lär dig mer om metod tips
Service Fabric innehåller ett antal artiklar som beskriver [bästa praxis](./service-fabric-best-practices-overview.md). Dra nytta av den här informationen för att säkerställa att ditt kluster och program körs och möjligt.
De avsnitt som omfattas är:
* [Säkerhet](./service-fabric-best-practices-security.md)
* [Nätverk](./service-fabric-best-practices-networking.md)
* [Beräkningsplanering och skalning](./service-fabric-best-practices-capacity-scaling.md)
* [Infrastruktur som kod](./service-fabric-best-practices-infrastructure-as-code.md)
* [Övervakning och diagnostik](./service-fabric-best-practices-monitoring.md)
* [Programdesign](./service-fabric-best-practices-applications.md)

Dessutom ingår en [Check lista för produktions beredskap](./service-fabric-production-readiness-checklist.md) som integrerar all bästa praxis i ett lättanvänt format.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Har du några frågor eller feedback?  Behöver du rapportera ett problem?
Läs igenom [vanliga frågor](service-fabric-common-questions.md) och få svar på vad Service Fabric kan göra och hur det ska användas.

[Fel söknings guider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) kan vara användbara för att hjälpa till att diagnostisera och lösa vanliga problem i Service Fabric kluster.

[Support alternativ](service-fabric-support.md) listar forum på STACKOVERFLOW och MSDN för att ställa frågor, samt alternativ för rapportering av problem, få support och skicka feedback från produkter.


## <a name="next-steps"></a>Nästa steg
- [Skapa ett Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualisera klustret, inklusive distribuerade program och fysisk layout, med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Version och uppgradera dina tjänster](service-fabric-application-upgrade-tutorial.md)