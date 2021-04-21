---
title: Utvecklarguide för Azure Key Vault
description: Utvecklare kan använda Azure Key Vault för att hantera kryptografiska nycklar Microsoft Azure miljön.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4f9523594c07209d530a143713061be6d0467af8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753388"
---
# <a name="azure-key-vault-developers-guide"></a>Utvecklarguide för Azure Key Vault

Key Vault kan du komma åt känslig information från dina program på ett säkert sätt:

- Nycklar, hemligheter och certifikat skyddas utan att du behöver skriva koden själv och du kan enkelt använda dem från dina program.
- Du låter kunder äga och hantera sina egna nycklar, hemligheter och certifikat så att du kan koncentrera dig på att tillhandahålla grundläggande programvarufunktioner. På så sätt kommer dina program inte att äga ansvaret eller potentiella ansvar för dina kunders klientnycklar, hemligheter och certifikat.
- Programmet kan använda nycklar för signering och kryptering men samtidigt hålla nyckelhanteringen extern från ditt program. Mer information om nycklar finns i [Om nycklar](../keys/about-keys.md)
- Du kan hantera autentiseringsuppgifter som lösenord, åtkomstnycklar och SAS-token genom att lagra dem i Key Vault som hemligheter, se [Om hemligheter](../secrets/about-secrets.md)
- Hantera certifikat. Mer information finns i [Om certifikat](../certificates/about-certificates.md)

Mer allmän information om Azure Key Vault finns i [Vad är Key Vault](overview.md).

## <a name="public-previews"></a>Offentliga förhandsversioner

Med jämna mellanrum släpper vi en offentlig förhandsversion av en ny Key Vault funktion. Prova funktionerna i den offentliga förhandsversionen och berätta vad du tycker via azurekeyvault@microsoft.com vår e-postadress för feedback.

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera nyckelvalv

Key Vault hantering, på liknande sätt som andra Azure-tjänster, görs via Azure Resource Manager tjänst. Azure Resource Manager är Azures tjänst för distribution och hantering. Den ger dig ett hanteringslager där du kan skapa, uppdatera och ta bort resurser i ditt Azure-konto. Mer information finns i [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Åtkomsten till hanteringslagret styrs av [rollbaserad åtkomstkontroll i Azure.](../../role-based-access-control/overview.md) I Key Vault kan du med hanteringslagret, även kallat hanterings- eller kontrollplan, skapa och hantera nyckelvalv och dess attribut, inklusive åtkomstprinciper, men inte nycklar, hemligheter och certifikat, som hanteras på dataplanet. Du kan använda en fördefinierad `Key Vault Contributor` roll för att bevilja hanteringsåtkomst till Key Vault.     

**API:er och -API:er för nyckelvalvshantering:**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault)<br>[Snabbstart](quick-create-cli.md)|[Referens](/powershell/module/az.keyvault)<br>[Snabbstart](quick-create-powershell.md)|[Referens](/rest/api/keyvault/)|[Referens](/azure/templates/microsoft.keyvault/vaults)<br>[Snabbstart](./vault-create-template.md)|[Referens](/dotnet/api/microsoft.azure.management.keyvault)|[Referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referens](/java/api/com.microsoft.azure.management.keyvault)|[Referens](/javascript/api/@azure/arm-keyvault)|

Se [Klientbibliotek](client-libraries.md) för installationspaket och källkod.

Mer information om Key Vault finns i Key Vault [hanteringsplanet](security-overview.md)

## <a name="authenticate-to-key-vault-in-code"></a>Autentisera till Key Vault i kod

Key Vault använder Azure AD-autentisering som kräver Azure AD-säkerhetsobjekt för att bevilja åtkomst. Ett Azure AD-säkerhetsobjekt kan vara en användare, ett programtjänsthuvudnamn, en hanterad identitet för [Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)eller en grupp av någon typ av säkerhetsobjekt.

### <a name="authentication-best-practices"></a>Metodtips för autentisering

