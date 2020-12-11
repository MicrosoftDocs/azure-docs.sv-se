---
title: Självstudie `:` Använd en hanterad identitet för att komma åt Azure Key Vault-Windows-Azure AD
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure Key Vault.
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
ms.openlocfilehash: 668d3cb044512220ff7afbc165c77da704a9a5d7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107523"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur en virtuell Windows-dator (VM) kan använda en systemtilldelad hanterad identitet för att få åtkomst till [Azure Key Vault](../../key-vault/general/overview.md). Key Vault fungerar som en start, gör det möjligt för ditt klient program att använda en hemlighet för att komma åt resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänst identiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan även autentiseringsinformation i din kod.

Lär dig att:

> [!div class="checklist"]
> * Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault
> * Få ett åtkomsttoken med hjälp av den virtuella datorns identitet och använd den när du hämtar hemligheten från Key Vault 

## <a name="prerequisites"></a>Förutsättningar

- Förståelse för hanterade identiteter. Om du inte känner till funktionen för hanterade identiteter för Azure-resurser kan du läsa igenom den här [översikten](overview.md). 
- Ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Ägarens behörigheter i lämpligt omfång (din prenumeration eller resurs grupp) för att utföra nödvändiga steg för att skapa resurser och roll hantering. Information om rolltilldelning finns i [Använda rollbaserad åtkomstkontroll för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md).
- Du behöver också en virtuell Windows-dator som har systemtilldelade hanterade identiteter aktiverade.
  - Om du behöver skapa en virtuell dator för den här självstudien kan du följa artikeln [skapa en virtuell dator med systemtilldelad identitet aktive rad](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning  

I det här avsnittet visas hur du beviljar din VM-åtkomst till en hemlighet som lagras i en Key Vault. Med hjälp av hanterade identiteter för Azure-resurser kan din kod hämta åtkomsttoken för att autentisera mot resurser som har stöd för Azure AD-autentisering.Men alla Azure-tjänster stöder inte Azure Active Directory-autentisering. Om du vill använda hanterade identiteter för Azure-resurser med dessa tjänster lagrar du autentiseringsuppgifterna för tjänsten i Azure Key Vault och får åtkomst till Key Vault och hämtar autentiseringsuppgifterna med hjälp av den virtuella datorns hanterade identitet.

Börja med att skapa ett Key Vault och bevilja den virtuella datorns systemtilldelade hanterade identitet åtkomst till Key Vault.

1. Öppna Azure [Portal](https://portal.azure.com/)
1. Längst upp i det vänstra navigerings fältet väljer du **skapa en resurs**  
1. I rutan **Sök på Marketplace skriver du** in **Key Vault** och trycker på **RETUR**.  
1. Välj **Key Vault** från resultaten.
1. Välj **Skapa**
1. Ange ett **namn** för det nya Key Vault.

    ![Skapa ett Key Vault-fönster](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

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

   ![Skapa en hemlighet](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Bevilja åtkomst

Den hanterade identitet som används av den virtuella datorn måste beviljas åtkomst för att läsa hemligheten som vi lagrar i Key Vault.

1. Navigera till din nyligen skapade Key Vault
1. Välj **åtkomst princip** på menyn på vänster sida.
1. Välj **Lägg till åtkomst princip**

   ![skärm för att skapa åtkomst princip för nyckel valv](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. I avsnittet **Lägg till åtkomst princip** under **Konfigurera från mall (valfritt)** väljer du **hemlig hantering** i den nedrullningsbara menyn.
1. Välj **Välj huvudkonto** och ange namnet på den virtuella dator som du skapade tidigare i sökfältet.  Välj den virtuella datorn i resultat listan och välj **Välj**.
1. Välj **Lägg till**
1. Välj **Spara**.


## <a name="access-data"></a>Åtkomst till data  

Det här avsnittet visar hur du hämtar en åtkomsttoken med hjälp av den virtuella datorns identitet och hur du hämtar hemligheten från Key Vault. Om du inte har PowerShell 4.3.1 eller senaste installerat måste du [ladda ned och installera den senaste versionen](/powershell/azure/).

Först måste vi använda den virtuella datorns systemtilldelade hanterade identitet för att få ett åtkomsttoken och autentisera mot Key Vault:
 
1. Gå till **Virtuella datorer** på portalen och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**.
2. Ange det **användar namn** och **lösen ord** som du lade till när du skapade den **virtuella Windows-datorn**.  
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.  
4. I PowerShell anropar du webbegäran för klientorganisationen om du vill få en token för den lokala värden i porten för den virtuella datorn.  

PowerShell-begäran:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Du kan se hur svaret ser ut så här:

![begäran med svar från token](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Extrahera sedan åtkomsttoken från svaret.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Slutligen använder du PowerShell-kommandot Invoke-WebRequest och hämtar hemligheten som du skapade tidigare i Key Vault, och skickar ditt åtkomsttoken i auktoriseringsrubriken.  Du behöver URL:en till ditt Key Vault, som finns i avsnittet **Essentials** på Key Vault-sidan **Översikt**.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

Svaret ser ut såhär: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

När du har hämtat hemligheten från Key Vault kan du använda den och autentisera mot en tjänst som kräver ett namn och lösenord.

## <a name="clean-up-resources"></a>Rensa resurser

När du vill rensa resurserna går du till [Azure Portal](https://portal.azure.com), väljer **resurs grupper**, letar upp och väljer den resurs grupp som skapades i processen för den här självstudien (till exempel `mi-test` ) och använder sedan kommandot **ta bort resurs grupp** .

Alternativt kan du också göra detta via [PowerShell eller CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en Windows VM-systemtilldelad hanterad identitet för att komma åt Azure Key Vault.  Läs mer om Azure Key Vault här:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)