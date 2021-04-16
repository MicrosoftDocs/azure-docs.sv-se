---
title: Snabbstart – Distribuera JBoss Enterprise Application Platform (EAP) på Red Hat Enterprise Linux (RHEL) till virtuella Azure-datorer och VM-skalningsuppsättningar
description: Så här distribuerar du Java-företagsprogram med Red Hat JBoss EAP på virtuella Azure RHEL-datorer och VM-skalningsuppsättningar.
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 18cf093adad858f50b2b1fa1c97e38821bd1c949
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538675"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Distribuera Java-företagsprogram till Azure med JBoss EAP på Red Hat Enterprise Linux

Azure-snabbstartsmallarna i den här artikeln visar hur du distribuerar [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) med [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) till virtuella Azure-datorer (VM) och VM-skalningsuppsättningar. Du använder en Java-exempelapp för att verifiera distributionen. 

JBoss EAP är en programserverplattform med öppen källkod. Det ger säkerhet, skalbarhet och prestanda i företagsklass för dina Java-program. RHEL är en plattform för operativsystem med öppen källkod (OS). Det möjliggör skalning av befintliga appar och utrullning av nya tekniker i alla miljöer. 

JBoss EAP och RHEL innehåller allt du behöver för att skapa, köra, distribuera och hantera Java-företagsprogram i alla miljöer. Kombinationen är en lösning med öppen källkod för lokala, virtuella miljöer och i privata, offentliga eller hybridmoln.

## <a name="prerequisites"></a>Förutsättningar 

