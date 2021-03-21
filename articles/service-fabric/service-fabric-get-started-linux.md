---
title: Konfigurera utvecklings miljön i Linux
description: Installera runtime och SDK, och skapa ett lokalt utvecklingskluster i Linux. När den här installationen är klar kan du börja bygga program.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 14b8a278605a908b4182c724831b2e42de54a753
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086898"
---
# <a name="prepare-your-development-environment-on-linux"></a>Förbereda utvecklingsmiljön i Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

För att kunna skapa och köra [Azure Service Fabric-program](service-fabric-application-model.md) på en Linux-utvecklingsdator måste du installera runtime och SDK. Du kan även installera SDK:er för Java- och .NET Core-utveckling. 

I den här artikeln förutsätter vi att du installerar internt i Linux eller använder [Service Fabric Onebox behållaravbildningen-behållar avbildningen](https://hub.docker.com/_/microsoft-service-fabric-onebox), t. ex. `mcr.microsoft.com/service-fabric/onebox:u18` .

Du kan hantera Service Fabric entiteter som finns i molnet eller lokalt med Azure Service Fabric kommando rads gränssnitt (CLI). Information om hur du installerar kommandoradsgränssnittet finns i [Konfigurera Service Fabric CLI](./service-fabric-cli.md).


## <a name="prerequisites"></a>Förutsättningar

Följande operativsystemversioner stöds för utveckling:

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Kontrollera att `apt-transport-https`-paketet har installerats.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)


## <a name="installation-methods"></a>Installationsmetoder

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Uppdatera dina APT-källor
Om du vill installera SDK och det tillhörande runtime-paketet via kommandoradsverktyget apt-get så måste du först uppdatera dina APT-källor (Advanced Packaging Tool).

## <a name="script-installation"></a>Installation av skript

