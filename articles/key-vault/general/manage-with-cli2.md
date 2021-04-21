---
title: Hantera Azure Key Vault cli – Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att automatisera vanliga uppgifter Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b29b668b21c375dd1202652b5093526f648c300
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749608"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Hantera Key Vault med hjälp av Azure CLI 

Den här artikeln beskriver hur du kommer igång med att Azure Key Vault med hjälp av Azure CLI.  Du kan se information om:

- Så här skapar du en härdad container (ett valv) i Azure
- Lägga till en nyckel, en hemlighet eller ett certifikat i nyckelvalvet
- Registrera ett program med Azure Active Directory
- Auktorisera ett program att använda en nyckel eller hemlighet
- Ställa in avancerade åtkomstprinciper för Key Vault
- Arbeta med maskinvarusäkerhetsmoduler (HSM)
- Ta bort nyckelvalvet och associerade nycklar och hemligheter
- Diverse kommandon för Azures plattformsoberoende kommandoradsgränssnitt


Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Den här artikeln innehåller inte instruktioner om hur du skriver Azure-programmet som ingår i något av stegen, som visar hur du auktoriserar ett program att använda en nyckel eller hemlighet i nyckelvalvet.
>

En översikt över Azure Key Vault finns i [Vad är Azure Key Vault?](overview.md)) Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-kommandona i den här artikeln måste du ha följande:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line Interface version 2.0 eller senare. Information om hur du installerar den senaste versionen [finns i Installera Azure CLI.](/cli/azure/install-azure-cli)
* Ett program som ska konfigureras för att använda den nyckel eller det lösenord som du skapar i den här artikeln. Ett exempelprogram är tillgängligt från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i den readme-fil som ingår.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azure Cross-Platform Command-Line Interface

Den här artikeln förutsätter att du är bekant med kommandoradsgränssnittet (Bash, Terminal, Kommandotolken).

Parametern --help eller -h kan användas för att visa hjälp för specifika kommandon. Alternativt kan Azure-hjälpen [kommando] [alternativ] även användas. Om du är osäker på vilka parametrar som krävs av ett kommando kan du läsa hjälpen. Till exempel returnerar följande kommandon samma information:

```azurecli
az account set --help
az account set -h
```

Du kan också läsa följande artiklar för att bekanta dig Azure Resource Manager i Azure Cross-Platform Command-Line Interface:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Så här skapar du en härdad container (ett valv) i Azure

Valv är skyddade containrar som backas upp av maskinvarusäkerhetsmoduler. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vault kontrollerar och loggar dessutom åtkomsten till allt som lagras i valven. Azure Key Vault kan hantera förfrågningar om och förnyelser av TLS-certifikat (Transport Layer Security), och ger tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat. I nästa steg skapar du ett valv.

### <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

Om du vill logga in interaktivt använder du följande kommando:

```azurecli
az login
```
Om du vill logga in med ett organisationskonto kan du ange ditt användarnamn och lösenord.

```azurecli
az login -u username@domain.com -p password
```

Om du har mer än en prenumeration och behöver ange vilken du vill använda skriver du följande för att se prenumerationerna för ditt konto:

```azurecli
az account list
```

Ange en prenumeration med prenumerationsparametern.

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azure Cross-Platform Command-Line Interface finns [i Installera Azure CLI.](/cli/azure/install-azure-cli)

### <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp

När du Azure Resource Manager skapas alla relaterade resurser i en resursgrupp. Du kan skapa ett nyckelvalv i en befintlig resursgrupp. Om du vill använda en ny resursgrupp kan du skapa en ny.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Den första parametern är resursgruppens namn och den andra parametern är platsen. Så här hämtar du en lista över alla möjliga platser:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrera Key Vault resursprovidern

 Du kan se felet "Prenumerationen har inte registrerats för användning av namnområdet Microsoft.KeyVault" när du försöker skapa ett nytt nyckelvalv. Om meddelandet visas kontrollerar du att Key Vault har registrerats i din prenumeration. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd kommandot `az keyvault create` för att skapa ett nyckelvalv. Det här skriptet har tre obligatoriska parametrar: ett resursgruppsnamn, ett nyckelvalvsnamn och den geografiska platsen.

Om du vill skapa ett nytt **valv** med namnet **ContosoKeyVault** i resursgruppen **ContosoResourceGroup,** som finns på Asien, östra plats, skriver du: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Utdata från det här kommandot visar egenskaperna för nyckelvalvet som du har skapat. De två viktigaste egenskaperna är:

* **name**: I exemplet är namnet ContosoKeyVault. Du använder det här namnet för andra Key Vault kommandon.
* **vaultUri:** I exemplet är URI:en https://contosokeyvault.vault.azure.net . Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Än så länge har ingen annan behörighet.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Lägga till en nyckel, en hemlighet eller ett certifikat i nyckelvalvet

