---
title: Guide för Security Center planering och åtgärder
description: I det här avsnittet får du hjälp med att planera inför införandet av Azure Security Center och med vad du bör tänka på i det dagliga arbetet.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a8b285ca0b8d0dae88bfe8dd19ac7e1bc8372e32
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099207"
---
# <a name="planning-and-operations-guide"></a>Planerings- och drifthandbok
Den här guiden är till för IT-experter, IT-arkitekter, informations säkerhets analytiker och moln administratörer som planerar att använda Azure Security Center.


## <a name="planning-guide"></a>Planeringsguide
Den här guiden beskriver uppgifter som du kan följa för att optimera din användning av Security Center baserat på din organisations säkerhets krav och moln hanterings modell. För att få ut mesta möjliga av Security Center är det viktigt att veta hur olika medarbetare och avdelningar i organisationen kommer att använda tjänsten så att kraven på säkerhet vid utvecklingsarbete, drift, övervakning, styrning och incidenthantering uppfylls. Tänk på följande när du planerar integreringen av Security Center:

* Säkerhetsroller och åtkomstkontroll
* Säkerhetsprinciper och säkerhetsrekommendationer
* Datainsamling och lagring
* Publicera icke-Azure-resurser
* Fortlöpande säkerhetsövervakning
* Incidenthantering

I nästa avsnitt lär du dig hur du planerar för vart och ett av dessa områden och hur du tillämpar rekommendationerna baserat på dina behov.


> [!NOTE]
> På vår sida med [vanliga frågor och svar om Azure Security Center](faq-general.md) finns en lista med ofta ställda frågor som kan vara bra att läsa i planerings- och utformningsfasen.

## <a name="security-roles-and-access-controls"></a>Säkerhetsroller och åtkomstkontroll
Beroende på hur stor din organisation är och hur den är uppbyggd kan olika medarbetare och avdelningar använda Security Center för att utföra olika säkerhetsrelaterade arbetsuppgifter. Följande diagram innehåller exempel på fiktiva personer och deras olika roller och ansvarsområden:

