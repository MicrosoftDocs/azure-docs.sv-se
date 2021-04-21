---
title: Kryptering i vila med kund hanterade nycklar
titleSuffix: Azure Cognitive Search
description: Komplettera kryptering på serversidan över index och synonymmappningar i Azure Cognitive Search med nycklar som du skapar och hanterar i Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813622"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurera kund hanterade nycklar för datakryptering i Azure Cognitive Search

Azure Cognitive Search krypterar automatiskt indexerat innehåll i vila med [tjänst-hanterade nycklar](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Om du behöver mer skydd kan du komplettera standardkrypteringen med ytterligare ett krypteringslager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault. Den här artikeln går igenom stegen för att konfigurera kund hanterad nyckelkryptering.

Kund hanterad nyckelkryptering är beroende av [Azure Key Vault](../key-vault/general/overview.md). Du kan skapa egna krypteringsnycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault:s API:er för att generera krypteringsnycklar. Med Azure Key Vault kan du också granska nyckelanvändning om du [aktiverar loggning.](../key-vault/general/logging.md)  

Kryptering med kund hanterade nycklar tillämpas på enskilda index eller synonymmappningar när dessa objekt skapas och anges inte på själva söktjänstnivån. Endast nya objekt kan krypteras. Du kan inte kryptera innehåll som redan finns.

Alla nycklar behöver inte finnas i samma nyckelvalv. En enda söktjänst kan vara värd för flera krypterade index eller synonymmappningar, var och en krypterad med sina egna kundbaserade krypteringsnycklar, som lagras i olika nyckelvalv. Du kan också ha index och synonymmappningar i samma tjänst som inte krypteras med kund hanterade nycklar.

>[!Important]
> Om du implementerar kund hanterade nycklar måste du följa strikta procedurer under rutinmässig rotation av nyckelvalvsnycklar och Active Directory-programhemligheter och registrering. Uppdatera alltid allt krypterat innehåll för att använda nya hemligheter och nycklar innan du tar bort de gamla. Om du missar det här steget kan innehållet inte dekrypteras.

## <a name="double-encryption"></a>Dubbel kryptering

För tjänster som skapats efter 1 augusti 2020 och i vissa regioner inkluderar omfånget för kundstyrd nyckelkryptering tillfälliga diskar, vilket uppnår [fullständig](search-security-overview.md#double-encryption)dubbel kryptering , som för närvarande är tillgängligt i dessa regioner: 

+ USA, västra 2
+ East US
+ USA, södra centrala
+ US Gov, Virginia
+ US Gov, Arizona

Om du använder en annan region, eller en tjänst som skapats före den 1 augusti, begränsas krypteringen av hanterade nycklar till endast datadisken, exklusive de temporära diskar som används av tjänsten.

## <a name="prerequisites"></a>Förutsättningar

Följande verktyg och tjänster används i det här scenariot.

+ [Azure Cognitive Search](search-create-service-portal.md) på en [fakturerbar nivå](search-sku-tier.md#tier-descriptions) (Basic eller högre i valfri region).
+ [Azure Key Vault](../key-vault/general/overview.md)kan du skapa nyckelvalv med [Azure Portal,](../key-vault//general/quick-create-portal.md) [Azure CLI](../key-vault//general/quick-create-cli.md)eller [Azure PowerShell](../key-vault//general/quick-create-powershell.md). i samma prenumeration som Azure Cognitive Search. Mjuk borttagning och rensning **måste vara aktiverat för** **nyckelvalvet.**
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Om du inte har någon kan du [konfigurera en ny klientorganisation.](../active-directory/develop/quickstart-create-new-tenant.md)

Du bör ha ett sökprogram som kan skapa det krypterade objektet. I den här koden refererar du till en nyckelvalvsnyckel och Active Directory-registreringsinformation. Den här koden kan vara en fungerande app eller prototypkod som [C#-kodexempel DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Du kan använda [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)eller [Azure PowerShell](./search-get-started-powershell.md)för att anropa REST API:er som skapar index och synonymmappningar som innehåller en krypteringsnyckelparameter. Det finns inget portalstöd för att lägga till en nyckel i index eller synonymmappningar just nu.

## <a name="1---enable-key-recovery"></a>1 – Aktivera nyckelåterställning

På grund av typen av kryptering med kund hanterade nycklar kan ingen hämta dina data om din Azure Key Vault-nyckel tas bort. För att förhindra dataförlust på grund Key Vault av misstag måste skydd för mjuk borttagning och rensning aktiveras i nyckelvalvet. Mjuk borttagning är aktiverat som standard, så du kommer bara att stöta på problem om du har inaktiverat det. Rensningsskydd är inte aktiverat som standard, men det krävs för kund hanterad nyckelkryptering i Cognitive Search. Mer information finns i [Översikt över mjuk borttagning](../key-vault/general/soft-delete-overview.md) och [rensningsskydd.](../key-vault/general/soft-delete-overview.md#purge-protection)

Du kan ange båda egenskaperna med hjälp av portalen, PowerShell eller Azure CLI-kommandon.

### <a name="using-azure-portal"></a>Använda Azure Portal

1. [Logga in på Azure Portal öppna](https://portal.azure.com) översiktssidan för nyckelvalvet.

1. På sidan **Översikt** under **Essentials** aktiverar du **mjuk borttagning** och **rensningsskydd.**

### <a name="using-powershell"></a>Använda PowerShell

1. Kör `Connect-AzAccount` för att konfigurera dina autentiseringsuppgifter för Azure.

1. Kör följande kommando för att ansluta till nyckelvalvet och ersätt `<vault_name>` med ett giltigt namn:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault skapas med mjuk borttagning aktiverat. Om det är inaktiverat i valvet kör du följande kommando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Aktivera rensningsskydd:

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

1. [Logga in på Azure Portal öppna](https://portal.azure.com) översiktssidan för nyckelvalvet.

1. Välj **Nycklar** till vänster och välj sedan **+ Generera/importera**.

1. I fönstret **Skapa en nyckel** i listan med Alternativ **väljer** du den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel, **ladda upp** en befintlig nyckel eller använda Återställ säkerhetskopia **för** att välja en säkerhetskopia av en nyckel.

1. Ange ett **Namn** för nyckeln och välj eventuellt andra nyckelegenskaper.

1. Välj **Skapa** för att starta distributionen.

1. Anteckna nyckelidentifieraren – den består av **nyckelvärdets URI,** **nyckelnamnet** och **nyckelversionen**. Du behöver identifieraren för att definiera ett krypterat index i Azure Cognitive Search.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Skapa en ny nyckelvalvsnyckel":::

## <a name="3---register-an-app-in-active-directory"></a>3 – Registrera en app i Active Directory

1. I [Azure Portal](https://portal.azure.com)söker du efter Azure Active Directory resurs för din prenumeration.

1. Till vänster under Hantera **väljer** du **Appregistreringar** och sedan **Ny registrering.**

1. Ge registreringen ett namn, kanske ett namn som liknar namnet på sökprogrammet. Välj **Register** (Registrera).

1. När appregistreringen har skapats kopierar du program-ID:t. Du måste ange den här strängen till ditt program. 

   Om du stegar igenom [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)klistrar du in det här **värdet iappsettings.jspå** filen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Program-ID i avsnittet Essentials":::

1. Välj sedan **Certifikat & hemligheter** till vänster.

1. Välj **Ny klienthemlighet**. Ge hemligheten ett visningsnamn och välj Lägg **till**.

1. Kopiera programhemligheten. Om du stegar igenom exemplet klistrar du in det här värdet **iappsettings.jspå** filen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Apphemlighet":::

## <a name="4---grant-key-access-permissions"></a>4 – Bevilja åtkomstbehörigheter för nycklar

I det här steget skapar du en åtkomstprincip i Key Vault. Den här principen ger programmet som du registrerade med Active Directory behörighet att använda din kund hanterade nyckel.

Åtkomstbehörigheter kan återkallas när som helst. När det har återkallats blir alla söktjänstindex eller synonymmappning som använder nyckelvalvet oanvändbara. Om du återställer åtkomstbehörigheter för Key Vault vid ett senare tillfälle återställs mappningsåtkomsten för index\synonymer. Mer information finns i [Säker åtkomst till ett nyckelvalv.](../key-vault/general/security-features.md)

1. Öppna sidan Översikt Azure Portal nyckelvalvet i **Azure Portal-fliken.** 

1. Välj **Åtkomstprinciper** till vänster och välj **+ Lägg till åtkomstprincip.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Lägga till en ny åtkomstprincip för nyckelvalv":::

1. Välj **Välj huvudnamn** och välj det program som du registrerade med Active Directory. Du kan söka efter den efter namn.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Välj key vault access policy principal (Huvudnamn för nyckelvalvsåtkomstprincip)":::

1. I **Nyckelbehörigheter** väljer *du Hämta,* *Packa upp nyckel och* *Omslut nyckeln*.

1. I **Hemliga behörigheter** väljer du *Hämta*.

1. I **Certifikatbehörigheter** väljer du *Hämta*.

1. Välj **Lägg till** och sedan **Spara.**

> [!Important]
> Krypterat innehåll i Azure Cognitive Search har konfigurerats för att använda en Azure Key Vault nyckel med en specifik **version**. Om du ändrar nyckeln eller versionen måste indexet eller synonymmappningen  uppdateras så att den nya nyckeln\versionen används innan du tar bort den tidigare nyckeln\versionen. Om du inte gör det blir indexet eller synonymmappningen oanvändbar. Då kan du inte dekryptera innehållet när nyckelåtkomsten har gått förlorad.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 – Kryptera innehåll

Om du vill lägga till en kund hanterad nyckel i ett index, en datakälla, en kunskapsuppsättning, en indexerare eller en synonymmappning, måste du använda [Sök REST API](/rest/api/searchservice/) eller ett SDK. Portalen exponerar inte synonymmappningar eller krypteringsegenskaper. När du använder ett giltigt API-index stöder datakällor, kompetensuppsättningar, indexerare och synonymmappningar en krypteringsnyckelegenskap **på toppnivå.**

I det här exemplet används REST API, med värden för Azure Key Vault och Azure Active Directory:

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
> Ingen av dessa nyckelvalvsuppgifter betraktas som hemliga och kan enkelt hämtas genom att bläddra till relevant Azure Key Vault nyckelsida i Azure Portal.

## <a name="example-index-encryption"></a>Exempel: Indexkryptering

Skapa ett krypterat index med hjälp [av skapa index Azure Cognitive Search REST API](/rest/api/searchservice/create-index). Använd egenskapen `encryptionKey` för att ange vilken krypteringsnyckel som ska användas.
> [!Note]
> Ingen av dessa nyckelvalvsuppgifter betraktas som hemliga och kan enkelt hämtas genom att bläddra till relevant Azure Key Vault nyckelsida i Azure Portal.

## <a name="rest-examples"></a>REST-exempel

Det här avsnittet visar den fullständiga JSON-filen för ett krypterat index och en synonymmappning

### <a name="index-encryption"></a>Indexkryptering

Information om hur du skapar ett nytt index via REST API finns i Skapa [index (REST API),](/rest/api/searchservice/create-index)där den enda skillnaden här är att ange krypteringsnyckelinformationen som en del av indexdefinitionen:

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

Du kan nu skicka begäran om att skapa index och sedan börja använda indexet på vanligt sätt.

### <a name="synonym-map-encryption"></a>Kryptering av synonymmappning

Skapa en krypterad synonymmappning med hjälp [av skapa synonymmappning Azure Cognitive Search REST API](/rest/api/searchservice/create-synonym-map). Använd egenskapen `encryptionKey` för att ange vilken krypteringsnyckel som ska användas.

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

Nu kan du skicka begäran om att skapa synonymmappningen och sedan börja använda den normalt.

## <a name="example-data-source-encryption"></a>Exempel: Kryptering av datakälla

Skapa en krypterad datakälla med [hjälp av Skapa datakälla (Azure Cognitive Search REST API).](/rest/api/searchservice/create-data-source) Använd egenskapen `encryptionKey` för att ange vilken krypteringsnyckel som ska användas.

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

Du kan nu skicka begäran om att skapa datakällan och sedan börja använda den som vanligt.

## <a name="example-skillset-encryption"></a>Exempel: Kunskapsuppsättningskryptering

Skapa en krypterad kunskapsuppsättning med hjälp [av skapa kunskapsuppsättning Azure Cognitive Search REST API](/rest/api/searchservice/create-skillset). Använd egenskapen `encryptionKey` för att ange vilken krypteringsnyckel som ska användas.

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

Nu kan du skicka begäran om att skapa kunskapsuppsättningen och sedan börja använda den normalt.

## <a name="example-indexer-encryption"></a>Exempel: Indexerarkryptering

Skapa en krypterad indexerare med [hjälp av Create Indexer Azure Cognitive Search REST API](/rest/api/searchservice/create-indexer). Använd egenskapen `encryptionKey` för att ange vilken krypteringsnyckel som ska användas.

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

Nu kan du skicka begäran om att skapa indexeraren och sedan börja använda den som vanligt.

>[!Important]
> Det går inte att lägga till befintliga sökindex eller synonymmappningar, men det kan uppdateras genom att ange olika värden för någon av de tre nyckelvalvsinformationen (till exempel uppdatering av `encryptionKey` nyckelversionen). När du ändrar till en ny Key Vault-nyckel eller en ny nyckelversion måste alla sökindex eller synonymmappning  som använder nyckeln först uppdateras så att den nya nyckeln\versionen används innan den tidigare nyckeln\versionen tas bort. Om du inte gör det återges indexet eller synonymmappningen som oanvändbar, eftersom den inte kan dekryptera innehållet när nyckelåtkomsten går förlorad. Även om du återställer åtkomstbehörigheter för Key Vault vid ett senare tillfälle återställs innehållsåtkomsten.

## <a name="simpler-alternative-trusted-service"></a>Enklare alternativ: Betrodd tjänst

Beroende på klientkonfiguration och autentiseringskrav kan du implementera en enklare metod för åtkomst till en nyckelvalvsnyckel. I stället för att skapa och använda ett Active Directory-program kan du göra en söktjänst till en betrodd tjänst genom att aktivera en system hanterad identitet för den. Du skulle sedan använda den betrodda söktjänsten som en säkerhetsprincip i stället för ett AD-registrerat program för att få åtkomst till nyckelvalvsnyckeln.

Med den här metoden kan du utelämna stegen för programregistrering och programhemligheter och förenklar en krypteringsnyckeldefinition till bara nyckelvalvskomponenterna (URI, valvnamn, nyckelversion).

I allmänhet gör en hanterad identitet att din söktjänst kan autentisera till Azure Key Vault utan att lagra autentiseringsuppgifter (ApplicationID eller ApplicationSecret) i kod. Livscykeln för den här typen av hanterad identitet är kopplad till söktjänstens livscykel, som bara kan ha en hanterad identitet. Mer information om hur hanterade identiteter fungerar finns i [Vad är hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/overview.md)

1. Gör söktjänsten till en betrodd tjänst.
   ![Aktivera system tilldelad hanterad identitet](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivera system tilldelad hanterad identitet")

1. När du ställer in en åtkomstprincip i Azure Key Vault du den betrodda söktjänsten som princip (i stället för det AD-registrerade programmet). Tilldela samma behörigheter (flera GET,WRAP, UNWRAP) enligt anvisningarna i steget Bevilja åtkomstnyckelbehörigheter.

1. Använd en förenklad konstruktion av `encryptionKey` som utelämnar Active Directory-egenskaperna.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Villkor som förhindrar att du använder den här förenklade metoden är:

+ Du kan inte ge söktjänsten direkt åtkomstbehörighet till nyckelvalvet (till exempel om söktjänsten finns i en annan Active Directory-klient än Azure Key Vault).

+ En enda söktjänst krävs för att vara värd för flera krypterade index\synonymmappningar, där var och en använder en annan nyckel från ett annat nyckelvalv, där varje nyckelvalv måste använda en annan identitet **för** autentisering. Eftersom en söktjänst bara kan ha en hanterad identitet, diskvalificerar ett krav för flera identiteter den förenklade metoden för ditt scenario.  

## <a name="work-with-encrypted-content"></a>Arbeta med krypterat innehåll

Med kund hanterad nyckelkryptering ser du svarstiden för både indexering och frågor på grund av det extra arbetet med kryptering/dekryptering. Azure Cognitive Search loggar inte krypteringsaktiviteten, men du kan övervaka nyckelåtkomst via nyckelvalvsloggning. Vi rekommenderar att du aktiverar [loggning som](../key-vault/general/logging.md) en del av konfigurationen av nyckelvalvet.

Nyckelrotation förväntas ske med tiden. När du roterar nycklar är det viktigt att du följer den här sekvensen:

1. [Fastställ nyckeln som används av ett index eller en synonymmappning](search-security-get-encryption-keys.md).
1. [Skapa en ny nyckel i nyckelvalvet](../key-vault/keys/quick-create-portal.md), men lämna den ursprungliga nyckeln tillgänglig.
1. [Uppdatera egenskaperna encryptionKey i](/rest/api/searchservice/update-index) ett index eller en synonymmappning för att använda de nya värdena. Endast objekt som ursprungligen skapades med den här egenskapen kan uppdateras för att använda ett annat värde.
1. Inaktivera eller ta bort den tidigare nyckeln i nyckelvalvet. Övervaka nyckelåtkomst för att kontrollera att den nya nyckeln används.

Av prestandaskäl cachelagrar söktjänsten nyckeln i upp till flera timmar. Om du inaktiverar eller tar bort nyckeln utan att ange en ny fortsätter frågorna att fungera tillfälligt tills cachen upphör att gälla. Men när söktjänsten inte kan dekryptera innehåll får du det här meddelandet: "Åtkomst nekas. Den frågenyckel som används kan ha återkallats – försök igen." 

## <a name="next-steps"></a>Nästa steg

Om du inte är bekant med Azure-säkerhetsarkitekturen kan du läsa [dokumentationen om Azure-säkerhet,](../security/index.yml)särskilt den här artikeln:

> [!div class="nextstepaction"]
> [Vilande datakryptering](../security/fundamentals/encryption-atrest.md)