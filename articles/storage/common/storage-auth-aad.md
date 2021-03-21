---
title: Ge åtkomst till blobbar och köer med hjälp av Active Directory
titleSuffix: Azure Storage
description: Ge åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory (Azure AD). Tilldela Azure-roller för åtkomst behörighet. Få åtkomst till data med ett Azure AD-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94635406"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Ge åtkomst till blobbar och köer med hjälp av Azure Active Directory

Azure Storage stöder användning av Azure Active Directory (Azure AD) för att auktorisera begär anden till blob-och Queue-lagring. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare, grupp eller ett program tjänst objekt. Säkerhets objekt autentiseras av Azure AD för att returnera en OAuth 2,0-token. Token kan sedan användas för att auktorisera en begäran mot BLOB-eller Queue-lagring.

Att auktorisera begär Anden mot Azure Storage med Azure AD ger överlägsen säkerhet och enkel användning över autentisering med delad nyckel. Microsoft rekommenderar att du använder Azure AD-auktorisering med dina blob-och Queue-program när det är möjligt för att minimera potentiella säkerhets risker i delade nycklar.

Auktorisering med Azure AD är tillgängligt för alla generella och Blob Storage-konton i alla offentliga regioner och nationella moln. Endast lagrings konton som skapats med Azure Resource Manager distributions modell har stöd för Azure AD-auktorisering.

Blob Storage har dessutom stöd för att skapa signaturer för delad åtkomst (SAS) som är signerade med autentiseringsuppgifter för Azure AD. Mer information finns i [ge begränsad åtkomst till data med signaturer för delad åtkomst](storage-sas-overview.md).

Azure Files stöder auktorisering med AD (för hands version) eller Azure AD DS (GA) över SMB för domänanslutna virtuella datorer. Information om hur du använder AD (för hands version) eller Azure AD DS (GA) över SMB för Azure Files finns i [översikt Azure Files över stöd för identitetsbaserade autentiseringar för SMB-åtkomst](../files/storage-files-active-directory-overview.md).

Auktorisering med Azure AD stöds inte för Azure Table Storage. Använd delad nyckel för att auktorisera begär anden till Table Storage.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Översikt över Azure AD för blobbar och köer

När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till en BLOB-eller Queue-resurs, måste begäran auktoriseras, såvida det inte är en blob som är tillgänglig för anonym åtkomst. Med Azure AD är åtkomst till en resurs en två stegs process. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Därefter skickas token som en del av en begäran till Bloben eller Kötjänst och används av tjänsten för att ge åtkomst till den angivna resursen.

Steget autentisering kräver att ett program begär en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs från en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Functions app, kan den använda en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att få åtkomst till blobbar eller köer. Information om hur du auktoriserar begär Anden som görs av en hanterad identitet till Azure-blobben eller Kötjänst finns i [bevilja åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).

Auktoriserings steget kräver att en eller flera Azure-roller tilldelas säkerhets objekt. Azure Storage ger Azure-roller som omfattar vanliga uppsättningar med behörigheter för blob-och Queue-data. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om hur du tilldelar Azure-roller för Azure Storage finns i [Hantera åtkomst behörigheter till lagrings data med Azure RBAC](./storage-auth-aad-rbac-portal.md).

Interna program och webb program som gör förfrågningar till Azure-blobben eller Kötjänst kan också ge åtkomst till Azure AD. Information om hur du begär en åtkomsttoken och använder den för att auktorisera förfrågningar om BLOB-eller Queue-data finns i [bevilja åtkomst till Azure Storage med Azure AD från ett Azure Storage-program](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomst rättigheter

Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att få åtkomst till blob-och Queue-data. Du kan också definiera anpassade roller för åtkomst till blob-och Queue-data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, lagrings kontot eller en enskild behållare eller kö. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Inbyggda Azure-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Information om hur du tilldelar en inbyggd Azure-roll till ett säkerhets objekt finns i någon av följande artiklar:

- [Använda Azure-portalen för tilldelning av en Azure-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-portal.md)
- [Använd Azure CLI för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-cli.md)
- [Använd Azure PowerShell-modulen för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-powershell.md)

Mer information om hur inbyggda roller definieras för Azure Storage finns i [förstå roll definitioner](../../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Åtkomst behörigheter för data åtgärder

Mer information om de behörigheter som krävs för att anropa vissa BLOB-eller Kötjänst-åtgärder finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Resursomfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Få åtkomst till data med ett Azure AD-konto

Åtkomst till BLOB-eller Queue-data via Azure Portal, PowerShell eller Azure CLI kan godkännas antingen med hjälp av användarens Azure AD-konto eller genom att använda konto åtkomst nycklar (autentisering med delad nyckel).

### <a name="data-access-from-the-azure-portal"></a>Data åtkomst från Azure Portal

Azure Portal kan använda antingen ditt Azure AD-konto eller konto åtkomst nycklar för att få åtkomst till blob-och Queue-data i ett Azure Storage-konto. Vilket autentiseringsschema som Azure Portal använder beror på vilka Azure-roller som har tilldelats dig.

När du försöker komma åt BLOB-eller Queue-data kontrollerar Azure Portal först om du har tilldelats en Azure-roll med **Microsoft. Storage/storageAccounts/listnycklar/Action**. Om du har tilldelats en roll med den här åtgärden använder Azure Portal konto nyckeln för att komma åt blob-och Queue-data via autentisering med delad nyckel. Om du inte har tilldelats en roll med den här åtgärden försöker Azure Portal komma åt data med ditt Azure AD-konto.

För att få åtkomst till BLOB-eller Queue-data från Azure Portal med ditt Azure AD-konto måste du ha behörighet att komma åt blob-och Queue-data och du måste också ha behörighet att navigera genom lagrings konto resurserna i Azure Portal. De inbyggda rollerna i Azure Storage ger åtkomst till blob- och köresurser, men ger inte behörighet till lagringskontoresurser. Det betyder att åtkomst till portalen även kräver tilldelningen av en Azure Resource Manager-roll, t.ex. rollen [Läsare](../../role-based-access-control/built-in-roles.md#reader), på lagringskontonivå eller högre. Rollen **läsare** ger mest begränsade behörigheter, men en annan Azure Resource Manager roll som beviljar åtkomst till lagrings kontots hanterings resurser är också acceptabel. Mer information om hur du tilldelar behörigheter till användare för data åtkomst i Azure Portal med ett Azure AD-konto finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-portal.md).

Azure Portal anger vilket Authorization Scheme som används när du navigerar till en behållare eller kö. Mer information om data åtkomst i portalen finns i [Välj hur du vill ge åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md) och [välja hur du vill ge åtkomst till köa data i Azure Portal](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Data åtkomst från PowerShell eller Azure CLI

Azure CLI och PowerShell-stöd för att logga in med autentiseringsuppgifter för Azure AD. När du har loggat in körs sessionen under dessa autentiseringsuppgifter. Läs mer i [köra Azure CLI-eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data](../blobs/authorize-data-operations-powershell.md).

## <a name="next-steps"></a>Nästa steg

- [Ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
- [Auktorisera med Azure Active Directory från ett program för åtkomst till blobbar och köer](storage-auth-aad-app.md)
- [Azure Storage stöd för Azure Active Directory baserad åtkomst kontroll allmänt tillgänglig](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)