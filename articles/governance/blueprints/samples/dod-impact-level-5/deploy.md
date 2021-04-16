---
title: Skissexempel för DoD:s effektnivå 5
description: Distribuera steg för skissexempel på DoD:s effektnivå 5, inklusive information om skissartefaktparametrar.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: b978b9fb30732c13785a6a425e5195daf67bae0f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377411"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Distribuera skissexempel för DoD:s effektnivå 5

För att Azure Blueprints skissexempel på försvarsdepartementets effektnivå 5 (DoD IL5) måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en prenumeration Azure Government kan du begära en [utvärderingsprenumeration](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Leta upp **skissexempel på DoD:s effektnivå 5** under _Andra exempel och_ välj Använd det här **exemplet.**

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skissnamn:** Ange ett namn på din kopia av skissexempel på DoD:s effektnivå 5.
   - **Definitionsplats:** Använd ellipsen och välj hanteringsgruppen där du vill spara kopian av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skissexempel kan anpassas efter din miljö och dina behov, men den ändringen kan flytta den från anpassningen till kontrollerna för DoD:s effektnivå 5.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **Ändringsanteckningar,** till exempel "Första versionen som publicerats från DoD IL5-skissexempel". Välj därefter **Publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopian av skissexempel har **publicerats** kan den tilldelas till en prenumeration i hanteringsgruppen som den sparades till. I det här steget anges parametrar för att göra varje distribution av skissexemplets kopia unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Tilldela skiss** överst på skissdefinitionssidan.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer:** Välj en eller flera av de prenumerationer som finns i hanteringsgruppen som du sparade din kopia av skissexempel till. Om du väljer mer än en prenumeration, skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelningsnamn:** Namnet fylls i automatiskt baserat på namnet på skissen.
       Ändra vid behov eller lämna det som det är.
     - **Plats:** Välj en region där den hanterade identiteten ska skapas. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Skissdefinitionsversion:** Välj **en publicerad** version av din kopia av skissexempel.

   - Låstilldelning

     Välj skissens låsinställning för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Låt standardalternativet _för system tilldelad_ hanterad identitet vara kvar.

   - Artefaktparametrar

     De parametrar som definieras i avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [Tabellen Artefaktparametrar.](#artifact-parameters-table)

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrollera statusen för distributionen genom att öppna skisstilldelningen.

> [!WARNING]
> Tjänsten Azure Blueprints och de inbyggda skissexemplen är **kostnadsfria**. Azures resurser [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) till att beräkna kostnaden för att köra de resurser som distribueras i skissexemplet.

## <a name="artifact-parameters-table"></a>Tabell med artefaktparametrar

I följande tabell visas en lista med skissartefaktens parametrar:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|DoD:s effektnivå 5|Principtilldelning|Lista över användare som måste ingå i gruppen Administratörer för virtuella Windows-datorer|En semikolonavgränsad lista över användare som ska ingå i den lokala gruppen Administratörer. Exempel: Administratör; myUser1; myUser2|
|DoD:s effektnivå 5|Principtilldelning|Lista över användare som undantas från gruppen Administratörer för virtuella Windows-datorer|En semikolonavgränsad lista över användare som ska undantas i den lokala gruppen Administratörer. Exempel: Administratör; myUser1; myUser2|
|DoD:s effektnivå 5|Principtilldelning|Lista över resurstyper som ska ha diagnostikloggar aktiverade||
|DoD:s effektnivå 5|Principtilldelning|Log Analytics-arbetsyte-ID för VM-agentrapportering|ID (GUID) för Log Analytics-arbetsytan där virtuella datorers agenter ska rapportera|
|DoD:s effektnivå 5|Principtilldelning|Lista över regioner där Network Watcher ska aktiveras|Om du vill se en fullständig lista över regioner använder du Get-AzLocation,|
|DoD:s effektnivå 5|Principtilldelning|Lägsta TLS-version för Windows-webbservrar|Lägsta TLS-protokollversion som ska vara aktiverad på Windows-webbservrar|
|DoD:s effektnivå 5|Principtilldelning|Senaste PHP-version|Senaste PHP-version som stöds för App Services|
|DoD:s effektnivå 5|Principtilldelning|Senaste Java-version|Senaste Java-version som stöds för App Services|
|DoD:s effektnivå 5|Principtilldelning|Senaste Windows Python-versionen|Senaste Python-version som stöds för App Services|
|DoD:s effektnivå 5|Principtilldelning|Senaste Linux Python-versionen|Senaste Python-version som stöds för App Services|
|DoD:s effektnivå 5|Principtilldelning|Valfritt: Lista över avbildningar av virtuella Windows-datorer som stöder Log Analytics-agent som ska läggas till i granskningsomfånget|En semikolonavgränsad lista med bilder. Exempel: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD:s effektnivå 5|Principtilldelning|Valfritt: Lista över virtuella Linux-datoravbildningar som stöder Log Analytics-agent att lägga till i granskningsomfånget|En semikolonavgränsad lista med bilder. Exempel: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Det bör finnas fler än en ägare tilldelad till din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Diskkryptering ska tillämpas på virtuella datorer|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: E-postavisering till prenumerationsägare om aviseringar med hög allvarlighetsgrad ska vara aktiverat|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Fjärrfelsökning ska vara inaktiverat för funktionsappar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att ".NET Framework"-versionen är den senaste, om den används som en del av funktionsappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: transparent datakryptering på SQL-databaser ska vara aktiverat|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Sårbarhetsbedömning ska aktiveras på dina SQL-hanterade instanser|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "PHP-versionen" är den senaste, om den används som en del av API-appen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: En Azure Active Directory bör etableras för SQL-servrar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Endast säkra anslutningar till Redis Cache ska aktiveras|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Endpoint Protection-lösningen ska installeras på VM-skalningsuppsättningar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Granska obegränsad nätverksåtkomst till lagringskonton|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Avancerade datasäkerhetsinställningar för SQL Managed Instance bör innehålla en e-postadress för att ta emot säkerhetsaviseringar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Säkerhetsrisker i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Säker överföring till lagringskonton ska aktiveras|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Anpassningsbara programkontroller ska aktiveras på virtuella datorer|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Geo-redundant säkerhetskopiering ska aktiveras för Azure Database for PostgreSQL|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "Java-versionen" är den senaste, om den används som en del av webbappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Högst 3 ägare bör anges för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: En e-postadress för säkerhetskontakten ska anges för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: CORS bör inte tillåta att alla resurser får åtkomst till dina webbprogram|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Externa konton med skrivbehörighet ska tas bort från din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Externa konton med läsbehörighet bör tas bort från din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Inaktuella konton bör tas bort från din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Funktionsappen bör endast vara tillgänglig via HTTPS|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "Python-versionen" är den senaste, om den används som en del av webbappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Se till att "Python-versionen" är den senaste, om den används som en del av funktionsappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "PHP-versionen" är den senaste, om den används som en del av WEBBappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "Python-versionen" är den senaste, om den används som en del av API-appen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Sårbarheter bör åtgärdas av en lösning för sårbarhetsbedömning|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Geo-redundant säkerhetskopiering ska aktiveras för Azure Database for MySQL|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att ".NET Framework"-versionen är den senaste, om den används som en del av webbappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Systemuppdateringar ska installeras på dina datorer|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "Java-versionen" är den senaste, om den används som en del av API-appen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att HTTP-versionen är den senaste, om den används för att köra webbappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Den senaste TLS-versionen ska användas i din API-app|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: MFA ska vara aktiverat för konton med skrivbehörighet för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Avancerade datasäkerhetsinställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhetsaviseringar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att HTTP-versionen är den senaste, om den används för att köra API-appen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Microsoft IaaSAntimalware-tillägget ska distribueras på Windows-servrar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "Java-versionen" är den senaste, om den används som en del av funktionsappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Åtkomst via Internetuppriktad slutpunkt ska vara begränsad|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Security Center standardprisnivån ska väljas|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Granska användningen av anpassade RBAC-regler|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Webbappen ska endast vara tillgänglig via HTTPS|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Granskning på SQL Server ska vara aktiverat|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Log Analytics-agenten ska installeras på virtuella datorer|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: DDoS Protection Standard ska vara aktiverat|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att "PHP-versionen" är den senaste, om den används som en del av funktionsappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Avancerad datasäkerhet ska vara aktiverat på SQL-servrarna|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Avancerad datasäkerhet ska vara aktiverat på dina SQL-hanterade instanser|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: E-postaviseringar till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL Managed Instance|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Övervaka saknat Endpoint Protection i Azure Security Center|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Just-In-Time-åtkomstkontroll för nätverk ska tillämpas på virtuella datorer|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Ett telefonnummer för säkerhetskontakten ska anges för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Service Fabric kluster endast ska använda Azure Active Directory för klientautentisering|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: API-appen ska endast vara tillgänglig via HTTPS|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Advanced Threat Protection-typer ska anges till Alla i SQL Managed Instance Advanced Data Security inställningar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Geo-redundant lagring ska aktiveras för lagringskonton|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att ".NET Framework"-versionen är den senaste, om den används som en del av API-appen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Systemuppdateringar på VM-skalningsuppsättningar ska installeras|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: E-postaviseringar till administratörer och prenumerationsägare bör aktiveras i avancerade inställningar för datasäkerhet i SQL Server|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Fjärrfelsökning ska vara inaktiverat för webbprogram|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Långsiktig geo-redundant säkerhetskopiering ska aktiveras för Azure SQL databaser|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Kontrollera att HTTP-versionen är den senaste, om den används för att köra funktionsappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: MFA ska aktiveras för konton med läsbehörighet för din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Advanced Threat Protection-typer ska anges till Alla i SQL Server Advanced Data Security inställningar|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Sårbarheter i containersäkerhetskonfigurationer bör åtgärdas|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Fjärrfelsökning bör inaktiveras för API Apps|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Inaktuella konton med ägarbehörighet bör tas bort från din prenumeration|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Sårbarhetsbedömning ska aktiveras på SQL-servrarna|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Log Analytics-agenten ska installeras på Virtual Machine Scale Sets|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Den senaste TLS-versionen ska användas i webbappen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Externa konton med ägarbehörighet ska tas bort från prenumerationen|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: Den senaste TLS-versionen ska användas i din funktionsapp|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Effekt för princip: [Förhandsversion]: Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|
|DoD:s effektnivå 5|Principtilldelning|Principeffekt: Sårbarheter i DINA SQL-databaser bör åtgärdas|Azure Policy effekt för den här principen. Mer information om effekter finns i https://aka.ms/policyeffects|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera skissexempel på DoD:s effektnivå 5 kan du gå till följande artiklar för att lära dig mer om skissen och kontrollmappningen:

> [!div class="nextstepaction"]
> [Skiss för DoD:s effektnivå 5 – översikt](./index.md) 
>  [Skiss för DoD:s effektnivå 5 – Kontrollmappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).