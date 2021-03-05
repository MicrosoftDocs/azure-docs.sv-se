---
title: Använd Azure AD Domain Services för att ge åtkomst till fildata över SMB
description: Lär dig hur du aktiverar identitets baserad autentisering över SMB (Server Message Block) för Azure Files via Azure Active Directory Domain Services. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 6d8402f5e8a1a9af1f84ff6beff7324d1675e441
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214413"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Aktivera Azure Active Directory Domain Services autentisering på Azure Files

[Azure Files](storage-files-introduction.md)   stöder identitets-baserad autentisering över Server Message Block (SMB) via två typer av domän tjänster: lokala Active Directory Domain Services (AD DS) och Azure Active Directory Domain Services (Azure AD DS). Vi rekommenderar starkt att du läser [avsnittet hur det fungerar](./storage-files-active-directory-overview.md#how-it-works) för att välja rätt domän tjänst för autentisering. Installations programmet skiljer sig åt beroende på vilken domän tjänst du väljer. Den här artikeln fokuserar på att aktivera och konfigurera Azure AD DS för autentisering med Azure-filresurser.

Om du är nybörjare på Azure-filresurser rekommenderar vi att du läser vår [planerings guide](storage-files-planning.md) innan du läser följande serie artiklar.

> [!NOTE]
> Azure Files stöder endast Kerberos-autentisering med Azure AD DS med RC4-HMAC. AES Kerberos-kryptering stöds inte ännu.
> Azure Files stöder autentisering för Azure AD DS med fullständig synkronisering med Azure AD. Om du har aktiverat begränsad synkronisering i Azure AD DS som bara synkroniserar en begränsad uppsättning identiteter från Azure AD, stöds inte autentisering och auktorisering.

## <a name="prerequisites"></a>Förutsättningar

Innan du aktiverar Azure AD över SMB för Azure-filresurser måste du kontrol lera att du har slutfört följande krav:

1.  **Välj eller skapa en Azure AD-klient.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering över SMB. Klienten och den fil resurs som du vill komma åt måste vara associerad med samma prenumeration.

    Om du vill skapa en ny Azure AD-klient kan du [lägga till en Azure AD-klient och en Azure AD-prenumeration](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Om du har en befintlig Azure AD-klient men vill skapa en ny klient för användning med Azure-filresurser, se [skapa en Azure Active Directory klient](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Aktivera Azure AD Domain Services på Azure AD-klienten.**

    Om du vill ha stöd för autentisering med Azure AD-autentiseringsuppgifter måste du aktivera Azure AD Domain Services för din Azure AD-klient. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer steg-för-steg-vägledningen för att [aktivera Azure Active Directory Domain Services att använda Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Det tar vanligt vis ungefär 15 minuter för en Azure AD DS-distribution att slutföras. Kontrol lera att hälso statusen för Azure AD **DS visar att den är** aktive rad och att synkronisering av lösen ord är aktiverat, innan du fortsätter till nästa steg.

1.  **Domän – Anslut till en virtuell Azure-dator med Azure AD DS.**

    För att få åtkomst till en fil resurs med hjälp av autentiseringsuppgifter för Azure AD från en virtuell dator måste den virtuella datorn vara domänansluten till Azure AD DS. Mer information om hur du ansluter till en virtuell dator finns i [ansluta en virtuell Windows Server-dator till en hanterad domän](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS-autentisering över SMB med Azure-filresurser stöds bara på virtuella Azure-datorer som körs på OS-versioner över Windows 7 eller Windows Server 2008 R2.

1.  **Välj eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig fil resurs som är associerad med samma prenumeration som din Azure AD-klient. Information om hur du skapar en ny fil resurs finns [i skapa en fil resurs i Azure Files](storage-how-to-create-file-share.md).
    För optimala prestanda rekommenderar vi att fil resursen finns i samma region som den virtuella dator som du planerar att komma åt resursen från.

1.  **Verifiera Azure Files anslutningen genom att montera Azure-filresurser med hjälp av lagrings konto nyckeln.**

    Om du vill kontrol lera att den virtuella datorn och fil resursen är korrekt konfigurerade kan du försöka att montera fil resursen med din lagrings konto nyckel. Mer information finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files-autentisering med Azure AD DS är tillgänglig i [alla regioner i Azures offentliga-, gov-och Kina](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Översikt över arbets flödet

Innan du aktiverar Azure AD DS-autentisering över SMB för Azure-filresurser måste du kontrol lera att dina Azure AD-och Azure Storage-miljöer är korrekt konfigurerade. Vi rekommenderar att du går igenom [kraven](#prerequisites) för att se till att du har slutfört alla nödvändiga steg.

Gör sedan följande för att ge åtkomst till Azure Files resurser med Azure AD-autentiseringsuppgifter:

1. Aktivera Azure AD DS-autentisering över SMB för ditt lagrings konto för att registrera lagrings kontot med den associerade Azure AD DS-distributionen.
2. Tilldela åtkomst behörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänstens huvud namn).
3. Konfigurera NTFS-behörigheter över SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domänansluten virtuell dator.

Följande diagram illustrerar arbets flödet från slut punkt till slut punkt för att aktivera Azure AD DS-autentisering över SMB för Azure Files.

![Diagram över Azure AD över SMB för Azure Files arbets flöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivera Azure AD DS-autentisering för ditt konto

Om du vill aktivera Azure AD DS-autentisering över SMB för Azure Files kan du ange en egenskap för lagrings konton med hjälp av Azure Portal, Azure PowerShell eller Azure CLI. Om du anger den här egenskapen implicit "domän anslutning" är lagrings kontot med den associerade Azure AD DS-distributionen. Azure AD DS-autentisering över SMB aktive ras sedan för alla nya och befintliga fil resurser i lagrings kontot.

Tänk på att du bara kan aktivera Azure AD DS-autentisering över SMB när du har distribuerat Azure AD DS till Azure AD-klienten. Mer information finns i [krav](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill aktivera Azure AD DS-autentisering över SMB med [Azure Portal](https://portal.azure.com):

1. I Azure Portal går du till ditt befintliga lagrings konto eller så [skapar du ett lagrings konto](../common/storage-account-create.md).
1. I avsnittet **Inställningar** väljer du **konfiguration**.
1. Under **identitets-baserad åtkomst för fil resurser** växlar du över växlingen för **Azure Active Directory Domain Service (AAD DS)** till **aktive rad**.
1. Välj **Spara**.

Följande bild visar hur du aktiverar Azure AD DS-autentisering över SMB för ditt lagrings konto.

![Aktivera Azure AD DS-autentisering över SMB i Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera Azure AD DS-autentisering över SMB med Azure PowerShell installerar du den senaste AZ-modulen (2,4 eller senare) eller modulen AZ. Storage (1,5 eller senare). Mer information om hur du installerar PowerShell finns i [installera Azure PowerShell på Windows med PowerShellGet](/powershell/azure/install-Az-ps).

Om du vill skapa ett nytt lagrings konto anropar du [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)och anger sedan parametern **EnableAzureActiveDirectoryDomainServicesForFile** till **True**. I följande exempel ska du komma ihåg att ersätta plats hållarnas värden med dina egna värden. (Om du använde föregående Preview-modul är parametern för att aktivera funktionen **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Om du vill aktivera den här funktionen på befintliga lagrings konton använder du följande kommando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill aktivera Azure AD-autentisering över SMB med Azure CLI installerar du den senaste CLI-versionen (version 2.0.70 eller senare). Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill skapa ett nytt lagrings konto anropar du [AZ lagrings konto Create](/cli/azure/storage/account#az-storage-account-create)och anger `--enable-files-aadds` egenskapen till **True**. I följande exempel ska du komma ihåg att ersätta plats hållarnas värden med dina egna värden. (Om du använde den tidigare förhands granskningen är parametern för funktions aktivering **fil-AAD**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Om du vill aktivera den här funktionen på befintliga lagrings konton använder du följande kommando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nu har du aktiverat Azure AD DS-autentisering över SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en Azure AD-identitet. Om du vill ge fler användare åtkomst till din fil resurs följer du anvisningarna i avsnittet [tilldela åtkomst behörigheter](#assign-access-permissions-to-an-identity) för att använda en identitet och [Konfigurera NTFS-behörigheter över SMB-avsnitt](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och hur du använder Azure AD över SMB finns i följande resurser:

- [Översikt över stödet för identitetsbaserad autentisering för SMB-åtkomst i Azure Files](storage-files-active-directory-overview.md)
- [Vanliga frågor och svar](storage-files-faq.md)
