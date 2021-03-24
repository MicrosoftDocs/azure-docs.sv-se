---
title: Felsök vanliga problem med Azure Kubernetes-tjänsten
description: Lär dig hur du felsöker och löser vanliga problem när du använder Azure Kubernetes service (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 1d3dff19bd75bfa4e7564eb4b188ffe68d605025
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952039"
---
# <a name="aks-troubleshooting"></a>AKS-felsökning

När du skapar eller hanterar AKS-kluster (Azure Kubernetes service) kan du ibland stöta på problem. I den här artikeln beskrivs några vanliga problem och fel söknings steg.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet hittar jag information om fel sökning av Kubernetes-problem?

Testa den [officiella guiden för att felsöka Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [fel söknings guide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)som publicerats av en Microsoft-tekniker för att felsöka poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett `quota exceeded` fel meddelande när jag skapar eller uppgraderar. Vad ska jag göra? 

 [Begär flera kärnor](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett `insufficientSubnetSize` fel meddelande när jag distribuerar ett AKS-kluster med avancerade nätverksfunktioner. Vad ska jag göra?

Det här felet anger att ett undernät som används för ett kluster inte längre har tillgängliga IP-adresser i CIDR för lyckad resurs tilldelning. För Kubernetes-kluster är kravet tillräckligt med IP-utrymme för varje nod i klustret. För Azure CNI-kluster är kravet tillräckligt med IP-utrymme för varje nod och Pod i klustret.
Läs mer om [utformningen av Azure-cni för att tilldela IP-adresser till poddar](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Dessa fel finns också i AKS- [diagnostik](concepts-diagnostics.md), som proaktivt utsätter problem som en otillräcklig under näts storlek.

Följande tre (3) ärenden orsakar ett otillräckligt näts storleks fel:

1. Skala AKS Scale eller AKS Node pool
   1. Om du använder Kubernetes när `number of free IPs in the subnet` är **mindre än** `number of new nodes requested` .
   1. Om du använder Azure-CNI när `number of free IPs in the subnet` är **mindre än** `number of nodes requested times (*) the node pool's --max-pod value` .

1. Uppgradering av AKS-eller AKS Node-pool
   1. Om du använder Kubernetes när `number of free IPs in the subnet` är **mindre än** `number of buffer nodes needed to upgrade` .
   1. Om du använder Azure-CNI när `number of free IPs in the subnet` är **mindre än** `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Som standard anger AKS-kluster värdet för maximal överspänning (Upgrade buffer) för ett (1), men det här uppgraderings beteendet kan anpassas genom att ange [Max värdet för överspänning för en nod, vilket ökar antalet tillgängliga IP-adresser som krävs för att slutföra en uppgradering.

1. AKS Create eller AKS Node pool Add
   1. Om du använder Kubernetes när `number of free IPs in the subnet` är **mindre än** `number of nodes requested for the node pool` .
   1. Om du använder Azure-CNI när `number of free IPs in the subnet` är **mindre än** `number of nodes requested times (*) the node pool's --max-pod value` .

Följande åtgärder kan vidtas genom att skapa nya undernät. Behörighet att skapa ett nytt undernät krävs för att undvika åtgärder på grund av möjligheten att uppdatera ett befintligt undernäts CIDR-intervall.

1. Återskapa ett nytt undernät med ett större CIDR-intervall tillräckligt för åtgärds mål:
   1. Skapa ett nytt undernät med ett nytt intervall som inte överlappar.
   1. Skapa en ny Node-pool i det nya under nätet.
   1. Töm poddar från den gamla Node-poolen som finns i det gamla under nätet som ska ersättas.
   1. Ta bort det gamla under nätet och den gamla Node-poolen.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mitt Pod fastnar i CrashLoopBackOff-läge. Vad ska jag göra?

Det kan finnas olika orsaker till att Pod har fastnat i det läget. Du kan titta på:

* Själva pod, med hjälp av `kubectl describe pod <pod-name>` .
* Loggarna med hjälp av `kubectl logs <pod-name>` .

Mer information om hur du felsöker Pod-problem finns i [Felsöka program](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Jag får `TCP timeouts` när jag använder `kubectl` eller andra verktyg från tredje part som ansluter till API-servern
AKS har kontroll plan som skalas lodrätt i enlighet med antalet kärnor för att säkerställa dess service nivå mål (SLO: erna) och service nivå avtal (service avtal). Om du råkar ut för anslutnings tids gränsen kontrollerar du följande:

- **Är alla API-kommandon timeout-bara konsekventa eller bara några?** Om det bara är några få `tunnelfront` POD eller `aks-link` pod, som ansvarar för kommunikation mellan noder i > kontroll planet, kanske inte är i körnings tillstånd. Se till att noderna som är värdar för denna Pod inte är överutnyttjade eller under stress. Överväg att flytta dem till en egen [ `system` Node-pool](use-system-pools.md).
- **Har du öppnat alla obligatoriska portar, FQDN-namn och IP-adresser som anges i [AKS-begränsningen av utgående trafikflöden](limit-egress-traffic.md)?** Annars går det inte att anropa flera kommandon.
- **Omfattas den aktuella IP-adressen av [API IP-auktoriserade intervall](api-server-authorized-ip-ranges.md)?** Om du använder den här funktionen och din IP-adress inte ingår i de intervall som dina samtal ska blockeras. 
- **Har du en klient eller ett program som läcker anrop till API-servern?** Se till att använda arm i stället för att få frekventa samtal och att program från tredje part inte läcker sådana samtal. Ett fel i Istio-mixern gör till exempel att en ny API Server Watch-anslutning skapas varje gång en hemlighet läses internt. Eftersom det här beteendet sker med jämna mellanrum kan du titta på anslutningar snabbt och på så vis få API-servern att bli överbelastad oavsett skalnings mönstret. https://github.com/istio/istio/issues/19481
- **Har du många versioner av dina Helm-distributioner?** Det här scenariot kan orsaka att båda till att använda för mycket minne på noderna, samt en stor mängd `configmaps` , vilket kan orsaka onödiga toppar på API-servern. Överväg att konfigurera `--history-max` på `helm init` och utnyttja den nya Helm 3. Mer information om följande problem: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Är intern trafik mellan noder blockerade?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Jag får `TCP timeouts` , till exempel `dial tcp <Node_IP>:10250: i/o timeout`

Dessa tids gränser kan vara relaterade till intern trafik mellan noder som blockeras. Kontrol lera att trafiken inte blockeras, till exempel av [nätverks säkerhets grupper](concepts-security.md#azure-network-security-groups) på under nätet för klustrets noder.

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) i ett befintligt kluster. Hur kan jag göra det?

Att aktivera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) i befintliga kluster stöds inte för tillfället, det måste anges när du skapar nya kluster. Kubernetes RBAC är aktiverat som standard när du använder CLI, Portal eller en API-version senare än `2020-03-01` .

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggar med kubectl-loggar eller så kan jag inte ansluta till API-servern. Jag får "fel från servern: fel vid uppringning av Server del: slå TCP...". Vad ska jag göra?

Se till att portarna 22, 9000 och 1194 är öppna för att ansluta till API-servern. Kontrol lera om `tunnelfront` eller `aks-link` Pod körs i *Kube-systemets* namnrymd med `kubectl get pods --namespace kube-system` kommandot. Om den inte är det, kan du framtvinga borttagning av Pod och startas om.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Jag får `"tls: client offered only unsupported versions"` från min klient när jag ansluter till AKS-API: et. Vad ska jag göra?

Den lägsta TLS-version som stöds i AKS är TLS 1,2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och får ett `"Changing property 'imageReference' is not allowed"` fel meddelande. Hur gör jag för att åtgärda det här problemet?

Du kan få det här felet eftersom du har ändrat taggarna i agent-noderna i AKS-klustret. Ändra eller ta bort taggar och andra egenskaper för resurser i resurs gruppen MC_ * kan leda till oväntade resultat. Att ändra resurserna under MC_ *-gruppen i AKS-klustret delar service nivå målet (service nivå mål).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Jag får fel meddelanden om att mitt kluster är i felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-cluster-failed*

Felet uppstår när kluster anger ett felaktigt tillstånd av flera orsaker. Följ stegen nedan för att lösa ett tillstånd för misslyckad kluster innan du försöker igen den tidigare misslyckade åtgärden:

1. Tills klustret är i ett tillstånd där det inte går att utföra `failed` `upgrade` `scale` åtgärder. Vanliga problem och lösningar för roten är:
    * Skalning med **otillräcklig beräknings kvot (CRP)**. För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan de här [stegen för att begära en ökad beräknings kvot](../azure-portal/supportability/resource-manager-core-quotas-request.md) innan du försöker skala upp igen utöver de inledande kvot gränserna.
    * Skala ett kluster med avancerade nätverk och **otillräckliga undernät (nätverks resurser)**. För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan [de här stegen för att begära en resurs kvot ökning](../azure-resource-manager/templates/error-resource-quota.md#solution) innan du försöker skala upp igen utöver de inledande kvot gränserna.
2. När den underliggande orsaken till uppgraderings felet har lösts bör klustret ha statusen klar. När en lyckad status har verifierats kan du försöka utföra den ursprungliga åtgärden igen.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Jag får fel meddelanden när jag försöker uppgradera eller skala det tillstånd mitt kluster uppgraderas eller har inte uppgraderats

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-pending-upgrade*

 Det går inte att ha ett kluster eller en Node-pool samtidigt för uppgradering och skalning. I stället måste varje åtgärds typ slutföras på mål resursen före nästa förfrågan på samma resurs. Det innebär att åtgärder begränsas när aktiva uppgraderingar eller skalnings åtgärder inträffar eller görs. 

För att diagnostisera problemet kan `az aks show -g myResourceGroup -n myAKSCluster -o table` du Hämta detaljerad status för klustret. Baserat på resultatet:

* Om klustret aktivt uppgraderas väntar du tills åtgärden har slutförts. Om det lyckades, gör om den tidigare misslyckade åtgärden igen.
* Om det inte går att uppgradera klustret följer du stegen som beskrivs i föregående avsnitt.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan jag flytta mitt kluster till en annan prenumeration eller min prenumeration med mitt kluster till en ny klient?

Om du har flyttat ditt AKS-kluster till en annan prenumeration eller klustrets prenumeration till en ny klient fungerar inte klustret på grund av saknade behörigheter för kluster identiteten. **AKS stöder inte flytt av kluster mellan prenumerationer eller klienter** på grund av den här begränsningen.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Jag får fel meddelanden vid försök att använda funktioner som kräver skalnings uppsättningar för virtuella datorer

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-vmss-enablement*

Du får fel meddelanden som anger att ditt AKS-kluster inte finns på en skal uppsättning för virtuella datorer, till exempel följande exempel:

**Agentpoolegenskap `<agentpoolname>` har ställt in automatisk skalning som aktive rad men inte på Virtual Machine Scale Sets**

Funktioner som till exempel klustrets autoskalning eller flera noder kräver skalnings uppsättningar för virtuella datorer som `vm-set-type` .

Följ stegen *innan du börjar* i rätt dokument för att skapa ett AKS-kluster på rätt sätt:

* [Använd kluster autoskalning](cluster-autoscaler.md)
* [Skapa och Använd flera noder i pooler](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Vilka namngivnings begränsningar tillämpas för AKS-resurser och parametrar?

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-naming-rules*

Namngivnings begränsningar implementeras av både Azure-plattformen och AKS. Om ett resurs namn eller en parameter delar någon av dessa begränsningar returneras ett fel som uppmanar dig att ange en annan Indatatyp. Följande rikt linjer gäller för namngivning:

* Kluster namn måste innehålla 1-63 tecken. De enda tillåtna tecknen är bokstäver, siffror, bindestreck och under streck. Det första och sista tecknet måste vara en bokstav eller en siffra.
* AKS nod/*MC_* resurs grupp namn kombinerar resurs grupps namn och resurs namn. Den automatiskt genererade syntaxen för `MC_resourceGroupName_resourceName_AzureRegion` får inte vara större än 80 tecken. Om det behövs kan du minska längden på resurs gruppens namn eller AKS kluster namn. Du kan också [Anpassa resurs grupps namnet för noden](cluster-configuration.md#custom-resource-group-name)
* *DnsPrefix* måste börja och sluta med alfanumeriska värden och måste vara mellan 1-54 tecken. Giltiga tecken är alfanumeriska värden och bindestreck (-). *DnsPrefix* får inte innehålla specialtecken, till exempel en punkt (.).
* AKS måste bestå av gemener och 1-11 tecken för Linux-nodkonfigurationer och 1-6-tecken för Windows-nodkonfigurationer. Namnet måste börja med en bokstav och de enda tillåtna tecknen är bokstäver och siffror.
* *Admin-username*, som anger administratörs användar namnet för Linux-noder, måste börja med en bokstav, får bara innehålla bokstäver, siffror, bindestreck och under streck och har högst 64 tecken.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Jag får fel meddelanden när jag försöker skapa, uppdatera, skala, ta bort eller uppgradera kluster, den åtgärden är inte tillåten eftersom en annan åtgärd pågår.

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-pending-operation*

Kluster åtgärder är begränsade när en tidigare åtgärd fortfarande pågår. Om du vill hämta en detaljerad status för klustret använder du `az aks show -g myResourceGroup -n myAKSCluster -o table` kommandot. Använd din egen resurs grupp och AKS kluster namn efter behov.

Baserat på utdata från klustrets status:

* Om klustret är i ett annat etablerings tillstånd än *lyckat* eller *misslyckat* väntar du tills åtgärden (*Uppgradera/uppdatera/skapa/skala/ta bort/migrera*) har slutförts. Försök med din senaste kluster åtgärd när den tidigare åtgärden har slutförts.

* Om det finns en misslyckad uppgradering av klustret följer du stegen som beskrivs [i avsnittet Jag får fel meddelanden om att mitt kluster är i ett felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Tog emot ett fel som säger att mitt huvud namn inte hittades eller är ogiltigt när jag försöker skapa ett nytt kluster.

När du skapar ett AKS-kluster kräver det ett huvud namn för tjänsten eller en hanterad identitet för att skapa resurser för din räkning. AKS kan automatiskt skapa ett nytt huvud namn för tjänsten när klustret skapas eller ta emot ett befintligt. När du använder en automatiskt skapad måste Azure Active Directory sprida den till varje region så att skapandet lyckas. Om spridningen tar för lång tid kommer klustret inte att verifieras för att skapa eftersom det inte går att hitta ett tillgängligt huvud namn för tjänsten. 

Använd följande lösningar för det här problemet:
* Använd ett befintligt huvud namn för tjänsten som redan har spridits över regioner och som finns för att skicka in till AKS vid klustrets skapande tid.
* Om du använder Automation-skript kan du lägga till tids fördröjningar mellan skapande av tjänstens huvud namn och AKS-kluster.
* Om du använder Azure Portal återgår du till kluster inställningarna när du skapar och försöker sedan att köra verifierings sidan igen efter några minuter.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Jag får `"AADSTS7000215: Invalid client secret is provided."` när jag använder AKS-API. Vad ska jag göra?

Det här problemet beror på att autentiseringsuppgifterna för tjänstens huvud namn har gått ut. [Uppdatera autentiseringsuppgifterna för ett AKS-kluster.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Jag kan inte komma åt mitt kluster-API från min Automation/dev Machine/verktygs uppsättning när du använder tillåtna IP-intervall för API-servern. Hur gör jag för att åtgärda det här problemet?

För att lösa det här problemet, se till att `--api-server-authorized-ip-ranges` inkludera de IP-adresser eller IP-intervall för Automation/dev/verktygs system som används. Se avsnittet "så här hittar du min IP" i [säker åtkomst till API-servern med hjälp av behöriga IP-adressintervall](api-server-authorized-ip-ranges.md).

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Jag kan inte Visa resurser i Kubernetes Resource Viewer i Azure Portal för mitt kluster konfigurerat med tillåtna IP-intervall för API-servern. Hur gör jag för att åtgärda det här problemet?

[Kubernetes Resource Viewer](kubernetes-portal.md) måste `--api-server-authorized-ip-ranges` innehålla åtkomst till den lokala klient datorn eller IP-adressintervallet (från vilken portalen bläddras). Se avsnittet "så här hittar du min IP" i [säker åtkomst till API-servern med hjälp av behöriga IP-adressintervall](api-server-authorized-ip-ranges.md).

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Jag får fel efter att ha begränsat utgående trafik

Vid begränsning av utgående trafik från ett AKS-kluster [krävs och valfria rekommenderade](limit-egress-traffic.md) utgående portar/nätverks regler och FQDN/applikations regler för AKS. Om inställningarna är i konflikt med någon av dessa regler `kubectl` fungerar inte vissa kommandon som de ska. Du kan också se fel när du skapar ett AKS-kluster.

Kontrol lera att inställningarna inte står i konflikt med några av de obligatoriska eller valfria rekommenderade utgående portarna/nätverks reglerna och reglerna för FQDN/program.

## <a name="im-receiving-429---too-many-requests-errors"></a>Jag får fel meddelanden om "429-för många begär Anden"

När ett Kubernetes-kluster på Azure (AKS eller No) ofta skalar upp/ned eller använder kluster autoskalning (CA), kan dessa åtgärder resultera i ett stort antal HTTP-anrop som i sin tur överskrider den tilldelade prenumerations kvoten som leder till fel. Felen kommer att se ut som

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Dessa begränsnings fel beskrivs i detalj [här](../azure-resource-manager/management/request-limits-and-throttling.md) och [här](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors)

Rekommendationen från AKS Engineering team är att se till att du kör version minst 1.18. x, som innehåller många förbättringar. Mer information finns i dessa förbättringar [här](https://github.com/Azure/AKS/issues/1413) [.](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)

Med tanke på att dessa begränsnings fel mäts på prenumerations nivå kan de fortfarande inträffa om:
- Det finns program från tredje part som gör GET-begäranden (till exempel övervakning av program och så vidare). Rekommendationen är att minska frekvensen för dessa anrop.
- Det finns flera AKS-kluster/Node-pooler som använder virtuella datorers skalnings uppsättningar. Försök att dela antalet kluster i olika prenumerationer, i synnerhet om du förväntar dig att de ska vara mycket aktiva (till exempel en aktiv kluster autoskalning) eller har flera klienter (till exempel rancher, terraform och så vidare).

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>Mitt klusters etablerings status har ändrats från klar till misslyckades med eller utan att jag utför en åtgärd. Vad ska jag göra?

Om klustrets etablerings status ändras från *klar* till *misslyckad* eller utan att du utför några åtgärder, men programmen i klustret fortsätter att köras, kan det här problemet lösas automatiskt av tjänsten och dina program påverkas inte.

[Skicka en supportbegäran](https://azure.microsoft.com/support/options/#submit)om klustrets etablerings status är kvar som *misslyckad* eller om programmen i klustret slutar fungera.

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>Min Watch är inaktuell eller Azure AD Pod Identity NMI returnerar status 500

Om du använder Azure-brandväggen som i det här [exemplet](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)kan du stöta på det här problemet eftersom de långsamma TCP-anslutningarna via brand vägg som använder program regler för närvarande har ett fel (som ska lösas i Q1CY21) som gör att go `keepalives` avslutas i brand väggen. Tills det här problemet har lösts kan du minska genom att lägga till en nätverks regel (i stället för program regel) till IP-AKS för API-servern.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage-och AKS-felsökning

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Det gick inte att ställa in UID och `GID` i mountOptions för Azure disk

Azure-disken använder ext4, xfs-filsystem som standard och mountOptions, till exempel UID = x, GID = x kan inte ställas in vid monterings tiden. Om du till exempel försökte ange mountOptions UID = 999, GID = 999, ser du ett fel som:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Du kan åtgärda problemet genom att göra något av alternativen:

* [Konfigurera säkerhets kontexten för en POD](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) genom att ange UID i runAsUser och GID i fsGroup. Följande inställning kommer till exempel att ange Pod kör som-rot, vilket gör den tillgänglig för alla filer:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Eftersom GID och UID monteras som rot eller 0 som standard. Om GID eller UID anges som icke-rot, till exempel 1000, används Kubernetes för `chown` att ändra alla kataloger och filer under den disken. Den här åtgärden kan ta lång tid och kan göra det mycket långsamt att montera disken.

* Använd `chown` i initContainers för att ange `GID` och `UID` . Exempel:

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Det gick inte att koppla från Azure-disken till ett potentiellt problem med konkurrens villkoret och ogiltig data disk lista

När en Azure-disk inte kan kopplas tillbaka, kommer den att försöka igen om till sex gånger för att koppla bort disken från exponentiellt. Det kommer också att innehålla ett lås på radnivå på data disk listan i ungefär 3 minuter. Om disk listan uppdateras manuellt under den tiden kommer den att orsaka att disk listan hålls kvar av låset på nodens nivå för att bli inaktuell och orsakar instabilitet på noden.

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
|--|:--:|
| 1.12 | 1.12.9 eller senare |
| 1.13 | 1.13.6 eller senare |
| 1,14 | 1.14.2 eller senare |
| 1,15 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och noden har en föråldrad disk lista kan du minska genom att koppla bort alla icke-befintliga diskar från den virtuella datorn som en Mass åtgärd. **En separat från koppling av icke-befintliga diskar kan Miss lyckas.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Ett stort antal Azure-diskar orsakar långsam anslutning/från koppling

När antalet åtgärder för att koppla/koppla från Azure-disk som är mål för en enskild nod är större än 10, eller större än 3 när du använder en pool för en enskild virtuell dators skalnings uppsättning, kan de vara långsammare än förväntat när de görs i tur och ordning. Det här problemet är en känd begränsning och det finns inga lösningar för tillfället. [Röst objekt för användare som stöder parallell anslutning/från koppling utöver nummer.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)..

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Det gick inte att koppla från Azure-disken till en möjlig nods VM i felaktigt tillstånd

I vissa fall kan en Azure disk-från koppling delvis Miss lyckas och lämna noden VM i ett felaktigt tillstånd.

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
|--|:--:|
| 1.12 | 1.12.10 eller senare |
| 1.13 | 1.13.8 eller senare |
| 1,14 | 1.14.4 eller senare |
| 1,15 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och noden är i ett felaktigt tillstånd kan du minska genom att manuellt uppdatera VM-statusen med hjälp av någon av följande:

* För ett tillgänglighets uppsättnings kluster:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* För ett VMSS-baserat kluster:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files-och AKS-felsökning

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Vilka är de rekommenderade stabila versionerna av Kubernetes för Azure Files?
 
| Kubernetes-version | Rekommenderad version |
|--|:--:|
| 1.12 | 1.12.6 eller senare |
| 1.13 | 1.13.4 eller senare |
| 1,14 | 1.14.0 eller senare |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Vad är standard-mountOptions när du använder Azure Files?

Rekommenderade inställningar:

| Kubernetes-version | fileMode-och dirMode-värde|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 och senare | 0777 |

Monterings alternativ kan anges för objektet lagrings klass. I följande exempel anges *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Några ytterligare användbara *mountOptions* -inställningar:

* `mfsymlinks` kommer att göra Azure Files montering (CIFS) stöder symboliska länkar
* `nobrl` förhindrar överföring av byte intervall lås begär anden till servern. Den här inställningen är nödvändig för vissa program som slutar med en CIFS-format som är obligatoriska byte intervall lås. De flesta CIFS-servrar har ännu inte stöd för begäran om att låsa byte intervall lås. Om du inte använder *nobrl* kan program som slutar med CIFS-format som är obligatoriska byte intervall lås orsaka fel meddelanden som liknar:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fel "Det gick inte att ändra behörigheter" vid användning av Azure Files

När du kör PostgreSQL på Azure Files-plugin-programmet kan du se ett fel som liknar följande:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Det här felet orsakas av Azure Files-plugin-programmet som använder CIFS/SMB-protokollet. När du använder CIFS/SMB-protokollet, kunde inte fil-och katalog behörigheter ändras efter montering.

Lös problemet genom att använda `subPath` tillsammans med Azures disk-plugin-programmet. 

> [!NOTE] 
> För ext3/4-disk typen finns en förlorad + found katalog efter att disken formaterats.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files har hög latens jämfört med Azure disk vid hantering av många små filer

I vissa fall, t. ex. hantering av många små filer, kan du uppleva hög fördröjning när du använder Azure Files jämfört med Azure disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fel vid aktivering av inställningen Tillåt åtkomst Tillåt åtkomst från valt nätverk på lagrings kontot

Om du aktiverar *Tillåt åtkomst från det valda nätverket* på ett lagrings konto som används för dynamisk etablering i AKS får du ett fel när AKS skapar en fil resurs:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Det här felet beror på att Kubernetes *-persistentvolume* inte finns på nätverket som valdes när du ställer in *Tillåt åtkomst från det valda nätverket*.

Du kan åtgärda problemet genom att använda [statisk etablering med Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files kan inte montera om i Windows Pod

Om en Windows-Pod med en Azure Files-montering tas bort och sedan schemaläggs för att återskapas på samma nod, kommer monteringen att Miss sen. Det här felet beror på att `New-SmbGlobalMapping` kommandot Miss lyckas eftersom Azure Files Mount redan har monterats på noden.

Du kan till exempel se ett fel som liknar:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
|--|:--:|
| 1.12 | 1.12.6 eller senare |
| 1.13 | 1.13.4 eller senare |
| 1,14 och senare | Ej tillämpligt |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Azure Files monteringen Miss lyckas på grund av att lagrings konto nyckeln har ändrats

Om din lagrings konto nyckel har ändrats kan du se Azure Files Mount-felen.

Du kan åtgärda problemet genom att manuellt uppdatera `azurestorageaccountkey` fältet manuellt i en Azure-filhemlighet med din base64-kodade lagrings konto nyckel.

Du kan använda för att koda lagrings konto nyckeln i base64 `base64` . Exempel:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Om du vill uppdatera din Azure-hemlig fil använder du `kubectl edit secret` . Exempel:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Efter några minuter kommer agent-noden att försöka montera Azure-filen igen med den uppdaterade lagrings nyckeln.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Det gick inte att skala kluster autoskalning med felet kunde inte åtgärda grupp storlekarna

Om klustrets autoskalning inte skalar upp/ned och du ser ett fel som det nedan på [loggarna för klustrets autoskalning][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Det här felet beror på ett konkurrens villkor för en överordnad kluster autoskalning. I sådana fall slutar kluster autoskalning med ett annat värde än det som faktiskt finns i klustret. Inaktivera och återaktivera [klustrets autoskalning][cluster-autoscaler]för att komma ur det här läget.

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Långsam disk bilaga, `GetAzureDiskLun` tar 10 till 15 minuter och du får ett fel meddelande

På Kubernetes-versioner som är **äldre än 1.15.0** kan du få ett fel meddelande som **fel WaitForAttach inte kan hitta LUN för disk**.  Lösningen på det här problemet är att vänta cirka 15 minuter och försöka igen.


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Varför fungerar inte uppgraderingar av Kubernetes 1,16 när du använder Node-etiketter med ett kubernetes.io-prefix

Från och med Kubernetes [1,16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) kan [endast en definierad delmängd av etiketter med Kubernetes.io-prefix](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/) användas av kubelet till noder. AKS kan inte ta bort aktiva etiketter för din räkning utan medgivande, eftersom det kan orsaka avbrott i arbets belastningar som påverkas.

Det innebär att du kan åtgärda problemet genom att:

1. Uppgradera ditt kluster kontroll plan till 1,16 eller högre
2. Lägg till en ny nodepoool på 1,16 eller högre utan de kubernetes.io-etiketter som inte stöds
3. Ta bort den äldre Node-poolen

AKS undersöker kapaciteten för att ligga över aktiva etiketter i en Node-pool för att förbättra den här lösningen.



<!-- LINKS - internal -->
[view-master-logs]: ./view-control-plane-logs.md
[cluster-autoscaler]: cluster-autoscaler.md