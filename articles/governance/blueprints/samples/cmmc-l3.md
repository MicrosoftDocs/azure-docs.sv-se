---
title: Skiss exempel på CMMC nivå 3
description: Översikt över skiss exemplet CMMC Level 3. Det här skissexemplet hjälper kunderna att utvärdera specifika kontroller.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572660"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Skiss exempel på CMMC nivå 3

Skiss exemplet CMMC Level 3 ger styrnings skydd-räler med [Azure policy](../../policy/overview.md) som hjälper dig att utvärdera vissa [CMMC-ramverk (cybersäkerhet förfallo modell certifiering)](https://www.acq.osd.mil/cmmc/index.html) . Den här skissen hjälper kunder att distribuera en central uppsättning principer för alla Azure-distribuerade arkitektur som måste implementera kontroller för CMMC nivå 3.

## <a name="control-mapping"></a>Kontrollmappning

Den [Azure policy kontroll mappningen](../../policy/samples/cmmc-l3.md) innehåller information om princip definitioner som ingår i den här skissen och hur dessa princip definitioner mappar till **kontrollerna** i CMMC-ramverket. När den är tilldelad till en arkitektur utvärderas resurserna av Azure Policy för inkompatibilitet med tilldelade princip definitioner. Mer information finns i [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Distribuera

Följande steg måste vidtas för att distribuera ett skiss exempel på Azure-skisser CMMC Level 3:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

### <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Hitta skiss exemplet **CMMC Level 3** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet CMMC Level 3.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som ingår i skiss exemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

### <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men den ändringen kan flytta den bort från justeringen med CMMC nivå 3-kontroller.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från skiss EXEMPLET på CMMC nivå 3". Välj därefter **Publicera** längst ned på sidan.

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

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|CMMC nivå 3|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av principer för gäst konfiguration|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator. Mer information finns på https://aka.ms/policy-pricing|
|CMMC nivå 3|Principtilldelning|Lista över användare som måste undantas från administratörs gruppen för virtuella Windows-datorer|En semikolonavgränsad lista med användare som ska undantas i den lokala gruppen Administratörer; T. ex. administratör; Mina Användare1; myUser2|
|CMMC nivå 3|Principtilldelning|Lista över användare som måste ingå i administratörs gruppen för en virtuell Windows-dator|En semikolonavgränsad lista med användare som ska ingå i den lokala gruppen Administratörer; T. ex. administratör; Mina Användare1; myUser2|
|CMMC nivå 3|Principtilldelning|Log Analytics arbetsyte-ID för VM-agentens rapportering|ID (GUID) för Log Analytics arbets ytan där VM-agenter ska rapportera|
|CMMC nivå 3|Principtilldelning|Tillåtna Elliptic kurv namn|Listan över tillåtna kurv namn för certifikat från Elliptic Curve-kryptografi.|
|CMMC nivå 3|Principtilldelning|Tillåtna nyckel typer|Listan över tillåtna nyckel typer|
|CMMC nivå 3|Principtilldelning|Tillåt användning av värd nätverk för Kubernetes-kluster poddar|Ange värdet sant om Pod får använda värd nätverket annars falskt.|
|CMMC nivå 3|Principtilldelning|Granska ändring av autentiseringsprincip|Anger om gransknings händelser genereras när ändringar görs i autentiseringsprincip. Den här inställningen är användbar för att spåra ändringar på domän nivå och förtroende på skogs nivå och behörigheter som beviljas användar konton eller grupper.|
|CMMC nivå 3|Principtilldelning|Granska ändring av auktoriseringsprincip|Anger om gransknings händelser ska genereras för tilldelning och borttagning av användar rättigheter i användar rättigheter, ändringar i objekt behörighet för säkerhetstoken, ändringar av resurs attribut och princip ändringar för central åtkomst för fil system objekt.|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Backup ska aktive ras för Virtual Machines|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Cognitive Services konton bör begränsa nätverks åtkomsten|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure API för FHIR bör använda en kundhanterad nyckel (CMK) för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) måste vara aktive rad för Azure-tjänsten för front dörr|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: åtkomst till offentligt nätverk ska inaktive ras för Cognitive Services konton|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: CORS bör inte tillåta alla resurser att komma åt dina funktions program|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Effekt för principen: anpassningsbara nätverks härdnings rekommendationer bör tillämpas på virtuella datorer som är riktade mot Internet|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Princip påverkan: det måste finnas fler än en ägare som har tilldelats din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Effekt för principen: disk kryptering bör tillämpas på virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: e-postmeddelande till Prenumerationens ägare för aviseringar med hög allvarlighets grad måste vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: nyckel valvet ska ha rensnings skyddet aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för Function-appar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för Key Vault ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for MariaDB|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: CORS ska inte tillåta varje domän åtkomst till ditt API för FHIR|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Windows-datorer uppfyller kraven för säkerhets alternativ-nätverks säkerhet|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: tillåten-regler i din princip för anpassningsbar program kontroll bör uppdateras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) ska använda det angivna läget för Application Gateway|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: nycklar ska ha förfallo datum inställda|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: transparent datakryptering på SQL-databaser ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att PHP-versionen är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Key Vault har mjuk borttagning aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en Azure Active Directory administratör bör tillhandahållas för SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: endast säkra anslutningar till Azure-cachen för Redis måste vara aktiverade|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: infrastruktur kryptering måste vara aktiverat för Azure Database for PostgreSQL servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för App Service ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Windows-datorer uppfyller kraven för system gransknings principer-princip ändring|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Cognitive Services konton ska aktivera data kryptering|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: SSH-åtkomst från Internet ska blockeras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: ej anslutna diskar ska vara krypterade|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för lagring ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: lagrings konton bör begränsa nätverks åtkomsten|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: CORS bör inte tillåta alla resurser åtkomst till din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Distribuera Avancerat skydd på lagrings konton|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: variabler för Automation-konton ska vara krypterade|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: diagnostikloggar i IoT Hub ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: infrastruktur kryptering måste vara aktiverat för Azure Database for MySQL servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa säkerhets åtgärder (Microsoft. Security/securitySolutions/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Windows-datorer måste uppfylla kraven för "säkerhets alternativ – nätverks åtkomst"|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: säker överföring till lagrings konton ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Monitor ska samla in aktivitets loggar från alla regioner|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) ska använda det angivna läget för Azures tjänst för front dörr|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: lagrings konton ska ha infrastruktur kryptering|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for PostgreSQL|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Windows-datorer måste uppfylla kraven för "säkerhets alternativ-User Account Control"|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för servrar måste vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: högst 3 ägare bör anges för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: lagrings kontots offentliga åtkomst ska inte tillåtas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för Kubernetes ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: brand väggen måste vara aktive rad på Key Vault|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: CORS bör inte tillåta alla resurser att komma åt dina webb program|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: behållar register ska krypteras med en kundhanterad nyckel (CMK)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: externa konton med Skriv behörighet bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: offentlig nätverks åtkomst ska inaktive ras för PostgreSQL-flexibla servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarheter i Azure Container Registry avbildningar bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: externa konton med Läs behörighet bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för SQL-servrar på datorer ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Cognitive Services-konton ska aktivera data kryptering med kundhanterad nyckel|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: föråldrade konton bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Funktionsapp bör endast vara tillgängligt via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: e-postavisering om aviseringar med hög allvarlighets grad ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: lagrings kontot bör använda kundhanterad nyckel för kryptering|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att PHP-versionen är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: nycklar ska vara den angivna kryptografiska typen RSA eller EC|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure-prenumerationer ska ha en logg profil för aktivitets loggen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: både operativ system och data diskar i Azure Kubernetes service-kluster bör krypteras med Kundhanterade nycklar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Defender för Azure SQL Database-servrar måste vara aktiverade|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Datautforskaren kryptering i vila bör använda en kundhanterad nyckel|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: nycklar som använder RSA-kryptografi bör ha en angiven minsta nyckel storlek|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for MySQL|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Kubernetes Cluster poddar bör endast använda godkända värd nätverk och port intervall|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: system uppdateringar bör installeras på datorerna|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Windows-datorer uppfyller kraven för "system gransknings principer-Privilege use"|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Stream Analytics jobb ska använda Kundhanterade nycklar för att kryptera data|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Microsoft IaaSAntimalware-tillägget bör distribueras på Windows-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: alla nätverks portar bör begränsas för nätverks säkerhets grupper som är kopplade till den virtuella datorn|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Security Center standard pris nivå ska väljas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska användningen av anpassade RBAC-regler|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: webb programmet bör endast vara tillgängligt via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: granskning på SQL Server måste vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Log Analytics agenten ska installeras på virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: avancerad data säkerhet ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: avancerad data säkerhet ska aktive ras på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Role-Based Access Control (RBAC) ska användas på Kubernetes Services|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: virtuella datorer ska ha gäst konfigurations tillägget|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: övervaka saknade Endpoint Protection i Azure Security Center|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: aktivitets loggen ska behållas i minst ett år|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: offentlig nätverks åtkomst ska inaktive ras för PostgreSQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Princip påverkan: Distribuera Avancerat skydd för Cosmos DB konton|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: diagnostikloggar i App Services ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: API-appen bör endast vara tillgänglig via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. ClassicNetwork/networkSecurityGroups/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. SQL/Server/firewallRules/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: icke-Internetbaserade virtuella datorer bör skyddas med nätverks säkerhets grupper|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Defender för behållar register ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Azure Data Box jobb bör aktivera Double kryptering för data i vila på enheten|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: system uppdateringar på Virtual Machine Scale set bör installeras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Microsoft Antimalware för Azure ska konfigureras att automatiskt uppdatera skydds-signaturer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: en aktivitets logg avisering ska finnas för vissa princip åtgärder (Microsoft. Authorization/policyAssignments/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: åtkomst till offentligt nätverk ska inaktive ras för flexibla MySQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för webb program|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: certifikat som använder RSA-kryptografi ska ha den angivna minsta nyckel storleken|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: behållar register ska inte tillåta obegränsad nätverks åtkomst|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Princip påverkan: framtvinga SSL-anslutning måste vara aktiverat för PostgreSQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: tillägg för gäst konfiguration bör distribueras till virtuella Azure-datorer med systemtilldelad hanterad identitet|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: offentlig nätverks åtkomst ska inaktive ras för MySQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Windows-datorer som inte lagrar lösen ord med omvänd kryptering|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Windows-datorer måste uppfylla kraven för tilldelning av användar rättigheter|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfigurationen på dina datorer bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: RDP-åtkomst från Internet ska blockeras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Linux-datorer som inte har behörigheterna passwd-fil inställd på 0644|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: undernät ska associeras med en nätverks säkerhets grupp|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Princip påverkan: framtvinga SSL-anslutning måste vara aktiverat för MySQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfigurationerna för behållaren bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för API Apps|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: föråldrade konton med ägar behörigheter bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Princip påverkan: dubbel kryptering bör vara aktiverat på Azure Datautforskaren|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Log Analytics agenten ska installeras på Virtual Machine Scale Sets|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: disk kryptering måste vara aktiverat på Azure Datautforskaren|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: granska Linux-datorer som har konton utan lösen ord|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Azure Synapse-arbetsytor bör använda Kundhanterade nycklar för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: externa konton med ägar behörigheter bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i Funktionsapp|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Påverkan för principen: Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: all Internet trafik ska dirigeras via din distribuerade Azure-brandvägg|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: Linux-datorer måste uppfylla kraven för Azures säkerhets bas linje|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: offentlig nätverks åtkomst ska inaktive ras för MariaDB-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: sårbarheter på SQL-databaser bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Gällande princip: nycklar som använder Elliptic Curve-kryptografi ska ha de angivna kurv namnen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nivå 3|Principtilldelning|Namnrymder som undantas från utvärdering av princip: Kubernetes Cluster poddar bör endast använda godkända värd nätverk och port intervall|Lista över Kubernetes-namnrymder som ska undantas från princip utvärderingen.|
|CMMC nivå 3|Principtilldelning|Senaste Java-version för App Services|Senaste Java-version som stöds för App Services|
|CMMC nivå 3|Principtilldelning|Senaste python-version för Linux för App Services|Senaste python-version som stöds för App Services|
|CMMC nivå 3|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget vid granskning Log Analytics agent distribution|Exempel värde: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage '|
|CMMC nivå 3|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Windows OS som ska läggas till i omfånget vid granskning Log Analytics agent distribution|Exempel värde: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage '|
|CMMC nivå 3|Principtilldelning|Lista över regioner där Network Watcher ska aktive ras|Granska om Network Watcher inte är aktiverat för en eller flera regioner.|
|CMMC nivå 3|Principtilldelning|Lista över resurs typer som ska ha diagnostiska loggar aktiverade||
|CMMC nivå 3|Principtilldelning|Högsta värde i tillåtet värd port intervall som poddar kan använda i värd nätverkets namnrymd|Det maximala värdet i det tillåtna port intervallet för värd porten som poddar kan använda i värd nätverkets namnrymd.|
|CMMC nivå 3|Principtilldelning|Minsta RSA-nyckel storlek för nycklar|Den minsta nyckel storleken för RSA-nycklar.|
|CMMC nivå 3|Principtilldelning|Lägsta RSA-nyckel storlek certifikat|Den minsta nyckel storleken för RSA-certifikat.|
|CMMC nivå 3|Principtilldelning|Lägsta TLS-version för Windows-webbservrar|Windows-webbservrar med lägre TLS-versioner kommer att utvärderas som icke-kompatibla|
|CMMC nivå 3|Principtilldelning|Minsta värde i tillåtet värd port intervall som poddar kan använda i värd nätverkets namnrymd|Det minsta värdet i tillåtet värd port intervall som poddar kan använda i värd nätverkets namnrymd.|
|CMMC nivå 3|Principtilldelning|Läges krav|Läge som krävs för alla WAF-principer|
|CMMC nivå 3|Principtilldelning|Läges krav|Läge som krävs för alla WAF-principer|
|CMMC nivå 3|Principtilldelning|Tillåtna värd Sök vägar för Pod hostPath-volymer som ska användas|De värd Sök vägar som tillåts för Pod hostPath-volymer som ska användas. Ange en tom Sök vägs lista för att blockera alla värd Sök vägar.|
|CMMC nivå 3|Principtilldelning|Nätverksåtkomst: Fjärråtkomliga registersökvägar|Anger vilka register Sök vägar som ska vara tillgängliga över nätverket, oavsett vilka användare eller grupper som anges i åtkomst kontrol listan (ACL) för `winreg` register nyckeln.|
|CMMC nivå 3|Principtilldelning|Nätverks åtkomst: fjärranslutna register Sök vägar och underordnade sökvägar|Anger vilka register Sök vägar och underordnade sökvägar som kommer att vara tillgängliga över nätverket, oavsett vilka användare eller grupper som anges i åtkomst kontrol listan (ACL) för `winreg` register nyckeln.|
|CMMC nivå 3|Principtilldelning|Nätverksåtkomst: Resurser som kan användas anonymt|Anger vilka nätverks resurser som kan nås av anonyma användare. Standard konfigurationen för den här inställningen har en liten inverkan eftersom alla användare måste autentiseras innan de kan komma åt delade resurser på servern.|
|CMMC nivå 3|Principtilldelning|Nätverks säkerhet: Konfigurera krypterings typer som tillåts för Kerberos|Anger krypterings typer som Kerberos kan använda.|
|CMMC nivå 3|Principtilldelning|Nätverkssäkerhet: Autentiseringsnivå för LAN Manager|Ange vilket autentiseringsprotokoll för Challenge-Response som används för nätverks inloggningar. Det här alternativet påverkar den nivå av autentiseringsprotokollet som används av klienter, vilken säkerhets nivå som förhandlas fram och vilken autentiseringsnivå som godkänns av servrarna.|
|CMMC nivå 3|Principtilldelning|Nätverkssäkerhet: Signeringskrav för LDAP-klient|Ange den nivå av data signering som begärs för klienter som utfärdar LDAP BIND-begäranden.|
|CMMC nivå 3|Principtilldelning|Nätverkssäkerhet: Lägsta sessionssäkerhet för NTLM SSP baserade (inklusive säker RPC) klienter|Anger vilka beteenden som tillåts av klienter för program som använder NTLM Security Support Provider (SSP). SSP-gränssnittet (SSPI) används av program som behöver Authentication Services. [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)Mer information finns i.|
|CMMC nivå 3|Principtilldelning|Nätverkssäkerhet: Lägsta sessionssäkerhet för NTLM SSP-baserade (inklusive säkra RPC) servrar|Anger vilka beteenden som tillåts av servrar för program som använder NTLM Security Support Provider (SSP). SSP-gränssnittet (SSPI) används av program som behöver Authentication Services.|
|CMMC nivå 3|Principtilldelning|Den senaste PHP-versionen för App Services|Den senaste PHP-versionen som stöds för App Services|
|CMMC nivå 3|Principtilldelning|Krävda kvarhållningsperiod (dagar) för IoT Hub diagnostikloggar||
|CMMC nivå 3|Principtilldelning|Namnet på resurs gruppen för Network Watcher|Namnet på resurs gruppen för NetworkWatcher, till exempel NetworkWatcherRG. Det här är resurs gruppen där nätverks Watcher finns.|
|CMMC nivå 3|Principtilldelning|Obligatorisk gransknings inställning för SQL-servrar||
|CMMC nivå 3|Principtilldelning|Azure Data Box SKU: er som stöder programvarubaserad dubbel kryptering|Listan med Azure Data Box SKU: er som stöder programvarubaserad dubbel kryptering|
|CMMC nivå 3|Principtilldelning|UAC: läge för administratörs godkännande för det inbyggda administratörs kontot|Anger beteendet för läget för administratörs godkännande för det inbyggda administratörs kontot.|
|CMMC nivå 3|Principtilldelning|UAC: beteende för fråga om utökad behörighet för administratörer i läget för administratörs godkännande|Anger beteendet för fråga om utökad behörighet för administratörer.|
|CMMC nivå 3|Principtilldelning|UAC: identifiera programinstallationer och fråga om utökade privilegier|Anger beteendet för identifiering av program installationer för datorn.|
|CMMC nivå 3|Principtilldelning|UAC: kör alla administratörer i läget för administratörs godkännande|Anger beteendet för alla princip inställningar för User Account Control (UAC) för datorn.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som kan framtvinga avstängning från ett fjärrsystem|Anger vilka användare och grupper som tillåts stänga av datorn från en annan plats i nätverket.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som nekas åtkomst till den här datorn från nätverket|Anger vilka användare eller grupper som uttryckligen förbjuds från att ansluta till datorn över nätverket.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som nekas lokal inloggning|Anger vilka användare och grupper som uttryckligen inte får logga in på datorn.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som nekas inloggning som batch-jobb|Anger vilka användare och grupper som uttryckligen inte får logga in på datorn som ett batchjobb (dvs. schemalagd aktivitet).|
|CMMC nivå 3|Principtilldelning|Användare och grupper som nekas inloggning som en tjänst|Anger vilka tjänst konton som uttryckligen inte tillåts registrera en process som en tjänst.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som nekas inloggning via Fjärrskrivbordstjänster|Anger vilka användare och grupper som uttryckligen inte får logga in på datorn via Terminal Services/fjärr skrivbords klienten.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som kan återställa filer och kataloger|Anger vilka användare och grupper som tillåts att kringgå fil-, katalog-, register-och andra beständiga objekt behörigheter vid återställning av säkerhetskopierade filer och kataloger.|
|CMMC nivå 3|Principtilldelning|Användare och grupper som kan stänga av systemet|Anger vilka användare och grupper som är inloggade lokalt på datorerna i din miljö som tillåts stänga av operativ systemet med kommandot Stäng av.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan logga in lokalt|Anger vilka fjärran vändare i nätverket som tillåts ansluta till datorn. Detta omfattar inte Anslutning till fjärrskrivbord.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan säkerhetskopiera filer och kataloger|Anger användare och grupper som tillåts att kringgå fil-och katalog behörigheter för att säkerhetskopiera systemet.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan ändra system tiden|Anger vilka användare och grupper som tillåts att ändra tid och datum på datorns interna klocka.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan ändra tidszon|Anger vilka användare och grupper som tillåts att ändra datorns tidszon.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan skapa ett token-objekt|Anger vilka användare och grupper som tillåts att skapa en åtkomsttoken, vilket kan ge utökade behörigheter för åtkomst till känsliga data.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan logga in lokalt|Anger vilka användare eller grupper som interaktivt kan logga in på datorn. Användare som försöker logga in via Anslutning till fjärrskrivbord eller IIS kräver även den här användar rättigheten.|
|CMMC nivå 3|Principtilldelning|Användare av fjärrskrivbord|Användare eller grupper som kan logga in via Fjärrskrivbordstjänster|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan hantera gransknings-och säkerhets loggar|Anger användare och grupper som tillåts att ändra gransknings alternativ för filer och kataloger och rensa säkerhets loggen.|
|CMMC nivå 3|Principtilldelning|Användare eller grupper som kan bli ägare till filer eller andra objekt|Anger vilka användare och grupper som tillåts att bli ägare till filer, mappar, register nycklar, processer eller trådar. Den här användar rättigheten hoppar över behörigheter som är på plats för att skydda objekt för att ge ägarskap till den angivna användaren.|

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).