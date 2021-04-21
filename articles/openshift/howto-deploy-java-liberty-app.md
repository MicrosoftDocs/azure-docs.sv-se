---
title: Distribuera ett Java-program med OpenSphere/WebSphereSphere På ett Azure Red Hat OpenShift 4-kluster
description: Distribuera ett Java-program med Open Javascript/WebSphereSphere På ett Azure Red Hat OpenShift 4-kluster.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-java, websphere-javascript, aro, openshift, red hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783583"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Distribuera ett Java-program med OpenSphere/WebSphereSphere På ett Azure Red Hat OpenShift 4-kluster

Den här guiden visar hur du kör ditt Java-, Java EE-, [Jakarta EE-](https://jakarta.ee/)eller [MicroProfile-program](https://microprofile.io/) på OpenSphere/WebSphereSphere-körningen och sedan distribuerar det containerbaserade programmet till ett Azure Red Hat OpenShift(ARO) 4-kluster med hjälp av Open Operator. I den här artikeln får du hjälp med att förbereda ett Program För program, skapa programmets Docker-avbildning och köra det containeriserade programmet i ett ARO 4-kluster.  Mer information om Open Project finns på [sidan för Open Project Project](https://openliberty.io/). Mer information om IBM WebSphereSphere finns [på produktsidan WebSphereSphere.](https://www.ibm.com/cloud/websphere-liberty)

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför följande förutsättningar för att gå igenom den här guiden.

> [!NOTE]
> Azure Red Hat OpenShift kräver minst 40 kärnor för att skapa och köra ett OpenShift-kluster. Standardkvoten för Azure-resurser för en ny Azure-prenumeration uppfyller inte det här kravet. Information om hur du begär en ökning av resursgränsen finns i [Standardkvot: Öka gränserna med VM-serien](../azure-portal/supportability/per-vm-quota-requests.md). Observera att den kostnadsfria utvärderingsprenumerationen inte är berättigad till en kvotökning, uppgradera till en Betala per [prenumeration](../cost-management-billing/manage/upgrade-azure-subscription.md) innan du begär en kvotökning.

1. Förbered en lokal dator med Unix-liknande operativsystem installerat (till exempel Ubuntu, macOS).
1. Installera en Java SE-implementering (till exempel [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Installera [Maven](https://maven.apache.org/download.cgi) 3.5.0 eller senare.
1. Installera [Docker](https://docs.docker.com/get-docker/) för ditt operativsystem.
1. Installera [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 eller senare.
1. Kontrollera och [`envsubst`](https://command-not-found.com/envsubst) installera om det inte är förinstallerat i operativsystemet.
1. Klona koden för det här exemplet på det lokala systemet. Exemplet finns på [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Följ anvisningarna i [Skapa ett Azure Red Hat OpenShift 4-kluster.](./tutorial-create-cluster.md)

   Även om steget Hämta en Red Hat-pull-hemlighet är märkt som valfritt **krävs det för den här artikeln.**  Pull-hemligheten gör att Azure Red Hat OpenShift klustret kan hitta Open Operator.

   Om du planerar att köra minnesintensiva program i klustret anger du rätt storlek på den virtuella datorn för arbetsnoderna med hjälp av `--worker-vm-size` parametern . är till exempel `Standard_E4s_v3` den minsta virtuella datorstorleken för att installera Elasticsearch-operatorn på ett kluster. Mer information finns i:

   * [Azure CLI för att skapa ett kluster](/cli/azure/aro#az_aro_create)
   * [Storlekar på virtuella datorer som stöds för minnesoptimerad](./support-policies-v4.md#memory-optimized)
   * [Krav för att installera Elasticsearch-operatorn](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Anslut till klustret genom att följa stegen i [Anslut till ett Azure Red Hat OpenShift 4-kluster.](./tutorial-connect-cluster.md)
   * Se till att följa stegen i "Installera OpenShift CLI" eftersom vi ska använda kommandot `oc` senare i den här artikeln.
   * Skriv ned klusterkonsol-URL:en som ser ut så `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` här: .
   * Anteckna `kubeadmin` autentiseringsuppgifterna.

1. Kontrollera att du kan logga in på OpenShift CLI med token för användaren `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Aktivera det inbyggda containerregistret för OpenShift

Stegen i den här självstudien skapar en Docker-avbildning som måste skickas till ett containerregister som är tillgängligt för OpenShift. Det enklaste alternativet är att använda det inbyggda registret som tillhandahålls av OpenShift. Om du vill aktivera det inbyggda containerregistret följer du stegen i [Konfigurera det inbyggda containerregistret för Azure Red Hat OpenShift 4](built-in-container-registry.md). Tre objekt från dessa steg används i den här artikeln.

* Användarnamnet och lösenordet för Azure AD-användaren för att logga in på OpenShift-webbkonsolen.
* Utdata från `oc whoami` efter att ha följt stegen för att logga in på OpenShift CLI.  Det här värdet kallas **aad-user** för diskussion.
* Containerregistrets URL.

Observera de här objekten när du slutför stegen för att aktivera det inbyggda containerregistret.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Skapa ett OpenShift-namnområde för Java-appen

1. Logga in på OpenShift-webbkonsolen från webbläsaren med `kubeadmin` autentiseringsuppgifterna.
2. Gå till  >  **Administrationsnamnområden**  >  **Skapa namnområde**.
3. Fyll i `open-liberty-demo` som **Namn** och välj **Skapa**, som du ser härnäst.

   ![skapa namnområde](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Skapa en administratör för demoprojektet

Förutom avbildningshantering beviljas **aad-användaren** även administratörsbehörighet för att hantera resurser i demoprojektet för ARO 4-klustret.  Logga in på OpenShift CLI och ge användaren de **behörigheter som** krävs genom att följa dessa steg.

1. Logga in på OpenShift-webbkonsolen från webbläsaren med `kubeadmin` autentiseringsuppgifterna.
1. Expandera snabbmenyn för den inloggade användaren längst upp till höger i webbkonsolen och välj sedan **Kopiera inloggningskommando.**
1. Logga in på ett nytt flikfönster med samma användare om det behövs.
1. Välj **Visa token**.
1. Kopiera värdet som anges nedan **Inloggning med denna token** till Urklipp och kör det i ett gränssnitt, som du ser här.
1. Kör följande kommandon för att `admin` ge rollen till **aad-user** i namnområdet `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Installera Open Shift OpenShift-operatorn

När du har skapat och anslutit till klustret installerar du Open Operator.  Huvudstartsidan för Open Operator finns på [GitHub.](https://github.com/OpenLiberty/open-liberty-operator)

1. Logga in på OpenShift-webbkonsolen från webbläsaren med `kubeadmin` autentiseringsuppgifterna.
2. Gå till  >  **OperatorHub och** sök efter **Open Operator**.
3. Välj **Open Operator Operator (Öppna operatör)** i sökresultatet.
4. Välj **installera**.
5. I popup-rutan Skapa operatörsprenumeration markerar du Alla namnområden i klustret (standard) för **Installationsläge,**  **beta** för **Uppdateringskanal** och **Automatisk** **för godkännandestrategi:** 

   ![skapa operatörsprenumeration för Open Operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Välj **Prenumerera** och vänta någon minut tills Open Operator visas.
7. Observera Open Operator operator (Öppen operatör) med statusen "Succeeded" (Lyckades).  Om du inte gör det kan du diagnostisera och lösa problemet innan du fortsätter.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Installerade operatorer som visar Att Öppna 1666 har installerats.":::

## <a name="prepare-the-liberty-application"></a>Förbereda Programmet

Vi använder ett Java EE 8-program som exempel i den här guiden. Open Javascript är en [java EE 8-server som är kompatibel](https://javaee.github.io/javaee-spec/javadocs/) med fullständig profil så att den enkelt kan köra programmet.  Open Java är också Jakarta EE 8 full profile compatible ( Fullständig [profilkompatibel med Jakarta EE 8).](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>Köra programmet på Open York

Om du vill köra programmet på Open Plugins måste du skapa en Open Plugins-serverkonfigurationsfil så att [Plugin-programmet För Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) kan paketera programmet för distribution. Plugin-programmet För Maven krävs inte för att distribuera programmet till OpenShift.  Men vi använder det i det här exemplet med Open Developer-läget för utvecklare (dev).  Med utvecklarläget kan du enkelt köra programmet lokalt. Utför följande steg på den lokala datorn.

1. Kopiera `2-simple/src/main/liberty/config/server.xml` till och skriv över den befintliga `1-start/src/main/liberty/config` nolllängdsfilen. Detta `server.xml` konfigurerar Open Javascript-servern med Java EE-funktioner.
1. Kopiera `2-simple/pom.xml` till `1-start/pom.xml` .  Det här steget lägger `liberty-maven-plugin` till i POM.
1. Ändra katalogen till `1-start` för den lokala klonen.
1. Kör `mvn clean package` i en konsol för att generera ett war-paket i katalogen `javaee-cafe.war` `./target` .
1. Kör `mvn liberty:dev` för att starta Open Dev i utvecklingsläge.
1. Vänta tills servern startar. Konsolens utdata bör sluta med följande meddelande:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Öppna `http://localhost:9080/` i webbläsaren för att besöka programmets startsida. Programmet ser ut ungefär som på följande bild:

   ![JavaEE Java Java Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Tryck **på Control-C** för att stoppa programmet och OpenSession-servern.

Katalogen för `2-simple` den lokala klonen visar Maven-projektet med ovanstående ändringar redan tillämpade.

## <a name="prepare-the-application-image"></a>Förbereda programavbildningen

Om du vill distribuera och köra ditt Program På ett ARO 4-kluster kan du använda programmet som en Docker-avbildning med Hjälp av [Open Container-containeravbildningar](https://github.com/OpenLiberty/ci.docker) eller [WebSphereSphere-containeravbildningar.](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Skapa programavbildning

Skapa programavbildningen genom att utföra följande steg:

1. Ändra katalogen till `2-simple` för den lokala klonen.
2. Kör `mvn clean package` för att paketera programmet.
3. Kör något av följande kommandon för att skapa programavbildningen.
   * Skapa med Open Base-basavbildning:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Skapa med basavbildningen WebSphereSphereSphere:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Köra programmet lokalt med Docker

Innan du distribuerar det containeriserade programmet till ett fjärrkluster kör du med din lokala Docker för att kontrollera om det fungerar:

1. Kör `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` i konsolen.
2. Vänta tills Server har startat och programmet har distribuerats.
3. Öppna `http://localhost:9080/` i webbläsaren för att besöka programmets startsida.
4. Tryck **på Control-C** för att stoppa programmet och Webbserver.

### <a name="push-the-image-to-the-container-image-registry"></a>Push-skicka avbildningen till containeravbildningsregistret

När du är nöjd med programmets tillstånd kan du skicka det till det inbyggda containeravbildningsregistret genom att följa anvisningarna nedan.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Logga in på OpenShift CLI som Azure AD-användare

1. Logga in på OpenShift-webbkonsolen från webbläsaren med autentiseringsuppgifterna för en Azure AD-användare.

   1. Använd en InPrivate-, Incognito- eller någon annan motsvarande webbläsarfönsterfunktion för att logga in på konsolen.
   1. Välj **openid**

   > [!NOTE]
   > Anteckna det användarnamn och lösenord som du använder för att logga in här. Det här användarnamnet och lösenordet fungerar som administratör för andra åtgärder i den här och andra artiklar.
1. Logga in med OpenShift CLI med hjälp av följande steg.  För diskussion kallas den här processen `oc login` .
   1. Expandera snabbmenyn för den inloggade användaren längst upp till höger i webbkonsolen och välj sedan **Kopiera inloggningskommando.**
   1. Logga in på ett nytt flikfönster med samma användare om det behövs.
   1. Välj **Visa token**.
   1. Kopiera värdet som anges nedan **Inloggning med denna token** till Urklipp och kör det i ett gränssnitt, som du ser här.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Push-skicka containeravbildningen till containerregistret för OpenShift

Kör dessa kommandon för att skicka avbildningen till containerregistret för OpenShift.

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

Skicka avbildningen till det inbyggda containeravbildningsregistret med följande kommando.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Distribuera program i ARO 4-klustret

Nu kan du distribuera Exempelprogrammet till det Azure Red Hat OpenShift 4-kluster som du skapade tidigare när du gick igenom förutsättningarna.

### <a name="deploy-the-application-from-the-web-console"></a>Distribuera programmet från webbkonsolen

Eftersom vi använder Open Operator Operator för att hantera Applications måste vi skapa en instans av dess *anpassade resursdefinition* av typen "OpenLibertyApplication". Operatören tar sedan hand om alla aspekter av hanteringen av de OpenShift-resurser som krävs för distributionen.

1. Logga in på OpenShift-webbkonsolen från webbläsaren med autentiseringsuppgifterna för Azure AD-användaren.
1. Expandera **Start**, välj **Projekt**  >  **open-demonstration**.
1. Gå till **Operatorer**  >  **Installerade operatorer**.
1. Välj **Open Operator** i mitten av sidan.
1. Välj Öppna Program mitt på **sidan.**  Navigeringen av objekt i användargränssnittet speglar den faktiska inneslutningshierarkin med tekniker som används.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java-inneslutning](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Välj **Skapa OpenLibertyApplication**
1. Ersätt den genererade yaml-koden med din, som finns på `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Välj **Skapa**. Du kommer tillbaka till listan över OpenLibertyApplications.
1. Välj **javaee-javaee-simple**.
1. Välj Resurser mitt på **sidan.**
1. I tabellen väljer du länken för **javaee-kafé-simple** med **typen** av **väg**.
1. På sidan som öppnas väljer du länken under **Plats**.

Programmets startsida öppnas i webbläsaren.

### <a name="delete-the-application-from-the-web-console"></a>Ta bort programmet från webbkonsolen

När du är klar med programmet följer du dessa steg för att ta bort programmet från Open Shift.

1. Expandera posten för Operatorer i det vänstra **navigeringsfönstret.**
1. Välj **Installerade operatorer.**
1. Välj **Open Operator operator (Öppna operatör).**
1. Välj Öppna Program mitt på **sidan.**
1. Välj den lodräta ellipsen (tre lodräta punkter) och välj sedan **Ta bort OpenLiberty-program.**

### <a name="deploy-the-application-from-cli"></a>Distribuera programmet från CLI

I stället för att använda webbkonsolens grafiska användargränssnitt kan du distribuera programmet från CLI. Om du inte redan gjort det laddar du ned och installerar kommandoradsverktyget genom att följa `oc` Red [Hat-dokumentationen Komma igång med CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Logga in på OpenShift-webbkonsolen från webbläsaren med autentiseringsuppgifterna för Azure AD-användaren.
2. Logga in på OpenShift CLI med token för Azure AD-användaren.
3. Byt katalog `2-simple` till för den lokala klonen och kör följande kommandon för att distribuera Ditt Program Till ARO 4-klustret.  Kommandoutdata visas också infogade.

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

4. Kontrollera att du `1/1` ser under kolumnen innan du `READY` fortsätter.  Annars undersöker och löser du problemet innan du fortsätter.
5. Identifiera värden för vägen till programmet med kommandot `oc get route` , som du ser här.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   När Programmet Är igång öppnar du utdata från **Route Host i** webbläsaren för att besöka programmets startsida.

### <a name="delete-the-application-from-cli"></a>Ta bort programmet från CLI

Ta bort programmet från CLI genom att köra det här kommandot.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort ARO-klustret genom att följa stegen i [Självstudie: Ta bort ett Azure Red Hat OpenShift 4-kluster](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig att:
> [!div class="checklist"]
>
> * Förbereda Programmet Förser dig med
> * Skapa programavbildningen
> * Kör det containeriserade programmet på ett ARO 4-kluster med hjälp av det grafiska användargränssnittet och CLI

Du kan lära dig mer från referenser som används i den här guiden:

* [Open IoT](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Konfiguration av Öppna server](https://openliberty.io/docs/ref/config/)
* [Plugin-program för Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Öppna ContainerAvbildningar](https://github.com/OpenLiberty/ci.docker)
* [WebSphereSphere Container Images](https://github.com/WASdev/ci.docker)