---
title: Distribuera exempel på arbets belastnings skiss för ISO 27001 ASE/SQL
description: Distribuera steg i exemplet ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss, inklusive information om skiss artefakt parameter.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 5c329a9d7175772e80ea6d9d8da3baf85ce0d170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669652"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuera exempel på arbets belastnings skiss för ISO 27001 App Service-miljön/SQL Database

För att distribuera Azure-skisserna ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss, måste du vidta följande steg:

> [!div class="checklist"]
> - Distribuera skiss exemplet för [delade tjänster i ISO 27001](../iso27001-shared/index.md)
> - Skapa en ny skiss från exemplet
> - Märk din kopia av exemplet som **Publicerat**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera skiss exemplet för delade tjänster i ISO 27001

Innan detta skiss exempel kan distribueras måste du distribuera [ISO 27001](../iso27001-shared/index.md) -skissen för delade tjänster till mål prenumerationen. Utan en lyckad distribution av skiss exemplet för delade tjänster i ISO 27001 kommer det här skiss exemplet att sakna infrastruktur beroenden och Miss lyckas under distributionen.

> [!IMPORTANT]
> Det här skiss exemplet måste tilldelas i samma prenumeration som ISO 27001-skiss exemplet för [delade tjänster](../iso27001-shared/index.md) .

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja först med att implementera skissexemplet genom att skapa en ny skiss i din miljö, med exemplet som utgångspunkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång** till vänster väljer du knappen **Skapa** under _Skapa en skiss_.

1. Leta upp exemplet **ISO 27001: ASE/SQL arbets belastnings** skiss under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _Grundinställningar_ för skissexemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet för arbets belastningen ISO 27001 ASE/SQL.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _Artefakter_ överst på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan med artefakter som utgör skissexemplet. Många av artefakterna innehåller parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Din kopia av skissexemplet har nu skapats i din miljö. Den skapas i läget **Utkast** och måste vara **Publicerad** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från ISO 27001-standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta din kopia av skissexemplet och markera det sedan.

1. Välj **Publicera skiss** överst på sidan. På den nya sidan till höger anger du en **Version** för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från ISO 27001-skiss exemplet". Välj därefter **Publicera** längst ned på sidan.

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
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skissens låsinställning för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelade_ hanterade identiteter.

   - Skissparametrar

     De parametrar som definieras i avsnittet används av många av artefakterna i skissdefinitionen för att ge konsekvens.

     - **Organisations namn**: Ange ett kort namn för din organisation. Den här egenskapen används främst för att namnge resurser.
     - **Prenumerations-ID för delad tjänst**: PRENUMERATIONS-ID där ISO 27001-skiss exemplet för [delade tjänster](../iso27001-shared/index.md) har tilldelats.
     - Adressprefix för **standard under nätet**: CIDR-notering för det virtuella nätverkets standard under nät.
       Standardvärdet är _10.1.0.0/16_.
     - **Arbets belastnings plats**: avgör vilken plats artefakterna distribueras till. Alla tjänster är inte tillgängliga på alla platser. Artefakter som distribuerar sådana tjänster tillhandahåller ett parameter alternativ för platsen som artefakten ska distribueras till.

   - Artefaktparametrar

     De parametrar som definieras i avsnittet gäller för den artefakt som de definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrollera statusen för distributionen genom att öppna skisstilldelningen.

> [!WARNING]
> Tjänsten Azure Blueprints och de inbyggda skissexemplen är **kostnadsfria**. Azures resurser [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) till att beräkna kostnaden för att köra de resurser som distribueras i skissexemplet.

## <a name="artifact-parameters-table"></a>Tabell med artefaktparametrar