Vi rekommenderar att du använder hanterad identitet för program som distribueras till Azure. Om du använder Azure-tjänster, som inte stöder hanterad identitet eller om program distribueras lokalt, är tjänstens huvudnamn [med](../../active-directory/develop/howto-create-service-principal-portal.md) ett certifikat ett möjligt alternativ. I det scenariot ska certifikatet lagras i Key Vault roteras ofta. Tjänstens huvudnamn med hemlighet kan användas för utvecklings- och testningsmiljöer, och lokalt eller i Cloud Shell användning av användarens huvudnamn rekommenderas.

Rekommenderade säkerhetsobjekt per miljö:
- **Produktionsmiljö:**
  - Hanterad identitet eller tjänstens huvudnamn med ett certifikat
- **Test- och utvecklingsmiljöer:**
  - Hanterad identitet, tjänstens huvudnamn med certifikat eller tjänstens huvudnamn med hemlighet
- **Lokal utveckling:**
  - Användarens huvudnamn eller tjänstens huvudnamn med hemlighet

Ovanstående autentiseringsscenarier stöds av **Azure Identity-klientbiblioteket** och integreras med Key Vault-ID:er. Azure Identity-biblioteket kan användas i olika miljöer och plattformar utan att ändra koden. Azure Identity skulle också automatiskt hämta autentiseringstoken från den inloggade Azure-användaren med Azure CLI, Visual Studio, Visual Studio Code med mera. 

Mer information om klientbibliotek för Azure Identity finns i:

**Azure Identity-klientbibliotek**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [App Authentication-biblioteket](/dotnet/api/overview/azure/service-to-service-authentication) som rekommenderas för Key Vault .NET SDK version 3, som för närvarande är depracated . Följ [appauthentication till Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration) för att migrera till Key Vault .NET SDK version 4.

