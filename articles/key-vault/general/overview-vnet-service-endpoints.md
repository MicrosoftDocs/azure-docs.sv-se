---
title: Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault
description: Lär dig hur tjänst slut punkter i virtuella nätverk för Azure Key Vault ger dig möjlighet att begränsa åtkomsten till ett angivet virtuellt nätverk, inklusive användnings scenarier.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 148f67fb87072a8ffd25f0fe1dfdb444be052173
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771625"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault

Med tjänst slut punkter för virtuella nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slut punkterna kan du också begränsa åtkomsten till en lista över IPv4-adress intervall (Internet Protocol version 4). Alla användare som ansluter till nyckel valvet utanför dessa källor nekas åtkomst.

Det finns ett viktigt undantag för den här begränsningen. Om en användare har valt att tillåta betrodda Microsoft-tjänster, kan anslutningar från dessa tjänster göra genom brand väggen. Dessa tjänster omfattar till exempel Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager och Azure Backup. Sådana användare måste ändå presentera en giltig Azure Active Directory-token och måste ha behörigheter (konfigurerade som åtkomst principer) för att utföra den begärda åtgärden. Mer information finns i [tjänst slut punkter för virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault brand väggar och virtuella nätverk](network-security.md) för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) som standard. Du kan bevilja åtkomst till trafik från specifika virtuella Azure-nätverk och offentliga Internet IP-adressintervall, så att du kan bygga en säker nätverks gränser för dina program.

> [!NOTE]
> Key Vault brand väggar och regler för virtuella nätverk gäller endast för Key Vaultens [data plan](secure-your-key-vault.md#data-plane-access-control) . Key Vault kontroll Plans åtgärder (till exempel skapa, ta bort och ändra åtgärder, ange åtkomst principer, ange brand väggar och regler för virtuella nätverk och distribution av hemligheter eller nycklar med ARM-mallar) påverkas inte av brand väggar och virtuella nätverks regler.

Här följer några exempel på hur du kan använda tjänst slut punkter:

* Du använder Key Vault för att lagra krypterings nycklar, program hemligheter och certifikat, och du vill blockera åtkomst till ditt nyckel valv från det offentliga Internet.
* Du vill låsa åtkomsten till ditt nyckel valv så att endast ditt program eller en kort lista med angivna värdar kan ansluta till ditt nyckel valv.
* Du har ett program som körs i det virtuella Azure-nätverket och det här virtuella nätverket är låst för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat, eller använda kryptografiska nycklar.

## <a name="trusted-services"></a>Betrodda tjänster

Här är en lista över betrodda tjänster som har behörighet att komma åt ett nyckel valv om alternativet **Tillåt betrodda tjänster** är aktiverat.

|Betrodd tjänst|Användnings scenarier som stöds|
| --- | --- |
|Azure Virtual Machines Deployment service|[Distribuera certifikat till virtuella datorer från kund hanterade Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager mall distributions tjänst|[Skicka säkra värden under distributionen](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption volym krypterings tjänst|Tillåt åtkomst till BitLocker Key (Windows VM) eller DM-lösen fras (Linux VM) och nyckel krypterings nyckel under distributionen av virtuella datorer. Detta gör att [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Tillåt säkerhets kopiering och återställning av relevanta nycklar och hemligheter under Azure Virtual Machines säkerhets kopiering med [Azure Backup](../../backup/backup-overview.md).|
|Exchange Online & SharePoint Online|Tillåt åtkomst till kund nyckel för Azure Storage tjänst kryptering med [kund nyckel](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Tillåt åtkomst till klient nyckeln för [Azure information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Distribuera Azure Web App-certifikat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&preserve-view=true&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) med en kundhanterad nyckel.|
|Azure Synapse Analytics|[Kryptering av data med Kundhanterade nycklar i Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Snabb, enkel och gemensam Apache Spark-baserad analys tjänst](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Distribuera certifikat för anpassad domän från Key Vault med MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Hämta autentiseringsuppgifter för data lager i Key Vault från Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Tillåt åtkomst till ett nyckel valv för kund hanterade nyckel scenarier](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Tillåt åtkomst till ett nyckel valv för kund hanterade nyckel scenarier](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Använda Kundhanterade nycklar i Azure Key Vault för import/export-tjänsten](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Register kryptering med Kundhanterade nycklar](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Använda Key Vault certifikat för HTTPS-aktiverade lyssnare](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Du måste ställa in relevanta Key Vault åtkomst principer så att motsvarande tjänster får åtkomst till Key Vault.

## <a name="next-steps"></a>Nästa steg

- Stegvisa instruktioner finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md)
- Se [Azure Key Vault säkerhets översikt](security-overview.md)
