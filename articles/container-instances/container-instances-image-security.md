---
title: Säkerhetsöverväganden för containerinstanser
description: Rekommendationer för att skydda avbildningar och hemligheter för Azure Container Instances och allmänna säkerhetsöverväganden för alla containerplattformar
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 40284c6e42cf1060906c6248495d08e133bda5bb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812668"
---
# <a name="security-considerations-for-azure-container-instances"></a>Säkerhetsöverväganden för Azure Container Instances

Den här artikeln innehåller säkerhetsöverväganden för att Azure Container Instances för att köra containerappar. Ämnena omfattar:

> [!div class="checklist"]
> * **Säkerhetsrekommendationer** för att hantera bilder och hemligheter för Azure Container Instances
> * **Överväganden för containerekosystemet** under hela containerlivscykeln för alla containerplattformar

Omfattande rekommendationer som hjälper dig att förbättra säkerhetsstatusen för distributionen finns i [Säkerhetsbaslinje för Azure för Container Instances](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Säkerhetsrekommendationer för Azure Container Instances

### <a name="use-a-private-registry"></a>Använda ett privat register

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa lagringsplatsen kan tillhöra ett offentligt register, [Docker Hub](https://hub.docker.com), eller till ett privat register. Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), som kan installeras lokalt eller i ett virtuellt privat moln. Du kan också använda molnbaserade tjänster för privata containerregister, inklusive [Azure Container Registry](../container-registry/container-registry-intro.md). 

En offentligt tillgänglig containeravbildning garanterar inte säkerhet. Containeravbildningar består av flera programvarulager och varje programvarulager kan ha sårbarheter. För att minska risken för attacker bör du lagra och hämta avbildningar från ett privat register, till exempel Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register stöder Azure Container Registry [autentisering](../container-registry/container-registry-authentication.md) med tjänstens huvudnamn via Azure Active Directory för grundläggande autentiseringsflöden. Den här autentiseringen omfattar rollbaserad åtkomst för skrivskyddade (pull),skrivbehörigheter (push-meddelanden) och andra behörigheter.

### <a name="monitor-and-scan-container-images"></a>Övervaka och genomsöka containeravbildningar

Dra nytta av lösningar för att genomsöka containeravbildningar i ett privat register och identifiera potentiella säkerhetsrisker. Det är viktigt att förstå djupet i hotidentifiering som de olika lösningarna ger.

Du kan till Azure Container Registry integrera med [en](../security-center/defender-for-container-registries-introduction.md) Azure Security Center automatiskt genomsöka alla Linux-avbildningar som skickas till ett register. Azure Security Center har en integrerad Qualys-skanner som identifierar sårbarheter i bilder, klassificerar dem och ger vägledning om åtgärder.

Säkerhetsövervaknings- och avbildningsgenomsökningslösningar som [Till exempelSöklås](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) [och Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) är också tillgängliga via Azure Marketplace.  

### <a name="protect-credentials"></a>Skydda autentiseringsuppgifter

Containrar kan spridas över flera kluster och Azure-regioner. Därför måste du skydda de autentiseringsuppgifter som krävs för inloggningar eller API-åtkomst, till exempel lösenord eller token. Se till att endast privilegierade användare kan komma åt dessa containrar under överföring och i vila. Inventera alla hemligheter för autentiseringsuppgifter och kräv sedan att utvecklare använder nya verktyg för hantering av hemligheter som är utformade för containerplattformar.  Se till att din lösning innehåller krypterade databaser, TLS-kryptering för hemligheter under överföring och rollbaserad åtkomstkontroll med minsta behörighet [(Azure RBAC).](../role-based-access-control/overview.md) [Azure Key Vault](../key-vault/general/security-features.md) är en molntjänst som skyddar krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar och lösenord) för program i containrar. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina nyckelvalv så att endast auktoriserade program och användare kan komma åt dem.

## <a name="considerations-for-the-container-ecosystem"></a>Överväganden för containerekosystemet

Följande säkerhetsåtgärder, som implementeras väl och hanteras effektivt, kan hjälpa dig att skydda dina containerekosystem. Dessa mått gäller under hela containerns livscykel, från utveckling till produktionsdistribution och till en mängd containerorkestrerare, värdar och plattformar. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Använda sårbarhetshantering som en del av livscykeln för containerutveckling 

Genom att använda effektiv sårbarhetshantering under hela livscykeln för containerutveckling förbättrar du risken för att du identifierar och löser säkerhetsproblem innan de blir ett mer allvarligt problem. 

### <a name="scan-for-vulnerabilities"></a>Söka efter sårbarheter 

Nya sårbarheter upptäcks hela tiden, så att söka efter och identifiera sårbarheter är en kontinuerlig process. Införliva sårbarhetsgenomsökning under containerns livscykel:

* Som en slutlig kontroll i din utvecklingspipeline bör du utföra en sårbarhetsgenomsökning på containrar innan du push-kar avbildningarna till ett offentligt eller privat register. 
* Fortsätt att genomsöka containeravbildningar i registret både för att identifiera eventuella brister som har missats under utvecklingen och åtgärda eventuella nyligen identifierade säkerhetsrisker som kan finnas i koden som används i containeravbildningarna.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappa sårbarheter i avbildningar till containrar som körs 

Du måste ha ett sätt att mappa sårbarheter som identifieras i containeravbildningar till containrar som körs, så att säkerhetsproblem kan åtgärdas eller åtgärdas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Se till att endast godkända avbildningar används i din miljö 

Det finns tillräckligt med förändring och instabilitet i ett containerekosystem utan att även tillåta okända containrar. Tillåt endast godkända containeravbildningar. Ha verktyg och processer på plats för att övervaka och förhindra användning av icke-godkända containeravbildningar. 

Ett effektivt sätt att minska angreppsytan och hindra utvecklare från att göra kritiska säkerhetsmisstag är att styra flödet av containeravbildningar till utvecklingsmiljön. Du kan till exempel sanktionera en enskild Linux-distribution som en basavbildning, helst en som är Lean (Alpine eller CoreOS i stället för Ubuntu), för att minimera risken för potentiella attacker. 

Signering av avbildningar eller fingeravtryck kan ge en kedja av kontroll som gör att du kan verifiera integriteten för containrarna. Till exempel Azure Container Registry dockers modell för [](https://docs.docker.com/engine/security/trust/content_trust) innehållsförtroende, vilket gör att avbildningsutgivare kan signera avbildningar som push-överförts till ett register och avbildningskonsumenter endast kan hämta signerade avbildningar.

### <a name="permit-only-approved-registries"></a>Tillåt endast godkända register 

En utökning av att se till att din miljö endast använder godkända avbildningar är att endast tillåta användning av godkända containerregister. Genom att kräva användning av godkända containerregister minskar du risken genom att begränsa risken för att införa okända säkerhetsrisker eller säkerhetsproblem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Säkerställa integriteten för bilder under hela livscykeln 

En del av att hantera säkerheten under hela containerlivscykeln är att säkerställa integriteten för containeravbildningarna i registret och när de ändras eller distribueras till produktion. 

* Avbildningar med sårbarheter, även mindre, bör inte tillåtas att köras i en produktionsmiljö. Vi rekommenderar att alla avbildningar som distribueras i produktion ska sparas i ett privat register som är tillgängligt för några få. Se till att antalet produktionsavbildningar är litet för att säkerställa att de kan hanteras effektivt.

* Eftersom det är svårt att hitta ursprunget för programvara från en offentligt tillgänglig containeravbildning kan du skapa avbildningar från källan för att säkerställa kunskap om skiktets ursprung. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med en offentlig avbildning skulle kunderna behöva hitta roten för en offentlig avbildning för att åtgärda den eller hämta en annan säker avbildning från utgivaren. 

* En noggrant genomsökt avbildning som distribueras i produktion är inte garanterad att vara uppdaterad under programmets livslängd. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. 

  Granska regelbundet avbildningar som distribuerats i produktion för att identifiera avbildningar som är in datera eller som inte har uppdaterats på ett tag. Du kan använda blågröna distributionsmetoder och mekanismer för löpande uppgradering för att uppdatera containeravbildningar utan driftstopp. Du kan skanna bilder med hjälp av verktyg som beskrivs i föregående avsnitt. 

* Använd en pipeline för kontinuerlig integrering (CI) med integrerad säkerhetsgenomsökning för att skapa säkra avbildningar och skicka dem till ditt privata register. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. 

  Ett CI-pipelinefel säkerställer att sårbara avbildningar inte skickas till det privata register som används för distribution av produktionsarbetsbelastningar. Det automatiserar även genomsökning av avbildningssäkerhet om det finns ett stort antal avbildningar. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift. 

### <a name="enforce-least-privileges-in-runtime"></a>Framtvinga minsta behörighet i körning 

Begreppet minsta behörighet är en grundläggande metod för säkerhet som även gäller för containrar. När en säkerhetsrisk utnyttjas ger den vanligtvis angriparen åtkomst och privilegier som motsvarar de behörigheter som det komprometterade programmet eller processen har. Om du ser till att containrar fungerar med de lägsta behörigheter och den åtkomst som krävs för att få jobbet gjort minskar risken för risk. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Minska containerangreppsytan genom att ta bort behörigheter som inte krävs 

Du kan också minimera risken för angrepp genom att ta bort eventuella oanvända eller onödiga processer eller privilegier från containerkörningen. Privilegierade containrar körs som rot. Om en obehörig användare eller arbetsbelastning ryms i en privilegierad container körs containern sedan som rot i systemet.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Förbeskriva filer och körbara filer som containern har behörighet att komma åt eller köra 

Genom att minska antalet variabler eller okända variabler kan du upprätthålla en stabil och tillförlitlig miljö. Att begränsa containrar så att de bara kan komma åt eller köra förinstallerade eller säkra filer och körbara filer är en beprövad metod för att begränsa exponeringen för risker.  

Det är mycket enklare att hantera en säker lista när den implementeras från början. En säker lista ger ett mått på kontroll och hanterbarhet när du lär dig vilka filer och körbara filer som krävs för att programmet ska fungera korrekt. 

En säker lista minskar inte bara angreppsytan, utan kan även ge en baslinje för avvikelser och förhindra användningsfall för scenarier med "störande grannar" och containrar. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Framtvinga nätverkssegmentering på containrar som körs  

Skydda containrar i ett undernät mot säkerhetsrisker i ett annat undernät genom att underhålla nätverkssegmentering (eller nanosegmentering) eller uppdelning mellan containrar som körs. Underhåll av nätverkssegmentering kan också vara nödvändigt för att använda containrar i branscher som krävs för att uppfylla efterlevnadskrav.  

Partnerverktyget Aqua är till [exempel en](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) automatiserad metod för nanosegmentering. Aqua övervakar containernätverksaktiviteter under körning. Den identifierar alla inkommande och utgående nätverksanslutningar till/från andra containrar, tjänster, IP-adresser och offentligt Internet. Nanosegmentering skapas automatiskt baserat på övervakad trafik. 

### <a name="monitor-container-activity-and-user-access"></a>Övervaka containeraktivitet och användaråtkomst 

Precis som med alla IT-miljöer bör du konsekvent övervaka aktivitet och användaråtkomst till ditt containerekosystem för att snabbt identifiera misstänkta eller skadliga aktiviteter. Azure tillhandahåller lösningar för övervakning av containrar, inklusive:

* [Azure Monitor för containrar](../azure-monitor/containers/container-insights-overview.md) övervakar prestanda för arbetsbelastningar som distribueras till Kubernetes-miljöer som finns Azure Kubernetes Service (AKS). Azure Monitor för containrar ger dig prestandasynlighet genom att samla in minnes- och processormått från styrenheter, noder och containrar som är tillgängliga i Kubernetes via Metrics-API:et. 

* Azure [Container Monitoring-lösningen hjälper](../azure-monitor/containers/containers.md) dig att visa och hantera andra Docker- och Windows-containervärdar på en enda plats. Exempel:

  * Visa detaljerad granskningsinformation som visar kommandon som används med containrar. 
  * Felsök containrar genom att visa och söka i centraliserade loggar utan att behöva visa Docker- eller Windows-värdar via fjärrstyrning.  
  * Hitta containrar som kan vara brus och förbruka överflödiga resurser på en värd.
  * Visa centraliserad cpu, minne, lagring, nätverksanvändning och prestandainformation för containrar.  

  Lösningen stöder containerorkestrerare som Docker Swarm, DC/OS, ohanterade Kubernetes, Service Fabric och Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Övervaka containerresursaktivitet 

Övervaka din resursaktivitet, till exempel filer, nätverk och andra resurser som dina containrar har åtkomst till. Övervakning av resursaktivitet och förbrukning är användbart både för prestandaövervakning och som en säkerhetsåtgärd. 

[Azure Monitor](../azure-monitor/overview.md) kärnövervakning för Azure-tjänster genom att tillåta insamling av mått, aktivitetsloggar och diagnostikloggar. Aktivitetsloggen ger dig t.ex. information om när nya resurser skapas eller ändras. 

  Tillgängliga mått tillhandahåller prestandastatistik för olika resurser och även för operativsystemet i en virtuell dator. Du kan visa dessa data med någon av utforskarna i Azure Portal och skapa aviseringar som baseras på måtten. Azure Monitor ger den snabbaste måttpipelinen (från 5 minuter ned till 1 minut), så du bör använda det för tidskritiska aviseringar och meddelanden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Logga all administrativ användaråtkomst för container för granskning 

Upprätthåll en korrekt granskningslogg för administrativ åtkomst till ditt containerekosystem, inklusive Kubernetes-kluster, containerregister och containeravbildningar. De här loggarna kan vara nödvändiga i granskningssyfte och är användbara som kriminaltekniska bevis efter en säkerhetsincident. Azure-lösningar omfattar:

* [Integrering av Azure Kubernetes Service med Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) för att övervaka klustermiljöns säkerhetskonfiguration och generera säkerhetsrekommendationer
* [Övervakningslösning för Azure-containrar](../azure-monitor/containers/containers.md)
* Resursloggar [för Azure Container Instances](container-instances-log-analytics.md) [och Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Nästa steg

* I [Azure-säkerhetsbaslinjen finns Container Instances](security-baseline.md) omfattande rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

* Läs mer om [att Azure Security Center](../security-center/container-security.md) för hotidentifiering i realtid i dina containermiljöer.

* Läs mer om att hantera sårbarheter i containrar med lösningar [från Variantlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) och [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)