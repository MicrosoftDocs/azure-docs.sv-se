---
title: Konfigurera en gemensam identitet
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du skapar vanliga användar konton som kan användas över flera data vetenskaps Virtual Machines. Du kan använda Azure Active Directory eller en lokal Active Directory för att autentisera användare till Data Science Virtual Machine.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell data vetenskaps dator, Geospatial analys, team data vetenskaps process
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519736"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Konfigurera en gemensam identitet på en Data Science Virtual Machine

På en Microsoft Azure virtuell dator (VM), inklusive en Data Science Virtual Machine (DSVM), skapar du lokala användar konton när du konfigurerar den virtuella datorn. Användarna autentiseras sedan till den virtuella datorn med dessa autentiseringsuppgifter. Om du har flera virtuella datorer som dina användare behöver åtkomst till kan hanteringen av autentiseringsuppgifter bli mycket besvärlig. En utmärkt lösning är att distribuera vanliga användar konton och hantering via en standardbaserad identitets leverantör. Med den här metoden kan du använda en enda uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser i Azure, inklusive flera Dsvm.

Active Directory är en populär identitets leverantör som stöds i Azure både som en moln tjänst och som en lokal katalog. Du kan använda Azure Active Directory (Azure AD) eller lokala Active Directory för att autentisera användare på en fristående DSVM eller ett kluster med Dsvm i en skalnings uppsättning för virtuella Azure-datorer. Du gör detta genom att ansluta DSVM-instanserna till en Active Directory domän.

Om du redan har Active Directory kan du använda den som din gemensamma identitets leverantör. Om du inte har Active Directory kan du köra en hanterad Active Directory-instans på Azure via [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS).

Dokumentationen för [Azure AD](../../active-directory/index.yml) innehåller detaljerade [hanterings anvisningar](../../active-directory/hybrid/whatis-hybrid-identity.md), inklusive vägledning om hur du ansluter Azure AD till din lokala katalog om du har en.

Den här artikeln beskriver hur du konfigurerar en fullständigt hanterad Active Directory domän tjänst på Azure med hjälp av Azure AD DS. Du kan sedan ansluta din Dsvm till den hanterade Active Directorys domänen. Den här metoden gör det möjligt för användare att komma åt en pool med Dsvm (och andra Azure-resurser) via ett gemensamt användar konto och autentiseringsuppgifter.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurera en fullständigt hanterad Active Directory domän på Azure

Azure AD DS gör det enkelt att hantera dina identiteter genom att tillhandahålla en fullständigt hanterad tjänst på Azure. I den här Active Directory-domänen hanterar du användare och grupper. Följ dessa steg om du vill konfigurera en Azure-värdbaserad Active Directory domän och användar konton i din katalog:

1. Lägg till användaren i Active Directory i Azure Portal: 

   1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) genom att använda ett konto som är en global administratör för katalogen.
    
   1. Välj **Azure Active Directory** och sedan **Användare och grupper**.
    
   1. I **användare och grupper** väljer du **alla användare** och väljer sedan **ny användare**.
   
        Fönstret **användare** öppnas:
      
        ![Fönstret "användare"](./media/add-user.png)
    
   1. Ange information för användaren, till exempel **Namn** och **Användarnamn**. Domän namns delen av användar namnet måste antingen vara det initiala standard domän namnet [domän namn]. onmicrosoft. com "eller ett verifierat, icke-federerat [anpassat domän namn](../../active-directory/fundamentals/add-custom-domain.md) , till exempel" contoso.com ".
    
   1. Kopiera eller anteckna det genererade användarlösenordet på annat sätt så att du kan ge det till användaren när den här processen är klar.
    
   1. Du kan också öppna och fylla i informationen i **Profil**, **Grupper** eller **Katalogroll** för användaren. 
    
   1. Under **användare** väljer du **skapa**.
    
   1. Distribuera det genererade lösen ordet på ett säkert sätt till den nya användaren så att de kan logga in.

1. Skapa en Azure AD DS-instans. Följ anvisningarna i  [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md) (avsnittet "skapa en instans och konfigurera grundläggande inställningar"). Det är viktigt att uppdatera befintliga användar lösen ord i Active Directory så att lösen ordet i Azure AD DS synkroniseras. Det är också viktigt att lägga till DNS i Azure AD DS, enligt beskrivningen under "Fyll i fälten i fönstret Basics i Azure Portal för att skapa en Azure AD DS-instans" i avsnittet.

1. Skapa ett separat DSVM-undernät i det virtuella nätverk som skapats i avsnittet "skapa och konfigurera det virtuella nätverket" i föregående steg.
1. Skapa en eller flera DSVM-instanser i DSVM-undernätet.
1. Följ [anvisningarna](../../active-directory-domain-services/join-ubuntu-linux-vm.md) för att lägga till DSVM i Active Directory. 
1. Montera en Azure Files-resurs som värd för din hem-eller Notebook-katalog så att din arbets yta kan monteras på vilken dator som helst. (Om du behöver tätt fil nivå behörigheter behöver du använda Network File System [NFS] på en eller flera virtuella datorer.)

   1. [Skapa en Azure Files-resurs](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Montera den här resursen på Linux-DSVM. När du väljer **Anslut** för Azure Files resursen på ditt lagrings konto i Azure Portal visas kommandot som ska köras i bash-gränssnittet på Linux-DSVM. Kommandot ser ut så här:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Anta till exempel att du har monterat din Azure Files-resurs i/data/Workspace. Skapa nu kataloger för var och en av dina användare i resursen:/data/Workspace/user1,/data/Workspace/user2 och så vidare. Skapa en `notebooks` katalog på varje användares arbets yta. 
1. Skapa symboliska länkar för `notebooks` i `$HOME/userx/notebooks/remote` .   

Nu har du användare i din Active Directory-instans som finns i Azure. Genom att använda Active Directory autentiseringsuppgifter kan användare logga in på alla DSVM (SSH eller JupyterHub) som är anslutna till Azure AD DS. Eftersom användar arbets ytan finns på en Azure Files resurs, har användare till gång till sina antecknings böcker och annat arbete från alla DSVM när de använder JupyterHub.

För automatisk skalning kan du använda en skalnings uppsättning för virtuella datorer för att skapa en pool med virtuella datorer som är anslutna till domänen på det här sättet och med den delade disken monterad. Användare kan logga in på alla tillgängliga datorer i skalnings uppsättningen för den virtuella datorn och ha åtkomst till den delade disk där deras antecknings böcker har sparats. 

## <a name="next-steps"></a>Nästa steg

* [Lagra autentiseringsuppgifter på ett säkert sätt för att få åtkomst till moln resurser](dsvm-secure-access-keys.md)