---
title: Hur du kör din kod med Azure dev Spaces fungerar
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processerna för att köra din kod i Azure Kubernetes service med Azure dev Spaces
keywords: azds. yaml, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014439"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Hur du kör din kod med Azure dev Spaces fungerar

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). När [projektet har för beretts för att köras i ett dev-utrymme][how-it-works-prep]kan du skapa och köra ditt projekt i ditt AKS-kluster med hjälp av dev Spaces.

I den här artikeln beskrivs vad som händer om du kör koden i AKS med dev Spaces.

## <a name="run-your-code"></a>Kör din kod

Om du vill köra din kod i ett dev-utrymme utfärdar du `up` kommandot i samma katalog som `azds.yaml` filen:

```cmd
azds up
```

`up`Kommandot laddar upp dina programkällfiler och andra artefakter som behövs för att skapa och köra ditt projekt i dev-området. Därifrån i ditt dev Space:

1. Skapar Kubernetes-objekt för att distribuera programmet.
1. Skapar behållaren för ditt program.
1. Distribuerar ditt program till dev-utrymmet.
1. Skapar ett offentligt tillgängligt DNS-namn för program slut punkten om det är konfigurerat.
1. Använder *Port-Forward* för att ge åtkomst till program slut punkten med hjälp av http://localhost .
1. Vidarebefordrar STDOUT och stderr till verktyg på klient sidan.


## <a name="starting-a-service"></a>Starta en tjänst

När du startar en tjänst i ett dev-utrymme fungerar klient sidans verktyg och kontrollant i samordning för att synkronisera källfilerna, skapa din behållare och Kubernetes-objekt och köra ditt program.

På en mer detaljerad nivå är det vad som händer när du kör `azds up` :

1. [Filerna synkroniseras][sync-section] från användarens dator till en Azure File Storage som är unik för ANVÄNDAREns AKS-kluster. Käll koden, Helm-diagrammet och konfigurationsfilerna laddas upp.
1. Kontrollanten skapar en begäran om att starta en ny session. Den här begäran innehåller flera egenskaper, inklusive ett unikt ID, utrymmes namn, sökväg till käll koden och en fel söknings flagga.
1. Kontrollanten ersätter plats hållaren *$ (tag)* i Helm-diagrammet med det unika sessions-ID: t och installerar Helm-diagrammet för din tjänst. Genom att lägga till en referens till det unika sessions-ID: t i Helm-diagrammet kan behållaren som distribueras till AKS-klustret för den här specifika sessionen kopplas tillbaka till sessionen och den tillhör ande informationen.
1. Under installationen av Helm-diagrammet lägger Kubernetes webhook-åtkomstkontroll till ytterligare behållare till programmets Pod för instrumentering och åtkomst till projektets käll kod. Devspaces-proxy-och devspaces-proxy-init-behållare läggs till för att tillhandahålla HTTP-spårning och utrymmes dirigering. Devspaces-build-behållaren läggs till för att ge Pod åtkomst till Docker-instansen och projekt käll koden för att skapa din program behållare.
1. När programmets Pod startas, används devspaces-build-behållaren och devspaces-proxy-init-behållaren för att bygga program behållaren. Program behållaren och devspaces-behållare startas sedan.
1. När program behållaren har startat använder klient sidan funktionen Kubernetes *-Port-Forward* för att ge http-åtkomst till ditt program http://localhost . Den här port vidarebefordringen ansluter din utvecklings dator till tjänsten i ditt utvecklings utrymme.
1. När alla behållare i pod har startat körs tjänsten. I det här läget börjar funktionen på klient sidan att strömma HTTP-spåren, STDOUT och stderr. Den här informationen visas i klient sidans funktioner för utvecklaren.

## <a name="updating-a-running-service"></a>Uppdatera en aktiv tjänst

När en tjänst körs har Azure dev Spaces möjlighet att uppdatera tjänsten om någon av Project-källfilerna ändras. Dev Spaces hanterar också uppdatering av tjänsten på olika sätt beroende på vilken typ av fil som ändras. Det finns tre sätt att använda dev Spaces för att uppdatera en tjänst som körs:

* Uppdatera en fil direkt
* Återskapa och starta om programmets process i den program behållare som körs
* Återskapa och omdistribuerar programmets behållare

