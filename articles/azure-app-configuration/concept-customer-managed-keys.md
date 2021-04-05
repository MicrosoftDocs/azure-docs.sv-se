---
title: Använd Kundhanterade nycklar för att kryptera dina konfigurations data
description: Kryptera dina konfigurations data med Kundhanterade nycklar
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930508"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Använd Kundhanterade nycklar för att kryptera dina konfigurations data för appar
Azure App konfiguration [krypterar känslig information i vila](../security/fundamentals/encryption-atrest.md). Användningen av Kundhanterade nycklar ger förbättrat data skydd genom att låta dig hantera dina krypterings nycklar.  När hanterad nyckel kryptering används krypteras all känslig information i appens konfiguration med en Azure Key Vault nyckel för användare som anges.  Detta ger möjlighet att rotera krypterings nyckeln på begäran.  Det ger också möjlighet att återkalla Azure App konfigurationens åtkomst till känslig information genom att återkalla appens konfigurations instans åtkomst till nyckeln.

## <a name="overview"></a>Översikt 
Azure App konfiguration krypterar känslig information i vila med hjälp av en 256-bitars AES-krypterings nyckel från Microsoft. Varje konfigurations instans för appar har en egen krypterings nyckel som hanteras av tjänsten och används för att kryptera känslig information. Känslig information omfattar de värden som finns i nyckel/värde-par.  När kundhanterad nyckel funktion är aktive rad använder app-konfigurationen en hanterad identitet som tilldelats appens konfigurations instans för att autentisera med Azure Active Directory. Den hanterade identiteten anropar sedan Azure Key Vault och omsluter appens konfigurations instansens krypterings nyckel. Den omslutna krypterings nyckeln lagras sedan och den icke-omslutna krypterings nyckeln cachelagras i appens konfiguration i en timme. Med appens konfiguration uppdateras den icke-omslutna versionen av krypterings nyckeln för program konfigurations instansen varje timme. Detta garanterar tillgänglighet under normala drifts förhållanden. 

>[!IMPORTANT]
> Om den identitet som är tilldelad till konfigurations instansen inte längre har behörighet att packa upp instansens krypterings nyckel, eller om den hanterade nyckeln tas bort permanent, kommer den inte längre att kunna dekryptera känslig information som lagras i appens konfigurations instans. Med hjälp av Azure Key Vault funktionen för [mjuk borttagning](../key-vault/general/soft-delete-overview.md) minimerar du risken för att oavsiktligt ta bort krypterings nyckeln.

När användarna aktiverar kundens hanterade nyckel funktioner på sin Azure App konfigurations instans kontrollerar de tjänstens möjlighet att komma åt känslig information. Den hanterade nyckeln fungerar som en rot krypterings nyckel. En användare kan återkalla sin konfigurations instanss åtkomst till deras hanterade nyckel genom att ändra åtkomst principen för nyckel valvet. När den här åtkomsten har återkallats förlorar app-konfigurationen möjligheten att dekryptera användar data inom en timme. I det här läget kommer program konfigurations instansen att förbjuda alla åtkomst försök. Den här situationen kan återkrävas genom att beviljar tjänsten åtkomst till den hanterade nyckeln en gång till.  Inom en timme kommer app-konfigurationen att kunna dekryptera användar data och fungera under normala förhållanden.

>[!NOTE]
>Alla Azure App konfigurations data lagras i upp till 24 timmar i en isolerad säkerhets kopiering. Detta inkluderar den icke-omslutna krypterings nyckeln. Dessa data är inte omedelbart tillgängliga för tjänsten eller tjänst teamet. I händelse av en nöd återställning återkallar Azure App-konfigurationen sig själv från de hanterade nyckel data.

## <a name="requirements"></a>Krav
Följande komponenter krävs för att kunna aktivera kundhanterad nyckel funktion för Azure App konfiguration:
- Konfigurations instans för standard-nivå Azure App
- Azure Key Vault med funktioner för mjuk borttagning och rensnings skydd aktiverade
- En RSA-eller RSA-HSM-nyckel i Key Vault
    - Nyckeln får inte ha upphört att gälla, den måste vara aktive rad och den måste ha både figursatta och diswrap-funktioner aktiverade

