---
title: Windows Virtual Desktop konfigurera fil resurs MSIX app Attach Preview – Azure
description: Så här konfigurerar du en fil resurs för MSIX-appen Anslut för Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930794"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Konfigurera en fil resurs för MSIX app attach (för hands version)

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Alla MSIX-avbildningar måste lagras på en nätverks resurs som kan nås av användare i en adresspool med Läs behörighet.

MSIX app attach (för hands version) saknar beroenden för typen av lagrings infrastruktur resurs som används av fil resursen. Överväganden för MSIX app Attach-resursen är samma som för en FSLogix-resurs. Mer information om lagrings kraven finns i [lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Prestandakrav

MSIX-appens kopplings storleks gränser för ditt system beror på vilken lagrings typ du använder för att lagra VHD-eller VHDx-filerna samt storleks begränsningarna för VHD-, VHSD-eller CIM-filer och fil systemet.

Följande tabell innehåller ett exempel på hur många resurser en enda 1 GB MSIX-avbildning med en MSIX-app i den måste för varje virtuell dator:

| Resurs             | Krav |
|----------------------|--------------|
| Stabilt läge IOPs    | 1 IOPs       |
| Datorns start inloggning | 10 IOPs      |
| Svarstid              | 400 MS       |

Kraven kan variera mycket beroende på hur många MSIX-paketerade program som lagras i MSIX-avbildningen. För större MSIX-avbildningar måste du allokera mer bandbredd.

### <a name="storage-recommendations"></a>Lagringsrekommendationer

Azure erbjuder flera lagrings alternativ som kan användas för att ansluta MISX-appen. Vi rekommenderar att du använder Azure Files eller Azure NetApp Files eftersom de här alternativen ger det bästa värdet mellan kostnads-och hanterings kostnader. Artikel [lagrings alternativen för FSLogix profil behållare i Windows Virtual Desktop](store-fslogix-profile.md) jämför de olika hanterings lösningarna för hanterade lagrings lösningar med Azure i Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Optimera MSIX-appens kopplings prestanda

Här följer några andra saker som vi rekommenderar att du optimerar MSIX-appens kopplings prestanda:

- Lagrings lösningen som du använder för MSIX-appens koppling bör finnas på samma plats i data centret som sessionens värdar.
- Undvik Flask halsar i prestanda genom att undanta följande VHD-, VHDX-och CIM-filer från virus genomsökningar:
   
    - <MSIXAppAttachFileShare \> \* . DISKEN
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net- \\ resurs \* \* . DISKEN
    - \\\\storageaccount.file.core.windows.net- \\ resurs \* \* . VHDX
    - <MSIXAppAttachFileShare>. OBJEKTHANTERAREN
    - \\\\storageaccount.file.core.windows.net- \\ resurs \* \* . OBJEKTHANTERAREN

- Separera lagrings infrastrukturen för MSIX-appen koppla från FSLogix profil behållare.
- Alla virtuella dator system konton och användar konton måste ha Läs behörighet för att få åtkomst till fil resursen.
- Eventuella katastrof återställnings planer för virtuella Windows-datorer måste inkludera replikering av MSIX-appen bifoga fil resurs på den sekundära platsen för redundans. Mer information om haveri beredskap finns i [Konfigurera en plan för affärs kontinuitet och katastrof återställning](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Så här konfigurerar du fil resursen

Installations processen för MSIX-appen bifoga fil resurs är i stort sett densamma som [installations processen för FSLogix profil fil resurser](create-host-pools-user-profile.md). Du måste dock tilldela användare olika behörigheter. MSIX app Attach kräver skrivskyddade behörigheter för åtkomst till fil resursen.

Om du lagrar dina MSIX-program i Azure Files måste du, för värdarna för-sessionen, tilldela alla VM-VM: ar för rollbaserad åtkomst kontroll (RBAC) och fil resursens nya teknik fil system (NTFS) för resursen.

| Azure-objekt                      | Nödvändig roll                                     | Roll funktion                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Sessions värd (VM-datornamn)| Storage-fildata för SMB-resursdeltagare          | Läsa och köra, läsa, Visa mappinnehåll  |
| Administratörer på fil resurs              | Storage-fildata för upphöjd SMB-resursdeltagare | Fullständig behörighet                                  |
| Användare på fil resurs               | Storage-fildata för SMB-resursdeltagare          | Läsa och köra, läsa, Visa mappinnehåll  |

Så här tilldelar du VM-behörigheter för VM för lagrings kontot och fil resursen:

1. Skapa en säkerhets grupp för Active Directory Domain Services (AD DS).

2. Lägg till dator kontona för alla VM-värdar som medlemmar i gruppen.

3. Synkronisera AD DS-gruppen med Azure Active Directory (Azure AD).

4. Skapa ett lagringskonto.

5. Skapa en fil resurs under lagrings kontot genom att följa anvisningarna i [skapa en Azure-filresurs](../storage/files/storage-how-to-create-file-share.md#create-file-share).

6. Anslut lagrings kontot till AD DS genom att följa anvisningarna i [del ett: Aktivera AD DS-autentisering för dina Azure-filresurser](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Tilldela den synkroniserade AD DS-gruppen till Azure AD och tilldela lagrings kontot lagrings filen data SMB Share Contributor roll.

8. Montera fil resursen på alla värdbaserade sessioner genom att följa anvisningarna i [del två: tilldela behörigheter på resurs nivå till en identitet](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Bevilja NTFS-behörighet för fil resursen till AD DS-gruppen.

10. Konfigurera NTFS-behörigheter för användar kontona. Du behöver en operativ enhet (OU) från AD DS som kontona i den virtuella datorn tillhör.

När du har tilldelat identiteten till lagrings utrymmet följer du anvisningarna i artiklarna i [Nästa steg](#next-steps) för att ge andra nödvändiga behörigheter till den identitet som du har tilldelat till de virtuella datorerna.

Du måste också se till att de virtuella datorerna i din sessions värd har nya NTFS-behörigheter (Technology File System). Du måste ha en behållare för drift enheter som har sitt ursprung i Active Directory Domain Services (AD DS), och användarna måste vara medlemmar i den operativa enheten för att kunna använda dessa behörigheter.

## <a name="next-steps"></a>Nästa steg

Här är de andra saker du behöver göra när du har konfigurerat fil resursen:

- Lär dig hur du konfigurerar Azure Active Directory Domain Services (AD DS) i [skapa en profil behållare med Azure Files och AD DS](create-file-share.md).
- Lär dig hur du konfigurerar Azure Files och Azure AD DS på [skapa en profil behållare med Azure Files och Azure AD DS](create-profile-container-adds.md).
- Lär dig hur du konfigurerar Azure NetApp Files för MSIX-appen bifoga i [skapa en profil behållare med Azure NetApp Files och AD DS](create-fslogix-profile-container.md).
- Lär dig hur du använder en virtuell dator baserad fil resurs på [skapa en profil behållare för en adresspool med hjälp av en fil resurs](create-host-pools-user-profile.md).

När du är klar finns några andra resurser som du kan ha nytta av:

- Fråga våra Community-frågor om den här funktionen på [Windows-TechCommunity för virtuella datorer](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Ord lista för MSIX-appen](app-attach-glossary.md)
- [Vanliga frågor och svar om MSIX app](app-attach-faq.md)
