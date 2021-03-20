---
title: Windows Virtual Desktop (klassisk) Host pool Azure Marketplace – Azure
description: Så här skapar du en Windows Virtual Desktop (klassisk)-värd pool med hjälp av Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04184a658ae1efd03afd3e920818eb5317bc7553
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008601"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Självstudie: skapa en värdbaserad pool i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../create-host-pools-azure-marketplace.md).

I den här självstudien får du lära dig hur du skapar en adresspool i en Windows-klient för virtuella skriv bord med hjälp av ett Microsoft Azure Marketplace erbjudande.

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows-miljöer för virtuella Skriv bords klienter. Varje adresspool kan innehålla en app-grupp som användare kan interagera med på samma sätt som på ett fysiskt skriv bord.

Uppgifterna i den här självstudien innehåller:

> [!div class="checklist"]
>
> * Skapa en adresspool i det virtuella Windows-skrivbordet.
> * Skapa en resurs grupp med virtuella datorer i en Azure-prenumeration.
> * Anslut de virtuella datorerna till Active Directory-domänen.
> * Registrera de virtuella datorerna med det virtuella Windows-skrivbordet.

## <a name="prerequisites"></a>Förutsättningar

* En klient i virtuellt skriv bord. En tidigare [självstudie](tenant-setup-azure-active-directory.md) skapar en klient.
* [PowerShell-modul för Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/).

När du har den här modulen kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Kör Azure Marketplace-erbjudandet för att etablera en ny värdbaserad pool

Så här kör du Azure Marketplace-erbjudandet för att etablera en ny adresspool:

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Ange **virtuellt Windows-skrivbord** i fönstret Marketplace search.
1. Välj **Windows Virtual Desktop – etablera en adresspool** och välj sedan **skapa**.

Efter det följer du anvisningarna i nästa avsnitt för att ange informationen för lämpliga flikar.

### <a name="basics"></a>Grundläggande inställningar

Det här kan du göra på fliken **grundläggande** :

1. Välj en **prenumeration**.
1. För **resurs grupp** väljer du **Skapa ny** och anger ett namn för den nya resurs gruppen.
1. Välj en **region**.
1. Ange ett namn för den värdbaserade poolen som är unik i Windows-klienten för virtuella skriv bord.
1. Välj **typ av skriv bord**. Om du väljer **privat**, tilldelas varje användare som ansluter till den här poolen permanent till en virtuell dator.
1. Ange användare som kan logga in på Windows-klienter för virtuella skriv bord och få åtkomst till ett skriv bord. Använd en kommaavgränsad lista. Om du till exempel vill tilldela `user1@contoso.com` och `user2@contoso.com` komma åt anger du *`user1@contoso.com,user2@contoso.com`*
1. För **platsen för tjänstens metadata** väljer du samma plats som det virtuella nätverk som har anslutning till Active Directory servern.

   >[!IMPORTANT]
   >Om du använder en ren Azure Active Directory Domain Services-lösning (Azure AD DS) och Azure Active Directory (Azure AD) måste du distribuera din adresspool i samma region som din Azure AD DS för att undvika fel i domän anslutning och autentiseringsuppgifter.

1. Välj **Nästa: Konfigurera virtuella datorer**.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

För fliken **Konfigurera virtuella datorer** :

