---
title: Snabbstart – WildFly på CentOS
description: Distribuera Java-program till WildFly på virtuella CentOS-datorer
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: bad9331a6d9a0c0936b1458e3aa4a84559e4a107
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533558"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Snabbstart: WildFly på CentOS 8

Den här snabbstarten visar hur du distribuerar den fristående noden för WildFly för virtuell CentOS 8-dator. Det är perfekt för utveckling och testning av Java-företagsprogram i Azure. Programserverprenumeration krävs inte för att distribuera den här snabbstarten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har en Azure-prenumeration kan du aktivera [dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) [eller skapa ett konto utan kostnad.](https://azure.microsoft.com/pricing/free-trial)

## <a name="use-case"></a>Användningsfall

WildFly är perfekt för utveckling och testning av Java-företagsprogram i Azure. Listor över tekniker som är tillgängliga i WildFly 18-serverkonfigurationsprofiler finns i [WildFly Komma igång Guide](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Du kan använda WildFly i fristående läge eller klusterläge enligt ditt användningsfall. Du kan säkerställa hög tillgänglighet för kritiska Jakarta EE-program av WildFly i ett kluster med noder. Gör ett litet antal ändringar i programkonfigurationen och distribuera sedan programmet i klustret. Mer information om detta finns i [WildFly-guiden för hög tillgänglighet.](https://docs.wildfly.org/18/High_Availability_Guide.html)

## <a name="configuration-choice"></a>Val av konfiguration

WildFly kan startas i fristående **serverläge** – en fristående serverinstans är en oberoende process, ungefär som en JBoss Application Server (AS) 3-, 4-, 5- eller 6-instans. Fristående instanser kan startas via standalone.sh eller standalone.bat starta skript. För mer än en fristående instans är det användarens ansvar att samordna hanteringen av flera servrar mellan servrarna.

Du kan också starta WildFly-instansen med en alternativ konfiguration med hjälp av konfigurationsfiler som är tillgängliga i konfigurationsmappen.

Följande är de fristående serverkonfigurationsfilerna:

- standalone.xml (standard) – Den här konfigurationen är standardfilen som används för att starta WildFly-instansen. Den innehåller jakarta-webbprofilscertifierad konfiguration med de tekniker som krävs.
   
- standalone-ha.xml – Jakarta EE Web Profile 8-certifierad konfiguration med hög tillgänglighet (riktad mot webbprogram).
   
- standalone-full.xml – Jakarta EE Platform 8-certifierad konfiguration, inklusive alla tekniker som krävs för att vara värd för Jakarta EE-program.

- standalone-full-ha.xml – Jakarta EE Platform 8-certifierad konfiguration med hög tillgänglighet som värd för Jakarta EE-program.

Om du vill starta en fristående WildFly-server med en annan tillhandahållen konfiguration använder du argumentet --server-config med filen server-config.

Om du till exempel vill använda Jakarta EE Platform 8 med klustringsfunktioner använder du följande kommando:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Mer information om konfigurationerna finns i [WildFly Komma igång Guide](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Information om licensiering, support och prenumeration

Azure CentOS 8-avbildningen är en PAYG-avbildning (Betala per användning) och kräver inte att användaren skaffar en licens. Första gången den virtuella datorn startas aktiveras den virtuella datorns OS-licens automatiskt och debiteras per timme. Detta är ett tillägg till Microsofts VM-priser per timme för Linux. Klicka på [Prissättning för virtuell Linux-dator](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) för mer information. WildFly är kostnadsfritt att ladda ned och använda och kräver ingen Red Hat-prenumeration eller licens.

## <a name="how-to-consume"></a>Så här använder du

Du kan distribuera mallen på följande tre sätt:

- Använd PowerShell – Distribuera mallen genom att köra följande kommandon: (Ta en [Azure PowerShell](/powershell/azure/) information om hur du installerar och konfigurerar Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Använd Azure CLI – Distribuera mallen genom att köra följande kommandon: (Kolla in [Azure Cross-Platform Command Line](/cli/azure/install-azure-cli) för mer information om hur du installerar och konfigurerar Azure Cross-Platform Command-Line Interface).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Använd Azure Portal – Distribuera mallen genom att klicka <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">här</a> och logga in på Azure Portal.

## <a name="arm-template"></a>ARM-mall

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 på CentOS 8 (fristående virtuell dator)</a> – Det här är en snabbstartsmall som skapar en fristående nod av WildFly 18.0.1.Final på den virtuella CentOS 8-datorn i resursgruppen (RG) som innehåller en privat IP-adress för den virtuella datorn, Virtual Network och diagnostiklagringskontot. Den distribuerar också ett Java-exempelprogram med namnet JBoss-EAP på Azure på WildFly.

## <a name="resource-links"></a>Resurslänkar

* Läs mer om [WildFly 18](https://docs.wildfly.org/18/)
* Läs mer om [Linux-distributioner på Azure](../../linux/endorsed-distros.md)
* [Dokumentation om Azure för Java-utvecklare](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nästa steg

För produktionsmiljö kan du ta en titt på ARM-mallarna för Red Hat JBoss EAP Azure-snabbstart:

Fristående virtuell RHEL-dator med exempelprogram:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP på RHEL (fristående virtuell dator)</a>

Klustrade virtuella RHEL-datorer med exempelprogram:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP på RHEL (klustrade virtuella datorer)</a>

Klustrad RHEL VM-skalningsuppsättning med exempelprogram:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP på RHEL (klustrad VM-skalningsuppsättning)</a>
