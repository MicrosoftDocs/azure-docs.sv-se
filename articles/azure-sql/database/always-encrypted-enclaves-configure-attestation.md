---
title: Konfigurera Azure-attestering för din logiska Azure SQL-Server
description: Konfigurera Azure-attestering för Always Encrypted med säker enclaves i Azure SQL Database.
keywords: kryptera data, SQL-kryptering, databas kryptering, känsliga data, Always Encrypted, säker enclaves, SGX, attestering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 664733f3d4c4e4bf17440db0323580c5d2c8c2ce
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555666"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Konfigurera Azure-attestering för din logiska Azure SQL-Server

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted med säker enclaves för Azure SQL Database finns för närvarande i **offentlig för hands version**.

[Microsoft Azure attestering](../../attestation/overview.md) är en lösning för att intyga betrodda körnings miljöer (tees), inklusive Intel Software Guard-tillägg (Intel SGX) enclaves. 

Om du vill använda Azure-attestering för att intyga Intel SGX-enclaves som används för [Always Encrypted med säker enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) i Azure SQL Database måste du:

1. Skapa en [attesterings leverantör](../../attestation/basic-concepts.md#attestation-provider) och konfigurera den med den rekommenderade policyn för attestering.

2. Ge din Azure SQL-Server åtkomst till din attesterings leverantör.

> [!NOTE]
> Att konfigurera attestering är ansvaret för attesterings administratören. Se [roller och ansvars områden när du konfigurerar SGX-enclaves och attestering](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Krav

Den logiska Azure SQL-servern och attesterings leverantören måste tillhöra samma Azure Active Directory klient. Interaktion mellan klienter stöds inte. 

Den logiska Azure SQL-servern måste ha en tilldelad Azure AD-identitet. Som attesterings administratör måste du hämta Azure AD-identiteten för servern från Azure SQL Database administratören för den servern. Du kommer att använda identiteten för att ge servern åtkomst till attesteringsservern. 

Instruktioner för hur du skapar en server med en identitet eller tilldelar en identitet till en befintlig server med PowerShell och Azure CLI finns i [tilldela en Azure AD-identitet till servern](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Skapa och konfigurera en attesterings leverantör

En [attesterings leverantör](../../attestation/basic-concepts.md#attestation-provider) är en resurs i Azure-attestering som utvärderar [attesterings begär Anden](../../attestation/basic-concepts.md#attestation-request) mot [attesterings principer](../../attestation/basic-concepts.md#attestation-request) och utfärdar [token](../../attestation/basic-concepts.md#attestation-token)för attestering. 

Policyer för attestering anges med hjälp av [grammatikkontrollen i anspråks regeln](../../attestation/claim-rule-grammar.md).

Microsoft rekommenderar följande princip för att intyga Intel SGX-enclaves som används för Always Encrypted i Azure SQL Database:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

Principen ovan verifierar:

- Enklaven i Azure SQL Database stöder inte fel sökning (vilket skulle minska den skydds nivå som enklaven tillhandahåller).
- Produkt-ID: t för biblioteket i enklaven är det produkt-ID som tilldelats Always Encrypted med säker enclaves (4639).
- Bibliotekets versions-ID (SVN) är större än 0.
- Biblioteket i enklaven har signerats med Microsofts signerings nyckel (värdet för x-MS-SGX-mrsigner-anspråket är hashen för signerings nyckeln).

> [!IMPORTANT]
> En attesterings leverantör skapas med standard principen för Intel SGX-enclaves, som inte validerar koden som körs inuti enklaven. Microsoft rekommenderar att du ställer in den rekommenderade principen ovan och inte använder standard principen för Always Encrypted med säker enclaves.

Instruktioner för hur du skapar en attesterings leverantör och konfigurerar med en attesterings princip med hjälp av:

- [Snabb start: Konfigurera Azure-attestering med Azure Portal](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > När du konfigurerar din attesterings princip med Azure Portal anger du typ av attestering till `SGX-IntelSDK` .
- [Snabb start: Konfigurera Azure-attestering med Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > När du konfigurerar din attesterings princip med Azure PowerShell anger du `Tee` parametern till `SgxEnclave` .
- [Snabb start: Konfigurera Azure-attestering med Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > När du konfigurerar din attesterings princip med Azure CLI anger du `attestation-type` parametern till `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Fastställ attesterings-URL: en för din attesterings princip

När du har konfigurerat en princip för attestering måste du dela URL: en för attestering, referera till principen, administratörer av program som använder Always Encrypted med säker enclaves i Azure SQL Database. Program administratörer eller/och program användare måste konfigurera sina appar med URL: en för attestering, så att de kan köra instruktioner som använder säker enclaves.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Använd PowerShell för att fastställa URL: en för attestering

Använd följande skript för att fastställa din URL för attestering:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Använd Azure Portal för att fastställa URL: en för attestering

1. Kopiera värdet för egenskapen attesterings-URI till Urklipp i fönstret Översikt för din attesterings leverantör. En attesterings-URI bör se ut så här: `https://MyAttestationProvider.us.attest.azure.net` .

2. Lägg till följande i deklarations-URI: en: `/attest/SgxEnclave` . 

Den resulterande attesterings-URL: en bör se ut så här: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Ge din Azure SQL-Server åtkomst till din hälsoattesterings leverantör

Under arbets flödet för attestering anropar den logiska Azure SQL-servern som innehåller databasen attesteringsservern för att skicka en begäran om attestering. För att den logiska Azure SQL-servern ska kunna skicka begäran om attestering måste servern ha behörighet för att utföra `Microsoft.Attestation/attestationProviders/attestation/read` åtgärden på attesteringsservern. Det rekommenderade sättet att bevilja behörigheten är för-administratören för attesteringsservern att tilldela Azure AD-identiteten för-servern till attesterings läsarens roll för attesteringsservern, eller resurs gruppen som innehåller den.

### <a name="use-azure-portal-to-assign-permission"></a>Använd Azure Portal för att tilldela behörighet

Om du vill tilldela identiteten för en Azure SQL-Server till rollen attesterings läsare för en attesterings leverantör följer du de allmänna anvisningarna i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md). När du är i fönstret **Lägg till roll tilldelning** :

1. I list rutan **roll** väljer du rollen **attesterings läsare** .
1. I fältet **Välj** anger du namnet på din Azure SQL-Server för att söka efter den.

Se skärm bilden nedan för ett exempel.

![roll tilldelning för attesterings läsare](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> För att en server ska visas i fönstret **Lägg till roll tilldelning** måste servern ha en tilldelad Azure AD-identitet – se [krav](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Använd PowerShell för att tilldela behörighet

1. Hitta din logiska Azure SQL-Server.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Tilldela servern rollen som attesterings läsare för den resurs grupp som innehåller din attesterings leverantör.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Mer information finns i [tilldela Azure-roller med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Nästa steg

- [Hantera nycklar för Always Encrypted med säkra enklaver](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Se även

- [Självstudie: komma igång med Always Encrypted med säker enclaves i Azure SQL Database](always-encrypted-enclaves-getting-started.md)
