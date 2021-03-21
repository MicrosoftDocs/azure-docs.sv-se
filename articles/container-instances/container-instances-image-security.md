---
title: Säkerhets aspekter för behållar instanser
description: Rekommendationer för att skydda bilder och hemligheter för Azure Container Instances och allmänna säkerhets överväganden för alla behållar plattformar
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: cbceeea24501bc9815cb07e1b0a054914ba8e964
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579270"
---
# <a name="security-considerations-for-azure-container-instances"></a>Säkerhets överväganden för Azure Container Instances

Den här artikeln beskriver säkerhets överväganden för att använda Azure Container Instances för att köra behållar appar. Ämnena omfattar:

> [!div class="checklist"]
> * **Säkerhets rekommendationer** för att hantera avbildningar och hemligheter för Azure Container instances
> * **Överväganden för containerns eko system** under hela livs cykelns livs cykel, för alla behållar plattformar

Omfattande rekommendationer som hjälper dig att förbättra position i distributionen finns i [Azures säkerhets bas linje för container instances](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Säkerhets rekommendationer för Azure Container Instances

### <a name="use-a-private-registry"></a>Använd ett privat register

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa databaser kan tillhöra ett offentligt register, som [Docker Hub](https://hub.docker.com)eller till ett privat register. Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), som kan installeras lokalt eller i ett virtuellt privat moln. Du kan också använda molnbaserad privata behållar register tjänster, inklusive [Azure Container Registry](../container-registry/container-registry-intro.md). 

En offentligt tillgänglig behållar avbildning garanterar inte säkerheten. Behållar avbildningar består av flera program varu lager och varje program varu lager kan ha sårbarheter. För att minska risken för attacker bör du lagra och hämta avbildningar från ett privat register, till exempel Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register, Azure Container Registry stödja [tjänstens huvudbaserad autentisering](../container-registry/container-registry-authentication.md) via Azure Active Directory för grundläggande autentiserings flöden. Den här autentiseringen innehåller rollbaserad åtkomst för skrivskyddad (pull), skrivning (push) och andra behörigheter.

### <a name="monitor-and-scan-container-images"></a>Övervaka och skanna behållar avbildningar

Dra nytta av lösningar för att skanna behållar avbildningar i ett privat register och identifiera potentiella sårbarheter. Det är viktigt att förstå djupet vid hot identifiering som de olika lösningarna ger.

Azure Container Registry kan till exempel [integreras med Azure Security Center](../security-center/defender-for-container-registries-introduction.md) för att automatiskt söka igenom alla Linux-avbildningar som skickas till ett register. Azure Security Center s integrerade Qualys-skanner identifierar avbildnings sårbarheter, klassificerar dem och ger vägledning om åtgärder.

Säkerhets övervakning och lösningar för avbildnings genomsökning, till exempel [twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) och [turkos säkerhet](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , är också tillgängliga via Azure Marketplace.  

### <a name="protect-credentials"></a>Skydda autentiseringsuppgifter

Behållare kan spridas över flera kluster och Azure-regioner. Därför måste du skydda autentiseringsuppgifter som krävs för inloggningar eller API-åtkomst, till exempel lösen ord eller tokens. Se till att endast privilegierade användare kan komma åt dessa behållare under överföring och i vila. Inventera alla hemligheter för autentiseringsuppgifter och Kräv sedan att utvecklare använder nya hemligheter – hanterings verktyg som är utformade för behållar plattformar.  Se till att lösningen innehåller krypterade databaser, TLS-kryptering för hemligheterade data i överföring och minst privilegierad [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) är en moln tjänst som skyddar krypterings nycklar och hemligheter (till exempel certifikat, anslutnings strängar och lösen ord) för program i behållare. Eftersom dessa data är känsliga och affärs kritiska kan du skydda åtkomsten till dina nyckel valv så att endast auktoriserade program och användare kan komma åt dem.

## <a name="considerations-for-the-container-ecosystem"></a>Överväganden för containerns eko system

Följande säkerhets åtgärder, som implementeras väl och hanteras effektivt, kan hjälpa dig att skydda och skydda ditt eko system. Dessa åtgärder gäller under hela livs cykelns livs cykel, från utveckling till produktions distribution och till ett antal behållar dirigeringar, värdar och plattformar. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Använd sårbarhets hantering som en del av livs cykeln för behållar utveckling 

Genom att använda effektiv sårbarhets hantering i livs cykeln för container utveckling förbättrar du strider som du identifierar och löser säkerhets problem innan de blir allvarligare. 

### <a name="scan-for-vulnerabilities"></a>Sök efter sårbarheter 

Nya sårbarheter upptäcks hela tiden, så genomsökning av och identifiera sårbarheter är en kontinuerlig process. Införliva säkerhets genomsökning under hela livs cykeln för behållare:

* Som en slutgiltig kontroll i din utvecklings pipeline bör du utföra en sårbarhets sökning på behållare innan du skickar avbildningarna till ett offentligt eller privat register. 
* Fortsätt att skanna behållar avbildningar i registret för att identifiera eventuella fel som skulle ha missats under utvecklingen och för att åtgärda eventuella nyligen identifierade säkerhets risker som kan finnas i koden som används i behållar avbildningarna.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Avbilda avbildnings sårbarheter för att köra behållare 

Du behöver ett sätt att mappa sårbarheter som identifieras i behållar avbildningar till att köra behållare, så säkerhets problem kan åtgärdas eller lösas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Se till att endast godkända avbildningar används i din miljö 

Det finns tillräckligt med ändrings-och flyktiga i ett eko system för behållare utan att tillåta okända behållare. Tillåt endast godkända behållar avbildningar. Ha verktyg och processer på plats för att övervaka och förhindra användning av icke-godkända behållar avbildningar. 

Ett effektivt sätt att minska risken för attacker och förhindra utvecklare från att fatta kritiska säkerhets fel är att styra flödet av behållar avbildningar till utvecklings miljön. Du kan till exempel sanktionera en enskild Linux-distribution som en bas avbildning, helst en som är Lean (Alpine eller core i stället för Ubuntu), för att minimera ytan för potentiella attacker. 

Bild signering eller finger avtryck kan ge en kedja av vårdnad som gör det möjligt att verifiera behållarens integritet. Azure Container Registry har till exempel stöd för Docker-modellen för [innehålls förtroende](https://docs.docker.com/engine/security/trust/content_trust) , som tillåter avbildnings utgivare att signera bilder som skickas till ett register och avbildnings konsumenter för att bara Hämta signerade avbildningar.

### <a name="permit-only-approved-registries"></a>Tillåt endast godkända register 

Ett tillägg för att säkerställa att miljön endast använder godkända avbildningar är att endast tillåta användning av godkända behållar register. Genom att kräva att godkända behållar register används minskar exponeringen för risker genom att begränsa möjligheten att införa okända sårbarheter eller säkerhets problem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Säkerställa integriteten för avbildningar under hela livs cykeln 

En del av att hantera säkerhet i livs cykeln för behållare är att säkerställa att behållar avbildningens integritet i registret och att de ändras eller distribueras till produktion. 

* Avbildningar med sårbarheter, även mindre, bör inte tillåtas att köras i produktions miljöer. Vi rekommenderar att alla avbildningar som distribueras i produktion sparas i ett privat register som är tillgängligt för ett urval. Behåll antalet produktions bilder små för att säkerställa att de kan hanteras effektivt.

* Eftersom det är svårt att hitta ursprunget för program varan från en offentligt tillgänglig behållar avbildning skapar du avbildningar från källan för att säkerställa att lagrets ursprung. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med en offentlig avbildning måste kunderna hitta roten till en offentlig avbildning för att kunna åtgärda den eller få en annan säker avbildning från utgivaren. 

* En väl skannad avbildning som distribueras i produktion är inte garanterat uppdaterad under programmets livs längd. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. 

  Granska regelbundet bilder som distribuerats i produktion för att identifiera avbildningar som är inaktuella eller som inte har uppdaterats på ett tag. Du kan använda blå och gröna distributions metoder och metoder för löpande uppgradering för att uppdatera behållar avbildningar utan drift stopp. Du kan skanna bilder med hjälp av verktygen som beskrivs i föregående avsnitt. 

* Använd en pipeline för kontinuerlig integrering (CI) med integrerad säkerhets genomsökning för att bygga säkra avbildningar och skicka dem till ditt privata register. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. 

  Ett problem med en CI-pipeline garanterar att sårbara avbildningar inte skickas till det privata registret som används för distributioner av produktions arbets belastningar. Dessutom automatiseras avbildnings säkerhets genomsökningen om det finns ett stort antal avbildningar. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift. 

### <a name="enforce-least-privileges-in-runtime"></a>Framtvinga minst behörighet i körnings miljön 

Begreppet minst privilegier är en grundläggande säkerhets metod som också gäller för behållare. När ett säkerhets problem utnyttjas ger det vanligt vis angripare åtkomst och privilegier som motsvarar de i det komprometterade programmet eller processen. Att se till att behållare använder de lägsta privilegier och åtkomst som krävs för att få jobbet utfört minskar exponeringen för risk. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Minska behållar attack ytan genom att ta bort onödiga privilegier 

Du kan också minimera den potentiella angrepps ytan genom att ta bort oanvända eller onödiga processer eller behörigheter från container Runtime. Privilegierade behållare körs som rot. Om en obehörig användare eller arbets belastning utsätts i en privilegie rad behållare, körs behållaren som rot på det systemet.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Förgodkänna filer och körbara filer som behållaren har behörighet att komma åt eller köra 

Genom att minska antalet variabler eller okända, kan du underhålla en stabil, tillförlitlig miljö. Begränsa behållare så att de kan komma åt eller köra enbart förgodkännda eller safelisted filer och körbara filer är en beprövad metod för att begränsa exponeringen för risker.  

Det är mycket enklare att hantera en SAFELIST när den implementeras från början. En SAFELIST tillhandahåller ett mått på kontroll och hanterbarhet när du lär dig vilka filer och körbara filer som krävs för att programmet ska fungera korrekt. 

En SAFELIST minskar inte bara angrepps ytan, men kan också tillhandahålla en bas linje för avvikelser och förhindra användnings fall av scenarier med "bruset" och container grupp. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Tvinga nätverks segmentering på behållare som körs  

Du kan skydda behållare i ett undernät från säkerhets risker i ett annat undernät genom att underhålla nätverks segmentering (eller nano-segmentering) eller uppdelning mellan behållare som körs. Det kan också vara nödvändigt att underhålla nätverks segmentering för att kunna använda behållare i branscher som krävs för att uppfylla efterlevnads uppdragen.  

Till exempel tillhandahåller partner verktyget [turkos](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) en automatiserad metod för nano-segmentering. Turkos övervakar nätverks aktiviteter för behållare i körnings miljön. Den identifierar alla inkommande och utgående nätverks anslutningar till/från andra behållare, tjänster, IP-adresser och det offentliga Internet. Nano-segmentering skapas automatiskt baserat på övervakad trafik. 

### <a name="monitor-container-activity-and-user-access"></a>Övervaka container aktivitet och användar åtkomst 

Precis som med en IT-miljö bör du konsekvent övervaka aktivitet och användares åtkomst till ditt container eko system för att snabbt identifiera misstänkt eller skadlig aktivitet. Azure tillhandahåller övervaknings lösningar för behållare, inklusive:

* [Azure Monitor for containers](../azure-monitor/containers/container-insights-overview.md) övervakar arbets Belastningens prestanda som distribueras till Kubernetes-miljöer som finns i Azure Kubernetes service (AKS). Azure Monitor för behållare ger dig prestanda synlighet genom att samla in minnes-och processor mått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics-API: et. 

* [Azure Container Monitoring-lösningen](../azure-monitor/containers/containers.md) hjälper dig att visa och hantera andra Docker-och Windows container-värdar på en enda plats. Exempel:

  * Visa detaljerad gransknings information som visar kommandon som används med behållare. 
  * Felsök behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrans luta till Docker eller Windows-värdar.  
  * Hitta behållare som kan vara störningar och förbruka överskott av resurser på en värd.
  * Visa centraliserad processor, minne, lagring och nätverks användning och prestanda information för behållare.  

  Lösningen stöder behållar dirigering, inklusive Docker Swarm, DC/OS, ohanterad Kubernetes, Service Fabric och Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Övervaka container resurs aktivitet 

Övervaka resurs aktiviteten, t. ex. filer, nätverk och andra resurser som behållarna har åtkomst till. Övervakning av resurs aktivitet och konsumtion är användbart både för prestanda övervakning och som ett säkerhets mått. 

[Azure Monitor](../azure-monitor/overview.md) möjliggör kärn övervakning av Azure-tjänster genom att tillåta insamling av mått, aktivitets loggar och diagnostikloggar. Aktivitetsloggen ger dig t.ex. information om när nya resurser skapas eller ändras. 

  Tillgängliga mått tillhandahåller prestandastatistik för olika resurser och även för operativsystemet i en virtuell dator. Du kan visa dessa data med någon av utforskarna i Azure Portal och skapa aviseringar som baseras på måtten. Azure Monitor ger den snabbaste måttpipelinen (från 5 minuter ned till 1 minut), så du bör använda det för tidskritiska aviseringar och meddelanden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Logga all behållar administrativ användar åtkomst för granskning 

Upprätthålla en korrekt gransknings historik för administrativ åtkomst till ditt container eko system, inklusive ditt Kubernetes-kluster, behållar register och behållar avbildningar. Dessa loggar kan vara nödvändiga för gransknings syfte och kommer att vara användbara som kriminal tekniska-bevis efter eventuella säkerhets incidenter. Azure-lösningar är:

* [Integrering av Azure Kubernetes-tjänsten med Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) för att övervaka säkerhets konfigurationen för kluster miljön och skapa säkerhets rekommendationer
* [Azure Container Monitor-lösning](../azure-monitor/containers/containers.md)
* Resurs loggar för [Azure Container instances](container-instances-log-analytics.md) och [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Nästa steg

* I [Azures säkerhets bas linje finns container instances](security-baseline.md) för omfattande rekommendationer som hjälper dig att förbättra din distributions säkerhets position.

* Läs mer om hur du använder [Azure Security Center](../security-center/container-security.md) för real tids identifiering av hot i dina behållares miljöer.

* Lär dig mer om att hantera sårbarheter för behållare med lösningar från [twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) och [turkos säkerhet](https://www.aquasec.com/solutions/azure-container-security/).