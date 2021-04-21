---
title: Logga in på en virtuell Linux-dator med Azure Active Directory autentiseringsuppgifter
description: Lär dig hur du skapar och konfigurerar en virtuell Linux-dator för att logga in Azure Active Directory autentisering.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 654d47102685c04d6440d7c155e4d6eb931abcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788123"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Förhandsversion: Logga in på en virtuell Linux-dator i Azure med hjälp Azure Active Directory autentisering

För att förbättra säkerheten för virtuella Linux-datorer (VM) i Azure kan du integrera med Azure Active Directory (AD)-autentisering. När du använder Azure AD-autentisering för virtuella Linux-datorer styr och tillämpar du principer centralt som tillåter eller nekar åtkomst till de virtuella datorerna. Den här artikeln visar hur du skapar och konfigurerar en virtuell Linux-dator för att använda Azure AD-autentisering.


> [!IMPORTANT]
> Azure Active Directory autentisering är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Använd den här funktionen på en virtuell testdator som du förväntar dig att ta bort efter testningen.
>


Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Linux-datorer i Azure, inklusive:

- **Förbättrad säkerhet:**
  - Du kan använda företagets AD-autentiseringsuppgifter för att logga in på virtuella Linux-datorer i Azure. Du behöver inte skapa lokala administratörskonton och hantera livslängden för autentiseringsuppgifter.
  - Genom att minska beroendet av lokala administratörskonton behöver du inte bekymra dig om förlust/stöld av autentiseringsuppgifter, användare som konfigurerar svaga autentiseringsuppgifter osv.
  - Lösenordskomplexiteten och livslängdsprinciperna för lösenord som konfigurerats för din Azure AD-katalog hjälper även till att skydda virtuella Linux-datorer.
  - För att ytterligare skydda inloggningen till virtuella Azure-datorer kan du konfigurera multifaktorautentisering.
  - Möjligheten att logga in på virtuella Linux-datorer med Azure Active Directory fungerar även för kunder som använder [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Sömlöst samarbete:** Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du ange vem som kan logga in på en viss virtuell dator som en vanlig användare eller med administratörsbehörighet. När användare går med i eller lämnar ditt team kan du uppdatera Azure RBAC-principen för den virtuella datorn för att bevilja åtkomst efter behov. Den här upplevelsen är mycket enklare än att behöva rensa virtuella datorer för att ta bort onödiga offentliga SSH-nycklar. När anställda lämnar din organisation och deras användarkonto inaktiveras eller tas bort från Azure AD har de inte längre åtkomst till dina resurser.

## <a name="supported-azure-regions-and-linux-distributions"></a>Azure-regioner och Linux-distributioner som stöds

Följande Linux-distributioner stöds för närvarande i förhandsversionen av den här funktionen:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 och Ubuntu Server 18.04 |


Följande Azure-regioner stöds för närvarande i förhandsversionen av den här funktionen:

- Alla globala Azure-regioner

>[!IMPORTANT]
> Om du vill använda den här förhandsversionsfunktionen distribuerar du endast en Linux-distribution som stöds och i en Azure-region som stöds. Funktionen stöds inte i Azure Government eller nationella moln.
>
> Det går inte att använda det här tillägget på Azure Kubernetes Service(AKS)-kluster. Mer information finns i [Supportprinciper för AKS.](../../aks/support-policies.md)


Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Linux-datorer i Azure måste du se till att nätverkskonfigurationen för dina virtuella datorer tillåter utgående åtkomst till följande slutpunkter via TCP-port 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /device.login.microsoftonline.com
* https: \/ /pas.windows.net
* https:\//management.azure.com
* https: \/ /packages.microsoft.com

> [!NOTE]
> För närvarande går det inte att konfigurera Azure-nätverkssäkerhetsgrupper för virtuella datorer som har aktiverats med Azure AD-autentisering.

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create)och skapa sedan en virtuell dator med az vm [create](/cli/azure/vm#az_vm_create) med hjälp av en distribution som stöds och i en region som stöds. I följande exempel distribueras en virtuell dator med namnet *myVM* som använder *Ubuntu 16.04 LTS* till en resursgrupp med namnet *myResourceGroup* i *regionen southcentralus.* I följande exempel kan du ange dina egna resursgrupps- och VM-namn efter behov.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser.

## <a name="install-the-azure-ad-login-vm-extension"></a>Installera azure AD-tillägget för inloggning till virtuell dator

> [!NOTE]
> Om du distribuerar det här tillägget till en tidigare skapad virtuell dator ser du till att datorn har minst 1 GB allokerat minne, annars kommer tillägget inte att installeras

Om du vill logga in på en virtuell Linux-dator med Azure AD-autentiseringsuppgifter installerar Azure Active Directory vm-tillägget för inloggning. VM-tillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer. Använd [az vm extension set för](/cli/azure/vm/extension#az_vm_extension_set) att installera tillägget *AADLoginForLinux* på den virtuella datorn med namnet *myVM* i *resursgruppen myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState för* *Succeeded (Lyckades)* visas när tillägget har installerats på den virtuella datorn. Den virtuella datorn behöver en VM-agent som körs för att installera tillägget. Mer information finns i Översikt [över VM-agenten.](../extensions/agent-windows.md)

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Azures princip för rollbaserad åtkomstkontroll (Azure RBAC) avgör vem som kan logga in på den virtuella datorn. Två Azure-roller används för att auktorisera vm-inloggning:

- **Inloggning för administratör för virtuell** dator: Användare med den här rollen kan logga in på en virtuell Azure-dator med Windows-administratörs- eller Linux-rotanvändarbehörighet.
- **Användarinloggning för virtuell** dator: Användare med den här rollen kan logga in på en virtuell Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill tillåta att en användare loggar in på  den virtuella datorn via SSH måste du tilldela antingen rollen Inloggning för administratör för virtuell dator eller *Användarinloggning för virtuell* dator. Rollerna Inloggning för administratör för virtuell dator och Inloggning för virtuell dator använder dataÅtgärder och kan därför inte tilldelas i hanteringsgruppomfånget. För närvarande kan dessa roller endast tilldelas för prenumerationen, resursgruppen eller resursomfånget. En Azure-användare med  rollen *Ägare* eller Deltagare tilldelad för en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via SSH. 

I följande exempel används [az role assignment create för](/cli/azure/role/assignment#az_role_assignment_create) att tilldela rollen *Administratör* för virtuell dator till den virtuella datorn för din aktuella Azure-användare. Användarnamnet för ditt aktiva Azure-konto hämtas med  [az account show](/cli/azure/account#az_account_show), och omfånget anges till den virtuella datorn som skapades i ett tidigare steg med az [vm show](/cli/azure/vm#az_vm_show). Omfånget kan också tilldelas på resursgrupps- eller prenumerationsnivå, och vanliga Azure RBAC-arvsbehörigheter gäller. Mer information finns i [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggningsanvändardomän inte matchar måste du ange objekt-ID för ditt användarkonto med *--assignee-object-id*, inte bara användarnamnet för *--assignee*. Du kan hämta objekt-ID:t för ditt användarkonto med [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Mer information om hur du använder Azure RBAC för att hantera åtkomst till dina Azure-prenumerationsresurser finns i använda [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [Azure Portal](../../role-based-access-control/role-assignments-portal.md) [eller Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Använda villkorsstyrd åtkomst

Du kan tillämpa principer för villkorsstyrd åtkomst, till exempel multifaktorautentisering eller riskkontroll för användar inloggning innan du auktoriserar åtkomst till virtuella Linux-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa principen för villkorlig åtkomst måste du välja inloggningsappen "Microsoft Azure Virtuell Linux-dator" från alternativet för tilldelning av molnappar eller åtgärder och sedan använda inloggningsrisk som ett villkor och/eller kräva multifaktorautentisering som en beviljande åtkomstkontroll. 

> [!WARNING]
> Per användare Aktiverad/Framtvingad Azure AD Multi-Factor Authentication stöds inte för vm-inloggning.

## <a name="log-in-to-the-linux-virtual-machine"></a>Logga in på den virtuella Linux-datorn

Visa först den offentliga IP-adressen för den virtuella datorn med [az vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Logga in på den virtuella Azure Linux-datorn med dina autentiseringsuppgifter för Azure AD. Med `-l` parametern kan du ange din egen Azure AD-kontoadress. Ersätt exempelkontot med ditt eget. Kontoadresser ska anges med gemener. Ersätt IP-exempeladressen med den offentliga IP-adressen för den virtuella datorn från föregående kommando.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Du uppmanas att logga in på Azure AD med en kod för användning en gång på [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Kopiera och klistra in användningskoden en gång på enhetens inloggningssida.

När du uppmanas till det anger du dina autentiseringsuppgifter för Azure AD-inloggning på inloggningssidan. 

Följande meddelande visas i webbläsaren när du har autentiserats: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Stäng webbläsarfönstret, gå tillbaka till SSH-prompten och tryck på **Retur.** 

Du är nu inloggad på den virtuella Azure Linux-datorn med rollbehörigheter som tilldelade, till exempel *VM-användare* eller *VM-administratör.* Om ditt användarkonto har tilldelats *inloggningsrollen Administratör* för virtuell dator kan du använda för `sudo` att köra kommandon som kräver rotbehörighet.

## <a name="sudo-and-aad-login"></a>Sudo- och AAD-inloggning

Första gången du kör sudo uppmanas du att autentisera en andra gång. Om du inte vill behöva autentisera igen för att köra sudo kan du redigera sudoers-filen `/etc/sudoers.d/aad_admins` och ersätta den här raden:

```bash
%aad_admins ALL=(ALL) ALL
```

Med den här raden:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggningsproblem

Några vanliga fel när du försöker använda SSH med Azure AD-autentiseringsuppgifter omfattar inga tilldelade Azure-roller och upprepade uppmaningar om att logga in. Använd följande avsnitt för att åtgärda dessa problem.

### <a name="access-denied-azure-role-not-assigned"></a>Åtkomst nekad: Azure-rollen har inte tilldelats

Om du ser följande fel i SSH-prompten kontrollerar du att du har konfigurerat Azure  RBAC-principer för den virtuella datorn som ger användaren antingen rollen Administratör för virtuell dator eller Inloggning för virtuell *dator:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Om du får problem med Azure-rolltilldelningar kan du gå till [Felsöka Azure RBAC.](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)

### <a name="continued-ssh-sign-in-prompts"></a>Anvisningarna för fortsatt SSH-inloggning

Om du har slutfört autentiseringssteget i en webbläsare kan du omedelbart uppmanas att logga in igen med en ny kod. Det här felet beror vanligtvis på ett matchningsfel mellan det inloggningsnamn som du angav i SSH-prompten och det konto som du loggade in på Azure AD med. Så här korrigerar du problemet:

- Kontrollera att inloggningsnamnet som du angav i SSH-prompten är korrekt. Ett skrivfel i inloggningsnamnet kan orsaka ett matchningsfel mellan inloggningsnamnet som du angav i SSH-prompten och det konto som du loggade in på Azure AD med. Du kan till exempel skriva *azuresuer \@ contoso.onmicrosoft.com* i stället *för azureuser \@ contoso.onmicrosoft.com*.
- Om du har flera användarkonton kontrollerar du att du inte anger ett annat användarkonto i webbläsarfönstret när du loggar in på Azure AD.
- Linux är ett ärendekänsligt operativsystem. Det finns en skillnad mellan Azureuser@contoso.onmicrosoft.com " " och azureuser@contoso.onmicrosoft.com ", vilket kan orsaka ett matchningsfel. Se till att du anger UPN med rätt fallkänslighet i SSH-prompten.

### <a name="other-limitations"></a>Andra begränsningar

Användare som ärver åtkomstbehörigheter via kapslade grupper eller rolltilldelningar stöds inte för närvarande. Användaren eller gruppen måste tilldelas de [rolltilldelningar som krävs direkt.](#configure-role-assignments-for-the-vm) Användning av hanteringsgrupper eller kapslade grupprolltilldelningar beviljar till exempel inte rätt behörigheter för att tillåta användaren att logga in.

## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här förhandsgranskningsfunktionen eller rapportera problem med att använda den i [Azure AD-feedbackforumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
