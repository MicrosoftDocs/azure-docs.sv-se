---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Key Vault – Linux – Azure AD
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb31d6e25ce1c1ff5c3e4dbabb4fa53da0bd2ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093927"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur en virtuell Linux-dator (VM) kan använda en systemtilldelad hanterad identitet för att få åtkomst till [Azure Key Vault](../../key-vault/general/overview.md). Key Vault fungerar som en start, gör det möjligt för ditt klient program att använda en hemlighet för att komma åt resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänst identiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan även autentiseringsinformation i din kod.

Lär dig att:

> [!div class="checklist"]
> * Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av identiteten för de virtuella datorerna och använd den för att hämta hemligheten från Key Vault 
 
## <a name="prerequisites"></a>Förutsättningar

- Grundläggande förståelse för hanterade identiteter. Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](overview.md). 
- Ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Ägarens behörigheter i lämpligt omfång (din prenumeration eller resurs grupp) för att utföra nödvändiga steg för att skapa resurser och roll hantering. Om du behöver hjälp med roll tilldelning, se [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../../role-based-access-control/role-assignments-portal.md).
- Du behöver också en virtuell Linux-dator som har systemtilldelade hanterade identiteter aktiverade.
  - Om du behöver skapa en virtuell dator för den här självstudien kan du följa artikeln [skapa en virtuell Linux-dator med Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning  

I det här avsnittet visas hur du beviljar din VM-åtkomst till en hemlighet som lagras i en Key Vault. Med hjälp av hanterade identiteter för Azure-resurser kan din kod hämta åtkomsttoken för att autentisera mot resurser som har stöd för Azure AD-autentisering.Men alla Azure-tjänster stöder inte Azure Active Directory-autentisering. Om du vill använda hanterade identiteter för Azure-resurser med dessa tjänster lagrar du autentiseringsuppgifterna för tjänsten i Azure Key Vault och får åtkomst till Key Vault och hämtar autentiseringsuppgifterna med hjälp av den virtuella datorns hanterade identitet.

Börja med att skapa ett Key Vault och bevilja den virtuella datorns systemtilldelade hanterade identitet åtkomst till Key Vault.

1. Öppna Azure [Portal](https://portal.azure.com/)
1. Längst upp i det vänstra navigerings fältet väljer du **skapa en resurs**  
1. I rutan **Sök på Marketplace skriver du** in **Key Vault** och trycker på **RETUR**.  
1. Välj **Key Vault** från resultaten.
1. Välj **Skapa**
1. Ange ett **namn** för det nya Key Vault.

    ![Skapa ett Key Vault-fönster](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Fyll i all nödvändig information och se till att du väljer den prenumeration och resurs grupp där du skapade den virtuella dator som du använder för den här självstudien.
1. Välj **Granska + skapa**
1. Välj **Skapa**

### <a name="create-a-secret"></a>Skapa en hemlighet

Lägg sedan till en hemlighet till Key Vault så att du kan hämta den senare med hjälp av kod som körs i den virtuella datorn. I den här självstudien använder vi PowerShell men samma koncept gäller för all kod som körs på den här virtuella datorn.

1. Navigera till din nyligen skapade Key Vault.
1. Välj **Hemligheter** och klicka på **Lägg till**.
1. Välj **generera/importera**
1. I fönstret **skapa en hemlig** skärm från **överförings alternativ** lämnar du **manuellt** markerat.
1. Ange ett namn och värde för hemligheten.  Värdet kan vara vad du vill. 
1. Låt aktiveringsdatum och förfallodatum vara tomt och sätt **Aktiverad** som **Ja**. 
1. Klicka på **Skapa** och skapa hemligheten.

   ![Skapa en hemlighet](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Bevilja åtkomst

Den hanterade identitet som används av den virtuella datorn måste beviljas åtkomst för att läsa hemligheten som vi lagrar i Key Vault.

1. Navigera till din nyligen skapade Key Vault
1. Välj **åtkomst princip** på menyn på vänster sida.
1. Välj **Lägg till åtkomst princip**

   ![skärm för att skapa åtkomst princip för nyckel valv](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. I avsnittet **Lägg till åtkomst princip** under **Konfigurera från mall (valfritt)** väljer du **hemlig hantering** i den nedrullningsbara menyn.
1. Välj **Välj huvudkonto** och ange namnet på den virtuella dator som du skapade tidigare i sökfältet.  Välj den virtuella datorn i resultat listan och välj **Välj**.
1. Välj **Lägg till**
1. Välj **Spara**.

## <a name="access-data"></a>Åtkomst till data

För att slutföra de här stegen behöver du en SSH-klient.  Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).
 
1. I portalen går du till den virtuella Linux-datorn och i **översikten** klickar du på **Anslut**. 
2. **Anslut** till den virtuella datorn med valfri SSH-klient. 
3. Använd CURL i terminalfönstret och skicka en begäran till de lokala hanterade identiteterna för Azure-resursslutpunkter för att hämta en åtkomsttoken för Azure Key Vault.  
 
    CURL-begäran för åtkomsttoken visas nedan.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Svaret innehåller den åtkomsttoken som du behöver för att komma åt Resource Manager. 
    
    Svar:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Du kan använda denna åtkomsttoken för att autentisera till Azure Key Vault.  Nästa CURL-begäran visar hur du kan läsa en hemlighet från Key Vault med hjälp av CURL och Key Vault REST API:et.  Du behöver URL:en till ditt Key Vault, som finns i avsnittet **Essentials** på Key Vault-sidan **Översikt**.  Du Behöver också den åtkomsttoken som du fick i det föregående anropet. 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Svaret ser ut såhär: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan du använda den och autentisera mot en tjänst som kräver ett namn och lösenord.

## <a name="clean-up-resources"></a>Rensa resurser

När du vill rensa resurserna går du till [Azure Portal](https://portal.azure.com), väljer **resurs grupper**, letar upp och väljer den resurs grupp som skapades i processen för den här självstudien (till exempel `mi-test` ) och använder sedan kommandot **ta bort resurs grupp** .

Alternativt kan du också göra detta via [PowerShell eller CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Key Vault.  Läs mer om Azure Key Vault här:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)