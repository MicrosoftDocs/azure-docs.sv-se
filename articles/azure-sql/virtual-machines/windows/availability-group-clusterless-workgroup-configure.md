---
title: Konfigurera en domän oberoende arbets grupps tillgänglighets grupp
description: Lär dig hur du konfigurerar en Active Directory domän oberoende arbets grupps tillgänglighets grupp på en SQL Server virtuell dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359463"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurera en tillgänglighets grupp för arbets gruppen 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln beskrivs de steg som krävs för att skapa ett Active Directory domän oberoende kluster med en tillgänglighets grupp som alltid är tillgänglig. Detta kallas även för ett arbets grupps kluster. Den här artikeln fokuserar på de steg som är relevanta för att förbereda och konfigurera gruppen för arbets grupper och tillgänglighet, och ord listas över steg som beskrivs i andra artiklar, till exempel hur du skapar klustret eller distribuerar tillgänglighets gruppen. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera en tillgänglighets grupp för arbets grupper behöver du följande:
- Minst två virtuella datorer med Windows Server 2016 (eller högre) som kör SQL Server 2016 (eller högre), distribueras till samma tillgänglighets uppsättning eller olika tillgänglighets zoner med statiska IP-adresser. 
- Ett lokalt nätverk med minst 4 lediga IP-adresser i under nätet. 
- Ett konto på varje dator i gruppen Administratörer som också har sysadmin-behörighet inom SQL Server. 
- Öppna portar: TCP 1433, TCP 5022, TCP 59999. 

För referens används följande parametrar i den här artikeln, men kan ändras när det behövs: 

| **Namn** | **Parameter** |
| :------ | :---------------------------------- |
| **Nod1**   | AGNode1 (10.0.0.4) |
| **NOD2**   | AGNode2 (10.0.0.5) |
| **Kluster namn** | AGWGAG (10.0.0.6) |
| **Lyssnare** | AGListener (10.0.0.7) | 
| **DNS-suffix** | ag.wgcluster.example.com | 
| **Arbets grupps namn** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Ange ett DNS-suffix 

I det här steget konfigurerar du DNS-suffixet för båda servrarna. Till exempel `ag.wgcluster.example.com`. På så sätt kan du använda namnet på det objekt som du vill ansluta till som en fullständigt kvalificerad adress i nätverket, till exempel `AGNode1.ag.wgcluster.example.com` . 

Följ dessa steg om du vill konfigurera DNS-suffixet:

