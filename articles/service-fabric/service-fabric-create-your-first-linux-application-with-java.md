---
title: Skapa en Azure Service Fabric Reliable-aktörer Java-program på Linux
description: Lär dig hur du skapar och distribuerar err Java Service Fabric tillförlitliga aktörer-program på fem minuter.
ms.topic: conceptual
ms.date: 06/18/2018
ms.custom: devx-track-java
ms.openlocfilehash: 7d87b72437f86d7dc1ca4e3cf9f3d67609691c70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655959"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Skapa ditt första Java Service Fabric Reliable Actors-program på Linux
> [!div class="op_single_selector"]
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Den här snabbstartsguiden hjälper dig att skapa ditt första Azure Service Fabric Java-program i en Linux-utvecklingsmiljö på bara några minuter.  När du är klar har du ett enkelt Java-program för en tjänst som körs i klustret för lokal utveckling.  

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du installera Service Fabric SDK, Service Fabric CLI, Yeoman, konfigurera Java-utvecklingsmiljön och konfigurera ett utvecklingskluster i [Linux-utvecklingsmiljön](service-fabric-get-started-linux.md). Om du använder Mac OS X kan du [konfigurera en utvecklingsmiljö på en Mac med hjälp av Docker](service-fabric-get-started-mac.md).

Installera också [Service Fabric CLI](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Installera och konfigurera generatorerna för Java
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa ett Service Fabric Java-program från terminalen med en Yeoman-mallgenerator.  Om Yeoman inte har installerats än kan du läsa informationen i [Service Fabric getting started with Linux](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) (Service Fabric – komma igång med Linux) om hur du konfigurerar Yeoman. Kör följande kommando för att installera Service Fabric Yeoman-mallgeneratorn för Java.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Grundläggande begrepp
För att komma igång med Reliable Actors behöver du bara förstå några grundläggande begrepp:

* **Aktörstjänst**. Reliable Actors paketeras i Reliable Services som kan distribueras i Service Fabric-infrastrukturen. Aktörsinstanser aktiveras i en namngiven tjänst-instans.
* **Aktörsregistrering**. Som med Reliable Services måste en Reliable Actor-tjänst registreras med Service Fabric Runtime. Utöver det måste aktörstypen registreras med Actor Runtime.
* **Aktörsgränssnitt**. Aktörsgränssnittet används till att definiera ett offentligt gränssnitt av stark typ för en aktör. I Reliable Actor-modellterminologin definierar aktörsgränssnittet de typer av meddelanden som aktören kan förstå och bearbeta. Aktörsgränssnittet används av andra aktörer och klientprogram för att ”skicka” (asynkrona) meddelanden till aktören. Reliable Actors kan implementera flera gränssnitt.
* **ActorProxy-klass**. ActorProxy-klassen används av klientprogram för att anropa metoderna som exponeras via aktörsgränssnittet. ActorProxy-klassen har två viktiga funktioner:

  * Namnmatchning: Den kan leta reda på aktören i klustret (hitta noden i klustret där den finns).
  * Felhantering: Den kan försöka utföra metodanrop igen och matcha aktörsplatsen igen efter, till exempel, ett fel som kräver att aktören flyttas till en annan nod i klustret.

Följande regler som gäller aktörsgränssnitt är värda att nämna:

* Aktörsgränssnittsmetoder får inte överbelastas.
* Aktörsgränssnittsmetoder får inte ha parametrarna out, ref eller optional.
* Allmänna gränssnitt stöds inte.

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program innehåller en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. Generatorn som du installerade i förra avsnittet gör det enkelt att skapa din första tjänst och lägga till fler senare.  Du kan också skapa, bygga och distribuera Service Fabric Java-program med ett plugin-program för Eclipse. Läs mer i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md). I den här snabbstartguiden använder vi Yeoman till att skapa ett program med en enda tjänst, som lagrar och hämtar ett värde.

1. I en terminal, skriver du in ``yo azuresfjava``.
2. Namnge ditt program.
3. Välj vilken typ din första tjänst ska ha och namnge den. I den här guiden väljer vi en Reliable Actor-tjänst. Mer information om andra typer av tjänster finns i [Service Fabric programming model overview](service-fabric-choose-framework.md) (Översikt över programmeringsmodellen i Service Fabric).
   ![Service Fabric Yeoman-generator för Java][sf-yeoman]

Om du ger programmet namnet "HelloWorldActorApplication" och aktören "HelloWorldActor" skapas följande scaffolding:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Grundläggande byggstenar i Reliable Actors
De grundläggande begreppen som beskrivs tidigare översätts till de grundläggande byggstenarna för en Reliable Actor-tjänst.

### <a name="actor-interface"></a>Aktörsgränssnitt
Det här innehåller gränssnittsdefinitionen för aktören. Det här gränssnittet definierar aktörskontraktet som delas av aktörsimplementeringen och klienterna som anropar aktören, så det är normalt en bra idé att definiera den på en plats som är separat från aktörsimplementeringen och kan delas av flera andra tjänster eller klientprogram.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Aktörstjänst
Det här innehåller aktörsimplementeringen och aktörsregistreringskoden. Aktörsklassen implementerar aktörsgränssnittet. Här gör aktören sitt jobb.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Aktörsregistrering
Aktörstjänsten måste vara registrerad med en tjänsttyp i Service Fabric Runtime. För att aktörstjänsten ska kunna köra dina aktörsinstanser måste aktörstypen också registreras hos aktörstjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());

