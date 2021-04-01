---
title: Kryptering – rest med Kundhanterade nycklar
titleSuffix: Azure Cognitive Search
description: Kompletterande kryptering på Server sidan över index och synonymer mappar i Azure Kognitiv sökning att använda nycklar som du skapar och hanterar i Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119296"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurera Kundhanterade nycklar för data kryptering i Azure Kognitiv sökning

Azure Kognitiv sökning krypterar automatiskt indexerat innehåll i vila med [tjänst hanterade nycklar](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Om du behöver mer skydd kan du komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault. Den här artikeln vägleder dig genom stegen för att konfigurera kundhanterad nyckel kryptering.

Kundhanterad nyckel kryptering är beroende av [Azure Key Vault](../key-vault/general/overview.md). Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault s API: er för att generera krypterings nycklar. Med Azure Key Vault kan du också granska nyckel användningen om du [aktiverar loggning](../key-vault/general/logging.md).  

Kryptering med Kundhanterade nycklar tillämpas på enskilda index eller synonyma mappningar när objekten skapas och anges inte på själva Sök tjänst nivån. Endast nya objekt kan krypteras. Det går inte att kryptera innehåll som redan finns.

Nycklar behöver inte vara i samma nyckel valv. En enskild Sök tjänst kan vara värd för flera krypterade index eller synonyma Maps, var och en krypterad med sina egna Kundhanterade krypterings nycklar som lagras i olika nyckel valv. Du kan också ha index och synonym mappningar i samma tjänst som inte är krypterade med Kundhanterade nycklar.

>[!Important]
> Om du implementerar Kundhanterade nycklar bör du följa strikta procedurer under rutinmässig rotation av Key Vault-nycklar och Active Directory program hemligheter och registrering. Uppdatera alltid allt krypterat innehåll för att använda nya hemligheter och nycklar innan du tar bort de gamla. Om du saknar det här steget kan innehållet inte dekrypteras.

## <a name="double-encryption"></a>Dubbel kryptering

För tjänster som skapats efter 1 augusti 2020 och i vissa regioner innehåller omfattningen av kundhanterad nyckel kryptering temporära diskar, vilket ger [fullständig dubbel kryptering](search-security-overview.md#double-encryption), för närvarande tillgängliga i dessa regioner: 

+ USA, västra 2
+ East US
+ USA, södra centrala
+ US Gov, Virginia
+ US Gov, Arizona

Om du använder en annan region eller en tjänst som skapats före den 1 augusti, begränsas hanterad nyckel kryptering enbart till data disken, exklusive de temporära diskar som används av tjänsten.

## <a name="prerequisites"></a>Förutsättningar

Följande verktyg och tjänster används i det här scenariot.

+ [Azure kognitiv sökning](search-create-service-portal.md) på en [fakturerbar nivå](search-sku-tier.md#tier-descriptions) (Basic eller högre) i vilken region som helst.
+ [Azure Key Vault](../key-vault/general/overview.md)kan du skapa nyckel valv med [Azure Portal](../key-vault//general/quick-create-portal.md), [Azure CLI](../key-vault//general/quick-create-cli.md)eller [Azure PowerShell](../key-vault//general/quick-create-powershell.md). i samma prenumeration som Azure Kognitiv sökning. Nyckel valvet måste ha aktiverat **skydd mot** **borttagning** och rensning.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Om du inte har ett kan du [Konfigurera en ny klient](../active-directory/develop/quickstart-create-new-tenant.md).

Du bör ha ett Sök program som kan skapa det krypterade objektet. I den här koden kommer du att referera till nyckel valvet och Active Directory registrerings information. Den här koden kan vara en fungerande app eller prototyp kod som C#- [kod exemplet DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Du kan använda [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)eller [Azure PowerShell](./search-get-started-powershell.md)för att anropa REST-API: er som skapar index och synonym mappningar som innehåller en krypterings nyckel parameter. Det finns inget stöd för portalen för att lägga till en nyckel i index eller synonym Maps för tillfället.

## <a name="1---enable-key-recovery"></a>1 – aktivera nyckel återställning

På grund av krypterings typen med Kundhanterade nycklar kan ingen hämta dina data om din Azure Key Vault-nyckel tas bort. För att förhindra data förlust som orsakas av oavsiktliga Key Vault nyckel borttagningar måste du aktivera mjuk borttagning och rensnings skydd i nyckel valvet. Mjuk borttagning är aktiverat som standard, så du kommer bara att stöta på problem om du har inaktiverat det. Rensnings skydd är inte aktiverat som standard, men det krävs för kundhanterad nyckel kryptering i Kognitiv sökning. Mer information finns i [mjuk borttagning](../key-vault/general/soft-delete-overview.md) och rensning av [skydds](../key-vault/general/soft-delete-overview.md#purge-protection) översikter.

Du kan ange båda egenskaperna med hjälp av kommandona Portal, PowerShell eller Azure CLI.

### <a name="using-azure-portal"></a>Använda Azure Portal

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Key Vault.

1. På sidan **Översikt** under **Essentials** aktiverar du **skydd mot** **mjuk borttagning** och rensning.

### <a name="using-powershell"></a>Använda PowerShell

1. Kör `Connect-AzAccount` för att konfigurera dina Azure-autentiseringsuppgifter.

1. Kör följande kommando för att ansluta till nyckel valvet och Ersätt `<vault_name>` med ett giltigt namn:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault skapas med mjuk borttagning aktiverat. Om den är inaktive rad i valvet kör du följande kommando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Aktivera rensnings skydd:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Spara dina uppdateringar:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Använda Azure CLI

+ Om du har en [installation av Azure CLI](/cli/azure/install-azure-cli)kan du köra följande kommando för att aktivera de nödvändiga egenskaperna.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 – Skapa en nyckel i Key Vault

Hoppa över det här steget om du redan har en nyckel i Azure Key Vault.

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Key Vault.

1. Välj **nycklar** till vänster och välj sedan **+ generera/importera**.

1. I fönstret **skapa en nyckel** , i listan med **alternativ**, väljer du den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel, **överföra** en befintlig nyckel eller använda **Återställ säkerhets kopia** för att välja en säkerhets kopia av en nyckel.

1. Ange ett **namn** för nyckeln och välj även andra nyckel egenskaper.

1. Välj **skapa** för att starta distributionen.

1. Anteckna nyckel identifieraren – den består av **nyckel värdets URI**, **nyckel namnet** och **nyckel versionen**. Du behöver identifieraren för att definiera ett krypterat index i Azure Kognitiv sökning.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Skapa en ny nyckel valvs nyckel":::

## <a name="3---register-an-app-in-active-directory"></a>3 – registrera en app i Active Directory

1. I [Azure Portal](https://portal.azure.com)letar du upp Azure Active Directory resurs för din prenumeration.

1. Till vänster, under **Hantera**, Välj **Appregistreringar** och välj sedan **ny registrering**.

1. Ge registreringen ett namn, kanske ett namn som liknar Sök programmets namn. Välj **Register** (Registrera).

1. Kopiera program-ID: t när appens registrering har skapats. Du måste ange den här strängen för ditt program. 

   Om du går igenom [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)klistrar du in det här värdet i **appsettings.jspå** filen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Program-ID i avsnittet Essentials":::

1. Välj sedan **certifikat & hemligheter** till vänster.

1. Välj **Ny klienthemlighet**. Ge hemligheten ett visnings namn och välj **Lägg till**.

1. Kopiera program hemligheten. Om du stegar igenom exemplet klistrar du in det här värdet i **appsettings.js** filen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Apphemlighet":::

## <a name="4---grant-key-access-permissions"></a>4 – bevilja nyckel åtkomst behörigheter

I det här steget ska du skapa en åtkomst princip i Key Vault. Den här principen ger programmet som du har registrerat med Active Directory behörighet att använda din Kundhanterade nyckel.

Åtkomst behörigheterna kan återkallas vid en angiven tidpunkt. När den har återkallats blir alla Sök tjänst index eller synonym mappningar som använder det nyckel valvet oanvändbara. Att återställa åtkomst behörigheter för Key Vault vid ett senare tillfälle kommer att återställa index\synonym Map-åtkomst. Mer information finns i [säker åtkomst till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).

1. Öppna din **översikts** sida för Key vault fortfarande Azure Portal. 

1. Välj **åtkomst principer** till vänster och välj **+ Lägg till åtkomst princip**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Lägg till ny åtkomst princip för nyckel valv":::

1. Välj **Välj huvud namn** och välj det program som du registrerade med Active Directory. Du kan söka efter den efter namn.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Välj nyckel valv åtkomst principens huvud namn":::

1. I **nyckel behörigheter** väljer du *Hämta*, *packa upp nyckel* och *Radbryt nyckel*.

1. I **hemliga behörigheter** väljer du *Hämta*.

1. I **certifikat behörigheter** väljer du *Hämta*.

1. Välj **Lägg till** och **Spara**.

> [!Important]
> Krypterat innehåll i Azure Kognitiv sökning konfigureras för att använda en speciell Azure Key Vault nyckel med en angiven **version**. Om du ändrar nyckeln eller versionen måste mappningen index eller synonymer uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer index eller synonym mappning inte att kunna användas, men du kan inte dekryptera innehållet när nyckel åtkomsten har gått förlorad.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 – Kryptera innehåll

Om du vill lägga till en kundhanterad nyckel på ett index, data källa, färdigheter, indexerare eller synonym mappning måste du använda [sök REST API](/rest/api/searchservice/) eller ett SDK. Portalen visar inte synonym kartor eller krypterings egenskaper. När du använder ett giltigt API-index, stöder data källor, färdighetsuppsättningar, indexerare och synonym Maps en **encryptionKey** -egenskap på högsta nivån.

I det här exemplet används REST API med värden för Azure Key Vault och Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Ingen av dessa nyckel valvs uppgifter betraktas som hemliga och kan enkelt hämtas genom att bläddra till den relevanta Azure Key Vault nyckel sidan i Azure Portal.

## <a name="example-index-encryption"></a>Exempel: index kryptering

Skapa ett krypterat index med hjälp av [create index Azure Kognitiv sökning REST API](/rest/api/searchservice/create-index). Använd `encryptionKey` egenskapen för att ange vilken krypterings nyckel som ska användas.
> [!Note]
> Ingen av dessa nyckel valvs uppgifter betraktas som hemliga och kan enkelt hämtas genom att bläddra till den relevanta Azure Key Vault nyckel sidan i Azure Portal.

## <a name="rest-examples"></a>REST-exempel

I det här avsnittet visas en fullständig JSON för ett krypterat index och en synonym mappning

### <a name="index-encryption"></a>Index kryptering

Information om hur du skapar ett nytt index via REST API finns i [create index (REST API)](/rest/api/searchservice/create-index), där den enda skillnaden här anger krypterings nyckel informationen som en del av index definitionen:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nu kan du skicka begäran om att skapa index och sedan börja använda indexet som vanligt.

### <a name="synonym-map-encryption"></a>Kryptering av synonym mappning

Skapa en krypterad synonym mappning med hjälp av [create synonymer Map Azure Kognitiv sökning REST API](/rest/api/searchservice/create-synonym-map). Använd `encryptionKey` egenskapen för att ange vilken krypterings nyckel som ska användas.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nu kan du skicka en förfrågan om att skapa synonym mappning och sedan börja använda den som vanligt.

## <a name="example-data-source-encryption"></a>Exempel: kryptering av data Källa

Skapa en krypterad data källa med hjälp av [skapa data källa (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-data-source). Använd `encryptionKey` egenskapen för att ange vilken krypterings nyckel som ska användas.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nu kan du skicka begäran om att skapa data källa och sedan börja använda den som vanligt.

## <a name="example-skillset-encryption"></a>Exempel: färdigheter-kryptering

Skapa en krypterad färdigheter med [REST API för att skapa färdigheter Azure kognitiv sökning](/rest/api/searchservice/create-skillset). Använd `encryptionKey` egenskapen för att ange vilken krypterings nyckel som ska användas.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nu kan du skicka begäran om att skapa färdigheter och sedan börja använda den som vanligt.

## <a name="example-indexer-encryption"></a>Exempel: indexerare kryptering

Skapa en krypterad indexerare med hjälp av [REST API skapa indexerare Azure Kognitiv sökning](/rest/api/searchservice/create-indexer). Använd `encryptionKey` egenskapen för att ange vilken krypterings nyckel som ska användas.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nu kan du skicka begäran om att skapa indexerare och sedan börja använda den som vanligt.

>[!Important]
> `encryptionKey`Det går inte att lägga till i befintliga Sök index eller synonym mappningar, men det kan uppdateras genom att ange olika värden för någon av de tre nyckel valvs detaljerna (till exempel uppdatering av nyckel versionen). När du ändrar till en ny Key Vault nyckel eller en ny nyckel version måste alla sökindex eller synonym mappningar som använder nyckeln först uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer indexet eller synonym mappningen att bli oanvändbar, eftersom den inte kan dekryptera innehållet när nyckel åtkomsten förlorats. Även om åtkomst behörigheterna för Key Vault återställs vid ett senare tillfälle återställs innehålls åtkomsten.

## <a name="simpler-alternative-trusted-service"></a>Enklare alternativ: betrodd tjänst

Beroende på klient konfiguration och autentiseringskrav kan du eventuellt implementera en enklare metod för att komma åt nyckel valvet. I stället för att skapa och använda ett Active Directory program kan du göra en Sök tjänst till en betrodd tjänst genom att aktivera en Systemhanterad identitet för den. Sedan använder du den betrodda Sök tjänsten som en säkerhets princip, i stället för ett AD-registrerat program, för att komma åt Key Vault-nyckeln.

Med den här metoden kan du utelämna stegen för program registrering och program hemligheter och förenkla en krypterings nyckel definition till bara Key Vault-komponenterna (URI, valv namn, nyckel version).

I allmänhet gör en hanterad identitet att Sök tjänsten kan autentisera till Azure Key Vault utan att lagra autentiseringsuppgifter (ApplicationID eller ApplicationSecret) i koden. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för din Sök tjänst, som bara kan ha en hanterad identitet. Mer information om hur hanterade identiteter fungerar finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

1. Gör din Sök tjänst till en betrodd tjänst.
   ![Aktivera systemtilldelad hanterad identitet](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivera systemtilldelad hanterad identitet")

1. När du konfigurerar en åtkomst princip i Azure Key Vault väljer du den betrodda Sök tjänsten som princip (i stället för det AD-registrerade programmet). Tilldela samma behörigheter (flera får, omslutning, unwrap) enligt anvisningarna i steget bevilja åtkomst nyckel behörighet.

1. Använd en förenklad konstruktion av `encryptionKey` som utesluter Active Directory egenskaper.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Villkor som hindrar dig från att införa den här förenklade metoden är:

+ Du kan inte direkt bevilja dina åtkomst behörigheter för Sök tjänsten till nyckel valvet (till exempel om Sök tjänsten är i en annan Active Directory klient än Azure Key Vault).

+ En enskild Sök tjänst krävs för att vara värd för flera krypterade indexes\synonym-mappningar, var och en använder en annan nyckel från ett annat nyckel valv, där varje nyckel valv måste använda **en annan identitet** för autentisering. Eftersom en Sök tjänst bara kan ha en hanterad identitet, deklarerar ett krav för flera identiteter den förenklade metoden för ditt scenario.  

## <a name="work-with-encrypted-content"></a>Arbeta med krypterat innehåll

Med kundhanterad nyckel kryptering kommer du att märka svars tider för både indexering och frågor på grund av det extra krypterings-/dekrypterings arbetet. Azure Kognitiv sökning loggar inte krypterings aktivitet, men du kan övervaka nyckel åtkomst via loggning av nyckel valv. Vi rekommenderar att du [aktiverar loggning](../key-vault/general/logging.md) som en del av Key Vault-konfigurationen.

Nyckel rotation förväntas ske över tid. När du roterar nycklar är det viktigt att följa den här ordningen:

1. [Avgör vilken nyckel som används av ett index eller en synonym mappning](search-security-get-encryption-keys.md).
1. [Skapa en ny nyckel i Key Vault](../key-vault/keys/quick-create-portal.md), men lämna den ursprungliga nyckeln tillgänglig.
1. [Uppdatera egenskaperna för encryptionKey](/rest/api/searchservice/update-index) på en index-eller synonym mappning så att de använder de nya värdena. Endast objekt som ursprungligen har skapats med den här egenskapen kan uppdateras för att använda ett annat värde.
1. Inaktivera eller ta bort föregående nyckel i nyckel valvet. Övervaka nyckel åtkomst för att verifiera att den nya nyckeln används.

Av prestanda skäl cachelagrar Sök tjänsten nyckeln i upp till flera timmar. Om du inaktiverar eller tar bort nyckeln utan att ange en ny, fortsätter frågorna att fungera temporärt tills cachen upphör att gälla. Men när Sök tjänsten inte kan dekryptera innehåll visas följande meddelande: "åtkomst förbjuden. Den frågeparameter som används kan ha återkallats-försök igen. " 

## <a name="next-steps"></a>Nästa steg

Om du inte är bekant med Azures säkerhets arkitektur kan du läsa [dokumentationen för Azure-säkerhet](../security/index.yml)och i synnerhet den här artikeln:

> [!div class="nextstepaction"]
> [Vilande datakryptering](../security/fundamentals/encryption-atrest.md)