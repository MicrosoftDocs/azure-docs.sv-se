---
title: Hantera Azure Key Vault med CLI-Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att automatisera vanliga uppgifter i Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb7e047d998342125a52af5ea3ae1e88fe88d313
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93289896"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Hantera Key Vault med Azure CLI 

Den här artikeln beskriver hur du kommer igång med Azure Key Vault med hjälp av Azure CLI.  Du kan se information om:

- Så här skapar du en härdnings behållare (ett valv) i Azure
- Lägga till en nyckel, hemlighet eller certifikat i nyckel valvet
- Registrera ett program med Azure Active Directory
- Auktorisera ett program för att använda en nyckel eller hemlighet
- Ange avancerade åtkomst principer för nyckel valv
- Arbeta med HSM: er (Hardware Security modules)
- Tar bort nyckel valvet och associerade nycklar och hemligheter
- Diverse kommando rads kommandon för Azures plattforms oberoende kommando


Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Den här artikeln innehåller inte instruktioner för hur du skriver Azure-programmet som ett av stegen innehåller, som visar hur du auktoriserar ett program att använda en nyckel eller hemlighet i nyckel valvet.
>

En översikt över Azure Key Vault finns i [Vad är Azure Key Vault?](overview.md)) Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-kommandona i den här artikeln måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line-gränssnitt version 2,0 eller senare. Information om hur du installerar den senaste versionen finns i [Installera Azure CLI](/cli/azure/install-azure-cli).
* Ett program som ska konfigureras för att använda nyckeln eller lösen ordet som du skapar i den här artikeln. Ett exempelprogram är tillgängligt från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i Readme-filen som ingår.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azures plattforms oberoende Command-Line gränssnitt

Den här artikeln förutsätter att du är bekant med kommando rads gränssnittet (bash, Terminal, kommando tolken).

--Hjälp-eller-h-parametern kan användas för att visa hjälp för vissa kommandon. Alternativt kan du även använda formatet Azure Help [kommando] [alternativ]. Vid tvivel om de parametrar som krävs av ett kommando, se hjälp. Följande kommandon returnerar till exempel samma information:

```azurecli
az account set --help
az account set -h
```

Du kan också läsa följande artiklar om du vill bekanta dig med Azure Resource Manager i Azures plattforms oberoende Command-Line gränssnitt:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Så här skapar du en härdnings behållare (ett valv) i Azure

Valv är skyddade behållare som backas upp av säkerhetsmoduler för maskin vara. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vault kontrollerar och loggar dessutom åtkomsten till allt som lagras i valven. Azure Key Vault kan hantera förfrågningar om och förnyelser av TLS-certifikat (Transport Layer Security), och ger tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat. I nästa steg ska du skapa ett valv.

### <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

Använd följande kommando för att logga in interaktivt:

```azurecli
az login
```
Om du vill logga in med ett organisations konto kan du skicka in ditt användar namn och lösen ord.

```azurecli
az login -u username@domain.com -p password
```

Om du har mer än en prenumeration och behöver ange vilken som ska användas, skriver du följande för att Visa prenumerationerna för ditt konto:

```azurecli
az account list
```

Ange en prenumeration med prenumerations parametern.

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azures plattforms oberoende Command-Line gränssnitt finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp

När du använder Azure Resource Manager skapas alla relaterade resurser i en resurs grupp. Du kan skapa ett nyckel valv i en befintlig resurs grupp. Om du vill använda en ny resurs grupp kan du skapa en ny.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Den första parametern är resurs gruppens namn och den andra parametern är platsen. Så här hämtar du en lista över alla möjliga plats typer:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrera Key Vault Resource Provider

 Du kan se felet "prenumerationen är inte registrerad för att använda namn området" Microsoft. Key Vault "" när du försöker skapa ett nytt nyckel valv. Om det meddelandet visas kontrollerar du att Key Vault Resource provider har registrerats i din prenumeration. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd `az keyvault create` kommandot för att skapa ett nyckel valv. Det här skriptet har tre obligatoriska parametrar: ett resurs grupps namn, ett nyckel valv namn och den geografiska platsen.

Om du vill skapa ett nytt valv med namnet **ContosoKeyVault** i resurs gruppen  **ContosoResourceGroup**, som finns på **Asien, östra** plats, skriver du: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Utdata från det här kommandot visar egenskaper för nyckel valvet som du har skapat. De två viktigaste egenskaperna är:

* **namn**: i exemplet är namnet ContosoKeyVault. Du kommer att använda det här namnet för andra Key Vault-kommandon.
* **vaultUri**: i exemplet är URI: n https://contosokeyvault.vault.azure.net . Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Från och med har ingen annan behörighet.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Lägga till en nyckel, hemlighet eller certifikat i nyckel valvet

