---
title: Skapa Windows-pool för virtuella Skriv bords värdar PowerShell – Azure
description: Så här skapar du en adresspool i Windows Virtual Desktop med PowerShell-cmdletar.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2c1ce95f9eba8c31b20d8e992fa1880a5d33da8d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447866"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Skapa en Windows-pool för virtuella skriv bord med PowerShell

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows-miljöer för virtuella Skriv bords klienter. Varje adresspool kan associeras med flera RemoteApp-grupper, en Skriv bords grupp och flera värdbaserade sessioner.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har följt instruktionerna i [Konfigurera PowerShell-modulen](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Använda PowerShell-klienten för att skapa en adresspool

Kör följande cmdlet för att logga in på Windows-miljön för virtuella skriv bord:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Den här cmdleten skapar den värdbaserade poolen, arbets ytan och skriv bords gruppen. Dessutom registrerar den Skriv bords gruppen på arbets ytan. Du kan antingen skapa en arbets yta med denna cmdlet eller använda en befintlig arbets yta.

Kör nästa cmdlet för att skapa en registrerings-token för att auktorisera en sessions värd att ansluta till värddatorn och spara den till en ny fil på din lokala dator. Du kan ange hur länge registrerings-token är giltig med hjälp av parametern-ExpirationHours.

>[!NOTE]
>Tokens förfallo datum får inte vara mindre än en timme och inte mer än en månad. Om du anger *ExpirationTime* utanför den gränsen skapar cmdleten inte token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Om du till exempel vill skapa en token som upphör att gälla om två timmar kör du denna cmdlet:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Därefter kör du denna cmdlet för att lägga till Azure Active Directory användare till standard gruppen för Skriv bords appar för poolen.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Kör den här nästa cmdlet för att lägga till Azure Active Directory användar grupper i standard gruppen för Skriv bords appar för den aktuella värd poolen:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Kör följande cmdlet för att exportera registrerings-token till en variabel, som du kommer att använda senare i [registrera de virtuella datorerna i Windows-poolen för virtuella skriv bord](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Skapa virtuella datorer för den värdbaserade poolen

Nu kan du skapa en virtuell Azure-dator som kan anslutas till din Windows-pool för virtuella skriv bord.

Du kan skapa en virtuell dator på flera sätt:

- [Skapa en virtuell dator från en Azure Gallery-avbildning](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Om du distribuerar en virtuell dator med Windows 7 som värd operativ system, är processen för att skapa och distribuera lite annorlunda. Mer information finns i [distribuera en virtuell Windows 7-dator på Windows Virtual Desktop](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

När du har skapat en sessions värd för virtuella datorer ska du [tillämpa en Windows-licens på en virtuell dator för en fjärrskrivbordssession](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) för att köra Windows-eller Windows Server-datorer utan att betala någon annan licens.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Förbereda de virtuella datorerna för Windows Virtual Desktop agent-installationer

Du måste göra följande för att förbereda dina virtuella datorer innan du kan installera de virtuella Windows-datorerna och registrera de virtuella datorerna i Windows-poolen för virtuella Skriv bords värdar:

- Du måste ha domän anslutning till datorn. Detta gör att inkommande virtuella Windows-skrivbord kan mappas från sina Azure Active Directory-konton till deras Active Directory konto och beviljas åtkomst till den virtuella datorn.
- Du måste installera RDSH-rollen (Remote Desktop Session Host) om den virtuella datorn kör ett Windows Server-operativsystem. RDSH-rollen tillåter att de virtuella Windows-datorerna installeras på rätt sätt.

För att lyckas med domän koppling gör du följande på varje virtuell dator:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta **kontroll panelen** på den virtuella datorn och välj **system**.
3. Välj **dator namn**, Välj **ändra inställningar** och välj sedan **ändra...**
4. Välj **domän** och ange sedan Active Directory domän i det virtuella nätverket.
5. Autentisera med ett domän konto som har behörighet att ansluta till datorer med domän anslutning.

    >[!NOTE]
    > Om du ansluter dina virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-miljö måste du se till att din domän anslutning också är medlem i [Administratörs gruppen för AAD-domänkontrollanten](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

>[!IMPORTANT]
>Vi rekommenderar att du inte aktiverar några principer eller konfigurationer som inaktiverar Windows Installer. Om du inaktiverar Windows Installer kommer tjänsten inte att kunna installera agent uppdateringar på värdarna och värdarna för din session fungerar inte som de ska.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrera de virtuella datorerna i Windows-poolen för virtuella skriv bord

Att registrera de virtuella datorerna på en Windows-pool för virtuella skriv bord är lika enkelt som att installera de virtuella Windows-datorerna.

Registrera Windows-agenter för virtuella skriv bord genom att göra följande på varje virtuell dator:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Ladda ned och installera Windows-agenten för virtuella skriv bord.
   - Hämta [Windows-agenten för virtuella skriv bord](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kör installationsprogrammet. När du uppmanas att ange registrerings-token anger du det värde som du fick från cmdleten **Get-AzWvdRegistrationInfo** .
3. Ladda ned och installera Windows Virtual Desktop agent start program.
   - Hämta [Start programmet för Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kör installationsprogrammet.

>[!IMPORTANT]
>Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows-miljö för virtuella skriv bord i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer. Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder [just-in-Time VM-åtkomst](../security-center/security-center-just-in-time.md). Vi rekommenderar också att du inte tilldelar dina virtuella datorer till en offentlig IP-adress.

## <a name="update-the-agent"></a>Uppdatera agenten

Du måste uppdatera agenten om du är i någon av följande situationer:

- Du vill migrera en tidigare registrerad sessionsnyckel till en ny adresspool
- Sessionens värd visas inte i din värd-pool efter en uppdatering

Så här uppdaterar du agenten:

1. Logga in på den virtuella datorn som administratör.
2. Gå till **tjänster** och stoppa **Rdagent** -och **fjärr skrivbords agentens inläsnings** processer.
3. Leta sedan upp agenten och Start programmet MSIs. De finns antingen i mappen **C:\DeployAgent** eller på den plats där du sparade den när du installerade den.
4. Hitta följande filer och avinstallera dem:
     
     - Microsoft. RDInfra. RDAgent. Installer-x64-verx. x. x
     - Microsoft. RDInfra. RDAgentBootLoader. Installer-x64

   Om du vill avinstallera filerna högerklickar du på varje fil namn och väljer sedan **Avinstallera**.
5. Alternativt kan du också ta bort följande register inställningar:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. När du har avinstallerat dessa objekt bör du ta bort alla associationer med den gamla poolen. Om du vill omregistrera den här värden till tjänsten följer du anvisningarna i [registrera de virtuella datorerna i Windows-poolen för virtuella skriv bord](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).


## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en modempool kan du fylla den med RemoteApp-programmet. Mer information om hur du hanterar appar i Windows Virtual Desktop finns i själv studie kursen Hantera app-grupper.

> [!div class="nextstepaction"]
> [Själv studie kurs för hantering av app-grupper](./manage-app-groups.md)
