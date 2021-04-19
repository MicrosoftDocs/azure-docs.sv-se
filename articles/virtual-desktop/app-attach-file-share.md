---
title: Windows Virtual Desktop konfigurera MSIX-appen för filresurs – Azure
description: Konfigurera en filresurs för MSIX-app attach för Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d4ebee02d85d10d5db8ec2de0bb1be334770dc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717665"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Konfigurera en filresurs för BIFOGA MSIX-app

Alla MSIX-avbildningar måste lagras på en nätverksresurs som kan nås av användare i en värdpool med skrivskyddad behörighet.

MSIX-apptillägget har inga beroenden för vilken typ av lagringsresurs som filresursen använder. Övervägandena för msix-appens bifogande resurs är samma som för en FSLogix-resurs. Mer information om lagringskrav finns i [Lagringsalternativ för FSLogix-profilcontainrar i Windows Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Prestandakrav

Storleksbegränsningarna för msix-apptillägg för ditt system beror på vilken lagringstyp du använder för att lagra VHD- eller VHDx-filerna, samt storleksbegränsningarna för VHD-, VHSD- eller CIM-filer och filsystemet.

I följande tabell visas ett exempel på hur många resurser en enskild MSIX-avbildning på 1 GB med en MSIX-app i den kräver för varje virtuell dator:

| Resurs             | Krav |
|----------------------|--------------|
| IOP:er för stabilt tillstånd    | 1 IOPs       |
| Inloggning för datorstart | 10 IOPs      |
| Svarstid              | 400 ms       |

Kraven kan variera mycket beroende på hur många MSIX-paketerade program som lagras i MSIX-avbildningen. För större MSIX-avbildningar måste du allokera mer bandbredd.

### <a name="storage-recommendations"></a>Lagringsrekommendationer

Azure erbjuder flera lagringsalternativ som kan användas för att koppla MISX-appar. Vi rekommenderar att Azure Files eller Azure NetApp Files eftersom dessa alternativ erbjuder det bästa värdet mellan kostnader och hanteringskostnader. Artikeln [Lagringsalternativ för FSLogix-profilcontainrar i Windows Virtual Desktop](store-fslogix-profile.md) jämför de olika hanterade lagringslösningarna som Azure erbjuder i kontexten för Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Optimera prestanda vid anslutning av MSIX-appar

Här är några andra saker som vi rekommenderar att du gör för att optimera msix-appens anslutning av prestanda:

- Lagringslösningen som du använder för att ansluta MSIX-appen ska finnas på samma datacenterplats som sessionsvärdarna.
- Undvik prestandaflaskhalsar genom att undanta följande VHD-, VHDX- och CIM-filer från virusgenomsökningar:
   
    - <MSIXAppAttachFileShare \> \* . Vhd
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net dela \\ \* \* . Vhd
    - \\\\storageaccount.file.core.windows.net dela \\ \* \* . VHDX
    - <MSIXAppAttachFileShare>. Cim
    - \\\\storageaccount.file.core.windows.net dela \\ \* \* . Cim

- Separera lagrings fabric för MSIX-app-anslutning från FSLogix-profilcontainrar.
- Alla systemkonton och användarkonton för virtuella datorer måste ha skrivskyddade behörigheter för att komma åt filresursen.
- Alla haveriberedskapsplaner för Windows Virtual Desktop måste inkludera replikering av MSIX-appens bifogande filresurs på din sekundära redundansplats. Mer information om haveriberedskap finns i [Konfigurera en plan för affärskontinuhet och haveriberedskap.](disaster-recovery.md)

## <a name="how-to-set-up-the-file-share"></a>Konfigurera filresursen

Installationsprocessen för att bifoga filresursen i MSIX-appen är i stort sett densamma som installationsprocessen för [FSLogix-profilfilresurser.](create-host-pools-user-profile.md) Du måste dock tilldela användare olika behörigheter. Bifoga MSIX-app kräver skrivskyddade behörigheter för att få åtkomst till filresursen.

Om du lagrar dina MSIX-program i Azure Files måste du för dina sessionsvärdar tilldela alla virtuella datorer för sessionsvärd både rollbaserad åtkomstkontroll (RBAC) för lagringskontot och filresursen NTFS-behörigheter (New Technology File System) på resursen.

| Azure-objekt                      | Obligatorisk roll                                     | Rollfunktion                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Sessionsvärd (datorobjekt för virtuella datorer)| Storage-fildata för SMB-resursdeltagare          | Läsa och köra, läsa, lista mappinnehåll  |
| Administratörer på filresurs              | Storage-fildata för upphöjd SMB-resursdeltagare | Fullständig behörighet                                  |
| Användare på filresurs               | Storage-fildata för SMB-resursdeltagare          | Läsa och köra, läsa, lista mappinnehåll  |

Så här tilldelar du behörigheter för sessionsvärds-VM:ar för lagringskontot och filresursen:

1. Skapa en Active Directory Domain Services (AD DS)-säkerhetsgrupp.

2. Lägg till datorkonton för alla sessionsvärd-VM:ar som medlemmar i gruppen.

3. Synkronisera AD DS-gruppen till Azure Active Directory (Azure AD).

4. Skapa ett lagringskonto.

5. Skapa en filresurs under lagringskontot genom att följa anvisningarna i [Skapa en Azure-filresurs](../storage/files/storage-how-to-create-file-share.md#create-a-file-share).

6. Anslut lagringskontot till AD DS genom att följa anvisningarna i [Del ett: Aktivera AD DS-autentisering för dina Azure-filresurser](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Tilldela den synkroniserade AD DS-gruppen till Azure AD och tilldela lagringskontot rollen Storage File Data SMB-resursdeltagare.

8. Montera filresursen till valfri sessionsvärd genom att följa anvisningarna i [del två: tilldela behörigheter på resursnivå till en identitet](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Bevilja NTFS-behörigheter på filresursen till AD DS-gruppen.

10. Konfigurera NTFS-behörigheter för användarkontona. Du behöver en driftsenhet (OU) som kommer från den AD DS som kontona på den virtuella datorn tillhör.

När du har tilldelat identiteten till lagringen följer du anvisningarna i artiklarna i [Nästa](#next-steps) steg för att bevilja andra nödvändiga behörigheter till den identitet som du har tilldelat till de virtuella datorerna.

Du måste också se till att dina virtuella sessionsvärdar har NTFS-behörigheter (New Technology File System). Du måste ha en container för driftsenhet som kommer från Active Directory Domain Services (AD DS) och användarna måste vara medlemmar i den driftenheten för att kunna använda dessa behörigheter.

## <a name="next-steps"></a>Nästa steg

Här är några andra saker du behöver göra när du har skapat filresursen:

- Lär dig att konfigurera Azure Active Directory Domain Services (AD DS) i Skapa en [profilcontainer med Azure Files och AD DS](create-file-share.md).
- Lär dig att konfigurera Azure Files och Azure AD DS skapa en [profilcontainer med Azure Files och Azure AD DS](create-profile-container-adds.md).
- Lär dig hur du Azure NetApp Files för MSIX-app bifoga i Skapa en [profilcontainer med Azure NetApp Files och AD DS](create-fslogix-profile-container.md).
- Lär dig hur du använder en virtuell datorbaserad filresurs i [Skapa en profilcontainer för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md).

När du är klar kan följande resurser vara till hjälp:

- Ställ våra communityfrågor om den här funktionen på [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Du kan också lämna feedback för Windows Virtual Desktop på Windows Virtual Desktop [feedbackhubben](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Bifoga MSIX-appordlistor](app-attach-glossary.md)
- [Vanliga frågor och svar om att bifoga MSIX-appar](app-attach-faq.md)
