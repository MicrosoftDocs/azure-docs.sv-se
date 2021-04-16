---
title: Skissexempel för DoD:s effektnivå 4
description: Distribuera steg för skissexempel på DoD:s effektnivå 4, inklusive information om skissartefaktparametern.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: ce0d2d162bf77c147e0e4bc26e68964cd50f4372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378456"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Distribuera skissexempel på DoD:s effektnivå 4

Om du vill Azure Blueprints skissexempel på försvarsdepartementets effektnivå 4 (DoD IL4) måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en prenumeration Azure Government du en [utvärderingsprenumeration](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Leta upp **skissexempel på DoD:s effektnivå 4** under _Andra exempel och_ välj Använd det här **exemplet.**

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skissnamn:** Ange ett namn på din kopia av skissexempel på DoD:s effektnivå 4.
   - **Definitionsplats:** Använd ellipsen och välj hanteringsgruppen där du vill spara kopian av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skissexempel kan anpassas efter din miljö och dina behov, men den ändringen kan flytta den från anpassningen till kontrollerna för DoD:s effektnivå 4.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **Ändringsanteckningar,** till exempel "Första versionen som publicerats från DoD IL4-skissexempel". Välj därefter **Publicera** längst ned på sidan.

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
|Tillåtna platser|Principtilldelning|Tillåtna platser|Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad.|
|Tillåtna platser för resursgrupper|Principtilldelning |Tillåtna platser|Med den här principen kan du begränsa de platser som din organisation kan skapa resursgrupper i. Den används för att genomdriva kraven på geo-efterlevnad.|
|Distribuera granskning på SQL-servrar|Principtilldelning|Värdet i dagar för kvarhållningsperioden (0 anger obegränsad kvarhållning)|Kvarhållningsdagar (valfritt, 180 dagar om det inte anges)|
|Distribuera granskning på SQL-servrar|Principtilldelning|Resursgruppsnamn för lagringskonto för SQL Server-granskning|Granskning skriver databashändelser till en granskningslogg i ditt Azure Storage-konto (ett lagringskonto skapas i varje region där en SQL Server skapas som ska delas av alla servrar i den regionen). Viktigt – för korrekt granskning ska du inte ta bort eller byta namn på resursgruppen eller lagringskontona.|
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper|Principtilldelning|Lagringskontoprefix för diagnostik för nätverkssäkerhetsgrupp|Det här prefixet kombineras med nätverkssäkerhetsgruppens plats för att bilda namnet på det skapade lagringskontot.|
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper|Principtilldelning|Resursgruppens namn för lagringskontot för diagnostik för nätverkssäkerhetsgrupp (måste finnas)|Den resursgrupp som lagringskontot ska skapas i. Den här resursgruppen måste redan finnas.|
|Distribuera Log Analytics-agenten för skalningsuppsättningar för virtuella Linux-datorer|Principtilldelning|Log Analytics-arbetsyta för skalningsuppsättningar för virtuella Linux-datorer|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt bevilja Log Analytics-deltagare behörigheter (eller liknande) till principtilldelningens huvudnamns-ID.|
|Distribuera Log Analytics-agenten för VM-skalningsuppsättningar för Linux|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS att lägga till i omfånget|En tom matris kan användas för att ange inga valfria parametrar: \[\]|
|Distribuera Log Analytics-agenten för virtuella Linux-datorer|Principtilldelning|Log Analytics-arbetsyta för virtuella Linux-datorer|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt bevilja Log Analytics-deltagare behörigheter (eller liknande) till principtilldelningens huvudnamns-ID.|
|Distribuera Log Analytics-agenten för virtuella Linux-datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS att lägga till i omfånget|En tom matris kan användas för att ange inga valfria parametrar: \[\]|
|Distribuera Log Analytics-agenten för VM-skalningsuppsättningar i Windows|Principtilldelning|Log Analytics-arbetsyta för VM-skalningsuppsättningar i Windows|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt bevilja Log Analytics-deltagare behörigheter (eller liknande) till principtilldelningens huvudnamns-ID.|
|Distribuera Log Analytics-agenten för VM-skalningsuppsättningar i Windows|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Windows-operativsystem som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar: \[\]|
|Distribuera Log Analytics-agenten för virtuella Windows-datorer|Principtilldelning|Log Analytics-arbetsyta för virtuella Windows-datorer|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt bevilja Log Analytics-deltagare behörigheter (eller liknande) till principtilldelningens huvudnamns-ID.|
|Distribuera Log Analytics-agenten för virtuella Windows-datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Windows OS som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar: \[\]|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Medlemmar som ska ingå i den lokala gruppen Administratörer|En semikolonavgränsad lista över medlemmar som ska undantas i den lokala gruppen Administratörer. Exempel: Administratör; myUser1; myUser2|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Medlemmar som ska undantas i den lokala gruppen Administratörer|En semikolonavgränsad lista över medlemmar som ska ingå i den lokala gruppen Administratörer. Exempel: Administratör; myUser1; myUser2|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Lista över resurstyper som ska ha diagnostikloggar aktiverade|Lista över resurstyper som ska granskas om diagnostiklogginställningen inte är aktiverad. Godkända värden finns i Azure Monitor [scheman för diagnostikloggar.](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Id för Log Analytics-arbetsyta som virtuella datorer ska konfigureras för|Det här är ID:t (GUID) för Log Analytics-arbetsytan som de virtuella datorerna ska konfigureras för.|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Långsiktig geo-redundant säkerhetskopiering ska aktiveras för Azure SQL databaser|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Sårbarhetsbedömning bör aktiveras på SQL-servrarna|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Geo-redundant lagring ska vara aktiverat för lagringskonton|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MySQL|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for PostgreSQL|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Webbprogram bör endast vara tillgängligt via HTTPS|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Funktionsappen bör endast vara tillgänglig via HTTPS|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Externa konton med skrivbehörighet ska tas bort från din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Externa konton med läsbehörighet ska tas bort från din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Externa konton med ägarbehörighet ska tas bort från din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Inaktuella konton med ägarbehörighet ska tas bort från prenumerationen|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Inaktuella konton bör tas bort från din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|CORS bör inte tillåta att alla resurser får åtkomst till ditt webbprogram|Information om principeffekter finns i Förstå [Azure Policy effekter.](../../../policy/concepts/effects.md)|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|Systemuppdateringar på VM-skalningsuppsättningar ska installeras|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|MFA ska vara aktiverat för konton med ägarbehörighet för din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion: \] DoD:s effektnivå 4|Principtilldelning|MFA ska vara aktiverat på konton med skrivbehörigheter för din prenumeration|Information om principeffekter finns i Förstå [Azure Policy effekter](../../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera skissexempel för DoD:s effektnivå 4 kan du gå till följande artiklar för att lära dig mer om skissen och kontrollmappningen:

> [!div class="nextstepaction"]
> [Skiss för DoD:s effektnivå 4 – översikt](./index.md) 
>  [Skiss för DoD:s effektnivå 4 – Kontrollmappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
