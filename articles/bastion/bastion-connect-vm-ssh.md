---
title: Ansluta till en virtuell Linux-dator med Azure skydds
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Linux-dator med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588658"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ansluta med SSH till en virtuell Linux-dator med Azure skydds

Den här artikeln visar hur du på ett säkert och smidigt sätt kan använda SSH för virtuella Linux-datorer i ett virtuellt Azure-nätverk. Du kan ansluta till en virtuell dator direkt från Azure Portal. När du använder Azure skydds kräver inte virtuella datorer en klient, agent eller ytterligare program vara. Mer information om Azure-skydds finns i [översikten](bastion-overview.md).

Du kan använda Azure-skydds för att ansluta till en virtuell Linux-dator med SSH. Du kan använda både användar namn/lösen ord och SSH-nycklar för autentisering. Du kan ansluta till din virtuella dator med SSH-nycklar genom att använda antingen:

* En privat nyckel som du anger manuellt
* En fil som innehåller information om privat nyckel

Den privata SSH-nyckeln måste vara i ett format som börjar med  `"-----BEGIN RSA PRIVATE KEY-----"` och slutar med `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. Mer information finns i [skapa en Azure skydds-värd](./tutorial-create-host-portal.md). När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det här virtuella nätverket. 

När du använder skydds för att ansluta antar det att du använder RDP för att ansluta till en virtuell Windows-dator och SSH för att ansluta till dina virtuella Linux-datorer. Information om hur du ansluter till en virtuell Windows-dator finns i [ansluta till en VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Nödvändiga roller

Följande roller krävs för att upprätta en anslutning:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP-adress
* Läsarroll på Azure Bastion-resursen

### <a name="ports"></a>Portar

För att kunna ansluta till den virtuella Linux-datorn via SSH måste du ha följande portar öppna på den virtuella datorn:

* Inkommande port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Anslut: Använd användar namn och lösen ord

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Skärm bild som visar en översikt över en virtuell dator i Azure Portal med Anslut valt":::
1. När du har valt skydds klickar du på **Använd skydds**. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](./quickstart-host-portal.md).
1. På sidan **Anslut med Azure-skydds** anger du **användar namn** och **lösen ord**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Lösenordsverifiering":::
1. Välj **Anslut** för att ansluta till den virtuella datorn.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Anslut: Ange en privat nyckel manuellt

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Skärm bild som visar en översikt över en virtuell dator i Azure Portal med Anslut valt":::
1. När du har valt skydds klickar du på **Använd skydds**. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](./quickstart-host-portal.md).
1. På sidan **Anslut med Azure-skydds** anger du **användar namnet** och den **privata SSH-nyckeln**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Autentisering med privat SSH-nyckel":::
1. Ange din privata nyckel i text ytans **privata SSH-nyckel** (eller klistra in den direkt).
1. Välj **Anslut** för att ansluta till den virtuella datorn.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Anslut: använder en privat nyckel fil

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Skärm bild som visar en översikt över en virtuell dator i Azure Portal med Anslut valt":::
1. När du har valt skydds klickar du på **Använd skydds**. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](./quickstart-host-portal.md).
1. På sidan **Anslut med Azure-skydds** anger du **användar namnet** och den **privata SSH-nyckeln från den lokala filen**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="Fil för privat SSH-nyckel":::

1. Bläddra efter filen och välj sedan **Öppna**.
1. Välj **Anslut** för att ansluta till den virtuella datorn. När du klickar på Anslut öppnas SSH till den virtuella datorn direkt i Azure Portal. Den här anslutningen är över HTML5 med port 443 på skydds-tjänsten via den privata IP-adressen för den virtuella datorn.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Anslut: använder en privat nyckel som lagras i Azure Key Vault

>[!NOTE]
>Portal uppdateringen för den här funktionen håller på att distribueras till regioner.
>

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Skärm bild som visar en översikt över en virtuell dator i Azure Portal med Anslut valt":::
1. När du har valt skydds klickar du på **Använd skydds**. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](./quickstart-host-portal.md).
1. På sidan **Anslut med Azure-skydds** anger du **användar namnet** och väljer **SSH privat nyckel från Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Privat SSH-nyckel från Azure Key Vault":::
1. Välj List rutan **Azure Key Vault** och välj den resurs där du sparade din privata SSH-nyckel. Om du inte har konfigurerat en Azure Key Vault-resurs kan du läsa [skapa ett nyckel valv](../key-vault/general/quick-create-portal.md) och lagra din privata SSH-nyckel som ett nytt Key Vault hemligt värde.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Kontrol lera att du har en **lista** och **får** åtkomst till hemligheterna som lagras i Key Vault resursen. Om du vill tilldela och ändra åtkomst principer för din Key Vault-resurs, se [tilldela en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md).
1. Välj List rutan **Azure Key Vault hemlighet** och välj den Key Vault hemlighet som innehåller värdet för den privata SSH-nyckeln.
1. Välj **Anslut** för att ansluta till den virtuella datorn. När du klickar på **Anslut** öppnas SSH till den virtuella datorn direkt i Azure Portal. Den här anslutningen är över HTML5 med port 443 på skydds-tjänsten via den privata IP-adressen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure skydds finns i [vanliga frågor och svar om skydds](bastion-faq.md). 
