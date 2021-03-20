---
title: Autentisera en hanterad identitet med Azure Active Directory
description: Den här artikeln innehåller information om hur du autentiserar en hanterad identitet med Azure Active Directory för att få åtkomst till Azure SignalR-tjänsten
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101092549"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för åtkomst till Azure Signals-resurser
Azure SignalR service stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till Azure SignalR service-resurser med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure Virtual Machines (VM), Function-appar, Virtual Machine Scale Sets och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.

Den här artikeln visar hur du auktoriserar åtkomst till en Azure SignalR-tjänst genom att använda en hanterad identitet från en virtuell Azure-dator.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Azure SIgnalr service-resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörighet till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Azure SignalR-tjänsten från en hanterad identitet i ditt program måste du först konfigurera inställningarna för rollbaserad åtkomst kontroll (RBAC) för den hanterade identiteten. Azure SignalR service definierar RBAC-roller som omfattar behörigheter för att förvärva `AccessKey` eller `ClientToken` . När RBAC-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Azure SignalR-tjänsten i det aktuella omfånget.

Mer information om hur du tilldelar RBAC-roller finns i [autentisera med Azure Active Directory för åtkomst till Azure SignalR service-resurser](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Ansluta till Azure SignalR-tjänsten med hanterade identiteter
För att ansluta till Azure SignalR-tjänsten med hanterade identiteter måste du tilldela identiteten rollen och lämplig omfattning. I proceduren i det här avsnittet används ett enkelt program som körs under en hanterad identitet och som har åtkomst till Azure SignalR service-resurser.

Här använder vi ett exempel på en virtuell Azure-dator resurs.

1. Gå till **Inställningar** och välj **identitet**. 
1. Välj den **status** som ska **användas.** 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en virtuell dator](./media/authenticate/identity-virtual-machine.png)

När du har aktiverat den här inställningen skapas en ny tjänst identitet i din Azure Active Directory (Azure AD) och konfigureras i App Service-värden.

Tilldela nu den här tjänst identiteten till en roll i det begärda omfånget i Azure SignalR service-resurserna.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal  
Mer information om hur du hanterar åtkomst till Azure-resurser med hjälp av Azure RBAC och Azure Portal finns i [den här artikeln](..//role-based-access-control/role-assignments-portal.md). 

När du har bestämt lämplig omfattning för en roll tilldelning navigerar du till den resursen i Azure Portal. Visa inställningarna för åtkomst kontroll (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

1. I [Azure Portal](https://portal.azure.com/)navigerar du till din signal resurs.
1. Välj **Access Control (IAM)** om du vill visa inställningar för åtkomst kontroll för Azure-signaleraren. 
1. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar. Välj knappen **Lägg till** i verktygsfältet och välj sedan **Lägg till roll tilldelning**. 

    ![Knappen Lägg till i verktygsfältet](./media/authenticate/role-assignments-add-button.png)

1. Utför följande steg på sidan **Lägg till roll tilldelning** :
    1. Välj den **Azure SignalR-roll** som du vill tilldela. 
    1. Sök efter **säkerhets objekt** (användare, grupp, tjänstens huvud namn) som du vill tilldela rollen.
    1. Spara roll tilldelningen genom att välja **Spara** . 

        ![Tilldela en roll till ett program](./media/authenticate/assign-role-to-application.png)

    1. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel programmet `signalr-dev` och `signalr-service` finns i App Server-rollen för signalerare. 
        
        ![Roll tilldelnings lista](./media/authenticate/role-assignment-list.png)

Du kan följa liknande steg för att tilldela en roll som är begränsad till resurs grupp eller prenumeration. När du definierar rollen och dess omfattning kan du testa det här beteendet med exempel [på den här GitHub-platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="samples-code-while-configuring-your-app-server"></a>Exempel kod när du konfigurerar din app server

Lägg till följande alternativ när `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Nästa steg
- Mer information om RBAC finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)?
- Information om hur du tilldelar och hanterar roll tilldelningar i Azure med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med REST API](../role-based-access-control/role-assignments-rest.md)
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure Resource Manager mallar](../role-based-access-control/role-assignments-template.md)

Se följande relaterade artiklar:
- [Autentisera ett program med Azure Active Directory för att få åtkomst till Azure SignalR-tjänsten](authenticate-application.md)
- [Ge åtkomst till Azure SignalR service med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md)