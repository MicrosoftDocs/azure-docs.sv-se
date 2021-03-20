---
title: Windows Virtual Desktop FSLogix profil behållare filer – Azure
description: I den här artikeln beskrivs FSLogix profil behållare i Windows Virtual Desktop och Azure Files.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b977944f3a531365487b9655820e5d8f85e13d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225923"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profilcontainrar och Azure-filer

Tjänsten Windows Virtual Desktop rekommenderar FSLogix profil behållare som en användar profil lösning. FSLogix är utformat för att roama profiler i fjärrdatormiljöer, till exempel Windows Virtual Desktop. Det lagrar en fullständig användarprofil i en enskild container. Vid inloggningen är den här behållaren dynamiskt kopplad till dator miljön med inbyggd virtuell hård disk (VHD) och virtuell Hyper-V-hårddisk (VHDX) som stöds. Användarprofilen blir tillgänglig omedelbart och visas i systemet precis som en intern användarprofil. I den här artikeln beskrivs hur FSLogix profil behållare används med Azure Files-funktionen i det virtuella Windows-skrivbordet.

>[!NOTE]
>Om du letar efter jämförelse material om de olika lagrings alternativen för FSLogix Profile container på Azure, se [lagrings alternativ för FSLogix profil behållare](store-fslogix-profile.md).

## <a name="user-profiles"></a>Användarprofiler

En användar profil innehåller data element om en individ, inklusive konfigurations information som Skriv bords inställningar, beständiga nätverks anslutningar och program inställningar. Som standard skapar Windows en lokal användar profil som är nära integrerad med operativ systemet.

En fjärran sluten användar profil innehåller en partition mellan användar data och operativ systemet. Det gör att operativ systemet kan ersättas eller ändras utan att påverka användar data. I RDSH (Remote Desktop Session Host) och VDI (Virtual Desktop Infrastructure) kan operativ systemet ersättas av följande orsaker:

- En uppgradering av operativ systemet
- Ersätta en befintlig virtuell dator (VM)
- En användare som ingår i en mellanliggande RDSH-eller VDI-miljö (icke-beständig)

Microsoft-produkter samarbetar med flera tekniker för fjärran vändare, inklusive dessa tekniker:
- Centrala användar profiler (RUP)
- Användar profil diskar (UPD)
- Enterprise State roaming (ESR)

UPD och RUP är de mest använda teknikerna för användar profiler i RDSH-och VHD-miljöer (Virtual Hard Disk).

### <a name="challenges-with-previous-user-profile-technologies"></a>Utmaningar med tidigare användar profil tekniker

Befintliga och äldre Microsoft-lösningar för användar profiler följde med olika utmaningar. Ingen tidigare lösning hanterade alla användar profil behov som medföljer en RDSH-eller VDI-miljö. UPD kan till exempel inte hantera stora OST-filer och RUP har inte kvar moderna inställningar.

#### <a name="functionality"></a>Funktioner

I följande tabell visas fördelarna och begränsningarna för tidigare användar profil tekniker.

| Teknik | Moderna inställningar | Win32-inställningar | OS-inställningar | Användardata | Stöds på Server-SKU: n | Server dels lagring på Azure | Server dels lagring lokalt | Versions stöd | Efterföljande inloggnings tid |Kommentarer|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Användar profil diskar (UPD)** | Ja | Ja | Ja | Ja | Ja | Inga | Ja | Win 7 + | Ja | |
| **Central användar profil (RUP), underhålls läge** | Inga | Ja | Ja | Ja | Ja| Inga | Ja | Win 7 + | Inga | |
| **Enterprise State Roaming (ESR)** | Ja | Inga | Ja | Inga | Se kommentarer | Ja | Inga | Win 10 | Inga | Funktioner på Server-SKU men saknar stöd för användar gränssnitt |
| **User Experience Virtualization (UE-V)** | Ja | Ja | Ja | Inga | Ja | Inga | Ja | Win 7 + | Inga |  |
| **OneDrive-molnappar** | Inga | Inga | Inga | Ja | Se kommentarer | Se kommentarer  | Se kommentarer | Win 10-RS3 | Inga | Inte testat på Server-SKU. Backend-lagring på Azure är beroende av Sync-klienten. Backend-lokal kräver en sync-klient. |

#### <a name="performance"></a>Prestanda