Om du Azure Key Vault skapa en programvaruskyddad nyckel åt dig använder du `az key create` kommandot .

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Om du har en befintlig nyckel i en .pem-fil kan du ladda upp den till Azure Key Vault. Du kan välja att skydda nyckeln med programvara eller HSM. Det här exemplet importerar nyckeln från .pem-filen och skyddar den med programvara med lösenordet "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Nu kan du referera till nyckeln som du skapade eller överförde till Azure Key Vault med hjälp av dess URI. Använd `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` för att alltid hämta den aktuella versionen. Använd https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] för att hämta den här specifika versionen. Till exempel `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Lägg till en hemlighet i valvet, som är ett lösenord med namnet SQLPassword, och som har värdet "hVFkk965BuUv" i Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Referera till det här lösenordet med hjälp av dess URI. Använd för att alltid hämta den aktuella **https://ContosoVault.vault.azure.net/secrets/SQLPassword** versionen och https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] för att hämta den här specifika versionen. Till exempel **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importera ett certifikat till valvet med hjälp av en .pem eller .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Nu ska vi visa nyckeln, hemligheten eller certifikatet som du skapade:

* Om du vill visa dina nycklar skriver du: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Om du vill visa dina hemligheter skriver du: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Om du vill visa certifikat skriver du: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory

Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifikt för Azure Key Vault men ingår här för medvetenhet. För att slutföra appregistreringen måste ditt konto, valvet och programmet finnas i samma Azure-katalog.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory.  Programmets ägare måste först registrera den i Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

- Ett **program-ID** (även kallat AAD-klient-ID eller appID)
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur ett program konfigureras för att hämta en token beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory finns i artiklarna Integrera program med [Azure Active Directory,](../../active-directory/develop/quickstart-register-app.md)Använda portalen för att skapa ett [Azure Active Directory-program](../../active-directory/develop/howto-create-service-principal-portal.md)och tjänstens huvudnamn som har åtkomst till resurser och Skapa ett huvudnamn för [Azure-tjänsten med Azure CLI.](/cli/azure/create-an-azure-service-principal-azure-cli)

Registrera ett program i Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Auktorisera ett program att använda en nyckel eller hemlighet

Om du vill ge programmet åtkomst till nyckeln eller hemligheten i valvet använder du `az keyvault set-policy` kommandot .

Om ditt valvnamn till exempel är ContosoKeyVault har programmet ett appID på 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill ge programmet behörighet att dekryptera och logga in med nycklar i valvet med följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Om du vill ge samma program behörighet att läsa hemligheter i valvet skriver du följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Ställa in avancerade åtkomstprinciper för nyckelvalv

Använd [az keyvault update för](/cli/azure/keyvault#az-keyvault-update) att aktivera avancerade principer för nyckelvalvet.

 Aktivera Key Vault för distribution: Tillåter att virtuella datorer hämtar certifikat som lagras som hemligheter från valvet.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Aktivera Key Vault för diskkryptering: Krävs när du använder valvet för Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Aktivera Key Vault malldistribution: Tillåter Resource Manager att hämta hemligheter från valvet.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Arbeta med maskinvarusäkerhetsmoduler (HSM)

För ytterligare säkerhet kan du importera eller generera nycklar från maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till Ta bort nyckelvalvet och associerade nycklar och hemligheter.

Om du vill skapa HSM-skyddade nycklar behöver du en valvprenumeration som stöder HSM-skyddade nycklar.

När du skapar nyckelvalvet lägger du till parametern "sku":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Om du vill skapa en HSM-skyddad nyckel anger du målparametern till "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Du kan använda följande kommando för att importera en nyckel från en .pem-fil på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Mer detaljerade anvisningar om hur du skapar det här BYOK-paketet finns i Så här använder [du HSM-Protected-nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter

Om du inte längre behöver nyckelvalvet och dess nycklar eller hemligheter kan du ta bort nyckelvalvet med hjälp av `az keyvault delete` kommandot :

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse kommandoradskommandon för Azure-plattformsoberoende

Andra kommandon som kan vara användbara för att hantera Azure Key Vault.

Det här kommandot visar en tabellvisning av alla nycklar och valda egenskaper:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Det här kommandot visar en tabellvisning av alla hemliga namn och valda egenskaper:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Här är ett exempel på hur du tar bort en specifik nyckel:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Här är ett exempel på hur du tar bort en specifik hemlighet:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Nästa steg

- Fullständig Azure CLI-referens för nyckelvalvskommandon finns i [Key Vault CLI-referens.](/cli/azure/keyvault)

- Programmeringsreferenser [finns i Azure Key Vault utvecklarhandbok](developers-guide.md)

- Mer information om Azure Key Vault och HSM:er finns i Använda [HSM-Protected nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).