När dessa resurser har kon figurer ATS fortsätter två steg att tillåta Azure App konfiguration att använda Key Vault nyckel:
1. Tilldela en hanterad identitet till Azure App konfigurations instans
2. Bevilja identiteten `GET` , `WRAP` och `UNWRAP` behörigheterna i mål Key Vaults åtkomst princip.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Aktivera kundhanterad nyckel kryptering för Azure App konfigurations instans
För att du ska kunna starta måste du ha en korrekt konfigurerad Azure App konfigurations instans. Om du inte har en instans av en app-konfiguration som är tillgänglig, följer du någon av följande snabb starter för att konfigurera en:
- [Skapa en ASP.NET Core-app med Azure App Configuration](quickstart-aspnet-core-app.md)
- [Skapa en .NET Core-app med Azure App konfiguration](quickstart-dotnet-core-app.md)
- [Skapa en .NET Framework-app med Azure App Configuration](quickstart-dotnet-app.md)
- [Skapa en Java våren-app med Azure App konfiguration](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra kommando rads instruktionerna i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive .NET Core SDK. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Skapa och konfigurera en Azure Key Vault
1. Skapa en Azure Key Vault med hjälp av Azure CLI.  Observera att både `vault-name` och `resource-group-name` är användardefinierade och måste vara unika.  Vi använder `contoso-vault` och `contoso-resource-group` i de här exemplen.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Aktivera mjuk borttagning och tömning av skydd för Key Vault. Ersätt namnen på de Key Vault ( `contoso-vault` ) och resurs gruppen ( `contoso-resource-group` ) som skapades i steg 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Skapa en Key Vault nyckel. Ange ett unikt `key-name` namn för den här nyckeln och ersätt namnen på de Key Vault ( `contoso-vault` ) som skapades i steg 1. Ange om du föredrar `RSA` eller `RSA-HSM` kryptering.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Utdata från det här kommandot visar nyckel-ID ("barn") för den genererade nyckeln.  Anteckna nyckel-ID: t som du kan använda senare i den här övningen.  Nyckel-ID: t har formatet: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  Nyckel-ID: t har tre viktiga komponenter:
    1. Key Vault-URI: https://{My Key Vault}. valv. Azure. net
    1. Key Vault nyckel namn: {Key Name}
    1. Key Vault nyckel version: {Key version}

1. Skapa en systemtilldelad hanterad identitet med Azure CLI och ersätt namnet på din app Configuration-instans och resurs grupp som användes i föregående steg. Den hanterade identiteten kommer att användas för att få åtkomst till den hanterade nyckeln. Vi använder `contoso-app-config` för att illustrera namnet på en app Configuration-instans:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    Utdata från det här kommandot omfattar huvud-ID: t ("principalId") och klient-ID ("tenandId") för den tilldelade identiteten.  Dessa ID: n kommer att användas för att ge identitets åtkomst till den hanterade nyckeln.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Den hanterade identiteten för Azure App konfigurations instansen behöver åtkomst till nyckeln för att utföra nyckel verifiering, kryptering och dekryptering. Den speciella uppsättning åtgärder som den behöver åtkomst till innehåller: `GET` , `WRAP` och `UNWRAP` för nycklar.  Att bevilja åtkomst kräver ägar-ID: t för den hanterade identiteten för appens konfigurations instans. Det här värdet hämtades i föregående steg. Den visas nedan som `contoso-principalId` . Bevilja behörighet till den hanterade nyckeln med hjälp av kommando raden:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. När Azure App konfigurations instansen har åtkomst till den hanterade nyckeln kan vi aktivera kundhanterad nyckel funktion i tjänsten med hjälp av Azure CLI. Återkalla följande egenskaper som registrerats när du skapade nyckeln: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Din Azure App konfigurations instans har nu kon figurer ATS för att använda en kundhanterad nyckel som lagras i Azure Key Vault.

## <a name="next-steps"></a>Nästa steg
I den här artikeln konfigurerade du Azure App konfigurations instansen så att den använder en kundhanterad nyckel för kryptering.  Lär dig hur du [integrerar din tjänst med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).
