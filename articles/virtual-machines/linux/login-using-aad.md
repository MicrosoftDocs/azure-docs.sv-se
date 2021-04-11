---
title: Logga in på en virtuell Linux-dator med Azure Active Directory autentiseringsuppgifter
description: Lär dig hur du skapar och konfigurerar en virtuell Linux-dator för att logga in med Azure Active Directory autentisering.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: e14e214a220d9dade4fac028620d23c563d86a8f
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554084"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>För hands version: Logga in på en virtuell Linux-dator i Azure med Azure Active Directory autentisering

För att förbättra säkerheten för virtuella Linux-datorer i Azure kan du integrera med autentisering med Azure Active Directory (AD). När du använder Azure AD-autentisering för virtuella Linux-datorer kan du centralt styra och tillämpa principer som tillåter eller nekar åtkomst till de virtuella datorerna. Den här artikeln visar hur du skapar och konfigurerar en virtuell Linux-dator för att använda Azure AD-autentisering.


> [!IMPORTANT]
> Azure Active Directory autentisering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Använd den här funktionen på en virtuell test dator som du förväntar dig att ignorera efter testning.
>


Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Linux-datorer i Azure, inklusive:

- **Förbättrad säkerhet:**
  - Du kan använda företagets AD-autentiseringsuppgifter för att logga in på virtuella datorer i Azure Linux. Du behöver inte skapa lokala administratörs konton och hantera livstid för autentiseringsuppgifter.
  - Genom att minska beroendet av lokala administratörs konton behöver du inte oroa dig för förlust/stöld av autentiseringsuppgifter, användare som konfigurerar svaga autentiseringsuppgifter osv.
  - Principerna för lösen ords komplexitet och lösen ord är konfigurerade för Azure AD-katalogen och skyddar virtuella Linux-datorer.
  - Du kan konfigurera Multi-Factor Authentication för att ytterligare säkra inloggning till virtuella Azure-datorer.
  - Möjligheten att logga in på virtuella Linux-datorer med Azure Active Directory fungerar också för kunder som använder [Federations tjänster](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Sömlöst samarbete:** Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du ange vem som kan logga in på en specifik virtuell dator som en vanlig användare eller med administratörs behörighet. När användarna ansluter till eller lämnar ditt team kan du uppdatera Azure RBAC-principen för den virtuella datorn för att ge åtkomst vid behov. Den här upplevelsen är mycket enklare än att behöva radera virtuella datorer för att ta bort onödiga offentliga SSH-nycklar. När anställda lämnar din organisation och deras användar konto har inaktiverats eller tagits bort från Azure AD har de inte längre till gång till dina resurser.

## <a name="supported-azure-regions-and-linux-distributions"></a>Azure-regioner och Linux-distributioner som stöds

Följande Linux-distributioner stöds för närvarande i för hands versionen av den här funktionen:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE skottår 42,3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 och Ubuntu Server 18,04 |


Följande Azure-regioner stöds för närvarande i för hands versionen av den här funktionen:

- Alla globala Azure-regioner

>[!IMPORTANT]
> Om du vill använda den här förhands granskningen distribuerar du bara en Linux-distribution som stöds och i en Azure-region som stöds Funktionen stöds inte i Azure Government eller suveräna moln.
>
> Det går inte att använda det här tillägget på Azure Kubernetes service-kluster (AKS). Mer information finns i [support policys för AKS](../../aks/support-policies.md).


Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Linux-datorer i Azure måste du se till att nätverks konfigurationen för virtuella datorer tillåter utgående åtkomst till följande slut punkter via TCP-port 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /Device.login.microsoftonline.com
* https: \/ /Pas.Windows.net
* https:\//management.azure.com
* https: \/ /packages.Microsoft.com

> [!NOTE]
> För närvarande går det inte att konfigurera Azures nätverks säkerhets grupper för virtuella datorer som är aktiverade med Azure AD-autentisering.

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az-group-create)och skapa sedan en virtuell dator med [AZ VM Create](/cli/azure/vm#az-vm-create) med en distribution som stöds och i en region som stöds. I följande exempel distribueras en virtuell dator med namnet *myVM* som använder *Ubuntu 16,04-LTS* i en resurs grupp med namnet *myResourceGroup* i regionen *usasödracentrala* . I följande exempel kan du ange dina egna resurs grupper och namn på virtuella datorer efter behov.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Installera tillägget Azure AD login VM

> [!NOTE]
> Om du distribuerar det här tillägget till en tidigare skapad virtuell dator måste datorn ha minst 1 GB minne, annars går det inte att installera tillägget

Om du vill logga in på en virtuell Linux-dator med Azure AD-autentiseringsuppgifter installerar du Azure Active Directory-inloggningens VM-tillägg. VM-tillägg är små program som tillhandahåller konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Använd [AZ VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) för att installera *AADLoginForLinux* -tillägget på den virtuella datorn med namnet *myVM* i *myResourceGroup* -resurs gruppen:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* av *lyckades* visas när tillägget har installerats på den virtuella datorn. Den virtuella datorn behöver en virtuell dator agent som körs för att installera tillägget. Mer information finns i [Översikt över VM-agenten](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera roll tilldelningar för den virtuella datorn

Azures rollbaserade åtkomst kontroll (Azure RBAC) policy avgör vem som kan logga in på den virtuella datorn. Två Azure-roller används för att auktorisera VM-inloggning:

- **Administratörs inloggning för virtuell dator**: användare med den här rollen som tilldelas kan logga in på en virtuell Azure-dator med Windows-administratör eller Linux-rot användar privilegier.
- **Användar inloggning för virtuell dator**: användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med vanliga användar behörigheter.

> [!NOTE]
> Om du vill tillåta att en användare loggar in på den virtuella datorn via SSH måste du tilldela antingen rollen *Administratörs inloggning för virtuell dator* eller *användar inloggning för virtuell dator* . Den virtuella datorns Administratörs inloggning och användar inloggnings roller för virtuell dator använder dataActions och kan därför inte tilldelas i hanterings gruppens omfattning. För närvarande kan de här rollerna bara tilldelas till prenumerationen, resurs gruppen eller resurs omfånget. En Azure-användare med rollen *ägare* eller *deltagare* som har tilldelats en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via SSH. 

I följande exempel används [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att tilldela den *virtuella datorns administratörs inloggnings* roll till den virtuella datorn för din aktuella Azure-användare. Användar namnet för ditt aktiva Azure-konto hämtas med [AZ-kontot show](/cli/azure/account#az-account-show), och *omfånget* ställs in på den virtuella datorn som skapades i ett föregående steg med [AZ VM show](/cli/azure/vm#az-vm-show). Omfattningen kan också tilldelas till en resurs grupp eller prenumerations nivå och normala behörigheter för Azure RBAC-arv gäller. Mer information finns i [Azure RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggnings användar domän inte matchar, måste du ange objekt-ID: t för ditt användar konto med namnet *--* tilldelad-Object-ID, inte bara användar namnet för *--tilldelas*. Du kan hämta objekt-ID: t för ditt användar konto med [AZ AD User List](/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder Azure RBAC för att hantera åtkomst till dina Azure-prenumerations resurser finns i använda [Azure CLI](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)eller [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Använda villkorlig åtkomst

Du kan tillämpa principer för villkorlig åtkomst, till exempel Multi-Factor Authentication eller användar inloggnings risker innan du auktoriserar åtkomsten till virtuella Linux-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa principen för villkorlig åtkomst måste du välja "Microsoft Azure Linux Virtual Machine Sign-in"-appen från molnbaserad Apps eller åtgärder tilldelnings alternativ och sedan använda inloggnings risker som ett villkor och/eller kräva Multi-Factor Authentication som en bevilja åtkomst kontroll. 

> [!WARNING]
> Per användare aktiverat/Tvingad Azure AD-Multi-Factor Authentication stöds inte för VM-inloggning.

## <a name="log-in-to-the-linux-virtual-machine"></a>Logga in på den virtuella Linux-datorn

Först visar du den offentliga IP-adressen för den virtuella datorn med [AZ VM show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Logga in på den virtuella Azure Linux-datorn med dina autentiseringsuppgifter för Azure AD. Med den `-l` här parametern kan du ange din egen Azure AD-kontoinformation. Ersätt exempel kontot med ditt eget. Konto adresser måste anges i gemener. Ersätt IP-exemplet med den offentliga IP-adressen för den virtuella datorn från föregående kommando.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Du uppmanas att logga in på Azure AD med en engångs kod på [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Kopiera och klistra in koden för att använda koden på enhetens inloggnings sida.

När du uppmanas till det anger du dina inloggnings uppgifter för Azure AD på inloggnings sidan. 

Följande meddelande visas i webbläsaren när du har autentiserats: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Stäng webbläsarfönstret, gå tillbaka till SSH-prompten och tryck på **RETUR** -tangenten. 

Du är nu inloggad på den virtuella Azure Linux-datorn med roll behörigheter som tilldelade, till exempel *VM-användare* eller VM- *administratör*. Om ditt användar konto har tilldelats *inloggnings rollen administratör för virtuell dator* kan du använda `sudo` för att köra kommandon som kräver rot privilegier.

## <a name="sudo-and-aad-login"></a>Sudo och AAD-inloggning

Första gången du kör sudo blir du ombedd att autentisera en andra gång. Om du inte vill att autentiseringen ska köras igen för att köra sudo, kan du redigera sudoers-filen `/etc/sudoers.d/aad_admins` och ersätta den här raden:

```bash
%aad_admins ALL=(ALL) ALL
```

Med den här raden:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggnings problem

Några vanliga fel när du försöker använda SSH med Azure AD-autentiseringsuppgifter inkluderar inga Azure-roller tilldelade och du får upprepade frågor om att logga in. Använd följande avsnitt för att åtgärda problemen.

### <a name="access-denied-azure-role-not-assigned"></a>Åtkomst nekad: Azure-rollen har inte tilldelats

Om du ser följande fel i SSH-prompten kontrollerar du att du har konfigurerat Azure RBAC-principer för den virtuella datorn som beviljar användaren antingen *Administratörs inloggning för den virtuella datorn* eller *användar inloggnings* rollen för den virtuella datorn:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Om du får problem med Azures roll tilldelningar läser du [Felsöka Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="continued-ssh-sign-in-prompts"></a>Kontinuerliga inloggnings meddelanden i SSH

Om du har slutfört Authentication-steget i en webbläsare kan du omedelbart uppmanas att logga in igen med en ny kod. Det här felet orsakas vanligt vis av ett matchnings fel mellan det inloggnings namn som du angav i SSH-prompten och det konto som du loggade in på Azure AD med. Så här löser du det här problemet:

- Kontrol lera att det inloggnings namn som du angav i SSH-prompten är korrekt. Ett skrivfel i inloggnings namnet kan orsaka ett matchnings fel mellan det inloggnings namn som du angav i SSH-prompten och det konto som du loggade in på Azure AD med. Du skrev till exempel *azuresuer \@ contoso.onmicrosoft.com* i stället för *azureuser \@ contoso.onmicrosoft.com*.
- Om du har flera användar konton ser du till att du inte anger något annat användar konto i webbläsarfönstret när du loggar in på Azure AD.
- Linux är ett skift läges känsligt operativ system. Det finns en skillnad mellan " Azureuser@contoso.onmicrosoft.com " och " azureuser@contoso.onmicrosoft.com ", vilket kan orsaka ett matchnings fel. Se till att du anger UPN med rätt Skift läges känslighet i SSH-prompten.

### <a name="other-limitations"></a>Andra begränsningar

Användare som ärver åtkomst rättigheter via kapslade grupper eller roll tilldelningar stöds inte för närvarande. Användaren eller gruppen måste tilldelas direkt de [nödvändiga roll tilldelningarna](#configure-role-assignments-for-the-vm). Till exempel ger användning av hanterings grupper eller kapslade grupp roll tilldelningar inte rätt behörighet för att tillåta användaren att logga in.

## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här för hands versions funktionen eller rapportera problem med hjälp av den i [Azure AD feedback-forumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
