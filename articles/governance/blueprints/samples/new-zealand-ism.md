---
title: New Zeelands ISM-ett begränsat skiss exempel
description: Översikt över det nya Zeeland ISM-exemplet med begränsade skisser. Det här skissexemplet hjälper kunderna att utvärdera specifika kontroller.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803938"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>New Zeelands ISM-ett begränsat skiss exempel

I det nya Zeeland ISM-exemplet med begränsade skisser får styrnings skydd-räler som använder [Azure policy](../../policy/overview.md) som hjälper dig att utvärdera vissa [Nya Zeelands information säkerhets manuella](https://www.nzism.gcsb.govt.nz/) kontroller. Den här skissen hjälper kunder att distribuera en central uppsättning principer för alla Azure-distribuerade arkitektur som måste implementera kontroller för nya Zeeland ISM-begränsade.

## <a name="control-mapping"></a>Kontrollmappning

Den [Azure policy kontroll mappningen](../../policy/samples/new-zealand-ism.md) innehåller information om princip definitioner som ingår i den här skissen och hur dessa princip definitioner mappar till **kontrollerna** i den nya Zeeland information Security-handboken. När den är tilldelad till en arkitektur utvärderas resurserna av Azure Policy för inkompatibilitet med tilldelade princip definitioner. Mer information finns i [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Distribuera

För att distribuera Azure-ritningar nya Zeeland ISM-ett begränsat skiss exempel måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

### <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Leta upp det **Nya Zeeland ISM** -exemplet med begränsade skisser under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av det nya Zeeland ISM-exemplet.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som ingår i skiss exemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

### <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men den ändringen kan flytta den bort från justeringen med nya Zeeland ISM-begränsade kontroller.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** , till exempel "första versionen som publicerats från New Zeelands ISM-exemplet med begränsade skisser". Välj därefter **Publicera** längst ned på sidan.

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
|Nya Zeeland ISM-begränsad|Principtilldelning|Lista över användare som måste ingå i administratörs gruppen för en virtuell Windows-dator|En semikolonavgränsad lista med användare som ska ingå i den lokala gruppen Administratörer; T. ex. administratör; Mina Användare1; myUser2|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lista över användare som måste undantas från administratörs gruppen för virtuella Windows-datorer|En semikolonavgränsad lista med användare som ska undantas i den lokala gruppen Administratörer; T. ex. administratör; Mina Användare1; myUser2|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lista över användare som administratörs gruppen för Windows VM-administratörer får bara innehålla|En semikolonavgränsad lista över alla förväntade medlemmar i den lokala gruppen Administratörer; T. ex. administratör; Mina Användare1; myUser2|
|Nya Zeeland ISM-begränsad|Principtilldelning|Log Analytics arbetsyte-ID för VM-agentens rapportering|ID (GUID) för Log Analytics arbets ytan där VM-agenter ska rapportera|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) måste vara aktive rad för Azure-tjänsten för front dörr|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Effekt för principen: anpassningsbara nätverks härdnings rekommendationer bör tillämpas på virtuella datorer som är riktade mot Internet|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Princip påverkan: det måste finnas fler än en ägare som har tilldelats din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Effekt för principen: disk kryptering bör tillämpas på virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för Function-appar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) ska använda det angivna läget för Application Gateway|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Krav för WAF-läge för Application Gateway|Förebyggande-eller identifierings läget måste vara aktiverat på den Application Gateway tjänsten|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: transparent datakryptering på SQL-databaser ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Windows OS som ska läggas till i omfånget till avbildningarna i galleriet för principen: Distribuera-konfigurera beroende agent så att de aktive ras på virtuella Windows-datorer|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: en Azure Active Directory administratör bör tillhandahållas för SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: endast säkra anslutningar till Azure-cachen för Redis måste vara aktiverade|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Windows-datorer som saknar några angivna medlemmar i gruppen Administratörer|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Windows-operativsystem som ska läggas till i omfånget till avbildningarna i galleriet för principen: [för hands version]: Log Analytics agent ska vara aktive rad för avbildningar av virtuella datorer|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget till avbildningarna i galleriet för principen: [för hands version]: Log Analytics agent måste vara aktive rad för avbildningar av virtuella datorer|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: lagrings konton bör begränsa nätverks åtkomsten|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Windows-operativsystem som ska läggas till i omfånget till avbildningarna i galleriet för principen: Distribuera-konfigurera beroende agent så att den är aktive rad i Windows Virtual Machine Scale Sets|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Windows-datorer som har extra konton i gruppen Administratörer|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: säker överföring till lagrings konton ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: brand vägg för webbaserade program (WAF) ska använda det angivna läget för Azures tjänst för front dörr|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Krav för WAF-läge för Azure-tjänsten för front dörr|Förebyggande-eller identifierings läget måste vara aktiverat på Azures frontend-tjänst|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: högst 3 ägare bör anges för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: [för hands version]: åtkomst till lagrings kontots offentliga åtkomst ska inte tillåtas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: en lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: CORS bör inte tillåta alla resurser att komma åt dina webb program|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Windows-webbservrar som inte använder säkra kommunikations protokoll|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lägsta TLS-version för Windows-webbservrar|Windows-webbservrar med lägre TLS-versioner kommer att utvärderas som icke-kompatibla|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget till avbildningarna i galleriet för principen: Log Analytics agent ska vara aktive rad i virtuella datorers skalnings uppsättningar för avbildningar av virtuella datorer|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Valfritt: lista med anpassade VM-avbildningar som har stöd för Windows-operativsystem som ska läggas till i omfånget till avbildningarna i galleriet för principen: Log Analytics agent ska vara aktive rad i virtuell dators skalnings uppsättningar för avbildningar av virtuella datorer|Mer information om gäst konfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: externa konton med Skriv behörighet bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: föråldrade konton bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: Funktionsapp bör endast vara tillgängligt via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: Azure-prenumerationer ska ha en logg profil för aktivitets loggen|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lista över resurs typer som ska ha diagnostiska loggar aktiverade||
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: system uppdateringar bör installeras på datorerna|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: Microsoft IaaSAntimalware-tillägget bör distribueras på Windows-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: webb programmet bör endast vara tillgängligt via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: Azure DDoS Protection standard ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: avancerad data säkerhet ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: avancerad data säkerhet ska aktive ras på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: övervaka saknade Endpoint Protection i Azure Security Center|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: aktivitets loggen ska behållas i minst ett år|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: API-appen bör endast vara tillgänglig via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: granska Windows-datorer där Windows Defender sårbarhet Guard inte är aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Windows-datorer där Windows Defender sårbarhet Guard inte är aktiverat|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Kompatibilitetstillstånd att rapportera för Windows-datorer där Windows Defender sårbarhet Guard inte är tillgängligt|Windows Defender sårbarhet Guard är bara tillgängligt från och med Windows 10/Windows Server med uppdatering 1709. Om det här värdet anges till icke-kompatibel visas datorer med äldre versioner av Windows Defender sårbarhet Guard (till exempel Windows Server 2012 R2) som icke-kompatibel. Om det här värdet anges till "kompatibel" visas de här datorerna som kompatibla.|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: system uppdateringar på Virtual Machine Scale set bör installeras|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för webb program|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfigurationen på dina datorer bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarheter i säkerhets konfigurationerna för behållaren bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: fjärrfelsökning bör inaktive ras för API Apps|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: granska Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: föråldrade konton med ägar behörigheter bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Påverkan för principen: Windows-datorer måste uppfylla kraven för "säkerhets inställningar-konto principer"|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Använd lösen ords historik för lokala Windows-konton för virtuell dator|Anger gränser för lösen ords åter användning – hur många gånger ett nytt lösen ord måste skapas för ett användar konto innan lösen ordet kan upprepas|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: Windows-datorer bör uppfylla kraven för "säkerhets inställningar-konto principer"|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Högsta ålder för lösen ord för lokala VM-konton i Windows|Anger det maximala antalet dagar som kan förflyta innan ett användar kontos lösen ord måste ändras. formatet för värdet är två heltal avgränsade med kommatecken, vilket anger ett inklusivt intervall|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lägsta ålder för lösen ord för lokala Windows-konton för virtuell dator|Anger det minsta antal dagar som måste förflyta innan användar kontots lösen ord kan ändras|
|Nya Zeeland ISM-begränsad|Principtilldelning|Minsta längd på lösen ord för lokala Windows-konton för virtuell dator|Anger det minsta antal tecken som ett användar konto lösen ord kan innehålla|
|Nya Zeeland ISM-begränsad|Principtilldelning|Lösen ordet måste uppfylla komplexitets kraven för lokala Windows-konton för virtuella datorer|Anger om lösen ordet för ett användar konto måste vara komplext. vid behov får ett komplext lösen ord inte innehålla en del av användarens konto namn eller fullständiga namn. vara minst 6 tecken långt; innehåller en blandning av versaler, gemener, siffror och icke-alfabetiska tecken|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: granska Linux-datorer som har konton utan lösen ord|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Inkludera Arc-anslutna servrar vid utvärdering av princip: granska Linux-datorer som har konton utan lösen ord|Genom att välja "sant" samtycker du till att debiteras månads vis per Arc-ansluten dator|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: externa konton med ägar behörigheter bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i Funktionsapp|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: [för hands version]: all Internet trafik ska dirigeras via din distribuerade Azure-brandvägg|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nya Zeeland ISM-begränsad|Principtilldelning|Gällande princip: sårbarheter på SQL-databaser bör åtgärdas|Mer information om effekter finns i [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).