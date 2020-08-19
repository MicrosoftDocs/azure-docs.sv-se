---
title: Viktig information för Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: dbd040bf7caf3dbe9ed9820bf189bc1f74475c09
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586902"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Azure-säkerhet ligger i aktiv utveckling och tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här sidan dig information om:

- Nya funktioner
- Felkorrigeringar
- Föråldrade funktioner

Den här sidan uppdateras regelbundet, så du kan uppdatera den ofta. Om du söker efter objekt som är äldre än sex månader hittar du dem i [arkivet för vad som är nytt i Azure Security Center](release-notes-archive.md).


## <a name="august-2020"></a>Augusti 2020

Uppdateringarna i augusti inkluderar:

- [Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Rekommendation för tjänstens huvud namn lades till](#service-principals-recommendation-added)
- [Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet

Security Center till gångs lagret är ett sätt att Visa säkerhets position för de resurser som du har anslutit till Security Center.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen. När en resurs har utestående rekommendationer visas de i inventeringen.

Du kan använda vyn och dess filter för att utforska dina säkerhets position data och vidta ytterligare åtgärder baserat på dina resultat.

Läs mer om [till gångs inventering](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)

Security Center har lagt till fullständigt stöd för [säkerhets standarder](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), Microsofts kostnads fria identitets skydds skydd.

Säkerhets standarder tillhandahåller förkonfigurerade säkerhets inställningar för identitet för att skydda din organisation från vanliga problem med identitets angrepp. Säkerhets standarder skyddar redan över 5 000 000 klienter totalt. 50 000-klienter skyddas också av Security Center.

Security Center har nu en säkerhets rekommendation när den identifierar en Azure-prenumeration utan säkerhets inställningar som är aktiverade. Fram till nu rekommenderar Security Center att du aktiverar Multi-Factor Authentication med villkorlig åtkomst, som är en del av Azure Active Directory (AD) Premium-licensen. För kunder som använder Azure AD kostnads fri rekommenderar vi nu att du aktiverar säkerhets inställningar. 

Vårt mål är att uppmuntra fler kunder att skydda sina moln miljöer med MFA och minimera en av de högsta riskerna som också är mest påverkan av sina [säkra Poäng](https://docs.microsoft.com/azure/security-center/secure-score-security-controls).

Läs mer om [säkerhets inställningar](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Rekommendation för tjänstens huvud namn lades till

En ny rekommendation har lagts till för att rekommendera att Security Center kunder som använder hanterings certifikat för att hantera sina prenumerationer växlar till tjänstens huvud namn.

Rekommendationen är att **tjänstens huvud namn ska användas för att skydda dina prenumerationer i stället för hanterings certifikat** som rekommenderar att du använder tjänstens huvud namn eller Azure Resource Manager för att hantera dina prenumerationer på ett säkert sätt. 

Läs mer om [program-och tjänst huvud objekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer

Security Center granskar de virtuella datorerna för att identifiera om de använder en lösning för sårbarhets bedömning. Om ingen lösning för sårbarhets bedömning hittas ger Security Center en rekommendation för att förenkla distributionen.

När det finns sårbarheter ger Security Center en rekommendation som sammanfattar resultaten för att undersöka och åtgärda vid behov.

För att säkerställa en konsekvent upplevelse för alla användare, oavsett vilken skanner typ de använder, har vi förenat fyra rekommendationer i följande två:

|Enhetlig rekommendation|Ändra beskrivning|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**|Ersätter följande två rekommendationer:<br> **•** Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys (nu föråldrad) (ingår i standard-nivån)<br> **•** Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer (nu föråldrade) (standard-och kostnads fria nivåer)|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**|Ersätter följande två rekommendationer:<br>**•** Åtgärda säkerhets problem som finns på dina virtuella datorer (drivs av Qualys) (nu föråldrade)<br>**•** Sårbarheter bör åtgärdas av en lösning för sårbarhets bedömning (nu föråldrad)|
|||

Nu ska du använda samma rekommendation för att distribuera Security Centers tillägg för sårbarhets bedömning eller en privat licensierad lösning ("BYOL") från en partner, till exempel Qualys eller Rapid7.

När säkerhets risker upptäcks och rapporteras till Security Center får du dessutom en enda rekommendation som meddelar dig om avgöranden, oavsett vilken lösning för sårbarhets bedömning som identifierade dem.

#### <a name="updating-dependencies"></a>Uppdaterar beroenden

Om du har skript, frågor eller automatiseringar som refererar till föregående rekommendationer eller princip nycklar/namn, använder du tabellerna nedan för att uppdatera referenserna:

##### <a name="before-august-2020"></a>Före augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys)**<br>Nyckel: 550e890b-e652-4D22-8274-60b3bdb24c63|Inbyggd|
|**Åtgärda sårbarheter som finns på dina virtuella datorer (drivs av Qualys)**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggd|
|**Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer**<br>Nyckel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Nyckel: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Policy|Omfång|
|----|:----|
|**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Inbyggd|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Princip-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Från augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**<br>Nyckel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Inbyggda + BYOL|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggda + BYOL|
||||

|Policy|Omfång|
|----|:----|
|[**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Inbyggda + BYOL|
||||



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

Även om du nu kan distribuera det integrerade tillägget för sårbarhets bedömning (drivs av Qualys) på många fler datorer, är stödet bara tillgängligt om du använder ett operativ system som anges i [distribuera den inbyggda säkerhets luckan för Qualys](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner).

Lär dig mer om den [integrerade säkerhets genomsökningen för virtuella datorer (endast standard-nivå)](built-in-vulnerability-assessment.md).

Lär dig mer om att använda en egen privat licensierad lösning för sårbarhets bedömning från Qualys eller Rapid7 i [distribuera en lösning för genomsökning av partner problem](partner-vulnerability-assessment.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Skydd mot Azure Storage utökas till att omfatta Azure Files och Azure Data Lake Storage Gen2 (förhands granskning)

Skydd mot hot för Azure Storage identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Security Center visar aviseringar när du upptäcker försök att komma åt eller utnyttja dina lagrings konton. 

Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar. 

Läs mer om [skydd mot hot för Azure Storage](threat-protection.md#threat-protection-for-azure-storage-).




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

De här nya rekommendationerna hör till säkerhets kontrollen **Aktivera avancerat skydd** .

Rekommendationerna omfattar även snabb korrigerings funktionen. 

> [!IMPORTANT]
> Att reparera någon av dessa rekommendationer ger kostnader för att skydda relevanta resurser. Dessa avgifter börjar omedelbart om du har relaterade resurser i den aktuella prenumerationen. Eller i framtiden, om du lägger till dem senare.
> 
> Om du till exempel inte har några Azure Kubernetes service-kluster i din prenumeration och du aktiverar hot skyddet debiteras inga avgifter. Om du i framtiden lägger till ett kluster i samma prenumeration, kommer det automatiskt att skyddas och kostnader kommer att börja vid den tidpunkten.

Läs mer om var och en av dessa på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).

Läs mer om [skydd av hot i Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Förbättringar av behållar säkerhet – snabbare register genomsökning och uppdaterad dokumentation

Som en del av de kontinuerliga investeringarna i behållar säkerhets domänen är vi glada att dela en betydande prestanda förbättring i Security Center dynamiska genomsökningar av behållar avbildningar som lagras i Azure Container Registry. Genomsökningar är nu vanligt vis slutförda på ungefär två minuter. I vissa fall kan det ta upp till 15 minuter.

För att förbättra skärpan och vägledningen avseende Azure Security Center behållar säkerhetsfunktioner har vi också uppdaterat dokumentations sidorna för behållar säkerhet. 

Läs mer om Security Center behållar säkerhet i följande artiklar:

- [Översikt över Security Center behållar säkerhetsfunktioner](https://docs.microsoft.com/azure/security-center/container-security)
- [Information om integreringen med Azure Container Registry](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Information om integrering med Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Anvisningar – söka igenom dina register och torka Docker-värdarna](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-värdar](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Säkerhets rekommendationer för behållare](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Anpassningsbara program kontroller uppdaterade med en ny rekommendation och stöd för jokertecken i Sök vägs regler

Funktionen för anpassningsbara program kontroller har tagit emot två viktiga uppdateringar:

* En ny rekommendation identifierar potentiellt legitimt beteende som inte tidigare har tillåtits. Den nya rekommendationen, **tillåten-regler i din anpassningsbar program kontroll princip ska uppdateras**, och du uppmanas att lägga till nya regler i den befintliga principen för att minska antalet falska positiva identifieringar i adaptiva program kontroller fel aviseringar.

* Sök vägs regler stöder nu jokertecken. Från den här uppdateringen kan du konfigurera tillåtna Sök vägs regler med hjälp av jokertecken. Det finns två scenarier som stöds:

    * Använda jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermappar

    * Använd ett jokertecken i mitten av en sökväg för att aktivera ett känt körbart namn med ett ändrat mappnamn (t. ex. personliga användarmappar med en känd körbar fil, automatiskt genererade mappnamn osv.).


[Läs mer om anpassningsbara program kontroller](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sex principer för avancerad data säkerhet i SQL är inaktuell

Sex principer relaterade till avancerad data säkerhet för SQL-datorer är föråldrade:

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server

Läs mer om [inbyggda principer](security-center-policy-definitions.md).





## <a name="june-2020"></a>Juni 2020

Uppdateringarna i juni inkluderar:
- [Secure score-API (för hands version)](#secure-score-api-preview)
- [Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokal) (för hands version)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nya principer för att aktivera hot skydd och avancerad data säkerhet](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure score-API (för hands version)

Nu kan du komma åt dina poäng via [Secure score-API: t](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (för närvarande i för hands version). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda **Secure Scores** -API: et för att hämta poängen för en speciell prenumeration. Dessutom kan du använda API: et för **säker Poäng** för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

Exempel på externa verktyg som möjliggörs med Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokal) (för hands version)

Azure Security Center avancerade data säkerhet för SQL-datorer skyddar nu SQL-servrar som finns i Azure, i andra moln miljöer och till och med lokala datorer. Detta utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullt stödja hybrid miljöer.

Avancerad data säkerhet ger sårbarhets bedömning och Avancerat skydd för dina SQL-datorer oavsett var de befinner sig.

Installations programmet omfattar två steg:

1. Distribuera Log Analytics agenten till SQL Servers värddator för att tillhandahålla anslutningen till Azure-kontot.

1. Aktivera det valfria paketet på Security Center pris-och inställnings sida.

Lär dig mer om [Avancerad data säkerhet för SQL-datorer](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)

Två nya rekommendationer har lagts till för att hjälpa till att distribuera [Log Analytics agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) till dina Azure Arc-datorer och se till att de skyddas av Azure Security Center:

- **Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)**
- **Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)**

De här nya rekommendationerna visas i samma fyra säkerhets kontroller som den befintliga (relaterade) rekommendationen, **övervaknings agenten ska installeras på datorerna**: åtgärda säkerhetskonfigurationer, tillämpa anpassningsbar program kontroll, tillämpa system uppdateringar och Aktivera Endpoint Protection.

Rekommendationerna innehåller också snabb korrigerings funktionen som hjälper dig att påskynda distributions processen. 

Läs mer om de här två nya rekommendationerna i tabellen med [rekommendationer för beräkning och appar](recommendations-reference.md#recs-computeapp) .

Läs mer om hur Azure Security Center använder agenten i [Vad är Log Analytics agenten?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Läs mer om [tillägg för Azure Arc-datorer](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Om du vill distribuera dina automatiserings konfigurationer i organisationen använder du de här inbyggda "DeployIfdNotExist" Azure-principerna för att skapa och konfigurera [kontinuerliga](continuous-export.md) metoder för export och [automatisering av arbets flöde](workflow-automation.md) :

Du hittar principerna i Azure policy:


|Mål  |Policy  |Princip-ID  |
|---------|---------|---------|
|Kontinuerlig export till händelsehubben|[Distribuera export till Händelsehubben för Azure Security Center aviseringar och rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Löpande export till Log Analytics arbets yta|[Distribuera export till Log Analytics-arbetsyta för Azure Security Center aviseringar och rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Arbets flödes automatisering för säkerhets aviseringar|[Distribuera arbets flödes automatisering för Azure Security Center aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbets flödes automatisering för säkerhets rekommendationer|[Distribuera arbets flödes automatisering för Azure Security Center rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Kom igång med [automatiserings mallar för arbets flöden](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Lär dig mer om att använda de två export principerna för att [kontinuerligt exportera Azure Security Center aviseringar och rekommendationer via policy](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade

Säkerhets kontrollen "implementera rekommenderade säkerhets metoder" innehåller nu följande nya rekommendation:

- **Virtuella datorer som inte är baserade på Internet bör skyddas med nätverks säkerhets grupper**

En befintlig rekommendation, **virtuella datorer som riktas mot Internet bör skyddas med nätverks säkerhets grupper**, skiljer sig inte mellan Internet-riktade och icke-Internetbaserade virtuella datorer. För båda, genererade en rekommendation med hög allvarlighets grad om en virtuell dator inte var tilldelad till en nätverks säkerhets grupp. Den nya rekommendationen separerar datorer som inte är på Internet för att minska antalet falska positiva identifieringar och undvika onödiga aviseringar med hög allvarlighets grad.

Läs mer i tabellen med [nätverks rekommendationer](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nya principer för att aktivera hot skydd och avancerad data säkerhet

De nya principerna nedan lades till i ASC-standardinitiativet och är utformade för att hjälpa till med att aktivera skydd av hot eller avancerad data säkerhet för relevanta resurs typer.

Du hittar principerna i Azure policy:


| Policy                                                                                                                                                                                                                                                                | Princip-ID                            |
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

Läs mer om [skydd av hot i Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).





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

Lär dig mer om [Security Center s integrerade sårbarhets bedömning för virtuella datorer](built-in-vulnerability-assessment.md).



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

!["Gruppera efter kontroller"-växla för rekommendationer](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Utökad säkerhets kontroll "implementera säkerhets metod tips" 

En säkerhets kontroll som introduceras med förbättrade säkra poäng är "implementera säkerhets metod tips". När en rekommendation är i den här kontrollen påverkar den inte de säkra poängen. 

Med den här uppdateringen har tre rekommendationer flyttats ut från de kontroller som de ursprungligen placerades i och i den här kontrollen med bästa praxis. Vi har vidtagit det här steget eftersom vi har fastställt att risken för dessa tre rekommendationer är lägre än vad som ursprungligen troddes.

Dessutom har två nya rekommendationer införts och lagts till i den här kontrollen.

De tre rekommendationer som flyttats är:

- **MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration** (ursprungligen i "Aktivera MFA"-kontrollen)
- **Externa konton med Läs behörighet bör tas bort från din prenumeration** (från början i kontrollen hantera åtkomst och behörigheter)
- **Högst 3 ägare bör anges för din prenumeration** (ursprungligen i "hantera åtkomst och behörigheter"-kontrollen)

De två nya rekommendationerna som läggs till i kontrollen är:

- **Gäst konfigurations tillägget bör installeras på virtuella Windows-datorer (för hands version)** – med [Azure policy gäst konfiguration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) får du insyn i virtuella datorer för Server-och program inställningar (endast Windows).

- **Windows Defender sårbarhet Guard måste vara aktiverat på dina datorer (för hands version)** – Windows Defender sårbarhet guard utnyttjar Azure policy konfigurations agenten för gäst. I sårbarhets Guard finns fyra komponenter som är utformade för att låsa enheter mot en mängd olika angrepps vektorer och blockera beteenden som ofta används i angrepp mot skadlig kod och som gör det möjligt för företag att balansera sina säkerhets risker och produktivitets krav (endast Windows).

Lär dig mer om Windows Defender sårbarhet Guard i [skapa och distribuera en princip för sårbarhets skydd](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Lär dig mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Anpassade principer med anpassade metadata är nu allmänt tillgängliga

Anpassade principer är nu en del av Security Center rekommendationer, säkra poäng och kontroll panelen för regler för efterlevnad. Den här funktionen är nu allmänt tillgänglig och gör att du kan utöka din organisations täckning för säkerhets utvärdering i Security Center. 

Skapa ett anpassat initiativ i Azure policy, Lägg till principer till den och publicera det i Azure Security Center och visualisera det som rekommendationer.

Nu har vi även lagt till alternativet för att redigera metadata för anpassade rekommendationer. Alternativen för metadata omfattar allvarlighets grad, reparations steg, Hot information och mycket annat.  

Lär dig mer om [att förbättra dina anpassade rekommendationer med detaljerad information](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Analys funktioner för krasch dumpning migrera till fil lös attack identifiering 

Vi integrerar Windows Crash dump Analysis-identifieringen (CDA) i en [fil med filbaserad attack identifiering](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). Med filbaserad attack identifierings analys får du förbättrade versioner av följande säkerhets aviseringar för Windows-datorer: kod inmatning identifierad, maskerad Windows-modul identifierad, shellcode identifierad och misstänkt kod segment identifierat.

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

Lär dig mer om [identitets-och åtkomst rekommendationer](recommendations-reference.md#recs-identity).

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

Lär dig mer om att [skapa Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


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

Lär dig mer om [Azure Kubernetes Services-integration med Security Center](azure-kubernetes-service-integration.md).

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

