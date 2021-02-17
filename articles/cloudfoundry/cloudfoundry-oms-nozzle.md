---
title: Distribuera Azure Log Analytics munstycke för Cloud Foundry övervakning
description: Steg för steg-anvisningar om hur du distribuerar Cloud Foundry loggregator munstycke för Azure Log Analytics. Använd munstycket för att övervaka Cloud Foundry systemets hälso-och prestanda mått.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 9fafa9bd014a44fdd0098ef2364375c3f9672bea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571066"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuera Azure Log Analytics munstycke för Cloud Foundry system övervakning

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure. Det hjälper dig att samla in och analysera data som genereras från molnet och lokala miljöer.

Log Analytics munstycke (munstycke) är en Cloud Foundry (CF)-komponent som vidarebefordrar mått från [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) -firehose till Azure Monitor loggar. Med munstycket kan du samla in, Visa och analysera CF-systemets hälso tillstånds-och prestanda mått i flera distributioner.

I det här dokumentet får du lära dig hur du distribuerar munstycket till CF-miljön och sedan åtkomst till data från konsolen för Azure Monitor loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande steg är nödvändiga för att distribuera munstycket.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. distribuera en CF-eller pivotal Cloud Foundry-miljö i Azure

Du kan använda munstycket med antingen en CF-distribution med öppen källkod eller en PCF-distribution (pivotal Cloud Foundry).

