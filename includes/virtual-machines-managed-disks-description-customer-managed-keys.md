---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ba50def51bcea4f477bea5cecbe5b1ed0409b01a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98792437"
---
Du kan välja att hantera kryptering på nivån för varje hanterad disk med dina egna nycklar. Kryptering på Server sidan för Managed disks med Kundhanterade nycklar ger en integrerad upplevelse med Azure Key Vault. Du kan antingen importera [dina RSA-nycklar](../articles/key-vault/keys/hsm-protected-keys.md) till Key Vault eller generera nya RSA-nycklar i Azure Key Vault. 

Azure Managed disks hanterar kryptering och dekryptering på ett helt transparent sätt med hjälp av [Kuvert kryptering](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Den krypterar data med hjälp av en [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Lagrings tjänsten genererar data krypterings nycklar och krypterar dem med Kundhanterade nycklar med hjälp av RSA-kryptering. Med kuvert krypteringen kan du rotera (ändra) dina nycklar regelbundet enligt dina efterlevnadsprinciper utan att de virtuella datorerna påverkas. När du roterar dina nycklar krypterar lagrings tjänsten om data krypterings nycklarna med de nya Kundhanterade nycklarna. 

#### <a name="full-control-of-your-keys"></a>Fullständig kontroll över dina nycklar

Du måste bevilja åtkomst till hanterade diskar i Key Vault för att kunna använda dina nycklar för att kryptera och dekryptera DEK. Detta gör att du får fullständig kontroll över dina data och nycklar. Du kan när som helst inaktivera dina nycklar eller återkalla åtkomsten till hanterade diskar. Du kan också granska krypterings nyckel användningen med Azure Key Vault övervakning så att endast hanterade diskar eller andra betrodda Azure-tjänster får åtkomst till dina nycklar.

För Premium SSD, standard-SSD och standard-HDD: när du inaktiverar eller tar bort din nyckel stängs alla virtuella datorer med diskar som använder den nyckeln automatiskt. Efter detta kommer de virtuella datorerna inte att vara användbara om inte nyckeln är aktive rad igen eller om du tilldelar en ny nyckel.    

För Ultra disks: när du inaktiverar eller tar bort en nyckel stängs inte alla virtuella datorer med Ultra disks med nyckeln automatiskt. När du har avallokerat och startat om de virtuella datorerna slutar diskarna att använda nyckeln och de virtuella datorerna kommer inte att vara online igen. Om du vill ta de virtuella datorerna online igen måste du tilldela en ny nyckel eller aktivera den befintliga nyckeln.    

Följande diagram visar hur hanterade diskar använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Arbets flöde för hanterade diskar och Kundhanterade nycklar. En administratör skapar en Azure Key Vault, skapar en disk krypterings uppsättning och konfigurerar disk krypterings uppsättningen. Uppsättningen är kopplad till en virtuell dator, vilket gör att disken kan använda Azure AD för att autentisera":::

I följande lista beskrivs diagrammet i detalj:

1. En Azure Key Vault administratör skapar nyckel valvs resurser.
1. Key Vault-administratören importerar antingen sina RSA-nycklar för att Key Vault eller generera nya RSA-nycklar i Key Vault.
1. Administratören skapar en instans av en resurs för disk krypterings uppsättning och anger ett Azure Key Vault-ID och en nyckel-URL. Disk krypterings uppsättning är en ny resurs som introducerats för att förenkla nyckel hanteringen för hanterade diskar. 
1. När en disk krypterings uppsättning skapas skapas en [systemtilldelad hanterad identitet](../articles/active-directory/managed-identities-azure-resources/overview.md) i Azure Active Directory (AD) och associeras med disk krypterings uppsättningen. 
1. Azure Key Vault-administratören ger sedan den hanterade identiteten behörighet att utföra åtgärder i nyckel valvet.
1. En VM-användare skapar diskar genom att associera dem med disk krypterings uppsättningen. Den virtuella dator användaren kan också aktivera kryptering på Server sidan med Kundhanterade nycklar för befintliga resurser genom att associera dem med disk krypterings uppsättningen. 
1. Managed disks använder den hanterade identiteten för att skicka begär anden till Azure Key Vault.
1. Vid läsning eller skrivning av data skickar Managed disks begär anden till Azure Key Vault att kryptera (omslutning) och dekryptera (unwrap) data krypterings nyckeln för att utföra kryptering och dekryptering av data. 

Information om hur du återkallar åtkomst till Kundhanterade nycklar finns i [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.
