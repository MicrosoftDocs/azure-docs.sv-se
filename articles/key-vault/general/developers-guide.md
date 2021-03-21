---
title: Utvecklarguide för Azure Key Vault
description: Utvecklare kan använda Azure Key Vault för att hantera kryptografiska nycklar i Microsoft Azures miljön.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 550084ccbb1df24fe0cbc0a4630efe19e6685e5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709911"
---
# <a name="azure-key-vault-developers-guide"></a>Utvecklarguide för Azure Key Vault

Med Key Vault kan du på ett säkert sätt komma åt känslig information i dina program:

- Nycklar, hemligheter och certifikat skyddas utan att du behöver skriva koden själv och du kan enkelt använda dem från dina program.
- Du låter kunderna äga och hantera sina egna nycklar, hemligheter och certifikat så att du kan koncentrera dig på att tillhandahålla viktiga program varu funktioner. På så sätt kommer dina program inte att äga ansvar eller potentiella ansvar för kundernas klient nycklar, hemligheter och certifikat.
- Ditt program kan använda nycklar för signering och kryptering och samtidigt behålla nyckel hanteringen som är extern från ditt program. Mer information om nycklar finns i [om nycklar](../keys/about-keys.md)
- Du kan hantera autentiseringsuppgifter som lösen ord, åtkomst nycklar och SAS-token genom att lagra dem i Key Vault som hemligheter, se [om hemligheter](../secrets/about-secrets.md)
- Hantera certifikat. Mer information finns i [om certifikat](../certificates/about-certificates.md)

Mer allmän information om Azure Key Vault finns i [Vad är Key Vault](overview.md).

## <a name="public-previews"></a>Offentliga för hands versionerna

Med jämna mellanrum släpper vi en offentlig för hands version av en ny Key Vault funktion. Testa de allmänna för hands funktionerna och berätta för oss vad du tycker via azurekeyvault@microsoft.com , vår e-postadress för feedback.

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera nyckel valv

Key Vault hantering, som liknar andra Azure-tjänster, görs via Azure Resource Manager-tjänsten. Azure Resource Manager är Azures tjänst för distribution och hantering. Den ger dig ett hanteringslager där du kan skapa, uppdatera och ta bort resurser i ditt Azure-konto. Mer information finns i [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Åtkomst till hanterings lagret styrs av [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/overview.md). I Key Vault, som även kallas hanterings-eller kontroll plan, kan du skapa och hantera nyckel valv och dess attribut, inklusive åtkomst principer, men inte nycklar, hemligheter och certifikat, som hanteras på data planet. Du kan använda fördefinierad `Key Vault Contributor` roll för att bevilja hanterings åtkomst till Key Vault.     

**API: er och SDK: er för hantering av nyckel valv:**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault)<br>[Snabbstart](quick-create-cli.md)|[Referens](/powershell/module/az.keyvault)<br>[Snabbstart](quick-create-powershell.md)|[Referens](/rest/api/keyvault/)|[Referens](/azure/templates/microsoft.keyvault/vaults)<br>[Snabbstart](./vault-create-template.md)|[Referens](/dotnet/api/microsoft.azure.management.keyvault)|[Referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referens](/java/api/com.microsoft.azure.management.keyvault)|[Referens](/javascript/api/@azure/arm-keyvault)|

Se [klient bibliotek](client-libraries.md) för installations paket och käll kod.

Mer information om Key Vault hanterings plan finns i [Key Vault hanterings plan](./secure-your-key-vault.md#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Autentisera till Key Vault i kod

Key Vault använder Azure AD-autentisering som kräver att Azure AD säkerhets objekt beviljar åtkomst. Ett säkerhets objekt i Azure AD kan vara en användare, ett program tjänst objekt, en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)eller en grupp av alla typer av säkerhets objekt.

### <a name="authentication-best-practices"></a>Metod tips för autentisering

