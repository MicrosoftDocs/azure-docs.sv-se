---
title: Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault
description: Lär dig hur tjänstslutpunkter för virtuellt Azure Key Vault gör att du kan begränsa åtkomsten till ett angivet virtuellt nätverk, inklusive användningsscenarier.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 8d3b88841f03b0c5bdb9b21ea66d9a67ba795546
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814254"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault

Med tjänstslutpunkter för virtuellt nätverk Azure Key Vault du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slutpunkterna kan du också begränsa åtkomsten till en lista över IPv4-adressintervall (Internet Protocol version 4). Alla användare som ansluter till ditt nyckelvalv utanför dessa källor nekas åtkomst.

Det finns ett viktigt undantag till den här begränsningen. Om en användare har valt att tillåta betrodda Microsoft-tjänster tillåts anslutningar från dessa tjänster genom brandväggen. Dessa tjänster omfattar till exempel Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager och Azure Backup. Sådana användare måste fortfarande presentera en giltig Azure Active Directory-token och måste ha behörigheter (konfigurerade som åtkomstprinciper) för att kunna utföra den begärda åtgärden. Mer information finns i [Tjänstslutpunkter för virtuellt nätverk.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault och virtuella](network-security.md) nätverk för att neka åtkomst till trafik från alla nätverk (inklusive Internettrafik) som standard. Du kan bevilja åtkomst till trafik från specifika virtuella Azure-nätverk och offentliga IP-adressintervall för Internet, så att du kan skapa en säker nätverksgräns för dina program.

> [!NOTE]
> Key Vault brandväggar och regler för virtuella nätverk gäller endast [för dataplanet](security-features.md#privileged-access) för Key Vault. Key Vault åtgärder på kontrollplanet (till exempel skapa, ta bort och ändra åtgärder, ställa in åtkomstprinciper, ange brandväggar och regler för virtuellt nätverk och distribution av hemligheter eller nycklar via ARM-mallar) påverkas inte av brandväggar och regler för virtuellt nätverk.

Här följer några exempel på hur du kan använda tjänstslutpunkter:

* Du använder Key Vault för att lagra krypteringsnycklar, programhemligheter och certifikat, och du vill blockera åtkomsten till ditt nyckelvalv från det offentliga Internet.
* Du vill låsa åtkomsten till ditt nyckelvalv så att endast ditt program, eller en kort lista över angivna värdar, kan ansluta till ditt nyckelvalv.
* Du har ett program som körs i ditt virtuella Azure-nätverk och det här virtuella nätverket är låst för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat eller använda kryptografiska nycklar.

## <a name="trusted-services"></a>Betrodda tjänster

Här är en lista över betrodda tjänster som har behörighet att komma åt ett nyckelvalv om **alternativet Tillåt betrodda** tjänster är aktiverat.

|Betrodd tjänst|Användningsscenarier som stöds|
| --- | --- |
|Azure Virtual Machines distributionstjänst|[Distribuera certifikat till virtuella datorer från kund-hanterade Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager för malldistribution|[Skicka säkra värden under distributionen](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption volymkrypteringstjänst|Tillåt åtkomst till BitLocker-nyckel (virtuell Windows-dator) eller DM-lösenfras (virtuell Linux-dator) och nyckelkrypteringsnyckel under distributionen av virtuella datorer. Detta aktiverar [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Tillåt säkerhetskopiering och återställning av relevanta nycklar och hemligheter under Azure Virtual Machines säkerhetskopiering med hjälp av [Azure Backup](../../backup/backup-overview.md).|
|Exchange Online & SharePoint Online|Tillåt åtkomst till kundnyckel för Azure Storage-tjänstkryptering med [kundnyckel](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Tillåt åtkomst till klientnyckel för [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Distribuera Azure Web App Certificate via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Storage|[Kryptering för lagringstjänst använda kund hanterade nycklar i Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) med en kund hanterad nyckel.|
|Azure Synapse Analytics|[Kryptering av data med kund hanterade nycklar i Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Snabb, enkel och samarbetsbaserad Apache Spark analystjänst](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Distribuera certifikat för Custom Domain från Key Vault med hjälp av MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Hämta autentiseringsuppgifter för datalager i Key Vault från Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Tillåt åtkomst till ett nyckelvalv för scenariot med kund hanterade nycklar](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Tillåt åtkomst till ett nyckelvalv för scenariot med kund hanterade nycklar](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Använda kund hanterade nycklar i Azure Key Vault import-/exporttjänst](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Registerkryptering med kund hanterade nycklar](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Använda Key Vault certifikat för HTTPS-aktiverade lyssnare](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Du måste konfigurera relevanta Key Vault åtkomstprinciper så att motsvarande tjänster får åtkomst till Key Vault.

## <a name="next-steps"></a>Nästa steg

- Stegvisa instruktioner finns i Konfigurera Azure Key Vault [och virtuella nätverk](network-security.md)
- se Azure Key Vault [säkerhetsöversikt](security-features.md)
