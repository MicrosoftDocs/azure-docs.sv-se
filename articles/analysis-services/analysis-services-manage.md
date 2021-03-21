---
title: Hantera Azure Analysis Services | Microsoft Docs
description: I den här artikeln beskrivs de verktyg som används för att hantera administrations-och hanterings uppgifter för en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91ffcac98b2b919a8fc131d235e699aad4fa215d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93078908"
---
# <a name="manage-analysis-services"></a>Manage Analysis Services
När du har skapat en Analysis Services-server i Azure kan det finnas några administrations-och hanterings uppgifter som du behöver utföra direkt eller vid en stund. Du kan till exempel köra bearbetning av uppdatera data, kontrol lera vem som har åtkomst till modellerna på servern eller övervaka serverns hälsa. Vissa hanterings uppgifter kan endast utföras i Azure Portal, andra i SQL Server Management Studio (SSMS) och vissa uppgifter kan göras i båda.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com/) är där du kan skapa och ta bort servrar, övervaka server resurser, ändra storlek och hantera vem som har åtkomst till dina servrar.  Om du har problem kan du också skicka in en support förfrågan.

![Skärm bild som visar Azure Portal där du kan skapa och ta bort servrar, övervaka server resurser, ändra storlek och hantera vem som har åtkomst till dina servrar.](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Att ansluta till din server i Azure är precis som att ansluta till en Server instans i din organisation. Från SSMS kan du utföra många av samma uppgifter, till exempel process data eller skapa ett bearbetnings skript, hantera roller och använda PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Ladda ned och installera SSMS
Se till att du använder den senaste versionen av SSMS för att få alla de senaste funktionerna och den smidigaste upplevelsen vid anslutning till din Azure Analysis Services-server. 

[Ladda ned SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Så här ansluter du till SSMS
 När du använder SSMS innan du ansluter till servern första gången, måste du se till att ditt användar namn ingår i gruppen Analysis Services admins. Mer information finns i [Server administratörer och databas användare](#server-administrators-and-database-users) senare i den här artikeln.

1. Innan du ansluter måste du hämta server namnet. Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
    ![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. I SSMS > **Object Explorer** klickar du på **Anslut** > **Analysis Services**.
3. I dialog rutan **Anslut till Server** klistrar du in namnet på servern och väljer sedan någon av följande autentiseringstyper i **autentisering**:   
    > [!NOTE]
    > Autentiseringstypen, **Active Directory-Universal med MFA-support**, rekommenderas.

    > [!NOTE]
    > Om du loggar in med ett Microsoft-konto, Live ID, Yahoo, Gmail osv. lämnar du fältet lösen ord tomt. Du uppmanas att ange ett lösen ord när du har klickat på Anslut.

    **Windows-autentisering** för att använda Windows-autentiseringsuppgifter för domän \ användar namn och lösen ord.

    **Active Directory** lösenordsautentisering för att använda ett organisations konto. Till exempel när du ansluter från en dator som inte är domänansluten.

    **Active Directory-universell med MFA-stöd** för att använda [icke-interaktiv eller Multi-Factor Authentication](../azure-sql/database/authentication-mfa-ssms-overview.md). 
   
    ![Ansluta i SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Server administratörer och databas användare
I Azure Analysis Services finns det två typer av användare, Server administratörer och databas användare. Båda typerna av användare måste finnas i din Azure Active Directory och måste anges med organisationens e-postadress eller UPN. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Felsöka anslutnings problem
Om du stöter på problem när du ansluter med SSMS kan du behöva rensa inloggnings-cachen. Inget cachelagras på skivan. Om du vill rensa cacheminnet stänger du och startar om anslutnings processen. 

## <a name="next-steps"></a>Nästa steg
Om du inte redan har distribuerat en tabell modell till den nya servern, är det nu en lämplig tid. Läs mer i [Distribuera till Azure Analysis Services](analysis-services-deploy.md).

Om du har distribuerat en modell till servern är du redo att ansluta till den med hjälp av en klient eller webbläsare. Läs mer i [Hämta data från Azure Analysis Services server](analysis-services-connect.md).