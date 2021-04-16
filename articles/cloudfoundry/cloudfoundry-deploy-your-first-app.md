---
title: Distribuera din första app till Cloud Foundry på Microsoft Azure
description: Distribuera ett program till Cloud Foundry på Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 1cffc36cbd4f24bbcbb5996a323ffa963e311693
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530961"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuera din första app till Cloud Foundry på Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) är en populär programplattform med öppen källkod som finns på Microsoft Azure. I den här artikeln visar vi hur du distribuerar och hanterar ett program på Cloud Foundry i en Azure-miljö.

## <a name="create-a-cloud-foundry-environment"></a>Skapa en Cloud Foundry miljö

Det finns flera alternativ för att skapa en Cloud Foundry miljö i Azure:

- Använd [pivotal-Cloud Foundry i][pcf-azuremarketplace] Azure Marketplace för att skapa en standardmiljö som innehåller PCF Ops Manager och Azure Service Broker. Du hittar fullständiga [anvisningar för][pcf-azuremarketplace-pivotaldocs] att distribuera Marketplace-erbjudandet i Pivotal-dokumentationen.
- Skapa en anpassad miljö genom [att distribuera Pivotal Cloud Foundry manuellt][pcf-custom].
- [Distribuera paketet med öppen källkod Cloud Foundry genom][oss-cf-bosh] att konfigurera en [BOSH-katalog,](https://bosh.io) en virtuell dator som samordnar distributionen av Cloud Foundry miljön.

> [!IMPORTANT] 
> Om du distribuerar PCF från Azure Marketplace ska du anteckna SYSTEMDOMAINURL och de administratörsautentiseringsuppgifter som krävs för att få åtkomst till Pivotal Apps Manager. Båda beskrivs i distributionsguiden för Marketplace. De behövs för att slutföra den här självstudien. För Marketplace-distributioner har SYSTEMDOMAINURL formen `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Ansluta till molnstyrenheten

Molnstyrenheten är den primära startpunkten för en Cloud Foundry för distribution och hantering av program. Kärn-API:et för molnkontrollant ( MOCKPI) är REST API, men det är tillgängligt via olika verktyg. I det här fallet interagerar vi med den via [Cloud Foundry CLI][cf-cli]. Du kan installera CLI på Linux, macOS eller Windows, men om du inte vill installera det alls är det tillgängligt förinstallerat i [Azure Cloud Shell][cloudshell-docs].

Logga in genom att gå till den `api` SYSTEMDOMAINURL som du fick från Marketplace-distributionen. Eftersom standarddistributionen använder ett själv signerat certifikat bör du även inkludera `skip-ssl-validation` växeln .

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du uppmanas att logga in på molnstyrenheten. Använd autentiseringsuppgifterna för administratörskontot som du har köpt från distributionsstegen för Marketplace.

Cloud Foundry tillhandahåller *organisationer och* *utrymmen som* namnrymder för att isolera teamen och miljöerna i en delad distribution. PCF Marketplace-distributionen  innehåller standardsystemorganisationen och en uppsättning utrymmen som skapats för att innehålla baskomponenterna, till exempel autoskalningstjänsten och Azure Service Broker. Välj systemutrymme för *tillfället.*


## <a name="create-an-org-and-space"></a>Skapa en organisation och ett utrymme

Om du `cf apps` skriver visas en uppsättning systemprogram som har distribuerats i systemutrymmet i systemorganisationen. 

Du bör behålla *systemorganisationen* reserverad för systemprogram, så skapa en organisation och ett utrymme för vårt exempelprogram.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Använd målkommandot för att växla till den nya organisationen och utrymmet:

```bash
cf target -o testorg -s dev
```

När du distribuerar ett program skapas det nu automatiskt i den nya organisationen och i det nya utrymmet. Bekräfta att det för närvarande inte finns några appar i den nya organisationen/utrymmet genom att skriva `cf apps` igen.

> [!NOTE] 
> Mer information om organisationer och utrymmen och hur de kan användas för Cloud Foundry rollbaserad åtkomstkontroll (Cloud Foundry RBAC) finns i [Cloud Foundry dokumentationen][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuera ett program

Vi använder ett exempelprogram Cloud Foundry Hello Spring Cloud, som är skrivet i Java och baserat på [Spring Framework](https://spring.io) [och Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klona Hello Spring Cloud databasen

Hello Spring Cloud-exempelprogrammet finns på GitHub. Klona den till din miljö och ändra till den nya katalogen:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Skapa programmet

Skapa appen med [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuera programmet med cf push

Du kan distribuera de flesta program Cloud Foundry med hjälp av `push` kommandot :

```bash
cf push
```

När du *push-Cloud Foundry* ett program identifierar Cloud Foundry typ av program (i det här fallet en Java-app) och identifierar dess beroenden (i det här fallet Spring-ramverket). Sedan paketerar den allt som krävs för att köra koden i en fristående containeravbildning, som kallas en *droplet*. Slutligen Cloud Foundry programmet på en av de tillgängliga datorerna i din miljö och skapar en URL där du kan nå den, som är tillgänglig i kommandots utdata.

![Utdata från push-kommandot cf][cf-push-output]

Om du vill se hello-spring-cloud-programmet öppnar du den angivna URL:en i webbläsaren:

![Standardgränssnitt för Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Mer information om vad som händer under `cf push` finns i How Applications Are [Staged i][cf-push-docs] Cloud Foundry dokumentationen.

## <a name="view-application-logs"></a>Visa programloggar

Du kan använda Cloud Foundry CLI för att visa loggar för ett program efter dess namn:

```bash
cf logs hello-spring-cloud
```

Som standard använder kommandot logs *tail*, som visar nya loggar när de skrivs. Om du vill se nya loggar uppdaterar du hello-spring-cloud-appen i webbläsaren.

Om du vill visa loggar som redan har skrivits lägger du till `recent` växeln:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skala programmet

Som standard skapar `cf push` endast en enda instans av ditt program. För att säkerställa hög tillgänglighet och aktivera utskalning för högre dataflöde vill du vanligtvis köra mer än en instans av dina program. Du kan enkelt skala ut redan distribuerade program med hjälp av `scale` kommandot :

```bash
cf scale -i 2 hello-spring-cloud
```

Om du `cf app` kör kommandot i programmet ser du Cloud Foundry skapar en annan instans av programmet. När programmet har startats startar Cloud Foundry belastningsutjämnande trafik till den.


## <a name="next-steps"></a>Nästa steg

- [Läs Cloud Foundry dokumentationen][cloudfoundry-docs]
- [Konfigurera Plugin-programmet Azure DevOps Services för Cloud Foundry][vsts-plugin]
- [Konfigurera Microsoft Log Analytics Nozzle för Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/ops-manager/2-10/install/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