* Ett Azure-konto med en aktiv prenumeration. Om du vill skaffa en Azure-prenumeration aktiverar du [dina Azure-krediter för Visual Studio prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) [eller skapar ett konto utan kostnad.](https://azure.microsoft.com/pricing/free-trial)

* JBoss EAP-installation. Du måste ha ett Red Hat-konto med behörigheten Red Hat Subscription Management (RHSM) för JBoss EAP. Med den här rättigheten kan du ladda ned den Red Hat-testade och certifierade JBoss EAP-versionen.  

  Om du inte har EAP-berättigande kan du skaffa en [JBoss EAP-utvärderingsprenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar. Om du vill skapa en ny Red Hat-prenumeration går du [till Red Hat-kundportalen](https://access.redhat.com/) och skapar ett konto.

* [Azure CLI](/cli/azure/overview).

* RHEL-alternativ. Välj Betala per du-go (PAYG) eller BYOS (Bring Your Own Subscription). Med BYOS måste du aktivera din [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image innan du distribuerar snabbstartsmallen.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migrering av Java EE- och Jakarta EE-program

### <a name="migrate-to-jboss-eap"></a>Migrera till JBoss EAP
JBoss EAP 7.2 och 7.3 är certifierade implementeringar av java-specifikationerna Enterprise Edition (Java EE) 8 och Jakarta EE 8. JBoss EAP innehåller förkonfigurerade alternativ för funktioner som hög tillgänglighetsklustring, meddelanden och distribuerad cachelagring. Det gör det också möjligt för användare att skriva, distribuera och köra program med hjälp av de olika API:er och tjänster som JBoss EAP tillhandahåller.  

Mer information om JBoss EAP finns i Introduktion till [JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) eller Introduktion till [JBoss EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)

 #### <a name="applications-on-jboss-eap"></a>Program på JBoss EAP

* **Webbtjänstprogram**. Webbtjänster är ett standard sätt att samarbeta mellan olika program. Varje program kan köras på olika plattformar och ramverk. Dessa webbtjänster underlättar intern och heterogen undersystemskommunikation. 

  Mer information finns i [Utveckla webbtjänstprogram på EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) eller [Utveckla webbtjänstprogram på EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)

* **Enterprise Java Beans -program (EJB).** EJB 3.2 är ett API för utveckling av distribuerade, transaktionella, säkra och portabla Java EE- och Jakarta EE-program. EJB använder komponenter på serversidan som kallas Enterprise Beans för att implementera affärslogiken i ett program på ett fristående sätt som uppmuntrar till återanvändning. 

  Mer information finns i Utveckla [EJB-program på EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) eller [Utveckla EJB-program på EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)

* **Vilolägesprogram**. Utvecklare och administratörer kan utveckla och distribuera JAVA Persistence API (JPA) och Hibernate-program med JBoss EAP. Hibernate Core är ett ramverk för objektrelationsmappning för Java-språket. Det ger ett ramverk för mappning av en objektorienterad domänmodell till en relationsdatabas, så att program kan undvika direkt interaktion med databasen. 

  Hibernate Entity Manager implementerar programmeringsgränssnitt och livscykelregler enligt definitionen i [JPA 2.1-specifikationen.](https://www.jcp.org/en/jsr/overview) Tillsammans med Hibernate-anteckningar implementerar den här omslutaren en komplett (och fristående) JPA-lösning ovanpå den mogna vilolägeskärnan. 
  
  Mer information om viloläge finns i JPA på [EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) eller [Jakarta Persistence på EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) är ett migreringsverktyg för Java-programservrar. Använd det här verktyget för att migrera från en annan appserver till JBoss EAP. Det fungerar med IDE-plugin-program för [Eclipse IDE,](https://www.eclipse.org/ide/) [Red Hat CodeReady-arbetsytor](https://developers.redhat.com/products/codeready-workspaces/overview) [och Visual Studio Code](https://code.visualstudio.com/docs/languages/java) för Java. 

MTA är ett kostnadsfritt verktyg med öppen källkod som:
* Automatiserar programanalys.
* Stöder beräkning av arbetsinsats.
* Påskyndar kodmigrering.
* Stöder containerisering.
* Integrerar med Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrera JBoss EAP från en lokal plats till Azure
Erbjudandet Azure Marketplace JBoss EAP på RHEL installeras och etableras på virtuella Azure-datorer på mindre än 20 minuter. Du kan komma åt dessa erbjudanden [från Azure Marketplace](https://azuremarketplace.microsoft.com/).

Det Azure Marketplace erbjudandet innehåller olika kombinationer av EAP- och RHEL-versioner som stöd för dina behov. JBoss EAP är alltid BYOS, men för RHEL OS kan du välja mellan BYOS eller PAYG. I Azure Marketplace finns alternativ för plan för JBoss EAP på RHEL som fristående eller klustrade virtuella datorer:

* JBoss EAP 7.2 på RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 på RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 på RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 på RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 på RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 på RHEL 8.0 VM (BYOS)

Tillsammans med Azure Marketplace-erbjudanden kan du använda snabbstartsmallar för att komma igång med din Azure-migreringsresa. De här snabbstarterna innehåller fördefinierade ARM Azure Resource Manager mallar och skript för att distribuera JBoss EAP på RHEL i olika konfigurationer och versionskombinationer. Du kommer att ha:

* En lastbalanserare.
* En privat IP-adress för belastningsutjämning och virtuella datorer.
* Ett virtuellt nätverk med ett enda undernät.
* VM-konfiguration (kluster eller fristående).
* Ett Java-exempelprogram.

Lösningsarkitekturen för dessa mallar omfattar:

* JBoss EAP på en fristående virtuell RHEL-dator.
* JBoss EAP klustrad över flera virtuella RHEL-datorer.
* JBoss EAP klustrad via skalningsuppsättningar för virtuella Azure-datorer.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrering av Linux-arbetsbelastningar för JBoss EAP
Azure Workload Builder förenklar processen för konceptbevis, utvärdering och migrering för lokala Java-appar till Azure. Workload Builder integreras med Azure Migrate Discovery för att identifiera JBoss EAP-servrar. Sedan genereras en Ansible-spelbok dynamiskt för distribution av JBoss EAP-server. Verktyget Red Hat MTA används för att migrera servrar från andra appservrar till JBoss EAP. 

Följande steg kan du vidta för att förenkla migreringen:
1. **Utvärdering**. Utvärdera JBoss EAP-kluster med hjälp av en virtuell Azure-dator eller en VM-skalningsuppsättning.
1. **Utvärdering**. Genomsöka program och infrastruktur.
1. **Infrastrukturkonfiguration**. Skapa en arbetsbelastningsprofil.
1. **Distribution och testning.** Distribuera, migrera och testa arbetsbelastningen.
1. **Konfiguration efter distribution.** Integrera med data, övervakning, säkerhet, säkerhetskopiering med mera.

## <a name="server-configuration-choice"></a>Val av serverkonfiguration

För distribution av den virtuella RHEL-datorn kan du välja antingen PAYG eller BYOS. Bilder från [Azure Marketplace](https://azuremarketplace.microsoft.com) payg som standard. Distribuera en RHEL VM av BYOS-typ om du har en egen RHEL OS-avbildning. Kontrollera att DITT RHSM-konto har BYOS-berättigande via Cloud Access innan du distribuerar VM eller VM-skalningsuppsättningen.

JBoss EAP har kraftfulla hanteringsfunktioner tillsammans med funktioner och API:er för sina program. Dessa hanteringsfunktioner varierar beroende på vilket driftläge du använder för att starta JBoss EAP. Det stöds på RHEL och Windows Server. JBoss EAP erbjuder ett fristående serverdriftsläge för hantering av diskreta instanser. Den erbjuder också ett hanterat domändriftsläge för hantering av grupper av instanser från en enda kontrollpunkt. 

> [!NOTE]
> JBoss EAP-hanterade domäner stöds inte Microsoft Azure eftersom Azure-infrastrukturtjänsterna hanterar HA-funktionen. 

Miljövariabeln `EAP_HOME` anger sökvägen till JBoss EAP-installationen. Använd följande kommando för att starta JBoss EAP-tjänsten i fristående läge:

```
$EAP_HOME/bin/standalone.sh
```
    
Det här startskriptet använder EAP_HOME/bin/standalone.conf för att ange vissa standardinställningar, till exempel JVM-alternativ. Du kan anpassa inställningarna i den här filen. JBoss EAP använder standalone.xml för att starta i fristående läge som standard, men du kan använda ett annat läge för att starta den. 

Mer information om tillgängliga fristående konfigurationsfiler och hur du använder dem finns i Fristående serverkonfigurationsfiler för [EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) eller fristående [serverkonfigurationsfiler för EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 

Om du vill starta JBoss EAP med en annan konfiguration använder du `--server-config` argumentet . Exempel:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Om du vill ha en fullständig lista över alla tillgängliga startskriptargument och deras syften använder du `--help` argumentet . Mer information finns i [Server Runtime Arguments on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) or [Server Runtime Arguments on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)(Argument för serverkörning på EAP 7.3).
    
JBoss EAP kan också fungera i klusterläge. Med JBoss EAP-klustermeddelanden kan du gruppera JBoss EAP-meddelandeservrar för att dela belastningen på meddelandebearbetningen. Varje aktiv nod i klustret är en aktiv JBoss EAP-meddelandeserver som hanterar sina egna meddelanden och hanterar sina egna anslutningar. Mer information finns i [Översikt över kluster på EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) eller Översikt över kluster på [EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) 

## <a name="support-and-subscription-notes"></a>Information om support och prenumerationer
De här snabbstartsmallarna erbjuds på följande sätt: 

- RHEL OS erbjuds som PAYG eller BYOS via Red Hat Gold Image-modellen.
- JBoss EAP erbjuds endast som BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Använda RHEL OS med PAYG-modellen

Som standard använder dessa snabbstartsmallar RHEL 7.7- eller 8.0 PAYG-avbildningen på begäran från Azure Marketplace. PAYG-avbildningar har ytterligare en RHEL-prenumerationsavgift per timme utöver de normala beräknings-, nätverks- och lagringskostnaderna. Samtidigt registreras instansen i din Red Hat-prenumeration. Det innebär att du kommer att använda en av dina rättigheter. 

Den här PAYG-avbildningen leder till "dubbel fakturering". Du kan undvika det här problemet genom att skapa en egen RHEL-avbildning. Mer information finns i Red Hat-kunskapsbasartikeln [Så här etablerar du en virtuell RHEL-dator för Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Eller aktivera din [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image.

Mer information om priser för virtuella PAYG-datorer finns [i Red Hat Enterprise Linux priser.](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) Om du vill använda RHEL i PAYG-modellen behöver du en Azure-prenumeration med den angivna betalningsmetoden för [RHEL 7.7 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) eller [RHEL 8.0 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Dessa erbjudanden kräver att en betalningsmetod anges i Azure-prenumerationen.

#### <a name="using-rhel-os-with-the-byos-model"></a>Använda RHEL OS med BYOS-modellen

Om du vill använda BYOS för RHEL OS måste du ha en giltig Red Hat-prenumeration med behörighet att använda RHEL OS i Azure. Slutför följande krav innan du distribuerar RHEL-operativsystemet med BYOS-modellen:

1. Kontrollera att du har RHEL OS- och JBoss EAP-rättigheter kopplade till din Red Hat-prenumeration.
2. Auktorisera ditt Azure-prenumerations-ID för att använda RHEL BYOS-avbildningar. Följ red [hat-prenumerationshanteringsdokumentationen](https://access.redhat.com/documentation/red_hat_subscription_management/1/) för att slutföra processen, som innehåller följande steg:

   1. Aktivera Microsoft Azure som leverantör i instrumentpanelen för Red Hat Cloud Access.

   1. Lägg till dina Azure-prenumerations-ID:er.

   1. Aktivera nya produkter för Cloud Access på Microsoft Azure.
    
   1. Aktivera Red Hat Gold Images för din Azure-prenumeration. Mer information finns i [Red Hat Gold Images on Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Vänta tills Red Hat Gold Images är tillgängligt i din Azure-prenumeration. Dessa avbildningar är vanligtvis tillgängliga inom tre timmar efter att de har skickas.
    
3. Godkänn Azure Marketplace för RHEL BYOS-avbildningar. Du kan slutföra den här processen genom att köra följande Azure CLI-kommandon. Mer information finns i dokumentationen [om RHEL BYOS Gold Images i Azure.](./byos.md) Det är viktigt att du kör den senaste Versionen av Azure CLI.

   1. Öppna en Azure CLI-session och autentisera med ditt Azure-konto. Mer information finns i [Logga in med Azure CLI.](/cli/azure/authenticate-azure-cli)

   1. Kontrollera att RHEL BYOS-avbildningarna är tillgängliga i din prenumeration genom att köra följande CLI-kommando. Om du inte får några resultat här ser du till att din Azure-prenumeration är aktiverad för RHEL BYOS-avbildningar.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Kör följande kommando för att Azure Marketplace villkoren för RHEL 7.7 BYOS respektive RHEL 8.0 BYOS:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Din prenumeration är nu redo att distribuera RHEL 7.7 eller 8.0 BYOS på virtuella Azure-datorer.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Använda JBoss EAP med BYOS-modellen

JBoss EAP är endast tillgängligt i Azure via BYOS-modellen. När du distribuerar den här mallen måste du ange dina RHSM-autentiseringsuppgifter tillsammans med RHSM-pool-ID:t med giltiga EAP-rättigheter. Om du inte har EAP-rättigheter kan du skaffa en [JBoss EAP-utvärderingsprenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar.

## <a name="deployment-options"></a>Distributionsalternativ

Du kan distribuera mallen på följande sätt:

- **PowerShell**. Distribuera mallen genom att köra följande kommandon: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Information om hur du installerar och konfigurerar Azure PowerShell finns i [PowerShell-dokumentationen.](/powershell/azure/)  

- **Azure CLI**. Distribuera mallen genom att köra följande kommandon:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Mer information om hur du installerar och konfigurerar Azure CLI finns [i Installera CLI.](/cli/azure/install-azure-cli)

- **Azure-portalen**. Du kan distribuera till Azure Portal genom att gå till Azure-snabbstartsmallarna enligt vad som anges i nästa avsnitt. När du är i snabbstarten väljer du knappen **Distribuera till Azure** eller Bläddra på **GitHub.**

## <a name="azure-quickstart-templates"></a>Azure-snabbstartsmallar

Du kan börja med någon av följande snabbstartsmallar för JBoss EAP på RHEL som uppfyller distributionsmålet:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/">JBoss EAP på RHEL (fristående virtuell dator).</a> Detta distribuerar en webbapp med namnet JBoss-EAP på Azure till JBoss EAP 7.2 eller 7.3 som körs på en virtuell RHEL 7.7- eller 8.0-dator.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/">JBoss EAP på RHEL (klustrad, flera virtuella datorer).</a> Detta distribuerar en webbapp med namnet eap-session-replication på ett JBoss EAP 7.2- eller 7.3-kluster som körs på *n* antal virtuella RHEL 7.7- eller 8.0-datorer. Användaren bestämmer *n-värdet.* Alla virtuella datorer läggs till i backend-poolen för en lastbalanserare.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/">JBoss EAP på RHEL (klustrad VM-skalningsuppsättning).</a> Detta distribuerar en webbapp med namnet eap-session-replication på ett JBoss EAP 7.2- eller 7.3-kluster som körs på RHEL 7.7- eller 8.0 VM-skalningsuppsättningar.

## <a name="resource-links"></a>Resurslänkar

* [Azure Hybrid-förmån](../../windows/hybrid-use-benefit-licensing.md)
* [Konfigurera en Java-app för Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP på Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP på Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Distribuera JBoss EAP på Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Läs mer om [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Läs mer om [Red Hat-prenumerationshantering.](https://access.redhat.com/products/red-hat-subscription-management)
* Läs mer om [Red Hat-arbetsbelastningar på Azure](./overview.md).
* Distribuera [JBoss EAP på en virtuell RHEL-dator eller VM-skalningsuppsättning från Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Distribuera [JBoss EAP på en virtuell RHEL-dator eller VM-skalningsuppsättning från Azure-snabbstartsmallar](https://aka.ms/Quickstart-JBoss-EAP).