För enkelhetens skull tillhandahålls ett skript för att installera Service Fabric Runtime och Service Fabric common SDK tillsammans med [ **sfctl** CLI](service-fabric-cli.md). Vi förutsätter att du har godkänt licenserna för all programvara som installeras innan du kör skriptet. Du kan också köra de [manuella installations](#manual-installation) stegen i nästa avsnitt som visar associerade licenser samt de komponenter som installeras.

När skriptet har körts kan du gå vidare till [Konfigurera ett lokalt kluster](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Manuell installation
Om du vill installera Service Fabric Runtime och SDK manuellt följer du stegen i resten av den här guiden.

1. Öppna en terminal.

2. Lägg till `dotnet` lagrings platsen i listan över källor som motsvarar din distribution.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Lägg till den nya MS Open Tech GNU Privacy Guard-nyckeln (GnuPG eller GPG) till din APT-nyckelring.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Lägg till den officiella Docker GPG-nyckeln i din APT-nyckelring.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Ställ in Docker-databasen.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Lägg till Azul JDK-nyckeln i APT-nyckelringen och konfigurera dess lagrings plats.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Uppdatera paketlistor baserat på nyligen tillagda lagringsplatser.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Installera och konfigurera Service Fabric SDK för ett lokalt kluster

När du har uppdaterat källorna kan du installera SDK. Installera Service Fabric SDK-paketet, bekräfta installationen och acceptera licensavtalet.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Följande kommandon automatiserar godkännandet av licensen för Service Fabric-paket:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>Uppdatera dina yum-databaser
Om du vill installera SDK och associerat runtime-paket via kommando rads verktyget yum måste du först uppdatera paket källorna.

## <a name="manual-installation-rhel"></a>Manuell installation (RHEL)
Om du vill installera Service Fabric Runtime och SDK manuellt följer du stegen i resten av den här guiden.

1. Öppna en terminal.
2. Ladda ned och installera fler paket för Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Lägg till EfficiOS RHEL7-paketdatabasen i systemet.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importera EfficiOS-paketets signeringsnyckel till den lokala GPG-nyckelringen.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Lägg till Microsoft RHEL-lagringsplatsen i systemet.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>Installera och konfigurera Service Fabric SDK för ett lokalt kluster (RHEL)

När du har uppdaterat källorna kan du installera SDK. Installera Service Fabric SDK-paketet, bekräfta installationen och acceptera licensavtalet.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Inkluderade paket
Service Fabric Runtime som medföljer SDK-installationen innehåller paketen i följande tabell. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1,8 | Implicit från npm | senaste |
**RHEL** | - | OpenJDK 1.8 | Implicit från npm | senaste |

## <a name="set-up-a-local-cluster"></a>Konfigurera ett lokalt kluster
1. Starta ett lokalt Service Fabric-kluster för utveckling.

# <a name="container-based-local-cluster"></a>[Container-baserat lokalt kluster](#tab/localclusteroneboxcontainer)

Starta en container-baserad [Service Fabric Onebox behållaravbildningen](https://hub.docker.com/_/microsoft-service-fabric-onebox) -kluster.

1. Installera Moby för att kunna distribuera Docker-behållare.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Uppdatera Docker-daemon-konfigurationen på värden med följande inställningar och starta om Docker daemon. Information: [Aktivera stöd för IPv6](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Starta klustret.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. Läs mer om vissa revideringar på sidan [Docker-hubb](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

# <a name="local-cluster"></a>[Lokalt kluster](#tab/localcluster)

När du har installerat SDK med hjälp av stegen ovan startar du ett lokalt kluster.

1. Kör klusterinstallationsskriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Öppna en webbläsare och gå till **Service Fabric Explorer** ( `http://localhost:19080/Explorer` ). När klustret startas visas Service Fabric Explorer-instrumentpanelen. Konfigurationen av klustret kan ta flera minuter. Om din webbläsare inte kan öppna URL:en, eller om Service Fabric Explorer inte visar att systemet är klart, väntar du några minuter och försöker sedan igen.

    ![Service Fabric Explorer på Linux][sfx-linux]

    Nu kan du distribuera fördefinierade Service Fabric-programpaket eller nya paket baserat på gästcontainrar eller körbara gästprogram. Om du vill skapa nya tjänster med SDK:er för Java eller .NET Core, följer du installationsanvisningarna i följande avsnitt.


> [!NOTE]
> Fristående kluster stöds inte i Linux.


> [!TIP]
> Om du har en tillgänglig SSD-disk rekommenderar vi att du skickar en SSD-mappsökväg med hjälp av `--clusterdataroot` med devclustersetup.sh för överlägsna prestanda.

## <a name="set-up-the-service-fabric-cli"></a>Konfigurera Service Fabric CLI

[Service Fabric CLI](service-fabric-cli.md) innehåller kommandon för att interagera med Service Fabric-entiteter, t.ex. kluster och program. Installera CLI genom att följa anvisningarna i [Service Fabric CLI](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Konfigurera Yeoman-generatorer för container och körbara gästprogram
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa Service Fabric-program från en terminal med en Yeoman-mallgenerator. Följ dessa steg för att konfigurera Service Fabric Yeoman-mallgeneratorer:

1. Installera Node.js och npm på datorn.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Installera [Yeoman](https://yeoman.io/)-mallgeneratorn från npm på datorn.

    ```bash
    sudo npm install -g yo
    ```
3. Installera Yeo-containergeneratorn för Service Fabric och generatorn för körbara gästprogram från npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

När du har installerat generatorerna skapar du körbara gästprogram eller containertjänster genom att köra `yo azuresfguest` respektive `yo azuresfcontainer`.

## <a name="set-up-net-core-31-development"></a>Konfigurera .NET Core 3,1-utveckling

Installera [.net Core 3,1 SDK för Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) för att börja [skapa C# Service Fabric-program](service-fabric-create-your-first-linux-application-with-csharp.md). Paket för .NET Core Service Fabric-program finns på NuGet.org.

## <a name="set-up-java-development"></a>Konfigurera Java-utveckling

Om du vill skapa Service Fabric tjänster med Java installerar du Gradle för att köra Bygg uppgifter. Kör kommandot nedan för att installera Gradle. Java-biblioteken för Service Fabric hämtas från Maven.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Du måste också installera Service Fabric Yeo-generatorn för körbara Java-filer. Kontrollera att [Yeoman är installerat](#set-up-yeoman-generators-for-containers-and-guest-executables) och kör sedan följande kommando:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Installera plugin-programmet Eclipse (valfritt)

Du kan installera Eclipse-pluginprogrammet för Service Fabric i Eclipse IDE för Java-utvecklare eller Java EE-utvecklare. Du kan använda Eclipse för att skapa körbara Service Fabric-gästprogram och containerprogram utöver Service Fabric Java-program.

> [!IMPORTANT]
> Service Fabric plugin-programmet kräver Eclipse Neon eller en senare version. Efter den här anteckningen följer instruktioner om hur du kontrollerar din Eclipse-version. Om du har en tidigare version av Eclipse installerad kan du hämta nyare versioner från [Eclipse-webbplatsen](https://www.eclipse.org). Vi rekommenderar att du inte installerar ovanpå (skriver över) en befintlig installation av Eclipse. Ta bort den gamla versionen innan du kör installationsprogrammet eller installera den nya versionen i en annan katalog.
> 
> I Ubuntu rekommenderar vi att du installerar direkt från Eclipses webbplats i stället för att använda ett installationspaket (`apt` eller `apt-get`). På så sätt kan du vara säker på att du får den senaste versionen av Eclipse. Du kan installera Eclipse IDE för Java-utvecklare eller för Java EE-utvecklare.

1. I Eclipse kontrollerar du att Eclipse Neon eller senare och Buildship version 2.2.1 eller senare är installerade. Kontrol lera de installerade komponenternas versioner genom att välja **Hjälp**  >  **om**  >  **installations information** för Sol förmörkelse. Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update].

2. Installera Service Fabric plugin-programmet genom att välja **Hjälp**  >  **installera ny program vara**.

3. I rutan **arbeta med** anger du **https: \/ /dl.Microsoft.com/Eclipse**.

4. Välj **Lägg till**.

    ![Sidan Tillgänglig programvara][sf-eclipse-plugin]

5. Välj **ServiceFabric**-plugin-programmet och välj sedan **Nästa**.

6. Utför följande installationssteg. Acceptera sedan licensavtalet.

Om du redan har Service Fabric Eclipse-plugin-programmet installerat kontrollerar du att du har den senaste versionen. Kontrol lera genom att välja **Hjälp**  >  **om**  >  **installations information** för Sol förmörkelse. Sök sedan efter Service Fabric i listan över installerade plugin-program. Välj **Uppdatera** om det finns en nyare version.

Mer information finns i [Service Fabric-plugin-program för utveckling av Java-program i Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Uppdatera SDK och Runtime

Om du vill uppdatera till den senaste versionen av SDK och Runtime kör du följande kommandon.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Om du vill uppdatera Java SDK-binärfilerna från Maven måste du uppdatera versionsinformationen för motsvarande binärfil i ``build.gradle``-filen så att den pekar på den senaste versionen. Om du vill veta exakt var du ska uppdatera versionen kan du titta i någon av ``build.gradle``-filerna i [komma igång-exemplen för Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> När du uppdaterar paketen ovan kan ditt lokala miljökluster stoppas. Starta om ditt lokala kluster efter en uppgradering genom att följa instruktionerna i den här artikeln.

## <a name="remove-the-sdk"></a>Ta bort SDK
Kör följande kommandon om du vill ta bort Service Fabric SDK:erna.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (stöd för förhandsversion av Service Fabric)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Nästa steg

* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)
* [Hantera program med hjälp av Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Skillnader mellan Service Fabric i Windows och Linux](service-fabric-linux-windows-differences.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
