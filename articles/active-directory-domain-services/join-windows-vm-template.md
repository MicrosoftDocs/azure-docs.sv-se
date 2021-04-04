---
title: Använda en mall för att ansluta en virtuell Windows-dator till Azure AD DS | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager mallar för att ansluta en ny eller befintlig virtuell Windows Server-dator till en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619444"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Ansluta en virtuell Windows Server-dator till en Azure Active Directory Domain Services hanterad domän med hjälp av en Resource Manager-mall

Om du vill automatisera distributionen och konfigurationen av virtuella datorer i Azure kan du använda en Resource Manager-mall. Med dessa mallar kan du skapa konsekventa distributioner varje tillfälle. Tillägg kan också inkluderas i mallar för att automatiskt konfigurera en virtuell dator som en del av distributionen. Ett användbart tillägg ansluter virtuella datorer till en domän som kan användas med Azure Active Directory Domain Services (Azure AD DS) hanterade domäner.

Den här artikeln visar hur du skapar och ansluter en virtuell Windows Server-dator till en Azure AD DS-hanterad domän med hjälp av Resource Manager-mallar. Du lär dig också hur du ansluter en befintlig virtuell Windows Server-dator till en Azure AD DS-domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett användar konto som är en del av den hanterade domänen.

## <a name="azure-resource-manager-template-overview"></a>Översikt över Azure Resource Manager mall

Med Resource Manager-mallar kan du definiera Azure-infrastruktur i kod. De resurser, nätverks anslutningar eller konfigurationer som krävs för virtuella datorer kan alla definieras i en mall. Dessa mallar skapar konsekventa, reproducerbara distributioner varje tillfälle och kan installeras när du gör ändringar. Mer information finns i [Översikt över Azure Resource Manager mallar][template-overview].

Varje resurs definieras i en mall med hjälp av JavaScript Object Notation (JSON). Följande JSON-exempel använder resurs typen *Microsoft. Compute/virtualMachines/Extensions* för att installera Active Directory domän anslutnings tillägget. Parametrar används som du anger vid distributions tiden. När tillägget distribueras är den virtuella datorn ansluten till den angivna hanterade domänen.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Detta VM-tillägg kan distribueras även om du inte skapar en virtuell dator i samma mall. I exemplen i den här artikeln visas båda följande metoder:

