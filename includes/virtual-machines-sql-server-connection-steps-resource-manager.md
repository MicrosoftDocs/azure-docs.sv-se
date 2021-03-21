---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025477"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Konfigurera en DNS-etikett för den offentliga IP-adressen

Om du vill ansluta till SQL Server Database Engine från Internet, bör du konfigurera en DNS-etikett för din offentliga IP-adress. Du kan ansluta med en IP-adress men DNS-etiketten skapar en A-post som är lättare att identifiera och sammanfattar den underliggande offentliga IP-adressen.

> [!NOTE]
> DNS-etiketter krävs inte om du planerar att endast ansluta till SQL Server-instansen inom samma virtuella nätverk eller bara lokalt.

För att skapa en DNS-etikett, väljer du först **Virtuella datorer** i portalen. Välj din SQL Server-VM för att få fram egenskaperna.

1. I översikten för den virtuella datorn väljer du din **offentliga IP-adress**.

    ![offentlig IP-adress](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. I egenskaperna för din offentliga IP-adress, expanderar du **Konfiguration**.

1. Ange ett namn för DNS-etiketten. Det här namnet är en A-post som kan användas för att ansluta till din SQL Server-VM via namn istället för direkt via IP-adress.

1. Klicka på knappen **Spara**.

    ![dns-etikett](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Anslut till databasmotorn från en annan dator

1. Öppna SQL Server Management Studio (SSMS) från en dator som är ansluten till Internet. Om du inte har SQL Server Management Studio kan du ladda ned den [här](/sql/ssms/download-sql-server-management-studio-ssms).

1. I dialogrutan **Anslut till server** eller **Anslut till databasmotor**, redigerar du värdet för **Servernamn**. Ange IP-adressen eller det fullständiga DNS-namnet för den virtuella datorn (bestäms i föregående åtgärd). Du kan också lägga till ett kommatecken och ange TCP-porten för SQL Server. Till exempel `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.

1. I rutan **Inloggning**, skriver du namnet på en giltig SQL-inloggning.

1. I rutan **Lösenord**, skriver du lösenordet för inloggningen.

1. Klicka på **Anslut**.

    ![ssms anslut](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)