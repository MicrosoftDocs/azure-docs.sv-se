---
title: Visualisera klustret med Azure Service Fabric Explorer
description: Service Fabric Explorer är ett program för att inspektera och hantera moln program och noder i ett Microsoft Azure Service Fabric kluster.
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a45aff305f97610cb2660c2e3f4b4427b905d7d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574063"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisera ditt kluster med Service Fabric Explorer

Service Fabric Explorer (SFX) är ett verktyg med öppen källkod för att inspektera och hantera Azure Service Fabric-kluster. Service Fabric Explorer är ett Skriv bords program för Windows, macOS och Linux.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer Ladda ned

Använd följande länkar för att ladda ned Service Fabric Explorer som ett Skriv bords program:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Skriv bords versionen av Service Fabric Explorer kan ha mer eller färre funktioner än kluster stödet. Du kan återgå till den Service Fabric Explorer-version som distribuerats till klustret för att säkerställa fullständig kompatibilitet.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Köra Service Fabric Explorer från klustret

Service Fabric Explorer finns också i ett Service Fabric klustrets HTTP Management-slutpunkt. För att starta SFX i en webbläsare, bläddra till klustrets HTTP-hanterings slut punkt från valfri webbläsare – till exempel https: \/ /clusterFQDN: 19080.

Du kan starta Service Fabric Explorer på ditt lokala kluster genom att gå till för installation av arbets station för utvecklare https://localhost:19080/Explorer . Titta på den här artikeln för att [förbereda din utvecklings miljö](service-fabric-get-started.md).

> [!NOTE]
> Om klustret skyddas av ett självsignerat certifikat visas ett fel meddelande från webbläsaren "den här webbplatsen är inte säker". Du kan helt enkelt gå igenom de flesta moderna webbläsare genom att åsidosätta varningen. I en produktions miljö bör klustret skyddas med ett eget namn och ett certifikat som utfärdats av en certifikat utfärdare. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Ansluta till ett Service Fabric-kluster
Om du vill ansluta till ett Service Fabric-kluster måste du ha slut punkten för kluster hantering (FQDN/IP) och HTTP-hanteringens slut punkts port (19080 som standard). Till exempel https \: //mysfcluster.westus.cloudapp.Azure.com:19080. Använd kryss rutan Anslut till localhost för att ansluta till ett lokalt kluster på din arbets Station.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
Du kan styra klient åtkomsten till ditt Service Fabric-kluster antingen med certifikat eller med Azure Active Directory (AAD).

Om du försöker ansluta till ett säkert kluster måste du, beroende på klustrets konfiguration, Visa ett klient certifikat eller logga in med AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Förstå Service Fabric Explorer layout
Du kan navigera genom Service Fabric Explorer med hjälp av trädet till vänster. I träd strukturen ger kluster instrument panelen en översikt över klustret, inklusive en sammanfattning av program-och nodens hälsa.

![Service Fabric Explorer kluster instrument panel][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Visa klustrets layout
Noder i ett Service Fabric kluster placeras i ett tvådimensionellt rutnät av fel domäner och uppgraderings domäner. Den här placeringen säkerställer att dina program är tillgängliga i närvaro av maskin varu problem och program uppgraderingar. Du kan se hur det aktuella klustret fastställs med hjälp av kluster kartan.

![Service Fabric Explorer kluster mappning][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Visa program och tjänster
Klustret innehåller två under träd: ett för program och ett annat för noder.

Du kan använda vyn program för att navigera genom Service Fabricens logiska hierarki: program, tjänster, partitioner och repliker.

I exemplet nedan består programmet **MyApp** av två tjänster, **MyStatefulService** och **WebService**. Eftersom **MyStatefulService** är tillstånds känsligt, innehåller den en partition med en primär och två sekundära repliker. Däremot är WebSvcService tillstånds lös och innehåller en enda instans.

![Service Fabric Explorer programvy][sfx-application-tree]

På varje nivå i trädet visar huvud fönstret relevant information om objektet. Du kan till exempel se hälso status och version för en viss tjänst.

![Fönstret Service Fabric Explorer Essentials][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Visa klustrets noder
Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden. Mer specifikt kan du se vilka repliker som körs där.

## <a name="actions"></a>Åtgärder
Service Fabric Explorer erbjuder ett snabbt sätt att anropa åtgärder på noder, program och tjänster i klustret.

Om du till exempel vill ta bort en program instans väljer du programmet i trädet till vänster och väljer sedan **åtgärder**  >  **ta bort program**.

![Ta bort ett program i Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Du kan utföra samma åtgärder genom att klicka på ellipsen bredvid varje element.
>
> Varje åtgärd som kan utföras via Service Fabric Explorer kan också utföras via PowerShell eller en REST API för att aktivera automatisering.
>
>

Du kan också använda Service Fabric Explorer för att skapa program instanser för en specifik program typ och version. Välj program typ i trädvyn och klicka sedan på länken **Skapa App-instans** bredvid den version som du vill ha i den högra rutan.

![Skapa en program instans i Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer stöder inte parametrar när program instanser skapas. Program instanser använder standard parameter värden.
>
>

## <a name="event-store"></a>Händelse lager
EventStore är en funktion som erbjuds av plattformen som tillhandahåller Service Fabric plattforms händelser som är tillgängliga i Service Fabric Explorer och via REST API. Du kan se en Snapshot-vy över vad som händer i ditt kluster för varje entitet, t. ex. Node, service, program och fråga baserat på händelsens tidpunkt. Du kan också läsa mer om EventStore i EventStore- [översikten](service-fabric-diagnostics-eventstore.md).   

![Skärm bild som visar fönstret noder där händelser har valts.][sfx-eventstore]

>[!NOTE]
>Från och med Service Fabric version 6,4. EventStore är inte aktiverat som standard och måste aktive ras i Resource Manager-mallen

>[!NOTE]
>Från och med Service Fabric version 6,4. EventStore-API: er är bara tillgängliga för Windows-kluster som körs på Azure. Vi arbetar på att hamna på den här funktionaliteten både i Linux och i våra fristående kluster.

## <a name="image-store-viewer"></a>Avbildningsarkiv Viewer
Image Store Viewer är en funktion som erbjuds om du använder interna Avbildningsarkiv som gör det möjligt att visa det aktuella innehållet i avbildnings arkivet och Hämta fil-och resursinformation, tillsammans med att ta bort filer/mappar.

![Skärm bild som visar Avbildningsarkiv Viewer.][sfx-imagestore]

## <a name="backup-and-restore"></a>Säkerhetskopiera och återställ
Service Fabric Explorer ger möjlighet att gränssnitt för [säkerhets kopiering och återställning](./service-fabric-reliable-services-backup-restore.md). För att kunna se säkerhets kopierings-och återställnings funktioner i SFX måste du aktivera avancerat läge.

![Aktivera avancerat läge][0]
 
Följande åtgärder är möjliga:

* Skapa, redigera och ta bort en säkerhets kopierings princip.
* Aktivera och inaktivera säkerhets kopiering för ett program, en tjänst eller en partition.
* Pausa och återuppta säkerhets kopiering för ett program, en tjänst eller en partition.
* Utlös och spåra säkerhets kopiering av en partition.
* Utlös och spåra återställning för en partition.

Mer information om säkerhets kopierings-och återställnings tjänsten finns i [REST API referens](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Nästa steg
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric program distribution med PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
