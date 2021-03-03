---
title: Logga in på den virtuella Windows-datorn i Azure med hjälp av Azure Active Directory (för hands version)
description: Azure AD logga in på en virtuell Azure-dator som kör Windows
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
ms.openlocfilehash: ee392666a6c6807497eeac2a2291dac915c4e136
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644314"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på den virtuella Windows-datorn i Azure med Azure Active Directory autentisering (för hands version)

Organisationer kan nu använda Azure Active Directory (AD)-autentisering för sina virtuella Azure-datorer (VM) som kör **Windows Server 2019 Data Center Edition** eller **Windows 10 1809** och senare. Genom att använda Azure AD för att autentisera till virtuella datorer får du ett sätt att centralt styra och tillämpa principer. Verktyg som rollbaserad åtkomst kontroll i Azure (Azure RBAC) och Azure AD villkorlig åtkomst gör det möjligt att kontrol lera vem som har åtkomst till en virtuell dator. Den här artikeln visar hur du skapar och konfigurerar en virtuell Windows Server 2019-dator för att använda Azure AD-autentisering.

> [!NOTE]
> Azure AD-inloggning för virtuella Azure Windows-datorer är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Windows-datorer i Azure, inklusive:

- Använd samma federerade eller hanterade autentiseringsuppgifter för Azure AD som du vanligt vis använder.
- Du behöver inte längre hantera lokala administratörs konton.
- Med Azure RBAC kan du ge rätt åtkomst till virtuella datorer baserat på behov och ta bort den när den inte längre behövs.
- Innan du tillåter åtkomst till en virtuell dator kan villkorlig åtkomst för Azure AD framtvinga ytterligare krav som: 
   - Multifaktorautentisering
   - Kontroll av inloggnings risker
- Automatisera och skala Azure AD-anslutning för virtuella Azure Windows-datorer som ingår i dina VDI-distributioner.

> [!NOTE]
> När du har aktiverat den här funktionen blir dina virtuella Windows-datorer i Azure anslutna till Azure AD. Du kan inte ansluta den till en annan domän, t. ex. lokal AD eller Azure AD DS. Om du behöver göra det måste du koppla från den virtuella datorn från din Azure AD-klient genom att avinstallera tillägget.

## <a name="requirements"></a>Krav

### <a name="supported-azure-regions-and-windows-distributions"></a>Azure-regioner och Windows-distributioner som stöds

Följande Windows-distributioner stöds för närvarande i för hands versionen av den här funktionen:

- Windows Server 2019 Datacenter
- Windows 10 1809 och senare

> [!IMPORTANT]
> Fjärr anslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som antingen är Azure AD-registrerade (från och med Windows 10 20H1), Azure AD som är ansluten eller en hybrid Azure AD-anslutning till **samma** katalog som den virtuella datorn. 

Följande Azure-regioner stöds för närvarande i för hands versionen av den här funktionen:

- Alla globala Azure-regioner

> [!IMPORTANT]
> Om du vill använda den här för hands versionen kan du bara distribuera en Windows-distribution som stöds och i en Azure-region som stöds. Funktionen stöds för närvarande inte i Azure Government eller suveräna moln.

### <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Windows-datorer i Azure måste du se till att nätverks konfigurationen för virtuella datorer tillåter utgående åtkomst till följande slut punkter via TCP-port 443:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivera Azure AD-inloggning för virtuell Windows-dator i Azure

Om du vill använda Azure AD-inloggning för Windows VM i Azure måste du först aktivera Azure AD-inloggningsnamn för din virtuella Windows-dator och sedan måste du konfigurera Azure Role-tilldelningar för användare som har behörighet att logga in på den virtuella datorn.
Det finns flera sätt att aktivera Azure AD-inloggning för din virtuella Windows-dator:

- Använda Azure Portal upplevelsen när du skapar en virtuell Windows-dator
- Använda Azure Cloud Shell upplevelsen när du skapar en virtuell Windows-dator **eller för en befintlig virtuell Windows-dator**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Använda Azure Portal skapa VM-upplevelse för att aktivera Azure AD-inloggning

Du kan aktivera Azure AD-inloggning för Windows Server 2019 Data Center eller Windows 10 1809 och senare VM-avbildningar. 

Så här skapar du en Windows Server 2019 datacenter-VM i Azure med Azure AD-inloggning: 