![Fil synkronisering för Azure dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Vissa projektfiler som är statiska till gångar, till exempel HTML-, CSS-och cshtml-filer, kan uppdateras direkt i programmets behållare utan att du behöver starta om något. Om en statisk till gång ändras, synkroniseras den nya filen med dev-utrymmet och används sedan av behållaren som körs.

Ändringar i filer som källkod eller programkonfigurationsfiler kan tillämpas genom att starta om programmets process inom den pågående behållaren. När de här filerna har synkroniserats startas programmets process om inom den behållare som körs med hjälp av processen *devhostagent* . När du skapar programmets behållare i första hand, ersätter kontrollanten Start kommandot för programmet med en annan process som heter *devhostagent*. Programmets faktiska process körs sedan som en underordnad process under *devhostagent* och dess utdata är skickas ut med *devhostagent* utdata. Processen *devhostagent* är också en del av dev Spaces och kan köra kommandon i den behållare som körs på uppdrag av dev Spaces. När du utför en omstart, *devhostagent*:

* Stoppar den aktuella processen eller de processer som är associerade med programmet
* Återskapar programmet
* Startar om processen eller processerna som är associerade med programmet

Hur *devhostagent* kör föregående steg har [kon figurer ATS i `azds.yaml`][azds-yaml-section].

Uppdateringar av projektfiler som Dockerfiles, CSPROJ-filer eller någon del av Helm-diagrammet kräver att programmets behållare återskapas och distribueras om. När en av dessa filer synkroniseras till dev-utrymmet kör kontrollanten [Helm Upgrade][helm-upgrade] -kommandot och programmets behållare återskapas och distribueras om.

## <a name="file-synchronization"></a>Filsynkronisering

Första gången ett program startas i ett utvecklings utrymme laddas alla källfiler för programmet upp. När programmet körs och senare startar om, laddas bara de ändrade filerna upp. Två filer används för att samordna processen: en fil på klient sidan och en fil på styrenhets sidan.

Filen på klient sidan lagras i en tillfällig katalog och namnges baserat på en hash för projekt katalogen som du kör i dev Spaces. I Windows skulle du till exempel ha en fil som *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* för ditt projekt. På Linux lagras filen på klient sidan i *katalogen/tmp* -katalogen. Du kan hitta katalogen på macOS genom att köra `echo $TMPDIR` kommandot.

Den här filen är i JSON-format och innehåller:

* En post för varje projekt fil som synkroniseras med dev-utrymmet
* Ett synkroniserings-ID
* Tidsstämpel för den senaste synkroniseringsåtgärden

Varje projekt fils post innehåller en sökväg till filen och dess tidsstämpel.

Enhets sidans fil lagras i AKS-klustret. Den innehåller synkroniserings-ID och tidsstämpel för den senaste synkroniseringen.

En synkronisering inträffar när tidsstämplar för synkronisering inte matchar mellan klient sidan och styrenhets sidans filer. Under en synkronisering upprepas klient sidans verktyg över fil posterna i filen på klient sidan. Om filens tidstämpel är efter synkroniseringens tidsstämpel, synkroniseras filen med dev-ytan. När synkroniseringen är klar uppdateras synkroniseringens tidsstämplar både på klient sidan och på styrenhets sidan.

Alla projektfiler synkroniseras om filen på klient sidan inte finns. Med det här beteendet kan du framtvinga en fullständig synkronisering genom att ta bort filen på klient sidan.

## <a name="how-running-your-code-is-configured"></a>Hur körning av din kod är konfigurerad

Azure dev Spaces använder `azds.yaml` filen för att installera och konfigurera tjänsten. Kontrollanten använder `install` egenskapen i `azds.yaml` filen för att installera Helm-diagrammet och skapa Kubernetes-objekten:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Som standard `prep` kommer kommandot att generera Helm-diagrammet. Den anger också egenskapen *install. Chart* till katalogen i Helm-diagrammet. Om du vill använda ett Helm-diagram på en annan plats kan du uppdatera den här egenskapen för att använda den platsen.

När du installerar Helm-diagram ger Azure dev Spaces ett sätt att åsidosätta värden i Helm-diagrammet. Standardvärdena för Helm-diagrammet finns i `charts/APP_NAME/values.yaml` .

Med hjälp av egenskapen *install. Values* kan du ange en eller flera filer som definierar värden som du vill ska ersättas i Helm-diagrammet. Om du till exempel vill ha ett värdnamn eller en databas konfiguration specifikt när du kör programmet i ett dev-utrymme kan du använda den här funktionen för åsidosättning. Du kan också lägga till en *?* i slutet av något av fil namnen för att ange det som valfritt.

Med egenskapen *install. Set* kan du konfigurera ett eller flera värden som du vill ersätta i Helm-diagrammet. Värden som kon figurer ATS i *install. Set* åsidosätter de värden som kon figurer ATS i filerna som anges i *install. Values*. Egenskaperna under *install. Set* är beroende av värdena i Helm-diagrammet och kan vara olika beroende på det genererade Helm-diagrammet.

I exemplet ovan anger egenskapen *install. Set. replicaCount* kontrollanten hur många instanser av programmet som ska köras i ditt dev-utrymme. Beroende på ditt scenario kan du öka det här värdet, men det påverkar hur du kopplar en fel sökare till programmets pod. Mer information finns i [fel söknings artikeln][troubleshooting].

I det genererade Helm-diagrammet är behållar avbildningen inställd på *{{. Values. image. databas}}: {{. Values. image. tag}}*. `azds.yaml`Filen definierar *install. Set. image. tag* -egenskapen som *$ (tag)* som standard som används som värde för *{{. Values. image. tag}}*. Genom att ställa in egenskapen *install. Set. image. tag* på det här sättet tillåter den att behållar avbildningen för programmet taggas på ett tydligt sätt när du kör Azure dev Spaces. I det här fallet är avbildningen Taggad som *\<value from image.repository> : $ (tag)*. Du måste använda variabeln *$ (tag)* som värdet för metoden   *install. Set. image. tag* för att dev Spaces ska kunna identifiera och hitta behållaren i AKS-klustret.

I ovanstående exempel `azds.yaml` definierar *install. Set. ingress. hosts*. Egenskapen *install. Set. ingress. hosts* anger ett värdnamn för offentliga slut punkter. Den här egenskapen använder också *$ (spacePrefix)*, *$ (rootSpacePrefix)* och *$ (hostSuffix)*, som är värden som tillhandahålls av kontrollanten.

*$ (SpacePrefix)* är namnet på den underordnade dev-ytan, som tar formen av *SPACENAME. s*. *$ (RootSpacePrefix)* är namnet på det överordnade utrymmet. Om *azureuser* till exempel är ett underordnat utrymme som *standard* är värdet för *$ (rootSpacePrefix)* *standard* och värdet för *$ (spacePrefix)* är *azureuser. s*. Om utrymmet inte är ett underordnat utrymme är *$ (spacePrefix)* tomt. Om *standard* utrymmet till exempel inte har något överordnat utrymme är värdet för *$ (rootSpacePrefix)* *standard* och värdet för *$ (spacePrefix)* är tomt. *$ (HostSuffix)* är ett DNS-suffix som pekar på ingångs styrenheten för Azure dev Spaces som körs i ditt AKS-kluster. Det här DNS-suffixet motsvarar en DNS-post med jokertecken, till exempel *\* . RANDOM_VALUE. EUs. azds. io*, som skapades när Azure dev Spaces-styrenheten lades till i ditt AKS-kluster.

I ovanstående `azds.yaml` fil kan du också uppdatera *install. Set. ingress. hosts* om du vill ändra värd namnet för ditt program. Om du till exempel vill förenkla värd namnet för ditt program från *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* till *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix)*.