Vi rekommenderar att du använder hanterad identitet för program som distribueras till Azure. Om du använder Azure-tjänster, som inte stöder hanterad identitet eller om program distribueras lokalt, är [tjänstens huvud namn med ett certifikat](../../active-directory/develop/howto-create-service-principal-portal.md) ett möjligt alternativ. I det scenariot ska certifikatet lagras i Key Vault och roteras ofta. Tjänstens huvud namn med hemlighet kan användas för utvecklings-och testnings miljöer, och lokalt eller i Cloud Shell med hjälp av användarens huvud namn rekommenderas.

Rekommenderade säkerhets objekt per miljö:
- **Produktions miljö**:
  - Hanterad identitet eller tjänstens huvud namn med ett certifikat
- **Test-och utvecklings miljöer**:
  - Hanterad identitet, tjänstens huvud namn med certifikat-eller tjänstens huvud namn med hemlighet
- **Lokal utveckling**:
  - Användarens huvud namn eller tjänstens huvud namn med hemlighet

Scenarier med ovan autentisering stöds av **klient biblioteket för Azure Identity** och integreras med Key Vault SDK: er. Azure Identity Library kan användas i olika miljöer och plattformar utan att ändra koden. Azure-identiteten kan också automatiskt hämta autentiseringstoken från inloggad till Azure User med Azure CLI, Visual Studio, Visual Studio Code och andra. 

Mer information om Azure Identity client-Libarary finns i:

**Klient bibliotek för Azure Identity**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[JavaScript-skript för Azure Identity SDK](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Bibliotek för app-autentisering](/dotnet/api/overview/azure/service-to-service-authentication) som rekommenderas för Key Vault .NET SDK version 3, som för närvarande är depracated. Följ [AppAuthentication till Azure. vägledning för identitets migrering](/dotnet/api/overview/azure/app-auth-migration) för att migrera till Key Vault .NET SDK version 4.