UPD kräver [Lagringsdirigering (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) för att uppfylla prestanda kraven. UPD använder SMB-protokoll (Server Message Block). Profilen kopieras till den virtuella dator där användaren loggas.

#### <a name="cost"></a>Cost

Även om S2D-kluster uppnår nödvändiga prestanda, är kostnaden dyra för företags kunder, men särskilt dyra för små och medel stora företags (SMB)-kunder. För den här lösningen betalar företag för lagrings diskar, tillsammans med kostnaden för de virtuella datorer som använder diskarna för en resurs.

#### <a name="administrative-overhead"></a>Högre administrativa kostnader

S2D-kluster kräver ett operativ system som korrigeras, uppdateras och bevaras i säkert tillstånd. De här processerna och komplexiteten vid installation av S2D-haveri beredskap gör S2D endast möjligt för företag med en dedikerad IT-personal.

## <a name="fslogix-profile-containers"></a>FSLogix profil behållare

Den 19 november 2018 har [Microsoft förvärvat FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix hanterar flera utmaningar för profil behållare. Nyckel mellan dem är:

- **Prestanda:** [FSLogix profil behållare](/fslogix/configure-profile-container-tutorial/) är höga prestanda och löser prestanda problem som har historiskt blockerat cachelagrat Exchange-läge.
- **OneDrive:** Utan FSLogix profil behållare stöds inte OneDrive för företag i icke-permanent RDSH-eller VDI-miljöer. På [sidan ONEDRIVE VDI-support](/onedrive/sync-vdi-support) kan du se hur de interagerar. Mer information finns i [använda Sync-klienten på virtuella skriv bord](/deployoffice/rds-onedrive-business-vdi/).
- **Ytterligare mappar:** FSLogix ger möjlighet att utöka användar profiler för att inkludera ytterligare mappar.

Eftersom förvärvs tjänsten ersatte befintliga användar profil lösningar, t. ex. UPD, med FSLogix profil behållare.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files integrering med Azure Active Directory Domain Service

FSLogix profil behållares prestanda och funktioner utnyttjar molnet. Den 7 augusti 2019 Microsoft Azure filer som är allmänt tillgängliga för [Azure Files autentisering med Azure Active Directory Domain Service (AD DS)](../storage/files/storage-files-active-directory-overview.md). Genom att adressera både kostnads-och administrations kostnader är Azure Files med Azure AD DS-autentisering en Premium-lösning för användar profiler i Windows Virtual Desktop-tjänsten.

## <a name="best-practices-for-windows-virtual-desktop"></a>Metod tips för virtuella Windows-datorer

Virtuella Windows-datorer ger fullständig kontroll över storlek, typ och antal virtuella datorer som används av kunderna. Mer information finns i [Vad är Windows Virtual Desktop?](overview.md).

För att se till att din Windows Virtual Desktop-miljö följer bästa praxis:

- Azure Files lagrings kontot måste finnas i samma region som de virtuella datorerna i sessionen.
- Azure Files behörigheter ska matcha behörigheter som beskrivs i [behållare för krav – profiler](/fslogix/fslogix-storage-config-ht).
- Varje virtuell dator för poolen med värdar måste vara inbyggd av samma typ och storlek som den virtuella datorn baserat på samma huvud avbildning.
- Varje virtuell dator i poolen för värdar måste finnas i samma resurs grupp för att hantera, skala och uppdatera.
- För optimala prestanda bör lagrings lösningen och behållaren för FSLogix-profilen finnas på samma plats i data centret.
- Lagrings kontot som innehåller huvud avbildningen måste finnas i samma region och i den prenumeration där de virtuella datorerna har allokerats.

## <a name="next-steps"></a>Nästa steg

Använd följande guider för att skapa en Windows-miljö för virtuella skriv bord.

- Om du vill börja skapa en lösning för Skriv bords virtualisering läser du [skapa en klient i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Information om hur du skapar en adresspool i din Windows-klient för virtuella datorer finns i [skapa en adresspool med Azure Marketplace](create-host-pools-azure-marketplace.md).
- Information om hur du konfigurerar fullständigt hanterade fil resurser i molnet finns i [konfigurera Azure Files-resurs](/azure/storage/files/storage-files-active-directory-enable/).
- Information om hur du konfigurerar FSLogix profil behållare finns i [skapa en profil behållare för en värd-pool med en fil resurs](create-host-pools-user-profile.md).
- Information om hur du tilldelar användare till en adresspool finns i [Hantera app-grupper för Windows Virtual Desktop](manage-app-groups.md).
- För att få åtkomst till dina Windows-resurser för virtuella skriv bord från en webbläsare, se [Anslut till Windows Virtual Desktop](connect-web.md).
