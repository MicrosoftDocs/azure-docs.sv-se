---
title: Använd Azure AD Domain Services för att auktorisera åtkomst till fildata via SMB
description: Lär dig hur du aktiverar identitetsbaserad autentisering över Server Message Block (SMB) för Azure Files via Azure Active Directory Domain Services. Dina domän-ansluten virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: e82ac4e1fdb8e5f88c9d83924da50b1ff5c659cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777931"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Aktivera Azure Active Directory Domain Services autentisering på Azure Files

[Azure Files](storage-files-introduction.md)   stöder identitetsbaserad autentisering över Server Message Block (SMB) via två typer av Domain Services: lokal Active Directory Domain Services (AD DS) och Azure Active Directory Domain Services (Azure AD DS). Vi rekommenderar starkt att du går igenom [avsnittet Så här fungerar det](./storage-files-active-directory-overview.md#how-it-works) för att välja rätt domäntjänst för autentisering. Konfigurationen varierar beroende på vilken domäntjänst du väljer. Den här artikeln fokuserar på att aktivera och konfigurera Azure AD DS för autentisering med Azure-filresurser.

Om du är nybörjare på Azure-filresurser rekommenderar vi att du läser [vår planeringsguide](storage-files-planning.md) innan du läser följande artikelserie.

> [!NOTE]
> Azure Files stöder Kerberos-autentisering Azure AD DS endast med RC4-HMAC. AES Kerberos-kryptering stöds inte ännu.
> Azure Files har stöd för autentisering Azure AD DS med fullständig synkronisering med Azure AD. Om du har aktiverat omfångssynkronisering i Azure AD DS som endast synkroniserar en begränsad uppsättning identiteter från Azure AD, stöds inte autentisering och auktorisering.

## <a name="prerequisites"></a>Förutsättningar

Innan du aktiverar Azure AD via SMB för Azure-filresurser kontrollerar du att du har slutfört följande krav:

1.  **Välj eller skapa en Azure AD-klientorganisation.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering via SMB. Klientorganisationen och filresursen som du vill komma åt måste associeras med samma prenumeration.

    Om du vill skapa en ny Azure AD-klientorganisation kan du [lägga till en Azure AD-klientorganisation och en Azure AD-prenumeration.](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Om du har en befintlig Azure AD-klient men vill skapa en ny klient för användning med Azure-filresurser kan du gå till [Skapa en Azure Active Directory klientorganisation.](/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Aktivera Azure AD Domain Services azure AD-klientorganisationen.**

    För att stödja autentisering med Azure AD-autentiseringsuppgifter måste du aktivera Azure AD Domain Services för din Azure AD-klientorganisation. Om du inte är administratör för Azure AD-klientorganisationen kontaktar du administratören och följer den stegvisa vägledningen för att aktivera Azure Active Directory Domain Services med hjälp [av Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Det tar vanligtvis cirka 15 minuter för en Azure AD DS distribution att slutföras. Kontrollera att hälsostatusen för Azure AD DS visar **Kör**, med synkronisering av lösenordshashar aktiverad, innan du fortsätter till nästa steg.

1.  **Domän-ansluta en virtuell Azure-dator med Azure AD DS.**

    Om du vill komma åt en filresurs med hjälp av Azure AD-autentiseringsuppgifter från en virtuell dator måste den virtuella datorn vara domän-ansluten till Azure AD DS. Mer information om hur du ansluter en virtuell dator till en domän finns i [Ansluta en virtuell Windows Server-dator till en hanterad domän.](../../active-directory-domain-services/join-windows-vm.md)

    > [!NOTE]
    > Azure AD DS-autentisering via SMB med Azure-filresurser stöds endast på virtuella Azure-datorer som körs på os-versioner över Windows 7 eller Windows Server 2008 R2.

1.  **Välj eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig filresurs som är associerad med samma prenumeration som din Azure AD-klientorganisation. Information om hur du skapar en ny filresurs finns [i Skapa en filresurs i Azure Files](storage-how-to-create-file-share.md).
    För optimala prestanda rekommenderar vi att filresursen finns i samma region som den virtuella dator som du planerar att komma åt resursen från.

1.  **Kontrollera Azure Files genom att montera Azure-filresurser med hjälp av din lagringskontonyckel.**

    Om du vill kontrollera att den virtuella datorn och filresursen är korrekt konfigurerade kan du prova att montera filresursen med hjälp av lagringskontonyckeln. Mer information finns i Montera [en Azure-filresurs och få åtkomst till resursen i Windows.](storage-how-to-use-files-windows.md)

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files autentisering med Azure AD DS är tillgängligt i [alla Azure Public-, Gov- och China-regioner.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview-of-the-workflow"></a>Översikt över arbetsflödet

Innan du aktiverar Azure AD DS-autentisering via SMB för Azure-filresurser kontrollerar du att dina Azure AD- Azure Storage-miljöer är korrekt konfigurerade. Vi rekommenderar att du går igenom [förutsättningarna för](#prerequisites) att se till att du har slutfört alla nödvändiga steg.

Gör sedan följande för att bevilja åtkomst till Azure Files med Azure AD-autentiseringsuppgifter:

1. Aktivera Azure AD DS-autentisering via SMB för ditt lagringskonto för att registrera lagringskontot med den associerade Azure AD DS distributionen.
2. Tilldela åtkomstbehörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänstens huvudnamn).
3. Konfigurera NTFS-behörigheter via SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domän-ansluten virtuell dator.

Följande diagram illustrerar arbetsflödet från slutet till slut för att Azure AD DS autentisering över SMB för Azure Files.

![Diagram som visar Azure AD över SMB för Azure Files arbetsflöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivera Azure AD DS autentisering för ditt konto

Om du Azure AD DS autentisering via SMB för Azure Files kan du ange en egenskap för lagringskonton med hjälp av Azure Portal, Azure PowerShell eller Azure CLI. Om du anger den här egenskapen implicit "domänkopplingar" till lagringskontot med den Azure AD DS distributionen. Azure AD DS autentisering via SMB aktiveras sedan för alla nya och befintliga filresurser i lagringskontot.

Tänk på att du kan aktivera Azure AD DS-autentisering via SMB först när du har distribuerat Azure AD DS till din Azure AD-klientorganisation. Mer information finns i [förutsättningarna](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du Azure AD DS autentisering via SMB med [Azure Portal](https://portal.azure.com)gör du följande:

1. I Azure Portal du till ditt befintliga lagringskonto eller skapar [ett lagringskonto](../common/storage-account-create.md).
1. I avsnittet **Inställningar** väljer du **Konfiguration.**
1. Under **Identitetsbaserad åtkomst för filresurser** växlar du växlingsknappen **för Azure Active Directory Domain Service (AAD DS)** till **Aktiverad**.
1. Välj **Spara**.

Följande bild visar hur du aktiverar Azure AD DS-autentisering via SMB för ditt lagringskonto.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Skärmbild av konfigurationsbladet i ditt lagringskonto och azure active directory doman services är aktiverat." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du Azure AD DS autentisering via SMB med Azure PowerShell installerar du den senaste Az-modulen (2.4 eller nyare) eller Az.Storage-modulen (1.5 eller nyare). Mer information om hur du installerar PowerShell finns i [Installera Azure PowerShell på Windows med PowerShellGet.](/powershell/azure/install-Az-ps)

Om du vill skapa ett nytt lagringskonto anropar du [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)och anger sedan parametern **EnableAzureActiveDirectoryDomainServicesForFile** till **true**. Kom ihåg att ersätta platshållarvärdena med dina egna värden i följande exempel. (Om du använde den tidigare förhandsversionsmodulen är parametern för att aktivera funktionen **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Om du vill aktivera den här funktionen på befintliga lagringskonton använder du följande kommando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill aktivera Azure AD-autentisering via SMB med Azure CLI installerar du den senaste CLI-versionen (version 2.0.70 eller senare). Mer information om hur du installerar Azure CLI finns [i Installera Azure CLI.](/cli/azure/install-azure-cli)

Om du vill skapa ett nytt lagringskonto [anropar du az storage account create](/cli/azure/storage/account#az_storage_account_create)och anger egenskapen till `--enable-files-aadds` **true**. Kom ihåg att ersätta platshållarvärdena med dina egna värden i följande exempel. (Om du använde den tidigare förhandsversionsmodulen är parametern för funktionsaktivering **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Om du vill aktivera den här funktionen på befintliga lagringskonton använder du följande kommando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Du har nu aktiverat Azure AD DS via SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en Azure AD-identitet. Om du vill ge ytterligare användare åtkomst till [](#assign-access-permissions-to-an-identity) filresursen följer du anvisningarna i avsnitten Tilldela åtkomstbehörigheter för att använda en identitet och [Konfigurera NTFS-behörigheter över SMB.](#configure-ntfs-permissions-over-smb)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och hur du använder Azure AD över SMB finns i följande resurser:

- [Översikt över stödet för identitetsbaserad autentisering för SMB-åtkomst i Azure Files](storage-files-active-directory-overview.md)
- [Vanliga frågor och svar](storage-files-faq.md)