public static void main(String[] args) throws Exception {

        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Skapa programmet
I Service Fabric Yeoman-mallarna ingår ett byggskript för [Gradle](https://gradle.org/) som du kan använda för att skapa programmet från terminalen.
Service Fabric Java-beroenden hämtas från Maven. Om du vill skapa och arbeta med Service Fabric Java-programmen måste du se till att du har JDK och Gradle installerade. Om den inte har installerats än kan du läsa mer i [Service Fabric getting started with Linux](service-fabric-get-started-linux.md#set-up-java-development) (Service Fabric – komma igång med Linux) om hur du installerar JDK och Gradle.

När du ska bygga och paketera programmet kör du följande:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret.

1. Anslut till det lokala Service Fabric-klustret (klustret måste vara [konfigurerat och igång](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Du hittar parametrarna till de här kommandona i de genererade manifesten i programpaketet.

När programmet har distribuerats öppnar du en webbläsare och går till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) på `http://localhost:19080/Explorer`.
Expandera sedan noden **Program** och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

> [!IMPORTANT]
> Om du vill distribuera programmet till ett säkert Linux-kluster i Azure måste du konfigurera ett certifikat för att verifiera ditt program med Service Fabric Runtime. På så sätt kan dina Reliable Actors-tjänster kommunicera med de underliggande Service Fabric runtime-API: erna. Mer information finns i [Konfigurera en Reliable Services app för att köra Linux-kluster](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Starta testklienten och utför en redundansväxling
Aktörer gör ingenting på egen hand, det behövs en annan tjänst eller klient för att skicka meddelanden till dem. Aktörsmallen innehåller ett enkelt testskript som du kan använda för att interagera med aktörstjänsten.

> [!Note]
> Test klienten använder klassen ActorProxy för att kommunicera med aktörer, som måste köras i samma kluster som aktörs tjänsten eller dela samma IP-adressutrymme.  Du kan köra test klienten på samma dator som det lokala utvecklings klustret.  För att kunna kommunicera med aktörer i ett fjärran slutet kluster måste du dock distribuera en gateway på klustret som hanterar extern kommunikation med aktörerna.

1. Kör skriptet med övervakningsverktyget för att se resultatet av aktörstjänsten.  Testskriptet anropar metoden `setCountAsync()` hos aktören för att öka en räknare, anropar metoden `getCountAsync()` hos aktören för att hämta det nya räknarvärdet och visar värdet på konsolen.

   Om du använder MAC OS X måste du kopiera mappen HelloWorldTestClient till en viss plats i behållaren genom att köra följande ytterligare kommandon.    

    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. Leta rätt på noden i Service Fabric Explorer som är värd för aktörstjänstens primära replik. På skärmbilden nedan är det nod 3. Den primära tjänsterepliken hanterar läs- och skrivåtgärder.  Ändringar i tjänst tillstånd replikeras sedan till de sekundära replikerna, som körs på noderna 0 och 1 i skärm bilden nedan.

    ![Hitta den primära repliken i Service Fabric Explorer][sfx-primary]

3. Klicka på noden du hittade i föregående steg under **Noder** och välj sedan **Inaktivera (starta om)** på menyn Åtgärder. Den här åtgärden startar om noden som kör den primära tjänsterepliken och tvingar fram en redundansväxling till en av de sekundära replikerna som körs på en annan nod.  Den sekundära repliken befordras till primär, en annan sekundär replik skapas på en annan nod och den primära repliken börjar hantera läs- och skrivåtgärder. Titta på resultatet från testklienten när noden startas om, och observera att räknaren fortsätter att räkna upp trots redundansväxlingen.

## <a name="remove-the-application"></a>Ta bort programmet
Använd installationsskriptet som medföljer mallen för att ta bort programinstansen, avregistrera programpaketet och ta bort programpaketet från klustrets avbildningslager.

```bash
./uninstall.sh
```

I Service Fabric Explorer ser du att programmet och programtypen inte längre visas i noden **Program**.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric Java-bibliotek på Maven
Service Fabric Java-bibliotek har lagrats i Maven. Du kan lägga till beroendena i ``pom.xml`` eller ``build.gradle`` för dina projekt så att de använder Service Fabric Java-bibliotek från **mavenCentral**. 

### <a name="actors"></a>Aktörer

Service Fabric-stöd för tillförlitliga aktörer för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Tjänster

Service Fabric Reliable Services för ditt program.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Andra
#### <a name="transport"></a>Transport

Transportnivåstöd för Service Fabric Java-program. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram, om du inte programmerar på transportnivån.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-stöd

Systemnivåstöd för Service Fabric, som kommunicerar med ursprunglig Service Fabric-körning. Du behöver inte uttryckligen lägga till det här beroendet till tillförlitliga aktörer- eller tjänstprogram. Det hämtas automatiskt från Maven när du inkluderar de andra beroendena ovan.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>Nästa steg

* [Skapa ditt första Service Fabric Java-program för Linux med hjälp av Eclipse](service-fabric-get-started-eclipse.md)
* [Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagera med Service Fabric-kluster med Service Fabric CLI](service-fabric-cli.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
