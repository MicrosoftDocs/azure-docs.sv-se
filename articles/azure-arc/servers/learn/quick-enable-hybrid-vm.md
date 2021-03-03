---
title: Ansluta hybrid dator med Azure Arc-aktiverade servrar
description: Lär dig hur du ansluter och registrerar en hybrid dator med Azure Arc-aktiverade servrar.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: c52b8d1f7098a7a2a88a9770a3b768b7fea31775
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646834"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>Snabb start: ansluta hybrid datorer med Azure Arc-aktiverade servrar

Med [Azure Arc-aktiverade servrar](../overview.md) kan du hantera och styra dina Windows-och Linux-datorer som finns i alla lokala, Edge-och multimoln miljöer. I den här snabb starten distribuerar och konfigurerar du den anslutna dator agenten på en Windows-eller Linux-dator som finns utanför Azure för hantering av Arc-aktiverade servrar.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Att distribuera Arc-aktiverade servrar hybrid ansluten dator agent kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, med ett konto som är medlem i den lokala gruppen Administratörer.

* Innan du börjar måste du granska agent [kraven](../agent-overview.md#prerequisites) och kontrol lera följande:

    * Mål datorn kör ett [operativ system](../agent-overview.md#supported-operating-systems)som stöds.

    * Ditt konto beviljas tilldelning till de [nödvändiga Azure-rollerna](../agent-overview.md#required-permissions).

    * Om datorn ansluter via en brand vägg eller proxyserver för kommunikation via Internet kontrollerar du att URL: erna i [listan](../agent-overview.md#networking-configuration) inte är blockerade.

    * Azure Arc-aktiverade servrar stöder bara de regioner som anges [här](../overview.md#supported-regions).

> [!WARNING]
> Linux-värdnamnet eller Windows-datornamnet kan inte använda ett av de reserverade orden eller varumärkena i namnet, annars försöker registreringen av den anslutna datorn i Azure Miss lyckas. Se [lösa fel i reserverat resurs namn](../../../azure-resource-manager/templates/error-reserved-resource-name.md) för en lista över reserverade ord.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrera Azure-resurs leverantörer

Azure Arc-aktiverade servrar är beroende av följande Azure-resurs leverantörer i din prenumeration för att kunna använda den här tjänsten:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Registrera dem med följande kommandon:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generera installations skript

Skriptet för att automatisera hämtning, installation och upprättande av anslutningen till Azure Arc är tillgängligt från Azure Portal. Slutför processen genom att göra följande:

1. Starta tjänsten Azure Arc i Azure Portal genom att klicka på **alla tjänster** och sedan söka efter och välja **servrar – Azure-båge**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Sök efter Arc-aktiverade servrar i alla tjänster" border="false":::

1. På sidan **servrar – Azure-båge** väljer du **Lägg till** längst upp till vänster.

1. På sidan **Välj en metod** väljer du panelen **Lägg till servrar med interaktiv skript** och väljer sedan **skapa skript**.

1. På sidan **skapa skript** väljer du den prenumeration och resurs grupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras. Den här platsen kan vara samma eller olika, som resurs gruppens plats.

1. På sidan **förutsättningar** granskar du informationen och väljer sedan **Nästa: resursinformation**.

1. På sidan **Resursinformation** anger du följande:

    1. I list rutan **resurs grupp** väljer du den resurs grupp som datorn ska hanteras från.
    1. I list rutan **region** väljer du den Azure-region där du vill lagra metadata för servrarna.
    1. I list rutan **operativ system** väljer du det operativ system som skriptet ska konfigureras för att köras på.
    1. Om datorn kommunicerar via en proxyserver för att ansluta till Internet anger du IP-adressen för proxyservern eller det namn och port nummer som datorn ska använda för att kommunicera med proxyservern. Ange värdet i formatet `http://<proxyURL>:<proxyport>` .
    1. Välj **Nästa: Taggar**.

1. På sidan **taggar** granskar du standard **koderna för fysiska platser** och anger ett värde, eller så anger du en eller flera **anpassade taggar** som stöder dina standarder.

1. Välj **Nästa: Ladda ned och kör skript**.

1. På sidan **Ladda ned och kör skript** granskar du sammanfattnings informationen och väljer sedan **Hämta**. Om du fortfarande behöver göra ändringar väljer du **föregående**.

## <a name="install-the-agent-using-the-script"></a>Installera agenten med hjälp av skriptet

### <a name="windows-agent"></a>Windows-agent

1. Logga in på servern.

1. Öppna en upphöjd 64-bitars PowerShell-kommandotolk.

1. Ändra till den mapp eller resurs som du kopierade skriptet till och kör det på servern genom att köra `./OnboardingScript.ps1` skriptet.

### <a name="linux-agent"></a>Linux-Agent

1. Kör följande kommando för att installera Linux-agenten på mål datorn som kan kommunicera direkt med Azure:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Om mål datorn kommunicerar via en proxyserver kör du följande kommando:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa datorn i [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="En lyckad dator anslutning" border="false":::

## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat din Linux-eller Windows hybrid-dator och har anslutit till tjänsten är du redo att aktivera Azure Policy för att förstå efterlevnad i Azure.

Fortsätt till självstudien om du vill lära dig att identifiera Azure Arc-aktiverade datorer som inte har installerat Log Analytics-agenten:

> [!div class="nextstepaction"]
> [Skapa en principtilldelning för att identifiera icke-kompatibla resurser](tutorial-assign-policy-portal.md)