Om du vill Azure Key Vault skapa en skyddad nyckel för dig använder du `az key create` kommandot.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Om du har en befintlig nyckel i en. PEM-fil kan du överföra den till Azure Key Vault. Du kan välja att skydda nyckeln med program vara eller HSM. I det här exemplet importeras nyckeln från. pem-filen och skyddas med program vara med hjälp av lösen ordet "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Nu kan du referera till nyckeln som du har skapat eller laddat upp till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** för att alltid hämta den aktuella versionen. Använd https://[Key Vault-Name]. valv. Azure. net/Keys/[nyckel namn]/[nyckel-Unique-ID] för att hämta den här specifika versionen. Till exempel **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Lägg till en hemlighet till valvet, som är ett lösen ord med namnet SQLPassword, och som har värdet "hVFkk965BuUv" till Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Referera till det här lösen ordet med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** för att alltid hämta den aktuella versionen och https://[nyckel valv-Name]. valv. Azure. net/Secret/[Secret-Name]/[Secret-Unique-ID] för att hämta den här specifika versionen. Till exempel **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importera ett certifikat till valvet med hjälp av en. pem eller. pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Nu ska vi Visa nyckeln, hemligheten eller certifikatet som du har skapat:

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

Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte särskilt för Azure Key Vault men ingår här, för kännedom. För att slutföra registreringen av appen, ditt konto, valvet och programmet måste finnas i samma Azure-katalog.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory.  Programmets ägare måste först registrera det i Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

- Ett **program-ID** (kallas även AAD-klient-ID eller AppID)
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur ett program konfigureras för att hämta en token beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory du bör läsa avsnitten [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md)och [skapa ett Azure-tjänstens huvud namn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Registrera ett program i Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Auktorisera ett program för att använda en nyckel eller hemlighet

Använd kommandot för att ge programmet åtkomst till nyckeln eller hemligheten i valvet `az keyvault set-policy` .

Om ditt valv till exempel är ContosoKeyVault har programmet ett appID för 8f8c4bbd-485b-45FD-98f7-ec6300b7b4ed och du vill ge programmet behörighet att dekryptera och signera med nycklar i valvet, använder du följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Om du vill ge samma program behörighet att läsa hemligheter i valvet skriver du följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Ange avancerade åtkomst principer för nyckel valv

Använd [AZ-uppdateringen](/cli/azure/keyvault#az-keyvault-update) för att aktivera avancerade principer för nyckel valvet.

 Aktivera Key Vault för distribution: tillåter att virtuella datorer hämtar certifikat som lagras som hemligheter från valvet.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Aktivera Key Vault för disk kryptering: krävs när du använder valvet för Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Aktivera Key Vault för mall distribution: tillåter Resource Manager att hämta hemligheter från valvet.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Arbeta med HSM: er (Hardware Security modules)

För extra trygghet kan du importera eller generera nycklar från HSM: er (Hardware Security modules) som aldrig lämnar HSM-gränser. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till Ta bort nyckelvalvet och associerade nycklar och hemligheter.

Om du vill skapa HSM-skyddade nycklar behöver du en valvprenumeration som stöder HSM-skyddade nycklar.

När du skapar ett nyckel valv lägger du till parametern SKU:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Om du vill skapa en HSM-skyddad nyckel anger du mål parametern till HSM:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Du kan använda följande kommando för att importera en nyckel från en. PEM-fil på din dator. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Nästa kommando importerar ett BYOK-paket ("ta med din egen nyckel"). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Mer detaljerad information om hur du skapar det här BYOK-paketet finns i [så här använder du HSM-Protected nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Tar bort nyckel valvet och associerade nycklar och hemligheter

Om du inte längre behöver nyckel valvet och dess nycklar eller hemligheter kan du ta bort nyckel valvet med hjälp av `az keyvault delete` kommandot:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse kommando rads kommandon för Azures plattforms oberoende kommando

Andra kommandon som kan vara användbara för att hantera Azure Key Vault.

Med det här kommandot visas en tabell över alla nycklar och valda egenskaper:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Det här kommandot visar en tabell över alla hemliga namn och valda egenskaper:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Här är ett exempel på hur du tar bort en enskild nyckel:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Här är ett exempel på hur du tar bort en speciell hemlighet:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Nästa steg

- En fullständig Azure CLI-referens för Key Vault-kommandon finns i [Key Vault CLI-referens](/cli/azure/keyvault).

- Programmerings referenser finns i [Azure Key Vault Developer ' s guide](developers-guide.md)

- Information om Azure Key Vault och HSM: er finns i [så här använder du HSM-Protected nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).