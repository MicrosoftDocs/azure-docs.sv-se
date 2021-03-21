---
title: Distribuera din första app till Cloud Foundry på Microsoft Azure
description: Distribuera ett program till Cloud Foundry på Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 037ac972dca49484f7b8c0ad8eed6942c901b997
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562936"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuera din första app till Cloud Foundry på Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) är en populär program plattform med öppen källkod som finns på Microsoft Azure. I den här artikeln visar vi hur du distribuerar och hanterar ett program på Cloud Foundry i en Azure-miljö.

## <a name="create-a-cloud-foundry-environment"></a>Skapa en Cloud Foundry-miljö

Det finns flera alternativ för att skapa en Cloud Foundry-miljö på Azure:

- Använd [pivotal Cloud Foundry-erbjudandet][pcf-azuremarketplace] på Azure Marketplace för att skapa en standard miljö som innehåller PCF Ops Manager och Azure Service Broker. Du hittar [fullständiga instruktioner][pcf-azuremarketplace-pivotaldocs] för att distribuera Marketplace-erbjudandet i Pivot-dokumentationen.
- Skapa en anpassad miljö genom att [distribuera pivotal Cloud Foundry manuellt][pcf-custom].
- [Distribuera Cloud Foundry-paket med öppen källkod direkt][oss-cf-bosh] genom att konfigurera en [Bosh](https://bosh.io) -regissör, en virtuell dator som samordnar distributionen av Cloud foundrys miljön.

> [!IMPORTANT] 
> Om du distribuerar PCF från Azure Marketplace noterar du de SYSTEMDOMAINURL och de autentiseringsuppgifter som krävs för att få åtkomst till hanteraren för pivoting Apps, som beskrivs i distributions guiden för Marketplace. De behövs för att slutföra den här självstudien. För Marketplace-distributioner finns SYSTEMDOMAINURL i formuläret `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Ansluta till moln styrenheten

Moln styrenheten är den primära start punkten för en Cloud Foundry miljö för distribution och hantering av program. CCAPI (Core Cloud Controller API) är ett REST API, men det är tillgängligt via olika verktyg. I det här fallet interagerar vi med det via [Cloud FOUNDRY CLI][cf-cli]. Du kan installera CLI på Linux, macOS eller Windows, men om du hellre inte vill installera det alls är det tillgängligt för hands installerat i [Azure Cloud Shell][cloudshell-docs].

Logga in genom att lägga `api` till den SYSTEMDOMAINURL som du har fått från Marketplace-distributionen. Eftersom standard distributionen använder ett självsignerat certifikat bör du även inkludera `skip-ssl-validation` växeln.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du uppmanas att logga in på moln styrenheten. Använd de autentiseringsuppgifter för administratörs kontot som du har köpt från distributions stegen för Marketplace.

Cloud Foundry tillhandahåller *organisationer* och *blank steg* som namn rymder för att isolera team och miljöer inom en delad distribution. PCF Marketplace-distributionen innehåller standard *system* org och en uppsättning blank steg som skapats för att innehålla bas komponenterna, t. ex. tjänsten för automatisk skalning och Azure Service Broker. För tillfället väljer du *system* utrymmet.


## <a name="create-an-org-and-space"></a>Skapa ett organisations-och utrymme

Om du skriver `cf apps` , ser du en uppsättning system program som har distribuerats i system utrymmet i system-org. 

Du bör se till att *system* org reserverat för System program, så skapa ett organisations-och utrymme för att House vårt exempel program.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Använd mål kommandot för att växla till det nya organisationen och utrymmet:

```bash
cf target -o testorg -s dev
```

Nu när du distribuerar ett program skapas det automatiskt i det nya org och utrymmet. För att bekräfta att det inte finns några appar i det nya org/utrymmet, skriver du `cf apps` igen.

> [!NOTE] 
> Mer information om organisationer och blank steg och hur de kan användas för Cloud Foundry rollbaserad åtkomst kontroll (Cloud Foundry RBAC) finns i [Cloud Foundry-dokumentationen][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuera ett program

Vi använder ett exempel Cloud Foundry program som kallas Hello våren Cloud, som är skrivet i Java och som baseras på [våren-ramverket](https://spring.io) och [våren Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klona Hello våren Cloud-lagringsplatsen

Exempel programmet Hello våren Cloud är tillgängligt på GitHub. Klona den till din miljö och ändra till den nya katalogen:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Skapa programmet

Bygg appen med [Apache maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuera programmet med CF-push

Du kan distribuera de flesta program till Cloud Foundry med hjälp av `push` kommandot:

```bash
cf push
```

När du *push* -överför ett program identifierar Cloud Foundry program typen (i det här fallet en Java-app) och identifierar dess beroenden (i det här fallet våren-ramverket). Sedan paketeras allt som krävs för att köra din kod i en fristående behållar avbildning som kallas *droplet*. Slutligen Cloud Foundry schemalägger programmet på någon av de tillgängliga datorerna i din miljö och skapar en URL där du kan komma åt den, vilket är tillgängligt i kommandots utdata.

![Utdata från CF push-kommando][cf-push-output]

Öppna den URL som visas i webbläsaren om du vill se programmet Hello-våren-Cloud:

![Standard gränssnitt för Hello våren Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Mer information om vad som händer `cf push` finns i [hur program mellanlagras][cf-push-docs] i Cloud Foundry-dokumentationen.

## <a name="view-application-logs"></a>Visa program loggar

Du kan använda Cloud Foundry CLI för att visa loggar för ett program med hjälp av namnet:

```bash
cf logs hello-spring-cloud
```

Som standard använder loggar kommandot *pilslut*, som visar nya loggar när de skrivs. Om du vill se nya loggar visas uppdaterar du appen Hello-våren-Cloud i webbläsaren.

Om du vill visa loggar som redan har skrivits lägger du till `recent` växeln:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skala programmet

Som standard `cf push` skapar bara en enda instans av programmet. För att säkerställa hög tillgänglighet och för att skala ut för högre data flöde vill du ofta köra fler än en instans av dina program. Du kan enkelt skala ut redan distribuerade program med hjälp av `scale` kommandot:

```bash
cf scale -i 2 hello-spring-cloud
```

Att köra `cf app` kommandot i programmet visar att Cloud Foundry skapar en annan instans av programmet. När programmet har startats påbörjar Cloud Foundry automatiskt belastnings Utjämnings trafik till den.


## <a name="next-steps"></a>Nästa steg

- [Läs Cloud Foundry-dokumentationen][cloudfoundry-docs]
- [Konfigurera Azure DevOps Services-plugin-programmet för Cloud Foundry][vsts-plugin]
- [Konfigurera Microsoft Log Analytics munstycke för Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
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