---
title: Windows Virtual Desktop FSLogix profil container Share-Azure
description: Konfigurera en FSLogix profil behållare för en Windows-pool för virtuella skriv bord med en virtuell dator baserad fil resurs.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002579"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Skapa en profilcontainer för en värdpool med hjälp av en filresurs

Tjänsten Windows Virtual Desktop tillhandahåller FSLogix profil behållare som den rekommenderade användar profil lösningen. Vi rekommenderar inte att du använder UPD-lösningen (User profile disk) som kommer att vara inaktuell i framtida versioner av Windows Virtual Desktop.

I den här artikeln får du veta hur du konfigurerar en FSLogix profil behållar resurs för en adresspool med en virtuell dator baserad fil resurs. Vi rekommenderar starkt att du använder Azure Files i stället för fil resurser. Mer FSLogix-dokumentation finns på [FSLogix-webbplatsen](https://docs.fslogix.com/).

>[!NOTE]
>Om du letar efter jämförelse material om de olika lagrings alternativen för FSLogix Profile container på Azure, se [lagrings alternativ för FSLogix profil behållare](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Skapa en ny virtuell dator som fungerar som en fil resurs

När du skapar den virtuella datorn måste du placera den antingen i samma virtuella nätverk som de virtuella datorerna i poolen för värdar eller i ett virtuellt nätverk som har anslutning till de virtuella datorerna i poolen för värdar. Du kan skapa en virtuell dator på flera sätt:

- [Skapa en virtuell dator från en Azure Gallery-avbildning](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

När du har skapat den virtuella datorn ansluter du den till domänen genom att göra följande:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta **kontroll panelen** på den virtuella datorn och välj **system**.
3. Välj **dator namn**, Välj **ändra inställningar** och välj sedan **ändra...**
4. Välj **domän** och ange sedan Active Directory domän i det virtuella nätverket.
5. Autentisera med ett domän konto som har behörighet att ansluta till datorer med domän anslutning.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Förbered den virtuella datorn så att den fungerar som en fil resurs för användar profiler

Här följer allmänna anvisningar om hur du förbereder en virtuell dator så att den fungerar som en fil resurs för användar profiler:

1. Lägg till den virtuella Windows-skrivbordet Active Directory användare i en [Active Directory säkerhets grupp](/windows/security/identity-protection/access-control/active-directory-security-groups/). Den här säkerhets gruppen används för att autentisera virtuella Windows-skrivbordet användare till den virtuella dator resursen som du nyss skapade.
2. [Anslut till den virtuella datorn för fil resursen](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. På den virtuella datorn fil resurs skapar du en mapp på **C-enheten** som ska användas som profil resurs.
4. Högerklicka på den nya mappen, Välj **Egenskaper**, Välj **delning** och välj sedan **Avancerad delning..**..
5. Välj **dela den här mappen**, Välj **behörigheter..**. och välj sedan **Lägg till.**...
6. Sök efter säkerhets gruppen där du lade till de virtuella Windows-användarna och kontrol lera att gruppen har **fullständig behörighet**.
7. När du har lagt till säkerhets gruppen högerklickar du på mappen, väljer **Egenskaper**, väljer **delning** och kopierar sedan **nätverks Sök vägen** för senare användning.

Mer information om behörigheter finns i FSLogix- [dokumentationen](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurera FSLogix profil behållare

Om du vill konfigurera de virtuella datorerna med FSLogix-programvaran gör du följande på varje dator som är registrerad på värddatorn:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta en webbläsare och navigera till [den här länken](https://go.microsoft.com/fwlink/?linkid=2084562) för att ladda ned FSLogix-agenten.
3. Gå till \\ \\ Win32- \\ versionen eller \\ \\ x64- \\ versionen i. zip-filen och kör **FSLogixAppsSetup** för att installera FSLogix-agenten.  Mer information om hur du installerar FSLogix finns i [Hämta och installera FSLogix](/fslogix/install-ht/).
4. Navigera till **Program Files**  >  **FSLogix**-  >  **appar** för att bekräfta att agenten är installerad.
5. Från Start-menyn kör du **regedit** som administratör. Navigera till **datorn \\ HKEY_LOCAL_MACHINE \\ program vara \\ FSLogix**.
6. Skapa en nyckel med namnet **profiler**.
7. Skapa följande värden för profil nyckeln:

| Namn                | Typ               | Data/värde                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Värde med flera strängar | "Nätverks Sök väg för fil resurs"     |

>[!IMPORTANT]
>Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows-miljö för virtuella skriv bord i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer. Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder [just-in-Time VM-åtkomst](../security-center/security-center-just-in-time.md).
