---
title: Ändra kluster inställningar för Azure Service Fabric
description: I den här artikeln beskrivs de infrastruktur inställningar och de uppgraderings principer för infrastruktur resurser som du kan anpassa.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 65ae2337ac7dbe4370411a154463a6ddc37f83b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255979"
---
# <a name="customize-service-fabric-cluster-settings"></a>Anpassa Service Fabric-klusterinställningar
I den här artikeln beskrivs de olika infrastruktur inställningarna för ditt Service Fabric-kluster som du kan anpassa. För kluster som finns i Azure kan du anpassa inställningarna via [Azure Portal](https://portal.azure.com) eller genom att använda en Azure Resource Manager mall. Mer information finns i [Uppgradera konfigurationen av ett Azure-kluster](service-fabric-cluster-config-upgrade-azure.md). För fristående kluster anpassar du inställningarna genom att uppdatera *ClusterConfig.jspå* filen och utföra en konfigurations uppgradering i klustret. Mer information finns i [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).

Det finns tre olika uppgraderings principer:

- **Dynamisk** – ändringar i en dynamisk konfiguration innebär inte att processen startas om antingen Service Fabric processer eller värd processer för tjänsten. 
- **Statisk** – ändringar i en statisk konfiguration kommer att medföra att Service Fabric-noden startas om för att kunna använda ändringen. Tjänsterna på noderna kommer att startas om.
- **Notalged** – de här inställningarna kan inte ändras. Om du ändrar inställningarna måste klustret förstöras och ett nytt kluster skapas. 

Följande är en lista över infrastruktur inställningar som du kan anpassa, ordnade efter avsnitt.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|sträng, standard är "ingen"|Statisk| Detta validerar inte Server certifikatet. lyckas med begäran. Se config ServiceCertificateThumbprints för den kommaavgränsade listan över tumavtrycken för de fjärrcertifikat som den omvända proxyn kan lita på. Se config ServiceCommonNameAndIssuer för namn på certifikat mottagare och utfärdarens tumavtryck för de fjärrcertifikat som den omvända proxyn kan lita på. Läs mer i en [säker anslutning för omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, standard är 16384 |Dynamisk| Anger storleken på för segmentet i byte som används för att läsa bröd texten. |
|CrlCheckingFlag|uint, standard är 0x40000000 |Dynamisk| Flaggor för verifiering av program-/tjänst certifikat kedja; t. ex. CRL-kontroll 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY inställningen 0 inaktiverar CRL-kontroll fullständig lista över värden som stöds dokumenteras av dwFlags för CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tid i sekunder. Standardvärdet är 120 |Dynamisk|Ange TimeSpan i sekunder.  Ger standardtimeout för begäran för HTTP-begäranden som bearbetas i http-appens Gateway. |
|ForwardClientCertificate|bool, standard är falskt|Dynamisk|Om värdet är False kommer omvänd proxy inte att begära klient certifikatet. När det är inställt på Sant begär omvänd proxy för klient certifikatet under TLS-handskakning och vidarebefordrar den base64-kodade PEM-format strängen till tjänsten i ett sidhuvud med namnet X-client-Certificate. tjänsten kan inte utföra begäran med lämplig status kod efter att ha kontrollerat certifikat data. Om detta är sant och klienten inte visar ett certifikat, kommer omvänd proxy att vidarebefordra en tom rubrik och låta tjänsten hantera ärendet. Omvänd proxy fungerar som ett transparent lager. Mer information finns i [Konfigurera autentisering av klient certifikat](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |sträng, standard är "ingen" |Statisk| Anger vilken typ av säkerhets uppgifter som ska användas vid giltigheten för http app Gateway-slutpunkten. giltiga värden är none/X509. |
|GatewayX509CertificateFindType |sträng, standard är "FindByThumbprint" |Dynamisk| Anger hur du söker efter certifikat i arkivet som anges av GatewayX509CertificateStoreName-stödda värde: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | sträng, standard är "" |Dynamisk| Sök filter värde som används för att hitta http app Gateway-certifikatet. Det här certifikatet har kon figurer ATS på https-slutpunkten och kan också användas för att verifiera appens identitet om det behövs av tjänsterna. FindValue har sökts upp först. och om det inte finns. FindValueSecondary har sökts upp. |
|GatewayX509CertificateFindValueSecondary | sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta http app Gateway-certifikatet. Det här certifikatet har kon figurer ATS på https-slutpunkten och kan också användas för att verifiera appens identitet om det behövs av tjänsterna. FindValue har sökts upp först. och om det inte finns. FindValueSecondary har sökts upp.|
|GatewayX509CertificateStoreName |sträng, standard är "My" |Dynamisk| Namnet på X. 509-certifikatarkivet som innehåller certifikat för http app Gateway. |
|HttpRequestConnectTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (5)|Dynamisk|Ange TimeSpan i sekunder.  Ger anslutnings tids gränsen för HTTP-begäranden som skickas från http-programgatewayen.  |
|IgnoreCrlOfflineError|bool, standard är sant|Dynamisk|Om CRL offline-fel ska ignoreras för verifiering av program/tjänst certifikat. |
|IsEnabled |Bool, standard är falskt |Statisk| Aktiverar eller inaktiverar HttpApplicationGateway. HttpApplicationGateway är inaktiverat som standard och denna konfiguration måste anges för att aktivera det. |
|NumberOfParallelOperations | Uint, standard är 5000 |Statisk|Antalet läsningar att publicera i http-server kön. Detta styr antalet samtidiga förfrågningar som kan uppfyllas av HttpGateway. |
|RemoveServiceResponseHeaders|sträng, standard är "datum; Servernamn|Statisk|Halv kolon/kommaavgränsad lista över svarshuvuden som tas bort från tjänst svaret. Innan du vidarebefordrar den till klienten. Om detta är inställt på tom sträng; Skicka alla huvuden som returneras av tjänsten som de är. säga Skriv inte över datum och Server |
|ResolveServiceBackoffInterval |Tid i sekunder, standard är 5 |Dynamisk|Ange TimeSpan i sekunder.  Ger standard intervallet för avstängning innan en misslyckad åtgärd för att lösa problemet görs igen. |
|SecureOnlyMode|bool, standard är falskt|Dynamisk| SecureOnlyMode: true: omvänd proxy vidarebefordrar bara till tjänster som publicerar säkra slut punkter. falskt: omvänd proxy kan vidarebefordra förfrågningar till säkra/oskyddade slut punkter. Läs mer i avsnittet om [slut punkts val för omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|sträng, standard är ""|Dynamisk|Den kommaavgränsade listan över tumavtrycken av de fjärrcertifikat som den omvända proxyn kan lita på. Läs mer i en [säker anslutning för omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/http/ServiceCommonNameAndIssuer

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk| Mottagar namn och utfärdarens tumavtryck för de fjärrcertifikat som den omvända proxyn kan lita på. Läs mer i en [säker anslutning för omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, standardvärdet är 0|Statisk|MinReplicaSetSize för BackupRestoreService |
|PlacementConstraints|sträng, standard är ""|Statisk|    PlacementConstraints för BackupRestore-tjänsten |
|SecretEncryptionCertThumbprint|sträng, standard är ""|Dynamisk|Tumavtryck för X509-certifikatet för hemlig kryptering |
|SecretEncryptionCertX509StoreName|sträng, rekommenderat värde är "My" (inget standardvärde) |    Dynamisk|    Detta anger det certifikat som ska användas för kryptering och dekryptering av namn på autentiseringsuppgifter för X. 509 certifikat arkiv som används för kryptering av kryptering av autentiseringsuppgifter som används av tjänsten för säkerhets kopierings återställning |
|TargetReplicaSetSize|int, standardvärdet är 0|Statisk| TargetReplicaSetSize för BackupRestoreService |

## <a name="centralsecretservice"></a>CentralSecretService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|DeployedState |wstring, standard är L "inaktive rad" |Statisk |2 – mellanlagra borttagning av CSS. |

## <a name="clustermanager"></a>ClusterManager

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, standard är falskt |Dynamisk|Huruvida sorterings principer för anpassade uppgraderingar är tillåtna. Detta används för att utföra 2-fas uppgradering som aktiverar den här funktionen. Service Fabric 6,5 lägger till stöd för att ange sorterings princip för uppgraderings domäner under kluster-eller program uppgraderingar. Principer som stöds är numeriska, lexicographical, ReverseNumeric och ReverseLexicographical. Standardvärdet är numeriskt. För att kunna använda den här funktionen måste kluster manifest inställningen ClusterManager/AllowCustomUpgradeSortPolicies anges till true som ett andra konfigurations uppgraderings steg efter uppgraderingen av SF 6,5-koden. Det är viktigt att detta görs i två faser, annars kan kod uppgraderingen bli förvirrad om uppgraderings ordningen under den första uppgraderingen.|
|EnableDefaultServicesUpgrade | Bool, standard är falskt |Dynamisk|Aktivera uppgradering av standard tjänster under program uppgradering. Standard beskrivningarna för tjänsten skrivs över efter uppgraderingen. |
|FabricUpgradeHealthCheckInterval |Tid i sekunder, standard är 60 |Dynamisk|Kontroll frekvensen för hälso status vid en övervakad infrastruktur uppgradering |
|FabricUpgradeStatusPollInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen för avsökningen av uppgraderings status för infrastruktur. Det här värdet avgör uppdaterings frekvensen för alla GetFabricUpgradeProgress-anrop |
|ImageBuilderTimeoutBuffer |Tid i sekunder, standard är 3 |Dynamisk|Ange TimeSpan i sekunder. Hur lång tid som ska tillåtas för timeout-fel i Image Builder för att återgå till klienten. Om bufferten är för liten; klienten nådde tids gränsen innan servern och får ett allmänt timeout-fel. |
|InfrastructureTaskHealthCheckRetryTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange TimeSpan i sekunder. Hur lång tid det tar att lägga till misslyckade hälso kontroller vid efter bearbetning av en infrastruktur aktivitet. Om du undersöker en godkänd hälso kontroll återställs denna timer. |
|InfrastructureTaskHealthCheckStableDuration | Tid i sekunder, standardvärdet är 0|Dynamisk| Ange TimeSpan i sekunder. Hur lång tid det tog att iaktta tidigare godkända hälso kontroller innan efter bearbetning av en infrastruktur aktivitet har slutförts. Om du undersöker en misslyckad hälso kontroll återställs denna timer. |
|InfrastructureTaskHealthCheckWaitDuration |Tid i sekunder, standardvärdet är 0|Dynamisk| Ange TimeSpan i sekunder. Vänte tiden innan hälso kontroller startar efter efter bearbetning av en infrastruktur aktivitet. |
|InfrastructureTaskProcessingInterval | Tid i sekunder, standard är 10 |Dynamisk|Ange TimeSpan i sekunder. Bearbetnings intervallet som används av datorn för bearbetning av infrastruktur aktiviteter. |
|MaxCommunicationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange TimeSpan i sekunder. Maximal tids gräns för intern kommunikation mellan ClusterManager och andra system tjänster (t. ex. Naming Service; Redundanshanteraren och så vidare). Denna timeout bör vara mindre än global MaxOperationTimeout (eftersom det kan finnas flera kommunikationer mellan system komponenter för varje klient åtgärd). |
|MaxDataMigrationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange TimeSpan i sekunder. Maximal tids gräns för återställnings åtgärder för datamigrering efter att en Fabric-uppgradering har ägt rum. |
|MaxOperationRetryDelay |Tid i sekunder, standard är 5|Dynamisk| Ange TimeSpan i sekunder. Maximal fördröjning för interna återförsök när fel påträffas. |
|MaxOperationTimeout |Tid i sekunder, standard är MaxValue |Dynamisk| Ange TimeSpan i sekunder. Maximal global tids gräns för internt bearbetnings åtgärder på ClusterManager. |
|MaxTimeoutRetryBuffer | Tid i sekunder, standard är 600 |Dynamisk|Ange TimeSpan i sekunder. Den maximala åtgärds tids gränsen vid internt försök på grund av timeout `<Original Time out> + <MaxTimeoutRetryBuffer>` . Ytterligare timeout har lagts till i steg om MinOperationTimeout. |
|MinOperationTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange TimeSpan i sekunder. Den minsta globala tids gränsen för internt bearbetnings åtgärder på ClusterManager. |
|MinReplicaSetSize |Int, standard är 3 |Tillåts inte|MinReplicaSetSize för ClusterManager. |
|PlacementConstraints | sträng, standard är "" |Tillåts inte|PlacementConstraints för ClusterManager. |
|QuorumLossWaitDuration |Tid i sekunder, standard är MaxValue |Tillåts inte| Ange TimeSpan i sekunder. QuorumLossWaitDuration för ClusterManager. |
|ReplicaRestartWaitDuration |Tid i sekunder, standard är (60,0 \* 30)|Tillåts inte|Ange TimeSpan i sekunder. ReplicaRestartWaitDuration för ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tid i sekunder, standard är 1200 |Dynamisk| Ange TimeSpan i sekunder. Om ReplicaSetCheckTimeout anges till det maximala värdet för DWORD; sedan åsidosätts den med värdet för den här konfigurationen i syfte att återställa. Det värde som används för sammanslagning-Forward åsidosätts aldrig. |
|SkipRollbackUpdateDefaultService | Bool, standard är falskt |Dynamisk|CM hoppar över att återställa uppdaterade standard tjänster under återställningen av program uppgraderingen. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är (3600,0 \* 2)|Tillåts inte|Ange TimeSpan i sekunder. StandByReplicaKeepDuration för ClusterManager. |
|TargetReplicaSetSize |Int, standard är 7 |Tillåts inte|TargetReplicaSetSize för ClusterManager. |
|UpgradeHealthCheckInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen hälso status kontroller under ett övervakat program uppgraderingar |
|UpgradeStatusPollInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen för avsökningen av programmets uppgraderings status. Det här värdet avgör uppdaterings frekvensen för alla GetApplicationUpgradeProgress-anrop |
|CompleteClientRequest | Bool, standard är falskt |Dynamisk| Slutför klientbegäran vid godkännande av CM. |

## <a name="common"></a>Common

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AllowCreateUpdateMultiInstancePerNodeServices |Bool, standard är falskt |Dynamisk|Tillåter att flera tillstånds lösa instanser av en tjänst per nod skapas. Den här funktionen finns för närvarande som en förhandsversion. |
|PerfMonitorInterval |Tid i sekunder, standard är 1 |Dynamisk|Ange TimeSpan i sekunder. Intervall för prestanda övervakning. Om du ställer in till 0 eller negativt värde inaktive ras övervakningen. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standard är ingen|Dynamisk|Anger att princip defragmenteringen ska följa när noder töms. För ett angivet mått 0 anger att SF ska försöka defragmentera noder jämnt över UDs och fd; 1 anger att noderna måste defragmenteras |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, standard är ingen|Dynamisk|Anger den uppsättning mått som ska användas för defragmentering och inte för belastnings utjämning. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Fastställer antalet lediga noder som behövs för att ta klustret defragmenterat genom att ange antingen procent i intervall [0,0-1,0] eller antal tomma noder som antal >= 1,0 |

## <a name="diagnostics"></a>Diagnostik

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, standard är sant | Dynamisk | Exkludera HTTP-begäranden som inte påverkar klustrets tillstånd från granskning. Öppet endast begär anden av typen "GET" utesluts. men det kan komma att ändras. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, standard är sant | Dynamisk |Huruvida personifiering krävs vid åtkomst till diagnostiska lager för programmets räkning. |
|AppEtwTraceDeletionAgeInDays |Int, standard är 3 | Dynamisk |Antal dagar efter vilket vi tar bort gamla ETL-filer som innehåller ETW-spår för program. |
|ApplicationLogsFormatVersion |Int, standardvärdet är 0 | Dynamisk |Version för program logg format. Värden som stöds är 0 och 1. Version 1 innehåller fler fält från posten ETW-händelse än version 0. |
|AuditHttpRequests |Bool, standard är falskt | Dynamisk | Aktivera eller inaktivera HTTP-granskning. Syftet med granskning är att se de aktiviteter som har utförts mot klustret. inklusive vem som initierade begäran. Observera att detta är ett bästa försök att logga. och spårning kan uppstå. HTTP-förfrågningar med "User"-autentisering har inte registrerats. |
|CaptureHttpTelemetry|Bool, standard är sant | Dynamisk | Aktivera eller inaktivera HTTP-telemetri. Syftet med telemetri är att Service Fabric kunna samla in telemetridata för att planera framtida arbete och identifiera problemområden. Telemetri registrerar inte någon personlig information eller begär ande texten. Telemetri fångar alla HTTP-begäranden om inget annat anges. |
|ClusterId |Sträng | Dynamisk |Unikt ID för klustret. Detta skapas när klustret skapas. |
|ConsumerInstances |Sträng | Dynamisk |Listan över DCA-konsument instanser. |
|DiskFullSafetySpaceInMB |Int, standard är 1024 | Dynamisk |Återstående disk utrymme i MB för att skydda från användning av DCA. |
|EnableCircularTraceSession |Bool, standard är falskt | Statisk |Flagga anger om cirkulära spårnings-sessioner ska användas. |
|EnablePlatformEventsFileSink |Bool, standard är falskt | Statisk |Aktivera/inaktivera plattforms händelser som skrivs till disk |
|EnableTelemetry |Bool, standard är sant | Dynamisk |Detta kommer att aktivera eller Inaktivera telemetri. |
|FailuresOnlyHttpTelemetry | Bool, standard är falskt | Dynamisk | Om insamling av HTTP-telemetri är aktiverat. fånga endast misslyckade förfrågningar. Detta är att hjälpa till att minska antalet händelser som har genererats för telemetri. |
|HttpTelemetryCapturePercentage | int, standard är 50 | Dynamisk | Om insamling av HTTP-telemetri är aktiverat. fånga endast en slumpmässig procent andel begär Anden. Detta är att hjälpa till att minska antalet händelser som har genererats för telemetri. |
|MaxDiskQuotaInMB |Int, standard är 65536 | Dynamisk |Disk kvot i MB för Windows-och Linux Fabric-loggfiler. |
|ProducerInstances |Sträng | Dynamisk |Listan över DCA producent instanser. |

## <a name="dnsservice"></a>DNS service
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, standard är falskt|Statisk|Flaggan för att aktivera stöd för DNS-frågor för partitionerade tjänster. Funktionen är inaktive rad som standard. Mer information finns i [Service Fabric DNS-tjänsten.](service-fabric-dnsservice.md)|
|InstanceCount|int, standard är-1|Statisk|Standardvärdet är-1 vilket innebär att DNS service körs på varje nod. Onebox behållaravbildningen måste anges till 1 eftersom DNS service använder välkänd port 53, så den kan inte ha flera instanser på samma dator.|
|IsEnabled|bool, standard är falskt|Statisk|Aktiverar/inaktiverar DNS service. DNS service är inaktiverat som standard och denna konfiguration måste anges för att aktivera det. |
|PartitionPrefix|sträng, standard är "--"|Statisk|Styr strängvärdet för partitions-prefixet i DNS-frågor om partitionerade tjänster. Värdet: <ul><li>Bör vara RFC-kompatibel eftersom den kommer att ingå i en DNS-fråga.</li><li>Får inte innehålla en punkt, ".", eftersom punkten stör beteendet för DNS-suffix.</li><li>Får inte vara längre än 5 tecken.</li><li>Kan inte vara en tom sträng.</li><li>Om PartitionPrefix-inställningen åsidosätts måste PartitionSuffix åsidosättas och vice versa.</li></ul>Mer information finns i [Service Fabric DNS-tjänsten.](service-fabric-dnsservice.md).|
|PartitionSuffix|sträng, standard är ""|Statisk|Kontrollerar strängvärdet partition suffix i DNS-frågor om partitionerade tjänster. Värdet: <ul><li>Bör vara RFC-kompatibel eftersom den kommer att ingå i en DNS-fråga.</li><li>Får inte innehålla en punkt, ".", eftersom punkten stör beteendet för DNS-suffix.</li><li>Får inte vara längre än 5 tecken.</li><li>Om PartitionPrefix-inställningen åsidosätts måste PartitionSuffix åsidosättas och vice versa.</li></ul>Mer information finns i [Service Fabric DNS-tjänsten.](service-fabric-dnsservice.md). |
|RetryTransientFabricErrors|Bool, standard är sant|Statisk|Inställningen styr möjligheterna för återförsök vid anrop Service Fabric-API: er från DNS service. När den är aktive rad försöker den igen upp till tre gånger om ett tillfälligt fel inträffar.|

## <a name="eventstoreservice"></a>EventStoreService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, standardvärdet är 0|Statisk|MinReplicaSetSize för EventStore-tjänsten |
|PlacementConstraints|sträng, standard är ""|Statisk|    PlacementConstraints för EventStore-tjänsten |
|TargetReplicaSetSize|int, standardvärdet är 0|Statisk| TargetReplicaSetSize för EventStore-tjänsten |

## <a name="fabricclient"></a>FabricClient

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tid i sekunder, standard är 2 |Dynamisk|Ange TimeSpan i sekunder. Timeout-intervall för anslutning för varje tid som klienten försöker öppna en anslutning till gatewayen.|
|HealthOperationTimeout |Tid i sekunder, standard är 120 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för ett rapport meddelande som skickats till Health Manager. |
|HealthReportRetrySendInterval |Tid i sekunder, standard är 30, minimum är 1 |Dynamisk|Ange TimeSpan i sekunder. Intervallet som rapporterings komponenten skickar om ackumulerade hälso rapporter till Health Manager. |
|HealthReportSendInterval |Tid i sekunder, standard är 30 |Dynamisk|Ange TimeSpan i sekunder. Intervallet då rapporterings komponenten skickar ackumulerade hälso rapporter till Health Manager. |
|KeepAliveIntervalInSeconds |Int, standard är 20 |Statisk|Intervallet då FabricClient-transporten skickar Keep-Alive-meddelanden till gatewayen. För 0; keepAlive har inaktiverats. Måste vara ett positivt värde. |
|MaxFileSenderThreads |Uint, standard är 10 |Statisk|Det maximala antalet filer som överförs parallellt. |
|NodeAddresses |sträng, standard är "" |Statisk|En samling adresser (anslutnings strängar) på olika noder som kan användas för att kommunicera med Naming Service. Första gången klienten ansluter väljer en av adresserna slumpmässigt. Om fler än en anslutnings sträng anges och en anslutning Miss lyckas på grund av ett kommunikations-eller tids gräns fel. Klienten växlar till att använda nästa adress i tur och ordning. Se avsnittet Naming Service Address retry för information om semantiska försök. |
|PartitionLocationCacheLimit |Int, standard är 100000 |Statisk|Antal partitioner som är cachelagrade för tjänst matchning (anges till 0 för ingen begränsning). |
|RetryBackoffInterval |Tid i sekunder, standard är 3 |Dynamisk|Ange TimeSpan i sekunder. Avstängnings intervallet innan du försöker utföra åtgärden igen. |
|ServiceChangePollInterval |Tid i sekunder, standard är 120 |Dynamisk|Ange TimeSpan i sekunder. Intervallet mellan efterföljande avsökningar för tjänst ändringar från klienten till gatewayen för registrering av meddelanden om tjänst ändringar som registrerats. |

## <a name="fabrichost"></a>Fabrichost returnerar

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, standard är 10 |Dynamisk|Detta är det högsta antalet för vilka systemet försöker att aktivera igen innan det upprättas. |
|ActivationMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|Ange TimeSpan i sekunder. Max återförsöksintervall för aktivering. Vid varje fort löp ande fel beräknas återförsöksintervall som min (ActivationMaxRetryInterval; Antal kontinuerliga haverier * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tid i sekunder, standard är 5 |Dynamisk|Ange TimeSpan i sekunder. Backoff intervall vid varje aktiverings fel. vid varje kontinuerlig aktivering görs ett nytt försök att aktivera för MaxActivationFailureCount. Återförsöksintervall för varje försök är en produkt med kontinuerlig aktiverings fel och aktiverings intervall. |
|EnableRestartManagement |Bool, standard är falskt |Dynamisk|Detta är att aktivera omstart av servern. |
|EnableServiceFabricAutomaticUpdates |Bool, standard är falskt |Dynamisk|Detta är att aktivera automatisk uppdatering av infrastruktur resurser via Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, standard är falskt |Dynamisk|Detta är att aktivera bas uppdatering för-servern. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, standard är sant | Statisk | Aktiverar snabbare uppladdnings hastigheter i DCA när Fabrichost returnerar är i läget för felrapportering. |
|FailureReportingTimeout | TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60) | Statisk |Ange TimeSpan i sekunder. Timeout för DCA-felrapportering i Case Fabrichost returnerar påträffar ett tidigt start haveri. | 
|RunDCAOnStartupFailure | Bool, standard är sant | Statisk |Avgör om du ska starta DCA för att ladda upp loggar vid problem med start i Fabrichost returnerar. | 
|StartTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för fabricactivationmanager-start. |
|StopTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för aktivering av värdbaserade tjänster. inaktive ring och uppgradering. |

## <a name="fabricnode"></a>FabricNode

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |sträng, standard är "FindByThumbprint" |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName-stödda värde: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |sträng, standard är "" | Dynamisk|Sök filter värde som används för att hitta certifikatet för standard administratörs rollen FabricClient. |
|ClientAuthX509FindValueSecondary |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta certifikatet för standard administratörs rollen FabricClient. |
|ClientAuthX509StoreName |sträng, standard är "My" |Dynamisk|Namnet på det X. 509-certifikat arkiv som innehåller certifikat för standard administratörs rollen FabricClient. |
|ClusterX509FindType |sträng, standard är "FindByThumbprint" |Dynamisk|Anger hur du söker efter kluster certifikat i arkivet som anges av ClusterX509StoreName-värden som stöds: "FindByThumbprint"; "FindBySubjectName" med "FindBySubjectName"; När det finns flera matchningar; den som har det sista giltighets datumet används. |
|ClusterX509FindValue |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta kluster certifikat. |
|ClusterX509FindValueSecondary |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta kluster certifikat. |
|ClusterX509StoreName |sträng, standard är "My" |Dynamisk|Namnet på X. 509-certifikatarkivet som innehåller kluster certifikat för att skydda kommunikationen mellan kluster. |
|EndApplicationPortRange |Int, standardvärdet är 0 |Statisk|Slut (utan inklusiv) av program portarna som hanteras av värd under systemet. Krävs om EndpointFilteringEnabled är sant i värd. |
|ServerAuthX509FindType |sträng, standard är "FindByThumbprint" |Dynamisk|Anger hur du söker efter server certifikat i arkivet som anges av ServerAuthX509StoreName-stödda värde: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta server certifikat. |
|ServerAuthX509FindValueSecondary |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta server certifikat. |
|ServerAuthX509StoreName |sträng, standard är "My" |Dynamisk|Namnet på X. 509-certifikatarkivet som innehåller Server certifikat för smärtfri-tjänsten. |
|StartApplicationPortRange |Int, standardvärdet är 0 |Statisk|Start av program portarna som hanteras av värd under systemet. Krävs om EndpointFilteringEnabled är sant i värd. |
|StateTraceInterval |Tid i sekunder, standard är 300 |Statisk|Ange TimeSpan i sekunder. Intervallet för att spåra nodens status på varje nod och upp noder på FM/FMM. |
|UserRoleClientX509FindType |sträng, standard är "FindByThumbprint" |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av UserRoleClientX509StoreName-stödda värde: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta certifikat för standard användar rollen FabricClient. |
|UserRoleClientX509FindValueSecondary |sträng, standard är "" |Dynamisk|Sök filter värde som används för att hitta certifikat för standard användar rollen FabricClient. |
|UserRoleClientX509StoreName |sträng, standard är "My" |Dynamisk|Namnet på det X. 509-certifikat arkiv som innehåller certifikat för standard användar rollen FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, standard är falskt |Dynamisk|Flagga för att ange om det ska vara tillåtet att ta bort ett nod-tillstånd för en Seed-nod |
|BuildReplicaTimeLimit|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (3600)|Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för att skapa en tillstånds känslig replik. efter vilken en varnings hälso rapport ska initieras |
|ClusterPauseThreshold|int, standard är 1|Dynamisk|Om antalet noder i systemet går under det här värdet, placering; belastnings utjämning; och redundansväxlingen har stoppats. |
|CreateInstanceTimeLimit|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (300)|Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för att skapa en tillstånds lös instans. efter vilken en varnings hälso rapport ska initieras |
|ExpectedClusterSize|int, standard är 1|Dynamisk|När klustret startas första gången. FM-tiden kommer att vänta på att många noder rapporterar sig innan de börjar placera andra tjänster. inklusive system tjänster som namn. Att öka det här värdet ökar hur lång tid det tar för ett kluster att starta. men förhindrar att de tidiga noderna blir överbelastade och även ytterligare flytter som krävs när fler noder är online. Det här värdet ska normalt anges till en liten del av den ursprungliga kluster storleken. |
|ExpectedNodeDeactivationDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60,0 \* 30)|Dynamisk|Ange TimeSpan i sekunder. Detta är den förväntade varaktigheten för en nod att slutföra inaktive ringen i. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60,0 \* 30)|Dynamisk|Ange TimeSpan i sekunder. Detta är den förväntade varaktigheten för en nod som ska uppgraderas under uppgraderingen av Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60,0 \* 30)|Dynamisk|Ange TimeSpan i sekunder. Detta är den förväntade varaktigheten för alla repliker som ska uppgraderas på en nod under program uppgraderingen. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, standard är sant|Dynamisk|Om värdet är sant; repliker med en mål replik uppsättnings storlek på 1 kommer att tillåtas att flyttas under uppgraderingen. |
|MinReplicaSetSize|int, standard är 3|Tillåts inte|Detta är den minsta replik uppsättnings storleken för FM. Om antalet aktiva FM-replikeringar sjunker under det här värdet. FM-förkastar ändringar i klustret tills minst det minsta antalet repliker återställs |
|PlacementConstraints|sträng, standard är ""|Tillåts inte|Placerings begränsningar för failover Manager-replikeringar |
|PlacementTimeLimit|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (600)|Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för att nå antalet mål repliker; efter vilken en varnings hälso rapport ska initieras |
|QuorumLossWaitDuration |Tid i sekunder, standard är MaxValue |Dynamisk|Ange TimeSpan i sekunder. Detta är den längsta tid som vi tillåter att en partition bevaras i status för kvorum. Om partitionen fortfarande förlorar kvorum efter denna varaktighet, partitionen återställs från kvorum genom att ta hänsyn till ned-replikerna som förlorade. Observera att detta kan innebära data förlust. |
|ReconfigurationTimeLimit|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (300)|Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för omkonfiguration; efter vilken en varnings hälso rapport ska initieras |
|ReplicaRestartWaitDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60,0 \* 30)|Tillåts inte|Ange TimeSpan i sekunder. Detta är ReplicaRestartWaitDuration för FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, standardvärdet är 0 | Dynamisk | Bufferten för de startnoder som behövs för att vara igång (tillsammans med kvorum för startnoder) FM bör tillåta att startnoder för totalNumSeedNodes (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes) kan gå nedåt. |
|StandByReplicaKeepDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (3600.0 \* 24 \* 7)|Tillåts inte|Ange TimeSpan i sekunder. Detta är StandByReplicaKeepDuration för FMService |
|TargetReplicaSetSize|int, standard är 7|Tillåts inte|Detta är det mål antal RM-replikeringar som Windows Fabric att underhålla. En högre siffra ger högre tillförlitlighet för FM-data. med mindre prestanda kompromisser. |
|UserMaxStandByReplicaCount |Int, standard är 1 |Dynamisk|Det maximala antalet vänte läges repliker som systemet behåller för användar tjänster. |
|UserReplicaRestartWaitDuration |Tid i sekunder, standard är 60,0 \* 30 |Dynamisk|Ange TimeSpan i sekunder. När en bestående replik slutar fungera. Windows Fabric väntar tills den här varaktigheten för repliken kommer att säkerhets kopie ras innan nya ersättnings repliker (som kräver en kopia av status) skapas. |
|UserStandByReplicaKeepDuration |Tid i sekunder, standard är 3600,0 \* 24 \* 7 |Dynamisk|Ange TimeSpan i sekunder. När en beständiga replik kommer tillbaka från ett tillstånd. den kanske redan har ersatts. Den här timern avgör hur länge FM-replikeringen ska behålla standby-repliken innan den tas bort. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, standard är 604800 |Statisk| Detta är ungefär hur länge du ska behålla åtgärder som är i ett terminalfönster. Detta beror också på StoredActionCleanupIntervalInSeconds; eftersom arbetet att rensa bara görs med det intervallet. 604800 är 7 dagar. |
|DataLossCheckPollIntervalInSeconds|int, standard är 5|Statisk|Detta är tiden mellan de kontroller som systemet utför och väntar på att data ska gå förlorade. Antalet gånger som data förlust numret markeras per intern iteration är DataLossCheckWaitDurationInSeconds/detta. |
|DataLossCheckWaitDurationInSeconds|int, standard är 25|Statisk|Den totala tiden. i sekunder; systemet väntar på att data ska gå förlorade. Detta används internt när API: et för StartPartitionDataLossAsync () anropas. |
|MinReplicaSetSize |Int, standardvärdet är 0 |Statisk|MinReplicaSetSize för FaultAnalysisService. |
|PlacementConstraints | sträng, standard är ""|Statisk| PlacementConstraints för FaultAnalysisService. |
|QuorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk|Ange TimeSpan i sekunder. QuorumLossWaitDuration för FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, standard är 600|Statisk|Den här parametern används när API: et för data förlust anropas. Den styr hur länge systemet väntar på att en replik ska tas bort efter att borttagning av repliken har anropats internt. |
|ReplicaRestartWaitDuration |Tid i sekunder, standardvärdet är 60 minuter|Statisk|Ange TimeSpan i sekunder. ReplicaRestartWaitDuration för FaultAnalysisService. |
|StandByReplicaKeepDuration| Tid i sekunder, standard är (60 *24* 7) minuter |Statisk|Ange TimeSpan i sekunder. StandByReplicaKeepDuration för FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, standard är 3600 |Statisk|Detta är hur ofta butiken ska rensas. Endast åtgärder i Terminal State; och som slutförde minst CompletedActionKeepDurationInSeconds sedan tas bort. |
|StoredChaosEventCleanupIntervalInSeconds | Int, standard är 3600 |Statisk|Detta är hur ofta butiken ska granskas för rensning. om antalet händelser är fler än 30000; rensningen används. |
|TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk|NOT_PLATFORM_UNIX_START TargetReplicaSetSize för FaultAnalysisService. |

## <a name="federation"></a>Federation

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|LeaseDuration |Tid i sekunder, standard är 30 |Dynamisk|Varaktigheten som ett lån varar mellan en nod och dess grannar. |
|LeaseDurationAcrossFaultDomain |Tid i sekunder, standard är 30 |Dynamisk|Varaktigheten som ett lån varar mellan en nod och dess grannar över fel domäner. |

## <a name="filestoreservice"></a>FileStoreService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, standard är sant|Dynamisk|Config för att avgöra om fil lagrings tjänsten accepterar överföring av segmentbaserade filer eller inte vid kopiering av programpaket. |
|AnonymousAccessEnabled | Bool, standard är sant |Statisk|Aktivera/inaktivera anonym åtkomst till FileStoreService-resurserna. |
|CommonName1Ntlmx509CommonName|sträng, standard är ""|Statisk| Det X509-certifikatets nätverks namn som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName1Ntlmx509StoreLocation|sträng, standard är "LocalMachine"|Statisk|Lagrings platsen för det X509-certifikat som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName1Ntlmx509StoreName|sträng, standard är "MY"| Statisk|Butiks namnet på det X509-certifikat som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509CommonName|sträng, standard är ""|Statisk|Det X509-certifikatets nätverks namn som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509StoreLocation|sträng, standard är "LocalMachine"| Statisk|Lagrings platsen för det X509-certifikat som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509StoreName|sträng, standard är "MY"|Statisk| Butiks namnet på det X509-certifikat som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonNameNtlmPasswordSecret|SecureString, standard är gemensamt:: SecureString ("")| Statisk|Lösen ords hemligheten som används som dirigering för att generera samma lösen ord när NTLM-autentisering används |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (5)|Dynamisk|Ange TimeSpan i sekunder. Tidsintervall mellan kontroll av disk utrymme för rapportering av hälso tillstånds händelse när disken är nära slut. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (15)|Dynamisk|Ange TimeSpan i sekunder. Tidsintervallet mellan att kontrol lera om det finns tillräckligt med utrymme på disken vid kontroll av disk utrymme för rapportering av hälso tillstånds händelser. |
|EnableImageStoreHealthReporting |bool, standard är sant    |Statisk|Config för att avgöra om fil lagrings tjänsten ska rapportera sitt hälso tillstånd. |
|FreeDiskSpaceNotificationSizeInKB|Int64, standard är 25 \* 1024 |Dynamisk|Storleken på det lediga disk utrymme som kan uppstå under hälso varningen. Det minsta värdet för den här config-och FreeDiskSpaceNotificationThresholdPercentage-konfigurationen används för att avgöra sändning av hälso varningen. |
|FreeDiskSpaceNotificationThresholdPercentage|Double, standard är 0,02 |Dynamisk|Procent andelen ledigt disk utrymme under vilken hälso varning kan uppstå. Det minsta värdet för den här config-och FreeDiskSpaceNotificationInMB-konfigurationen används för att avgöra sändning av hälso varning. |
|GenerateV1CommonNameAccount| bool, standard är sant|Statisk|Anger om du vill generera ett konto med användar namnet v1 generations algoritm. Från och med Service Fabric version 6,1; ett konto med v2-generering skapas alltid. V1-kontot krävs för uppgraderingar från/till versioner som inte stöder V2-generering (före 6,1).|
|MaxCopyOperationThreads | Uint, standardvärdet är 0 |Dynamisk| Det maximala antalet parallella filer som sekundärt kan kopiera från primär. 0 = = antal kärnor. |
|MaxFileOperationThreads | Uint, standard är 100 |Statisk| Det maximala antalet parallella trådar som tillåts utföra FileOperations (Copy/Move) i den primära. 0 = = antal kärnor. |
|MaxRequestProcessingThreads | Uint, standard är 200 |Statisk|Det maximala antalet parallella trådar som tillåts bearbeta begär anden i den primära. 0 = = antal kärnor. |
|MaxSecondaryFileCopyFailureThreshold | Uint, standard är 25|Dynamisk|Maximalt antal fil kopierings försök på den sekundära innan den får plats. |
|MaxStoreOperations | Uint, standard är 4096 |Statisk|Det maximala antalet parallella lager transaktions åtgärder som tillåts på primär. 0 = = antal kärnor. |
|NamingOperationTimeout |Tid i sekunder, standard är 60 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för att utföra namngivnings åtgärden. |
|PrimaryAccountNTLMPasswordSecret | SecureString, standard är tomt |Statisk| Lösen ords hemligheten som används som dirigering för att generera samma lösen ord när NTLM-autentisering används. |
|PrimaryAccountNTLMX509StoreLocation | sträng, standard är "LocalMachine"|Statisk| Lagrings platsen för det X509-certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|PrimaryAccountNTLMX509StoreName | sträng, standard är "MY"|Statisk| Butiks namnet på det X509-certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|PrimaryAccountNTLMX509Thumbprint | sträng, standard är ""|Statisk|Tumavtrycket för det X509-certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|PrimaryAccountType | sträng, standard är "" |Statisk|Huvud-startvärdet för huvudobjektet för att ACL: en FileStoreService-resurser. |
|PrimaryAccountUserName | sträng, standard är "" |Statisk|Huvud kontots användar namn för huvudobjektet för att ACL: en FileStoreService-resurser. |
|PrimaryAccountUserPassword | SecureString, standard är tomt |Statisk|Det primära konto lösen ordet för huvudobjektet för att ACL: en FileStoreService-resurser. |
|QueryOperationTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för att köra en fråga. |
|SecondaryAccountNTLMPasswordSecret | SecureString, standard är tomt |Statisk| Lösen ords hemligheten som används som dirigering för att generera samma lösen ord när NTLM-autentisering används. |
|SecondaryAccountNTLMX509StoreLocation | sträng, standard är "LocalMachine" |Statisk|Lagrings platsen för det X509-certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountNTLMX509StoreName | sträng, standard är "MY" |Statisk|Butiks namnet på det X509-certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountNTLMX509Thumbprint | sträng, standard är ""| Statisk|Tumavtrycket för det X509-certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountType | sträng, standard är ""|Statisk| Den sekundära startvärden för huvudobjektet för att ACL: en FileStoreService-resurser. |
|SecondaryAccountUserName | sträng, standard är ""| Statisk|Det sekundära kontots användar namn för huvudobjektet för att ACL: en FileStoreService-resurser. |
|SecondaryAccountUserPassword | SecureString, standard är tomt |Statisk|Lösen ordet för det sekundära kontot för huvudobjektet för att ACL: en FileStoreService-resurser. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, standard är 500|Dynamisk|Fördröjningen för fil kopierings försök (i millisekunder).|
|UseChunkContentInTransportMessage|bool, standard är sant|Dynamisk|Flaggan för att använda den nya versionen av överförings protokollet som introducerades i v 6.4. Den här protokoll versionen använder Service Fabric-transport för att överföra filer till avbildnings arkivet, vilket ger bättre prestanda än SMB-protokoll som används i tidigare versioner. |

## <a name="healthmanager"></a>HealthManager

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, standard är falskt |Statisk|Utvärderings princip för kluster hälsa: Aktivera utvärdering av hälso tillstånd per program typ. |
|EnableNodeTypeHealthEvaluation |Bool, standard är falskt |Statisk|Utvärderings princip för kluster hälsa: Aktivera typ av hälso utvärdering per nodtyp. |
|MaxSuggestedNumberOfEntityHealthReports|Int, standard är 100 |Dynamisk|Det maximala antalet hälso rapporter som en entitet kan ha innan du får problem med övervaknings logiken för hälso rapporter. Varje hälsoentitet ska ha ett relativt litet antal hälso rapporter. Om antalet rapporter hamnar ovanför det här talet. Det kan finnas problem med övervaknings enhetens implementering. En entitet med för många rapporter flaggas via en varnings hälso rapport när entiteten utvärderas. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, standard är falskt |Statisk|Utvärderings princip för kluster hälsa: varningar behandlas som fel. |
|MaxPercentUnhealthyApplications | Int, standardvärdet är 0 |Statisk|Utvärderings princip för kluster hälsa: Max procent av felaktiga program som tillåts för klustret är felfri. |
|MaxPercentUnhealthyNodes | Int, standardvärdet är 0 |Statisk|Utvärderings princip för kluster hälsa: Max procent av felaktiga noder tillåts för att klustret ska vara felfritt. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, standard är 10|Statisk|Hälso utvärderings princip för kluster uppgradering: maximalt antal felaktiga noder i procent tillåts för att klustret ska vara felfritt |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, standard är 15|Statisk|Hälso utvärderings princip för kluster uppgradering: högsta procent andel av antal felaktiga noder i en uppgraderings domän som tillåts för att klustret ska vara felfritt |

## <a name="hosting"></a>Värd

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Heltal, standard är 10 |Dynamisk|Antal gånger det inte gick att aktivera system försök innan det får upp |
|ActivationMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|Vid varje kontinuerlig aktivering, försöker systemet att aktivera igen för upp till ActivationMaxFailureCount. ActivationMaxRetryInterval anger vänte tid i intervallet innan ett nytt försök efter varje aktiverings fel |
|ActivationRetryBackoffInterval |Tid i sekunder, standard är 5 |Dynamisk|Backoff intervall vid varje aktiverings haveri; Vid varje kontinuerlig aktivering, försöker systemet att aktivera igen med MaxActivationFailureCount. Återförsöksintervall för varje försök är en produkt med kontinuerlig aktiverings fel och aktiverings intervall. |
|ActivationTimeout| TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (180)|Dynamisk| Ange TimeSpan i sekunder. Tids gränsen för program aktivering. inaktive ring och uppgradering. |
|ApplicationHostCloseTimeout| TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk| Ange TimeSpan i sekunder. När infrastruktur stängningen identifieras i självaktiverade processer. FabricRuntime stänger alla replikerna i användarens värd process (ApplicationHost). Detta är tids gränsen för stängnings åtgärden. |
| CnsNetworkPluginCnmUrlPort | wstring, standard är L "48080" | Statisk | URL-port för Azure CNM API |
| CnsNetworkPluginCnsUrlPort | wstring, standard är L "10090" | Statisk | URL-port för Azure CNS |
|Container Service arguments|sträng, standard är "-H localhost: 2375-H npipe://"|Statisk|Service Fabric (SF) hanterar Docker daemon (förutom på Windows-klientdatorer som Win10). Med den här konfigurationen kan användaren ange anpassade argument som ska skickas till Docker daemon när den startas. När anpassade argument anges skickar Service Fabric inte några andra argument till Docker-motorn förutom argumentet--pidfile. Användarna bör därför inte ange argumentet--pidfile som en del av deras kund argument. Dessutom bör de anpassade argumenten se till att Docker daemon lyssnar på standard namns pipe i Windows (eller UNIX-domänsuffix på Linux) för att Service Fabric kunna kommunicera med det.|
|ContainerServiceLogFileMaxSizeInKb|int, standard är 32768|Statisk|Maximal fil storlek för logg filen som genererades av Docker-behållare.  Endast Windows.|
|ContainerImageDownloadTimeout|int, antal sekunder, standard är 1200 (20 minuter)|Dynamisk|Antal sekunder innan tids gränsen för avbildningen laddas ned.|
|ContainerImagesToSkip|sträng, bild namn avgränsade med lodräta linje tecken, standard är ""|Statisk|Namnet på en eller flera behållar avbildningar som inte ska tas bort.  Används med parametern PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|sträng, standard är "sfcontainerlogs"|Statisk|Fil namns prefix för loggfiler som genereras av Docker-behållare.  Endast Windows.|
|ContainerServiceLogFileRetentionCount|int, standard är 10|Statisk|Antalet loggfiler som genererats av Docker-behållare innan loggfiler skrivs över.  Endast Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk| Ange TimeSpan i sekunder. Timeout-värdet för Sync FabricCreateRuntime-anropet |
|DefaultContainerRepositoryAccountName|sträng, standard är ""|Statisk|Standardautentiseringsuppgifter som används i stället för autentiseringsuppgifter som anges i ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|sträng, standard är ""|Statisk|Standard lösen ords referenser som används i stället för autentiseringsuppgifter som anges i ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|sträng, standard är ""|Statisk|Om strängen inte är tom, kan värdet vara "krypterat" eller "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, standard är falskt|Statisk|Som standard läggs tjänst namnet till i SF DNS-namnet för Container Services. Den här funktionen stoppar det här beteendet så att inget läggs till i SF DNS-namnet som standard i lösnings väg.|
|DeploymentMaxFailureCount|int, standard är 20| Dynamisk|Ett nytt försök att utföra program distributionen kommer att göras för DeploymentMaxFailureCount gånger innan distributionen av programmet på noden avbryts.| 
|DeploymentMaxRetryInterval| TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (3600)|Dynamisk| Ange TimeSpan i sekunder. Max återförsöksintervall för distributionen. Vid varje fort löp ande fel beräknas återförsöksintervall som min (DeploymentMaxRetryInterval; Antal kontinuerliga haverier * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (10)|Dynamisk|Ange TimeSpan i sekunder. Intervall för distributions problem. Vid alla kontinuerliga distributioner gör systemet ett nytt försök att distribuera upp till MaxDeploymentFailureCount. Återförsöksintervall är en produkt med kontinuerlig distributions fel och distributionens backoff-intervall. |
|DisableContainers|bool, standard är falskt|Statisk|Konfiguration för att inaktivera behållare – används i stället för DisableContainerServiceStartOnContainerActivatorOpen som är föråldrad config |
|DisableDockerRequestRetry|bool, standard är falskt |Dynamisk| Som standard kommunicerar sa med DD (Docker dameon) med en tids gräns på "DockerRequestTimeout" för varje http-begäran som skickas till den. Om DD inte svarar inom den här tids perioden; SF skickar begäran på nytt om den översta nivån fortfarande har kvar tiden.  Med HyperV-behållare; DD ibland tar det mycket mer tid att ta upp behållaren eller inaktivera den. I sådana fall är det en begäran från sa-perspektivet och SF-försöket. Ibland verkar detta vara att lägga till mer belastning på DD. Med den här konfigurationen kan du inaktivera det här försöket och vänta tills DD har svarat. |
|DnsServerListTwoIps | Bool, standard är falskt | Statisk | Med den här flaggan lägger du till den lokala DNS-servern två gånger för att hjälpa till att lösa tillfälliga problem. |
| DockerTerminateOnLastHandleClosed | bool, standard är sant | Statisk | Som standard om Fabrichost returnerar hanterar "dockerd" (baserat på: SkipDockerProcessManagement = = false) den här inställningen anger vad som händer när antingen Fabrichost returnerar eller dockerd kraschar. När den är inställd på `true` om någon av processerna kraschar tvingas alla behållare att avslutas av HCS uppdateringsklienten. Om värdet är inställt på `false` behållarna fortsätter att köras. Obs! föregående till 8,0 det här beteendet var avsiktligt motsvarande `false` . Standardvärdet för `true` här är vad vi förväntar sig att hända som standard när vår rensnings logik används för att starta om de här processerna. |
| DoNotInjectLocalDnsServer | bool, standard är falskt | Statisk | Hindrar körningen från att mata in den lokala IP-adressen som DNS-server för behållare. |
|EnableActivateNoWindow| bool, standard är falskt|Dynamisk| Den aktiverade processen skapas i bakgrunden utan någon konsol. |
|EnableContainerServiceDebugMode|bool, standard är sant|Statisk|Aktivera/inaktivera loggning för Docker-behållare.  Endast Windows.|
|EnableDockerHealthCheckIntegration|bool, standard är sant|Statisk|Aktiverar integrering av Docker HEALTHCHECK-händelser med Service Fabric system hälso rapport |
|EnableProcessDebugging|bool, standard är falskt|Dynamisk| Aktiverar start av program värdar under fel sökning |
|EndpointProviderEnabled| bool, standard är falskt|Statisk| Möjliggör hantering av slut punkts resurser efter infrastruktur resurs. Kräver specificering av port intervallet för start-och slut program i FabricNode. |
|FabricContainerAppsEnabled| bool, standard är falskt|Statisk| |
|FirewallPolicyEnabled|bool, standard är falskt|Statisk| Aktiverar öppning av brand Väggs portar för slut punkts resurser med explicita portar angivna i ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk|Ange TimeSpan i sekunder. Timeout-värdet för CodePackageActivationContext-anrop. Detta gäller inte för ad hoc-tjänster. |
|GovernOnlyMainMemoryForProcesses|bool, standard är falskt|Statisk|Standard beteendet för resurs styrning är att ställa in en gräns som anges i MemoryInMB för den totala mängden minne (RAM + växling) som processen använder. Om gränsen överskrids. processen får ett OutOfMemory-undantag. Om den här parametern har angetts till true; gränsen tillämpas endast på mängden RAM-minne som en process kommer att använda. Om den här gränsen överskrids, och om den här inställningen är true; sedan byter operativ systemet huvud minnet till disk. |
|IPProviderEnabled|bool, standard är falskt|Statisk|Möjliggör hantering av IP-adresser. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, standard är falskt|Statisk|Anger om DefaultContainerRepositoryPassword är krypterad eller inte.|
|LinuxExternalExecutablePath|sträng, standard är "/usr/bin/" |Statisk|Den primära katalogen för externa körbara kommandon på noden.|
|NTLMAuthenticationEnabled|bool, standard är falskt|Statisk| Aktiverar stöd för användning av NTLM av kod paket som körs som andra användare så att processerna mellan datorer kan kommunicera säkert. |
|NTLMAuthenticationPasswordSecret|SecureString, standard är gemensamt:: SecureString ("")|Statisk|Är en krypterad som används för att generera lösen ordet för NTLM-användare. Måste anges om NTLMAuthenticationEnabled är true. Verifieras av distributören. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (3)|Dynamisk|Ange TimeSpan i sekunder. Miljöbaserade inställningar är det periodiska intervallet som används för att söka efter nya certifikat som ska användas för FileStoreService NTLM-konfiguration. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (4)|Dynamisk| Ange TimeSpan i sekunder. Tids gränsen för att konfigurera NTLM-användare med hjälp av certifikatets egna namn. NTLM-användare krävs för FileStoreService-resurser. |
|PruneContainerImages|bool, standard är falskt|Dynamisk| Ta bort oanvända program behållar avbildningar från noder. När en ApplicationType avregistreras från Service Fabric-klustret, tas behållar avbildningarna som användes av programmet bort på noder där den hämtades av Service Fabric. Rensningen körs varje timme, så det kan ta upp till en timme (och tid att rensa avbildningen) för avbildningar som ska tas bort från klustret.<br>Service Fabric laddar aldrig ned eller tar bort bilder som inte är relaterade till ett program.  Orelaterade bilder som hämtades manuellt eller som annars måste tas bort uttryckligen.<br>Avbildningar som inte ska tas bort kan anges i ContainerImagesToSkip-parametern.| 
|RegisterCodePackageHostTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk| Ange TimeSpan i sekunder. Timeout-värdet för FabricRegisterCodePackageHost. Detta gäller endast för program värdar med flera kod paket som FWP |
|RequestTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (30)|Dynamisk| Ange TimeSpan i sekunder. Detta representerar tids gränsen för kommunikation mellan användarens program värd och infrastruktur process för olika värdbaserade åtgärder, till exempel fabriks registrering. körnings registrering. |
|RunAsPolicyEnabled| bool, standard är falskt|Statisk| Aktiverar körning av kod paket som annan lokal användare än den användare under vilken Fabric-processen körs. För att aktivera den här principens infrastruktur måste köras som SYSTEM eller som användare med SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk|Ange TimeSpan i sekunder. Timeout-värdet för ServiceFactory-anropet (tillstånds lös/tillstånds känslig) |
|ServiceTypeDisableFailureThreshold |Heltal, standard är 1 |Dynamisk|Detta är tröskelvärdet för antalet haverier efter vilka FailoverManager (FM) meddelas om att inaktivera tjänst typen på noden och testa en annan nod för placering. |
|ServiceTypeDisableGraceInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (30)|Dynamisk|Ange TimeSpan i sekunder. Tidsintervall efter vilket tjänst typen kan inaktive ras |
|ServiceTypeRegistrationTimeout |Tid i sekunder, standard är 300 |Dynamisk|Maximal tid som tillåts för ServiceType att registreras med Fabric |
|UseContainerServiceArguments|bool, standard är sant|Statisk|Den här konfigurationen instruerar att vara värd för att hoppa över att skicka argument (anges i config Container Service arguments) till Docker daemon.|

## <a name="httpgateway"></a>HttpGateway

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, standard är 50 |Statisk| Antalet läsningar att publicera i http-server kön. Detta styr antalet samtidiga förfrågningar som kan uppfyllas av HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tid i sekunder, standard är 30 |Statisk|Ange TimeSpan i sekunder. Intervallet då http-gatewayen skickar ackumulerade hälso rapporter till hälso chefen. |
|HttpStrictTransportSecurityHeader|sträng, standard är ""|Dynamisk| Ange det HTTP-stränga transport säkerhets huvud värde som ska tas med i varje svar som skickas av HttpGateway. När den är inställd på tom sträng; den här rubriken tas inte med i Gateway-svaret.|
|IsEnabled|Bool, standard är falskt |Statisk| Aktiverar eller inaktiverar HttpGateway. HttpGateway är inaktive rad som standard. |
|MaxEntityBodySize |Uint, standard är 4194304 |Dynamisk|Ger den maximala storleken på texten som kan förväntas från en http-begäran. Standardvärdet är 4 MB. Httpgateway kommer inte att kunna utföra en begäran om den har en storleks text som > det här värdet. Minsta Läs segment storlek är 4096 byte. Detta måste vara >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Enabled |Bool, standard är falskt |Statisk|Flaggan Enabled för ImageStoreService. Standard: falskt |
|MinReplicaSetSize | Int, standard är 3 |Statisk|MinReplicaSetSize för ImageStoreService. |
|PlacementConstraints | sträng, standard är "" |Statisk| PlacementConstraints för ImageStoreService. |
|QuorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk| Ange TimeSpan i sekunder. QuorumLossWaitDuration för ImageStoreService. |
|ReplicaRestartWaitDuration | Tid i sekunder, standard är 60,0 \* 30 |Statisk|Ange TimeSpan i sekunder. ReplicaRestartWaitDuration för ImageStoreService. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 3600,0 \* 2 |Statisk| Ange TimeSpan i sekunder. StandByReplicaKeepDuration för ImageStoreService. |
|TargetReplicaSetSize | Int, standard är 7 |Statisk|TargetReplicaSetSize för ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, standard är 1 |Dynamisk|Flagga som anger om minnes inställningarna ska konfigureras automatiskt och konfigureras dynamiskt. Om värdet är noll används inställningarna för minnes konfigurationen direkt och ändras inte utifrån system villkoren. Om någon sedan konfigureras minnes inställningarna automatiskt och kan ändras baserat på system villkor. |
|MaximumDestagingWriteOutstandingInKB | Int, standardvärdet är 0 |Dynamisk|Antalet KB som tillåter den delade loggen att gå före den dedikerade loggen. Använd 0 om du vill indikera ingen gräns.
|SharedLogId |sträng, standard är "" |Statisk|Unikt GUID för delad logg behållare. Använd "" om standard Sök vägen används under infrastruktur data roten. |
|SharedLogPath |sträng, standard är "" |Statisk|Sökväg och fil namn till platsen där den delade logg behållaren ska placeras. Använd "" om du vill använda standard Sök vägen under infrastruktur data roten. |
|SharedLogSizeInMB |Int, standard är 8192 |Statisk|Antalet MB att allokera i den delade logg behållaren. |
|SharedLogThrottleLimitInPercentUsed|int, standardvärdet är 0 | Statisk | Procent andelen av användningen av den delade loggen som kommer att inducera begränsning. Värdet måste vara mellan 0 och 100. Värdet 0 betyder att standardvärdet för procent andelen används. Värdet 100 innebär ingen begränsning alls. Ett värde mellan 1 och 99 anger den procent andel av logg användningen som inträffar ovan och som begränsningen ska ske till. om den delade loggen till exempel är 10 GB och värdet är 90, kommer begränsningen att ske när 9 GB används. Du rekommenderas att använda standardvärdet.|
|WriteBufferMemoryPoolMaximumInKB | Int, standardvärdet är 0 |Dynamisk|Antalet KB som den skrivbara lagringspoolen i cacheminnet ska växa till. Använd 0 om du vill indikera ingen gräns. |
|WriteBufferMemoryPoolMinimumInKB |Int, standard är 8388608 |Dynamisk|Antalet KB som ursprungligen allokeras för lagringspoolen för skrivcache. Använd 0 om du vill indikera att ingen gräns standard ska vara konsekvent med SharedLogSizeInMB nedan. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är falskt|Statisk|Flagga som styr närvaro och status för den hanterade identitetsprovider i klustret. Detta är ett krav för att använda funktionen för hanterade identiteter i Service Fabric program.|

## <a name="management"></a>Hantering

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (5)|Dynamisk|Ange TimeSpan i sekunder. Rensnings intervallet som tillåts för avregistrering av program typ under automatisk rensning av program typ.|
|AzureStorageMaxConnections | Int, standard är 5000 |Dynamisk|Maximalt antal samtidiga anslutningar till Azure Storage. |
|AzureStorageMaxWorkerThreads | Int, standard är 25 |Dynamisk|Det maximala antalet arbets trådar parallellt. |
|AzureStorageOperationTimeout | Tid i sekunder, standard är 6000 |Dynamisk|Ange TimeSpan i sekunder. Tids gränsen för xstore-åtgärden slutfördes. |
|CleanupApplicationPackageOnProvisionSuccess|bool, standard är sant |Dynamisk|Aktiverar eller inaktiverar automatisk rensning av programpaketet vid lyckad etablering.
|CleanupUnusedApplicationTypes|Bool, standard är falskt |Dynamisk|Den här konfigurationen om den är aktive rad, tillåter att automatiskt avregistrera oanvända program typ versioner som hoppar över de senaste tre oanvända versionerna, vilket frigör det disk utrymme som upptas av avbildnings arkivet. Den automatiska rensningen aktive ras i slutet av lyckad etablering för den specifika typen av app och körs regelbundet en gång per dag för alla program typer. Antalet oanvända versioner att hoppa över kan konfigureras med parametern "MaxUnusedAppTypeVersionsToKeep". <br/> *Bästa praxis är att använda `true` .*
|DisableChecksumValidation | Bool, standard är falskt |Statisk| Med den här konfigurationen kan vi aktivera eller inaktivera verifiering av kontroll Summa under applikations etablering. |
|DisableServerSideCopy | Bool, standard är falskt |Statisk|Den här konfigurationen aktiverar eller inaktiverar kopia av programpaket på Server sidan på avbildnings Arkiv under applikations etablering. |
|ImageCachingEnabled | Bool, standard är sant |Statisk|Med den här konfigurationen kan vi aktivera eller inaktivera cachelagring. |
|ImageStoreConnectionString |SecureString |Statisk|Anslutnings sträng till roten för avbildnings arkiv. |
|ImageStoreMinimumTransferBPS | Int, standard är 1024 |Dynamisk|Den minsta överföringshastigheten mellan klustret och avbildnings arkiv. Det här värdet används för att fastställa tids gränsen vid åtkomst till den externa avbildnings arkiv. Ändra bara det här värdet om svars tiden mellan klustret och avbildnings arkiv är hög för att det ska gå att ladda ned klustret från den externa avbildnings arkiv. |
|MaxUnusedAppTypeVersionsToKeep | Int, standard är 3 |Dynamisk|Den här konfigurationen definierar antalet oanvända program typ versioner som ska hoppas över vid rensning. Den här parametern kan bara användas om parametern CleanupUnusedApplicationTypes har Aktiver ATS. <br/>*Den allmänna rekommenderade metoden är att använda standardvärdet ( `3` ). Värden som är mindre än 1 är ogiltiga.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standard är ingen|Dynamisk|Anger uppsättningen MetricActivityThresholds för måtten i klustret. Balanseringen fungerar om maxNodeLoad är större än MetricActivityThresholds. För defragments mått definierar du mängden belastning som är lika med eller lägre som Service Fabric kommer att betrakta noden som Tom |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Anger uppsättningen MetricBalancingThresholds för måtten i klustret. Balanseringen fungerar om maxNodeLoad/minNodeLoad är större än MetricBalancingThresholds. Defragmentering fungerar om maxNodeLoad/minNodeLoad i minst en FD eller UD är mindre än MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Bestämmer den del av belastningen som fastnar med repliken när den byts ut tar värdet mellan 0 (inläsningen fungerar inte med repliken) och 1 (belastnings käppar med replik – standard) |

## <a name="namingservice"></a>NamingService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, standardvärdet är 0 |Statisk|Det maximala antalet poster som behålls i cachen för LRU service-beskrivning på namngivnings-gatewayen (inställt på 0 för ingen begränsning). |
|MaxClientConnections |Int, standard är 1000 |Dynamisk|Det högsta tillåtna antalet klient anslutningar per Gateway. |
|MaxFileOperationTimeout |Tid i sekunder, standard är 30 |Dynamisk|Ange TimeSpan i sekunder. Den längsta tillåtna tids gränsen för fil lagrings tjänst åtgärden. Begär Anden som anger en större tids gräns kommer att avvisas. |
|MaxIndexedEmptyPartitions |Int, standard är 1000 |Dynamisk|Det maximala antalet tomma partitioner som ska fortsätta att indexeras i Notification cache för att synkronisera åter anslutning av klienter. Alla tomma partitioner ovanför det här antalet tas bort från indexet i stigande versions ordning. Att återansluta klienter kan fortfarande synkronisera och ta emot missade uppdateringar av partitionen. men protokollet för synkronisering blir dyrare. |
|MaxMessageSize |Int, standard är 4 \* 1024 \* 1024 |Statisk|Maximal meddelande storlek för kommunikation mellan klienter när du använder namn. DOS-avgrepp, Standardvärdet är 4 MB. |
|MaxNamingServiceHealthReports | Int, standard är 10 |Dynamisk|Det maximala antalet långsamma åtgärder som namngivningen av lagrings tjänsten rapporterar i en tidpunkt. Om 0; alla långsamma åtgärder skickas. |
|MaxOperationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange TimeSpan i sekunder. Maximal tids gräns som tillåts för klient åtgärder. Begär Anden som anger en större tids gräns kommer att avvisas. |
|MaxOutstandingNotificationsPerClient |Int, standard är 1000 |Dynamisk|Det maximala antalet väntande meddelanden innan en klient registrering tvingas stängas av gatewayen. |
|MinReplicaSetSize | Int, standard är 3 |Tillåts inte| Det minsta antalet Naming Service repliker som krävs för att kunna skriva till en uppdatering. Om det finns färre repliker än vad som är aktivt i systemet kan Tillförlitlighets systemet neka uppdateringar till Naming Service arkivet förrän replikerna återställs. Det här värdet ska aldrig vara större än TargetReplicaSetSize. |
|PartitionCount |Int, standard är 3 |Tillåts inte|Antalet partitioner i Naming Services arkivet som ska skapas. Varje partition äger en enda partitionsnyckel som motsvarar dess index. så partitionera nycklar [0; PartitionCount] finns. Om du ökar antalet Naming Service-partitioner ökar den skala som Naming Service kan utföra på genom att minska den genomsnittliga mängden data som innehas av valfri säkerhets kopie replik uppsättning. till en kostnad av ökad användning av resurser (sedan PartitionCount * ReplicaSetSize-tjänsterepliker måste underhållas).|
|PlacementConstraints | sträng, standard är "" |Tillåts inte| Placerings begränsning för Naming Service. |
|QuorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Tillåts inte| Ange TimeSpan i sekunder. När en Naming Service får kvorum förlust, den här timern startar. När den upphör att gälla ser FM-replikeringen ut som förlorad. och försöker återställa kvorum. Detta kan leda till data förlust. |
|RepairInterval | Tid i sekunder, standard är 5 |Statisk| Ange TimeSpan i sekunder. Intervall i vilken namngivnings inkonsekvensen i reparationen mellan auktoritets ägaren och namn ägaren kommer att starta. |
|ReplicaRestartWaitDuration | Tid i sekunder, standard är (60,0 * 30)|Tillåts inte| Ange TimeSpan i sekunder. När en Naming Service replik slutar fungera; den här timern startar. När den går ut börjar FM-filerna att ersätta de repliker som är nere (den har ännu inte beaktat att de förlorats). |
|ServiceDescriptionCacheLimit | Int, standardvärdet är 0 |Statisk| Det maximala antalet poster som behålls i cachen för LRU service-beskrivningen på namngivnings lagrings tjänsten (anges till 0 för ingen begränsning). |
|ServiceNotificationTimeout |Tid i sekunder, standard är 30 |Dynamisk|Ange TimeSpan i sekunder. Den tids gräns som användes när tjänst meddelanden levererades till klienten. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 3600,0 * 2 |Tillåts inte| Ange TimeSpan i sekunder. När en Naming Service replik kommer tillbaka från ett läge. den kanske redan har ersatts. Den här timern avgör hur länge FM-replikeringen ska behålla standby-repliken innan den tas bort. |
|TargetReplicaSetSize |Int, standard är 7 |Tillåts inte|Antalet replik uppsättningar för varje partition i Naming Service arkivet. Om du ökar antalet replik uppsättningar ökar Tillförlitlighets nivån för informationen i Naming Service Store. att minska ändringen att informationen kommer att gå förlorad till följd av nodfel. till en kostnad av ökad belastning på Windows Fabric och hur lång tid det tar att utföra uppdateringar av namngivnings data.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Procent andel för Node-kapacitet per mått namn; används som en buffert för att hålla en viss kostnads fri plats på en nod för redundansväxlingen. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statisk|En samling nod kapaciteter för olika mått. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statisk|Beskriver de fel domäner som en nod tillhör. Fel domänen definieras via en URI som beskriver nodens plats i data centret.  Fel domänens URI: er har formatet fd:/fd/följt av ett URI-söksegment.|
|UpgradeDomainId |sträng, standard är "" |Statisk|Beskriver uppgraderings domänen som en nod tillhör. |

## <a name="nodeproperties"></a>NodeProperties

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statisk|En samling sträng nyckel/värde-par för Node-egenskaper. |

## <a name="paas"></a>PaaS

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ClusterId |sträng, standard är "" |Tillåts inte|X509 certifikat arkiv som används av infrastruktur resurser för konfigurations skydd. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Räknare |Sträng | Dynamisk |Kommaavgränsad lista över prestanda räknare som ska samlas in. |
|IsEnabled |Bool, standard är sant | Dynamisk |Flagga anger om prestanda räknar samlingen på den lokala noden är aktive rad. |
|MaxCounterBinaryFileSizeInMB |Int, standard är 1 | Dynamisk |Maximal storlek (i MB) för varje binär prestanda räknar fil. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, standard är 10 | Dynamisk |Högsta intervall (i sekunder) efter vilken en ny binär prestanda räknare skapas. |
|SamplingIntervalInSeconds |Int, standard är 60 | Dynamisk |Samplings intervall för prestanda räknare som samlas in. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för tillhörighets begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|ApplicationCapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitets begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|AutoDetectAvailableResources|bool, standard är sant|Statisk|Den här konfigurationen utlöser automatisk identifiering av tillgängliga resurser på noden (processor och minne) när den här konfigurationen är inställd på Sant – vi kommer att läsa verkliga kapaciteter och korrigera dem om användaren har angett felaktig funktions kapacitet eller inte definierar dem alls om den här konfigurationen är inställd på falskt – vi kommer att spåra en varning om att användaren har angett Felaktiga noder. men vi kommer inte att åtgärda dem. Det innebär att användaren vill ha den kapacitet som anges som > än vad som är i själva verket eller om kapaciteten är odefinierad. Det kommer att anta obegränsad kapacitet |
|BalancingDelayAfterNewNode | Tid i sekunder, standard är 120 |Dynamisk|Ange TimeSpan i sekunder. Starta inte balansering av aktiviteter inom den här perioden efter att du har lagt till en ny nod. |
|BalancingDelayAfterNodeDown | Tid i sekunder, standard är 120 |Dynamisk|Ange TimeSpan i sekunder. Starta inte balansering av aktiviteter inom den här perioden efter en nod ned-händelse. |
|BlockNodeInUpgradeConstraintPriority | Int, standard är-1 |Dynamisk|Anger prioriteten för kapacitets begränsning: 0: hårt; 1: mjuk; negativt: ignorera  |
|CapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitets begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, standard är 20 | Dynamisk|Definierar antalet gånger i rad som ResourceBalancer-utfärdade rörelser tas bort innan diagnostiken utförs och hälso varningar genereras. Negativt: inga varningar har spridits i det här tillståndet. |
|ConstraintFixPartialDelayAfterNewNode | Tid i sekunder, standard är 120 |Dynamisk| Ange TimeSpan i sekunder. DDo inte Faulydomain och UpgradeDomain begränsnings överträdelser inom den här perioden efter att en ny nod har lagts till. |
|ConstraintFixPartialDelayAfterNodeDown | Tid i sekunder, standard är 120 |Dynamisk| Ange TimeSpan i sekunder. Korrigera inte Faulydomain-och UpgradeDomain-begränsnings överträdelser inom den här perioden efter en nods händelse. |
|ConstraintViolationHealthReportLimit | Int, standard är 50 |Dynamisk| Definierar antalet gånger som strider mot repliken måste vara permanenta innan diagnostiken utförs och hälso rapporter genereras. |
|DetailedConstraintViolationHealthReportLimit | Int, standard är 200 |Dynamisk| Definierar antalet gånger som strider mot repliken måste vara permanenta innan diagnostiken utförs och detaljerade hälso rapporter genereras. |
|DetailedDiagnosticsInfoListLimit | Int, standard är 15 |Dynamisk| Definierar antalet diagnostiska poster (med detaljerad information) per begränsning som ska inkluderas före trunkering i diagnostik.|
|DetailedNodeListLimit | Int, standard är 15 |Dynamisk| Definierar antalet noder per begränsning som ska inkluderas före trunkering i de ej placerade replik rapporterna. |
|DetailedPartitionListLimit | Int, standard är 15 |Dynamisk| Definierar antalet partitioner per diagnostisk post för en begränsning som ska inkluderas före trunkering i diagnostik. |
|DetailedVerboseHealthReportLimit | Int, standard är 200 | Dynamisk|Definierar antalet gånger som en ej placerad replik måste vara permanent avplacerad innan detaljerade hälso rapporter genereras. |
|EnforceUserServiceMetricCapacities|bool, standard är falskt | Statisk |Aktiverar Fabric Services-skydd. Alla användar tjänster finns under ett jobb objekt/cgroup och begränsas till en viss mängd resurser. Detta måste vara statiskt (kräver omstart av Fabrichost returnerar) när du skapar/tar bort objekt för användar jobb och ställer in gränser i utfört när du öppnar Fabric-värden. |
|FaultDomainConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger prioriteten för fel domän begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|GlobalMovementThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange TimeSpan i sekunder. Ange längden på det förflutna intervall som ska användas för att spåra per domän replik förflyttning (används tillsammans med GlobalMovementThrottleThreshold). Kan anges till 0 om du vill ignorera global begränsning helt. |
|GlobalMovementThrottleThreshold | Uint, standard är 1000 |Dynamisk| Maximalt antal förflyttningar som tillåts i balanserings fasen i det tidigare intervallet som anges av GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, standardvärdet är 0 | Dynamisk|Maximalt antal förflyttningar som tillåts i balans fasen i det förflutna intervallet som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. |
|GlobalMovementThrottleThresholdForPlacement | Uint, standardvärdet är 0 |Dynamisk| Maximalt antal tillåtna förflyttningar i placerings fasen i det förflutna intervallet som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns.|
|GlobalMovementThrottleThresholdPercentage|Double, standardvärdet är 0|Dynamisk|Maximalt antal tillåtna förflyttningar i balans-och placerings faser (uttryckt i procent av det totala antalet repliker i klustret) i det förflutna intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. Om både den här och GlobalMovementThrottleThreshold anges. sedan används mer restriktivt gräns.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, standardvärdet är 0|Dynamisk|Maximalt antal förflyttningar som tillåts i balans fasen (uttryckt i procent av det totala antalet repliker i PLB) i det förflutna intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. Om både den här och GlobalMovementThrottleThresholdForBalancing anges. sedan används mer restriktivt gräns.|
|InBuildThrottlingAssociatedMetric | sträng, standard är "" |Statisk| Det associerade mått namnet för den här begränsningen. |
|InBuildThrottlingEnabled | Bool, standard är falskt |Dynamisk| Avgör om begränsningen i bygge är aktive rad. |
|InBuildThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk|Maximalt antal inbyggda repliker tillåts globalt. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, standard är falskt | Dynamisk|Bestämmer om en typ av uppdatering av redundans ska avbryta eller köra långsam balansering. Med angiven "false"-balanserad körning avbryts om FailoverUnit: skapas/tas bort. saknar repliker; den primära replik platsen eller det ändrade antalet repliker har ändrats. Balansering av körning avbryts inte i andra fall, om FailoverUnit: har extra repliker; en replik flagga har ändrats; endast ändrad partitions version eller andra fall. |
|MinConstraintCheckInterval | Tid i sekunder, standard är 1 |Dynamisk| Ange TimeSpan i sekunder. Definierar den minsta tid som måste förflyta innan två efterföljande villkors kontroll avrundas. |
|MinLoadBalancingInterval | Tid i sekunder, standard är 5 |Dynamisk| Ange TimeSpan i sekunder. Definierar den minsta tid som måste passera innan två i följd avrundas. |
|MinPlacementInterval | Tid i sekunder, standard är 1 |Dynamisk| Ange TimeSpan i sekunder. Definierar den minsta tid som måste passera innan två på varandra följande placering avrundas. |
|MoveExistingReplicaForPlacement | Bool, standard är sant |Dynamisk|Inställning som avgör om den befintliga repliken ska flyttas under placeringen. |
|MovementPerPartitionThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange TimeSpan i sekunder. Ange längden på det förflutna intervall som du vill använda för att spåra replikerings förflyttningar för varje partition (används tillsammans med MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, standard är 50 |Dynamisk| Ingen balanserande förflyttning sker för en partition om antalet balanserade relaterade rörelser för repliker av den partitionen har nått eller överskridit MovementPerFailoverUnitThrottleThreshold i det förflutna intervall som anges av MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, standard är falskt |Dynamisk| Inställning som avgör om överordnade repliker kan flyttas för att korrigera tillhörighets begränsningar.|
|NodeTaggingEnabled | Bool, standard är falskt |Dynamisk| Om sant; Funktionen NodeTagging kommer att aktive ras. |
|NodeTaggingConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Konfigurerbar prioritet för Node-taggning. |
|PartiallyPlaceServices | Bool, standard är sant |Dynamisk| Anger om alla tjänst repliker i klustret ska placeras "alla eller inga" begränsade lämpliga noder för dem.|
|PlaceChildWithoutParent | Bool, standard är sant | Dynamisk|Inställning som anger om den underordnade tjänstens replik kan placeras om ingen överordnad replik är aktiv. |
|PlacementConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioritet för placerings begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|PlacementConstraintValidationCacheSize | Int, standard är 10000 |Dynamisk| Begränsar storleken på den tabell som används för snabb verifiering och cachelagring av placerings begränsnings uttryck. |
|PlacementSearchTimeout | Tid i sekunder, standard är 0,5 |Dynamisk| Ange TimeSpan i sekunder. När du placerar tjänster, Sök efter den här långa tiden innan resultatet returneras. |
|PLBRefreshGap | Tid i sekunder, standard är 1 |Dynamisk| Ange TimeSpan i sekunder. Definierar den minsta tids period som måste passera innan PLB uppdaterar status igen. |
|PreferredLocationConstraintPriority | Int, standard är 2| Dynamisk|Anger prioritet för prioriterad plats begränsning: 0: hårt; 1: mjuk; 2: optimering; negativt: ignorera |
|PreferredPrimaryDomainsConstraintPriority| Int, standard är 1 | Dynamisk| Anger prioriteten för den prioriterade primära domän begränsningen: 0: hårt; 1: mjuk; negativt: ignorera |
|PreferUpgradedUDs|bool, standard är falskt|Dynamisk|Aktiverar och inaktiverar logik som föredrar att flytta till redan uppgraderad UDs. Från och med SF 7,0 ändras standardvärdet för den här parametern från TRUE till FALSe.|
|PreventTransientOvercommit | Bool, standard är falskt | Dynamisk|Fastställer antalet PLB som ska frigöras av de initierade flyttarna. Som standard, PLB kan initiera flytta ut och flytta in på samma nod som kan skapa övergående övergenomförande. Om du anger den här parametern till True förhindras de här typerna av överbelastningar och defrag på begäran (aka placementWithMove) inaktive ras. |
|ScaleoutCountConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger begränsningen för antal skalnings begränsningar: 0: hårt; 1: mjuk; negativt: ignorera. |
|SubclusteringEnabled|Bool, standard är falskt | Dynamisk |Bekräfta under kluster vid beräkning av standard avvikelse för balansering |
|SubclusteringReportingPolicy| Int, standard är 1 |Dynamisk|Definierar hur och om under klustrens hälso rapporter skickas: 0: rapportera inte; 1: varning; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | sträng, standard är ""|Statisk| Det associerade mått namnet för den här begränsningen. |
|SwapPrimaryThrottlingEnabled | Bool, standard är falskt|Dynamisk| Ta reda på om den primära begränsningen är aktive rad. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk| Det maximala antalet växlings bara primära repliker som tillåts globalt. |
|TraceCRMReasons |Bool, standard är sant |Dynamisk|Anger om du vill spåra orsaker för CRM-utfärdade transporter till kanalen för drift händelser. |
|UpgradeDomainConstraintPriority | Int, standard är 1| Dynamisk|Fastställer prioriteten för uppgraderings domän begränsning: 0: hårt; 1: mjuk; negativt: ignorera. |
|UseMoveCostReports | Bool, standard är falskt | Dynamisk|Instruerar LB att ignorera Cost-elementet i poängsättnings funktionen. resulterar i ett potentiellt stort antal flyttningar för bättre balanserade placering. |
|UseSeparateSecondaryLoad | Bool, standard är sant | Dynamisk|Inställning som anger om separat belastning ska användas för sekundära repliker. |
|UseSeparateSecondaryMoveCost | Bool, standard är sant | Dynamisk|Inställning som anger om PLB ska använda olika flytt kostnader för sekundär på varje nod. Om UseSeparateSecondaryMoveCost är inaktive rad:-rapporterad flytt kostnad för sekundär på en nod leder till overwritting flytt kostnad för varje sekundär (på alla andra noder) om UseSeparateSecondaryMoveCost är aktive rad:-rapporterad flytt kostnad för sekundär på en nod börjar gälla endast på den sekundära (ingen påverkan på sekundära noder på andra noder) – om replik krascher uppstår – nya repliker skapas med standard flytt kostnad som anges på service nivå – om PLB flyttar befintliga replikerings-flytt kostnader. |
|ValidatePlacementConstraint | Bool, standard är sant |Dynamisk| Anger om PlacementConstraint-uttrycket för en tjänst verifieras när en tjänsts ServiceDescription uppdateras. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, standard är sant |Dynamisk|Anger om PlacementConstraint-uttrycket för en tjänst utvärderas för primär preferens på redundansväxling. |
|VerboseHealthReportLimit | Int, standard är 20 | Dynamisk|Definierar antalet gånger som en replik måste placeras innan en hälso varning rapporteras för den (om utförlig hälso rapportering har Aktiver ATS). |
|NodeLoadsOperationalTracingEnabled | Bool, standard är sant |Dynamisk|Config som aktiverar strukturell spårning av operationella noder i händelse lagret. |
|NodeLoadsOperationalTracingInterval | TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (20) | Dynamisk|Ange TimeSpan i sekunder. Intervallet som noden läses in till i händelse arkivet för varje tjänst domän. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk|Ange TimeSpan i sekunder. Den tid som systemet väntar innan tjänst värdar som har repliker som fastnar i slutet av program uppgraderingen avbryts.|
|FabricUpgradeMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk| Ange TimeSpan i sekunder. Hur länge systemet ska vänta innan tjänst värdar som har repliker som fastnar i slutet under uppgraderingen av infrastrukturen avslutas. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (120)|Dynamisk|Ange TimeSpan i sekunder. Den tid som systemet väntar innan tjänst värdar som har repliker som fastnar i slutet avbryts. Om det här värdet är inställt på 0 uppmanas repliker inte att stängas.|
|NodeDeactivationMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk|Ange TimeSpan i sekunder. Den varaktighet som systemet väntar på innan tjänst värdar som har repliker som fastnar i slutet vid nod-inaktive ras avbryts. |
|PeriodicApiSlowTraceInterval | Tid i sekunder, standard är 5 minuter |Dynamisk| Ange TimeSpan i sekunder. PeriodicApiSlowTraceInterval definierar intervallet över vilka långsamma API-anrop kommer att spåras av API-övervakaren. |
|ReplicaChangeRoleFailureRestartThreshold|int, standard är 10|Dynamisk| Talet. Ange antalet API-avbrott under primär befordran efter vilken åtgärd för automatisk minskning (replik omstart) ska tillämpas. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, standard är 2147483647|Dynamisk| Talet. Ange antalet API-fel under primär befordran efter vilken varnings hälso rapport som ska aktive ras.|
|ServiceApiHealthDuration | Tid i sekunder, standardvärdet är 30 minuter |Dynamisk| Ange TimeSpan i sekunder. ServiceApiHealthDuration definierar hur lång tid det tar för ett tjänst-API att köras innan vi rapporterar att den inte är felfri. |
|ServiceReconfigurationApiHealthDuration | Tid i sekunder, standard är 30 |Dynamisk| Ange TimeSpan i sekunder. ServiceReconfigurationApiHealthDuration definierar hur länge vi väntar på att ett tjänst-API ska köras innan vi rapporterar fel. Detta gäller för API-anrop som påverkar tillgänglighet.|

## <a name="replication"></a>Replikering
| **Parameter** | **Tillåtna värden** | **Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromMilliseconds (15)|Statisk|Ange TimeSpan i sekunder. Anger hur lång tid som replikeraren väntar efter att ha tagit emot en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som tas emot under den här tids perioden kommer att få sina bekräftelser tillbaka i ett enda meddelande – > minskning av nätverks trafiken, men eventuellt minska data flödet hos replikeraren.|
|MaxCopyQueueSize|uint, standard är 1024|Statisk|Detta är det maximala värdet definierar den inledande storleken för kön som upprätthåller replikeringen. Observera att det måste vara en potens av 2. Om kön växer till den här storleks åtgärden begränsas den mellan de primära och sekundära replikerarna.|
|MaxPrimaryReplicationQueueMemorySize|uint, standardvärdet är 0|Statisk|Detta är det maximala värdet för den primära replikeringsprovidern i byte.|
|MaxPrimaryReplicationQueueSize|uint, standard är 1024|Statisk|Detta är det maximala antalet åtgärder som kan finnas i den primära replikeringsrelationen. Observera att det måste vara en potens av 2.|
|MaxReplicationMessageSize|uint, standard är 52428800|Statisk|Maximal meddelande storlek för replikering. Standardvärdet är 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, standardvärdet är 0|Statisk|Detta är det maximala värdet för den sekundära replikeringstjänsten i byte.|
|MaxSecondaryReplicationQueueSize|uint, standard är 2048|Statisk|Detta är det maximala antalet åtgärder som kan finnas i den sekundära replikeringsrelationen. Observera att det måste vara en potens av 2.|
|QueueHealthMonitoringInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (30)|Statisk|Ange TimeSpan i sekunder. Det här värdet avgör den tids period som används av replikeraren för att övervaka varnings-/fel hälso händelser i köer för replikering. Värdet 0 inaktiverar hälso övervakning |
|QueueHealthWarningAtUsagePercent|uint, standard är 80|Statisk|Det här värdet bestämmer hur replikerings kön används (i procent) när vi rapporterar varning om hög kös användning. Vi gör det efter ett Grace-intervall på QueueHealthMonitoringInterval. Om kösökvägen är under den här procent andelen i Grace-intervallet|
|ReplicatorAddress|sträng, standard är "localhost: 0"|Statisk|Slut punkten i form av en sträng-IP: port som används av Windows Fabric Replicator för att upprätta anslutningar till andra repliker för att kunna skicka/ta emot-åtgärder.|
|ReplicatorListenAddress|sträng, standard är "localhost: 0"|Statisk|Slut punkten i form av en sträng-IP: port som används av Windows Fabric Replicator för att ta emot åtgärder från andra repliker.|
|ReplicatorPublishAddress|sträng, standard är "localhost: 0"|Statisk|Slut punkten i form av en sträng-IP: port som används av Windows Fabric Replicator för att skicka åtgärder till andra repliker.|
|RetryInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (5)|Statisk|Ange TimeSpan i sekunder. När en åtgärd bryts eller avvisas, bestämmer den här timern hur ofta replikeringen ska försöka skicka åtgärden igen.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parameter** | **Tillåtna värden** | **Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är falskt |Statisk|Kontrollerar om tjänsten är aktive rad i klustret eller inte. |

## <a name="runas"></a>RunAs

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standard är "" |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för konto typen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän \ användare" eller " user@domain ". |
|RunAsAccountType|sträng, standard är "" |Dynamisk|Anger RunAs-kontots typ. Detta krävs för alla giltiga RunAs-avsnitt som giltiga värden är "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|sträng, standard är "" |Dynamisk|Anger RunAs-kontots lösen ord. Detta krävs endast för konto typen "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standard är "" |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för konto typen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän \ användare" eller " user@domain ". |
|RunAsAccountType|sträng, standard är "" |Dynamisk|Anger RunAs-kontots typ. Detta krävs för alla giltiga RunAs-avsnitt som giltiga värden är "lokal användare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|sträng, standard är "" |Dynamisk|Anger RunAs-kontots lösen ord. Detta krävs endast för konto typen "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standard är "" |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för konto typen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän \ användare" eller " user@domain ". |
|RunAsAccountType|sträng, standard är "" |Dynamisk|Anger RunAs-kontots typ. Detta krävs för alla giltiga RunAs-avsnitt som giltiga värden är "lokal användare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|sträng, standard är "" |Dynamisk|Anger RunAs-kontots lösen ord. Detta krävs endast för konto typen "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standard är "" |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för konto typen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän \ användare" eller " user@domain ". |
|RunAsAccountType|sträng, standard är "" |Dynamisk|Anger RunAs-kontots typ. Detta krävs för alla giltiga RunAs-avsnitt som giltiga värden är "lokal användare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|sträng, standard är "" |Dynamisk|Anger RunAs-kontots lösen ord. Detta krävs endast för konto typen "DomainUser". |

## <a name="security"></a>Säkerhet
| **Parameter** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|sträng, standard är ""|Statisk|Slut punkts format för AAD-certifikat, standard Azure-kommersiell, anges för miljö som inte är standard, till exempel Azure Government https: \/ /login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml |
|AADClientApplication|sträng, standard är ""|Statisk|Ursprungligt klient program namn eller ID som representerar Fabric-klienter |
|AADClusterApplication|sträng, standard är ""|Statisk|Webb-API-programnamn eller ID som representerar klustret |
|AADLoginEndpoint|sträng, standard är ""|Statisk|Inloggnings slut punkt för AAD, standard Azure-kommersiell, anges för miljö som inte är standard, till exempel Azure Government https: \/ /login.microsoftonline.us " |
|AADTenantId|sträng, standard är ""|Statisk|Klient-ID (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, standard är falskt|Dynamisk|Flagga som anger om utgångna kluster certifikat som deklarerats av tumavtryck endast ska accepteras för kluster certifikat; för att hålla klustret Alive. |
|AdminClientCertThumbprints|sträng, standard är ""|Dynamisk|Tumavtrycken av certifikat som används av klienter i administratörs rollen. Det är en kommaavgränsad lista med namn. |
|AADTokenEndpointFormat|sträng, standard är ""|Statisk|AAD-token-slutpunkt, standard Azure-kommersiell, anges för miljö som inte är standard, till exempel Azure Government https: \/ /login.microsoftonline.us/ {0} " |
|AdminClientClaims|sträng, standard är ""|Dynamisk|Alla möjliga anspråk som förväntas från administratörs klienter; samma format som ClientClaims; den här listan läggs internt till i ClientClaims. du behöver inte heller lägga till samma poster i ClientClaims. |
|AdminClientIdentities|sträng, standard är ""|Dynamisk|Windows-identiteter för Fabric-klienter i administratörs rollen; används för att auktorisera privilegierade infrastruktur åtgärder. Det är en kommaavgränsad lista. varje post är ett domän konto namn eller grupp namn. För enkelhetens skull. kontot som kör fabric.exe tilldelas automatiskt administratörs rollen. så är grupp ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|sträng, standard är/Home/sfuser/sfusercerts |Statisk|Mapp där AppRunAsAccountGroup X509-certifikat och privata nycklar finns |
|CertificateExpirySafetyMargin|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (43200)|Statisk|Ange TimeSpan i sekunder. Säkerhets marginal för certifikatets förfallo datum; status för certifikat hälso rapporten ändras från OK till varning när förfallo datumet är närmare. Standardvärdet är 30 dagar. |
|CertificateHealthReportingInterval|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (3600 * 8)|Statisk|Ange TimeSpan i sekunder. Ange intervall för certifikat hälso rapportering; Standardvärdet 8 timmar; Om du anger 0 inaktive ras hälso rapportering för certifikat |
|ClientCertThumbprints|sträng, standard är ""|Dynamisk|Tumavtrycken av certifikat som används av klienter för att kommunicera med klustret. klustret använder den här auktoriserade inkommande anslutningen. Det är en kommaavgränsad lista med namn. |
|ClientClaimAuthEnabled|bool, standard är falskt|Statisk|Anger om anspråksbaserad autentisering är aktiverat på klienter. inställningen true anger ClientRoleEnabled. |
|ClientClaims|sträng, standard är ""|Dynamisk|Alla möjliga anspråk som förväntas från klienter för anslutning till gateway. Detta är en "OR"-lista: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... varje ClaimsEntry är en "och"-lista: ClaimType = ClaimValue && ClaimType = ClaimValue && ClaimType = ClaimValue... |
|ClientIdentities|sträng, standard är ""|Dynamisk|Windows-identiteter för FabricClient; namngivning av Gateway använder detta för att auktorisera inkommande anslutningar. Det är en kommaavgränsad lista. varje post är ett domän konto namn eller grupp namn. För enkelhetens skull. det konto som kör fabric.exe tillåts automatiskt. Därför är Group ServiceFabricAllowedUsers och ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, standard är falskt|Statisk|Anger om klient rollen är aktive rad; När värdet är true; klienter tilldelas roller baserat på deras identiteter. För v2; Om du aktiverar detta innebär det att klienten inte finns i AdminClientCommonNames/AdminClientIdentities bara kan köra skrivskyddade åtgärder. |
|ClusterCertThumbprints|sträng, standard är ""|Dynamisk|Tumavtrycken för certifikat som får ansluta till klustret. en kommaavgränsad lista med namn. |
|ClusterCredentialType|sträng, standard är "ingen"|Tillåts inte|Anger vilken typ av säkerhets uppgifter som ska användas för att skydda klustret. Giltiga värden är "none/X509/Windows" |
|ClusterIdentities|sträng, standard är ""|Dynamisk|Windows-identiteter för klusternoder; används för auktorisering av kluster medlemskap. Det är en kommaavgränsad lista. varje post är ett domän konto namn eller grupp namn |
|ClusterSpn|sträng, standard är ""|Tillåts inte|Tjänstens huvud namn för klustret. När infrastrukturen körs som en enskild domän användare (gMSA/domän användar konto). Det är SPN för låne lyssnare och lyssnare i fabric.exe: Federations lyssnare; interna lyssnare för replikering; lyssnare för körnings tjänstens lyssnare och namngivning av gateway. Detta bör lämnas tomt när infrastrukturen körs som dator konton. i vilket fall ansluter sid computes lyssnar-SPN från lyssnar transport adress. |
|CrlCheckingFlag|uint, standard är 0x40000000|Dynamisk|Standard validerings flagga för certifikat kedja; kan åsidosättas av en komponent-speciell flagga; t. ex. Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY inställningen 0 inaktiverar CRL-kontrollen. en fullständig lista över de värden som stöds dokumenteras av dwFlags för CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (15)|Dynamisk|Ange TimeSpan i sekunder. Hur länge CRL-kontroll inaktive ras för ett angivet certifikat efter att det har uppstått ett offline-fel. om CRL offline-fel kan ignoreras. |
|CrlOfflineHealthReportTtl|TimeSpan, standard är gemensamt:: TimeSpan:: FromMinutes (1440)|Dynamisk|Ange TimeSpan i sekunder. |
|DisableFirewallRuleForDomainProfile| bool, standard är sant |Statisk| Anger om brand Väggs regel inte ska aktive ras för domän profil |
|DisableFirewallRuleForPrivateProfile| bool, standard är sant |Statisk| Anger om brand Väggs regeln inte ska aktive ras för privat profil | 
|DisableFirewallRuleForPublicProfile| bool, standard är sant | Statisk|Anger om brand Väggs regeln inte ska aktive ras för offentlig profil |
| EnforceLinuxMinTlsVersion | bool, standard är falskt | Statisk | Om värdet är sant; endast TLS version 1.2 + stöds.  Om falskt; stöd för tidigare TLS-versioner. Gäller endast för Linux |
| EnforcePrevalidationOnSecurityChanges | bool, standard är falskt| Dynamisk | Flagga som styr beteendet för kluster uppgraderingen vid identifiering av ändringar av säkerhets inställningarna. Om det är inställt på "true" försöker kluster uppgraderingen se till att minst ett certifikat som matchar någon av presentations reglerna kan skicka en motsvarande verifierings regel. För valideringen utförs innan de nya inställningarna tillämpas på alla noder, men körs bara på noden som är värd för den primära repliken av kluster hanterarens tjänst vid tidpunkten för initieringen av uppgraderingen. Standardvärdet är "false". från och med version 7,1 anges inställningen "true" för nya Azure Service Fabric-kluster.|
|FabricHostSpn| sträng, standard är "" |Statisk| Tjänstens huvud namn för Fabrichost returnerar; När infrastrukturen körs som en enskild domän användare (gMSA/domän användar konto) och Fabrichost returnerar körs under dator konto. Det är SPN för IPC-lyssnare för Fabrichost returnerar; som standard ska lämnas tomt eftersom Fabrichost returnerar körs under dator konto |
|IgnoreCrlOfflineError|bool, standard är falskt|Dynamisk|Huruvida CRL offline-fel ska ignoreras när Server sidan verifierar inkommande klient certifikat |
|IgnoreSvrCrlOfflineError|bool, standard är sant|Dynamisk|Huruvida CRL offline-fel ska ignoreras när klient sidan verifierar inkommande server certifikat; Standardvärdet är true. Attacker med återkallade Server certifikat kräver kompromissa med DNS; svårare än med återkallade klient certifikat. |
|ServerAuthCredentialType|sträng, standard är "ingen"|Statisk|Anger vilken typ av säkerhets uppgifter som ska användas för att skydda kommunikationen mellan FabricClient och klustret. Giltiga värden är "none/X509/Windows" |
|ServerCertThumbprints|sträng, standard är ""|Dynamisk|Tumavtrycken för Server certifikat som används av klustret för att kommunicera med klienter. klienter använder detta för att autentisera klustret. Det är en kommaavgränsad lista med namn. |
|SettingsX509StoreName| sträng, standard är "MY"| Dynamisk|X509 certifikat arkiv som används av infrastruktur resurser för konfigurations skydd |
|UseClusterCertForIpcServerTlsSecurity|bool, standard är falskt|Statisk|Om du vill använda kluster certifikatet för att skydda IPC-serverns TLS-transport enhet |
|X509Folder|sträng, standard är/var/lib/waagent|Statisk|Mapp där X509-certifikat och privata nycklar finns |
|TLS1_2_CipherList| sträng| Statisk|Om det är inställt på en icke-tom sträng; åsidosätter listan över tillåtna chiffer för TLS 1.2 och nedan. Se dokumentationen "openssl-cipher" för att hämta listan över tillåtna chiffer och list formatet för en stark chiffer-lista för TLS 1.2: "ECDHE-ECDSA-AES256-GCM-SHA384: ECDHE-ECDSA-AES128-GCM-SHA256: ECDHE-RSA-AES256-GCM-SHA384: ECDHE-RSA-AES-128-GCM-SHA256: ECDHE-ECDSA-AES256-CBC-SHA384: ECDHE-ECDSA-AES128-CBC-SHA256: ECDHE-RSA-AES256-CBC-SHA384: ECDHE-RSA-AES128-CBC-SHA256" gäller endast Linux. |

## <a name="securityadminclientx509names"></a>Säkerhet/AdminClientX509Names

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk|Det här är en lista med paret "namn" och "värde". Varje "namn" är av certifikatets gemensamma namn eller DnsName för X509-certifikat som har behörighet för administratörs klient åtgärder. För ett angivet "namn" är "värde" en kommaavgränsad lista över certifikat tumavtrycken för utfärdande av utfärdare, om den inte är tom, måste den direkta utfärdaren av administratörs klient certifikat finnas i listan. |

## <a name="securityclientaccess"></a>Säkerhet/ClientAccess

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ActivateNode |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för aktivering av en nod. |
|CancelTestCommand |sträng, standardvärdet är "admin" |Dynamisk| Avbryter en speciell TestCommand – om den är i flygning. |
|CodePackageControl |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att starta om kod paket. |
|CreateApplication |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för att skapa program. |
|CreateComposeDeployment|sträng, standardvärdet är "admin"| Dynamisk|Skapar en Skriv distribution som beskrivs av skapa filer |
|CreateGatewayResource|sträng, standardvärdet är "admin"| Dynamisk|Skapa en gateway-resurs |
|CreateName |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för skapande av namngivnings-URI. |
|CreateNetwork|sträng, standardvärdet är "admin" |Dynamisk|Skapar ett behållar nätverk |
|CreateService |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för skapande av tjänst. |
|CreateServiceFromTemplate |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för skapande av tjänst från mall. |
|CreateVolume|sträng, standardvärdet är "admin"|Dynamisk|Skapar en volym |
|DeactivateNode |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att inaktivera en nod. |
|DeactivateNodesBatch |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att inaktivera flera noder. |
|Ta bort |sträng, standardvärdet är "admin" |Dynamisk| Säkerhetskonfigurationer för borttagnings åtgärd för avbildnings lagrings klient. |
|DeleteApplication |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för borttagning av program. |
|DeleteComposeDeployment|sträng, standardvärdet är "admin"| Dynamisk|Tar bort Skriv distributionen |
|DeleteGatewayResource|sträng, standardvärdet är "admin"| Dynamisk|Tar bort en gateway-resurs |
|DeleteName |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för borttagning av namngivnings-URI. |
|DeleteNetwork|sträng, standardvärdet är "admin" |Dynamisk|Tar bort ett behållar nätverk |
|DeleteService |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för borttagning av tjänst. |
|DeleteVolume|sträng, standardvärdet är "admin"|Dynamisk|Tar bort en volym.| 
|EnumerateProperties |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för uppräkning av namngivnings egenskaper. |
|EnumerateSubnames |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för namngivning av URI-uppräkning. |
|FileContent |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för klient fil överföring i avbildnings arkivet (externt till kluster). |
|FileDownload |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för avbildnings lagrings klient fil hämtnings initiering (extern till kluster). |
|FinishInfrastructureTask |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att slutföra infrastruktur uppgifter. |
|GetChaosReport | sträng är standard "administratörs \| \| användare" |Dynamisk| Hämtar status för kaos inom ett angivet tidsintervall. |
|GetClusterConfiguration | sträng är standard "administratörs \| \| användare" | Dynamisk|Inducerar GetClusterConfiguration på en partition. |
|GetClusterConfigurationUpgradeStatus | sträng är standard "administratörs \| \| användare" |Dynamisk| Inducerar GetClusterConfigurationUpgradeStatus på en partition. |
|GetFabricUpgradeStatus |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för avsökning av uppgraderings status för kluster. |
|GetFolderSize |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för FileStoreService-mappens hämtnings storlek |
|GetNodeDeactivationStatus |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att kontrol lera status för inaktive ring. |
|GetNodeTransitionProgress | sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för att få förloppet för en nod över gångs kommando. |
|GetPartitionDataLossProgress | sträng är standard "administratörs \| \| användare" | Dynamisk|Hämtar förloppet för ett anrop till API-anrop för anrop av data förlust. |
|GetPartitionQuorumLossProgress | sträng är standard "administratörs \| \| användare" |Dynamisk| Hämtar förloppet för ett anrop till API-anrop för Invoke-kvorum. |
|GetPartitionRestartProgress | sträng är standard "administratörs \| \| användare" |Dynamisk| Hämtar förloppet för ett API-anrop för Restart-partition. |
|GetSecrets|sträng, standardvärdet är "admin"|Dynamisk|Hämta hemliga värden |
|GetServiceDescription |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för tjänst meddelanden för lång avsökning och läsning av tjänst beskrivningar. |
|GetStagingLocation |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för klient lagrings plats för avbildnings klient. |
|GetStoreLocation |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för klient lagrings plats i avbildnings arkivet. |
|GetUpgradeOrchestrationServiceState|sträng, standardvärdet är "admin"| Dynamisk|Inducerar GetUpgradeOrchestrationServiceState på en partition |
|GetUpgradesPendingApproval |sträng, standardvärdet är "admin" |Dynamisk| Inducerar GetUpgradesPendingApproval på en partition. |
|GetUpgradeStatus |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för avsökning av program uppgraderings status. |
|InternalList |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för klient fil List åtgärd i avbildnings lager (intern). |
|InvokeContainerApi|sträng, standardvärdet är "admin"|Dynamisk|Anropa container-API |
|InvokeInfrastructureCommand |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för kommandon för hantering av infrastruktur uppgifter. |
|InvokeInfrastructureQuery |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för frågor om infrastruktur uppgifter. |
|Lista |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för klient fil List åtgärd i avbildnings lager. |
|MoveNextFabricUpgradeDomain |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återuppta kluster uppgraderingar med en explicit uppgraderings domän. |
|MoveNextUpgradeDomain |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återuppta program uppgraderingar med en explicit uppgraderings domän. |
|MoveReplicaControl |sträng, standardvärdet är "admin" | Dynamisk|Flytta repliken. |
|NameExists |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för att namnge URI-kontroller. |
|NodeControl |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för start; stoppas och starta om noderna. |
|NodeStateRemoved |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för status för rapporterings nod har tagits bort. |
|Pinga |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för klient-pingar. |
|PredeployPackageToNode |sträng, standardvärdet är "admin" |Dynamisk| API för för distribution. |
|PrefixResolveService |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för klagomåls-baserad tjänstens prefix matchning. |
|PropertyReadBatch |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för Läs åtgärder för namngivnings egenskapen. |
|PropertyWriteBatch |sträng, standardvärdet är "admin" |Dynamisk|Säkerhetskonfigurationer för namngivning av egenskaps Skriv åtgärder. |
|ProvisionApplicationType |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för etablering av program typ. |
|ProvisionFabric |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för MSI och/eller kluster manifest etablering. |
|Fråga |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för frågor. |
|RecoverPartition |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för återställning av en partition. |
|RecoverPartitions |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för återställning av partitioner. |
|RecoverServicePartitions |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för återställning av tjänst partitioner. |
|RecoverSystemPartitions |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för återställning av system tjänst partitioner. |
|RemoveNodeDeactivations |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för återställning av inaktive ring på flera noder. |
|ReportCompletion |wstring, standard är L "admin" |Dynamisk| Säkerhets konfiguration för slut för ande av rapportering. |
|ReportFabricUpgradeHealth |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återuppta kluster uppgraderingar med den aktuella uppgraderings processen. |
|ReportFault |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för rapporterings fel. |
|ReportHealth |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för rapporterings hälsa. |
|ReportUpgradeHealth |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återuppta program uppgraderingar med den aktuella uppgraderings processen. |
|ResetPartitionLoad |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för återställnings belastning för en failoverUnit. |
|ResolveNameOwner |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för matchning av namngivning av URI-ägare. |
|ResolvePartition |sträng är standard "administratörs \| \| användare" | Dynamisk|Säkerhets konfiguration för att lösa system tjänster. |
|ResolveService |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för klagomåls-baserad tjänst upplösning. |
|ResolveSystemService|sträng är standard "administratörs \| \| användare"|Dynamisk| Säkerhets konfiguration för att lösa system tjänster |
|RollbackApplicationUpgrade |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återställa program uppgraderingar. |
|RollbackFabricUpgrade |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att återställa kluster uppgraderingar. |
|ServiceNotifications |sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för händelsebaserade tjänst aviseringar. |
|SetUpgradeOrchestrationServiceState|sträng, standardvärdet är "admin"| Dynamisk|Inducerar SetUpgradeOrchestrationServiceState på en partition |
|StartApprovedUpgrades |sträng, standardvärdet är "admin" |Dynamisk| Inducerar StartApprovedUpgrades på en partition. |
|StartChaos |sträng, standardvärdet är "admin" |Dynamisk| Startar kaos – om det inte redan har startats. |
|StartClusterConfigurationUpgrade |sträng, standardvärdet är "admin" |Dynamisk| Inducerar StartClusterConfigurationUpgrade på en partition. |
|StartInfrastructureTask |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för start av infrastruktur uppgifter. |
|StartNodeTransition |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att starta en nod över gång. |
|StartPartitionDataLoss |sträng, standardvärdet är "admin" |Dynamisk| Inducerar data förlust på en partition. |
|StartPartitionQuorumLoss |sträng, standardvärdet är "admin" |Dynamisk| Inducerar kvorum förlust på en partition. |
|StartPartitionRestart |sträng, standardvärdet är "admin" |Dynamisk| Startar om några eller alla repliker av en partition. |
|StopChaos |sträng, standardvärdet är "admin" |Dynamisk| Stoppar kaos – om det har startats. |
|ToggleVerboseServicePlacementHealthReporting | sträng är standard "administratörs \| \| användare" |Dynamisk| Säkerhets konfiguration för att växla utförlig ServicePlacement-HealthReporting. |
|UnprovisionApplicationType |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för avetablering av program typ. |
|UnprovisionFabric |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för MSI och/eller kluster manifest avetableras. |
|UnreliableTransportControl |sträng, standardvärdet är "admin" |Dynamisk| Otillförlitlig transport för att lägga till och ta bort beteenden. |
|UpdateService |sträng, standardvärdet är "admin" |Dynamisk|Säkerhets konfiguration för tjänst uppdateringar. |
|UpgradeApplication |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att starta eller avbryta program uppgraderingar. |
|UpgradeComposeDeployment|sträng, standardvärdet är "admin"| Dynamisk|Uppgraderar Skriv distributionen |
|UpgradeFabric |sträng, standardvärdet är "admin" |Dynamisk| Säkerhets konfiguration för att starta kluster uppgraderingar. |
|Ladda upp |sträng, standardvärdet är "admin" | Dynamisk|Säkerhets konfiguration för klient överförings åtgärd i Image Store. |

## <a name="securityclientcertificateissuerstores"></a>Säkerhet/ClientCertificateIssuerStores

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdarens certifikat Arkiv för klient certifikat; Namn = clientIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclientx509names"></a>Säkerhet/ClientX509Names

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk|Det här är en lista med paret "namn" och "värde". Varje "namn" är av certifikatets gemensamma namn eller DnsName för X509-certifikat som har behörighet för klient åtgärder. För ett angivet "namn" är "värde" en kommaavgränsad lista över certifikat tumavtrycken för utfärdande av utfärdare, om den inte är tom, måste den direkta utfärdaren av klient certifikat finnas i listan.|

## <a name="securityclustercertificateissuerstores"></a>Säkerhet/ClusterCertificateIssuerStores

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdarens certifikat Arkiv för kluster certifikat; Namn = clusterIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclusterx509names"></a>Säkerhet/ClusterX509Names

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk|Det här är en lista med paret "namn" och "värde". Varje "namn" är av certifikatets gemensamma namn eller DnsName för X509-certifikat som har auktoriserats för kluster åtgärder. För ett angivet "namn" är "värde" en kommaavgränsad lista över certifikat tumavtrycken för utfärdande av utfärdare, om den inte är tom, måste den direkta utfärdaren av kluster certifikat finnas i listan.|

## <a name="securityservercertificateissuerstores"></a>Säkerhet/ServerCertificateIssuerStores

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdarens certifikat Arkiv för Server certifikat; Namn = serverIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityserverx509names"></a>Säkerhet/ServerX509Names

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk|Det här är en lista med paret "namn" och "värde". Varje "namn" är av certifikatets gemensamma namn eller DnsName för X509-certifikat som är auktoriserade för Server åtgärder. För ett angivet "namn" är "värde" en kommaavgränsad lista över certifikat tumavtrycken för utfärdande av utfärdare, om den inte är tom, måste den direkta utfärdaren av Server certifikat finnas i listan.|

## <a name="setup"></a>Konfiguration

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ContainerNetworkName|sträng, standard är ""| Statisk |Nätverks namnet som ska användas när ett behållar nätverk konfigureras.|
|ContainerNetworkSetup|bool, standard är falskt (Linux) och standard är sant (Windows)| Statisk |Om ett behållar nätverk ska konfigureras.|
|FabricDataRoot |Sträng | Tillåts inte |Service Fabric data rot Katalog. Standard för Azure är d:\svcfab |
|FabricLogRoot |Sträng | Tillåts inte |Rot Katalog för Service Fabric-logg. Det är här som SF-loggar och-spår placeras. |
|NodesToBeRemoved|sträng, standard är ""| Dynamisk |Noderna som ska tas bort som en del av konfigurations uppgraderingen. (Endast för fristående distributioner)|
|ServiceRunAsAccountName |Sträng | Tillåts inte |Det konto namn under vilket du vill köra infrastruktur värd tjänsten. |
|SkipContainerNetworkResetOnReboot|bool, standard är falskt|Talg|Om du vill hoppa över ominställning av behållarens nätverk vid omstart.|
|SkipFirewallConfiguration |Bool, standard är falskt | Tillåts inte |Anger om brand Väggs inställningar måste anges av systemet eller inte. Detta gäller endast om du använder Windows-brandväggen. Om du använder brand väggar från tredje part måste du öppna portarna för systemet och programmen som ska användas |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Leverantörer |sträng, standard är "DSTS" |Statisk|Kommaavgränsad lista över providers för token-verifiering som ska aktive ras (giltiga providrar är: DSTS; AAD). För närvarande kan endast en enda Provider aktive ras när som helst. |

## <a name="traceetw"></a>Spårning/ETW

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Nivå |Int, standard är 4 | Dynamisk |Spårnings-ETW-nivå kan ta värden 1, 2, 3, 4. För att stödjas måste du hålla spårnings nivån på 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tid i sekunder, standard är 0,015 | Statisk | Ange TimeSpan i sekunder. Anger hur lång tid som replikeraren väntar efter att ha tagit emot en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som tas emot under den här tids perioden kommer att få sina bekräftelser tillbaka i ett enda meddelande – > minskning av nätverks trafiken, men eventuellt minska data flödet hos replikeraren. |
|MaxCopyQueueSize |Uint, standard är 16384 | Statisk |Detta är det maximala värdet definierar den inledande storleken för kön som upprätthåller replikeringen. Observera att det måste vara en potens av 2. Om kön växer till den här storleks åtgärden begränsas den mellan de primära och sekundära replikerarna. |
|MaxPrimaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Detta är det maximala värdet för den primära replikeringsprovidern i byte. |
|MaxPrimaryReplicationQueueSize |Uint, standard är 8192 | Statisk |Detta är det maximala antalet åtgärder som kan finnas i den primära replikeringsrelationen. Observera att det måste vara en potens av 2. |
|MaxReplicationMessageSize |Uint, standard är 52428800 | Statisk | Maximal meddelande storlek för replikering. Standardvärdet är 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Detta är det maximala värdet för den sekundära replikeringstjänsten i byte. |
|MaxSecondaryReplicationQueueSize |Uint, standard är 16384 | Statisk |Detta är det maximala antalet åtgärder som kan finnas i den sekundära replikeringsrelationen. Observera att det måste vara en potens av 2. |
|ReplicatorAddress |sträng, standard är "localhost: 0" | Statisk | Slut punkten i form av en sträng-IP: port som används av Windows Fabric Replicator för att upprätta anslutningar till andra repliker för att kunna skicka/ta emot-åtgärder. |

## <a name="transport"></a>Transport
| **Parameter** | **Tillåtna värden** |**Uppgradera princip** |**Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60)|Statisk|Ange TimeSpan i sekunder. Tids gränsen för anslutnings installationen på både inkommande och mottagande sida (inklusive säkerhets förhandling i säkert läge) |
|FrameHeaderErrorCheckingEnabled|bool, standard är sant|Statisk|Standardinställning för fel kontroll i ram rubriken i icke-säkert läge. komponent inställningen åsidosätter detta. |
|MessageErrorCheckingEnabled|bool, standard är sant|Statisk|Standardinställning för fel kontroll av meddelande huvud och brödtext i icke-säkert läge. komponent inställningen åsidosätter detta. |
|ResolveOption|sträng, standard är "ospecificerad"|Statisk|Anger hur FQDN matchas.  Giltiga värden är "ospecificerade/IPv4/IPv6". |
|SendTimeout|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (300)|Dynamisk|Ange TimeSpan i sekunder. Sändnings tids gräns för att identifiera fastnad anslutning. TCP-felrapporter är inte tillförlitliga i vissa miljöer. Detta kan behöva justeras enligt tillgänglig nätverks bandbredd och storlek på utgående data ( \* MaxMessageSize \/ \* SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, standard är sant |Statisk| Automatisk avsökning och uppgraderings åtgärd baserat på en mål tillstånds fil. |
|AutoupgradeInstallEnabled|Bool, standard är falskt|Statisk|Automatisk avsökning, etablering och installation av kod uppgraderings åtgärd baserat på en mål tillstånds fil.|
|GoalStateExpirationReminderInDays|int, standard är 30|Statisk|Anger antalet återstående dagar efter vilken påminnelse om mål tillstånd ska visas.|
|MinReplicaSetSize |Int, standardvärdet är 0 |Statisk |MinReplicaSetSize för UpgradeOrchestrationService.|
|PlacementConstraints | sträng, standard är "" |Statisk| PlacementConstraints för UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk| Ange TimeSpan i sekunder. QuorumLossWaitDuration för UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tid i sekunder, standardvärdet är 60 minuter|Statisk| Ange TimeSpan i sekunder. ReplicaRestartWaitDuration för UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 60 *24* 7 minuter |Statisk| Ange TimeSpan i sekunder. StandByReplicaKeepDuration för UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk |TargetReplicaSetSize för UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, standard är falskt | Statisk|Inställningen för att göra kod uppgraderingen kräver administratörs godkännande innan du fortsätter. |

## <a name="upgradeservice"></a>UpgradeService

| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BaseUrl | sträng, standard är "" |Statisk|BaseUrl för UpgradeService. |
|ClusterId | sträng, standard är "" |Statisk|ClusterId för UpgradeService. |
|CoordinatorType | sträng, standard är "WUTest"|Tillåts inte|CoordinatorType för UpgradeService. |
|MinReplicaSetSize | Int, standard är 2 |Tillåts inte| MinReplicaSetSize för UpgradeService. |
|OnlyBaseUpgrade | Bool, standard är falskt |Dynamisk|OnlyBaseUpgrade för UpgradeService. |
|PlacementConstraints |sträng, standard är "" |Tillåts inte|PlacementConstraints för uppgraderings tjänsten. |
|PollIntervalInSeconds|TimeSpan, standard är gemensamt:: TimeSpan:: FromSeconds (60) |Dynamisk|Ange TimeSpan i sekunder. Intervallet mellan UpgradeService-avsökning för hanterings åtgärder för ARM. |
|TargetReplicaSetSize | Int, standard är 3 |Tillåts inte| TargetReplicaSetSize för UpgradeService. |
|TestCabFolder | sträng, standard är "" |Statisk| TestCabFolder för UpgradeService. |
|X509FindType | sträng, standard är ""|Dynamisk| X509FindType för UpgradeService. |
|X509FindValue | sträng, standard är "" |Dynamisk| X509FindValue för UpgradeService. |
|X509SecondaryFindValue | sträng, standard är "" |Dynamisk| X509SecondaryFindValue för UpgradeService. |
|X509StoreLocation | sträng, standard är "" |Dynamisk| X509StoreLocation för UpgradeService. |
|X509StoreName | sträng, standard är "My"|Dynamisk|X509StoreName för UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parameter** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, standard är ingen | Statisk | En samling med gränser för resurs styrning av användar tjänster måste vara statisk eftersom den påverkar logik för automatisk identifiering |

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Uppgradera konfigurationen av ett Azure-kluster](service-fabric-cluster-config-upgrade-azure.md) och [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).