För att bygga behållaren för ditt program använder kontrollanten följande avsnitt i `azds.yaml` konfigurations filen:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Kontrollanten använder en Dockerfile för att skapa och köra ditt program.

Egenskapen *build. context* visar en lista över katalogen där Dockerfiles finns. Egenskapen *build.dockerfile* definierar namnet på Dockerfile för att skapa produktions versionen av programmet. Egenskapen *configurations.develop.build.dockerfile* konfigurerar namnet på Dockerfile för program utvecklings versionen.

Om du har olika Dockerfiles för utveckling och produktion kan du aktivera vissa saker under utveckling och inaktivera dessa objekt för produktions distributioner. Du kan till exempel aktivera fel sökning eller mer utförlig loggning under utveckling och inaktivera i en produktions miljö. Du kan också uppdatera dessa egenskaper om dina Dockerfiles har ett annat namn eller är på en annan plats.

För att hjälpa dig att snabbt iterera under utvecklingen kommer Azure dev Spaces att synkronisera ändringar från det lokala projektet och uppdatera ditt program stegvis. Avsnittet nedan i `azds.yaml` konfigurations filen används för att konfigurera synkronisering och uppdatering:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

De filer och kataloger som ska synkronisera ändringar visas i filen *Configurations. utvecklar. container. Sync* . Dessa kataloger synkroniseras från början när du kör `up` kommandot och när ändringar upptäcks. Om det finns ytterligare eller olika kataloger som du vill synkronisera till ditt dev-utrymme kan du ändra den här egenskapen.

Egenskapen *Configurations. utvecklar. container. ITER. buildCommands* anger hur programmet ska skapas i ett utvecklings scenario. Egenskapen *Configurations. utvecklar. container. Command* innehåller kommandot för att köra programmet i ett utvecklings scenario. Du kanske vill uppdatera någon av dessa egenskaper om det finns ytterligare bygg-eller körnings flaggor eller parametrar som du vill använda under utvecklingen.

*Konfigurationerna. utvecklar. container. ITER. processesToKill* listar de processer som ska avbrytas för att stoppa programmet. Du kanske vill uppdatera den här egenskapen om du vill ändra omstarts beteendet för ditt program under utveckling. Om du till exempel har uppdaterat *konfigurationerna. utvecklar. container. ITER. buildCommands* eller *Configurations. utvecklar. container. Command* för att ändra hur programmet byggs eller startas kan du behöva ändra vilka processer som har stoppats.

När du förbereder din kod med hjälp av `azds prep` kommandot har du möjlighet att lägga till `--enable-ingress` flaggan. Om du lägger till `--enable-ingress` flaggan skapas en offentligt tillgänglig URL för ditt program. Om du utelämnar den här flaggan är programmet endast tillgängligt i klustret eller med hjälp av localhost-tunneln. När du har kört `azds prep` kommandot kan du ändra inställningen genom att ändra egenskapen *ingress. enabled* i `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om nätverk och hur förfrågningar dirigeras i Azure dev-utrymmen ser du [hur routning fungerar med Azure dev Spaces][how-it-works-routing].

Mer information om hur du snabbt kan iterera och utveckla med Kubernetes finns i [så här fungerar brygga till Kubernetes][how-it-works-bridge-to-kubernetes] och [hur fjärrfelsökning av koden med Azure dev Spaces fungerar][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md