![Roller](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Med Security Center kan dessa medarbetare effektivt sköta sina respektive arbetsuppgifter. Exempel:

**Jens (arbetsbelastningsägare)**

* Hanterar en arbetsbelastning i molnet och relaterade resurser.
* Ansvarig för att implementera och underhålla säkerheten i enlighet med företagets säkerhetspolicy.

**Elisabeth (IT-chef)**

* Ansvarig för alla säkerhetsaspekter på företaget.
* Behöver ha en bra överblick över företagets säkerhet i alla arbetsbelastningar i molnet.
* Måste bli informerad om större attacker och risker.

**Daniel (IT-säkerhetsansvarig)**

* Definierar företagets säkerhetsprinciper för att se till att rätt skyddsåtgärder finns på plats.
* Övervakar principefterlevnaden.
* Genererar rapporter till chefer eller granskare.

**Selma (säkerhetsmedarbetare)**

* Övervakar och agerar på säkerhetsvarningar dygnet runt alla dagar i veckan.
* Eskalerar till molnansvarig eller IT-säkerhetsanalytiker.

**Sami (säkerhetsanalytiker)**

* Undersöker attacker.
* Arbetar med företagets molnansvarige för att åtgärda problemet

Security Center använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), vilket ger [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure. När en användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Detta betyder att användaren tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resursgrupp som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- **Security-läsare**: en användare som tillhör den här rollen kan bara visa Security Center-konfigurationer, vilket innehåller rekommendationer, aviseringar, principer och hälsa, men kan inte ändra.
- **Security-admin**: samma som security-läsare, men kan också uppdatera säkerhetsprinciper, stänga rekommendationer och aviseringar.

Security Center-rollerna som beskrivs ovan har inte åtkomst till andra delar av Azure, till exempel lagring, webb och mobil eller IoT (sakernas internet).

Med hjälp av personer som förklaras i föregående diagram krävs följande Azure RBAC:

**Jens (arbetsbelastningsägare)**

* Resurs grupp ägare/deltagare

**Elisabeth (IT-chef)**

* Prenumerationens ägare/deltagare eller säkerhets administratör

**Daniel (IT-säkerhetsansvarig)**

* Prenumerationens ägare/deltagare eller säkerhets administratör

**Selma (säkerhetsmedarbetare)**

* Läsare i prenumeration eller Security-läsare för att kunna se aviseringar
* Prenumerations ägare/deltagare eller säkerhets administratör som krävs för att ignorera aviseringar

**Sami (säkerhetsanalytiker)**

* Läsare i prenumeration för att kunna se aviseringar
* Prenumerationens ägare/deltagare krävs för att ignorera aviseringar
* Åtkomst till arbetsytan kan krävas

Tänk även på följande:

* Endast ägare och deltagare i prenumerationer samt Security-administratörer kan ändra säkerhetsprinciper.
* Endast ägare och deltagare i prenumerationer och resursgrupper kan tillämpa säkerhetsrekommendationer på en resurs.

När du planerar åtkomst kontroll med Azure RBAC för Security Center bör du se till att förstå vilka i din organisation som kommer att använda Security Center. Dessutom vilka typer av uppgifter som de ska utföra och sedan konfigurera Azure RBAC i enlighet med detta.

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. De användare som till exempel endast behöver se information om säkerhetsstatusen på resurser men inte vidta några åtgärder, som att tillämpa rekommendationer eller ändra principer, bör få rollen som läsare.
>
>

## <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center kan du definiera principer för dina Azure-prenumerationer, som kan skräddarsys efter arbetsbelastningstyp eller datakänslighet.

Security Center-principer innehåller följande komponenter:
- [Datainsamling](security-center-enable-data-collection.md): agentetablering och datainsamlingsinställningar.
- [Säkerhets princip](tutorial-security-policy.md): en [Azure policy](../governance/policy/overview.md) som avgör vilka kontroller som övervakas och rekommenderas av Security Center, eller Använd Azure policy för att skapa nya definitioner, definiera ytterligare principer och tilldela principer över hanterings grupper.
- [E-postmeddelanden](security-center-provide-security-contact-details.md): säkerhetskontakter och inställningar för meddelanden.
- [Pris nivå](security-center-pricing.md): med eller utan Azure Defender som avgör vilka Security Center funktioner som är tillgängliga för resurser i området (kan anges för prenumerationer och arbets ytor eller resurs grupper med hjälp av API: et).

> [!NOTE]
> Om du anger en säkerhetskontakt säkerställer du att Azure kan nå rätt person i organisationen om en säkerhetsincident inträffar. Mer information om hur du aktiverar den här rekommendationen finns i [Lägga till kontaktuppgifter i Azure Security Center](security-center-provide-security-contact-details.md).

### <a name="security-policies-definitions-and-recommendations"></a>Säkerhetsprincipdefinitioner och rekommendationer
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principen i Security Center eller använda Azure Policy för att skapa nya definitioner, definiera ytterligare principer och tilldela principer i hanteringsgrupper (som kan motsvara hela organisationen, en affärsenhet osv.) och övervaka efterlevnad av dessa principer i dessa omfattningar.

Innan du börjar konfigurera säkerhetsprinciper går du igenom de olika [säkerhetsrekommendationerna](security-center-recommendations.md) och avgör om dessa principer passar dina olika prenumerationer och resursgrupper. Det är också viktigt att förstå vilka åtgärder som vidtas för att hantera säkerhetsrekommendationer och vem i din organisation är ansvarig för att övervaka nya rekommendationer och vidta nödvändiga åtgärder.

## <a name="data-collection-and-storage"></a>Datainsamling och datalagring
Azure Security Center använder Log Analytics agent – det här är samma agent som används av Azure Monitor tjänsten – för att samla in säkerhets data från dina virtuella datorer. [Data som samlas in](security-center-enable-data-collection.md) från den här agenten kommer att lagras i dina Log Analytics-arbetsytor.

### <a name="agent"></a>Agent

När automatisk etablering är aktiverat i säkerhets principen installeras Log Analytics agent (för [Windows](../azure-monitor/agents/agent-windows.md) eller [Linux](../azure-monitor/vm/quick-collect-linux-computer.md)) på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Om den virtuella datorn eller datorn redan har Log Analytics-agenten installerad använder Azure Security Center den aktuella installerade agenten. Agentens process är utformad för att vara icke-invasiv och har mycket minimal påverkan på VM-prestanda.

Log Analytics agent för Windows kräver TCP-port 443. Se [Felsökningsartikeln](security-center-troubleshooting-guide.md) för ytterligare information.

Om du vid ett senare tillfälle vill inaktivera datainsamlingen kan du göra det i säkerhetsprincipen. Men eftersom Log Analytics-agenten kan användas av andra tjänster för hantering och övervakning av Azure kommer agenten inte att avinstalleras automatiskt när du stänger av data insamling i Security Center. Du kan avinstallera agenten manuellt om det behövs.

> [!NOTE]
> Om du vill veta vilka virtuella datorer som stöds finns en lista bland våra [vanliga frågor och svar om Azure Security Center](faq-vms.md).

### <a name="workspace"></a>Arbetsyta

En arbetsyta är en Azure-resurs som fungerar som en datacontainer. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Data som samlas in från Log Analytics agenten (för Azure Security Center) lagras antingen i en befintlig Log Analytics arbets yta som är associerad med din Azure-prenumeration eller nya arbets ytor med hänsyn till den virtuella datorns geografiska plats.

Du kan bläddra om du vill se en lista över dina logganalysarbetsytor, inklusive alla som skapats av Azure Security Center i Azure-portalen. En relaterad resursgrupp skapas för nya arbetsytor. Både följer namnkonventionen:

* Arbetsyta: *DefaultWorkspace-[prenumerations-ID]-[geo]*
* Resurs grupp: *DefaultResourceGroup-[geo]*

För arbetsytor som skapats av Azure Security Center sparas data i 30 dagar. För befintliga arbetsytor baseras kvarhållningen på arbetsytans prisnivå. Om du vill kan du även använda en befintlig arbetsyta.

> [!NOTE]
> Microsoft gör starka åtaganden för att skydda sekretessen och säkerheten för dessa data. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Mer information om datahantering och sekretess finns i [Datasäkerhet i Azure Security Center](security-center-data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Publicera icke-Azure-resurser

Security Center kan övervaka säkerhetsstatusen för icke-Azure-datorer men du måste först publicera dessa resurser. Läs [andra icke-Azure-datorer](quickstart-onboard-machines.md) om du vill ha mer information om hur du kan publicera icke-Azure-resurser.

## <a name="ongoing-security-monitoring"></a>Fortlöpande säkerhetsövervakning
När rekommendationerna i Security Center har ställts in och tillämpats är det dags att fundera över driftrutinerna i Security Center.

Översikten över Security Center ger en enhetlig vy över säkerheten i alla dina Azure-resurser och eventuella icke-Azure-resurser som du har anslutit. Exemplet nedan visar en miljö med många problem att hantera:

![instrumentpanel](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center påverkar inte de normala driftrutinerna. Alla distributioner övervakas passivt och rekommendationer går ut baserat på de säkerhetsprinciper som du har aktiverat.

När du först väljer att använda Security Center för din aktuella Azure-miljö bör du kontrol lera att du har granskat alla rekommendationer, som du kan göra på sidan **rekommendationer** .

Planera in att använda Hotinformation som en del av dina dagliga säkerhetsåtgärder. Där kan du identifiera säkerhetshot mot miljön, till exempel identifiera om en viss dator är en del av ett botnät.

### <a name="monitoring-for-new-or-changed-resources"></a>Övervakning av nya och ändrade resurser

De flesta Azure-miljöer är dynamiska, och de resurser som regelbundet skapas, anpassas upp eller ned, konfigureras om och ändras. Med Security Center har du bra insyn i de nya objektens säkerhetsstatus.

När du lägger till nya resurser (virtuella datorer, SQL-databaser osv.) i Azure-miljön identifierar Security Center de här resurserna automatiskt och börjar övervaka deras säkerhet. Detta omfattar även arbetarroller och webbroller i PaaS. Om data insamling har Aktiver ATS i [säkerhets principen](tutorial-security-policy.md)aktive ras ytterligare övervaknings funktioner automatiskt för dina virtuella datorer.

Du bör också regelbundet övervaka befintliga resurser för konfigurations ändringar som kan ha skapat säkerhets risker, avvikelse från rekommenderade bas linjer och säkerhets aviseringar. 

### <a name="hardening-access-and-applications"></a>Härdning av åtkomst och program

Som en del av dina säkerhetsåtgärder bör du även vidta förebyggande åtgärder för att begränsa åtkomsten till virtuella datorer och kontrollera programmen som körs på virtuella datorer. Genom att låsa inkommande trafik till dina virtuella Azure-datorer minskar du exponeringen för attacker och samtidigt ger du enkel anslutningsåtkomst till virtuella datorer när det behövs. Använd [just-in-Time-](security-center-just-in-time.md) funktionen för åtkomst till virtuella datorer för att förstärka åtkomsten till dina virtuella datorer.

Du kan använda [anpassningsbara program kontroller](security-center-adaptive-application.md) för att begränsa vilka program som kan köras på dina virtuella datorer som finns i Azure. Bland andra förmåner hjälper detta till att förstärka dina virtuella datorer mot skadlig kod. Med hjälp av maskin inlärning analyserar Security Center processer som körs i den virtuella datorn så att du kan skapa List regler.


## <a name="incident-response"></a>Incidenthantering
Security Center identifierar och varnar dig om hot så fort de uppstår. Organisationen bör övervaka om det kommer nya säkerhetsaviseringar och vidta de åtgärder som behövs för att undersöka vidare eller stoppa angreppet. Mer information om hur Security Center Threat Protection fungerar finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

Även om den här artikeln inte har avsikt att hjälpa dig att skapa en egen incident svars plan ska vi använda Microsoft Azure säkerhets svar i moln livs cykeln som grund för incident svars faser. Stegen visas i följande diagram:

![Steg för incident svaret i moln livs cykeln](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> National Institute of Standards and Technology (NIST) har en [handbok för hantering av datasäkerhetsrelaterade incidenter](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) som kan vara till hjälp när du vill skapa en egen plan.
>

Du kan använda Security Center-aviseringar i följande faser:

* **Identifiera**: Identifiera misstänkt aktivitet i en eller flera resurser.
* **Utvärdera**: Utför en första utvärdering för att få mer information om den misstänkta aktiviteten.
* **Diagnostisera**: Gå igenom de tekniska rutinerna genom att utföra åtgärdsstegen för att åtgärda problemet.

I säkerhetsaviseringarna finns information som gör att du kan förstå vilken typ av angrepp det rör sig om och vad du kan göra för att åtgärda angreppet. I vissa aviseringar finns länkar antingen till mer information eller till andra informationskällor inom Azure. Du kan använda informationen för ytterligare forskning och för att påbörja åtgärdsarbetet. Du kan också söka säkerhetsrelaterade data som lagras på din arbetsyta.

Följande exempel visar en misstänkt RDP-aktivitet:

![Misstänkt aktivitet](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Den här sidan visar information om när angreppet upptäcktes, varifrån det kommer och vilken virtuell dator som är drabbad, och här finns även rekommendationer för vad du bör göra. I vissa fall kan Angreppets käll information vara tom. [Här](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) finns mer information om de fall då uppgift om källa saknas i aviseringar i Azure Security Center.

När du har identifierat det komprometterade systemet kan du köra en [arbets flödes automatisering](workflow-automation.md) som skapats tidigare. Detta är en samling procedurer som kan köras från Security Center när de utlöses av en avisering.

I [hur du utnyttjar Azure Security Center & Microsoft Operations Management Suite för en incident svars](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) video kan du se vissa demonstrationer som visar hur Security Center kan användas i var och en av dessa steg.

> [!NOTE]
> Läs [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) om du vill ha mer information om hur du använder Security Center funktioner för att hjälpa dig under incident svars processen.
>
>

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du planerar integreringen av Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Övervaka partner lösningar med Azure Security Center](./security-center-partner-integration.md) – lär dig hur du övervakar dina partner lösningars hälso status.
* [Azure Security Center vanliga frågor och svar](faq-general.md) – hitta vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](/archive/blogs/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.