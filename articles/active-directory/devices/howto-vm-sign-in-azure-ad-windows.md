---
title: Logga in på en virtuell Windows-dator i Azure med Azure Active Directory (förhandsversion)
description: Azure AD-inloggning på en virtuell Azure-dator som kör Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776509"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på den virtuella Windows-datorn i Azure med hjälp Azure Active Directory autentisering (förhandsversion)

Organisationer kan nu använda Azure Active Directory-autentisering (AD) för sina virtuella Azure-datorer som kör **Windows Server 2019 Datacenter edition** eller Windows 10 **1809** och senare. Genom att använda Azure AD för att autentisera till virtuella datorer får du ett sätt att centralt kontrollera och genomdriva principer. Med verktyg som rollbaserad åtkomstkontroll i Azure (Azure RBAC) och villkorsstyrd åtkomst i Azure AD kan du styra vem som har åtkomst till en virtuell dator. Den här artikeln visar hur du skapar och konfigurerar en virtuell Windows Server 2019-dator för att använda Azure AD-autentisering.

> [!NOTE]
> Azure AD-inloggning för virtuella Azure Windows-datorer är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Windows-datorer i Azure, inklusive:

- Använd samma federerade eller hanterade Azure AD-autentiseringsuppgifter som du normalt använder.
- Du behöver inte längre hantera lokala administratörskonton.
- Med Azure RBAC kan du bevilja lämplig åtkomst till virtuella datorer baserat på behov och ta bort den när den inte längre behövs.
- Innan åtkomst till en virtuell dator tillåts kan villkorsstyrd åtkomst i Azure AD framtvinga ytterligare krav som: 
   - Multifaktorautentisering
   - Riskkontroll för inloggning
- Automatisera och skala Azure AD-anslutning för virtuella Azure Windows-datorer som ingår i dina VDI-distributioner.

> [!NOTE]
> När du aktiverar den här funktionen blir dina virtuella Windows-datorer i Azure Azure-ansluten till Azure AD. Du kan inte ansluta den till en annan domän, till exempel lokal AD eller Azure AD DS. Om du behöver göra det måste du koppla bort den virtuella datorn från Azure AD-klienten genom att avinstallera tillägget.

## <a name="requirements"></a>Krav

### <a name="supported-azure-regions-and-windows-distributions"></a>Azure-regioner och Windows-distributioner som stöds

Följande Windows-distributioner stöds för närvarande i förhandsversionen av den här funktionen:

- Windows Server 2019 Datacenter
- Windows 10 1809 och senare

> [!IMPORTANT]
> Fjärranslutning till virtuella datorer som är ansluten till Azure AD tillåts endast från Windows 10-datorer som antingen är Azure AD-registrerade (från och med Windows 10 20H1), Azure AD-ansluten eller Hybrid Azure AD-ansluten till samma katalog som den virtuella datorn.  

Följande Azure-regioner stöds för närvarande i förhandsversionen av den här funktionen:

- Alla globala Azure-regioner

> [!IMPORTANT]
> Om du vill använda den här förhandsversionsfunktionen distribuerar du bara en Windows-distribution som stöds och i en Azure-region som stöds. Funktionen stöds för närvarande inte i Azure Government eller nationella moln.

### <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Windows-datorer i Azure måste du se till att din nätverkskonfiguration för virtuella datorer tillåter utgående åtkomst till följande slutpunkter via TCP-port 443:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivera Azure AD-inloggning för virtuella Windows-datorer i Azure

Om du vill använda Azure AD-inloggning för virtuella Windows-datorer i Azure måste du först aktivera Inloggningsalternativ för Azure AD för din virtuella Windows-dator och sedan konfigurera Azure-rolltilldelningar för användare som har behörighet att logga in på den virtuella datorn.
Det finns flera sätt att aktivera Azure AD-inloggning för din virtuella Windows-dator:

