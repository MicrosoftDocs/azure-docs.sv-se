---
title: CIS-Microsoft Azure grunderna benchmark v 1.3.0 skiss exempel
description: Översikt över Microsoft Azure CIS-exemplet för 1.3.0s benchmark v-utkast. Det här skissexemplet hjälper kunderna att utvärdera specifika kontroller.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566004"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>CIS-Microsoft Azure grunderna benchmark v 1.3.0 skiss exempel

Analys exemplet för CIS Microsoft Azureis benchmark v-1.3.0 ger styrnings skydd-räler som använder [Azure policy](../../policy/overview.md) som hjälper dig att utvärdera vissa CIS-Microsoft Azure grunderna för benchmark v 1.3.0-rekommendationer. Den här skissen hjälper kunder att distribuera en central uppsättning principer för Azure-distribuerad arkitektur som måste implementera CIS-Microsoft Azure grunderna i 1.3.0-rekommendationer.

## <a name="recommendation-mapping"></a>Rekommendationsmappning

[Mappningen av Azure policy rekommendation](../../policy/samples/cis-azure-1-3-0.md) innehåller information om princip definitioner som ingår i den här skissen och hur dessa princip definitioner mappar till **rekommendationerna** i CIS Microsoft Azure grunderna benchmark v 1.3.0. När den är tilldelad till en arkitektur utvärderas resurserna av Azure Policy för inkompatibilitet med tilldelade princip definitioner. Mer information finns i [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Distribuera

För att distribuera Azure-skisser CIS-Microsoft Azure grunderna benchmark v 1.3.0 skiss, måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

### <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Leta reda på exempel **Microsoft Azure på benchmark-1.3.0 för benchmark v** -utkast under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av exempel på benchmark-exempel för Microsoft Azure grunderna.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som ingår i skiss exemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

### <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från anpassningen till CIS Microsoft Azure grunderna för benchmark-1.3.0.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** , till exempel "första versionen som publicerats från CIS-Microsoft Azure grunderna i benchmark-utkastet". Välj därefter **Publicera** längst ned på sidan.

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
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Lista över tillägg för virtuella datorer som godkänns för användning|En semikolonavgränsad lista över virtuella dator tillägg; Om du vill se en fullständig lista över tillägg använder du kommandot Azure PowerShell Get-AzVMExtensionImage|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Azure Data Lake Store ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Effekt för principen: disk kryptering bör tillämpas på virtuella datorer|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: nyckel valvet ska ha rensnings skyddet aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: se till att API-appen har klient certifikat (inkommande klient certifikat) inställd på|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: hanterad identitet ska användas i Funktionsapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för Key Vault ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: anpassade prenumerations ägare roller bör inte finnas|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: nycklar ska ha förfallo datum inställda|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: transparent datakryptering på SQL-databaser ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att PHP-versionen är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en Azure Active Directory administratör bör tillhandahållas för SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för App Service ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: lagrings konton bör begränsa nätverks åtkomst med hjälp av regler för virtuella nätverk|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: hanterad identitet ska användas i din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: SSH-åtkomst från Internet ska blockeras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: ej anslutna diskar ska vara krypterade|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för lagring ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: lagrings konton bör begränsa nätverks åtkomsten|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Logic Apps ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i IoT Hub ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: FTPS måste anges i Funktionsapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa säkerhets åtgärder (Microsoft. Security/securitySolutions/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa säkerhets åtgärder (Microsoft. Security/securitySolutions/Write)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: säker överföring till lagrings konton ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i batch-konton måste vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: FTPS måste anges i din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för servrar måste vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: lagrings kontots offentliga åtkomst ska inte tillåtas|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för Kubernetes ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: anslutnings begränsning ska vara aktiverat för PostgreSQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att WEBBAPP har klient certifikat (inkommande klient certifikat) inställd på|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: externa konton med Skriv behörighet bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: externa konton med Läs behörighet bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för SQL-servrar på datorer ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: e-postavisering om aviseringar med hög allvarlighets grad ska aktive ras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: lagrings kontot bör använda kundhanterad nyckel för kryptering|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att PHP-versionen är den senaste, om den används som en del av webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: se till att "python-version" är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: Azure Defender för Azure SQL Database-servrar måste vara aktiverade|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Händelsehubben måste vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: system uppdateringar bör installeras på datorerna|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: SQL-servrar bör konfigureras med 90 dagars gransknings kvarhållning eller högre.|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra webbappen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: autentisering ska vara aktiverat på din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: hemligheter ska ha förfallo datum inställda|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: FTPS måste anges i din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att Java-version är den senaste, om den används som en del av Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: webb programmet bör endast vara tillgängligt via HTTPS|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: granskning på SQL Server måste vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: avancerad data säkerhet ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: avancerad data säkerhet ska aktive ras på SQL-hanterad instans|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: Role-Based Access Control (RBAC) ska användas på Kubernetes Services|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: övervaka saknade Endpoint Protection i Azure Security Center|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Sök tjänster ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i App Services ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/securityRules/Write)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. Network/networkSecurityGroups/Write)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. SQL/Server/firewallRules/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering bör finnas för vissa administrativa åtgärder (Microsoft. SQL/Server/firewallRules/Write)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: endast godkända VM-tillägg ska installeras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: Azure Defender för behållar register ska vara aktiverat|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: hanterad identitet ska användas i din API-app|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: autentisering ska vara aktiverat i API-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering ska finnas för vissa princip åtgärder (Microsoft. Authorization/policyAssignments/Delete)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: en aktivitets logg avisering ska finnas för vissa princip åtgärder (Microsoft. Authorization/policyAssignments/Write)|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: autentisering ska aktive ras i din Function-app|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Data Lake Analytics ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Key Vault ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Princip påverkan: framtvinga SSL-anslutning måste vara aktiverat för PostgreSQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: kontrol lera att "HTTP-version" är den senaste, om den används för att köra Function-appen|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: RDP-åtkomst från Internet ska blockeras|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Princip påverkan: framtvinga SSL-anslutning måste vara aktiverat för MySQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: se till att Function-appen har klient certifikat (inkommande klient certifikat) inställd på|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: logg kontroll punkter ska vara aktiverade för PostgreSQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Påverkan för principen: logg anslutningar ska vara aktiverade för PostgreSQL-databas servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: från kopplingar ska loggas för PostgreSQL-databas servrar.|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: sårbarhets bedömning ska vara aktiverat på dina SQL-servrar|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i din webbapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: externa konton med ägar behörigheter bör tas bort från din prenumeration|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Service Bus ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: diagnostikloggar i Azure Stream Analytics ska vara aktive rad|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: den senaste TLS-versionen ska användas i Funktionsapp|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Gällande princip: det lagrings konto som innehåller behållaren med aktivitets loggar måste vara krypterat med BYOK|Mer information om effekter finns i [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Inkludera AKS-kluster vid granskning om de virtuella datorernas skalnings uppsättningar diagnostikloggar är aktiverade||
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Senaste Java-version för App Services|Senaste Java-version som stöds för App Services|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Senaste python-version för Linux för App Services|Senaste python-version som stöds för App Services|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Lista över regioner där Network Watcher ska aktive ras|Om du vill se en fullständig lista över regioner kör du PowerShell-kommandot Get-AzLocation|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Den senaste PHP-versionen för App Services|Den senaste PHP-versionen som stöds för App Services|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Krävda kvarhållningsperiod (dagar) för resurs loggar|Mer information om resurs loggar finns i [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Namnet på resurs gruppen för Network Watcher|Namnet på resurs gruppen där nätverks Watcher finns|
|CIS Microsoft Azure grunderna benchmark v 1.3.0|Principtilldelning|Obligatorisk gransknings inställning för SQL-servrar||

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).