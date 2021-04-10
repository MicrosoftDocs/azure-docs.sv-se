---
title: Snabb start – distribuera JBoss Enterprise Application Platform (EAP) på Red Hat Enterprise Linux (RHEL) till virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer
description: Distribuera Enterprise Java-program med Red Hat JBoss EAP på virtuella Azure RHEL-datorer och skalnings uppsättningar för virtuella datorer.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671975"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Distribuera Java-företagsprogram till Azure med JBoss EAP på Red Hat Enterprise Linux

Azure snabb starts-mallar i den här artikeln visar hur du distribuerar [JBoss (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) med [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) till virtuella Azure-datorer (VM) och virtuella datorers skalnings uppsättningar. Du ska använda ett exempel på en Java-app för att verifiera distributionen. 

JBoss EAP är en plattform för program server med öppen källkod. Den ger säkerhet, skalbarhet och prestanda i företags klass för dina Java-program. RHEL är en operativ system plattform med öppen källkod. Det möjliggör skalning av befintliga appar och att ta del av nya tekniker i alla miljöer. 

JBoss EAP och RHEL innehåller allt du behöver för att skapa, köra, distribuera och hantera företags-Java-program i valfri miljö. Kombinationen är en lösning med öppen källkod för lokala, virtuella miljöer och privata, offentliga eller hybrid moln.

## <a name="prerequisites"></a>Förutsättningar 

* Ett Azure-konto med en aktiv prenumeration. Om du vill skaffa en Azure-prenumeration aktiverar du dina [Azure-krediter för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller [skapar ett konto kostnads fritt](https://azure.microsoft.com/pricing/free-trial).

* JBoss-EAP-installation. Du måste ha ett Red Hat-konto med Red Hat Subscription Management (RHSM)-rättighet för JBoss EAP. Med den här rättigheten kan du ladda ned Red Hat tested-och Certified JBoss EAP-versionen.  

  Om du inte har EAP-rättighet kan du hämta en [JBoss EAP-utvärderings prenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar. Om du vill skapa en ny Red Hat-prenumeration går du till [Red Hat-kund Portal](https://access.redhat.com/) och konfigurerar ett konto.

* [Azure CLI](/cli/azure/overview).

* RHEL alternativ. Välj betala per användning (PAYG) eller ta din egen prenumeration (BYOS). Med BYOS måste du aktivera din [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-avbildning innan du distribuerar snabb starts mal len.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE-och Jakarta EE-programmigrering

### <a name="migrate-to-jboss-eap"></a>Migrera till JBoss EAP
JBoss EAP 7,2 och 7,3 är certifierade implementeringar av specifikationerna Java Enterprise Edition (Java EE) 8 och Jakarta EE 8. JBoss EAP innehåller förkonfigurerade alternativ för funktioner som hög tillgänglighet (HA), meddelanden och distribuerad cachelagring. Det gör det också möjligt för användarna att skriva, distribuera och köra program med hjälp av de olika API: er och tjänster som JBoss EAP tillhandahåller.  

Mer information om JBoss-EAP finns i [Introduktion till JBoss eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) eller [Introduktion till JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Program på JBoss EAP

* **Webb tjänst program**. Webb tjänster är ett standard sätt att samverka mellan program. Varje program kan köras på olika plattformar och ramverk. Dessa webb tjänster underlättar intern och heterogen kommunikation mellan system. 

  Läs mer i [utveckla webb tjänst program på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) eller [utveckla webb tjänst program på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **EJB-program (Enterprise Java bönor)**. EJB 3,2 är ett API för att utveckla distribuerade, transaktionella, säkra och portabla program för Java EE och Jakarta. EJB använder komponenter på Server sidan som kallas företags bönor för att implementera affärs logiken i ett program på ett frikopplat sätt som uppmuntrar åter användning. 

  Mer information finns i [utveckla EJB-program på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) eller [utveckla EJB-program på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Vilo läges program**. Utvecklare och administratörer kan utveckla och distribuera Java Persisted API (JPA) och vilo läges program med JBoss EAP. Vilo läge kärnan är ett ramverk för objekt Relations mappning för Java-språket. Det ger ett ramverk för att mappa en objektorienterad domän modell till en Relations databas, så att program kan undvika direkt interaktion med databasen. 

  Vilo läge för enhets hanteraren implementerar programmerings gränssnitten och livs cykel reglerna som definieras i [JPA 2,1-specifikationen](https://www.jcp.org/en/jsr/overview). Tillsammans med vilo läges anteckningar implementerar den här omslutningen en komplett (och fristående) JPA-lösning ovanpå den äldre vilo läges kärnan. 
  
  Mer information om vilo läge finns i [JPA on eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) eller  [Jakarta persisted on EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit för program
[Red Hat Migration Toolkit för program (MTA)](https://developers.redhat.com/products/mta/overview) är ett Migreringsverktyg för Java-programservrar. Använd det här verktyget om du vill migrera från en annan app server till JBoss EAP. Det fungerar med IDE-plugin-program för [sol FÖRMÖRKELSE IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady-arbetsytor](https://developers.redhat.com/products/codeready-workspaces/overview)och [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) för Java. 

MTA är ett kostnads fritt verktyg med öppen källkod som:
* Automatiserar program analyser.
* Stöder beräkning av insatser.
* Påskyndar kod migrering.
* Stöder skapa behållare.
* Kan integreras med Azures arbets belastnings verktyg.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrera JBoss-EAP från lokala datorer till Azure
Azure Marketplace-erbjudandet om JBoss EAP på RHEL kommer att installera och etablera på virtuella Azure-datorer på mindre än 20 minuter. Du kan komma åt dessa erbjudanden från [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Det här Azure Marketplace-erbjudandet innehåller olika kombinationer av EAP-och RHEL-versioner som stöder dina krav. JBoss EAP är alltid BYOS, men för RHEL OS kan du välja mellan BYOS eller PAYG. Azure Marketplace-erbjudandet innehåller plan alternativ för JBoss-EAP på RHEL som fristående eller klustrade virtuella datorer:

* JBoss EAP 7,2 på RHEL 7,7 VM (PAYG)
* JBoss EAP 7,2 på RHEL 8,0 VM (PAYG)
* JBoss EAP 7,3 på RHEL 8,0 VM (PAYG)
* JBoss EAP 7,2 på RHEL 7,7 VM (BYOS)
* JBoss EAP 7,2 på RHEL 8,0 VM (BYOS)
* JBoss EAP 7,3 på RHEL 8,0 VM (BYOS)

Tillsammans med Azure Marketplace-erbjudanden kan du använda snabb starts mallar för att komma igång med din Azure migration-resa. De här snabb starterna omfattar färdiga Azure Resource Manager (ARM) mallar och skript för att distribuera JBoss-EAP på RHEL i olika konfigurationer och versions kombinationer. Du har:

* En belastningsutjämnare.
* En privat IP-adress för belastnings utjämning och virtuella datorer.
* Ett virtuellt nätverk med ett enda undernät.
* Konfiguration av virtuell dator (kluster eller fristående).
* Ett Java-exempelprogram.

Lösnings arkitekturen för de här mallarna innehåller:

* JBoss-EAP på en fristående RHEL VM.
* JBoss EAP-klustrad över flera virtuella RHEL-datorer.
* JBoss EAP-kluster via skalnings uppsättningar för virtuella Azure-datorer.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrering av Linux-arbetsbelastning för JBoss EAP
Azures arbets belastnings verktyg fören klar koncept beviset, utvärderingen och migreringsprocessen för lokala Java-appar till Azure. Arbets belastnings verktyget integreras med Azure Migrate identifierings verktyget för att identifiera JBoss EAP-servrar. Sedan genererar den dynamiskt en Ansible-Spelbok för distribution av JBoss EAP-server. Det använder Red Hat MTA-verktyget för att migrera servrar från andra App-servrar till JBoss-EAP. 

Steg för att förenkla migreringen är:
1. **Utvärderingen**. Utvärdera JBoss EAP-kluster med hjälp av en virtuell Azure-dator eller en skalnings uppsättning för virtuella datorer.
1. **Utvärdering**. Genomsök program och infrastruktur.
1. **Infrastruktur konfiguration**. Skapa en arbets belastnings profil.
1. **Distribution och testning**. Distribuera, migrera och testa arbets belastningen.
1. **Konfiguration efter distribution**. Integrera med data, övervakning, säkerhet, säkerhets kopiering och mycket annat.

## <a name="server-configuration-choice"></a>Val av Server konfiguration

För distribution av den virtuella RHEL-datorn kan du välja antingen PAYG eller BYOS. Avbildningar från [Azure Marketplace](https://azuremarketplace.microsoft.com) standard till PAYG. Distribuera en BYOS RHEL VM om du har en egen RHEL OS-avbildning. Se till att ditt RHSM-konto har BYOS-behörighet via moln åtkomst innan du distribuerar den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

JBoss EAP har kraftfulla hanterings möjligheter och ger funktioner och API: er till sina program. Dessa hanterings funktioner varierar beroende på vilket operativ läge du använder för att starta JBoss EAP. Den stöds på RHEL och Windows Server. JBoss EAP erbjuder ett fristående server drift läge för hantering av diskreta instanser. Det ger också ett hanterat domän operativt läge för hantering av grupper av instanser från en enda kontroll punkt. 

> [!NOTE]
> JBoss EAP-hanterade domäner stöds inte i Microsoft Azure eftersom Azures infrastruktur tjänster hanterar HA-funktionen. 

Miljö variabeln `EAP_HOME` anger sökvägen till JBoss-EAP-installationen. Använd följande kommando för att starta JBoss EAP-tjänsten i fristående läge:

```
$EAP_HOME/bin/standalone.sh
```
    
Detta start skript använder EAP_HOME/bin/standalone.conf-filen för att ange vissa standardinställningar, t. ex. JVM alternativ. Du kan anpassa inställningarna i den här filen. JBoss EAP använder standalone.xml konfigurations filen för att starta i fristående läge som standard, men du kan använda ett annat läge för att starta det. 

Mer information om tillgängliga fristående konfigurationsfiler och hur du använder dem finns i [konfigurations filer för fristående servrar för eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) eller [fristående server konfigurationsfiler för EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Använd argumentet för att starta JBoss EAP med en annan konfiguration `--server-config` . Exempel:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Om du vill ha en fullständig lista över alla tillgängliga start skript argument och deras syfte använder du `--help` argumentet. Mer information finns i [Server runtime-argument för eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) eller [Server runtime-argument på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP kan också fungera i kluster läge. JBoss EAP Cluster Messaging gör det möjligt att gruppera JBoss EAP-meddelanden för att dela meddelande behandlings belastning. Varje aktiv nod i klustret är en aktiv JBoss-EAP-meddelande server som hanterar sina egna meddelanden och hanterar sina egna anslutningar. Mer information finns i [kluster översikt på eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) eller [ kluster översikt på EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Support-och prenumerations anteckningar
De här snabb starts mallarna erbjuds enligt följande: 

- RHEL OS erbjuds som PAYG eller BYOS via Red Hat Gold-avbildnings modell.
- JBoss EAP erbjuds endast som BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Använda RHEL OS med PAYG-modellen

Som standard använder de här snabb starts mallarna RHEL 7,7-eller 8,0-PAYG-avbildningen på begäran från Azure Marketplace. PAYG-avbildningar har en ytterligare Tim RHEL prenumerations avgift ovanpå de normala beräknings-, nätverks-och lagrings kostnaderna. På samma gång registreras instansen på din Red Hat-prenumeration. Det innebär att du kommer att använda en av dina rättigheter. 

Den här PAYG-avbildningen kommer att leda till "dubbel fakturering". Du kan undvika det här problemet genom att skapa en egen RHEL-avbildning. Läs mer i Red Hat Knowledge Base-artikeln [hur du etablerar en RHEL VM för Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Eller aktivera din [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold-avbildning.

Mer information om priser för PAYG VM finns [Red Hat Enterprise Linux prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Om du vill använda RHEL i PAYG-modellen behöver du en Azure-prenumeration med den angivna betalnings metoden för [RHEL 7,7 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) eller [RHEL 8,0 på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Dessa erbjudanden kräver att en betalnings metod anges i Azure-prenumerationen.

#### <a name="using-rhel-os-with-the-byos-model"></a>Använda RHEL OS med BYOS-modellen

Om du vill använda BYOS för RHEL OS måste du ha en giltig Red Hat-prenumeration med rättigheter för att använda RHEL OS i Azure. Slutför följande förutsättningar innan du distribuerar RHEL-OS med BYOS-modellen:

1. Se till att du har RHEL-OS-och JBoss EAP-rättigheter kopplade till din Red Hat-prenumeration.
2. Auktorisera ditt Azure-prenumerations-ID för att använda RHEL BYOS-avbildningar. Följ [dokumentationen för prenumerations hantering i Red Hat](https://access.redhat.com/documentation/red_hat_subscription_management/1/) för att slutföra processen, som innehåller följande steg:

   1. Aktivera Microsoft Azure som en provider i Red Hat Cloud Access-instrumentpanelen.

   1. Lägg till dina ID för Azure-prenumeration.

   1. Aktivera nya produkter för moln åtkomst på Microsoft Azure.
    
   1. Aktivera Red Hat Gold-avbildningar för din Azure-prenumeration. Mer information finns i [Red Hat Gold-bilder på Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Vänta tills de guld bilderna i Red Hat är tillgängliga i din Azure-prenumeration. Dessa bilder är vanligt vis tillgängliga inom tre timmar efter det att de har lagts in.
    
3. Godkänn de allmänna villkoren för Azure Marketplace för RHEL BYOS-avbildningar. Du kan slutföra den här processen genom att köra följande Azure CLI-kommandon. Mer information finns i [RHEL BYOS Gold-avbildningarna i Azure](./byos.md) -dokumentationen. Det är viktigt att du kör den senaste versionen av Azure CLI.

   1. Öppna en Azure CLI-session och autentisera med ditt Azure-konto. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

   1. Kontrol lera att RHEL BYOS-avbildningarna är tillgängliga i din prenumeration genom att köra följande CLI-kommando. Om du inte får några resultat här ser du till att Azure-prenumerationen har Aktiver ATS för RHEL BYOS-avbildningar.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Kör följande kommando för att godkänna villkoren för Azure Marketplace för RHEL 7,7 BYOS och RHEL 8,0 BYOS:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Din prenumeration är nu redo att distribuera RHEL 7,7-eller 8,0-BYOS på virtuella Azure-datorer.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Använda JBoss EAP med BYOS-modellen

JBoss EAP är bara tillgängligt på Azure via BYOS-modellen. När du distribuerar den här mallen måste du ange dina RHSM-autentiseringsuppgifter tillsammans med RHSM-pool-ID med giltiga EAP-rättigheter. Om du inte har EAP-rättigheter kan du hämta en [JBoss EAP-utvärderings prenumeration](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) innan du börjar.

## <a name="deployment-options"></a>Distributionsalternativ

Du kan distribuera mallen på följande sätt:

- **PowerShell**. Distribuera mallen genom att köra följande kommandon: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Information om hur du installerar och konfigurerar Azure PowerShell finns i [PowerShell-dokumentationen](/powershell/azure/).  

- **Azure CLI**. Distribuera mallen genom att köra följande kommandon:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Information om hur du installerar och konfigurerar Azure CLI finns i [Installera CLI](/cli/azure/install-azure-cli).

- **Azure-portalen**. Du kan distribuera till Azure Portal genom att gå till Azures snabb starts mallar enligt vad som anges i nästa avsnitt. När du är i snabb starten väljer du knappen **distribuera till Azure** eller **Bläddra på GitHub** .

## <a name="azure-quickstart-templates"></a>Azure-snabbstartsmallar

Du kan börja med någon av följande snabb starts mallar för JBoss-EAP på RHEL som uppfyller distributions målet:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss-EAP på RHEL (fristående virtuell dator)</a>. Detta distribuerar ett webb program med namnet JBoss-EAP på Azure till JBoss EAP 7,2 eller 7,3 som körs på RHEL 7,7 eller 8,0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP på RHEL (klustrade, flera virtuella datorer)</a>. Detta distribuerar ett webb program som kallas EAP-session-replikering på ett JBoss EAP 7,2-eller 7,3-kluster som körs på *n* antal RHEL 7,7 eller 8,0 virtuella datorer. Användaren bestämmer värdet *n* . Alla virtuella datorer läggs till i backend-poolen för en belastningsutjämnare.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP på RHEL (klustrad, skal uppsättning för virtuell dator)</a>. Detta distribuerar ett webb program som kallas EAP-session-replikering på ett JBoss EAP 7,2-eller 7,3-kluster som körs på RHEL 7,7 eller 8,0 Virtual Machine Scale set.

## <a name="resource-links"></a>Resurs länkar

* [Azure Hybrid-förmån](../../windows/hybrid-use-benefit-licensing.md)
* [Konfigurera en Java-app för Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss-EAP på Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss-EAP på Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Distribuera JBoss-EAP på Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Läs mer om [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Läs mer om [hantering av Red Hat-prenumeration](https://access.redhat.com/products/red-hat-subscription-management).
* Lär dig mer om [Red Hat-arbetsbelastningar på Azure](./overview.md).
* Distribuera [JBoss-EAP på en virtuell RHEL-dator eller skalnings uppsättning för virtuella datorer från Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Distribuera [JBoss EAP på en virtuell RHEL-dator eller skalnings uppsättning för virtuella datorer från Azures snabb starts mallar](https://aka.ms/Quickstart-JBoss-EAP).