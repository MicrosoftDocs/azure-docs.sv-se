---
title: Skapa din första Reliable service i Java
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillstånds lösa och tillstånds känsliga tjänster i Java.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91535244"
---
# <a name="get-started-with-reliable-services-in-java"></a>Kom igång med Reliable Services i Java
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Den här artikeln beskriver grunderna i Azure Service Fabric Reliable Services och vägleder dig genom att skapa och distribuera ett enkelt tillförlitligt tjänst program som är skrivet i Java. 

## <a name="installation-and-setup"></a>Installation och installation
Kontrol lera att du har konfigurerat Service Fabric utvecklings miljö på din dator innan du börjar.
Om du behöver konfigurera det går du till [komma igång med Mac](service-fabric-get-started-mac.md) eller [Kom igång på Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundläggande begrepp
För att komma igång med Reliable Services behöver du bara förstå några grundläggande begrepp:

* **Tjänst typ**: det här är tjänst implementeringen. Den definieras av den klass som du skriver som utökar `StatelessService` och annan kod eller beroenden som används däri, tillsammans med ett namn och ett versions nummer.
* **Namngiven tjänst instans**: om du vill köra tjänsten skapar du namngivna instanser av tjänst typen, ungefär som du skapar objekt instanser av en klass typ. Tjänst instanser är i själva verket instansieringar av din tjänst klass som du skriver.
* **Tjänst värd**: de namngivna tjänst instanser som du skapar måste köras inuti en värd. Tjänste värden är bara en process där instanser av tjänsten kan köras.
* **Tjänst registrering**: registreringen samlar ihop allt. Tjänst typen måste vara registrerad med Service Fabric runtime i en tjänst värd för att Service Fabric ska kunna skapa instanser av den som ska köras.  

## <a name="create-a-stateless-service"></a>Skapa en tillstånds lös tjänst
Börja med att skapa ett Service Fabric-program. I Service Fabric SDK för Linux finns en Yeoman-generator för att tillhandahålla ramverk för ett Service Fabric program med en tillstånds lös tjänst. Starta genom att köra följande Yeoman-kommando:

```bash
$ yo azuresfjava
```

Följ instruktionerna för att skapa en **tillförlitlig tillstånds lös tjänst**. Ge programmet namnet "HelloWorldApplication" och tjänsten "HelloWorld" i den här självstudien. Resultatet inkluderar kataloger för `HelloWorldApplication` och `HelloWorld` .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Tjänst registrering
Tjänst typer måste registreras med Service Fabric Runtime. Tjänst typen definieras i `ServiceManifest.xml` och tjänst klassen som implementerar `StatelessService` . Tjänst registreringen utförs i den huvudsakliga start punkten för processen. I det här exemplet är processens huvud start punkt `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementera tjänsten

Öppna **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService. java**. Den här klassen definierar tjänst typen och kan köra vilken kod som helst. Tjänst-API: et tillhandahåller två start punkter för din kod:

* En öppen start punkt metod, anropad `runAsync()` , där du kan börja köra alla arbets belastningar, inklusive tids krävande beräknings arbets belastningar.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* En kommunikations start punkt där du kan ansluta dig till den kommunikations stack som du väljer. Det är här du kan börja ta emot förfrågningar från användare och andra tjänster.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Den här självstudien fokuserar på `runAsync()` Start punkt metoden. Det är här du kan börja köra koden direkt.

### <a name="runasync"></a>RunAsync
Plattformen anropar den här metoden när en instans av en tjänst placeras och är klar att köras. För en tillstånds lös tjänst innebär det att när tjänst instansen öppnas. En avbrotts-token anges för att koordineras när tjänst instansen måste stängas. I Service Fabric kan den här öppna/stäng-cykeln för en tjänst instans ske många gånger över hela livs längden för tjänsten. Detta kan inträffa av olika orsaker, inklusive:

* Systemet flyttar tjänst instanserna för resurs balansering.
* Fel uppstår i koden.
* Programmet eller systemet har uppgraderats.
* Den underliggande maskin varan upplever ett avbrott.

Den här dirigeringen hanteras av Service Fabric för att hålla tjänsten hög tillgänglig och korrekt bal anse rad.

`runAsync()` bör inte blockera synkront. Din implementering av runAsync ska returnera en CompletableFuture som gör att körningen kan fortsätta. Om din arbets belastning behöver implementera en tids krävande uppgift som ska utföras i CompletableFuture.

#### <a name="cancellation"></a>Annullering
Annullering av din arbets belastning är en samarbets insats som dirigeras av den angivna avbrotts-token. Systemet väntar tills åtgärden har slutförts (genom slutförd, annullering eller fel) innan den flyttas. Det är viktigt att du följer token för uppsägning, slutför allt arbete och avslutar `runAsync()` så snabbt som möjligt när systemet begär uppsägning. Följande exempel visar hur du hanterar en annullerings händelse:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

I det här tillstånds lösa tjänst exemplet lagras antalet i en lokal variabel. Men eftersom det här är en tillstånds lös tjänst finns bara det värde som lagras för den aktuella livs cykeln för tjänst instansen. När tjänsten flyttas eller startas om går värdet förlorat.

## <a name="create-a-stateful-service"></a>Skapa en tillstånds känslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillstånds känslig. En tillstånds känslig tjänst kan upprätthålla tillstånd på ett tillförlitligt sätt i själva tjänsten, tillsammans med den kod som används. Tillstånd har gjorts med hög tillgänglighet genom Service Fabric utan att behöva beständigt tillstånd till en extern lagrings plats.

Om du vill konvertera ett räknar värde från tillstånds lös till hög tillgänglighet och beständigt, även när tjänsten flyttas eller startas om, behöver du en tillstånds känslig tjänst.

I samma katalog som programmet HelloWorld kan du lägga till en ny tjänst genom att köra `yo azuresfjava:AddService` kommandot. Välj "tillförlitlig tillstånds känslig tjänst" för ditt ramverk och ge tjänsten namnet "HelloWorldStateful". 

Ditt program bör nu ha två tjänster: den tillstånds lösa tjänsten HelloWorld och den tillstånds känsliga tjänsten HelloWorldStateful.

En tillstånds känslig tjänst har samma start punkter som en tillstånds lös tjänst. Den största skillnaden är tillgängligheten för en tillstånds leverantör som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en tillstånds leverantörs implementering som kallas Reliable Collections, vilket gör att du kan skapa replikerade data strukturer via den pålitliga tillstånds hanteraren. En tillstånds känslig tillförlitlig tjänst använder denna tillstånds leverantör som standard.

Öppna HelloWorldStateful. java i **HelloWorldStateful-> src**, som innehåller följande RunAsync-metod:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` fungerar på samma sätt i tillstånds känsliga och tillstånds lösa tjänster. Men i en tillstånds känslig tjänst utför plattformen ytterligare arbete för din räkning innan den körs `RunAsync()` . Detta arbete kan omfatta att se till att de pålitliga tillstånds hanteraren och pålitliga samlingar är klara att använda.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och tillförlitliga tillstånds hanterare
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) är en ordboks implementering som du kan använda för att lagra tillstånd i tjänsten på ett tillförlitligt sätt. Med Service Fabric och Reliable HashMaps kan du lagra data direkt i din tjänst utan att ha behov av ett externt beständigt arkiv. Reliable HashMaps gör dina data hög tillgängliga. Service Fabric uppnår detta genom att skapa och hantera flera *repliker* av tjänsten åt dig. Det innehåller också ett API som sammanfattar komplexiteten vid hantering av dessa repliker och deras tillstånds över gångar.