1. Logga in på [Azure Portal](https://portal.azure.com)med ett konto som har åtkomst till att skapa virtuella datorer och välj **+ skapa en resurs**.
1. Skriv **Windows Server** i Sök i Sök fältet i Marketplace.
   1. Klicka på **Windows Server** och välj **Windows Server 2019 Data Center** i list rutan Välj en program varu plan.
   1. Klicka på **skapa**.
1. På fliken "hantering" aktiverar du alternativet att **Logga in med AAD-autentiseringsuppgifter (för hands version)** under avsnittet Azure Active Directory från till **på**.
1. Se till att **systemtilldelad hanterad identitet** under avsnittet identitet är inställt **på on**. Den här åtgärden ska ske automatiskt när du aktiverar inloggning med autentiseringsuppgifter för Azure AD.
1. Gå igenom resten av upplevelsen med att skapa en virtuell dator. Under för hands versionen måste du skapa ett administratörs användar namn och lösen ord för den virtuella datorn.

![Logga in med Azure AD-autentiseringsuppgifter skapa en virtuell dator](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> För att kunna logga in på den virtuella datorn med din Azure AD-autentiseringsuppgift måste du först konfigurera roll tilldelningar för den virtuella datorn enligt beskrivningen i något av avsnitten nedan.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Använda Azure Cloud Shell-upplevelsen för att aktivera Azure AD-inloggning

Azure Cloud Shell är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Välj knappen Kopiera för att kopiera koden, klistra in den i Cloud Shell och tryck på RETUR för att köra den. Det finns flera olika sätt att öppna Cloud Shell:

- Välj **Prova** i det övre högra hörnet av ett kodblock.
- Öppna Cloud Shell i din webbläsare.
- Välj knappen Cloud Shell på menyn längst upp till höger i [Azure Portal](https://portal.azure.com).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.31 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa artikeln [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). 
1. Skapa en virtuell dator med [AZ VM Create](/cli/azure/vm#az-vm-create) med en distribution som stöds i en region som stöds. 
1. Installera tillägget Azure AD login VM. 

I följande exempel distribueras en virtuell dator med namnet myVM som använder Win2019Datacenter i en resurs grupp med namnet myResourceGroup i usasödracentrala-regionen. I följande exempel kan du ange dina egna resurs grupper och namn på virtuella datorer efter behov.

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
> Det krävs att du aktiverar systemtilldelad hanterad identitet på den virtuella datorn innan du installerar tillägget Azure AD login VM.

Det tar några minuter att skapa den virtuella datorn och stödresurser.

Slutligen installerar du tillägget Azure AD login VM för att aktivera Azure AD-inloggning för Windows VM. VM-tillägg är små program som tillhandahåller konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Använd [AZ VM Extension](/cli/azure/vm/extension#az-vm-extension-set) set för att installera AADLoginForWindows-tillägget på den virtuella datorn med namnet `myVM` i `myResourceGroup` resurs gruppen:

> [!NOTE]
> Du kan installera AADLoginForWindows-tillägget på en befintlig Windows Server 2019 eller Windows 10 1809 och senare VM för att aktivera det för Azure AD-autentisering. Ett exempel på AZ CLI visas nedan.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState` `Succeeded` Visas när tillägget har installerats på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera roll tilldelningar för den virtuella datorn

Nu när du har skapat den virtuella datorn måste du konfigurera en Azure RBAC-princip för att fastställa vem som kan logga in på den virtuella datorn. Två Azure-roller används för att auktorisera VM-inloggning:

- **Administratörs inloggning för virtuell dator**: användare med den här rollen som tilldelas kan logga in på en virtuell Azure-dator med administratörs behörighet.
- **Användar inloggning för virtuell dator**: användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med vanliga användar behörigheter.

> [!NOTE]
> Om du vill att en användare ska kunna logga in på den virtuella datorn via RDP måste du tilldela antingen rollen Administratörs inloggning för virtuell dator eller användar inloggning för virtuell dator. En Azure-användare med rollen ägare eller deltagare som har tilldelats en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via RDP. Detta är att tillhandahålla granskad åtskillnad mellan den uppsättning personer som kontrollerar virtuella datorer och den uppsättning personer som kan komma åt virtuella datorer.

Det finns flera sätt att konfigurera roll tilldelningar för virtuella datorer:

- Använda Azure AD Portal-upplevelsen
- Använda Azure Cloud Shell upplevelse

> [!NOTE]
> Den virtuella datorns Administratörs inloggning och användar inloggnings roller för virtuell dator använder dataActions och kan därför inte tilldelas i hanterings gruppens omfattning. För närvarande kan de här rollerna bara tilldelas till prenumerationen, resurs gruppen eller resurs omfånget.

### <a name="using-azure-ad-portal-experience"></a>Använda Azure AD Portal-upplevelse

Konfigurera roll tilldelningar för dina Azure AD-aktiverade Windows Server 2019 datacenter-virtuella datorer:

1. Gå till översikts sidan för den aktuella virtuella datorn
1. Välj **åtkomst kontroll (IAM)** från meny alternativen
1. Välj **Lägg till**, **Lägg till roll tilldelning** för att öppna fönstret Lägg till roll tilldelning.
1. I list rutan **roll** väljer du en roll som **Administratörs inloggning för virtuell** dator eller **användar inloggning för virtuell dator**.
1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i fältet **Välj** . Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan **Välj** om du vill söka i katalogen.
1. Välj **Spara** för att tilldela rollen.

Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

![Tilldela roller till användare som ska ha åtkomst till den virtuella datorn](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Använda Azure Cloud Shell upplevelse

I följande exempel används [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att tilldela den virtuella datorns administratörs inloggnings roll till den virtuella datorn för din aktuella Azure-användare. Användar namnet för ditt aktiva Azure-konto hämtas med [AZ-kontot show](/cli/azure/account#az-account-show), och omfånget ställs in på den virtuella datorn som skapades i ett föregående steg med [AZ VM show](/cli/azure/vm#az-vm-show). Omfattningen kan också tilldelas till en resurs grupp eller prenumerations nivå och normala behörigheter för Azure RBAC-arv gäller. Mer information finns i [Logga in på en virtuell Linux-dator i Azure med Azure Active Directory autentisering](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggnings användar domän inte matchar, måste du ange objekt-ID: t för ditt användar konto med `--assignee-object-id` , inte bara användar namnet för `--assignee` . Du kan hämta objekt-ID: t för ditt användar konto med [AZ AD User List](/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder Azure RBAC för att hantera åtkomst till dina Azure-prenumerations resurser finns i följande artiklar:

- [Tilldela Azure-roller med Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Tilldela Azure-roller med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Använda villkorlig åtkomst

Du kan tillämpa principer för villkorlig åtkomst, till exempel Multi-Factor Authentication eller användar inloggnings risker innan du auktoriserar åtkomsten till virtuella Windows-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa en princip för villkorlig åtkomst måste du markera appen "Azure Windows VM-inloggning" från modulen molnappar eller åtgärder tilldelning och sedan använda inloggnings risker som ett villkor och/eller kräva multifaktorautentisering som en bevilja åtkomst kontroll. 

> [!NOTE]
> Om du använder "Kräv Multi-Factor Authentication" som en beviljande åtkomst kontroll för att begära åtkomst till appen "Azure Windows VM-inloggning" måste du ange Multi-Factor Authentication-anspråk som en del av klienten som initierar RDP-sessionen till den virtuella Windows-datorn i Azure. Det enda sättet att åstadkomma detta på en Windows 10-klient är att använda Windows Hello för företag-PIN-kod eller bio metrisk autentisering med RDP-klienten. Stöd för bio metrisk autentisering har lagts till i RDP-klienten i Windows 10 version 1809. Fjärr skrivbord med Windows Hello för företag-autentisering är bara tillgängligt för distributioner som använder certifikat förtroende modell och som för närvarande inte är tillgängligt för nyckel förtroende modell.

> [!WARNING]
> Per användare aktiverat/Tvingad Azure AD-Multi-Factor Authentication stöds inte för VM-inloggning.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logga in med autentiseringsuppgifter för Azure AD till en virtuell Windows-dator

> [!IMPORTANT]
> Fjärr anslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som antingen är registrerade i Azure AD (minsta version som krävs är 20H1) eller Azure AD-ansluten eller hybrid Azure AD som är ansluten till **samma** katalog som den virtuella datorn. För RDP med Azure AD-autentiseringsuppgifter måste användaren dessutom tillhöra en av de två Azure-rollerna, logga in för virtuella datorer eller användar inloggning för virtuella datorer. Om du använder en Azure AD-registrerad Windows 10-dator måste du ange autentiseringsuppgifter i `AzureAD\UPN` formatet (till exempel `AzureAD\john@contoso.com` ). För närvarande går det inte att använda Azure-skydds för att logga in med hjälp av Azure Active Directory-autentisering med AADLoginForWindows-tillägget. endast direkt RDP stöds.

Logga in på din virtuella Windows Server 2019-dator med hjälp av Azure AD: 

1. Gå till översikts sidan för den virtuella dator som har Aktiver ATS med Azure AD-inloggning.
1. Välj **Anslut** för att öppna bladet Anslut till virtuell dator.
1. Välj **Hämta RDP-fil**.
1. Välj **Öppna** för att starta anslutning till fjärrskrivbord-klienten.
1. Välj **Anslut** för att starta dialog rutan Windows-inloggning.
1. Logga in med dina autentiseringsuppgifter för Azure AD.

Du är nu inloggad på den virtuella Windows Server 2019 Azure-datorn med roll behörigheter som tilldelade, till exempel VM-användare eller VM-administratör. 

> [!NOTE]
> Du kan spara. RDP-fil lokalt på datorn för att starta framtida fjärr skrivbords anslutningar till den virtuella datorn i stället för att behöva gå till översikts sidan för den virtuella datorn i Azure Portal och med alternativet Connect.

## <a name="troubleshoot"></a>Felsöka

### <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

AADLoginForWindows-tillägget måste kunna installeras för att den virtuella datorn ska kunna slutföra Azure AD Join-processen. Utför följande steg om det inte går att installera VM-tillägget korrekt.

1. RDP till den virtuella datorn med det lokala administratörs kontot och undersök `CommandExecution.log` filen under:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Om tillägget startas om efter det första felet sparas loggen med distributions felet som `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. Öppna en PowerShell-kommandotolk på den virtuella datorn och kontrol lera att dessa frågor mot den Instance Metadata Service-slutpunkt (IMDS) som körs på Azure-värden returnerar:

   | Kommando som ska köras | Förväntad utdata |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Korrigera information om den virtuella Azure-datorn |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Giltigt klient-ID som är associerat med Azure-prenumerationen |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Giltig åtkomsttoken utfärdat av Azure Active Directory för den hanterade identitet som har tilldelats den här virtuella datorn |

   > [!NOTE]
   > Åtkomsttoken kan avkodas med hjälp av ett verktyg som [calebb.net](http://calebb.net/). Verifiera att `appid` i åtkomsttoken matchar den hanterade identitet som tilldelats den virtuella datorn.

1. Se till att de nödvändiga slut punkterna är tillgängliga från den virtuella datorn med hjälp av kommando raden:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klient-ID som är associerat med Azure-prenumerationen.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Enhetens tillstånd kan visas genom att köra `dsregcmd /status` . Målet är för enhets tillstånd att visa som `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD Join-aktivitet registreras i logg boken under `User Device Registration\Admin` loggen.

Om AADLoginForWindows-tillägget Miss lyckas med en viss felkod kan du utföra följande steg:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: AADLoginForWindows-tillägget kan inte installeras med Terminal-felkoden "1007" och avslutnings kod:-2145648574.

Den här avslutnings koden översätts till `DSREG_E_MSI_TENANTID_UNAVAILABLE` eftersom tillägget inte kan skicka frågor till Azure AD-klientens information.

1. Kontrol lera att den virtuella Azure-datorn kan hämta TenantID från Instance Metadata Service.

   - RDP till den virtuella datorn som lokal administratör och kontrol lera att slut punkten returnerar giltigt klient-ID genom att köra det här kommandot från en upphöjd kommando rad på den virtuella datorn:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. Den virtuella dator administratören försöker installera AADLoginForWindows-tillägget, men en systemtilldelad hanterad identitet har inte aktiverat den virtuella datorn först. Gå till den virtuella datorns identitets blad. Från fliken systemtilldelad kontrollerar du att status är växlad till på.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: AADLoginForWindows-tillägget kan inte installeras med avslutnings koden:-2145648607

Den här avslutnings koden översätts till `DSREG_AUTOJOIN_DISC_FAILED` eftersom tillägget inte kan komma åt `https://enterpriseregistration.windows.net` slut punkten.

1. Verifiera att de nödvändiga slut punkterna är tillgängliga från den virtuella datorn med hjälp av kommando raden:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klient-ID som är associerat med Azure-prenumerationen. Om du behöver hitta klient-ID: t kan du hovra över ditt konto namn för att hämta katalog-ID eller välja **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Om något av kommandona Miss lyckas med "Det gick inte att matcha värden `<URL>` ", kan du prova att köra det här kommandot för att avgöra vilken DNS-server som används av den virtuella datorn.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersätt `<URL>` med de fullständigt kvalificerade domän namnen som används av slut punkterna, till exempel `login.microsoftonline.com` .

1. Sedan kan du se om du anger en offentlig DNS-server så att kommandot kan utföras:

   `nslookup <URL> 208.67.222.222`

1. Om det behövs ändrar du DNS-servern som är tilldelad den nätverks säkerhets grupp som den virtuella Azure-datorn tillhör.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: AADLoginForWindows-tillägget kan inte installeras med avslutnings koden: 51

Slutkod 51 översätts till "det här tillägget stöds inte på den virtuella datorns operativ system".

Vid en offentlig för hands version är AADLoginForWindows-tillägget endast avsett att installeras på Windows Server 2019 eller Windows 10 (version 1809 eller senare). Kontrol lera att Windows-versionen stöds. Om Windows-versionen inte stöds avinstallerar du VM-tillägget.

### <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggnings problem

Några vanliga fel när du försöker använda RDP med Azure AD-autentiseringsuppgifter inkluderar inga Azure-roller tilldelade, otillåten klient eller 2FA inloggnings metod krävs. Använd följande information för att åtgärda problemen.

Du kan visa enhets-och SSO-status genom att köra `dsregcmd /status` . Målet är för enhets tillstånd att visa som `AzureAdJoined : YES` och `SSO State` att visa `AzureAdPrt : YES` .

Dessutom registreras RDP-inloggning med Azure AD-konton i logg boken under `AAD\Operational` händelse loggarna.

#### <a name="azure-role-not-assigned"></a>Ingen Azure-roll har tilldelats

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Ditt konto har kon figurer ATS för att förhindra att du använder den här enheten. Kontakta system administratören om du vill ha mer information.

![Ditt konto har kon figurer ATS för att förhindra att du använder den här enheten.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Kontrol lera att du har [konfigurerat Azure RBAC-principer](../../virtual-machines/linux/login-using-aad.md) för den virtuella datorn som ger användaren antingen Administratörs inloggning för den virtuella datorn eller användar inloggnings rollen för den virtuella datorn:

> [!NOTE]
> Om du får problem med Azures roll tilldelningar läser du [Felsöka Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).
 
#### <a name="unauthorized-client"></a>Obehörig klient

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Autentiseringsuppgifterna fungerade inte.

![Autentiseringsuppgifterna fungerade inte](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Kontrol lera att den Windows 10-dator som du använder för att initiera fjärr skrivbords anslutningen är antingen en Azure AD-ansluten eller en hybrid Azure AD som är ansluten till samma Azure AD-katalog som den virtuella datorn är ansluten till. Mer information om enhets identitet finns i artikeln [Vad är en enhets identitet](./overview.md).

> [!NOTE]
> Windows 10 build-20H1 har lagt till stöd för en registrerad Azure AD-dator för att initiera RDP-anslutning till den virtuella datorn. När du använder en Azure AD-registrerad (inte Azure AD-ansluten eller hybrid Azure AD-ansluten) som RDP-klient för att initiera anslutningar till den virtuella datorn, måste du ange autentiseringsuppgifter i formatet `AzureAD\UPN` (till exempel `AzureAD\john@contoso.com` ).

Kontrol lera att AADLoginForWindows-tillägget inte avinstallerades efter att Azure AD Join har slutförts.

Kontrol lera också att säkerhets principen "nätverks säkerhet: Tillåt att PKU2U autentiseringsbegäranden till den här datorn använder" online-identiteter "är aktive rad på både servern **och** klienten.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-inloggnings metod krävs

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Den inloggnings metod som du försöker använda är inte tillåten. Försök med en annan inloggnings metod eller kontakta system administratören.

![Den inloggnings metod som du försöker använda är inte tillåten.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Om du har konfigurerat en princip för villkorlig åtkomst som kräver Multi-Factor Authentication (MFA) innan du kan komma åt resursen, måste du se till att Windows 10-datorn som initierar fjärr skrivbords anslutningen till den virtuella datorn loggar in med en stark autentiseringsmetod, till exempel Windows Hello. Om du inte använder en stark autentiseringsmetod för fjärr skrivbords anslutningen visas föregående fel.

Om du inte har distribuerat Windows Hello för företag och om det inte är ett alternativ för tillfället, kan du utesluta MFA-kravet genom att konfigurera principen för villkorlig åtkomst som utesluter "Azure Windows VM-inloggning"-appen från listan över molnappar som kräver MFA. Mer information om Windows Hello för företag finns i [Översikt över Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Windows Hello för företag PIN-autentisering med RDP har stöd av Windows 10 för flera versioner, men stöd för bio metrisk autentisering med RDP lades till i Windows 10 version 1809. Användning av Windows Hello för företag-autentisering under RDP är endast tillgängligt för distributioner som använder certifikat förtroende modell och som för närvarande inte är tillgängligt för nyckel förtroende modell.
 
## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här för hands versions funktionen eller rapportera problem med hjälp av den i [Azure AD feedback-forumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](../fundamentals/active-directory-whatis.md).
