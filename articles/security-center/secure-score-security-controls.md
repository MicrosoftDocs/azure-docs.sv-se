---
title: Säkra poäng i Azure Security Center
description: Beskrivning av Azure Security Center säkra poäng och säkerhets kontroller
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 9594e1ed14b017591ea2c4ddda59ba61feb81b0c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272288"
---
# <a name="enhanced-secure-score-in-azure-security-center"></a>Förbättrade säkra poäng i Azure Security Center

## <a name="introduction-to-secure-score"></a>Introduktion till säkra Poäng

Azure Security Center har två huvudsakliga mål: för att hjälpa dig att förstå den aktuella säkerhets situationen och för att hjälpa dig att effektivt och effektivt förbättra säkerheten. Den centrala aspekten av Security Center som gör att du kan uppnå dessa mål är säkra poäng.

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation efter säkerhets problem. Den sammanställer sedan alla resultat i en enda poäng så att du snabbt kan tala om din aktuella säkerhets situation: ju högre poäng, desto lägre är den identifierade risk nivån.

På sidan säker Poäng för Security Center ingår:

- **Poängen** – den säkra poängen visas som ett procent värde, men de underliggande värdena är också tydliga:

    [![Säkra poäng som visas som ett procent värde med underliggande tal rensa för](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Säkerhets kontroller** – varje kontroll är en logisk grupp relaterade säkerhets rekommendationer och återspeglar dina sårbara angrepps ytor. En kontroll är en uppsättning säkerhets rekommendationer med anvisningar som hjälper dig att implementera dessa rekommendationer. Poängen ökar bara när du reparerar *alla* rekommendationer för en enskild resurs i en kontroll.

    Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

    Mer information finns i [så här beräknas din säkra Poäng](secure-score-security-controls.md#how-your-secure-score-is-calculated) nedan. 


>[!TIP]
> Tidigare versioner av Security Center tilldelade punkter på rekommendations nivå: när du har reparerat en rekommendation för en enskild resurs, förbättras dina säkra poäng. Idag ökar poängen bara om du reparerar *alla* rekommendationer för en enskild resurs i en kontroll. Det innebär att poängen bara ökar när du har förbättrat säkerheten för en resurs.


## <a name="accessing-your-secure-score"></a>Åtkomst till dina säkra Poäng

Du hittar den övergripande säkra poängen, samt poängen per prenumeration, via Azure Portal eller program mässigt med Azure Security Center REST API.

### <a name="getting-your-secure-score-from-the-portal"></a>Få dina säkra Poäng från portalen

Security Center visar ditt resultat på en framträdande plats i portalen: det är det första som visas på sidan Översikt. Om du klickar till sidan för dedikerade säkra poäng visas poängen uppdelad efter prenumeration. Klicka på en enskild prenumeration om du vill se en detaljerad lista över prioriterade rekommendationer och den potentiella påverkan som åtgärdas i prenumerationens resultat.

![Övergripande säkra poäng som visas i portalen](media/secure-score-security-controls/single-secure-score-via-ui.png)

### <a name="getting-your-secure-score-from-the-rest-api"></a>Få dina säkra Poäng från REST API

Du kan komma åt dina poäng via [Secure score-API: t](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (för närvarande i för hands version). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda **Secure Scores** -API: et för att hämta poängen för en speciell prenumeration. Dessutom kan du använda API: et för **säker Poäng** för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

![Hämta en enskild säker Poäng via API: et](media/secure-score-security-controls/single-secure-score-via-api.png)

Exempel på verktyg som byggts ovanpå Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="how-your-secure-score-is-calculated"></a>Så här beräknas din säkra Poäng 

Bidraget för varje säkerhets kontroll till den övergripande säkra poängen visas tydligt på sidan rekommendationer.

[![Den förbättrade säkra poängen introducerar säkerhets kontroller](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

För att få alla möjliga punkter för en säkerhets kontroll måste alla dina resurser följa alla säkerhets rekommendationer i säkerhets kontrollen. Security Center har till exempel flera rekommendationer om hur du skyddar dina hanterings portar. Tidigare kunde du åtgärda några av de relaterade och beroende rekommendationerna samtidigt som du lämnar andra olösta och dina säkra poäng skulle öka. När du har tittat på målet är det enkelt att att att din säkerhetsinte hade förbättras tills du har löst alla. Nu måste du reparera dem för att göra en skillnad på dina säkra poäng.

Säkerhets kontrollen "tillämpa system uppdateringar" har till exempel en högsta poäng på sex punkter, som du kan se i knapp beskrivningen för kontrollens potentiella öknings värde:

[![Säkerhets kontrollen tillämpa system uppdateringar](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Den maximala poängen för den här kontrollen, tillämpa system uppdateringar, är alltid 6. I det här exemplet finns det 50 resurser. Vi delar upp max poängen med 50 och resultatet är att varje resurs bidrar med 0,12 punkter. 

* **Potentiell ökning** (0,12 x 8 Felaktiga resurser = 0,96) – de återstående punkterna som är tillgängliga för dig inom kontrollen. Om du reparerar alla rekommendationer i den här kontrollen kommer poängen att öka med 2% (i det här fallet 0,96 punkter avrundade uppåt till 1 punkt). 
* **Nuvarande Poäng** (0,12 x 42 friska resurser = 5,04) – aktuell Poäng för den här kontrollen. Varje kontroll bidrar till den totala poängen. I det här exemplet bidrar kontrollen 5,04 punkter till den aktuella säkra summan.
* **Högsta poäng** – det maximala antalet poäng som du kan få genom att slutföra alla rekommendationer inom en kontroll. Den maximala poängen för en kontroll anger den relativa betydelsen av kontrollen. Använd Max poäng värden för att prioritering problemen. 


### <a name="calculations---understanding-your-score"></a>Beräkningar – förstå dina Poäng

|Mått|Formel och exempel|
|-|-|
|**Säkerhets kontrollens aktuella Poäng**|<br>![Formel för att beräkna en säkerhets kontrolls aktuella Poäng](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Varje enskild säkerhets kontroll bidrar till säkerhets poängen. Varje resurs som påverkas av en rekommendation inom kontrollen bidrar till kontrollens aktuella resultat. Den aktuella poängen för varje kontroll är ett mått på statusen för resurserna *i* kontrollen.<br>![Knapp beskrivningar som visar de värden som används när du beräknar säkerhets kontrollens aktuella Poäng](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>I det här exemplet skulle max poängen på 6 divideras med 78 eftersom det är summan av de felfria och felaktiga resurserna.<br>6/78 = 0,0769<br>Om du multiplicerar det med antalet felfria resurser (4) resulterar det i den aktuella poängen:<br>0,0769 * 4 = **0,31**<br><br>|
|**Säkerhetspoäng**<br>Enstaka prenumeration|<br>![Ekvation för att beräkna nuvarande säkra Poäng](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Säker Poäng för enskild prenumeration med alla kontroller aktiverade](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>I det här exemplet finns det en enda prenumeration med alla säkerhets kontroller som är tillgängliga (en potentiell högsta poäng på 60 punkter). Poängen visar 28 punkter av en möjlig 60 och de återstående 32 punkterna visas i siffrorna "potentiella Poäng ökning" i säkerhets kontrollerna.<br>![Lista över kontroller och potentiella Poäng ökningar](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Säkerhetspoäng**<br>Flera prenumerationer|<br>De aktuella poängen för alla resurser i alla prenumerationer läggs till och beräkningen är sedan samma som för en enskild prenumeration<br><br>När du visar flera prenumerationer utvärderar säkra poäng alla resurser i alla aktiverade principer och grupperar deras kombinerade påverkan på varje säkerhets kontrolls maximala poäng.<br>![Säkra Poäng för flera prenumerationer med aktiverade kontroller](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Den kombinerade poängen är **inte** ett medelvärde. i stället är det utvärderat position av status för alla resurser i alla prenumerationer.<br>Om du går till sidan rekommendationer och lägger till tillgängliga tillgängliga punkter, kommer du att se att det är skillnaden mellan den aktuella poängen (24) och den maximala poängen som är tillgänglig (60).|
||||

## <a name="improving-your-secure-score"></a>Förbättra dina säkra Poäng

Du kan förbättra dina säkra poäng genom att åtgärda säkerhets rekommendationerna från listan över rekommendationer. Du kan åtgärda varje rekommendation manuellt för varje resurs, eller genom att använda **snabb korrigering!** alternativ (när det är tillgängligt) för att tillämpa en reparation för en rekommendation till en grupp med resurser snabbt. Mer information finns i [åtgärda rekommendationer](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Det är bara inbyggda rekommendationer som påverkar de säkra poängen.


## <a name="security-controls-and-their-recommendations"></a>Säkerhets kontroller och deras rekommendationer

I tabellen nedan visas säkerhets kontrollerna i Azure Security Center. För varje kontroll kan du se det maximala antalet punkter som du kan lägga till i dina säkra poäng om du reparerar *alla* rekommendationer som anges i kontrollen för *alla* dina resurser. 

<div class="foo">

<style type="text/css"> . TG {kant linje-komprimera: minimera; kant linje avstånd: 0;}. TG TD {kant linje färg: svart; kant linje format: solid; kant linje bredd: 1px; teckensnitts familj: Arial, sans-serif; font-size: 14px; spill: Hidden; utfyllnad: 10px 5px; Word-Break: normal;}. TG {kant linje färg: Black; kant linje format: solid; kant linje bredd: 1px; teckensnitts familj: Arial, sans-serif; font-size: 18px; font-weight: normal; spill: Hidden; utfyllnad: 10px 5px; ord-Break: normal;}. TG. TG-cly1 {text-align: Left; vertikalt-align: mitten}. TG. TG-lboi {kant linje färg: Ärv; text – justera: vänster; lodrätt justerad: mitten} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Säkerhets kontroll, poäng och beskrivning</b><br></th>
    <th class="tg-cly1"><b>Rekommendationer</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aktivera MFA (max. 10 Poäng)</p></strong>Om du bara använder ett lösen ord för att autentisera en användare lämnar den en angrepps vektor öppen. Om lösen ordet är svagt eller har exponerats någon annan stans, är det verkligen användaren som loggar in med användar namn och lösen ord?<br>När <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> är aktiverat är dina konton säkrare och användare kan fortfarande autentisera till nästan alla program med enkel inloggning (SSO).</td>
    <td class="tg-lboi"; width=55%>- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration<br>- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Säkra hanterings portar (högst 8 Poäng)</p></strong>Brute force-attacker riktas mot mål hanterings portar för att få åtkomst till en virtuell dator. Eftersom portarna inte alltid måste vara öppna, är en minsknings strategi att minska exponeringen för portarna med just-in-Time-kontroller för nätverks åtkomst, nätverks säkerhets grupper och hantering av virtuella dator portar.<br>Eftersom många IT-organisationer inte blockerar SSH-kommunikation utgående från nätverket kan angripare skapa krypterade tunnlar som gör att RDP-portar på infekterade system kan kommunicera tillbaka till angripare kommandot för att kontrol lera servrar. Angripare kan använda under systemet Windows Remote Management för att gå vidare i din miljö och använda stulna autentiseringsuppgifter för att komma åt andra resurser i ett nätverk.</td>
    <td class="tg-lboi"; width=55%>- Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst<br>- Virtuella datorer ska associeras med en nätverks säkerhets grupp<br>- Hanterings portar bör stängas på dina virtuella datorer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Tillämpa system uppdateringar (Max poäng 6)</p></strong>System uppdateringar ger organisationer möjlighet att underhålla drifts effektivitet, minska säkerhets risker och tillhandahålla en mer stabil miljö för slutanvändare. Att inte tillämpa uppdateringar lämnar uppdateringar som inte har uppdaterats och resulterar i miljöer som är mottagliga för attacker. Dessa sårbarheter kan utnyttjas och leda till data förlust, data exfiltrering, utpressnings tro och resurs missbruk. Om du vill distribuera system uppdateringar kan du använda <a href="https://docs.microsoft.com/azure/automation/automation-update-management">uppdateringshantering-lösningen för att hantera korrigeringar och uppdateringar</a> för dina virtuella datorer. Uppdaterings hantering är en process för att kontrol lera distribution och underhåll av program varu versioner.</td>
    <td class="tg-lboi"; width=55%>- Övervaknings agentens hälso problem bör lösas på dina datorer<br>- Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>- Övervaknings agenten ska installeras på dina datorer<br>- OS-versionen bör uppdateras för dina moln tjänst roller<br>- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras<br>- System uppdateringar bör installeras på dina datorer<br>- Datorerna måste startas om för att tillämpa system uppdateringar<br>- Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version<br>- Övervaknings agenten ska installeras på dina virtuella datorer<br>- Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)<br>- Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Åtgärda sårbarheter (högst 6 Poäng)</p></strong>Ett säkerhets problem är en svaghet som en hot aktör kan utnyttja för att avslöja konfidentialitet, tillgänglighet eller integritet för en resurs. <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Hantering av sårbarheter</a> minskar organisationens exponering, härdnings områdets yta, ökar organisationens återhämtning och minskar risken för angrepp på dina resurser. Hot-och sårbarhets hantering ger insyn i program varu-och säkerhets inställningar och ger rekommendationer för begränsningar.</td>
    <td class="tg-lboi"; width=55%>- Avancerad data säkerhet ska vara aktiverat på SQL Database<br>- Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas<br>- Säkerhets risker i SQL-databaser bör åtgärdas<br>- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning<br>- Sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans<br>- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar<br>- Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aktivera kryptering i vilo läge (max. 4)</p></strong><a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">Kryptering i vila</a> tillhandahåller data skydd för lagrade data. Attacker mot data i rest inkluderar försök att få fysisk åtkomst till den maskin vara som data lagras på. Azure använder symmetrisk kryptering för att kryptera och dekryptera stora mängder data i vila. En symmetrisk krypterings nyckel används för att kryptera data när de skrivs till lagringen. Krypterings nyckeln används också för att dekryptera dessa data när de är lätta att använda i minnet. Nycklar måste lagras på en säker plats med identitetsbaserade åtkomst kontroll och gransknings principer. En sådan säker plats är Azure Key Vault. Om en angripare hämtar krypterade data men inte krypterings nycklarna, kan angriparen inte komma åt data utan att behöva bryta krypteringen.</td>
    <td class="tg-lboi"; width=55%>- Disk kryptering bör tillämpas på virtuella datorer<br>- transparent datakryptering på SQL Database ska vara aktive rad<br>- Variabler för Automation-konton ska vara krypterade<br>- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign<br>- SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Kryptera data under överföring (max. 4)</p></strong>Data är "under överföring" när den överförs mellan komponenter, platser eller program. Organisationer som inte kan skydda data under överföringen är utsatta för att man ska kunna skydda data som är i mellanliggande attacker, avlyssning och kapning av sessioner. SSL/TLS-protokoll ska användas för att utbyta data och ett VPN rekommenderas. När du skickar krypterade data mellan en virtuell Azure-dator och en lokal plats, via Internet, kan du använda en virtuell nätverksgateway som <a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN gateway</a> för att skicka krypterad trafik.</td>
    <td class="tg-lboi"; width=55%>- API-appen bör bara vara tillgänglig via HTTPS<br>- Funktionsapp bör endast vara tillgängligt via HTTPS<br>- Endast säkra anslutningar till din Redis Cache ska vara aktiverade<br>- Säker överföring till lagrings konton ska vara aktiverat<br>- Webb program bör endast vara tillgängliga via HTTPS</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Hantera åtkomst och behörigheter (max. 4)</p></strong>En kärn del av ett säkerhets program ser till att användarna har den behörighet som krävs för att utföra sina jobb, men inte mer än det: den <a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">minsta behörighets åtkomst modellen</a>.<br>Kontrol lera åtkomsten till dina resurser genom att skapa roll tilldelningar med <a href="https://docs.microsoft.com/azure/role-based-access-control/overview">rollbaserad åtkomst kontroll (RBAC)</a>. En roll tilldelning består av tre element:<br>- <strong>Säkerhets objekt</strong>: det objekt som användaren begär åtkomst till<br>- <strong>Roll definition</strong>: deras behörigheter<br>- <strong>Omfattning</strong>: den uppsättning resurser som behörigheterna gäller</td>
    <td class="tg-lboi"; width=55%>- Föråldrade konton bör tas bort från din prenumeration (för hands version)<br>- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration (för hands version)<br>- Externa konton med ägar behörigheter bör tas bort från din prenumeration (för hands version)<br>- Externa konton med Skriv behörighet bör tas bort från din prenumeration (för hands version)<br>- Det bör finnas fler än en ägare som tilldelats din prenumeration<br>- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services (för hands version)<br>- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering<br>- Tjänstens huvud namn ska användas för att skydda dina prenumerationer i stället för hanterings certifikat</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Åtgärda säkerhetskonfigurationer (max. 4)</p></strong>Felkonfigurerade IT-tillgångar har en högre risk för angrepp. Grundläggande härdnings åtgärder glöms ofta när till gångar distribueras och tids gränser måste vara uppfyllda. Säkerhets konfigurations inställningar kan vara på valfri nivå i infrastrukturen: från operativ system och nätverks enheter till moln resurser.<br>Azure Security Center jämför kontinuerligt konfigurationen av dina resurser med krav i bransch standarder, regler och benchmarks. När du har konfigurerat relevanta "efterlevnadsprinciper" (standarder och bas linjer) som är viktiga för din organisation kommer eventuella luckor att leda till säkerhets rekommendationer som innehåller CCEID och en förklaring till den potentiella säkerhets påverkan.<br>Vanliga paket är <a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">Azure Security benchmark</a> och <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure grunderna 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- Pod säkerhets principer bör definieras på Kubernetes-tjänster<br>- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas<br>- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas<br>- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas<br>- Övervaknings agenten ska installeras på dina virtuella datorer<br>- Övervaknings agenten ska installeras på dina datorer<br>- Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)<br>- Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)<br>- Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>- Övervaknings agentens hälso problem bör lösas på dina datorer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Begränsa obehörig nätverks åtkomst (max. 4)</p></strong>Slut punkter inom en organisation ger en direkt anslutning från ditt virtuella nätverk till Azure-tjänster som stöds. Virtuella datorer i ett undernät kan kommunicera med alla resurser. Om du vill begränsa kommunikationen till och från resurser inom ett undernät skapar du en nätverks säkerhets grupp och kopplar den till under nätet. Organisationer kan begränsa och skydda mot obehörig trafik genom att skapa regler för inkommande och utgående trafik.</td>
    <td class="tg-lboi"; width=55%>- IP-vidarebefordran på den virtuella datorn bör inaktive ras<br>- Auktoriserade IP-intervall ska definieras för Kubernetes Services (för hands version)<br>- FÖRÅLDRAD Åtkomst till App Services ska vara begränsad (för hands version)<br>- FÖRÅLDRAD Reglerna för webb program på IaaS NSG: er bör vara härdade<br>- Virtuella datorer ska associeras med en nätverks säkerhets grupp<br>- CORS bör inte tillåta alla resurser åtkomst till din API-app<br>- CORS bör inte tillåta alla resurser att komma åt din Funktionsapp<br>- CORS bör inte tillåta alla resurser åtkomst till ditt webb program<br>- Fjärrfelsökning bör inaktive ras för API-appen<br>- Fjärrfelsökning bör inaktive ras för Funktionsapp<br>- Fjärrfelsökning bör inaktive ras för webb program<br>- Åtkomsten ska begränsas för tillåtade nätverks säkerhets grupper med virtuella datorer som riktas mot Internet<br>- Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Använd adaptiv program kontroll (Max poäng 3)</p></strong>Adaptiva program kontroller (AAC) är en intelligent, automatiserad lösning från slut punkt till slut punkt som gör det möjligt att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer. Det hjälper också till att förstärka dina datorer mot skadlig kod.<br>Security Center använder Machine Learning för att skapa en lista över kända säkra program för en grupp datorer.<br>Den här innovativa metoden för godkänd program registrering ger säkerhets fördelarna utan hanterings komplexitet.<br>AAC är särskilt relevant för syftes skapade servrar som behöver köra en särskild uppsättning program.</td>
    <td class="tg-lboi"; width=55%>- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer<br>- Övervaknings agenten ska installeras på dina virtuella datorer<br>- Övervaknings agenten ska installeras på dina datorer<br>- Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)<br>- Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)<br>- Övervaknings agentens hälso problem bör lösas på dina datorer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Använd data klassificering (max antal poäng 2)</p></strong>Genom att klassificera din organisations data efter känslighet och inverkan på företaget kan du fastställa och tilldela värden till data och tillhandahålla strategin och grunden för styrning.<br><a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">Azure information Protection</a> kan hjälpa med data klassificering. Den använder krypterings-, identitets-och Auktoriseringsprinciper för att skydda data och begränsa data åtkomsten. Vissa klassificeringar som Microsoft använder är icke-affärsmässiga, offentliga, allmänna, konfidentiella och mycket konfidentiella.</td>
    <td class="tg-lboi"; width=55%>- Känsliga data i dina SQL-databaser ska klassificeras (för hands version)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Skydda program mot DDoS-attacker (Max poäng 2)</p></strong>Distributed denial-of-service (DDoS) kan överbelasta resurser och göra program oanvändbara. Använd <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection standard</a> för att skydda din organisation från de tre viktigaste typerna av DDoS-attacker:<br>Med - <strong>volym attacker</strong> översvämmas nätverket med legitim trafik. DDoS Protection standard minskar dessa attacker genom att absorbera eller rensa dem automatiskt.<br>- <strong>Protokoll attacker</strong> återger ett mål som inte kan nås genom att utnyttja svagheter i skikt 3-och lager 4-protokollstacken. DDoS Protection-standarden minimerar dessa attacker genom att blockera skadlig trafik.<br>- <strong>Resurs (program) skikt attacker</strong> mål webb program paket. Skydda mot den här typen med en brand vägg för webbaserade program och DDoS Protection standard.</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection standard ska vara aktive rad</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aktivera Endpoint Protection (Max poäng 2)</p></strong>För att se till att dina slut punkter skyddas från skadlig kod kan beteende sensorer samla in och bearbeta data från dina slut punkter till operativ system och skicka dessa data till det privata molnet för analys. Security Analytics utnyttjar stora data, maskin inlärning och andra källor för att rekommendera svar på hot. <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> använder till exempel Hot information för att identifiera angrepps metoder och generera säkerhets aviseringar.<br>Security Center stöder följande lösningar för slut punkts skydd: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee v10 för Windows, McAfee v10 för Linux och Sophos v9 för Linux. Om Security Center identifierar någon av dessa lösningar visas inte längre rekommendationen att installera Endpoint Protection.</td>
    <td class="tg-lboi"; width=55%>- Hälso fel i Endpoint Protection bör åtgärdas på virtuella datorers skalnings uppsättningar<br>- Problem med slut punkts skydd bör lösas på dina datorer<br>- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar<br>- Installera Endpoint Protection-lösning på virtuella datorer<br>- Övervaknings agentens hälso problem bör lösas på dina datorer<br>- Övervaknings agenten ska installeras på virtuella datorers skalnings uppsättningar<br>- Övervaknings agenten ska installeras på dina datorer<br>- Övervaknings agenten ska installeras på dina virtuella datorer<br>- Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)<br>- Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)<br>- Installera Endpoint Protection-lösning på dina datorer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aktivera granskning och loggning (max resultat 1)</p></strong>Loggnings data ger insikter om tidigare problem, förhindrar att de kan förbättra programmets prestanda och ger möjlighet att automatisera åtgärder som annars skulle vara manuella.<br>- <strong>Kontroll-och hanterings loggar</strong> innehåller information om <a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> åtgärder.<br>- <strong>Data Plans loggar</strong> innehåller information om händelser som Aktiver ATS som en del av Azure-resursanvändningen.<br>- <strong>Bearbetade händelser</strong> innehåller information om analyserade händelser/aviseringar som har bearbetats.</td>
    <td class="tg-lboi"; width=55%>- Granskning på SQL Server måste vara aktiverat<br>- Diagnostikloggar i App Services ska vara aktive rad<br>- Diagnostikloggar i Azure Data Lake Store ska vara aktive rad<br>- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad<br>- Diagnostikloggar i batch-konton måste vara aktiverade<br>- Diagnostikloggar i Data Lake Analytics ska vara aktive rad<br>- Diagnostikloggar i Händelsehubben måste vara aktive rad<br>- Diagnostikloggar i IoT Hub ska vara aktive rad<br>- Diagnostikloggar i Key Vault ska vara aktive rad<br>- Diagnostikloggar i Logic Apps ska vara aktive rad<br>- Diagnostikloggar i Search-tjänsten måste vara aktive rad<br>- Diagnostikloggar i Service Bus ska vara aktive rad<br>- Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad<br>- Mått varnings regler ska konfigureras för batch-konton<br>- Inställningarna för SQL-granskning bör ha åtgärds grupper konfigurerade för att fånga kritiska aktiviteter<br>- SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aktivera avancerat skydd (max antal poäng 0)</p></strong>Azure Security Center s hot skydd ger omfattande försvar för din miljö. När Security Center identifierar ett hot i alla områden i miljön genererar den en avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar.<br>Varje hot skydds paket är ett separat, valfritt erbjudande som du kan aktivera med hjälp av rekommendationen i den här säkerhets kontrollen.<br><a href="https://docs.microsoft.com/azure/security-center/threat-protection">Läs mer om skydd av hot i Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- Avancerad data säkerhet ska vara aktiverat på Azure SQL Database servrar<br>- Avancerad data säkerhet ska vara aktiverat på SQL-servrar på datorer<br>- Avancerat skydd bör vara aktiverat på Virtual Machines<br>- Avancerat skydd bör vara aktiverat på Azure App Service planer<br>- Avancerat skydd bör vara aktiverat på Azure Storage konton<br>- Avancerat skydd bör vara aktiverat på Azure Kubernetes service-kluster<br>- Avancerat skydd bör vara aktiverat på Azure Container Registry register<br>- Avancerat skydd bör vara aktiverat på Azure Key Vault valv</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementera rekommenderade säkerhets metoder (max antal poäng 0)</p></strong>Modern säkerhets praxis "anta överträdelse" av nätverks omkretsen. Därför är många av de bästa metoderna i den här kontrollen fokuserade på att hantera identiteter.<br>Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">Azure Key Vault</a> skyddar nycklar och hemligheter genom att kryptera nycklar, PFX-filer och lösen ord.<br>Virtuella privata nätverk (VPN) är ett säkert sätt att komma åt dina virtuella datorer. Om VPN inte är tillgängligt använder du komplexa lösen fraser och tvåfaktorautentisering, till exempel <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">Azure Multi-Factor Authentication</a>. Med tvåfaktorautentisering undviker du de svagheter som endast förlitar sig på användar namn och lösen ord.<br>Att använda kraftfulla plattformar för autentisering och auktorisering är en annan metod. Med federerade identiteter kan organisationer delegera hantering av auktoriserade identiteter. Detta är också viktigt när anställda avslutas och deras åtkomst måste återkallas.</td>
    <td class="tg-lboi"; width=55%>- Högst 3 ägare bör anges för din prenumeration<br>- Externa konton med Läs behörighet bör tas bort från din prenumeration<br>- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration<br>- Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas<br>- Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet<br>- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar<br>- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser<br>- Auktoriseringsregler för Event Hub-instansen måste definieras<br>- Lagrings konton ska migreras till nya Azure Resource Manager resurser<br>- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser<br>- Undernät ska associeras med en nätverks säkerhets grupp<br>- Förhandsgranskningsvyn Windows sårbarhets Guard ska vara aktive rad <br>- Förhandsgranskningsvyn Konfigurations agenten för gäster bör installeras<br>- Virtuella datorer som inte är baserade på Internet bör skyddas med nätverks säkerhets grupper</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Vanliga frågor och svar om säker Poäng

### <a name="why-has-my-secure-score-gone-down"></a>Varför är mina säkra Poäng borta?
Security Center har bytt till en förbättrad säker Poäng som innehåller ändringar i hur poängen beräknas. Nu måste du lösa alla rekommendationer för en resurs för att ta emot punkter. Poängen har också ändrats till en skala på 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Om jag bara har tre av fyra rekommendationer i en säkerhets kontroll, kommer mina säkra poäng att ändras?
Nej. Den ändras inte förrän du reparerar alla rekommendationer för en enskild resurs. För att få den högsta poängen för en kontroll måste du åtgärda alla rekommendationer för alla resurser.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Är tidigare erfarenhet av de säkra poängen fortfarande tillgängliga? 
Nej. En gång de körde sida vid sida för att under lätta över gången. Föregående modell har nu blivit föråldrad. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Om en rekommendation inte gäller mig och jag inaktiverar den i principen, kommer min säkerhets kontroll att uppfyllas och mina säkra resultat uppdateras?
Ja. Vi rekommenderar att du inaktiverar rekommendationer när de inte är tillämpliga i din miljö. Instruktioner för hur du inaktiverar en speciell rekommendation finns i [inaktivera säkerhets principer](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Om en säkerhets kontroll ger mig noll punkter mot mina säkra poäng, ska jag ignorera den?
I vissa fall visas en kontroll över max poängen som är större än noll, men effekten är noll. När den stegvisa poängen för att lösa resurserna är försumbar, avrundas den till noll. Ignorera inte de här rekommendationerna eftersom de fortfarande ger säkerhets förbättringar. Det enda undantaget är kontrollen "ytterligare bästa praxis". Att åtgärda dessa rekommendationer ökar inte dina poäng, men det förbättrar den övergripande säkerheten.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs säkra poäng och de säkerhets kontroller som det introducerar. Information om relaterade material finns i följande artiklar:

- [Lär dig mer om de olika elementen i en rekommendation](security-center-recommendations.md)
- [Lär dig hur du åtgärdar rekommendationer](security-center-remediate-recommendations.md)