Självstudier om hur du autentiserar till Key Vault i program finns i:
- [Autentisera till Key Vault i program som finns i en virtuell dator i .NET](./tutorial-net-virtual-machine.md)
- [Autentisera till Key Vault i program som finns på en virtuell dator i python](./tutorial-python-virtual-machine.md)
- [Autentisera till Key Vault med App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Hantera nycklar, certifikat och hemligheter

Åtkomst till nycklar, hemligheter och certifikat styrs av data planet. Åtkomst kontroll för data plan kan göras med hjälp av lokala valv åtkomst principer eller Azure RBAC (för hands version).

**Nycklars-API: er och SDK: er**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/key)<br>[Snabbstart](../keys/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault/)<br>[Snabbstart](../keys/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#key-operations)|[Referens](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Snabbstart](../keys/quick-create-template.md)|[Referens](/dotnet/api/azure.security.keyvault.keys)<br>[Snabbstart](../keys/quick-create-net.md)|[Referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Snabbstart](../keys/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Snabbstart](../keys/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-keys/)<br>[Snabbstart](../keys/quick-create-node.md)|

**API: er och SDK: er för certifikat**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/certificate)<br>[Snabbstart](../certificates/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault)<br>[Snabbstart](../certificates/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#certificate-operations)|Ej tillämpligt|[Referens](/dotnet/api/azure.security.keyvault.certificates)<br>[Snabbstart](../certificates/quick-create-net.md)|[Referens](/python/api/overview/azure/keyvault-certificates-readme)<br>[Snabbstart](../certificates/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Snabbstart](../certificates/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-certificates/)<br>[Snabbstart](../certificates/quick-create-node.md)|

**Hemligheter och SDK: er**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/secret)<br>[Snabbstart](../secrets/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault/)<br>[Snabbstart](../secrets/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#secret-operations)|[Referens](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Snabbstart](../secrets/quick-create-template.md)|[Referens](/dotnet/api/azure.security.keyvault.secrets)<br>[Snabbstart](../secrets/quick-create-net.md)|[Referens](/python/api/overview/azure/keyvault-secrets-readme)<br>[Snabbstart](../secrets/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Snabbstart](../secrets/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-secrets/)<br>[Snabbstart](../secrets/quick-create-node.md)|

Se [klient bibliotek](client-libraries.md) för installations paket och käll kod.

Mer information om Key Vault data Plans säkerhet finns [Key Vault data plan och åtkomst principer](./secure-your-key-vault.md#data-plane-and-access-policies) och [Key Vault data plan och Azure RBAC (för hands version)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Kodexempel

Fullständiga exempel som använder Key Vault med dina program finns i:

- [Azure Key Vault kod exempel](https://azure.microsoft.com/resources/samples/?service=key-vault) – kod exempel för Azure Key Vault. 

## <a name="how-tos"></a>Instruktioner

Följande artiklar och scenarier innehåller en detaljerad vägledning för att arbeta med Azure Key Vault:

- [Åtkomst till Key Vault bakom brand väggen](access-behind-firewall.md) – för att få åtkomst till ett nyckel valv måste klient programmet för nyckel valv kunna komma åt flera slut punkter för olika funktioner.
- Hur du distribuerar certifikat till virtuella datorer från Key Vault- [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) -ett moln program som körs i en virtuell dator på Azure behöver ett certifikat. Hur får du det här certifikatet till den virtuella datorn idag?
- [Distribuera Azure Web App-certifikat via Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Tilldela en åtkomst princip ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  -[Portal](assign-access-policy-portal.md)). 
- [Använda Key Vault mjuk borttagning med CLI](./key-vault-recovery.md) guidar dig genom användningen och livs cykeln för ett nyckel valv och olika Key Vault-objekt med mjuk borttagning aktiverat.
- [Så här skickar du säkra värden (t. ex. lösen ord) under distributionen](../../azure-resource-manager/templates/key-vault-parameter.md) – när du behöver skicka ett säkert värde (till exempel ett lösen ord) som en parameter under distributionen kan du lagra värdet som en hemlighet i en Azure Key Vault och referera till värdet i andra Resource Manager-mallar.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

De här artiklarna är till för andra scenarier och tjänster som använder eller integrerar med Key Vault.

- [Kryptering i vilo](../../security/fundamentals/encryption-atrest.md) läge tillåter kodning (kryptering) av data när den är beständig. Data krypterings nycklar krypteras ofta med en nyckel krypterings nyckel i Azure Key Vault för att ytterligare begränsa åtkomsten.
- Med [Azure information Protection](/azure/information-protection/plan-implement-tenant-key) kan du hantera din egen klient nyckel. I stället för att Microsoft hanterar din klient nyckel (standard) kan du till exempel hantera din egen klient nyckel för att följa särskilda regler som gäller för din organisation. Att hantera sin egen klientnyckel kallas också för att ta med sin egen nyckel eller BYOK.
- [Azure Private Link service](private-link-service.md) ger dig åtkomst till Azure-tjänster (till exempel Azure Key Vault, Azure Storage och Azure Cosmos dB) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.
- Key Vault integration med [Event Grid](../../event-grid/event-schema-key-vault.md)  gör det möjligt för användare att meddelas när statusen för en hemlighet som lagras i Key Vault har ändrats. Du kan distribuera en ny version av hemligheter till program eller rotera nära utgångs hemligheter för att förhindra avbrott.
- Du kan skydda dina [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) -hemligheter från oönskad åtkomst i Key Vault.
- [Använd hemlighet som lagrats i Key Vault i DataBricks för att ansluta till Azure Storage](./integrate-databricks-blob-storage.md)
- Konfigurera och kör Azure Key Vault providern för [hemligheter Store CSI-drivrutinen](./key-vault-integrate-kubernetes.md) på Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault översikter och begrepp

- [Key Vault beteende för mjuk borttagning](soft-delete-overview.md) beskriver en funktion som gör det möjligt att återställa borttagna objekt, om borttagningen var oavsiktligt eller avsiktligt.
- [Key Vault klient begränsningen](overview-throttling.md) orienterar dig till de grundläggande begreppen för begränsning och erbjuder en metod för din app.
- [Key Vault säkerhets världar](overview-security-worlds.md) beskriver relationerna mellan regioner och säkerhets områden.

## <a name="social"></a>Socialt

- [Key Vault blogg](/archive/blogs/kv/)
- [Key Vault forum](https://aka.ms/kvforum)