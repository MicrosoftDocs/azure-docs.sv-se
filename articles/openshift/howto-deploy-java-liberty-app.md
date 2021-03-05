---
title: Distribuera ett Java-program med öppen frihet/WebSphere-frihet på ett Azure Red Hat OpenShift 4-kluster
description: Distribuera ett Java-program med öppen frihet/WebSphere-frihet på ett Azure Red Hat OpenShift 4-kluster.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, Java-part, mikroprofil, öppen-frihet, WebSphere-frihet, Aro, OpenShift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213002"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Distribuera ett Java-program med öppen frihet/WebSphere-frihet på ett Azure Red Hat OpenShift 4-kluster

Den här guiden visar hur du kör ditt Java-, Java-, [Jakarta](https://jakarta.ee/)-eller [mikroprofil](https://microprofile.io/) -program på Open frihet/WebSphere frihets körning och sedan distribuerar det containerbaserade programmet till ett Azure Red Hat OpenShift (Aro) 4-kluster med hjälp av Open frihet-operatorn. Den här artikeln vägleder dig genom att förbereda ett frihets program, skapa program Docker-avbildningen och köra program varan för behållare i ett ARO 4-kluster.  Mer information om öppen frihet finns [på sidan öppna frihets projekt](https://openliberty.io/). Mer information om IBM WebSphere-frihet finns på [produkt sidan för WebSphere-frihet](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför följande krav för att kunna gå igenom den här guiden.

> [!NOTE]
> För att kunna skapa och köra ett OpenShift-kluster krävs minst 40 kärnor i Azure Red Hat OpenShift. Standard kvoten för Azure-resurser för en ny Azure-prenumeration uppfyller inte det här kravet. Om du vill begära en ökning av resurs gränsen, se [standard kvot: öka gränserna efter VM-serien](../azure-portal/supportability/per-vm-quota-requests.md). Observera att den kostnads fria utvärderings prenumerationen inte är berättigad till en kvot ökning, [Uppgradera till en prenumeration där du betalar per](../cost-management-billing/manage/upgrade-azure-subscription.md) användning innan du begär en kvot ökning.

1. Förbered en lokal dator med UNIX-liknande operativ system installerat (till exempel Ubuntu, macOS).
1. Installera en Java SE-implementering (till exempel [AdoptOpenJDK openjdk 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Installera [maven](https://maven.apache.org/download.cgi) 3.5.0 eller högre.
1. Installera [Docker](https://docs.docker.com/get-docker/) för ditt operativ system.
1. Installera [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 eller senare.
1. Kontrol lera och installera [`envsubst`](https://command-not-found.com/envsubst) om det inte redan är förinstallerat i operativ systemet.
1. Klona koden för det här exemplet i det lokala systemet. Exemplet finns på [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Följ instruktionerna i [skapa ett Azure Red Hat OpenShift 4-kluster](./tutorial-create-cluster.md).

   Även om steget "Hämta en pull-hemlighet i Red Hat" är märkt som valfritt **krävs det för den här artikeln**.  Pull-hemligheten gör det möjligt för ditt Azure Red Hat OpenShift-kluster att hitta Open frihet-operatorn.

   Om du planerar att köra minnes intensiva program i klustret anger du rätt storlek på den virtuella datorn för arbetsnoderna med hjälp av `--worker-vm-size` parametern. Till exempel `Standard_E4s_v3` är den minsta storleken på virtuella datorer för att installera ElasticSearch-operatören på ett kluster. Mer information finns i:

   * [Azure CLI för att skapa ett kluster](/cli/azure/aro#az-aro-create)
   * [Virtuella dator storlekar som stöds för minnesoptimerade](./support-policies-v4.md#memory-optimized)
   * [Krav för att installera ElasticSearch-operatören](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Anslut till klustret genom att följa stegen i [ansluta till ett Azure Red Hat OpenShift 4-kluster](./tutorial-connect-cluster.md).
   * Se till att följa stegen i "installera OpenShift CLI" eftersom vi använder `oc` kommandot senare i den här artikeln.
   * Skriv ner kluster konsolens URL som ser ut så här `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Anteckna `kubeadmin` autentiseringsuppgifterna.

1. Verifiera att du kan logga in på OpenShift CLI med token för användare `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Aktivera det inbyggda behållar registret för OpenShift

Stegen i den här självstudien skapar en Docker-avbildning som måste skickas till ett behållar register som är tillgängligt för OpenShift. Det enklaste alternativet är att använda det inbyggda registret från OpenShift. Om du vill aktivera det inbyggda behållar registret följer du stegen i [Konfigurera inbyggt behållar register för Azure Red Hat OpenShift 4](built-in-container-registry.md). Tre objekt från dessa steg används i den här artikeln.

* Användar namnet och lösen ordet för Azure AD-användaren för att logga in på webb konsolen OpenShift.
* Utdata från `oc whoami` efter att du har gått igenom stegen för att logga in på OpenShift cli.  Det här värdet kallas **AAD-User** för diskussionen.
* URL för behållar registret.

Observera dessa objekt när du slutför stegen för att aktivera det inbyggda behållar registret.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Skapa ett OpenShift-namnområde för Java-appen

1. Logga in på webb konsolen OpenShift från webbläsaren med hjälp av `kubeadmin` autentiseringsuppgifterna.
2. Navigera till **Administration**  >  **namn områden**  >  **skapa namnrymd**.
3. Fyll i `open-liberty-demo` efter **namn** och välj **skapa**, som visas nästa.

   ![Skapa namnrymd](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Skapa en administratör för demonstrations projektet

Förutom avbildnings hantering beviljas **AAD-användaren** även administratörs behörighet för att hantera resurser i demonstrations projektet för Aro 4-klustret.  Logga in på OpenShift CLI och ge **AAD-användaren** nödvändiga privilegier genom att följa dessa steg.

1. Logga in på webb konsolen OpenShift från webbläsaren med hjälp av `kubeadmin` autentiseringsuppgifterna.
1. I den högra delen av webb konsolen expanderar du snabb menyn för den inloggade användaren och väljer sedan **Kopiera inloggnings kommando**.
1. Logga in på ett nytt flik-fönster med samma användare om det behövs.
1. Välj **visnings-token**.
1. Kopiera värdet i listan nedan **Logga in med denna token** till Urklipp och kör det i ett gränssnitt, som visas här.
1. Kör följande kommandon för att ge `admin` rollen **AAD-användare** i namn område `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Installera operatorn Open frihet OpenShift

När du har skapat och anslutit till klustret installerar du operatorn Open frihet.  Den huvudsakliga start sidan för Open frihet-operatören är på [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Logga in på webb konsolen OpenShift från webbläsaren med hjälp av `kubeadmin` autentiseringsuppgifterna.
2. Navigera till **operatorer**  >  **OperatorHub** och Sök efter **Open frihet-operator**.
3. Välj **Open frihet-operator** från Sök resultatet.
4. Välj **installera**.
5. I popup- **fönstret Skapa operatör**, kontrol **lera alla namn områden i klustret (standard)** för **installations läge**, **beta** för **uppdaterings kanal** och strategi för **Automatisk** **godkännande för godkännande**:

   ![Skapa operatörs prenumeration för Open frihet-operatör](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Välj **Prenumerera** och vänta en minut eller två tills den öppna frihets operatorn visas.
7. Observera operatorn Open frihet med statusen "lyckades".  Om du inte gör det diagnostiserar du och löser problemet innan du fortsätter.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Installerade operatörer som visar Open frihet är installerat.":::

## <a name="prepare-the-liberty-application"></a>Förbered frihets programmet

Vi använder ett Java-program med 8program som vårt exempel i den här hand boken. Open frihet är en [Java](https://javaee.github.io/javaee-spec/javadocs/) -kompatibel server med 8-kompatibel profil, så att du enkelt kan köra programmet.  Öppen frihet är också [Jakarta EE 8 full Profile Compatible](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Kör programmet med öppen frihet

Om du vill köra programmet med öppen frihet måste du skapa en öppen frihets Server konfigurations fil så att [maven-plugin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) -programmet kan paketera programmet för distribution. Frihets maven-plugin-programmet krävs inte för att distribuera programmet till OpenShift.  Vi kommer dock att använda den i det här exemplet med öppen frihets utvecklings läge (dev).  I utvecklarläge kan du enkelt köra programmet lokalt. Utför följande steg på den lokala datorn.

1. Kopiera `2-simple/src/main/liberty/config/server.xml` till `1-start/src/main/liberty/config` , och skriva över den befintliga filen med Tom längd. Detta `server.xml` konfigurerar den öppna frihets servern med Java EE-funktioner.
1. Kopiera `2-simple/pom.xml` till `1-start/pom.xml` .  Det här steget lägger till i `liberty-maven-plugin` Pom.
1. Ändra katalog till `1-start` av din lokala klon.
1. Kör `mvn clean package` i en-konsol för att generera ett War-paket `javaee-cafe.war` i katalogen `./target` .
1. Kör `mvn liberty:dev` för att starta öppen frihet i dev-läge.
1. Vänta tills servern startar. Konsolens utdata ska avslutas med följande meddelande:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Öppna `http://localhost:9080/` i webbläsaren för att gå till programmets start sida. Programmet kommer att se ut ungefär som på följande bild:

   ![Webb gränssnitt för Java-Cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Tryck på **CTRL + C** för att stoppa programmet och öppna frihets servern.

Katalogen `2-simple` för din lokala klon visar maven-projektet med ovanstående ändringar redan tillämpade.

## <a name="prepare-the-application-image"></a>Förbereda program avbildningen

Om du vill distribuera och köra ditt frihets program på ett ARO 4-kluster Använd du ditt program som en Docker-avbildning med hjälp av [Öppna frihets behållar avbildningar](https://github.com/OpenLiberty/ci.docker) eller [WebSphere frihets avbildningar](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Bygg program avbildning

Utför följande steg för att bygga program avbildningen:

1. Ändra katalog till `2-simple` av din lokala klon.
2. Kör `mvn clean package` för att paketera programmet.
3. Kör något av följande kommandon för att skapa program avbildningen.
   * Skapa med öppen frihets bas avbildning:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Bygg med WebSphere frihets bas avbildning:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Kör programmet lokalt med Docker

Innan du distribuerar det behållar programmet till ett fjärrkluster kan du köra med din lokala Docker för att kontrol lera om det fungerar:

1. Kör `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` i konsolen.
2. Vänta tills frihets servern har startats och att programmet har distribuerats.
3. Öppna `http://localhost:9080/` i webbläsaren för att gå till programmets start sida.
4. Tryck på **CTRL + C** för att stoppa programmet och frihets servern.

### <a name="push-the-image-to-the-container-image-registry"></a>Push-överför avbildningen till behållar avbildnings registret

När du är nöjd med programmets tillstånd kan du skicka det till det inbyggda registret för behållar avbildningar genom att följa anvisningarna nedan.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Logga in på OpenShift CLI som Azure AD-användare

1. Logga in på webb konsolen OpenShift från webbläsaren med autentiseringsuppgifterna för en Azure AD-användare.

   1. Använd en InPrivate-, Incognito-eller motsvarande webb läsar fönster funktion för att logga in i-konsolen.
   1. Välj **OpenID**

   > [!NOTE]
   > Anteckna användar namnet och lösen ordet som du använder för att logga in här. Det här användar namnet och lösen ordet fungerar som administratör för andra åtgärder i den här och andra artiklar.
1. Logga in med OpenShift CLI med hjälp av följande steg.  Den här processen kallas för diskussion `oc login` .
   1. I den högra delen av webb konsolen expanderar du snabb menyn för den inloggade användaren och väljer sedan **Kopiera inloggnings kommando**.
   1. Logga in på ett nytt flik-fönster med samma användare om det behövs.
   1. Välj **visnings-token**.
   1. Kopiera värdet i listan nedan **Logga in med denna token** till Urklipp och kör det i ett gränssnitt, som visas här.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Push-överför behållar avbildningen till behållar registret för OpenShift

Kör dessa kommandon för att skicka avbildningen till behållar registret för OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Lyckade utdata ser ut ungefär så här.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Push-överför avbildningen till det inbyggda registret för behållar avbildningar med följande kommando.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Distribuera program på ARO 4-klustret

Nu kan du distribuera exempel frihets programmet till det Azure Red Hat OpenShift 4-kluster som du skapade tidigare när du arbetade igenom kraven.

### <a name="deploy-the-application-from-the-web-console"></a>Distribuera programmet från webb konsolen

Eftersom vi använder operatorn Open frihet för att hantera frihets program, måste vi skapa en instans av dess *anpassade resurs definition*, av typen "OpenLibertyApplication". Operatören kommer sedan att ta hand om alla aspekter av hanteringen av de OpenShift-resurser som krävs för distribution.

1. Logga in på webb konsolen OpenShift från webbläsaren med autentiseringsuppgifterna för Azure AD-användaren.
1. Expandera **Start**, Välj **Projects**  >  **Open-frihet-demo**.
1. Navigera till **operatorer**  >  **installerade operatorer**.
1. I mitten av sidan väljer du **öppen frihets operator**.
1. I mitten av sidan väljer du **Öppna frihets program**.  Navigeringen mellan objekt i användar gränssnittet speglar den faktiska inne slutnings-hierarkin med tekniker som används.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java-inne slutning](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Välj **skapa OpenLibertyApplication**
1. Ersätt den genererade yaml med din, som finns på `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Välj **Skapa**. Du kommer att returneras till listan över OpenLibertyApplications.
1. Välj **Java-kafé – enkelt**.
1. I mitten av sidan väljer du **resurser**.
1. I tabellen väljer du länken för **Java-kafé – enkelt** med **typen** av **väg**.
1. På sidan som öppnas väljer du **länken nedan.**

Du ser program start sidan som öppnas i webbläsaren.

### <a name="delete-the-application-from-the-web-console"></a>Ta bort programmet från webb konsolen

När du är klar med programmet följer du stegen nedan för att ta bort programmet från öppna SKIFT.

1. I det vänstra navigerings fönstret expanderar du posten för **operatorer**.
1. Välj **installerade operatörer**.
1. Välj **Open frihet-operator**.
1. I mitten av sidan väljer du **Öppna frihets program**.
1. Välj den lodräta ellipsen (tre lodräta punkter) och välj sedan **ta bort Openfrihets program**.

### <a name="deploy-the-application-from-cli"></a>Distribuera programmet från CLI

I stället för att använda webb konsolens användar gränssnitt kan du distribuera programmet från CLI. Om du inte redan har gjort det kan du hämta och installera `oc` kommando rads verktyget genom att följa Red Hat-dokumentationen [komma igång med CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Logga in på webb konsolen OpenShift från webbläsaren med autentiseringsuppgifterna för Azure AD-användaren.
2. Logga in på OpenShift CLI med token för Azure AD-användaren.
3. Ändra katalogen till den `2-simple` lokala klonen och kör följande kommandon för att distribuera ditt frihets program till Aro 4-klustret.  Kommandoutdata visas också infogat.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Kontrol lera att `1/1` du ser under `READY` kolumnen innan du fortsätter.  Om inte, undersöker du och löser problemet innan du fortsätter.
5. Identifiera värden för väg till programmet med `oc get route` kommandot, som visas här.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   När frihets programmet är igång öppnar du resultatet av **väg värden** i webbläsaren för att gå till programmets start sida.

### <a name="delete-the-application-from-cli"></a>Ta bort programmet från CLI

Ta bort programmet från CLI genom att köra det här kommandot.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort ARO-klustret genom att följa stegen i [själv studie kursen: ta bort ett Azure Red Hat OpenShift 4-kluster](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Nästa steg

I den här hand boken har du lärt dig att:
> [!div class="checklist"]
>
> * Förbered frihets programmet
> * Bygg program avbildningen
> * Kör programmet för behållare i ett ARO 4-kluster med hjälp av GUI och CLI

Du kan lära dig mer om referenser som används i den här guiden:

* [Öppen frihet](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Öppen frihets operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Öppna frihets Server konfigurationen](https://openliberty.io/docs/ref/config/)
* [Frihets maven-plugin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Öppna frihets behållar avbildningar](https://github.com/OpenLiberty/ci.docker)
* [WebSphere frihets behållar avbildningar](https://github.com/WASdev/ci.docker)