1. Acceptera standardvärdena eller anpassa antalet och storleken på de virtuella datorerna.

    >[!NOTE]
    >Om den angivna storleken på den virtuella datorn som du söker efter inte visas i storleks väljaren, beror det på att vi inte har publicerat den på Azure Marketplace-verktyget ännu. Om du vill begära en storlek skapar du en begäran eller avröstar en befintlig begäran i [Windows Virtual Desktop UserVoice-forumet](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Ange ett prefix för namnen på de virtuella datorerna. Om du till exempel anger *prefix* kommer de virtuella datorerna att kallas **prefix-0**, **prefix-1** och så vidare.
1. Välj **Nästa: inställningar för virtuella datorer**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

För fliken **Inställningar för virtuell dator** :

1. För **avbildnings källa** väljer du källan och anger den information som krävs för att hitta den och hur den ska lagras. Alternativen skiljer sig åt för blob-lagring, hanterad avbildning och Galleri.

   Om du väljer att inte använda hanterade diskar väljer du det lagrings konto som innehåller *VHD* -filen.
1. Ange User Principal Name och lösen ord. Kontot måste vara det domän konto som ska ansluta till de virtuella datorerna till den Active Directory domänen. Samma användar namn och lösen ord kommer att skapas på de virtuella datorerna som ett lokalt konto. Du kan återställa de här lokala kontona senare.

   >[!NOTE]
   > Om du ansluter till dina virtuella datorer till en Azure AD DS-miljö måste du kontrol lera att din domän anslutning är medlem i [Administratörs gruppen för AAD-domänkontrollanten](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Kontot måste också ingå i den Azure AD DS-hanterade domänen eller Azure AD-klienten. Konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentiseras korrekt under processen för domän anslutning.

1. Välj det **virtuella nätverk** som har anslutning till Active Directory-servern och välj sedan ett undernät som är värd för de virtuella datorerna.
1. Välj **Nästa: information om virtuella Windows-datorer**.

### <a name="windows-virtual-desktop-tenant-information"></a>Klient information för virtuella Windows-datorer

På fliken **Windows-klient för virtuella Skriv bords information** :

1. Ange namnet på klient gruppen som innehåller din klient organisation för **Windows Virtual Desktop klient grupp namn**. Lämna det som standard om du inte angav ett angivet klient grupps namn.
1. Ange namnet på den klient där du vill skapa den här poolen för **Windows Virtual Desktop klient namn**.
1. Ange vilken typ av autentiseringsuppgifter du vill använda för att autentisera som Windows-ägare för klient organisation för virtuella skriv bord. Ange UPN-eller tjänstens huvud namn och lösen ord.

   Om du har slutfört [guiden skapa tjänst huvud namn och roll tilldelningar med PowerShell](create-service-principal-role-powershell.md)väljer du **tjänstens huvud namn**.

1. För **tjänstens huvud namn** anger du administratörs kontot för Azure AD-instansen som innehåller tjänstens huvud namn för klient **-ID för Azure AD**. Det finns bara stöd för tjänst huvud namn med autentiseringsuppgifter för lösen ord.
1. Välj **Nästa: Granska + skapa**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Slutför installationen och skapa den virtuella datorn

Granska installations informationen i **Granska och skapa**. Om du behöver ändra något går du tillbaka och gör ändringar. När du är klar väljer du **skapa** för att distribuera din värd-pool.

Beroende på hur många virtuella datorer du skapar kan den här processen ta 30 minuter eller mer att slutföra.

>[!IMPORTANT]
> Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows Virtual Desktop-miljö i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer.
>
> Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder just-in-Time-åtkomst. Mer information finns i [skydda dina hanterings portar med just-in-Time-åtkomst](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Valfritt Tilldela ytterligare användare till program gruppen Skriv bord

När du har skapat poolen med Azure Marketplace kan du tilldela fler användare till program gruppen Skriv bord. Hoppa över det här avsnittet om du inte vill lägga till fler.

Så här tilldelar du användare till program gruppen Skriv bord:

1. Öppna ett PowerShell-fönster.

1. Kör följande kommando för att logga in på Windows-miljön för virtuella skriv bord:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Lägg till användare i program gruppen Skriv bord med hjälp av det här kommandot:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Användarens UPN ska matcha användarens identitet i Azure AD, till exempel *user1@contoso.com* . Om du vill lägga till flera användare kör du kommandot för varje användare.

Användare som du lägger till i gruppen Skriv bords program kan logga in på Windows Virtual Desktop med stöd för fjärr skrivbords klienter och se en resurs för ett skrivbord-skrivbord.

Här är de klienter som stöds:

* [Fjärr skrivbords klient för Windows 7 och Windows 10](connect-windows-7-10-2019.md)
* [Webb klient för Windows Virtual Desktop](connect-web-2019.md)

## <a name="next-steps"></a>Nästa steg

Du har skapat en adresspool och tilldelat användare åtkomst till Skriv bordet. Du kan fylla i din värd-pool med RemoteApp-program. Mer information om hur du hanterar appar i virtuella Windows-datorer finns i den här självstudien:

> [!div class="nextstepaction"]
> [Själv studie kurs för hantering av app-grupper](manage-app-groups-2019.md)
