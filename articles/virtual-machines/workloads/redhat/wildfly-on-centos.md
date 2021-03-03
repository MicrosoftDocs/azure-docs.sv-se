---
title: Snabbstart – WildFly på CentOS
description: Distribuera Java-program till WildFly på CentOS VM
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 533d2881688598430ca05e964595352edf993dff
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676006"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Snabb start: WildFly på CentOS 8

Den här snabb starten visar hur du distribuerar den fristående noden för WildFly av CentOS 8 VM. Den är idealisk för utveckling och testning av Java-program i Enterprise på Azure. Program Server prenumeration krävs inte för att distribuera den här snabb starten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera din [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) -prenumeration eller [skapa ett kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Användningsfall

WildFly är perfekt för utveckling och testning av Java-program i företag på Azure. Listor över tekniker som är tillgängliga i WildFly 18-Server konfigurations profiler finns i [WildFly komma igångs guide](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Du kan använda WildFly i antingen fristående eller kluster läge per användnings fall. Du kan garantera hög tillgänglighet för kritiska Jakarta EE-program genom WildFly på ett kluster med noder. Gör ett litet antal ändringar av program konfigurationen och distribuera sedan programmet i klustret. Om du vill veta mer om det här kontrollerar du [guiden för hög tillgänglighet för WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Konfigurations alternativ

WildFly kan startas i **fristående server** läge – en fristående Server instans är en oberoende process, ungefär som en JBoss-Programserver (as) 3, 4, 5 eller 6 instanser. Fristående instanser kan startas via standalone.sh eller standalone.bat starta skript. För fler än en fristående instans är det användarens ansvar att koordinera hantering av flera servrar på servrarna.

Du kan också starta WildFly-instansen med alternativ konfiguration genom att använda konfigurationsfiler som finns i mappen konfiguration.

Följande är de fristående server konfigurations filerna:

- standalone.xml (standard) – den här konfigurationen är den standard fil som används för att starta WildFly-instansen. Den innehåller Jakarta-certifierad konfiguration av webb profil med de tekniker som krävs.
   
- standalone-ha.xml-Jakarta EE Web Profile 8-certifierad konfiguration med hög tillgänglighet (riktad mot webb program).
   
- standalone-full.xml Jakarta EE Platform 8 Certified Configuration, inklusive alla tekniker som krävs för att vara värd för Jakarta EE-program.

- standalone-full-ha.xml-Jakarta EE Platform 8 Certified Configuration med hög tillgänglighet för att vara värd för Jakarta EE-program.

Om du vill starta en fristående WildFly-server med en annan angiven konfiguration använder du argumentet--server-config med Server konfigurations filen.

Om du till exempel vill använda Jakarta EE plattform 8 med kluster funktioner använder du följande kommando:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Om du vill veta mer om konfigurationerna kan du läsa mer om konfigurationen i [WildFly komma igång guide](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Anteckningar om licensiering, support och prenumeration

Azure CentOS 8-avbildningen är en virtuell dator avbildning med PAYG (betala per användning) och kräver inte att användaren skaffar en licens. Första gången den virtuella datorn startas aktive ras den virtuella datorns OS-licens automatiskt och debiteras per tim pris. Detta är i tillägg till Microsofts Linux-Tim taxa för virtuella datorer. Klicka på [priser för virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) för mer information. WildFly är kostnads fri att ladda ned och använda och kräver inte en Red Hat-prenumeration eller-licens.

## <a name="how-to-consume"></a>Hur man använder

Du kan distribuera mallen på följande tre sätt:

- Använd PowerShell – distribuera mallen genom att köra följande kommandon: (kolla [Azure PowerShell](/powershell/azure/) för information om hur du installerar och konfigurerar Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Använd Azure CLI – distribuera mallen genom att köra följande kommandon: (kolla in [Azures plattforms oberoende kommando rad](/cli/azure/install-azure-cli) om du vill ha mer information om hur du installerar och konfigurerar Azures plattforms oberoende Command-Line gränssnittet).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Använd Azure Portal – distribuera mallen genom att klicka <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">här</a> och logga in på din Azure Portal.

## <a name="arm-template"></a>ARM-mall

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 på CentOS 8 (fristående virtuell dator)</a> – det här är en snabb starts mall som skapar en fristående nod till WildFly 18.0.1. slutlig på CENTOS 8 VM i resurs gruppen (RG) som innehåller en privat IP-adress för den virtuella datorn, Virtual Network och diagnostik-lagrings konto. Det distribuerar också ett Java-exempel för Java-program med namnet JBoss-EAP på Azure på WildFly.

## <a name="resource-links"></a>Resurs länkar

* Läs mer om [WildFly 18](https://docs.wildfly.org/18/)
* Lär dig mer om [Linux-distributioner på Azure](../../linux/endorsed-distros.md)
* [Dokumentation om Azure för Java-utvecklare](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nästa steg

För produktions miljön kan du kolla in Red Hat JBoss EAP Azure snabb starts ARM mallar:

Fristående RHEL virtuell dator med exempel program:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss-EAP på RHEL (fristående virtuell dator)</a>

Klustrade virtuella RHEL-datorer med exempel program:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss-EAP på RHEL (klustrade virtuella datorer)</a>

RHEL virtuell dator skalnings uppsättning med exempel program:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP på RHEL (skalnings uppsättning för klustrad virtuell dator)</a>