---
title: Distribuera skiss exemplet för Azure Security benchmark Foundation
description: Distribuera steg för utkastet till utkastet av Azure Security benchmark Foundation, inklusive information om skiss artefakts parametrar.
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: e1a5e011f5b53d35971fa43613914c0a43549600
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100421976"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Distribuera skiss exemplet för Azure Security benchmark Foundation

Om du vill distribuera skiss exemplet för Azure Security benchmark Foundation, måste du vidta följande steg:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Hitta skiss exemplet för **Azure Security benchmark Foundation** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet för Azure Security benchmark Foundation.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från Azure Security benchmark Foundation-utkastet.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från utkastet av Azure Security benchmark Foundation." Välj därefter **Publicera** längst ned på sidan.

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
    
     - **Prefix för resurser och resurs grupper**: den här strängen används som prefix för alla resurs-och resurs grupp namn
     - **Hubbens namn**: namn på hubben
     - **Logg kvarhållning (dagar)**: antalet dagar som loggarna kvarhålls. Om du anger 0 behålls loggarna oändligt
     - **Distribuera hubb**: Ange true eller false för att ange om tilldelningen distribuerar Hub-komponenterna i arkitekturen
     - **Hubbens plats**: plats för NAV resurs gruppen
     - **Mål-IP-adresser**: mål-IP-adresser för utgående anslutning; kommaavgränsad lista över IP-adresser eller prefix för IP-intervall
     - **Network Watcher namn**: Network Watcher resursens namn
     - **Network Watcher resurs grupp namn**: namn på Network Watcher resurs grupp
     - **Aktivera DDoS-skydd**: Ange true eller false om du vill ange om DDoS Protection har Aktiver ATS i det virtuella nätverket

   - Artefaktparametrar

     De parametrar som definieras i avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrollera statusen för distributionen genom att öppna skisstilldelningen.

> [!WARNING]
> Tjänsten Azure Blueprints och de inbyggda skissexemplen är **kostnadsfria**. Azures resurser [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) till att beräkna kostnaden för att köra de resurser som distribueras i skissexemplet.

## <a name="artifact-parameters-table"></a>Tabell med artefaktparametrar

Följande tabell innehåller en lista över skiss parametrar:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Hubb resurs grupp|Resursgrupp|Namn på resursgrupp|Locked-sammanfogar prefix med hubbens namn|
|Hubb resurs grupp|Resursgrupp|Resursgruppsplats|Låst – använder hubb plats|
|Mall för Azure-brandvägg|Resource Manager-mall|Azure Firewall-privata IP-adress||
|Mall för Azure Log Analytics och diagnostik|Resource Manager-mall|Plats för Log Analytics arbets yta|Plats där Log Analytics arbets ytan skapas. kör `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` i Azure PowersShell för att se tillgängliga regioner|
|Mall för Azure Log Analytics och diagnostik|Resource Manager-mall|Azure Automation konto-ID (valfritt)|Resurs-ID för Automation-konto används för att skapa en länkad tjänst mellan Log Analytics och ett Automation-konto|
|Mall för Azure nätverks säkerhets grupp|Resource Manager-mall|Aktivera NSG Flow-loggar|Ange true eller false om du vill aktivera eller inaktivera NSG flödes loggar|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för virtuellt nätverk|Adressprefix för virtuellt nätverk för hubb virtuellt nätverk|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för brand vägg under nätet|Adressprefix för brand vägg under nätet för hubb virtuellt nätverk|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för skydds-undernät|Skydds för undernät i hubb virtuellt nätverk|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för gateway-undernät|Adressprefix för gateway-undernät för virtuellt hubb nätverk|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för hanterings under nätet|Adressprefix för hantering under nätet för hubb virtuellt nätverk|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adressprefix för under nätet i hopp rutan|Adress prefix för hopp box för virtuellt nätverk i hubben|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Adress namn för undernät (valfritt)|Matris med under näts namn som ska distribueras till det virtuella hubb nätverket; till exempel "subnet1", "subnet2"|
|Mall för Azure Virtual Network Hub|Resource Manager-mall|Prefix för under näts adress (valfritt)|Matris med IP-adressprefix för valfria undernät för det virtuella hubb nätverket; till exempel "10.0.7.0/24", "10.0.8.0/24"|
|Resurs grupp för ekrar|Resursgrupp|Namn på resursgrupp|Locked-sammanfogar prefix med eker-namn|
|Resurs grupp för ekrar|Resursgrupp|Resursgruppsplats|Låst – använder hubb plats|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Distribuera eker|Ange true eller false för att ange om tilldelningen distribuerar eker-komponenterna i arkitekturen|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Prenumerations-ID för hubb|Prenumerations-ID där hubb har distribuerats; Standardvärdet är den prenumeration där skiss definitionen finns|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Eker-namn|Namn på ekern|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Virtual Network adressprefix|Virtual Network adressprefix för virtuellt nätverk i eker|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Adressprefix för undernät|Adressprefix för virtuellt nätverk i ekrar|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Adress namn för undernät (valfritt)|Matris med under näts namn som ska distribueras till det virtuella eker-nätverket; till exempel "subnet1", "subnet2"|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Prefix för under näts adress (valfritt)|Matris med IP-adressprefix för valfria undernät för det virtuella eker-nätverket; till exempel "10.0.7.0/24", "10.0.8.0/24"|
|Mall för Azure Virtual Network eker|Resource Manager-mall|Distribuera eker|Ange true eller false för att ange om tilldelningen distribuerar eker-komponenterna i arkitekturen|
|Network Watcher resurs grupp|Resursgrupp|Namn på resursgrupp|Locked-använder Network Watcher resurs grupp namn|
|Network Watcher resurs grupp|Resursgrupp|Resursgruppsplats|Låst – använder hubb plats|
|Azure Network Watcher-mall|Resource Manager-mall|Network Watcher plats|Plats för Network Watcher resursen|
|Azure Network Watcher-mall|Resource Manager-mall|Plats för Network Watcher resurs grupp|Plats för Network Watcher resurs grupp|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera skiss exemplet för Azure Security benchmark Foundation kan du gå till följande artikel om du vill lära dig mer om arkitekturen:

> [!div class="nextstepaction"]
> [Azure Security benchmark Foundation-skiss – översikt](./index.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
