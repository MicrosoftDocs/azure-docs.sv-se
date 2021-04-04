---
title: Använd Key Vault för att lagra och komma åt Azure Cosmos DB nycklar
description: Använd Azure Key Vault för att lagra och komma åt Azure Cosmos DB anslutnings sträng, nycklar, slut punkter.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359336"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Skydda Azure Cosmos-nycklar med Azure Key Vault 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> Den rekommenderade lösningen för att komma åt Azure Cosmos DB nycklar är att använda en [systemtilldelad hanterad identitet](managed-identity-based-authentication.md). Om tjänsten inte kan dra nytta av hanterade identiteter använder du den [certbaserade lösningen](certificate-based-authentication.md). Om både den hanterade identitets lösningen och den certbaserade lösningen inte uppfyller dina behov kan du använda Key Vault-lösningen nedan.

När du använder Azure Cosmos DB för dina program kan du komma åt databasen, samlingarna, dokumenten med hjälp av slut punkten och nyckeln i appens konfigurations fil.  Men det är inte säkert att skicka nycklar och URL: er direkt i program koden eftersom de är tillgängliga i klartext-format för alla användare. Du vill se till att slutpunkten och nycklarna är tillgängliga, men via en säker mekanism. Med Azure Key Vault kan du då säkert lagra och hantera programhemligheter.

Följande steg krävs för att lagra och läsa Azure Cosmos DB åtkomst nycklar från Key Vault:

* Skapa en Key Vault-lösning  
* Lägg till Azure Cosmos DB åtkomst nycklar i Key Vault  
* Skapa ett Azure-webbprogram  
* Registrera programmet & bevilja behörighet att läsa Key Vault  


## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

1. Logga in på [Azure Portal](https://portal.azure.com/).  
2. Välj **skapa en resurs > säkerhets > Key Vault**.  
3. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:  
   * **Namn:** Ange ett unikt namn för din Key Vault.  
   * **Prenumeration:** Välj den prenumeration som du vill använda.  
   * Under **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.  
   * Välj en plats i listrutan Plats.  
   * Lämna standardvärdena för övriga alternativ.  
4. När du har angett den här informationen väljer du **Skapa**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Lägg till Azure Cosmos DB åtkomst nycklar till Key Vault.
1. Navigera till Key Vault som du skapade i föregående steg, öppna fliken **hemligheter** .  
2. Välj **+ generera/importera**, 

   * Välj **manuell** för **överförings alternativ**.
   * Ange ett **namn** för din hemlighet
   * Ange anslutnings strängen för ditt Cosmos DB konto i fältet **värde** . Välj sedan **Skapa**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Skapa en hemlighet":::

4. När hemligheten har skapats öppnar du den och kopierar den * * hemliga identifieraren i följande format. Du kommer att använda den här identifieraren i nästa avsnitt. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Skapa ett Azure-webbprogram

1. Skapa ett Azure-webbprogram eller så kan du ladda ned appen från [GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo). Det är ett enkelt MVC-program.  

2. Zippa upp det hämtade programmet och öppna filen **HomeController. cs** . Uppdatera det hemliga ID: t på följande rad:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Spara** filen och **Bygg** lösningen.  
4. Distribuera sedan programmet till Azure. Högerklicka på projektet och välj **publicera**. Skapa en ny App Service-profil (du kan namnge appen WebAppKeyVault1) och välja **publicera**.   

5. När programmet har distribuerats. Från Azure Portal navigerar du till den webbapp som du har distribuerat och aktiverar den **hanterade tjänst identiteten** för det här programmet.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Hanterad tjänst identitet":::

Om du kommer att köra programmet nu visas följande fel, eftersom du inte har gett några behörigheter till det här programmet i Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="App distribuerad utan åtkomst":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrera programmet & bevilja behörighet att läsa Key Vault

I det här avsnittet registrerar du programmet med Azure Active Directory och ger behörighet för programmet att läsa Key Vault. 

1. Navigera till Azure Portal och öppna **Key Vault** som du skapade i föregående avsnitt.  

2. Öppna **åtkomst principer**, Välj **+ Lägg till ny** hitta den webbapp som du har distribuerat, Välj behörigheter och välj **OK**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Lägg till åtkomst princip":::

Om du kör programmet kan du nu läsa hemligheten från Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="App distribuerad med hemlighet":::
 
På samma sätt kan du lägga till en användare för att komma åt nyckel valvet. Du måste lägga till dig själv i Key Vault genom att välja **åtkomst principer** och sedan bevilja alla behörigheter du behöver för att köra programmet från Visual Studio. När det här programmet körs från Skriv bordet tar det emot din identitet.

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera en brand vägg för Azure Cosmos DB se artikeln om [brand Väggs stöd](how-to-configure-firewall.md) .
* Information om hur du konfigurerar tjänst slut punkten för virtuellt nätverk finns i artikeln [skydda åtkomst med hjälp av VNet-tjänsten](how-to-configure-vnet-service-endpoint.md) .
