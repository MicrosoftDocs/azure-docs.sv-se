---
title: Aktivera Azure DS Domain Services med hjälp av en mall | Microsoft Docs
description: Lär dig hur du konfigurerar och aktiverar Azure Active Directory Domain Services att använda en Azure Resource Manager-mall
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618798"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Skapa en Azure Active Directory Domain Services hanterad domän med hjälp av en Azure Resource Manager mall

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Du använder dessa domän tjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klient. Med den här integreringen kan användarna logga in med sina företags uppgifter, och du kan använda befintliga grupper och användar konton för att skydda åtkomsten till resurser.

Den här artikeln visar hur du skapar en hanterad domän med hjälp av en Azure Resource Manager-mall. Stöd resurser skapas med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln behöver du följande resurser:

* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du anvisningarna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Kontrol lera att du loggar in på Azure-prenumerationen med hjälp av cmdleten [Connect-AzAccount][Connect-AzAccount] .
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du anvisningarna för att [Installera Azure AD PowerShell-modulen och ansluta till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Kontrol lera att du loggar in på Azure AD-klienten med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] .
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

## <a name="dns-naming-requirements"></a>Krav för DNS-namngivning

När du skapar en Azure AD DS-hanterad domän anger du ett DNS-namn. Det finns några saker att tänka på när du väljer det här DNS-namnet:

* **Inbyggt domän namn:** Som standard används det inbyggda domän namnet för katalogen (a *. onmicrosoft.com* suffix). Om du vill aktivera säker LDAP-åtkomst till den hanterade domänen via Internet kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standard domänen. Microsoft äger *onmicrosoft.com* -domänen så att en certifikat utfärdare (ca) inte utfärdar ett certifikat.
* **Anpassade domän namn:** Det vanligaste tillvägagångs sättet är att ange ett anpassat domän namn, vanligt vis ett som du redan äger och som är dirigerbart. När du använder en dirigerbart, anpassad domän kan trafik flöda korrekt efter behov för att stödja dina program.
* **Icke-dirigerbart domänsuffix:** Vi rekommenderar vanligt vis att du undviker ett icke-dirigerbart domänsuffix, t. ex. *contoso. local*. *Lokalt* suffix kan inte dirigeras och kan orsaka problem med DNS-matchning.

> [!TIP]
> Om du skapar ett eget domän namn bör du ta hand om befintliga DNS-namnområden. Vi rekommenderar att du använder ett domän namn separat från ett befintligt Azure eller lokalt DNS-adressutrymme.
>
> Om du till exempel har ett befintligt DNS-namnområdet *contoso.com* skapar du en hanterad domän med det anpassade domän namnet för *aaddscontoso.com*. Om du behöver använda säker LDAP måste du registrera och äga det här anpassade domän namnet för att generera nödvändiga certifikat.
>
> Du kan behöva skapa ytterligare DNS-poster för andra tjänster i din miljö, eller villkorliga DNS-vidarebefordrare mellan befintliga DNS-namn utrymmen i din miljö. Om du till exempel kör en webb server som är värd för en plats som använder rot-DNS-namnet, kan det finnas namn konflikter som kräver ytterligare DNS-poster.
>
> I det här exemplet och instruktions artiklar används den anpassade domänen för *aaddscontoso.com* som ett kort exempel. I alla kommandon anger du ditt eget domän namn.

Följande DNS-namn begränsningar gäller också:

* **Begränsningar för domänautentiseringsuppgifter:** Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken. Prefixet för det angivna domän namnet (t. ex. *aaddscontoso* i domän namnet *aaddscontoso.com* ) måste innehålla högst 15 tecken.
* **Nätverks namns konflikter:** DNS-domännamnet för den hanterade domänen bör inte redan finnas i det virtuella nätverket. Mer specifikt kan du söka efter följande scenarier som leder till en namn konflikt:
    * Om du redan har en Active Directory domän med samma DNS-domännamn i det virtuella Azure-nätverket.
    * Om det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning till ditt lokala nätverk. I det här scenariot ser du till att du inte har en domän med samma DNS-domännamn i det lokala nätverket.
    * Om du har en befintlig Azure-moln tjänst med det namnet i det virtuella Azure-nätverket.

## <a name="create-required-azure-ad-resources"></a>Skapa nödvändiga Azure AD-resurser

Azure AD DS kräver ett huvud namn för tjänsten och en Azure AD-grupp. Dessa resurser låter den hanterade domänen synkronisera data och definiera vilka användare som har administratörs behörighet i den hanterade domänen.

