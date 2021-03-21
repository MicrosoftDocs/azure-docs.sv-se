---
title: Översikt över HIPAA HITRUST 9,2 skissing-exempel
description: Översikt över skiss exemplet HIPAA HITRUST 9,2. Det här skiss exemplet hjälper kunderna att bedöma vissa HIPAA HITRUST 9,2-kontroller.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: bd65b6113f291457096bacc02bdbcfd92d6e0f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915555"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Skiss exempel för HIPAA HITRUST 9,2

Skiss exemplet HIPAA HITRUST 9,2 ger styrnings skydd-räler med [Azure policy](../../policy/overview.md) som hjälper dig att utvärdera vissa HIPAA HiTRUST 9,2-kontroller. Den här skissen hjälper kunder att distribuera en central uppsättning principer för alla Azure-distribuerade arkitektur som måste implementera HIPAA HITRUST 9,2-kontroller.

## <a name="control-mapping"></a>Kontrollmappning

Den [Azure policy kontroll mappningen](../../policy/samples/hipaa-hitrust-9-2.md) innehåller information om princip definitioner som ingår i den här skissen och hur dessa princip definitioner mappas till **domänerna för regelefterlevnad** och **kontroller** i HIPAA HiTRUST 9,2. När den är tilldelad till en arkitektur utvärderas resurserna av Azure Policy för inkompatibilitet med tilldelade princip definitioner. Mer information finns i [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Distribuera

För att distribuera HIPAA HITRUST 9,2-skissen för Azure skisser måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

### <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Hitta skiss exemplet **HIPAA HiTRUST** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss EXEMPLET HIPAA HiTRUST 9,2.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

### <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från justeringen med HIPAA HITRUST 9,2-kontroller.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från HIPAA HiTRUST 9,2 skiss exemplet". Välj därefter **Publicera** längst ned på sidan.

### <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopieringen av skiss exemplet har **publicerats** kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. I det här steget anges parametrar för att göra varje distribution av skissexemplets kopia unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Tilldela skiss** överst på skissdefinitionssidan.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer mer än en prenumeration, skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelnings namn**: namnet fylls i automatiskt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som det är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skissens låsinställning för din miljö. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelade_ hanterade identiteter.

   - Artefaktparametrar

     De parametrar som definieras i avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrollera statusen för distributionen genom att öppna skisstilldelningen.

> [!WARNING]
> Tjänsten Azure Blueprints och de inbyggda skissexemplen är **kostnadsfria**. Azures resurser [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) till att beräkna kostnaden för att köra de resurser som distribueras i skissexemplet.

### <a name="artifact-parameters-table"></a>Tabell med artefaktparametrar

I följande tabell visas en lista med skissartefaktens parametrar:

|Artefaktnamn |Parameternamn |Beskrivning |
|---|---|---|
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Åtkomst via slut punkt mot Internet bör vara begränsad |Aktivera eller inaktivera överanvändning av regler för inkommande NSG-regler |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Konton: Status för gästkonto |Anger om det lokala gäst kontot är inaktiverat. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer |Aktivera eller inaktivera övervakning av Application vit listning i Azure Security Center |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Tillåt samtidiga anslutningar till Internet eller en Windows-domän |Ange om du vill förhindra att datorer ansluter till både ett domänbaserat nätverk och ett icke-domänbaserat nätverk på samma gång. Värdet 0 tillåter samtidiga anslutningar och värdet 1 blockerar dem. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |API-appen bör endast vara tillgänglig via HTTPS v2 |Aktivera eller inaktivera övervakning av användningen av HTTPS i API app v2 |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Program namn (stöder jokertecken) |En semikolonavgränsad lista med namnen på de program som ska installeras. t. ex. "Microsoft SQL Server 2014 (64-bitars); Microsoft Visual Studio Code "eller" Microsoft SQL Server 2014 * "(för att matcha alla program som börjar med" Microsoft SQL Server 2014 ") |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Granska processavslut |Anger om gransknings händelser genereras när en process har avslut ATS. Rekommenderas för att övervaka avslutning av kritiska processer. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Granska obegränsad nätverks åtkomst till lagrings konton |Aktivera eller inaktivera övervakning av nätverks åtkomst till lagrings kontot |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Granskning: Stäng av systemet omedelbart om det inte går att logga säkerhetsgranskningar |Granskar om systemet avslutas när det inte går att logga säkerhets händelser. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Certifikat tumavtrycken |En semikolonavgränsad lista över certifikat tumavtrycken som ska finnas under det betrodda rot certifikat arkivet (cert: \ LocalMachine\Root). t. ex. THUMBPRINT1; THUMBPRINT2; THUMBPRINT3 |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Diagnostikloggar i batch-konton måste vara aktiverade |Aktivera eller inaktivera övervakning av diagnostikloggar i batch-konton |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Diagnostikloggar i Händelsehubben måste vara aktive rad |Aktivera eller inaktivera övervakning av diagnostikloggar i Event Hub-konton |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Diagnostikloggar i search Services ska vara aktive rad |Aktivera eller inaktivera övervakning av diagnostikloggar i Azure Searchs tjänsten |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad |Aktivera eller inaktivera övervakning av diagnostikloggar i Service Fabric |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Disk kryptering bör tillämpas på virtuella datorer |Aktivera eller inaktivera övervakning av disk kryptering för virtuell dator |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Aktivera oskyddade gäst inloggningar |Anger om SMB-klienten ska tillåta oskyddade gäst inloggningar till en SMB-server. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer |Aktivera eller inaktivera övervakning av nätverk just-in-Time-åtkomst |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Hanterings portar bör stängas på dina virtuella datorer |Aktivera eller inaktivera övervakning av öppna hanterings portar på Virtual Machines |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen |Aktivera eller inaktivera övervakning av MFA för konton med Skriv behörighet i prenumerationen |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration |Aktivera eller inaktivera övervakning av MFA för konton med ägar behörigheter i prenumerationen |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Nätverksåtkomst: Fjärråtkomliga registersökvägar |Anger vilka register Sök vägar som ska vara tillgängliga över nätverket, oavsett vilka användare eller grupper som anges i åtkomst kontrol listan (ACL) för `winreg` register nyckeln. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Nätverks åtkomst: fjärranslutna register Sök vägar och underordnade sökvägar |Anger vilka register Sök vägar och underordnade sökvägar som kommer att vara tillgängliga över nätverket, oavsett vilka användare eller grupper som anges i åtkomst kontrol listan (ACL) för `winreg` register nyckeln. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Nätverksåtkomst: Resurser som kan användas anonymt |Anger vilka nätverks resurser som kan nås av anonyma användare. Standard konfigurationen för den här inställningen har en liten inverkan eftersom alla användare måste autentiseras innan de kan komma åt delade resurser på servern. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Återställnings konsolen: Tillåt diskett kopiering och åtkomst till alla enheter och alla mappar |Anger om du vill göra kommandot för att ställa in återställnings konsolen tillgängligt, vilket innebär att miljövariabler för återställnings konsolen kan anges. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Fjärrfelsökning bör inaktive ras för API-appen |Aktivera eller inaktivera övervakning av fjärr fel sökning för API-app |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Fjärrfelsökning bör inaktive ras för webb program |Aktivera eller inaktivera övervakning av fjärrfelsökning för webb program |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Kräv kvarhållning (i dagar) för loggar i batch-konton |Lagrings perioden för de begärda diagnostikloggar i dagar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Kräv kvarhållning (i dagar) av loggar i Azure Searchs tjänsten |Lagrings perioden för de begärda diagnostikloggar i dagar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Kräv kvarhållning (i dagar) för loggar i Event Hub-konton |Lagrings perioden för de begärda diagnostikloggar i dagar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Resurs grupp namn för lagrings konto (måste finnas) för att distribuera diagnostikinställningar för nätverks säkerhets grupper |Resurs gruppen som lagrings kontot kommer att skapas i. Resurs gruppen måste redan finnas. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Role-Based Access Control (RBAC) ska användas på Kubernetes Services |Aktivera eller inaktivera övervakning av Kubernetes Services utan RBAC-aktiverad |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel |Aktivera eller inaktivera övervakning av transparent datakryptering (TDE) med din egen nyckel support. TDE med ditt eget nyckel stöd ger ökad insyn i och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av separering av uppgifter. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel |Aktivera eller inaktivera övervakning av transparent datakryptering (TDE) med din egen nyckel support. TDE med ditt eget nyckel stöd ger ökad insyn i och kontroll över TDE-skyddet, ökad säkerhet med en HSM-baserad extern tjänst och befordran av separering av uppgifter. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Prefix för lagrings konto för regionalt lagrings konto för att distribuera diagnostikinställningar för nätverks säkerhets grupper |Det här prefixet kombineras med nätverks säkerhets gruppens plats för att bilda det skapade lagrings konto namnet. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras |Aktivera eller inaktivera rapportering av skalnings uppsättningar för virtuella datorer i System uppdateringar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras |Aktivera eller inaktivera rapportering av skalnings uppsättningar för virtuella datorer i System uppdateringar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Inaktivera multicast-namnmatchning |Anger om LLMNR, ett sekundärt namn matchnings protokoll som överför med multicast över en lokal under näts länk i ett enda undernät, är aktiverat. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Virtuella datorer ska migreras till nya Azure Resource Manager-resurser |Aktivera eller inaktivera övervakning av klassiska virtuella dator instanser |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas |Aktivera eller inaktivera övervakning av OS-sårbarheter för virtuella dator skalnings uppsättningar |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning |Aktivera eller inaktivera identifiering av VM-sårbarheter med en sårbarhets bedömnings lösning |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser |Granska SQL-hanterade instanser som inte har återkommande aktiverade sökningar efter sårbarhets bedömning. En sårbarhets bedömning kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (domän): Använd lokala brand Väggs regler |Anger om lokala administratörer tillåts att skapa lokala brand Väggs regler som tillämpas tillsammans med brand Väggs regler som kon figurer ATS av grupprincip för domän profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (domän): beteende för utgående anslutningar |Anger beteendet för utgående anslutningar för domän profilen som inte matchar en utgående brand Väggs regel. Standardvärdet 0 innebär att tillåta anslutningar och värdet 1 innebär att anslutningar blockeras. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (domän): beteende för utgående anslutningar |Anger beteendet för utgående anslutningar för domän profilen som inte matchar en utgående brand Väggs regel. Standardvärdet 0 innebär att tillåta anslutningar och värdet 1 innebär att anslutningar blockeras. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (domän): Visa aviseringar |Anger om Windows-brandväggen med avancerad säkerhet visar meddelanden till användaren när ett program blockeras från att ta emot inkommande anslutningar för domän profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (domän): Använd profil inställningar |Anger om Windows-brandväggen med avancerad säkerhet använder inställningarna för domän profilen för att filtrera nätverks trafik. Om du väljer av kommer Windows-brandväggen med avancerad säkerhet inte att använda någon av brand Väggs reglerna eller anslutnings säkerhets reglerna för den här profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (privat): tillämpa säkerhets regler för lokal anslutning |Anger om lokala administratörer får skapa anslutnings säkerhets regler som gäller tillsammans med anslutnings säkerhets regler som kon figurer ATS av grupprincip för den privata profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (privat): Använd lokala brand Väggs regler |Anger om lokala administratörer tillåts att skapa lokala brand Väggs regler som tillämpas tillsammans med brand Väggs regler som kon figurer ATS av grupprincip för den privata profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (privat): beteende för utgående anslutningar |Anger beteendet för utgående anslutningar för den privata profilen som inte matchar en utgående brand Väggs regel. Standardvärdet 0 innebär att tillåta anslutningar och värdet 1 innebär att anslutningar blockeras. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (privat): Visa aviseringar |Anger om Windows-brandväggen med avancerad säkerhet visar meddelanden till användaren när ett program blockeras från att ta emot inkommande anslutningar för den privata profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandväggen (privat): Använd profil inställningar |Anger om Windows-brandväggen med avancerad säkerhet använder inställningarna för den privata profilen för att filtrera nätverks trafik. Om du väljer av kommer Windows-brandväggen med avancerad säkerhet inte att använda någon av brand Väggs reglerna eller anslutnings säkerhets reglerna för den här profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (offentlig): tillämpa säkerhets regler för lokal anslutning |Anger om lokala administratörer får skapa anslutnings säkerhets regler som tillämpas tillsammans med anslutnings säkerhets regler som kon figurer ATS av grupprincip för den offentliga profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (offentlig): Använd lokala brand Väggs regler |Anger om lokala administratörer tillåts att skapa lokala brand Väggs regler som tillämpas tillsammans med brand Väggs regler som kon figurer ATS av grupprincip för den offentliga profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (offentlig): beteende för utgående anslutningar |Anger beteendet för utgående anslutningar för den offentliga profilen som inte matchar en utgående brand Väggs regel. Standardvärdet 0 innebär att tillåta anslutningar och värdet 1 innebär att anslutningar blockeras. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (offentlig): Visa aviseringar |Anger om Windows-brandväggen med avancerad säkerhet visar meddelanden till användaren när ett program blockeras från att ta emot inkommande anslutningar för den offentliga profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brandvägg (offentlig): Använd profil inställningar |Anger om Windows-brandväggen med avancerad säkerhet använder inställningarna för den offentliga profilen för att filtrera nätverks trafik. Om du väljer av kommer Windows-brandväggen med avancerad säkerhet inte att använda någon av brand Väggs reglerna eller anslutnings säkerhets reglerna för den här profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brand vägg: domän: Tillåt unicast-svar |Anger om Windows-brandväggen med avancerad säkerhet tillåter att den lokala datorn tar emot unicast-svar på utgående multicast-eller broadcast-meddelanden. för domän profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brand vägg: privat: Tillåt unicast-svar |Anger om Windows-brandväggen med avancerad säkerhet tillåter att den lokala datorn tar emot unicast-svar på utgående multicast-eller broadcast-meddelanden. för den privata profilen. |
|Granska HITRUST/HIPAA-kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Windows-brand vägg: offentlig: Tillåt unicast-svar |Anger om Windows-brandväggen med avancerad säkerhet tillåter att den lokala datorn tar emot unicast-svar på utgående multicast-eller broadcast-meddelanden. för den offentliga profilen. |

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