- Använda den Azure Portal när du skapar en virtuell Windows-dator
- Använda den Azure Cloud Shell när du skapar en virtuell Windows-dator **eller för en befintlig virtuell Windows-dator**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Använda Azure Portal skapa VM-upplevelse för att aktivera Azure AD-inloggning

Du kan aktivera Azure AD-inloggning för Windows Server 2019 Datacenter eller Windows 10 1809 och senare VM-avbildningar. 

Så här skapar du en virtuell Windows Server 2019 Datacenter-dator i Azure med Azure AD-inloggning: 

1. Logga in på [Azure Portal](https://portal.azure.com)med ett konto som har åtkomst för att skapa virtuella datorer och välj **+ Skapa en resurs**.
1. Skriv **Windows Server** i Sök på Marketplace-sökfältet.
   1. Klicka **på Windows Server** och välj Windows Server **2019 Datacenter** i listrutan Välj en programvaruplan.
   1. Klicka på **Skapa.**
1. På fliken "Hantering" aktiverar du alternativet logga in med **AAD-autentiseringsuppgifter (förhandsversion)** under avsnittet Azure Active Directory från Av till **På**.
1. Kontrollera att **System tilldelad hanterad** identitet under avsnittet Identitet är inställt på **På.** Den här åtgärden bör ske automatiskt när du aktiverar inloggning med Azure AD-autentiseringsuppgifter.
1. Gå igenom resten av upplevelsen med att skapa en virtuell dator. I den här förhandsversionen måste du skapa ett administratörsnamn och lösenord för den virtuella datorn.

![Logga in med Azure AD-autentiseringsuppgifter och skapa en virtuell dator](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> För att kunna logga in på den virtuella datorn med dina Azure AD-autentiseringsuppgifter måste du först konfigurera rolltilldelningar för den virtuella datorn enligt beskrivningen i något av avsnitten nedan.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Använda den Azure Cloud Shell för att aktivera Azure AD-inloggning

Azure Cloud Shell är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Välj knappen Kopiera för att kopiera koden, klistra in den i Cloud Shell och tryck på RETUR för att köra den. Det finns flera olika sätt att öppna Cloud Shell:

- Välj **Prova** i det övre högra hörnet av ett kodblock.
- Öppna Cloud Shell i din webbläsare.
- Välj knappen Cloud Shell på menyn längst upp till höger i [Azure Portal](https://portal.azure.com).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.31 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa artikeln [Installera Azure CLI.](/cli/azure/install-azure-cli)

1. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). 
1. Skapa en virtuell dator med [az vm create med](/cli/azure/vm#az_vm_create) hjälp av en distribution som stöds i en region som stöds. 
1. Installera azure AD-tillägget för inloggning för virtuell dator. 

I följande exempel distribueras en virtuell dator med namnet myVM som använder Win2019Datacenter till en resursgrupp med namnet myResourceGroup i regionen southcentralus. I följande exempel kan du ange dina egna resursgrupps- och VM-namn efter behov.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Du måste aktivera System-tilldelad hanterad identitet på den virtuella datorn innan du installerar tillägget för azure AD-inloggning för virtuella datorer.

Det tar några minuter att skapa den virtuella datorn och stödresurser.

Installera slutligen vm-tillägget för Azure AD-inloggning för att aktivera Azure AD-inloggning för virtuella Windows-datorer. VM-tillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer. Använd [az vm extension](/cli/azure/vm/extension#az_vm_extension_set) set för att installera tillägget AADLoginForWindows på den virtuella datorn med namnet i `myVM` `myResourceGroup` resursgruppen:

> [!NOTE]
> Du kan installera tillägget AADLoginForWindows på en befintlig virtuell dator med Windows Server 2019 eller Windows 10 1809 och senare för att aktivera det för Azure AD-autentisering. Ett exempel på AZ CLI visas nedan.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

av `provisioningState` `Succeeded` visas när tillägget har installerats på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Nu när du har skapat den virtuella datorn måste du konfigurera Azure RBAC-principen för att avgöra vem som kan logga in på den virtuella datorn. Två Azure-roller används för att auktorisera VM-inloggning:

- **Inloggning för administratör för virtuell** dator: Användare med den här rollen kan logga in på en virtuell Azure-dator med administratörsbehörighet.
- **Användarinloggning för virtuell** dator: Användare med den här rollen kan logga in på en virtuell Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill tillåta att en användare loggar in på den virtuella datorn via RDP måste du tilldela antingen rollen Administratör för virtuell dator eller Inloggning för virtuell datoranvändare. En Azure-användare med rollen Ägare eller Deltagare tilldelad för en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via RDP. Detta är för att tillhandahålla en granskad uppdelning mellan en uppsättning personer som kontrollerar virtuella datorer jämfört med en uppsättning personer som har åtkomst till virtuella datorer.

Det finns flera sätt att konfigurera rolltilldelningar för virtuella datorer:

- Använda Azure AD-portalen
- Använda Azure Cloud Shell upplevelse

> [!NOTE]
> Rollerna Inloggning för administratör för virtuell dator och Inloggning för virtuell dator använder dataÅtgärder och kan därför inte tilldelas i hanteringsgruppomfånget. För närvarande kan dessa roller endast tilldelas för prenumerationen, resursgruppen eller resursomfånget.

### <a name="using-azure-ad-portal-experience"></a>Använda Azure AD-portalen

Så här konfigurerar du rolltilldelningar för dina Azure AD-aktiverade virtuella Windows Server 2019 Datacenter-datorer:

1. Gå till översiktssidan för den specifika virtuella datorn
1. Välj **Åtkomstkontroll (IAM)** från menyalternativen
1. Välj **Lägg till,** **Lägg till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.
1. I **listrutan** Roll väljer du en roll, till exempel Administratörsinloggning för virtuell dator **eller** **Användarinloggning för virtuell dator.**
1. I fältet **Välj** väljer du en användare, grupp, tjänstens huvudnamn eller hanterad identitet. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan **Välj** om du vill söka i katalogen.
1. Välj **Spara** för att tilldela rollen.

Efter en liten stund tilldelas säkerhetsobjektrollen i det valda omfånget.

![Tilldela roller till användare som kommer åt den virtuella datorn](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Använda Azure Cloud Shell upplevelse

I följande exempel används [az role assignment create för](/cli/azure/role/assignment#az_role_assignment_create) att tilldela den virtuella datorns administratör inloggningsroll till den virtuella datorn för din aktuella Azure-användare. Användarnamnet för ditt aktiva Azure-konto hämtas med [az account show](/cli/azure/account#az_account_show)och omfånget är inställt på den virtuella dator som skapades i ett tidigare steg med az vm [show](/cli/azure/vm#az_vm_show). Omfånget kan också tilldelas på resursgrupps- eller prenumerationsnivå, och vanliga Azure RBAC-arvsbehörigheter gäller. Mer information finns i Logga [in på en virtuell Linux-dator i Azure med hjälp Azure Active Directory autentisering.](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om domänen för AAD-domänen och inloggningsanvändardomänen inte matchar måste du ange objekt-ID:t för ditt användarkonto med , inte `--assignee-object-id` bara användarnamnet för `--assignee` . Du kan hämta objekt-ID:t för ditt användarkonto med [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Mer information om hur du använder Azure RBAC för att hantera åtkomst till dina Azure-prenumerationsresurser finns i följande artiklar:

- [Tilldela Azure-roller med Hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Tilldela Azure-roller med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Använda villkorsstyrd åtkomst

Du kan framtvinga principer för villkorlig åtkomst, till exempel multifaktorautentisering eller riskkontroll för användar inloggning innan du auktoriserar åtkomst till virtuella Windows-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa principen för villkorlig åtkomst måste du välja inloggningsappen för virtuella Azure Windows-datorer från tilldelningsalternativet för molnappar eller åtgärder och sedan använda inloggningsrisk som ett villkor och/eller kräva multifaktorautentisering som en beviljad åtkomstkontroll. 

> [!NOTE]
> Om du använder "Kräv multifaktorautentisering" som en beviljande åtkomstkontroll för att begära åtkomst till appen "Inloggning för virtuell Azure Windows-dator" måste du ange multifaktorautentiseringsanspråk som en del av klienten som initierar RDP-sessionen till den virtuella Windows-måldatorn i Azure. Det enda sättet att åstadkomma detta på en Windows 10-klient är att använda Windows Hello för företag PIN-kod eller biometrisk autentisering med RDP-klienten. Stöd för biometrisk autentisering lades till i RDP-klienten i Windows 10 version 1809. Fjärranslutning med Windows Hello för företag-autentisering är endast tillgängligt för distributioner som använder certifikatförtroendemodell och som för närvarande inte är tillgängliga för nyckelförtroendemodellen.

> [!WARNING]
> Per användare Aktiverad/Framtvingad Azure AD Multi-Factor Authentication stöds inte för vm-inloggning.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logga in med Azure AD-autentiseringsuppgifter på en virtuell Windows-dator

> [!IMPORTANT]
> Fjärranslutning till virtuella datorer som är ansluten till Azure AD tillåts endast från Windows 10-datorer som antingen är Azure AD-registrerade (minsta  nödvändiga version är 20H1) eller Azure AD-ansluten eller hybrid Azure AD ansluten till samma katalog som den virtuella datorn. För ATT RDP ska kunna använda Autentiseringsuppgifter för Azure AD måste användaren dessutom tillhöra någon av de två Azure-rollerna, Inloggning för administratör för virtuell dator eller Användarinloggning för virtuell dator. Om du använder en Azure AD Windows 10 dator måste du ange autentiseringsuppgifter i `AzureAD\UPN` formatet (till exempel `AzureAD\john@contoso.com` ). För stunden kan Azure Bastion inte användas för att logga in med hjälp av Azure Active Directory-autentisering med tillägget AADLoginForWindows. endast direkt RDP stöds.

Så här loggar du in på din virtuella Windows Server 2019-dator med Azure AD: 

1. Gå till översiktssidan för den virtuella dator som har aktiverats med Azure AD-inloggning.
1. Välj **Anslut** för att öppna bladet Anslut till virtuell dator.
1. Välj **Hämta RDP-fil**.
1. Välj **Öppna** för att starta Anslutning till fjärrskrivbord klient.
1. Välj **Anslut** för att starta windows-inloggningsdialogrutan.
1. Logga in med dina autentiseringsuppgifter för Azure AD.

Du är nu inloggad på den virtuella Windows Server 2019 Azure-datorn med rollbehörigheter som tilldelade, till exempel VM-användare eller VM-administratör. 

> [!NOTE]
> Du kan spara . RDP-fil lokalt på datorn för att starta framtida fjärrskrivbordsanslutningar till den virtuella datorn i stället för att behöva gå till översiktssidan för virtuella datorer i Azure Portal och använda alternativet Anslut.

## <a name="troubleshoot"></a>Felsöka

### <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

Tillägget AADLoginForWindows måste installeras korrekt för att den virtuella datorn ska kunna slutföra Azure AD-anslutningsprocessen. Utför följande steg om det inte går att installera VM-tillägget korrekt.

1. RDP till den virtuella datorn med hjälp av det lokala administratörskontot och `CommandExecution.log` granska filen under:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Om tillägget startas om efter det första felet sparas loggen med distributionsfelet som `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Öppna en PowerShell-kommandotolk på den virtuella datorn och verifiera dessa frågor mot Instance Metadata Service(IMDS) slutpunkten som körs på Azure-värden returnerar:

   | Kommando som ska köras | Förväntad utdata |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Korrigera information om den virtuella Azure-datorn |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Giltigt klientorganisations-ID som är associerat med Azure-prenumerationen |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Giltig åtkomsttoken som utfärdats av Azure Active Directory för den hanterade identitet som har tilldelats den här virtuella datorn |

   > [!NOTE]
   > Åtkomsttoken kan avkodas med ett verktyg som [calebb.net](http://calebb.net/). Kontrollera att `appid` i åtkomsttoken matchar den hanterade identitet som tilldelats den virtuella datorn.

1. Se till att de slutpunkter som krävs är tillgängliga från den virtuella datorn med hjälp av kommandoraden:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klientorganisations-ID som är associerat med Azure-prenumerationen.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Enhetstillståndet kan visas genom att köra `dsregcmd /status` . Målet är att Enhetstillstånd ska visas som `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD-kopplingsaktiviteten avbildas i Loggboken under `User Device Registration\Admin` loggen.

Om tillägget AADLoginForWindows misslyckas med viss felkod kan du utföra följande steg:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: AADLoginForWindows-tillägget kan inte installeras med terminalfelkoden "1007" och slutkoden: -2145648574.

Den här slutkoden översätts `DSREG_E_MSI_TENANTID_UNAVAILABLE` till eftersom tillägget inte kan fråga Azure AD-klientinformationen.

1. Kontrollera att den virtuella Azure-datorn kan hämta TenantID från Instance Metadata Service.

   - RDP till den virtuella datorn som lokal administratör och kontrollera att slutpunkten returnerar giltigt klientorganisations-ID genom att köra det här kommandot från en upphöjd kommandorad på den virtuella datorn:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. Vm-administratören försöker installera tillägget AADLoginForWindows, men en systemtilldelad hanterad identitet har inte aktiverat den virtuella datorn först. Gå till bladet Identitet för den virtuella datorn. På fliken System tilldelad kontrollerar du att Status är på.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: AADLoginForWindows-tillägget kan inte installeras med slutkod: -2145648607

Den här slutkoden översätts `DSREG_AUTOJOIN_DISC_FAILED` till eftersom tillägget inte kan nå `https://enterpriseregistration.windows.net` slutpunkten.

1. Kontrollera att de slutpunkter som krävs är tillgängliga från den virtuella datorn med hjälp av kommandoraden:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klientorganisations-ID som är associerat med Azure-prenumerationen. Om du behöver hitta klientorganisations-ID:t kan du hovra över ditt kontonamn för att hämta katalog-/klient-ID:t eller välja **Azure Active Directory > Egenskaper > Katalog-ID** i Azure Portal.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Om något av kommandona misslyckas med "Det gick inte att matcha värden" kan du prova att köra det här kommandot för att fastställa `<URL>` vilken DNS-server som används av den virtuella datorn.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersätt `<URL>` med de fullständigt kvalificerade domännamnen som används av slutpunkterna, till exempel `login.microsoftonline.com` .

1. Därefter kan du se om kommandot kan lyckas genom att ange en offentlig DNS-server:

   `nslookup <URL> 208.67.222.222`

1. Om det behövs ändrar du DNS-servern som är tilldelad till den nätverkssäkerhetsgrupp som den virtuella Azure-datorn tillhör.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: AADLoginForWindows-tillägget kan inte installeras med slutkod: 51

Slutkod 51 betyder "Det här tillägget stöds inte på den virtuella datorns operativsystem".

I den offentliga förhandsversionen är tillägget AADLoginForWindows endast avsett att installeras på Windows Server 2019 eller Windows 10 (version 1809 eller senare). Kontrollera att Windows-versionen stöds. Om Windows-versionen inte stöds avinstallerar du VM-tillägget.

### <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggningsproblem

Några vanliga fel när du försöker använda RDP med Azure AD-autentiseringsuppgifter är att inga Azure-roller har tilldelats, att ingen obehörig klient eller 2FA-inloggningsmetod krävs. Använd följande information för att åtgärda dessa problem.

Enheten och SSO-tillståndet kan visas genom att köra `dsregcmd /status` . Målet är att Enhetstillstånd ska visas som `AzureAdJoined : YES` och för att visa `SSO State` `AzureAdPrt : YES` .

Dessutom avbildas RDP-inloggning med Azure AD-konton i Loggboken under `AAD\Operational` händelseloggarna.

#### <a name="azure-role-not-assigned"></a>Azure-rollen har inte tilldelats

Om du ser följande felmeddelande när du initierar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Ditt konto har konfigurerats för att förhindra att du använder den här enheten. Kontakta systemadministratören om du vill ha mer information.

![Ditt konto har konfigurerats för att förhindra att du använder den här enheten.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Kontrollera att du har [konfigurerat Azure RBAC-principer](../../virtual-machines/linux/login-using-aad.md) för den virtuella datorn som ger användaren antingen rollen Administratör för virtuell dator eller Användarinloggning för virtuell dator:

> [!NOTE]
> Om du får problem med Azure-rolltilldelningar kan du gå till [Felsöka Azure RBAC.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)
 
#### <a name="unauthorized-client"></a>Obehörig klient

Om du ser följande felmeddelande när du initierar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Dina autentiseringsuppgifter fungerade inte.

![Dina autentiseringsuppgifter fungerade inte](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Kontrollera att den Windows 10-dator som du använder för att initiera fjärrskrivbordsanslutningen är en dator som antingen är ansluten till Azure AD eller en Hybrid Azure AD-ansluten till samma Azure AD-katalog som den virtuella datorn är ansluten till. Mer information om enhetsidentitet finns i artikeln Vad [är en enhetsidentitet?](./overview.md)

> [!NOTE]
> Windows 10 Version 20H1 har lagts till för en Azure AD-registrerad dator för att initiera RDP-anslutning till den virtuella datorn. När du använder en Azure AD-registrerad (inte Azure AD-ansluten eller Hybrid Azure AD-ansluten) dator som RDP-klient för att initiera anslutningar till den virtuella datorn måste du ange autentiseringsuppgifter i formatet `AzureAD\UPN` (till exempel `AzureAD\john@contoso.com` ).

Kontrollera att tillägget AADLoginForWindows inte avinstallerades när Azure AD-anslutning slutfördes.

Kontrollera också att säkerhetspolicyn "Nätverkssäkerhet: Tillåt PKU2U-autentiseringsförfrågningar till den här datorn att använda onlineidentiteter" är aktiverad på **både** servern och klienten.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-inloggningsmetod krävs

Om du ser följande felmeddelande när du initierar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Inloggningsmetoden som du försöker använda är inte tillåten. Prova en annan inloggningsmetod eller kontakta systemadministratören.

![Inloggningsmetoden som du försöker använda är inte tillåten.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Om du har konfigurerat en princip för villkorsstyrd åtkomst som kräver multifaktorautentisering (MFA) innan du kan komma åt resursen måste du se till att Windows 10-datorn som initierar fjärrskrivbordsanslutningen till den virtuella datorn loggar in med en stark autentiseringsmetod, till exempel Windows Hello. Om du inte använder en stark autentiseringsmetod för anslutning till fjärrskrivbord visas föregående fel.

Om du inte har distribuerat Windows Hello för företag och det inte är ett alternativ för tillfället kan du undanta MFA-krav genom att konfigurera en princip för villkorsstyrd åtkomst som exkluderar "Azure Windows VM Sign-In"-appen från listan över molnappar som kräver MFA. Mer information om Windows Hello för företag finns i [Windows Hello för företag Översikt](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Windows Hello för företag PIN-autentisering med RDP har stöd av Windows 10 för flera versioner, men stöd för biometrisk autentisering med RDP lades till i Windows 10 version 1809. Användning Windows Hello för företag autentisering under RDP är endast tillgängligt för distributioner som använder certifikatförtroendemodell och för närvarande inte är tillgänglig för nyckelförtroendemodell.
 
## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här förhandsgranskningsfunktionen eller rapportera problem med att använda den på [Azure AD-feedbackforumet.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](../fundamentals/active-directory-whatis.md).
