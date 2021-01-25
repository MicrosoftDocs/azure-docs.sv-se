---
title: Konfigurera Kundhanterade nycklar för Azure API för FHIR
description: Ta med din egen nyckel funktion som stöds i Azure API för FHIR via Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 3879280f56a4b99d8e6e08a9c9ed852ef2cafa68
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747331"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurera Kundhanterade nycklar i vila

När du skapar ett nytt Azure API för FHIR-konto krypteras dina data med hjälp av Microsoft-hanterade nycklar som standard. Nu kan du lägga till ett sekundärt krypterings lager för data med hjälp av din egen nyckel som du väljer och hanterar själv.

I Azure utförs detta vanligt vis med hjälp av en krypterings nyckel i kundens Azure Key Vault. Azure SQL, Azure Storage och Cosmos DB är några exempel som tillhandahåller den här funktionen idag. Azure API för FHIR utnyttjar detta stöd från Cosmos DB. När du skapar ett konto kan du välja att ange en Azure Key Vault nyckel-URI. Den här nyckeln skickas till Cosmos DB när DB-kontot har tillhandahållits. När en FHIR-begäran görs hämtar Cosmos DB nyckeln och använder den för att kryptera/dekryptera data. För att komma igång kan du referera till följande länkar:

- [Registrera Azure Cosmos DB Resource Provider för din Azure-prenumeration](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurera Azure Key Vault-instansen](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Lägg till en åtkomst princip till Azure Key Vault-instansen](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generera en nyckel i Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Ange Azure Key Vault nyckel

När du skapar ditt Azure API för FHIR-konto på Azure Portal kan du se konfigurations alternativet "data kryptering" under "databas inställningar" på fliken "ytterligare inställningar". Som standard väljs alternativet tjänst hanterad nyckel. 

Du kan välja din nyckel från nyckel väljaren:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="-Väljaren":::

Du kan också ange din Azure Key Vault nyckel här genom att välja alternativet "kundhanterad nyckel". Du kan ange nyckel-URI: n här:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Skapa Azure API för FHIR":::

För befintliga FHIR-konton kan du Visa nyckel krypterings valet (tjänst-eller kundhanterad nyckel) i bladet "databas" enligt nedan. Konfigurations alternativet kan inte ändras när det har valts. Du kan dock ändra och uppdatera din nyckel.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Databas":::

Dessutom kan du skapa en ny version av den angivna nyckeln, efter vilken dina data kommer att krypteras med den nya versionen utan avbrott i tjänsten. Du kan också ta bort åtkomsten till nyckeln för att ta bort åtkomsten till data. När nyckeln är inaktive rad leder frågorna till ett fel. Om nyckeln har Aktiver ATS på nytt kommer frågorna att lyckas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar Kundhanterade nycklar i vila. Härnäst kan du titta närmare på avsnittet med vanliga frågor och svar om Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: Konfigurera CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
