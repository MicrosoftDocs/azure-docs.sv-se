---
title: Azure Service Fabric-avsökningar
description: Hur man modellerar en Direktmigrering i Azure Service Fabric med hjälp av MANIFEST-filer för program och tjänster.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "82137594"
---
# <a name="liveness-probe"></a>Direktmigreringens avsökning
Från och med version 7,1 stöder Azure Service Fabric en mekanism för direktmigrering av program i [behållare][containers-introduction-link] . En Live-avsökning hjälper till att rapportera om liveheten för ett behållar program, som kommer att startas om det inte svarar snabbt.
Den här artikeln innehåller en översikt över hur du definierar en direktmigreringens avsökning med hjälp av MANIFEST filer.

Innan du fortsätter med den här artikeln kan du bekanta dig med [Service Fabric program modellen][application-model-link] och [Service Fabric värd modell][hosting-model-link].

> [!NOTE]
> Direktmigreringens avsökning stöds bara för behållare i NAT-nätverk.

## <a name="semantics"></a>Semantik
Du kan bara ange en Direktmigrering per behållare och kan styra dess beteende genom att använda följande fält:

* `initialDelaySeconds`: Den första fördröjningen i sekunder att börja köra avsökningen efter att behållaren har startats. Det värde som stöds är **int**. Standardvärdet är 0 och minimivärdet är 0.

* `timeoutSeconds`: Den tid i sekunder som vi anser att avsökningen är klar, om den inte har slutförts. Det värde som stöds är **int**. Standardvärdet är 1 och minimivärdet är 1.

* `periodSeconds`: Tiden i sekunder för att ange frekvensen för avsökningen. Det värde som stöds är **int**. Standardvärdet är 10 och minimivärdet är 1.

* `failureThreshold`: När vi träffar det här värdet startar behållaren om. Det värde som stöds är **int**. Standardvärdet är 3 och minimivärdet är 1.

* `successThreshold`: Vid fel, för att avsökningen ska betraktas som lyckad, måste den köras korrekt för det här värdet. Det värde som stöds är **int**. Standardvärdet är 1 och minimivärdet är 1.

Det kan bara finnas en avsökning till en behållare när som helst. Om avsökningen inte slutförs inom den tid som anges i **timeoutSeconds**, väntar du och räknar tiden mot **failureThreshold**. 

Dessutom kommer Service Fabric att öka följande [hälso rapporter][health-introduction-link] för avsökning på **DeployedServicePackage**:

* `OK`: Avsökningen slutförs för det värde som anges i **successThreshold**.

* `Error`: Avsökningen **failureCount**  ==   **failureThreshold** innan behållaren startas om.

* `Warning`: 
    * Avsökningen Miss lyckas och **failureCount**  <  **failureThreshold**. Hälso rapporten är kvar tills **failureCount** når värdet som anges i **failureThreshold** eller **successThreshold**.
    * Vid lyckad efter fel kvarstår varningen men med uppdaterad efterföljande lyckade försök.

## <a name="specifying-a-liveness-probe"></a>Ange en direktmigreringens avsökning

Du kan ange en avsökning i ApplicationManifest.xml-filen under **service manifest import**.

Avsökningen kan vara något av följande:

* HTTP
* TCP
* Ledn 

### <a name="http-probe"></a>HTTP-avsökning

För en HTTP-avsökning kommer Service Fabric att skicka en HTTP-begäran till den port och sökväg som du anger. En returkod som är större än eller lika med 200, och mindre än 400, visar att det lyckades.

Här är ett exempel på hur du anger en HTTP-avsökning:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HTTP-avsökningen har ytterligare egenskaper som du kan ange:

* `path`: Sökvägen som ska användas i HTTP-begäran.

* `port`: Porten som ska användas för avsökningar. Den här egenskapen är obligatorisk. Intervallet är 1 till 65535.

* `scheme`: Det schema som ska användas för att ansluta till kod paketet. Om den här egenskapen är inställd på HTTPS hoppas certifikat verifieringen över. Standardvärdet är HTTP.

* `httpHeader`: De rubriker som ska anges i begäran. Du kan ange flera huvuden.

* `host`: Värdens IP-adress att ansluta till.

### <a name="tcp-probe"></a>TCP-avsökning

För en TCP-avsökning försöker Service Fabric att öppna en socket på behållaren genom att använda den angivna porten. Om den kan upprätta en anslutning anses avsökningen vara lyckad. Här är ett exempel på hur du anger en avsökning som använder en TCP-socket:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec-avsökning

Den här avsökningen utfärdar ett **exec** -kommando till behållaren och väntar på att kommandot ska slutföras.

> [!NOTE]
> **Exec** -kommandot tar en kommaavgränsad sträng. Kommandot i följande exempel fungerar för en Linux-behållare.
> Om du försöker avsöka en Windows-behållare använder du **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Nästa steg
I följande artikel finns relaterad information:
* [Service Fabric och behållare][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