I följande tabell visas en lista med skissartefaktens parametrar:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Log Analytics resurs grupp|Resursgrupp|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-log-rg` för att göra resurs gruppen unik.|
|Log Analytics resurs grupp|Resursgrupp|Plats|**Locked** – använder skiss parametern.|
|Log Analytics mall|Resource Manager-mall|Tjänstenivå|Anger nivån för Log Analytics arbets ytan. Standardvärdet är _PerNode_.|
|Log Analytics mall|Resource Manager-mall|Kvarhållning av logg i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Log Analytics mall|Resource Manager-mall|Location|Region som används för att skapa Log Analytics-arbetsytan. Standardvärdet är _USA, västra 2_.|
|Nätverks resurs grupp|Resursgrupp|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-net-rg` för att göra resurs gruppen unik.|
|Nätverks resurs grupp|Resursgrupp|Plats|**Locked** – använder skiss parametern.|
|Mall för nätverks säkerhets grupp|Resource Manager-mall|Kvarhållning av logg i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Azure Firewall privat IP|Konfigurerar den privata IP-adressen för [Azure-brandväggen](../../../../firewall/overview.md). Ska ingå i CIDR-notationen som definieras i _ISO 27001:_ artefakt parameter för delade tjänster **Azure Firewall Subnet Address prefix**. Standardvärdet är _10.0.4.4_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Prenumerations-ID för delade tjänster|Värde som används för att aktivera VNET-peering mellan en arbets belastning och delade tjänster.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Virtual Network adressprefix|CIDR-notering för det virtuella nätverket. Standardvärdet är _10.1.0.0/16_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för standard under nätet|CIDR-notering för det virtuella nätverkets standard under nät. Standardvärdet är _10.1.0.0/16_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Lägger till IP-adress|IP-adressen för den första lägger till den virtuella datorn. Det här värdet används som anpassat VNET DNS.|
|Key Vault resurs grupp|Resursgrupp|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-kv-rg` för att göra resurs gruppen unik.|
|Key Vault resurs grupp|Resursgrupp|Plats|**Locked** – använder skiss parametern.|
|Key Vault mall|Resource Manager-mall|AAD-objekt-ID|AAD-objektmodellen för det konto som kräver åtkomst till Key Vault-instansen. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ väljer du ikonen "Klicka för att kopiera" bredvid _objekt-ID_.|
|Key Vault mall|Resource Manager-mall|Kvarhållning av logg i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Key Vault mall|Resource Manager-mall|Key Vault SKU|Anger SKU: n för den Key Vault som skapas. Standardvärdet är _Premium_.|
|Key Vault mall|Resource Manager-mall|Användar namn för Azure SQL Server Admin|Det användar namn som används för att få åtkomst till Azure-SQL Server. Måste matcha samma egenskaps värde i **Azure SQL Database-mallen**. Standardvärdet är _SQL-admin-user_.|
|Azure SQL Database resurs grupp|Resursgrupp|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-azsql-rg` för att göra resurs gruppen unik.|
|Azure SQL Database resurs grupp|Resursgrupp|Plats|**Locked** – använder skiss parametern.|
|Azure SQL Database mall|Resource Manager-mall|Användar namn för Azure SQL Server Admin|Användar namn för Azure-SQL Server. Måste matcha samma egenskaps värde i **Key Vault-mallen**. Standardvärdet är _SQL-admin-user_.|
|Azure SQL Database mall|Resource Manager-mall|Azure SQL Server admin-lösenord (Key Vault resurs-ID)|Resurs-ID för Key Vault. Använd "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-workload-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" och Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med skiss parametern **organisations namn** .|
|Azure SQL Database mall|Resource Manager-mall|Azure SQL Server admin-lösenord (Key Vault hemligt namn)|SQL Server administratörens användar namn. Måste överensstämma med värdet i **Key Vault Template** -egenskapen **Azure SQL Server Admin användar namn**.|
|Azure SQL Database mall|Resource Manager-mall|Kvarhållning av logg i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Azure SQL Database mall|Resource Manager-mall|AAD admin-objekt-ID|AAD-objekt-ID för den användare som ska tilldelas som Active Directory administratör. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ väljer du ikonen "Klicka för att kopiera" bredvid _objekt-ID_.|
|Azure SQL Database mall|Resource Manager-mall|AAD admin-inloggning|För närvarande kan Microsoft-konton (t. ex. live.com eller outlook.com) inte anges som administratör. Endast användare och säkerhets grupper inom organisationen kan anges som administratör. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ kopierar du _användar namnet_.|
|App Service-miljön resurs grupp|Resursgrupp|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-ase-rg` för att göra resurs gruppen unik.|
|App Service-miljön resurs grupp|Resursgrupp|Plats|**Locked** – använder skiss parametern.|
|App Service-miljön mall|Resource Manager-mall|Domännamn|Namnet på Active Directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|App Service-miljön mall|Resource Manager-mall|ASE-plats|App Service-miljön plats. Standardvärdet är _USA, västra 2_.|
|App Service-miljön mall|Resource Manager-mall|Application Gateway logg kvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss kan du gå till följande artiklar om du vill veta mer om arkitektur och kontroll mappning:

> [!div class="nextstepaction"]
> [ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss – översikt](./index.md) 
>  [ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss – kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