Registrera först Azure AD Domain Services Resource Provider med hjälp av cmdleten [register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Skapa ett Azure AD-tjänstens huvud namn med cmdleten [New-AzureADServicePrincipal][New-AzureADServicePrincipal] för Azure AD DS för att kommunicera och autentisera sig själv. Ett angivet program-ID används med namnet *domänkontrollant tjänster* med ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Ändra inte det här program-ID: t.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Skapa nu en Azure AD-grupp med namnet *AAD DC-administratörer* med cmdleten [New-AzureADGroup][New-AzureADGroup] . Användare som läggs till i den här gruppen beviljas sedan behörigheter för att utföra administrations uppgifter på den hanterade domänen.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Med *Administratörs gruppen för AAD-domänkontrollanten* skapad lägger du till en användare i gruppen med cmdleten [Add-AzureADGroupMember][Add-AzureADGroupMember] . Först får du ett objekt-ID för *AAD DC-administratörer* som använder cmdleten [Get-AzureADGroup][Get-AzureADGroup] och sedan den önskade användarens objekt-ID med hjälp av cmdleten [Get-AzureADUser][Get-AzureADUser] .

I följande exempel är användar objekt-ID: t för kontot med UPN för `admin@contoso.onmicrosoft.com` . Ersätt det här användar kontot med UPN för den användare som du vill lägga till i *Administratörs* gruppen för AAD-domänkontrollant:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Slutligen skapar du en resurs grupp med cmdleten [New-AzResourceGroup][New-AzResourceGroup] . I följande exempel heter resurs gruppen *myResourceGroup* och skapas i regionen *väst* . Använd ditt eget namn och önskad region:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Om du väljer en region som stöder Tillgänglighetszoner fördelas Azure AD DS-resurserna mellan zoner för ytterligare redundans. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

Det finns inget som du kan konfigurera för att Azure AD DS ska distribueras mellan zoner. Azure-plattformen hanterar automatiskt zon distributionen av resurser. Mer information och mer information om regions tillgänglighet finns i [Vad är Tillgänglighetszoner i Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Resurs definition för Azure AD DS

Som en del av resurs definitionen för Resource Manager krävs följande konfigurations parametrar:

| Parameter               | Värde |
|-------------------------|---------|
| Namn              | DNS-domännamnet för din hanterade domän, med hänsyn till föregående punkter om namngivning av prefix och konflikter. |
| filteredSync            | Med Azure AD DS kan du synkronisera *alla* användare och grupper som är tillgängliga i Azure AD, eller en *begränsad* synkronisering av enbart vissa grupper.<br /><br /> Mer information om omfångs synkronisering finns i [Azure AD Domain Services omfångs synkronisering][scoped-sync].|
| notificationSettings    | Om det finns aviseringar som genererats i den hanterade domänen kan e-postmeddelanden skickas ut. <br /><br />*Globala administratörer* av Azure-klienten och medlemmar i gruppen *AAD DC-administratörer* kan *aktive ras* för dessa aviseringar.<br /><br /> Om du vill kan du lägga till ytterligare mottagare för aviseringar när det finns aviseringar som kräver åtgärder.|
| domainConfigurationType | Som standard skapas en hanterad domän som en *användar* skog. Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användar konton som skapats i en lokal AD DS-miljö. Du behöver inte ange ett *domainConfiguration* -värde för att skapa en användar skog.<br /><br /> En *resurs* skog synkroniserar bara användare och grupper som skapats direkt i Azure AD. Ställ in värdet på *ResourceTrusting* för att skapa en resurs skog.<br /><br />Mer information om *resurs* skogar, inklusive varför du kan använda en och hur du skapar skogs förtroenden med lokala AD DS-domäner finns i [Översikt över Azure AD DS resurs skogar][resource-forests].|

I följande dekomprimerade Parameters-definition visas hur dessa värden deklareras. En användar skog med namnet *aaddscontoso.com* skapas med alla användare från Azure AD synkroniserat till den hanterade domänen:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Följande resurs typ för en komprimerad Resource Manager-mall används sedan för att definiera och skapa den hanterade domänen. Ett virtuellt Azure-nätverk och undernät måste redan finnas eller skapas som en del av Resource Manager-mallen. Den hanterade domänen är ansluten till det här under nätet.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Dessa parametrar och resurs typer kan användas som en del av en bredare Resource Manager-mall för att distribuera en hanterad domän, som du ser i följande avsnitt.

## <a name="create-a-managed-domain-using-sample-template"></a>Skapa en hanterad domän med hjälp av exempel mall

Följande fullständiga Resource Manager-exempel skapar en hanterad domän och de stödda reglerna för virtuella nätverk, undernät och nätverks säkerhets grupper. Reglerna för nätverks säkerhets grupper krävs för att skydda den hanterade domänen och se till att trafiken kan flöda korrekt. En användar skog med DNS-namnet *aaddscontoso.com* skapas med alla användare som synkroniseras från Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Den här mallen kan distribueras med den önskade distributions metoden, till exempel [Azure Portal][portal-deploy], [Azure POWERSHELL][powershell-deploy]eller en CI/CD-pipeline. I följande exempel används cmdleten [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] . Ange ett eget namn på din resurs grupp och mall:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Det tar några minuter att skapa resursen och returnera kontrollen till PowerShell-prompten. Den hanterade domänen fortsätter att tillhandahållas i bakgrunden och kan ta upp till en timme att slutföra distributionen. På sidan Azure Portal visar **översikts** sidan för din hanterade domän den aktuella statusen i den här distributions fasen.

När Azure Portal visar att den hanterade domänen har slutfört etableringen måste följande uppgifter utföras:

* Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
* [Aktivera Lösenordssynkronisering till Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="next-steps"></a>Nästa steg

Om du vill se den hanterade domänen i praktiken kan du [domän ansluta till en virtuell Windows-dator][windows-join], [Konfigurera säker LDAP][tutorial-ldaps]och [Konfigurera hash-synkronisering av lösen ord][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment