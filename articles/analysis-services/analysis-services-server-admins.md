---
title: Hantera Server administratörer i Azure Analysis Services | Microsoft Docs
description: 'Den här artikeln beskriver hur du hanterar Server administratörer för en Azure Analysis Services-server med hjälp av Azure Portal-, PowerShell-eller REST-API: er.'
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62acb526a247362b17c4dfd4e26c52760deecd71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573508"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer

Server administratörer måste vara en giltig användare, tjänstens huvud namn eller säkerhets grupp i Azure Active Directory (Azure AD) för den klient där-servern finns. Du kan använda **Analysis Services administratörer** för servern i Azure Portal, Server egenskaper i SSMS, PowerShell eller REST API för att hantera Server administratörer. 

Använd om du vill lägga till en **säkerhets grupp** `obj:groupid@tenantid` . Tjänstens huvud namn stöds inte i säkerhets grupper som har lagts till i Server administratörs rollen.

Mer information om hur du lägger till ett huvud namn för tjänsten i Server administratörs rollen finns i [lägga till ett huvud namn för tjänsten i Server administratörs rollen](analysis-services-addservprinc-admins.md).

Om Server brand väggen är aktive rad måste IP-adresser för Server administratörs klienten inkluderas i en brand Väggs regel. Mer information finns i [Konfigurera Server brand väggen](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Lägga till Server administratörer med hjälp av Azure Portal

1. Klicka på **Analysis Services administratörer** på din server i portalen.
2. **\<servername> Analysis Services administratörer** klickar du på **Lägg till**.
3. I **Lägg till Server administratörer** väljer du användar konton från din Azure AD eller bjuder in externa användare via e-postadress.

    ![Server administratörer i Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Så här lägger du till Server administratörer med SSMS

1. Högerklicka på Server > **Egenskaper**.
2. I **Analysis Server egenskaper** klickar du på **säkerhet**.
3. Klicka på **Lägg till** och ange sedan e-postadressen för en användare eller grupp i din Azure AD.
   
    ![Lägga till Server administratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd cmdleten [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) för att ange parametern administratör när du skapar en ny server. <br>
Använd [set-AzAnalysisServicesServer-](/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdleten för att ändra parametern administratör för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [create](/rest/api/analysisservices/servers/create) för att ange egenskapen för administratör när du skapar en ny server. <br>
Använd [Update](/rest/api/analysisservices/servers/update) för att ange egenskapen för administratör när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databas roller och användare](analysis-services-database-users.md)  
[Azure RBAC (rollbaserad åtkomstkontroll)](../role-based-access-control/overview.md)