Tillförlitliga samlingar kan lagra vilken Java-typ som helst, inklusive dina anpassade typer, med ett par varningar:

* Service Fabric gör ditt tillstånd hög tillgängligt genom att *Replikera* tillstånd mellan noder, och tillförlitliga HashMap lagrar dina data till en lokal disk på varje replik. Det innebär att allt som lagras i Reliable HashMaps måste kunna *serialiseras*. 
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på Reliable HashMaps. Objekt som lagras i Reliable HashMaps hålls i lokalt minne i tjänsten. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte följer lokala instanser av dessa objekt utan att utföra en uppdaterings åtgärd för den tillförlitliga samlingen i en transaktion. Detta beror på att ändringar av lokala instanser av objekt inte replikeras automatiskt. Du måste infoga objektet igen i ord listan eller använda en av *uppdaterings* metoderna i ord listan.

Den pålitliga tillstånds hanteraren hanterar pålitliga HashMaps åt dig. Du kan ställa en tillförlitlig tillstånds hanterare för tillförlitlig insamling efter namn när som helst och var som helst i tjänsten. Den tillförlitliga tillstånds hanteraren garanterar att du får tillbaka en referens. Vi rekommenderar inte att du sparar referenser till tillförlitliga samlings instanser i klass medlems variabler eller egenskaper. Särskild försiktighet måste vidtas för att säkerställa att referensen anges till en instans hela tiden i tjänste livs cykeln. Den Reliable State Manager hanterar det här arbetet åt dig, och den är optimerad för upprepade besök.


### <a name="transactional-and-asynchronous-operations"></a>Transaktionella och asynkrona åtgärder
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Åtgärder på Reliable HashMaps är asynkrona. Detta beror på att Skriv åtgärder med tillförlitliga samlingar utför I/O-åtgärder för att replikera och bevara data till disk.

Reliable HashMap-åtgärder är *transaktionella*, så att du kan hålla tillstånd konsekvent över flera Reliable HashMaps och åtgärder. Du kan till exempel få ett arbets objekt från en tillförlitlig ord lista, utföra en åtgärd på den och spara resultatet i en annan tillförlitlig HashMap, allt inom en enskild transaktion. Detta behandlas som en atomisk åtgärd och garanterar att hela åtgärden kommer att lyckas eller att hela åtgärden kommer att återställas. Om ett fel inträffar när du har avplacerat objektet, men innan du sparar resultatet, återställs hela transaktionen och objektet behålls i kön för bearbetning.


## <a name="build-the-application"></a>Skapa programmet

Yeoman-ramverk innehåller ett gradle-skript för att bygga program-och bash-skript för att distribuera och ta bort programmet. Om du vill köra programmet måste du först skapa programmet med gradle:

```bash
$ gradle
```

Detta skapar ett Service Fabric-programpaket som kan distribueras med hjälp av Service Fabric CLI.

## <a name="deploy-the-application"></a>Distribuera programmet

När du har skapat programmet kan du distribuera det till det lokala klustret.

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Du hittar parametrarna till de här kommandona i de genererade manifesten i programpaketet.

När programmet har distribuerats öppnar du en webbläsare och går till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) på `http://localhost:19080/Explorer`. Expandera sedan noden **Program** och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

> [!IMPORTANT]
> Om du vill distribuera programmet till ett säkert Linux-kluster i Azure måste du konfigurera ett certifikat för att verifiera ditt program med Service Fabric Runtime. På så sätt kan dina Reliable Services-tjänster kommunicera med de underliggande Service Fabric runtime-API: erna. Mer information finns i [Konfigurera en Reliable Services app för att köra Linux-kluster](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