Självstudier om hur du autentiserar för Key Vault i program finns i:
- [Autentisera till Key Vault i program som finns på en virtuell dator i .NET](./tutorial-net-virtual-machine.md)
- [Autentisera till Key Vault i ett program som körs på en virtuell dator i Python](./tutorial-python-virtual-machine.md)
- [Autentisera till Key Vault med App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Hantera nycklar, certifikat och hemligheter

Åtkomst till nycklar, hemligheter och certifikat styrs av dataplanet. Åtkomstkontroll på dataplanet kan göras med hjälp av åtkomstprinciper för lokalt valv eller Azure RBAC.

**API:er och -API:er för nycklar**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/key)<br>[Snabbstart](../keys/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault/)<br>[Snabbstart](../keys/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#key-operations)|[Referens](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Snabbstart](../keys/quick-create-template.md)|[Referens](/dotnet/api/azure.security.keyvault.keys)<br>[Snabbstart](../keys/quick-create-net.md)|[Referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Snabbstart](../keys/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Snabbstart](../keys/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-keys/)<br>[Snabbstart](../keys/quick-create-node.md)|

**CERTIFIKAT-API:er och -API:er**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/certificate)<br>[Snabbstart](../certificates/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault)<br>[Snabbstart](../certificates/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#certificate-operations)|Ej tillämpligt|[Referens](/dotnet/api/azure.security.keyvault.certificates)<br>[Snabbstart](../certificates/quick-create-net.md)|[Referens](/python/api/overview/azure/keyvault-certificates-readme)<br>[Snabbstart](../certificates/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Snabbstart](../certificates/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-certificates/)<br>[Snabbstart](../certificates/quick-create-node.md)|

**API:er och -API:er för hemligheter**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referens](/cli/azure/keyvault/secret)<br>[Snabbstart](../secrets/quick-create-cli.md)|[Referens](/powershell/module/az.keyvault/)<br>[Snabbstart](../secrets/quick-create-powershell.md)|[Referens](/rest/api/keyvault/#secret-operations)|[Referens](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Snabbstart](../secrets/quick-create-template.md)|[Referens](/dotnet/api/azure.security.keyvault.secrets)<br>[Snabbstart](../secrets/quick-create-net.md)|[Referens](/python/api/overview/azure/keyvault-secrets-readme)<br>[Snabbstart](../secrets/quick-create-python.md)|[Referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Snabbstart](../secrets/quick-create-java.md)|[Referens](/javascript/api/@azure/keyvault-secrets/)<br>[Snabbstart](../secrets/quick-create-node.md)|

Se [Klientbibliotek](client-libraries.md) för installationspaket och källkod.

Mer information om hur Key Vault dataplanssäkerhet finns [i Key Vault Säkerhetsöversikt](security-overview.md).

### <a name="code-examples"></a>Kodexempel

Fullständiga exempel som använder Key Vault med dina program finns i:

- [Azure Key Vault kodexempel](https://azure.microsoft.com/resources/samples/?service=key-vault) – Kodexempel för Azure Key Vault. 

## <a name="how-tos"></a>Instruktioner

Följande artiklar och scenarier innehåller uppgiftsspecifik vägledning för att arbeta med Azure Key Vault:

- [Åtkomst Key Vault bakom brandväggen](access-behind-firewall.md) – För att få åtkomst till ett nyckelvalv måste ditt nyckelvalvsklientprogram kunna komma åt flera slutpunkter för olika funktioner.
- Hur du distribuerar certifikat till virtuella datorer från Key Vault [– Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) – Ett molnprogram som körs på en virtuell dator på Azure behöver ett certifikat. Hur hämtar du det här certifikatet till den här virtuella datorn idag?
- [Distribuera Azure Web App Certificate via Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Tilldela en åtkomstprincip[(CLI](assign-access-policy-cli.md)  |  [PowerShell-portalen).](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md) 
- [Hur du använder Key Vault mjuk](./key-vault-recovery.md) borttagning med CLI vägleder dig genom användningen och livscykeln för ett nyckelvalv och olika nyckelvalvsobjekt med mjuk borttagning aktiverat.
- Så här skickar du säkra värden (till exempel [lösenord)](../../azure-resource-manager/templates/key-vault-parameter.md) under distributionen – När du behöver skicka ett säkert värde (till exempel ett lösenord) som en parameter under distributionen kan du lagra det värdet som en hemlighet i en Azure Key Vault och referera till värdet i andra Resource Manager-mallar.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

De här artiklarna handlar om andra scenarier och tjänster som använder eller integrerar med Key Vault.

- [Kryptering i vila](../../security/fundamentals/encryption-atrest.md) tillåter kodning (kryptering) av data när de bevaras. Datakrypteringsnycklar krypteras ofta med en nyckelkrypteringsnyckel i Azure Key Vault att ytterligare begränsa åtkomsten.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kan du chef din egen klientnyckel. I stället för att Microsoft hanterar din klientnyckel (standard) kan du till exempel hantera din egen klientnyckel för att följa specifika regler som gäller för din organisation. Att hantera sin egen klientnyckel kallas också för att ta med sin egen nyckel eller BYOK.
- [med Azure Private Link Service](private-link-service.md) kan du komma åt Azure-tjänster (till exempel Azure Key Vault, Azure Storage och Azure Cosmos DB) och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk.
- Key Vault integrering med [Event Grid](../../event-grid/event-schema-key-vault.md)  kan användarna meddelas när statusen för en hemlighet som lagras i nyckelvalvet har ändrats. Du kan distribuera nya versioner av hemligheter till program eller rotera hemligheter som snart upphör att gälla för att förhindra avbrott.
- Du kan skydda dina [Azure Devops-hemligheter](/azure/devops/pipelines/release/azure-key-vault) från oönskad åtkomst i Key Vault.
- [Använda en hemlighet som lagras Key Vault i DataBricks för att ansluta till Azure Storage](./integrate-databricks-blob-storage.md)
- Konfigurera och kör Azure Key Vault för [CSI-drivrutinen Secrets Store](./key-vault-integrate-kubernetes.md) på Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault översikter och begrepp

- [Key Vault beteende för mjuk borttagning](soft-delete-overview.md) beskriver en funktion som tillåter återställning av borttagna objekt, oavsett om borttagningen var oavsiktlig eller avsiktlig.
- [Key Vault klientbegränsning ger](overview-throttling.md) en orientering om de grundläggande begränsningsbegreppen och erbjuder en metod för din app.
- [Key Vault säkerhetsvärldar](overview-security-worlds.md) beskriver relationerna mellan regioner och säkerhetsområden.

## <a name="social"></a>Socialt

- [Key Vault blogg](/archive/blogs/kv/)
- [Key Vault Forum](https://aka.ms/kvforum)