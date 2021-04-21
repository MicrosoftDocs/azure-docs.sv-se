---
title: Felsöka nätverksproblem med registret
description: Symptom, orsaker och lösningar på vanliga problem vid åtkomst till ett Azure-containerregister i ett virtuellt nätverk eller bakom en brandvägg
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: dc2110405713791d11fb438565fc091da9c9dd5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780760"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Felsöka nätverksproblem med registret

Den här artikeln hjälper dig att felsöka problem som kan uppstå vid åtkomst till ett Azure-containerregister i ett virtuellt nätverk eller bakom en brandvägg eller proxyserver. 

## <a name="symptoms"></a>Symtom

Kan innehålla ett eller flera av följande:

* Det går inte att push-skicka eller hämta avbildningar och du får felmeddelandet `dial tcp: lookup myregistry.azurecr.io`
* Det går inte att push-skicka eller hämta avbildningar och du får felmeddelandet `Client.Timeout exceeded while awaiting headers`
* Det går inte att skicka eller hämta avbildningar och du får Ett Azure CLI-fel visas `Could not connect to the registry login server`
* Det går inte att hämta avbildningar från registret Azure Kubernetes Service en annan Azure-tjänst
* Det går inte att komma åt ett register bakom en HTTPS-proxy och du får `Error response from daemon: login attempt failed with status: 403 Forbidden` felmeddelandet eller `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Det går inte att konfigurera inställningar för virtuellt nätverk och du får felmeddelandet `Failed to save firewall and virtual network settings for container registry`
* Det går inte att komma åt eller visa registerinställningar i Azure Portal eller hantera registret med hjälp av Azure CLI
* Det går inte att lägga till eller ändra inställningar för virtuellt nätverk eller regler för offentlig åtkomst
* ACR-uppgifter kan inte skicka eller hämta avbildningar
* Azure Security Center kan inte genomsöka avbildningar i registret eller så visas inte sökresultat i Azure Security Center
* Du får ett `host is not reachable` felmeddelande när du försöker komma åt ett register som konfigurerats med en privat slutpunkt.

## <a name="causes"></a>Orsaker

* En klientbrandvägg eller proxy förhindrar åtkomst – [lösning](#configure-client-firewall-access)
* Regler för offentlig nätverksåtkomst i registret förhindrar åtkomst – [lösning](#configure-public-access-to-registry)
* Konfiguration av virtuellt nätverk förhindrar åtkomst – [lösning](#configure-vnet-access)
* Du försöker integrera Azure Security Center eller vissa andra Azure-tjänster med ett register som har en privat slutpunkt, tjänstslutpunkt eller offentliga IP-åtkomstregler – [lösning](#configure-service-access)

## <a name="further-diagnosis"></a>Ytterligare diagnos 

Kör kommandot [az acr check-health](/cli/azure/acr#az_acr_check_health) för att få mer information om hälsotillståndet för registermiljön och eventuellt åtkomst till ett målregister. Du kan till exempel diagnostisera vissa problem med nätverksanslutningen eller konfigurationen. 

Kommandoexempel [finns i Kontrollera hälsotillståndet för ett Azure-containerregister.](container-registry-check-health.md) Om fel rapporteras granskar du [felreferensen och](container-registry-health-error-reference.md) följande avsnitt för att hitta rekommenderade lösningar.

Om du har problem med att använda en Azure Kubernetes Service med ett integrerat register kör du [kommandot az aks check-acr](/cli/azure/aks#az_aks_check_acr) för att verifiera att AKS-klustret kan nå registret.

> [!NOTE]
> Vissa problem med nätverksanslutningen kan också uppstå när det finns problem med autentisering eller auktorisering i registret. Se [Felsöka registerinloggning.](container-registry-troubleshoot-login.md)

## <a name="potential-solutions"></a>Möjliga lösningar

### <a name="configure-client-firewall-access"></a>Konfigurera åtkomst till klientbrandväggen

Om du vill komma åt ett register bakom en klientbrandvägg eller proxyserver konfigurerar du brandväggsregler för åtkomst till registrets offentliga REST- och dataslutpunkter. Om [dedikerade dataslutpunkter](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) är aktiverade behöver du regler för åtkomst:

* REST-slutpunkt: `<registryname>.azurecr.io`
* Dataslutpunkter: `<registry-name>.<region>.data.azurecr.io`

För ett geo-replikerat register konfigurerar du åtkomst till dataslutpunkten för varje regional replik.

Kontrollera att både Docker-klienten och Docker-daemonen är konfigurerade för proxybeteende bakom en HTTPS-proxy. Om du ändrar proxyinställningarna för Docker-daemonen måste du starta om daemonen. 

Registerresursloggar i tabellen ContainerRegistryLoginEvents kan hjälpa dig att diagnostisera ett försök till blockerad anslutning.

Relaterade länkar:

* [Konfigurera regler för åtkomst till ett Azure-containerregister bakom en brandvägg](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-proxykonfiguration](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geo-replikeringi Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurera offentlig åtkomst till registret

Om du använder ett register via Internet bekräftar du att registret tillåter offentlig nätverksåtkomst från klienten. Som standard ger ett Azure-containerregister åtkomst till de offentliga registerslutpunkterna från alla nätverk. Ett register kan begränsa åtkomsten till valda nätverk eller valda IP-adresser. 

Om registret har konfigurerats för ett virtuellt nätverk med en tjänstslutpunkt inaktiverar inaktivering av offentlig nätverksåtkomst även åtkomst via tjänstslutpunkten. Om registret har konfigurerats för ett virtuellt nätverk med Private Link gäller inte IP-nätverksregler för registrets privata slutpunkter. 

Relaterade länkar:

* [Konfigurera regler för offentligt IP-nätverk](container-registry-access-selected-networks.md)
* [Ansluta privat till ett Azure-containerregister med hjälp av Azure Private Link](container-registry-private-link.md)
* [Begränsa åtkomsten till ett containerregister med hjälp av en tjänstslutpunkt i ett virtuellt Azure-nätverk](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurera VNet-åtkomst

Bekräfta att det virtuella nätverket har konfigurerats med antingen en privat slutpunkt för Private Link eller en tjänstslutpunkt (förhandsversion). För närvarande Azure Bastion en slutpunkt inte.

Om en privat slutpunkt har konfigurerats bekräftar du att DNS matchar registrets offentliga FQDN, till exempel *myregistry.azurecr.io* till registrets privata IP-adress. Använd ett nätverksverktyg som `dig` eller `nslookup` för DNS-sökning. Kontrollera att [DNS-posterna är konfigurerade](container-registry-private-link.md#dns-configuration-options) för registrets FQDN och för varje dataslutpunkts-FQDN.

Granska NSG-regler och tjänsttaggar som används för att begränsa trafik från andra resurser i nätverket till registret. 

Om en tjänstslutpunkt i registret har konfigurerats bekräftar du att en nätverksregel har lagts till i registret som tillåter åtkomst från nätverkets undernät. Tjänstslutpunkten stöder endast åtkomst från virtuella datorer och AKS-kluster i nätverket.

Om du vill begränsa registeråtkomsten med hjälp av ett virtuellt nätverk i en annan Azure-prenumeration måste du registrera `Microsoft.ContainerRegistry` resursprovidern i prenumerationen. [Registrera resursprovidern](../azure-resource-manager/management/resource-providers-and-types.md) för Azure Container Registry med Azure Portal, Azure CLI eller andra Azure-verktyg.

Om Azure Firewall eller en liknande lösning har konfigurerats i nätverket kontrollerar du att utgående trafik från andra resurser, till exempel ett AKS-kluster, har aktiverats för att nå registerslutpunkterna.

Relaterade länkar:

* [Ansluta privat till ett Azure-containerregister med hjälp av Azure Private Link](container-registry-private-link.md)
* [Felsöka anslutningsproblem för privata slutpunkter i Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Begränsa åtkomsten till ett containerregister med hjälp av en tjänstslutpunkt i ett virtuellt Azure-nätverk](container-registry-vnet.md)
* [Nödvändiga regler för utgående nätverk och FQDN för AKS-kluster](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Felsöka DNS-upplösning](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tjänsttaggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurera tjänståtkomst

För närvarande tillåts inte åtkomst till ett containerregister med nätverksbegränsningar från flera Azure-tjänster:

* Azure Security Center kan inte utföra [](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) sårbarhetsgenomsökning av avbildningar i ett register som begränsar åtkomsten till privata slutpunkter, valda undernät eller IP-adresser. 
* Resurser i vissa Azure-tjänster kan inte komma åt ett containerregister med nätverksbegränsningar, inklusive Azure App Service och Azure Container Instances.

Om åtkomst eller integrering av dessa Azure-tjänster med ditt containerregister krävs tar du bort nätverksbegränsningen. Ta till exempel bort registrets privata slutpunkter eller ta bort eller ändra registrets regler för offentlig åtkomst.

Från och med januari 2021 kan du konfigurera ett nätverksbegränsat register för [att tillåta åtkomst](allow-access-trusted-services.md) från utvalda betrodda tjänster.

Relaterade länkar:

* [Azure Container Registry avbildningsgenomsökning Security Center](../security-center/defender-for-container-registries-introduction.md)
* Ge [feedback](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Tillåt betrodda tjänster att få säker åtkomst till ett nätverksbegränsat containerregister](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Om [insamling av resursloggar](container-registry-diagnostics-audit-logs.md) är aktiverad i registret granskar du loggen ContainterRegistryLoginEvents. Den här loggen lagrar autentiseringshändelser och -status, inklusive inkommande identitet och IP-adress. Fråga loggen efter [fel vid registerautentisering.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Relaterade länkar:

* [Loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)
* [Vanliga frågor och svar om Container Registry](container-registry-faq.md)
* [Azure-säkerhetsbaslinje för Azure Container Registry](security-baseline.md)
* [Bästa metoder för Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nästa steg

Om du inte löser problemet här kan du se följande alternativ.

* Andra avsnitt om felsökning av registret är:
  * [Felsöka registerinloggning](container-registry-troubleshoot-login.md) 
  * [Felsöka registerprestanda](container-registry-troubleshoot-performance.md)
* [Supportalternativ för](https://azure.microsoft.com/support/community/) communityn
* [Microsoft Q&A](/answers/products/)
* [Öppna en supportbegäran](https://azure.microsoft.com/support/create-ticket/)