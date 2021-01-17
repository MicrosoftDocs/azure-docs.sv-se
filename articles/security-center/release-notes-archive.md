---
title: Arkiv av nyheter i Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center från sex månader sedan och tidigare.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2020
ms.author: memildin
ms.openlocfilehash: 751ee19225e7e550f368fff2415cd07f25b02d25
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539930"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Vill du arkivera vad som är nytt i Azure Security Center?

Den primära sidan [Nyheter i Azure Security Center?](release-notes.md) viktig information innehåller uppdateringar de senaste sex månaderna, medan den här sidan innehåller äldre objekt.

På den här sidan får du information om:

- Nya funktioner
- Felkorrigeringar
- Föråldrade funktioner


## <a name="july-2020"></a>Juli 2020

I juli ingår följande uppdateringar:
- [Sårbarhets bedömning för virtuella datorer är nu tillgängligt för avbildningar som inte är Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Skydd mot Azure Storage utökas till att omfatta Azure Files och Azure Data Lake Storage Gen2 (förhands granskning)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Åtta nya rekommendationer för att aktivera hot skydds funktioner](#eight-new-recommendations-to-enable-threat-protection-features)
- [Förbättringar av behållar säkerhet – snabbare register genomsökning och uppdaterad dokumentation](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Anpassningsbara program kontroller uppdaterade med en ny rekommendation och stöd för jokertecken i Sök vägs regler](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sex principer för avancerad data säkerhet i SQL är inaktuell](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Sårbarhets bedömning för virtuella datorer är nu tillgängligt för avbildningar som inte är Marketplace

När du distribuerar en lösning för sårbarhets bedömning Security Center tidigare genomförde en verifierings kontroll innan du distribuerar. Kontrollen var att bekräfta en Marketplace-SKU för den virtuella mål datorn. 

Från den här uppdateringen har kontrollen tagits bort och nu kan du distribuera verktyg för sårbarhets bedömning till anpassade Windows-och Linux-datorer. Anpassade avbildningar är de som du har ändrat från Marketplace-standardvärden.

Även om du nu kan distribuera det integrerade tillägget för sårbarhets bedömning (drivs av Qualys) på många fler datorer, är support endast tillgängligt om du använder ett operativ system som anges i [distribuera den integrerade säkerhets luckan till virtuella datorer med standard nivå](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

Läs mer om den [integrerade säkerhets genomsökningen för virtuella datorer (kräver Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Lär dig mer om att använda en egen privat licensierad lösning för sårbarhets bedömning från Qualys eller Rapid7 i [distribuera en lösning för genomsökning av partner problem](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Skydd mot Azure Storage utökas till att omfatta Azure Files och Azure Data Lake Storage Gen2 (förhands granskning)

Skydd mot hot för Azure Storage identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Security Center visar aviseringar när du upptäcker försök att komma åt eller utnyttja dina lagrings konton. 

Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Åtta nya rekommendationer för att aktivera hot skydds funktioner

Åtta nya rekommendationer har lagts till för att ge ett enkelt sätt att aktivera Azure Security Centers hot skydds funktioner för följande resurs typer: virtuella datorer, App Service-planer, Azure SQL Database-servrar, SQL-servrar på datorer, Azure Storage konton, Azure Kubernetes service-kluster, Azure Container Registry register och Azure Key Vault-valv.

De nya rekommendationerna är:

- **Avancerad data säkerhet ska vara aktiverat på Azure SQL Database servrar**
- **Avancerad data säkerhet ska vara aktiverat på SQL-servrar på datorer**
- **Avancerat skydd bör vara aktiverat på Azure App Service planer**
- **Avancerat skydd bör vara aktiverat på Azure Container Registry register**
- **Avancerat skydd bör vara aktiverat på Azure Key Vault valv**
- **Avancerat skydd bör vara aktiverat på Azure Kubernetes service-kluster**
- **Avancerat skydd bör vara aktiverat på Azure Storage konton**
- **Avancerat skydd bör vara aktiverat på virtuella datorer**

Dessa nya rekommendationer tillhör säkerhets kontrollen **Aktivera Azure Defender** .

Rekommendationerna omfattar även snabb korrigerings funktionen. 

> [!IMPORTANT]
> Att reparera någon av dessa rekommendationer ger kostnader för att skydda relevanta resurser. Dessa avgifter börjar omedelbart om du har relaterade resurser i den aktuella prenumerationen. Eller i framtiden, om du lägger till dem senare.
> 
> Om du till exempel inte har några Azure Kubernetes service-kluster i din prenumeration och du aktiverar hot skyddet debiteras inga avgifter. Om du i framtiden lägger till ett kluster i samma prenumeration, kommer det automatiskt att skyddas och kostnader kommer att börja vid den tidpunkten.

Läs mer om var och en av dessa på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).

Läs mer om [skydd av hot i Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Förbättringar av behållar säkerhet – snabbare register genomsökning och uppdaterad dokumentation

Som en del av de kontinuerliga investeringarna i behållar säkerhets domänen är vi glada att dela en betydande prestanda förbättring i Security Center dynamiska genomsökningar av behållar avbildningar som lagras i Azure Container Registry. Genomsökningar är nu vanligt vis slutförda på ungefär två minuter. I vissa fall kan det ta upp till 15 minuter.

För att förbättra skärpan och vägledningen avseende Azure Security Center behållar säkerhetsfunktioner har vi också uppdaterat dokumentations sidorna för behållar säkerhet. 

Läs mer om Security Center behållar säkerhet i följande artiklar:

- [Översikt över Security Center behållar säkerhetsfunktioner](container-security.md)
- [Information om integreringen med Azure Container Registry](defender-for-container-registries-introduction.md)
- [Information om integrering med Azure Kubernetes-tjänsten](defender-for-kubernetes-introduction.md)
- [Anvisningar – söka igenom dina register och torka Docker-värdarna](container-security.md)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-kluster](alerts-reference.md#alerts-akscluster)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-värdar](alerts-reference.md#alerts-containerhost)
- [Säkerhets rekommendationer för behållare](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Anpassningsbara program kontroller uppdaterade med en ny rekommendation och stöd för jokertecken i Sök vägs regler

Funktionen för anpassningsbara program kontroller har tagit emot två viktiga uppdateringar:

* En ny rekommendation identifierar potentiellt legitimt beteende som inte tidigare har tillåtits. Den nya rekommendationen, **tillåten-regler i din anpassningsbar program kontroll princip ska uppdateras**, och du uppmanas att lägga till nya regler i den befintliga principen för att minska antalet falska positiva identifieringar i adaptiva program kontroller fel aviseringar.

* Sök vägs regler stöder nu jokertecken. Från den här uppdateringen kan du konfigurera tillåtna Sök vägs regler med hjälp av jokertecken. Det finns två scenarier som stöds:

    * Använda jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermappar

    * Använd ett jokertecken i mitten av en sökväg för att aktivera ett känt körbart namn med ett ändrat mappnamn (t. ex. personliga mappar med en känd körbar fil, automatiskt genererade mappnamn osv.).


[Läs mer om anpassningsbara program kontroller](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sex principer för avancerad data säkerhet i SQL är inaktuell

Sex principer relaterade till avancerad data säkerhet för SQL-datorer är föråldrade:

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server

Läs mer om [inbyggda principer](./policy-reference.md).



## <a name="june-2020"></a>Juni 2020

Uppdateringarna i juni inkluderar:
- [Secure score-API (för hands version)](#secure-score-api-preview)
- [Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokalt) (för hands version)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nya principer för att aktivera hot skydd och avancerad data säkerhet](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure score-API (för hands version)

Nu kan du komma åt dina poäng via [Secure score-API: t](/rest/api/securitycenter/securescores/) (för närvarande i för hands version). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda **Secure Scores** -API: et för att hämta poängen för en speciell prenumeration. Dessutom kan du använda API: et för **säker Poäng** för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

Exempel på externa verktyg som möjliggörs med Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokalt) (för hands version)

Azure Security Center avancerade data säkerhet för SQL-datorer skyddar nu SQL-servrar som finns i Azure, i andra moln miljöer och till och med lokala datorer. Detta utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullt stödja hybrid miljöer.

Avancerad data säkerhet ger sårbarhets bedömning och Avancerat skydd för dina SQL-datorer oavsett var de befinner sig.

Konfigurations åtgärder omfattar två steg:

1. Distribuera Log Analytics agenten till SQL Servers värddator för att tillhandahålla anslutningen till Azure-kontot.

1. Aktivera det valfria paketet på Security Center pris-och inställnings sida.

Lär dig mer om [Avancerad data säkerhet för SQL-datorer](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)

Två nya rekommendationer har lagts till för att hjälpa till att distribuera [Log Analytics agenten](../azure-monitor/platform/log-analytics-agent.md) till dina Azure Arc-datorer och se till att de skyddas av Azure Security Center:

- **Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)**
- **Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)**

De här nya rekommendationerna visas i samma fyra säkerhets kontroller som den befintliga (relaterade) rekommendationen, **övervaknings agenten ska installeras på datorerna**: åtgärda säkerhetskonfigurationer, tillämpa anpassningsbar program kontroll, tillämpa system uppdateringar och Aktivera Endpoint Protection.

Rekommendationerna innehåller också snabb korrigerings funktionen som hjälper dig att påskynda distributions processen. 

Läs mer om de här två nya rekommendationerna i tabellen med [rekommendationer för beräkning och appar](recommendations-reference.md#recs-compute) .

Läs mer om hur Azure Security Center använder agenten i [Vad är Log Analytics agenten?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Läs mer om [tillägg för Azure Arc-datorer](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Om du vill distribuera dina automatiserings konfigurationer i organisationen använder du de här inbyggda "DeployIfdNotExist" Azure-principerna för att skapa och konfigurera [kontinuerliga](continuous-export.md) metoder för export och [automatisering av arbets flöde](workflow-automation.md) :

Du hittar principerna i Azure policy:


|Mål  |Princip  |Princip-ID  |
|---------|---------|---------|
|Kontinuerlig export till händelsehubben|[Distribuera export till händelsehubben för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Löpande export till Log Analytics arbets yta|[Distribuera export till Log Analytics-arbetsytan för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Arbets flödes automatisering för säkerhets aviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbets flödes automatisering för säkerhets rekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Kom igång med [automatiserings mallar för arbets flöden](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Lär dig mer om att använda de två export principerna i [Konfigurera automatisering av arbets flöden i skala med hjälp av de angivna principerna](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) och [Konfigurera en löpande export](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade

Säkerhets kontrollen "implementera rekommenderade säkerhets metoder" innehåller nu följande nya rekommendation:

- **Virtuella datorer som inte är baserade på Internet bör skyddas med nätverks säkerhets grupper**

En befintlig rekommendation, **virtuella datorer som riktas mot Internet bör skyddas med nätverks säkerhets grupper**, skiljer sig inte mellan Internet-riktade och icke-Internetbaserade virtuella datorer. För båda, genererade en rekommendation med hög allvarlighets grad om en virtuell dator inte var tilldelad till en nätverks säkerhets grupp. Den nya rekommendationen separerar datorer som inte är på Internet för att minska antalet falska positiva identifieringar och undvika onödiga aviseringar med hög allvarlighets grad.

Läs mer i tabellen med [nätverks rekommendationer](recommendations-reference.md#recs-networking) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nya principer för att aktivera hot skydd och avancerad data säkerhet

De nya principerna nedan lades till i ASC-standardinitiativet och är utformade för att hjälpa till med att aktivera skydd av hot eller avancerad data säkerhet för relevanta resurs typer.

Du hittar principerna i Azure policy:


| Princip                                                                                                                                                                                                                                                                | Princip-ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Avancerad data säkerhet ska vara aktiverat på Azure SQL Database servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Avancerad data säkerhet ska vara aktiverat på SQL-servrar på datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Avancerat skydd bör vara aktiverat på Azure Storage konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Avancerat skydd bör vara aktiverat på Azure Key Vault valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Avancerat skydd bör vara aktiverat på Azure App Service planer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Avancerat skydd bör vara aktiverat på Azure Container Registry register](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Avancerat skydd bör vara aktiverat på Azure Kubernetes service-kluster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Avancerat skydd bör vara aktiverat på Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Läs mer om [skydd av hot i Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Maj 2020

Uppdateringar i kan vara:
- [Undervisnings regler för aviseringar (för hands version)](#alert-suppression-rules-preview)
- [Utvärdering av säkerhets risker för virtuella datorer är nu allmänt tillgänglig](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Ändringar av åtkomst till virtuell dator (VM) för just-in-Time (JIT)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Anpassade rekommendationer har flyttats till en separat säkerhets kontroll](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Växla tillagd för att Visa rekommendationer i kontroller eller som en platt lista](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Utökad säkerhets kontroll "implementera säkerhets metod tips"](#expanded-security-control-implement-security-best-practices)
- [Anpassade principer med anpassade metadata är nu allmänt tillgängliga](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Analys funktioner för krasch dumpning migrera till fil lös attack identifiering](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Undervisnings regler för aviseringar (för hands version)

Den här nya funktionen (för närvarande i för hands version) hjälper till att minska aviserings utmattningen Använd regler för att automatiskt dölja aviseringar som är kända för att vara innocuous eller relaterade till normala aktiviteter i din organisation. På så sätt kan du fokusera på de mest relevanta hoten. 

Aviseringar som matchar dina aktiverade undertrycks regler kommer fortfarande att genereras, men deras tillstånd ställs in på stängs. Du kan se statusen i Azure Portal eller så får du åtkomst till dina Security Center säkerhets aviseringar.

Under trycknings regler definierar kriterierna för vilka aviseringar automatiskt ska avvisas. Normalt använder du en undertrycks regel för att:

- Ignorera aviseringar som du har identifierat som falska positiva identifieringar

- Ignorera aviseringar som utlöses för ofta för att vara användbara

Lär dig mer om att [Ignorera aviseringar från Azure Security Center skydd mot hot](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Utvärdering av säkerhets risker för virtuella datorer är nu allmänt tillgänglig

Security Center standard nivån innehåller nu en integrerad sårbarhets bedömning för virtuella datorer utan extra avgift. Det här tillägget drivs av Qualys men rapporterar sina resultat direkt tillbaka till Security Center. Du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center.

Den nya lösningen kan kontinuerligt genomsöka dina virtuella datorer för att hitta sårbarheter och visa resultaten i Security Center. 

Använd den nya säkerhets rekommendationen för att distribuera lösningen:

"Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys)"

Lär dig mer om [Security Center s integrerade sårbarhets bedömning för virtuella datorer](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Ändringar av åtkomst till virtuell dator (VM) för just-in-Time (JIT)

Security Center innehåller en valfri funktion för att skydda de virtuella datorernas hanterings portar. Detta ger ett försvar mot den vanligaste formen av brute force-attacker.

Den här uppdateringen ger följande ändringar i den här funktionen:

- Rekommendationen som aviserar dig om att aktivera JIT på en virtuell dator har bytt namn. Tidigare bör "just-in-Time-Network Access Control" tillämpas på virtuella datorer "det är nu:" hanterings portar för virtuella datorer bör skyddas med just-in-Time Network Access Control ".

- Rekommendationen utlöses endast om det finns öppna hanterings portar.

Lär dig mer om [funktionen för JIT-åtkomst](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Anpassade rekommendationer har flyttats till en separat säkerhets kontroll

En säkerhets kontroll som introducerades med den förbättrade säkra poängen var "implementera säkerhets metod tips". Alla anpassade rekommendationer som skapats för dina prenumerationer placerades automatiskt i denna kontroll. 

För att göra det lättare att hitta dina anpassade rekommendationer har vi flyttat dem till en särskild säkerhets kontroll, "anpassade rekommendationer". Den här kontrollen påverkar inte dina säkra poäng.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Växla tillagd för att Visa rekommendationer i kontroller eller som en platt lista

Säkerhets kontroller är logiska grupper av relaterade säkerhets rekommendationer. De återspeglar dina sårbara angrepps ytor. En kontroll är en uppsättning säkerhets rekommendationer med anvisningar som hjälper dig att implementera dessa rekommendationer.

Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

Som standard visas dina rekommendationer i säkerhets kontrollerna. Från den här uppdateringen kan du också visa dem som en lista. Om du vill visa dem som en enkel lista som sorteras efter hälso status för de berörda resurserna använder du den nya växla-gruppen efter-kontroller. Växlingen är ovanför listan i portalen.

Säkerhets kontrollerna – och den här växlingen är en del av den nya säkra poängen. Kom ihåg att skicka oss din feedback från portalen.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Gruppera efter-kontroller växla för rekommendationer":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Utökad säkerhets kontroll "implementera säkerhets metod tips" 

En säkerhets kontroll som introduceras med förbättrade säkra poäng är "implementera säkerhets metod tips". När en rekommendation är i den här kontrollen påverkar den inte de säkra poängen. 

Med den här uppdateringen har tre rekommendationer flyttats ut från de kontroller som de ursprungligen placerades i och i den här kontrollen med bästa praxis. Vi har vidtagit det här steget eftersom vi har fastställt att risken för dessa tre rekommendationer är lägre än vad som ursprungligen troddes.

Dessutom har två nya rekommendationer införts och lagts till i den här kontrollen.

De tre rekommendationer som flyttats är:

- **MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration** (ursprungligen i "Aktivera MFA"-kontrollen)
- **Externa konton med Läs behörighet bör tas bort från din prenumeration** (från början i kontrollen hantera åtkomst och behörigheter)
- **Högst 3 ägare bör anges för din prenumeration** (ursprungligen i "hantera åtkomst och behörigheter"-kontrollen)

De två nya rekommendationerna som läggs till i kontrollen är:

- **Gäst konfigurations tillägget bör installeras på virtuella Windows-datorer (för hands version)** – med [Azure policy gäst konfiguration](../governance/policy/concepts/guest-configuration.md) får du insyn i virtuella datorer för Server-och program inställningar (endast Windows).

- **Windows Defender sårbarhet Guard måste vara aktiverat på dina datorer (för hands version)** – Windows Defender sårbarhet guard utnyttjar Azure policy konfigurations agenten för gäst. I sårbarhets Guard finns fyra komponenter som är utformade för att låsa enheter mot en mängd olika angrepps vektorer och blockera beteenden som ofta används i angrepp mot skadlig kod och som gör det möjligt för företag att balansera sina säkerhets risker och produktivitets krav (endast Windows).

Lär dig mer om Windows Defender sårbarhet Guard i [skapa och distribuera en princip för sårbarhets skydd](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Lär dig mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Anpassade principer med anpassade metadata är nu allmänt tillgängliga

Anpassade principer är nu en del av Security Center rekommendationer, säkra poäng och kontroll panelen för regler för efterlevnad. Den här funktionen är nu allmänt tillgänglig och gör att du kan utöka din organisations täckning för säkerhets utvärdering i Security Center. 

Skapa ett anpassat initiativ i Azure policy, Lägg till principer till den och publicera det i Azure Security Center och visualisera det som rekommendationer.

Nu har vi även lagt till alternativet för att redigera metadata för anpassade rekommendationer. Alternativen för metadata omfattar allvarlighets grad, reparations steg, Hot information och mycket annat.  

Lär dig mer om [att förbättra dina anpassade rekommendationer med detaljerad information](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Analys funktioner för krasch dumpning migrera till fil lös attack identifiering 

Vi integrerar Windows Crash dump Analysis-identifieringen (CDA) i en [fil med filbaserad attack identifiering](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Med filbaserad attack identifierings analys får du förbättrade versioner av följande säkerhets aviseringar för Windows-datorer: kod inmatning identifierad, maskerad Windows-modul identifierad, shellcode identifierad och misstänkt kod segment identifierat.

Några av fördelarna med den här över gången:

- **Proaktivt identifiering** av skadlig kod – den CDA-metod som väntar på att krascher ska inträffa och sedan kör analys för att hitta skadliga artefakter. Genom att använda filbaserad attack identifiering får du proaktiv identifiering av minnes minnes hot medan de körs. 

- **Omfattande aviseringar** – säkerhets aviseringar från fil igenkännings identifiering inkluderar berikade objekt som inte är tillgängliga från CDA, till exempel information om aktiva nätverks anslutningar. 

- **Varnings agg regering** – när CDA har identifierat flera angrepps mönster i en enda kraschdump utlöses det flera säkerhets aviseringar. Vid fil lös angrepp kombineras alla identifierade angrepps mönster från samma process till en enda avisering, vilket tar bort behovet av att korrelera flera aviseringar.

- **Lägre krav på din Log Analytics-arbetsyta** – krasch dum par som innehåller potentiellt känsliga data kommer inte längre att överföras till Log Analytics-arbetsytan.






## <a name="april-2020"></a>April 2020

Uppdateringar i april inkluderar:
- [Dynamiska Compliance-paket är nu allmänt tillgängliga](#dynamic-compliance-packages-are-now-generally-available)
- [Identitets rekommendationer ingår nu i Azure Security Center kostnads fri nivå](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamiska Compliance-paket är nu allmänt tillgängliga

Instrument panelen för Azure Security Center reglering av efterlevnad innehåller nu **dynamiska paket för efterlevnad** (nu allmänt tillgängliga) för att spåra ytterligare bransch-och reglerings standarder.

Du kan lägga till dynamiska efterlevnadsprinciper i din prenumeration eller hanterings grupp på sidan Security Center säkerhets princip. När du har registrerat en standard eller benchmark visas standarden på instrument panelen för kontroll av efterlevnad med alla associerade efterlevnadsprinciper som har mappats som utvärderingar. En sammanfattnings rapport för någon av de standarder som har registrerats är tillgänglig för hämtning.

Nu kan du lägga till standarder som till exempel:

- **NIST SP 800-53 R4**
- **SWIFT CSP-CSCF – v2020**
- **Storbritannien, officiella och UK NHS**
- **Canada Federal PBMM**
- **Azure CIS-1.1.0 (ny)** (som är en mer fullständig representation av Azure CIS-1.1.0)

Dessutom har vi nyligen lagt till **Azures säkerhets benchmark**, de Microsoft-baserade Azure-/regionsspecifika rikt linjerna för säkerhet och efterlevnad av praxis som baseras på vanliga ramverk för efterlevnad. Ytterligare standarder kommer att stödjas på instrument panelen när de blir tillgängliga.  
 
Läs mer om hur [du anpassar uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md)regelefterlevnad.


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identitets rekommendationer ingår nu i Azure Security Center kostnads fri nivå

Säkerhets rekommendationer för identitet och åtkomst på den Azure Security Center kostnads fria nivån är nu allmänt tillgängliga. Detta är en del av arbetet för att göra CSPM-funktionerna (Cloud Security position Management) kostnads fritt. Fram till nu var de här rekommendationerna bara tillgängliga på standard pris nivån.

Exempel på identitets-och åtkomst rekommendationer är:

- "Multifaktorautentisering måste vara aktiverat på konton med ägar behörigheter för din prenumeration."
- "Högst tre ägare bör anges för din prenumeration."
- "Föråldrade konton bör tas bort från din prenumeration."

Om du har prenumerationer på den kostnads fria pris nivån kommer deras säkra resultat att påverkas av den här ändringen eftersom de aldrig utvärderades för identitets-och åtkomst säkerhet.

Lär dig mer om [identitets-och åtkomst rekommendationer](recommendations-reference.md#recs-identityandaccess).

Läs mer om [övervakning av identitet och åtkomst](security-center-identity-access.md).



## <a name="march-2020"></a>Mars 2020

Uppdateringar i mars inkluderar:

- [Arbets flödes automatisering är nu allmänt tillgänglig](#workflow-automation-is-now-generally-available)
- [Integrering av Azure Security Center med administrations Center för Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Skydd för Azure Kubernetes-tjänsten](#protection-for-azure-kubernetes-service)
- [Förbättrad just-in-Time-upplevelse](#improved-just-in-time-experience)
- [Två säkerhets rekommendationer för webb program som är föråldrade](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Arbets flödes automatisering är nu allmänt tillgänglig

Funktionen för automatisering av arbets flöden i Azure Security Center är nu allmänt tillgänglig. Använd den för att automatiskt utlösa Logic Apps om säkerhets aviseringar och rekommendationer. Dessutom är manuella utlösare tillgängliga för aviseringar och alla rekommendationer som har alternativet snabb korrigering tillgängligt.

Varje säkerhets program innehåller flera arbets flöden för incident svar. De här processerna kan omfatta att meddela relevanta intressenter, starta en ändrings hanterings process och tillämpa vissa åtgärder för reparation. Säkerhets experter rekommenderar att du automatiserar så många steg som beskrivs i de här procedurerna. Automation minskar kostnaderna och kan förbättra säkerheten genom att se till att process stegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

Mer information om automatiska och manuella Security Center-funktioner för att köra arbets flöden finns i [arbets flödes automatisering](workflow-automation.md).

Lär dig mer om att [skapa Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrering av Azure Security Center med administrations Center för Windows

Nu kan du flytta dina lokala Windows-servrar från Windows administrations Center direkt till Azure Security Center. Security Center blir sedan den enda rutan med glas för att Visa säkerhets information för alla dina Windows administrations Center-resurser, inklusive lokala servrar, virtuella datorer och ytterligare PaaS arbets belastningar.

När du har flyttat en server från Windows administrations Center till Azure Security Center kan du:

- Visa säkerhets aviseringar och rekommendationer i Security Center tillägget för Windows administrations Center.
- Visa säkerhets position och hämta ytterligare detaljerad information om dina hanterade Windows administrations Center-servrar i Security Center inom Azure Portal (eller via ett API).

Läs mer om [hur du integrerar Azure Security Center med administrations Center för Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Skydd för Azure Kubernetes-tjänsten

Azure Security Center utökar dess behållar säkerhetsfunktioner för att skydda Azure Kubernetes service (AKS).

Den populära plattforms Kubernetes med öppen källkod har antagits så mycket att den nu är bransch standard för behållar dirigering. Trots den här omfattande implementeringen finns det fortfarande brist på förståelse för hur du skyddar en Kubernetes-miljö. Att skydda angrepps ytorna i ett behållar program kräver expert kunskaper för att säkerställa att infrastrukturen konfigureras säkert och ständigt övervakas för potentiella hot.

Security Center försvaret omfattar:

- **Identifiering och synlighet** – kontinuerlig identifiering av hanterade AKS-instanser i prenumerationer som registrerats på Security Center.
- **Säkerhets rekommendationer** – användbara rekommendationer som hjälper dig att följa säkerhets metoder för AKS. Dessa rekommendationer ingår i dina säkra Poäng för att se till att de visas som en del av din organisations säkerhets position. Ett exempel på en AKS rekommendation som du kan se är "rollbaserad åtkomst kontroll ska användas för att begränsa åtkomsten till ett Kubernetes service-kluster".
- **Hot skydd** – genom kontinuerlig analys av din AKS-distribution, Security Center varnar dig om hot och skadlig aktivitet som har identifierats på värd-och AKS kluster nivå.

Lär dig mer om [Azure Kubernetes Services-integration med Security Center](defender-for-kubernetes-introduction.md).

Läs mer om [behållar säkerhetsfunktionerna i Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Förbättrad just-in-Time-upplevelse

Funktionerna, åtgärden och gränssnittet för Azure Security Center just-in-Time-verktyg som skyddar hanterings portarna har förbättrats på följande sätt: 

- **Motiverings fält** – när du begär åtkomst till en virtuell dator (VM) via sidan just-in-time i Azure Portal, är ett nytt valfritt fält tillgängligt för att ange en motivering för begäran. Information som anges i det här fältet kan spåras i aktivitets loggen. 
- **Automatisk rensning av redundanta regler för just-in-Time (JIT)** – när du uppdaterar en JIT-princip körs automatiskt ett rensnings verktyg för att kontrol lera giltigheten för hela ruleset. Verktyget söker efter avvikelser mellan regler i principen och reglerna i NSG. Om ett matchnings fel upptäcks i rensnings verktyget, bestäms orsaken och när det är säkert att göra det, tar bort inbyggda regler som inte behövs längre. Rengöringen tar aldrig bort regler som du har skapat. 

Lär dig mer om [funktionen för JIT-åtkomst](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Två säkerhets rekommendationer för webb program som är föråldrade

Två säkerhets rekommendationer relaterade till webb program är föråldrade: 

- Reglerna för webb program på IaaS NSG: er bör vara härdade.
    (Relaterad princip: NSG: er-reglerna för webb program på IaaS bör vara härdade)

- Åtkomst till App Services bör vara begränsad.
    (Relaterad princip: åtkomst till App Services ska vara begränsad [för hands version])

Dessa rekommendationer visas inte längre i Security Center listan över rekommendationer. De relaterade principerna kommer inte längre att ingå i initiativet med namnet "Security Center default".

Läs mer om [säkerhets rekommendationer](recommendations-reference.md).




## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Filbaserad attack identifiering för Linux (för hands version)

När angripare ökar angreppet med stealthier-metoder för att undvika identifiering kan Azure Security Center utöka identifieringen av fil lös attacker för Linux, förutom Windows. Filbaserade attacker utnyttjar sårbarheter i program vara, injicera skadliga nytto laster i ofarliga system processer och Dölj i minnet. Dessa tekniker:

- minimera eller eliminera spår av skadlig kod på disk
- minska risken för identifiering med diskbaserade genomsöknings lösningar med skadlig kod

För att räkna ut det här hotet Azure Security Center lanserad fil lös attack identifiering för Windows i oktober 2018 och har nu utökad avkänning av fil lös attacker på Linux också. 



## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Förbättrade säkra poäng (för hands version)

Nu finns en förbättrad version av den säkra poängen-funktionen i Azure Security Center tillgänglig i för hands versionen. I den här versionen är flera rekommendationer grupperade i säkerhets kontroller som bättre återspeglar dina sårbara attack ytor (till exempel begränsa åtkomst till hanterings portar).

Bekanta dig med de säkra Poäng ändringarna under förhands gransknings fasen och bestäm andra åtgärder som kan hjälpa dig att skydda din miljö ytterligare.

Läs mer om [förbättrade säkra poäng (för hands version)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Uppdateringarna i november omfattar:
 - [Skydd mot hot för Azure Key Vault i Nordamerika regioner (för hands version)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Skydd mot hot skydd för Azure Storage innehåller kontroll av ryktes rykte](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Arbets flödes automatisering med Logic Apps (för hands version)](#workflow-automation-with-logic-apps-preview)
 - [Snabb korrigering för Mass resurser som är allmänt tillgängliga](#quick-fix-for-bulk-resources-generally-available)
 - [Skanna behållar avbildningar för sårbarheter (för hands version)](#scan-container-images-for-vulnerabilities-preview)
 - [Ytterligare normer för regelefterlevnad (för hands version)](#additional-regulatory-compliance-standards-preview)
 - [Skydd mot hot för Azure Kubernetes service (för hands version)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Sårbarhets bedömning av virtuell dator (för hands version)](#virtual-machine-vulnerability-assessment-preview)
 - [Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (för hands version)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Stöd för anpassade principer (förhands granskning)](#support-for-custom-policies-preview)
 - [Utöka Azure Security Center täckning med plattform för community och partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Avancerad integrering med export av rekommendationer och aviseringar (för hands version)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Publicera på lokal-servrar för att Security Center från Windows administrations Center (för hands version)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Skydd mot hot för Azure Key Vault i Nordamerika regioner (för hands version)

Azure Key Vault är en viktig tjänst för att skydda data och förbättra prestanda för moln program genom att erbjuda möjligheten att centralt hantera nycklar, hemligheter, kryptografiska nycklar och principer i molnet. Eftersom Azure Key Vault lagrar känsliga och affärs kritiska data, krävs maximal säkerhet för nyckel valven och de data som lagras i dem.

Azure Security Center s support för skydd mot Azure Key Vault ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja nyckel valv. Det nya skydds lagret gör det möjligt för kunder att lösa sina nyckel valv utan att vara säkerhets expert eller hantera säkerhets övervaknings system. Funktionen finns i en allmänt tillgänglig för hands version i Nordamerika regioner.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Skydd mot hot skydd för Azure Storage innehåller kontroll av ryktes rykte

Skydd mot hot för Azure Storage erbjuder nya identifieringar som drivs av Microsoft Threat intelligence för att identifiera överbelastningar mot skadlig kod för att Azure Storage använda hash-ryktes analys och misstänkt åtkomst från en aktiv Tor-nod (en maskera proxy). Nu kan du Visa identifierad skadlig kod över lagrings konton med Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Arbets flödes automatisering med Logic Apps (för hands version)

Organisationer med centralt hanterad säkerhet och IT/åtgärder implementerar interna arbets flödes processer för att köra nödvändig åtgärd i organisationen när avvikelser upptäcks i sina miljöer. I många fall är dessa arbets flöden upprepade processer och automatisering kan effektivisera processer i organisationen avsevärt.

Idag introducerar vi en ny funktion i Security Center som gör det möjligt för kunder att skapa automatiserings konfigurationer som använder Azure Logic Apps och för att skapa principer som automatiskt aktiverar dem baserat på vissa ASC-resultat som rekommendationer eller aviseringar. Azure Logic app kan konfigureras för att utföra en anpassad åtgärd som stöds av den stora communityn med Logic app-kopplingar, eller använda en av mallarna som tillhandahålls av Security Center, till exempel att skicka ett e-postmeddelande eller öppna en ServiceNow™-biljett.

Mer information om automatiska och manuella Security Center-funktioner för att köra arbets flöden finns i [arbets flödes automatisering](workflow-automation.md).

Information om hur du skapar Logic Apps finns i [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Snabb korrigering för Mass resurser som är allmänt tillgängliga

Med de många uppgifter som en användare har fått som en del av säkra Poäng kan möjligheten att effektivt åtgärda problem i en stor flottan bli en utmaning.

För att förenkla reparationen av felkonfigurationer av säkerhet och för att snabbt kunna åtgärda rekommendationer för en mängd resurser och förbättra dina säkra poäng, använder du snabb korrigerings reparation.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparations åtgärd som konfigurerar inställningen för din räkning.

Snabb korrigering är allmänt tillgänglig idag-kunder som en del av sidan med Security Center rekommendationer.

Se vilka rekommendationer som har snabb korrigering aktive rad i [referens guiden för säkerhets rekommendationer](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skanna behållar avbildningar för sårbarheter (för hands version)

Azure Security Center kan nu skanna behållar avbildningar i Azure Container Registry för sårbarheter.

Avbildnings genomsökningen fungerar genom att parsa behållar avbildnings filen och sedan kontrol lera om det finns några kända sårbarheter (drivs av Qualys).

Själva sökningen utlöses automatiskt när nya behållar avbildningar skickas till Azure Container Registry. Identifierade sårbarheter som Security Center rekommendationer och ingår i Azures säkra poäng tillsammans med information om hur de kan korrigeras för att minska angrepps ytan.


### <a name="additional-regulatory-compliance-standards-preview"></a>Ytterligare normer för regelefterlevnad (för hands version)

Instrument panelen för övervakning av efterlevnad ger insikter om position som baseras på Security Center bedömningar. Instrument panelen visar hur din miljö uppfyller de kontroller och krav som anges av särskilda regler och bransch riktmärken och ger rekommendationer för hur du kan åtgärda dessa krav.

Instrument panelen för kontroll av efterlevnad har därför stöd för fyra inbyggda standarder: Azure CIS-1.1.0, PCI-DSS, ISO 27001 och SOC-TSP. Vi presenterar nu den offentliga för hands versionen av ytterligare standarder som stöds: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Kanadas federala PBMM och Storbritannien officiellt tillsammans med UK NHS. Vi publicerar också en uppdaterad version av Azure CIS-1.1.0, som täcker fler kontroller från standard och utökad utökning.

[Läs mer om hur du anpassar uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md)regelefterlevnad.


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Skydd mot hot för Azure Kubernetes service (för hands version)

Kubernetes blir snabbt den nya standarden för att distribuera och hantera program vara i molnet. Några få personer har omfattande erfarenhet av Kubernetes och många fokuserar på allmän teknik och administration och förbise säkerhets aspekten. Kubernetes-miljön måste konfigureras försiktigt för att vara säker, och se till att ingen behållare som fokuserar på angrepps ytan inte är öppen är öppen för angripare. Security Center utökar sitt stöd i container utrymmet till en av de snabbast växande tjänsterna i Azure – Azure Kubernetes service (AKS).

De nya funktionerna i den här offentliga för hands versionen omfattar:

- **Identifiering & synlighet** -kontinuerlig identifiering av hanterade AKS-instanser i Security Center registrerade prenumerationer.
- **Säkra Poäng rekommendationer** – åtgärds bara objekt som hjälper kunder att följa rekommenderade säkerhets metoder för AKS och öka sina säkra poäng. Rekommendationer inkluderar objekt som "rollbaserad åtkomst kontroll" ska användas för att begränsa åtkomsten till ett Kubernetes-tjänst kluster ".
- **Hot identifiering** – värd-och klusterbaserade analyser, till exempel "en privilegie rad behållare identifierad".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Sårbarhets bedömning av virtuell dator (för hands version)

Program som installeras på virtuella datorer kan ofta ha sårbarheter som kan leda till en överträdelse av den virtuella datorn. Vi presenterar att Security Center standard nivån innehåller inbyggd sårbarhets bedömning för virtuella datorer utan extra avgift. Med en sårbarhets bedömning, som drivs av Qualys i den offentliga för hands versionen, kan du kontinuerligt genomsöka alla installerade program på en virtuell dator för att hitta sårbara program och visa resultaten i Security Center portalens upplevelse. Security Center tar hand om alla distributions åtgärder så att inget extra arbete krävs från användaren. Vi planerar att tillhandahålla alternativ för sårbarhets bedömning för att stödja kundernas unika affärs behov.

[Lär dig mer om sårbarhets bedömningar för Azure-Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (för hands version)

Azure Security Center support för skydd mot hot och sårbarheter för SQL-databaser som körs på virtuella IaaS-datorer finns nu som för hands version.

[Sårbarhetsbedömning](../azure-sql/database/sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger insyn i din säkerhets position som en del av Azures säkra poäng och innehåller stegen för att lösa säkerhets problem och förbättra din databas Fortifications.

[Avancerat skydd](../azure-sql/database/threat-detection-overview.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL Server. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och tillhandahåller åtgärds säkerhets aviseringar om avvikande databas åtkomst mönster. De här aviseringarna ger information om misstänkta aktiviteter och rekommenderade åtgärder för att undersöka och minimera hotet.


### <a name="support-for-custom-policies-preview"></a>Stöd för anpassade principer (förhands granskning)

Azure Security Center stöder nu anpassade principer (i för hands versionen).

Våra kunder har kunnat utöka sin nuvarande skydds utvärderings omfattning i Security Center med sina egna säkerhets utvärderingar baserade på principer som de skapar i Azure Policy. Det är nu möjligt med stöd för anpassade principer.

Dessa nya principer kommer att ingå i Security Center rekommendationer, säkra poäng och instrument panelen för regler för efterlevnad. Med stöd för anpassade principer kan du nu skapa ett anpassat initiativ i Azure Policy och sedan lägga till det som en princip i Security Center och visualisera det som en rekommendation.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Utöka Azure Security Center täckning med plattform för community och partner

Använd Security Center för att få rekommendationer som inte bara kommer från Microsoft utan även från befintliga lösningar från partners som Check Point, Tenable och CyberArk med många fler integreringar.  Security Center det enkla onboarding-flödet kan ansluta dina befintliga lösningar till Security Center, så att du kan visa dina position-rekommendationer på en enda plats, köra enhetliga rapporter och utnyttja alla Security Centers funktioner mot både inbyggda och partner rekommendationer. Du kan också exportera Security Center rekommendationer till partner produkter.

[Läs mer om Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Avancerad integrering med export av rekommendationer och aviseringar (för hands version)

För att aktivera scenarier på företags nivå ovanpå Security Center, är det nu möjligt att använda Security Center aviseringar och rekommendationer på ytterligare platser förutom Azure Portal eller API. Dessa kan exporteras direkt till en Event Hub och till Log Analytics arbets ytor. Här följer några arbets flöden som du kan skapa runt dessa nya funktioner:

- Med exportera till Log Analytics arbets yta kan du skapa anpassade instrument paneler med Power BI.
- Med exportera till Event Hub kan du exportera Security Center aviseringar och rekommendationer till Siem från tredje part, till en lösning i real tid eller Azure Datautforskaren.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Publicera på lokal-servrar för att Security Center från Windows administrations Center (för hands version)

Windows administrations Center är en hanterings Portal för Windows-servrar som inte distribueras i Azure och erbjuder dem flera hanterings funktioner för Azure, till exempel säkerhets kopierings-och system uppdateringar. Vi har nyligen lagt till en möjlighet att publicera dessa icke-Azure-servrar som ska skyddas av ASC direkt från Windows Admin Center-upplevelsen.

Med den här nya upplevelsen kommer användare att bli medlem i en WAC-Server för att Azure Security Center och kunna visa sina säkerhets aviseringar och rekommendationer direkt i Windows administrations Center.


## <a name="september-2019"></a>September 2019

Uppdateringar i september omfattar:

 - [Hantera regler med anpassningsbara program kontroller förbättringar](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrol lera säkerhets rekommendationer för behållare med hjälp av Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Hantera regler med anpassningsbara program kontroller förbättringar

Erfarenheten av att hantera regler för virtuella datorer med anpassningsbara program kontroller har förbättrats. Azure Security Center adaptiva program kontroller hjälper dig att styra vilka program som kan köras på dina virtuella datorer. Förutom en allmän förbättring av regel hanteringen gör en ny förmån att du kan styra vilka filtyper som ska skyddas när du lägger till en ny regel.

[Läs mer om anpassningsbara program kontroller](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrol lera säkerhets rekommendationer för behållare med hjälp av Azure Policy

Azure Security Center rekommendationen att åtgärda sårbarheter i behållar säkerhet kan nu aktive ras eller inaktive ras via Azure Policy.

Om du vill visa dina aktiverade säkerhets principer från Security Center öppnar du sidan säkerhets princip.


## <a name="august-2019"></a>Augusti 2019

Uppdateringarna i augusti inkluderar:

 - [Just-in-Time (JIT) VM-åtkomst för Azure-brandvägg](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Reparation med enkel klickning för att öka din säkerhets position (för hands version)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Hantering av flera klienter](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-in-Time (JIT) VM-åtkomst för Azure-brandvägg 

Just-in-Time (JIT) VM-åtkomst för Azure Firewall är nu allmänt tillgänglig. Använd den för att skydda dina Azure Firewall-skyddade miljöer förutom dina NSG-skyddade miljöer.

JIT VM-åtkomst minskar exponeringen för nätverks volym attacker genom att tillhandahålla kontrollerad åtkomst till virtuella datorer endast vid behov, med hjälp av NSG-och Azure brand Väggs regler.

När du aktiverar JIT för dina virtuella datorer skapar du en princip som avgör vilka portar som ska skyddas, hur länge portarna ska vara öppna och godkända IP-adresser från var dessa portar kan nås. Den här principen hjälper dig att hålla kontrollen över vad användarna kan göra när de begär åtkomst.

Begär Anden loggas i aktivitets loggen i Azure så att du enkelt kan övervaka och granska åtkomst. Sidan just-in-Time hjälper dig också att snabbt identifiera befintliga virtuella datorer med JIT aktiverat och virtuella datorer där JIT rekommenderas.

[Läs mer om Azure-brandväggen](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Reparation med enkel klickning för att öka din säkerhets position (för hands version)

Säkra poäng är ett verktyg som hjälper dig att utvärdera din position för arbets belastnings säkerhet. Den granskar dina säkerhets rekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer som ska utföras först. Detta hjälper dig att hitta de säkerhets risker som är mest allvarliga för att prioritera undersökningen.

Vi har lagt till en ny funktion som gör det möjligt att reparera en rekommendation om en samling resurser i ett enda klick för att förenkla reparationen av felkonfigurationer av säkerhet och hjälpa dig att snabbt förbättra dina säkra poäng.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparations åtgärd som konfigurerar inställningen för din räkning.

Se vilka rekommendationer som har snabb korrigering aktive rad i [referens guiden för säkerhets rekommendationer](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Hantering av flera klienter

Security Center stöder nu scenarier för hantering av flera innehavare som en del av Azure-Lighthouse. På så sätt kan du få insyn och hantera säkerhets position för flera klienter i Security Center. 

[Läs mer om hanterings upplevelser mellan flera innehavare](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Uppdateringar av nätverks rekommendationer

Azure Security Center (ASC) har lanserat nya nätverks rekommendationer och förbättrade några befintliga. Nu kan du med hjälp av Security Center se till att det är ännu större nätverks skyddet för dina resurser. 

[Lär dig mer om nätverks rekommendationer](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Anpassad nätverks härdning – allmänt tillgänglig

En av de största angrepps ytorna för arbets belastningar som körs i det offentliga molnet är anslutningar till och från det offentliga Internet. Våra kunder tycker att det är svårt att veta vilka regler för nätverks säkerhets grupper (NSG) som ska vara på plats för att se till att Azure-arbetsbelastningar endast är tillgängliga för de käll intervall som krävs. Med den här funktionen kan Security Center lära sig nätverks trafik-och anslutnings mönster för Azure-arbetsbelastningar och ger rekommendationer för NSG-regler för virtuella datorer som är riktade mot Internet. Detta hjälper våra kunder att bättre konfigurera sina principer för nätverks åtkomst och begränsa deras exponering för attacker. 

[Lär dig mer om anpassad nätverks härdning](security-center-adaptive-network-hardening.md).