1. RDP in till din första nod och öppna Serverhanteraren. 
1. Välj **lokal server** och välj sedan namnet på den virtuella datorn under **dator namn**. 
1. Välj **ändra..** . under **för att byta namn på den här datorn.**.. 
1. Ändra namnet på arbets grupps namnet så att det är något meningsfullt, till exempel `AGWORKGROUP` : 

   ![Ändra arbets grupps namn](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Välj **mer...** för att öppna dialog rutan **DNS-suffix och NetBIOS-** datornamn. 
1. Skriv namnet på ditt DNS-suffix under **primärt DNS-suffix för den här datorn**, till exempel `ag.wgcluster.example.com` och välj sedan **OK**: 

   ![Skärm bild som visar dialog rutan D N S suffix och NetBIOS-datornamn där du kan ange värdet.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Bekräfta att det **fullständiga dator namnet** nu visar DNS-suffixet och välj sedan **OK** för att spara ändringarna: 

   ![Skärm bild som visar var du kan se det fullständiga dator namnet.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Starta om servern när du uppmanas att göra det. 
1. Upprepa de här stegen på alla andra noder som ska användas för tillgänglighets gruppen. 

## <a name="edit-a-host-file"></a>Redigera en värd fil

Eftersom det inte finns någon Active Directory finns det inget sätt att autentisera Windows-anslutningar. Tilldela till exempel förtroende genom att redigera värd filen med en text redigerare. 

Följ dessa steg om du vill redigera värd filen:

1. RDP in på den virtuella datorn. 
1. Använd **Utforskaren** för att gå till `c:\windows\system32\drivers\etc` . 
1. Högerklicka på **hosts** -filen och öppna filen med **anteckningar** (eller någon annan text redigerare).
1. I slutet av filen lägger du till en post för varje nod, tillgänglighets gruppen och lyssnaren i form av `IP Address, DNS Suffix #comment` t. ex.: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Lägg till poster för IP-adress, kluster och lyssnare till värd filen](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Ställa in behörigheter

Eftersom det inte finns några Active Directory för att hantera behörigheter måste du manuellt tillåta ett lokalt administratörs konto som inte är fördefinierat för att skapa klustret. 

Det gör du genom att köra följande PowerShell-cmdlet i en administrativ PowerShell-session på varje nod: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Skapa redundansklustret

I det här steget ska du skapa redundansklustret. Om du inte är bekant med de här stegen kan du följa anvisningarna i [själv studie kursen för redundanskluster](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Viktiga skillnader mellan självstudien och vad som ska göras för ett arbets grupps kluster:
- Avmarkera **lagringen** och **Lagringsdirigering** när du kör kluster verifieringen. 
- När du lägger till noderna i klustret lägger du till det fullständigt kvalificerade namnet, till exempel:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Avmarkera **Lägg till alla tillgängliga lagrings enheter i klustret**. 

När klustret har skapats tilldelar du en statisk kluster-IP-adress. Det gör du på följande sätt:

1. Öppna **Klusterhanteraren för växling vid fel** på en av noderna, Välj klustret, högerklicka på **namnet: \<ClusterNam>** under **kluster kärn resurser** och välj sedan **Egenskaper**. 

   ![Start egenskaper för kluster namnet](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Välj IP-adressen under **IP-adresser** och välj **Redigera**. 
1. Välj **Använd statisk**, ange IP-adressen för klustret och välj sedan **OK**: 

   ![Ange en statisk IP-adress för klustret](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Kontrol lera att inställningarna ser korrekta ut och välj sedan **OK** för att spara dem:

   ![Verifiera kluster egenskaper](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Skapa ett moln vittne 

I det här steget konfigurerar du ett moln resurs vittne. Om du inte är bekant med stegen läser du [distribuera ett moln vittne för ett redundanskluster](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Aktivera funktionen tillgänglighets grupp 

I det här steget aktiverar du funktionen tillgänglighets grupp. Om du inte är bekant med stegen går du till [självstudien för tillgänglighets gruppen](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Skapa nycklar och certifikat

I det här steget skapar du certifikat som en SQL-inloggning använder på den krypterade slut punkten. Skapa en mapp på varje nod för att lagra säkerhets kopior av certifikatet, till exempel `c:\certs` . 

Följ dessa steg om du vill konfigurera den första noden: 

1. Öppna **SQL Server Management Studio** och Anslut till din första nod, till exempel `AGNode1` . 
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-uttryck (T-SQL) efter att ha uppdaterat till ett komplext och säkert lösen ord:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Skapa sedan HADR-slutpunkten och Använd certifikatet för autentisering genom att köra Transact-SQL-instruktionen (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Använd **Utforskaren** för att gå till fil platsen där ditt certifikat är, till exempel `c:\certs` . 
1. Gör en kopia av certifikatet manuellt, till exempel `AGNode1Cert.crt` från den första noden, och överför den till samma plats på den andra noden. 

Följ dessa steg om du vill konfigurera den andra noden: 

1. Anslut till den andra noden med **SQL Server Management Studio**, till exempel `AGNode2` . 
1. I ett **nytt frågefönster** kör du följande Transact-SQL-uttryck (T-SQL) efter att ha uppdaterat till ett komplext och säkert lösen ord: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Skapa sedan HADR-slutpunkten och Använd certifikatet för autentisering genom att köra Transact-SQL-instruktionen (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Använd **Utforskaren** för att gå till fil platsen där ditt certifikat är, till exempel `c:\certs` . 
1. Gör en kopia av certifikatet manuellt, till exempel `AGNode2Cert.crt` från den andra noden, och överför den till samma plats på den första noden. 

Om det finns några andra noder i klustret upprepar du de här stegen även och ändrar respektive certifikat namn. 

## <a name="create-logins"></a>Skapa inloggningar

Certifikatautentisering används för att synkronisera data mellan noder. Om du vill tillåta detta skapar du en inloggning för den andra noden, skapar en användare för inloggningen, skapar ett certifikat för inloggningen för att använda det säkerhetskopierade certifikatet och tilldelar sedan Connect på speglings slut punkten. 

Det gör du genom att först köra följande Transact-SQL-fråga (T-SQL) på den första noden, till exempel `AGNode1` : 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Kör sedan följande Transact-SQL-fråga (T-SQL) på den andra noden, till exempel `AGNode2` : 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Om det finns andra noder i klustret upprepar du de här stegen också, och ändrar respektive certifikat och användar namn. 

## <a name="configure-an-availability-group"></a>Konfigurera en tillgänglighetsgrupp

I det här steget konfigurerar du din tillgänglighets grupp och lägger till dina databaser i den. Skapa inte en lyssnare just nu. Om du inte är bekant med stegen går du till [självstudien för tillgänglighets gruppen](availability-group-manually-configure-tutorial.md#create-the-availability-group). Se till att initiera en redundansväxling och återställning efter fel för att kontrol lera att allt fungerar som det ska. 

   > [!NOTE]
   > Om det uppstår ett fel under synkroniseringsprocessen kan du behöva ge `NT AUTHORITY\SYSTEM` sysadmin-behörighet att skapa kluster resurser på den första noden, till exempel `AGNode1` tillfälligt. 

## <a name="configure-a-load-balancer"></a>Konfigurera en belastningsutjämnare

I det sista steget konfigurerar du belastningsutjämnaren med hjälp av antingen [Azure Portal](availability-group-load-balancer-portal-configure.md) eller [PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Nästa steg

Du kan också använda [AZ SQL CLI](./availability-group-az-commandline-configure.md) för att konfigurera en tillgänglighets grupp.