* [Distribuera Cloud Foundry på Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuera pivotal Cloud Foundry på Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installera CF-kommando rads verktygen för att distribuera munstycket

Munstycket körs som ett program i CF-miljö. Du behöver CF CLI för att distribuera programmet.

Munstycket måste också ha åtkomst behörighet till loggregator-Firehose och moln styrenheten. Om du vill skapa och konfigurera användaren behöver du tjänsten användar konto och autentisering (UAA).

* [Installera Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installera Cloud Foundry UAA kommando rads klient](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Innan du konfigurerar kommando rads klienten för UAA kontrollerar du att RubyGems har installerats.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. skapa en Log Analytics arbets yta i Azure

Du kan skapa Log Analytics arbets ytan manuellt eller genom att använda en mall. Mallen distribuerar en konfiguration av förkonfigurerade KPI-vyer och aviseringar för konsolen Azure Monitor loggar. 

#### <a name="to-create-the-workspace-manually"></a>Så här skapar du arbets ytan manuellt:

1. Sök i listan över tjänster på Azure Marketplace i Azure Portal och välj sedan Log Analytics arbets ytor.
2. Välj **skapa** och välj sedan alternativ för följande objekt:

   * **Log Analytics arbets yta**: Ange ett namn för din arbets yta.
   * **Prenumeration**: om du har flera prenumerationer väljer du den som är samma som din CF-distribution.
   * **Resurs grupp**: du kan skapa en ny resurs grupp eller använda samma som i CF-distributionen.
   * **Plats**: Ange platsen.
   * **Pris nivå**: Välj **OK** för att slutföra.

Mer information finns i [Kom igång med Azure Monitor loggar](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Skapa Log Analytics arbets ytan via övervaknings mal len från Azures marknads plats:

1. Öppna Azure-portalen.
1. Klicka på tecknet "+" eller "skapa en resurs" i det övre vänstra hörnet.
1. Skriv "Cloud Foundry" i sökfönstret och välj Cloud Foundry övervaknings lösning.
1. Sidan för mall för Cloud Foundry övervaknings lösning har lästs in och klicka på "skapa" för att starta bladet mall.
1. Ange de parametrar som krävs:
    * **Prenumeration**: Välj en Azure-prenumeration för Log Analytics arbets ytan, vanligt vis samma med Cloud Foundry-distribution.
    * **Resurs grupp**: Välj en befintlig resurs grupp eller skapa en ny för arbets ytan Log Analytics.
    * **Plats för resurs grupp**: Välj platsen för resurs gruppen.
    * **OMS_Workspace_Name**: Ange ett namn på arbets ytan, om arbets ytan inte finns, kommer mallen att skapa en ny.
    * **OMS_Workspace_Region**: Välj plats för arbets ytan.
    * **OMS_Workspace_Pricing_Tier**: Välj Log Analytics-arbetsyteets SKU. Se [pris vägledningen](https://azure.microsoft.com/pricing/details/log-analytics/) för referens.
    * **Juridiska villkor**: Klicka på juridiska villkor och klicka sedan på "skapa" för att godkänna den juridiska termen.
1. När du har angett alla parametrar klickar du på "skapa" för att distribuera mallen. När distributionen har slutförts visas statusen på fliken meddelande.


## <a name="deploy-the-nozzle"></a>Distribuera munstycket

Det finns ett par olika sätt att distribuera munstycket: som en PCF panel eller som ett CF-program.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuera munstycket som en PCF Ops Manager-panel

Följ stegen för att [Installera och konfigurera Azure Log Analytics munstycke för PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Detta är den förenklade metoden, PCF Ops Manager-panelen konfigurerar och push-munstycket automatiskt. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Distribuera munstycket manuellt som ett CF-program

Om du inte använder PCF Ops Manager distribuerar du munstycket som ett program. I följande avsnitt beskrivs den här processen.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Logga in på CF-distributionen som en administratör via CF CLI

Kör följande kommando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" är namnet på CF-domänen. Du kan hämta den genom att söka i "SYSTEM_DOMAIN" i manifest filen CF-distribution. 

"CF_User" är namnet på CF-administratören. Du kan hämta namnet och lösen ordet genom att söka i avsnittet "scim" och söka efter namnet och "cf_admin_password" i manifest filen för CF-distributionen.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Skapa en CF-användare och bevilja nödvändiga behörigheter

Kör följande kommandon:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" är namnet på CF-domänen. Du kan hämta den genom att söka i "SYSTEM_DOMAIN" i manifest filen CF-distribution.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Hämta den senaste versionen av Log Analytics munstycke

Kör följande kommando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Nu kan du ange miljövariabler i manifest. yml-filen i den aktuella katalogen. Följande visar app-manifestet för munstycket. Ersätt värden med din speciella Log Analytics arbets ytans information.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Skicka programmet från utvecklings datorn

Se till att du är under mappen OMS-Log-Analytics-Firehose-munstycke. Kör följande kommando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validera munstycke installationen

### <a name="from-apps-manager-for-pcf"></a>Från Apps-hanteraren (för PCF)

1. Logga in på Ops Manager och kontrol lera att panelen visas på instrument panelen för installationen.
2. Logga in på Apps Manager, kontrol lera att utrymmet som du har skapat för munstycket visas i användnings rapporten och bekräfta att statusen är normal.

### <a name="from-your-development-computer"></a>Från din utvecklings dator

I fönstret CF CLI skriver du:
```
cf apps
```
Kontrol lera att programmet OMS munstycke körs.

## <a name="view-the-data-in-the-azure-portal"></a>Visa data i Azure Portal

Om du har distribuerat övervaknings lösningen via marknads plats mal len går du till Azure Portal och letar upp lösningen. Du hittar lösningen i den resurs grupp som du har angett i mallen. Klicka på lösningen, bläddra till "Log Analytics-konsolen", förkonfigurerade vyer visas med Top Cloud Foundry system-KPI: er, program data, aviseringar och hälso mått för virtuella datorer. 

Om du har skapat Log Analytics arbets ytan manuellt följer du stegen nedan för att skapa vyer och aviseringar:

### <a name="1-import-the-oms-view"></a>1. Importera OMS-vyn

I OMS-portalen bläddrar du till **Visa designer**  >  **Importera**  >  **Bläddra** och väljer en av omsview-filerna. Välj till exempel *Cloud Foundry. omsview* och spara vyn. Nu visas en panel på sidan **Översikt** . Markera det om du vill visa visualiserings mått.

Du kan anpassa dessa vyer eller skapa nya vyer via **View Designer**.

*"Cloud Foundry. omsview"* är en för hands version av Cloud Foundry OMS View-mallen. Det här är en fullständigt konfigurerad standard mall. Om du har förslag eller feedback om mallen kan du skicka dem till [avsnittet ärende](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. skapa aviserings regler

Du kan [skapa aviseringarna](../azure-monitor/alerts/alerts-overview.md)och anpassa frågorna och tröskelvärdena efter behov. Följande är rekommenderade aviseringar:

| Sökfråga                                                                  | Generera avisering baserat på | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Skriv = CF_ValueMetric_CL Origin_s = BBS Name_s = "domän. CF-Apps"                   | Antal resultat < 1   | **BBS. Domain.cf – appar** anger om domänen CF-Apps är uppdaterad. Det innebär att CF app-begäranden från moln styrenheten synkroniseras till BBS. LRPsDesired (Diego-önskad AIs) för körning. Inga mottagna data betyder CF-Apps-domänen är inte uppdaterad i den angivna tids perioden. |
| Skriv = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d>1            | Antal resultat > 0   | För Diego-celler betyder 0 att det är felfritt och 1 betyder fel. Ange aviseringen om flera felaktiga Diego-celler har identifierats i det angivna tidsfönstret. |
| Skriv = CF_ValueMetric_CL Origin_s = "Bosh-igneringsalgoritm-forwarder" Name_s = "system. felfri" Value_d = 0 | Antal resultat > 0 | 1 betyder att systemet är felfritt och 0 innebär att systemet inte är felfritt. |
| Skriv = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d>0 | Antal resultat > 0   | Konsulat avger en hälso status regelbundet. 0 innebär att systemet är felfritt och att en väg sändare upptäcker att konsulär är nere. |
| Skriv = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" eller Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Antal resultat > 0 | Delta antalet meddelanden som har släppts avsiktligt av Doppler på grund av mottryck. |
| Skriv = CF_LogMessage_CL SourceType_s = LGR MessageType_s = fel                      | Antal resultat > 0   | Loggregator avger **LGR** för att indikera problem med loggnings processen. Ett exempel på ett sådant problem är när logg meddelandets utdata är för hög. |
| Typ = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Antal resultat > 0   | När munstycket tar emot en långsam konsument avisering från loggregator skickar den **slowConsumerAlert** -ValueMetric till Azure Monitor loggar. |
| Skriv = CF_CounterEvent_CL Job_s = munstycke Name_s = eventsLost Delta_d>0              | Antal resultat > 0   | Om delta antalet förlorade händelser når ett tröskelvärde, innebär det att munstycket kan ha problem med att köra. |

## <a name="scale"></a>Skala

Du kan skala munstycket och loggregator.

### <a name="scale-the-nozzle"></a>Skala munstycket

Du bör börja med minst två instanser av munstycket. Firehose distribuerar arbets belastningen över alla instanser av munstycket.
För att se till att munstycket kan hålla sig med data trafiken från Firehose konfigurerar du **slowConsumerAlert** -aviseringen (anges i föregående avsnitt, "skapa varnings regler"). När du har fått en avisering följer du [rikt linjerna för långsam munstycke](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) för att avgöra om skalning krävs.
För att skala upp munstycket använder du Apps Manager eller CF CLI för att öka instans numren eller minnes-eller disk resurserna för munstycket.

### <a name="scale-the-loggregator"></a>Skala loggregator

Loggregator skickar ett **LGR** -logg meddelande för att indikera problem med loggnings processen. Du kan övervaka aviseringen för att avgöra om loggregator måste skalas upp.
Du kan skala upp loggregator genom att antingen öka buffertstorleken för Doppler eller lägga till ytterligare Doppler Server-instanser i CF-manifestet. Mer information finns i [rikt linjerna för att skala loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Uppdatera

Om du vill uppdatera munstycket med en nyare version laddar du ned den nya munstycke versionen, följer stegen i avsnittet "distribuera munstycket" och skicka programmet igen.

### <a name="remove-the-nozzle-from-ops-manager"></a>Ta bort munstycket från OPS Manager

1. Logga in i OPS Manager.
2. Leta reda på **Microsoft Azure Log Analytics munstycket för PCF** panel.
3. Välj skräp ikonen och bekräfta borttagningen.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Ta bort munstycket från utvecklings datorn

I CF CLI-fönstret skriver du:
```
cf delete <App Name> -r
```

Om du tar bort munstycket tas data i OMS-portalen inte bort automatiskt. Den upphör att gälla baserat på inställningarna för kvarhållning av Azure Monitor loggar.

## <a name="support-and-feedback"></a>Support och feedback

Azure Log Analytics munstycke är öppet. Skicka dina frågor och synpunkter till [GitHub-avsnittet](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Öppna en support förfrågan för Azure genom att välja "virtuell dator som kör Cloud Foundry" som tjänste kategori. 

## <a name="next-step"></a>Nästa steg

Från PCF 2.0 överförs prestanda mått för virtuella datorer till Azure Log Analytics munstycke av system mått vidarebefordrare och integreras i arbets ytan Log Analytics. Du behöver inte längre Log Analytics agent för prestanda mått för virtuella datorer. Du kan dock fortfarande använda Log Analytics agent för att samla in syslog-information. Log Analytics-agenten installeras som ett Bosh-tillägg för dina CF-VM: ar. 

Mer information finns i [distribuera Log Analytics agent till din Cloud Foundry-distribution](https://github.com/Azure/oms-agent-for-linux-boshrelease).