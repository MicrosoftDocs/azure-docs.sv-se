---
title: Hantera serveradministratörer i Azure Analysis Services | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar serveradministratörer för en Azure Analysis Services-server med hjälp av Azure Portal-, PowerShell- eller REST-API:er.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769201"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer

Serveradministratörer måste vara en giltig användare, tjänstens huvudnamn eller säkerhetsgrupp i Azure Active Directory (Azure AD) för den klientorganisation där servern finns. Du kan använda **Analysis Services administratörer** för servern i Azure Portal, Serveregenskaper i SSMS, PowerShell eller REST API för att hantera serveradministratörer. 

När du lägger **till en säkerhetsgrupp** använder du `obj:groupid@tenantid` . Tjänstens huvudnamn stöds inte i säkerhetsgrupper som läggs till i serveradministratörsrollen.

Mer information om hur du lägger till ett huvudnamn för tjänsten i serveradministratörsrollen finns i [Lägga till ett huvudnamn för tjänsten i serveradministratörsrollen](analysis-services-addservprinc-admins.md).

Om serverbrandväggen är aktiverad måste klientdatorns IP-adresser för serveradministratören inkluderas i en brandväggsregel. Mer information finns i Konfigurera [serverns brandvägg.](analysis-services-qs-firewall.md)

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Lägga till serveradministratörer med hjälp av Azure Portal

1. I portalen för servern klickar du på **Analysis Services Administratörer**.
2. I **\<servername> - Analysis Services administratörer klickar** du på Lägg **till**.
3. I **Lägg till serveradministratörer** väljer du användarkonton från Azure AD eller bjuder in externa användare via e-postadress.

    ![Serveradministratörer i Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Lägga till serveradministratörer med hjälp av SSMS

1. Högerklicka på servern > **Egenskaper**.
2. I **Analysis Server egenskaper klickar** du på **Säkerhet**.
3. Klicka **på** Lägg till och ange sedan e-postadressen för en användare eller grupp i Azure AD.
   
    ![Lägga till serveradministratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd [cmdleten New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) för att ange parametern Administratör när du skapar en ny server. <br>
Använd cmdleten [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) för att ändra administratörsparametern för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [Skapa](/rest/api/analysisservices/servers/create) för att ange egenskapen asAdministrator när du skapar en ny server. <br>
Använd [Uppdatera](/rest/api/analysisservices/servers/update) för att ange egenskapen asAdministrator när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Azure RBAC (rollbaserad åtkomstkontroll)](../role-based-access-control/overview.md)
