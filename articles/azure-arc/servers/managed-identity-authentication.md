---
title: Autentisera mot Azure-resurser med ARC-aktiverade servrar
description: I den här artikeln beskrivs Azure Instance Metadata Service stöd för Arc-aktiverade servrar och hur du kan autentisera mot Azure-resurser och lokala med en hemlighet.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96939197"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Autentisera mot Azure-resurser med ARC-aktiverade servrar

Program eller processer som körs direkt på en Azure Arc-aktiverad server kan utnyttja hanterade identiteter för att få åtkomst till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering. Ett program kan hämta en [åtkomsttoken som representerar dess](../../active-directory/develop/developer-glossary.md#access-token) identitet, som är systemtilldelad för Arc-aktiverade servrar och använder den som en "Bearer"-token för att autentisera sig mot en annan tjänst.

Mer detaljerad information om hanterade identiteter finns i [översikts dokumentationen för Managed Identity](../../active-directory/managed-identities-azure-resources/overview.md) , samt skillnaden mellan systemtilldelade och användarspecifika identiteter.

I den här artikeln visar vi hur en server kan använda en systemtilldelad hanterad identitet för att få åtkomst till Azure [Key Vault](../../key-vault/general/overview.md). Key Vault fungerar som en start, gör det möjligt för ditt klient program att använda en hemlighet för att komma åt resurser som inte skyddas av Azure Active Directory (AD). TLS/SSL-certifikat som används av dina IIS-webbservrar kan till exempel lagras i Azure Key Vault och distribuera certifikaten på ett säkert sätt till Windows-eller Linux-servrar utanför Azure.

## <a name="security-overview"></a>Säkerhetsöversikt

När du registrerar servern på Azure Arc-aktiverade servrar utförs flera åtgärder för att konfigureras med hjälp av en hanterad identitet, som liknar vad som utförs för en virtuell Azure-dator:

- Azure Resource Manager tar emot en begäran om att aktivera den systemtilldelade hanterade identiteten på den Arc-aktiverade servern.

- Azure Resource Manager skapar ett huvud namn för tjänsten i Azure AD för identiteten för servern. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.

- Azure Resource Manager konfigurerar identiteten på servern genom att uppdatera identitets slut punkten för Azure Instance Metadata Service (IMDS) för [Windows](../../virtual-machines/windows/instance-metadata-service.md) eller [Linux](../../virtual-machines/linux/instance-metadata-service.md) med klient-ID och certifikat för tjänstens huvud namn. Slut punkten är en REST-slutpunkt som endast är tillgänglig från servern med en välkänd, icke-flyttbar IP-adress. Den här tjänsten innehåller en delmängd metadatainformation om den Arc-aktiverade servern som hjälper dig att hantera och konfigurera den.

Miljön för en hanterad-Identity-aktiverad server kommer att konfigureras med följande variabler på en Windows Arc-aktiverad server:

- **IMDS_ENDPOINT**: IP-adressen för IMDS-slutpunkten `http://localhost:40342` för Arc-aktiverade servrar.

- **IDENTITY_ENDPOINT**: den localhost-slutpunkt som motsvarar tjänstens hanterade identitet `http://localhost:40342/metadata/identity/oauth2/token` .

Din kod som körs på servern kan begära en token från Azure instance metadata service-slutpunkt, som endast kan nås från servern.

System miljö variabeln **IDENTITY_ENDPOINT** används för att identifiera slut punkt för identiteter för program. Program bör försöka hämta **IDENTITY_ENDPOINT** och **IMDS_ENDPOINT** värden och använda dem. Program med valfri åtkomst nivå får göra förfrågningar till slut punkterna. Svar på metadata hanteras som normalt och ges till alla processer på datorn. Men när en begäran görs som skulle exponera en token kräver vi att klienten tillhandahåller en hemlighet för att intyga att de kan komma åt data som endast är tillgängliga för användare med högre privilegier.

## <a name="prerequisites"></a>Förutsättningar

- Förståelse för hanterade identiteter.
- En server som är ansluten och registrerad med ARC-aktiverade servrar.
- Du är medlem i [ägar gruppen](../../role-based-access-control/built-in-roles.md#owner)* * i prenumerationen eller resurs gruppen, för att kunna utföra nödvändiga steg för att skapa resurser och roll hantering.
- En Azure Key Vault för att lagra och hämta dina autentiseringsuppgifter. och tilldela Azure Arc Identity-åtkomst till nyckel valvet.

    - Om du inte har skapat en Key Vault, se [skapa Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Information om hur du konfigurerar åtkomst av den hanterade identitet som används av-servern finns i [bevilja åtkomst för Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) eller [bevilja åtkomst för Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). I steg nummer 5 ska du ange namnet på den Arc-aktiverade servern. Om du vill slutföra detta med hjälp av PowerShell läser du [tilldela en åtkomst princip med hjälp av PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Skaffa en åtkomsttoken med hjälp av REST API

Metoden för att hämta och använda en systemtilldelad hanterad identitet för att autentisera med Azure-resurser liknar hur den utförs med en virtuell Azure-dator.

För en ARC-aktiverad Windows Server med PowerShell anropar du webbegäran för att hämta token från den lokala värden i den aktuella porten. Ange begäran med hjälp av IP-adressen eller miljövariabeln **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

Följande svar är ett exempel som returneras:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="En lyckad hämtning av åtkomsttoken med PowerShell.":::

För en ARC-aktiverad Linux-server med hjälp av bash anropar du webbegäran för att hämta token från den lokala värden i den aktuella porten. Ange följande begäran med hjälp av IP-adressen eller miljövariabeln **IDENTITY_ENDPOINT**. Du behöver en SSH-klient för att slutföra det här steget.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

Följande svar är ett exempel som returneras:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="En lyckad hämtning av åtkomsttoken med hjälp av bash.":::

Svaret innehåller den åtkomsttoken som du behöver för att få åtkomst till alla resurser i Azure. För att slutföra konfigurationen för att autentisera till Azure Key Vault, se [åtkomst Key Vault med Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) eller [åtkomst Key Vault med Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Key Vault finns i [Key Vault översikt](../../key-vault/general/overview.md).

- Lär dig hur du tilldelar hanterad identitets åtkomst till en resurs [med hjälp av PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) eller med hjälp av [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).