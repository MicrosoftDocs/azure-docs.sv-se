---
title: Distribuera CAF Foundation skiss-exempel
description: Distribuera steg för CAF Foundation-skissen som innehåller information om skiss artefakt parameter.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: a8d41364451bf2ebf1e48b90fad74f4b24c804e1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470157"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Distribuera skiss exemplet Microsoft Cloud adoption Framework för Azure Foundation

Om du vill Microsoft Cloud distribuera CAF (implementering Framework for Azure) Foundation skiss-exemplet måste du vidta följande steg:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Hitta skiss exemplet **CAF Foundation** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av CAF Foundation skiss-exemplet.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från CAF Foundation-skissen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från CAF Foundation-skissen". Välj därefter **Publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopieringen av skiss exemplet har **publicerats** kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. I det här steget anges parametrar för att göra varje distribution av skissexemplets kopia unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Tilldela skiss** överst på skissdefinitionssidan.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar
       - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer mer än en prenumeration, skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelnings namn**: namnet fylls i automatiskt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som det är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i.
     - Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen.
       Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skissens låsinställning för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Välj antingen standardalternativet _Systemtilldelad_ hanterad identitet, eller alternativet _Användartilldelad_ hanterad identitet.

   - Skissparametrar

     De parametrar som definieras i avsnittet används av många av artefakterna i skissdefinitionen för att ge konsekvens.

     - **Organisation**: Ange organisationens namn, till exempel contoso, måste vara unikt.
     - **Azure-region**: Välj Azure-regionen för distribution.
     - **Tillåtna platser**: vilka Azure-regioner kommer du att tillåta att resurser skapas?
     
   - Artefaktparametrar

     De parametrar som definieras i avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrollera statusen för distributionen genom att öppna skisstilldelningen.

> [!WARNING]
> Tjänsten Azure Blueprints och de inbyggda skissexemplen är **kostnadsfria**. Azures resurser [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) till att beräkna kostnaden för att köra de resurser som distribueras i skissexemplet.

## <a name="artifact-parameters-table"></a>Tabell med artefaktparametrar

I följande tabell visas en lista med skissartefaktens parametrar:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Policy_Allowed-StorageAccount-SKU: er|SKU som används i konton för diagnostisk logg lagring|
|Tillåtna SKU: er för virtuella datorer|Principtilldelning|Policy_Allowed-VM-SKU: er|Tillåtna SKU: er för virtuella datorer|
|Lägg till CostCenter-TAGGEN i resurs grupper|Principtilldelning|Policy_CostCenter_Tag|Lägg till CostCenter-TAGGEN och dess värde från resurs gruppen|
|Resurs typer som du inte vill tillåta i din miljö|Principtilldelning|Princip _Allowed-resurs-typer|Vilka Azure-resurser du vill tillåta i din miljö|
|Distribuera Key Vault|Resource Manager-mall|KV-AccessPolicy|**Låst** – Azure AD-grupp eller användare <Object ID> för att ge behörighet till i Key Vault|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_DataRetention|**Låst** – antal dagar som data kommer att behållas i Log Analytics|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_Location|**Låst** – Region som används när arbetsytan etableras|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera CAF Foundation skiss-exemplet kan du gå till följande artikel om du vill lära dig mer om arkitekturen:

> [!div class="nextstepaction"]
> [CAF Foundation-skiss – översikt](./index.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