* [Skapa en virtuell Windows Server-dator och ansluta till en hanterad domän](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Anslut en befintlig virtuell Windows Server-dator till en hanterad domän](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Skapa en virtuell Windows Server-dator och ansluta till en hanterad domän

Om du behöver en virtuell Windows Server-dator kan du skapa och konfigurera en med hjälp av en Resource Manager-mall. När den virtuella datorn distribueras installeras ett tillägg för att ansluta den virtuella datorn till en hanterad domän. Om du redan har en virtuell dator som du vill ansluta till en hanterad domän, hoppar du över att [ansluta en befintlig virtuell Windows Server-dator till en hanterad domän](#join-an-existing-windows-server-vm-to-a-managed-domain).

Om du vill skapa en virtuell Windows Server-dator ansluter du den till en hanterad domän och utför följande steg:

1. Bläddra till [snabb starts mal len](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Välj alternativet för att **distribuera till Azure**.
1. På sidan **Anpassad distribution** anger du följande information för att skapa och ansluta en virtuell Windows Server-dator till den hanterade domänen:

    | Inställning                   | Värde |
    |---------------------------|-------|
    | Prenumeration              | Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services. |
    | Resursgrupp            | Välj resurs grupp för den virtuella datorn. |
    | Location                  | Välj platsen för den virtuella datorn. |
    | Befintligt VNET-namn        | Namnet på det befintliga virtuella nätverket för att ansluta den virtuella datorn till, till exempel *myVnet*. |
    | Befintligt under näts namn      | Namnet på det befintliga under nätet för virtuella nätverk, till exempel *arbets belastningar*. |
    | DNS-etikett-prefix          | Ange ett DNS-namn som ska användas för den virtuella datorn, till exempel *myvm*. |
    | Storlek på virtuell dator                   | Ange en storlek på virtuell dator, t. ex. *Standard_DS2_v2*. |
    | Domän att ansluta till            | DNS-namnet för den hanterade domänen, till exempel *aaddscontoso.com*. |
    | Domän användar namn           | Användar kontot i den hanterade domänen som ska användas för att ansluta den virtuella datorn till den hanterade domänen, till exempel `contosoadmin@aaddscontoso.com` . Det här kontot måste vara en del av den hanterade domänen. |
    | Domän lösen ord           | Lösen ordet för det användar konto som anges i föregående inställning. |
    | Valfri OU-sökväg          | Den anpassade ORGANISATIONSENHETen där den virtuella datorn ska läggas till. Om du inte anger ett värde för den här parametern läggs den virtuella datorn till i ou för *AAD DC-datorer* . |
    | Användar namn för administratör för virtuell dator         | Ange ett lokalt administratörs konto som ska skapas på den virtuella datorn. |
    | Administratörs lösen ord för virtuell dator         | Ange ett lokalt administratörs lösen ord för den virtuella datorn. Skapa ett starkt lokalt administratörs lösen ord för att skydda mot lösen ords bruten angrepp. |

1. Granska villkoren och markera sedan kryss rutan för **Jag accepterar villkoren ovan**. När du är klar väljer du **köp** för att skapa och ansluta den virtuella datorn till den hanterade domänen.

> [!WARNING]
> **Hantera lösen ord med försiktighet.**
> Mallens parameter fil begär lösen ordet för ett användar konto som är en del av den hanterade domänen. Ange inte värden manuellt i filen och lämna den tillgänglig på fil resurser eller andra delade platser.

Det tar några minuter för distributionen att slutföras. När du är färdig skapas en virtuell Windows-dator som är ansluten till den hanterade domänen. Den virtuella datorn kan hanteras eller loggas in i med domän konton.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Anslut en befintlig virtuell Windows Server-dator till en hanterad domän

Om du har en befintlig virtuell dator eller grupp med virtuella datorer som du vill ansluta till en hanterad domän, kan du använda en Resource Manager-mall för att bara distribuera VM-tillägget.

Slutför följande steg för att ansluta en befintlig virtuell Windows Server-dator till en hanterad domän:

1. Bläddra till [snabb starts mal len](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Välj alternativet för att **distribuera till Azure**.
1. På sidan **Anpassad distribution** anger du följande information för att ansluta den virtuella datorn till den hanterade domänen:

    | Inställning                   | Värde |
    |---------------------------|-------|
    | Prenumeration              | Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services. |
    | Resursgrupp            | Välj resurs gruppen med din befintliga virtuella dator. |
    | Location                  | Välj plats för den befintliga virtuella datorn. |
    | VM-lista                   | Ange den kommaavgränsade listan över de befintliga virtuella datorerna för att ansluta till den hanterade domänen, till exempel *myVM1, myVM2*. |
    | Användar namn för domän anslutning     | Användar kontot i den hanterade domänen som ska användas för att ansluta den virtuella datorn till den hanterade domänen, till exempel `contosoadmin@aaddscontoso.com` . Det här kontot måste vara en del av den hanterade domänen. |
    | Användar lösen ord för domän anslutning | Lösen ordet för det användar konto som anges i föregående inställning. |
    | Valfri OU-sökväg          | Den anpassade ORGANISATIONSENHETen där den virtuella datorn ska läggas till. Om du inte anger ett värde för den här parametern läggs den virtuella datorn till i ou för *AAD DC-datorer* . |

1. Granska villkoren och markera sedan kryss rutan för **Jag accepterar villkoren ovan**. När du är klar väljer du **köp** för att ansluta den virtuella datorn till den hanterade domänen.

> [!WARNING]
> **Hantera lösen ord med försiktighet.**
> Mallens parameter fil begär lösen ordet för ett användar konto som är en del av den hanterade domänen. Ange inte värden manuellt i filen och lämna den tillgänglig på fil resurser eller andra delade platser.

Det tar en stund innan distributionen har slutförts. När du är färdig är de angivna virtuella Windows-datorerna anslutna till den hanterade domänen och kan hanteras eller loggas in i med domän konton.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde du Azure Portal för att konfigurera och distribuera resurser med hjälp av mallar. Du kan också distribuera resurser med Resource Manager-mallar med [Azure PowerShell][deploy-powershell] eller [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
