---
title: 'För hands version: Distribuera en betrodd virtuell lanserings dator'
description: Distribuera en virtuell dator som använder betrodd start.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 6499e4b3404a255025f88488e73da1efb6449296
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075959"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Distribuera en virtuell dator med Trusted Launch Enabled (för hands version)

[Betrodd start](trusted-launch.md) är ett sätt att förbättra säkerheten för virtuella datorer i [generation 2](generation-2.md) . Betrodd lansering skyddar mot avancerade och permanenta angrepps tekniker genom att kombinera infrastruktur tekniker som vTPM och säker start.

> [!IMPORTANT]
> Den betrodda starten är för närvarande en offentlig för hands version.
> 
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Distribuera med portalen

Skapa en virtuell dator med betrodd start aktiverat.

1. Logga in på Azure- [portalen](https://aka.ms/TL_preview).
   > [!NOTE] 
   > Portal länken är unik för för hands versionen av betrodd start.
   >  
2. Sök efter **Virtual Machines**.
3. Under **tjänster** väljer du **virtuella datorer**.
4. På sidan **virtuella datorer** väljer du **Lägg till** och väljer sedan **virtuell dator**.
5. Under **projekt information** kontrollerar du att rätt prenumeration har valts.
6. Under **resurs grupp** väljer du **Skapa nytt** och skriver ett namn för din resurs grupp eller väljer en befintlig resurs grupp i list rutan.
7. Under **instans information** anger du ett namn för namnet på den virtuella datorn och väljer en region som stöder [betrodd start](trusted-launch.md#public-preview-limitations).
8. Under **bild** väljer du en [avbildning som stöder betrodd start](trusted-launch.md#public-preview-limitations). Du kanske bara ser gen 1-versionen av avbildningen, som du kan gå vidare till nästa steg.
9. Växla över till fliken **Avancerat** genom att markera den överst på sidan.
10. Rulla ned till avsnittet **VM generation** och välj sedan **gen 2**.
11. Medan du fortfarande är på fliken **Avancerat** rullar du ned till **betrodd start** och väljer sedan kryss rutan **betrodd start** . Detta gör att ytterligare två alternativ visas – säker start och vTPM. Välj lämpliga alternativ för distributionen.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Skärm bild som visar alternativen för betrodd start.":::

12. Gå tillbaka till fliken **grundläggande** inställningar under **bild** och se till att du ser följande meddelande: **den här avbildningen har stöd för förhands granskning av betrodda starta. Konfigurera på fliken Avancerat**. Du bör nu välja gen 2-avbildningen.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Skärm bild som visar meddelandet som bekräftar att det här är en Gen2-avbildning som stöder betrodd start.":::

13. Välj en VM-storlek som har stöd för betrodd start. Se listan över [storlekar som stöds](trusted-launch.md#public-preview-limitations).
14. Fyll i **administratörens konto** information och sedan **regler för inkommande port**.
15. Längst ned på sidan väljer du **Granska + skapa**
16. På sidan **skapa en virtuell dator** kan du se information om den virtuella dator som du ska distribuera. När du är klar väljer du **Skapa**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot på sidan verifiering, som visar de betrodda start alternativen ingår.":::


Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="deploy-using-a-template"></a>Distribuera med hjälp av en mall

Du kan distribuera betrodda virtuella datorer med en snabb starts mall:

**Linux**:    
[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Visa och uppdatera

Du kan visa den betrodda start konfigurationen för en befintlig virtuell dator genom att besöka **översikts** sidan för den virtuella datorn i portalen.

Om du vill ändra den betrodda start konfigurationen väljer du **konfiguration** under avsnittet **Inställningar** i den vänstra menyn. Du kan aktivera eller inaktivera säker start och vTPM från avsnittet för **betrodd start** . Välj **Spara** längst upp på sidan när du är färdig. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Skärm bild av hur du ändrar den betrodda start konfigurationen.":::

Om den virtuella datorn körs visas ett meddelande om att den virtuella datorn kommer att startas om för att tillämpa den ändrade konfigurationen för betrodd start. Välj **Ja** och vänta tills den virtuella datorn har startats om för att ändringarna ska börja gälla.


## <a name="verify-secure-boot-and-vtpm"></a>Verifiera säker start och vTPM

Du kan kontrol lera att säker start och vTPM är aktiverade på den virtuella datorn.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: verifiera om säker start körs

SSH till den virtuella datorn och kör sedan följande kommando: 

```bash
mokutil --sb-state
```

Om säker start är aktiverat kommer kommandot att returnera:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: verifiera om vTPM har Aktiver ATS

SSH till den virtuella datorn. Kontrol lera om tpm0-enheten finns: 

```bash
ls /dev/tpm0
```

Om vTPM har Aktiver ATS kommer kommandot att returnera:

```output
/dev/tpm0
```

Om vTPM är inaktiverat kommer kommandot att returnera:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: verifiera att säker start körs

Anslut till den virtuella datorn med hjälp av fjärr skrivbord och kör sedan `msinfo32.exe` .

Kontrol lera att läget för säker start är **aktiverat** i den högra rutan.

## <a name="enable-the-azure-security-center-experience"></a>Aktivera Azure Security Center upplevelsen

Om du vill aktivera Azure Security Center Visa information om dina betrodda virtuella datorer måste du aktivera flera principer. Det enklaste sättet att aktivera principerna är genom att distribuera den här [Resource Manager-mallen](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) till din prenumeration. 

Klicka på knappen nedan om du vill distribuera principerna till din prenumeration:

[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Mallen behöver bara distribueras en gång per prenumeration. Den installeras automatiskt `GuestAttestation` och `AzureSecurity` tilläggen på alla virtuella datorer som stöds. Försök att distribuera om mallen igen om du får fel meddelanden.

För att få vTPM och säkra start rekommendationer för betrodda virtuella datorer, se [Lägg till ett anpassat initiativ till din prenumeration](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Signera saker för säker start på Linux

I vissa fall kan du behöva signera saker för UEFI säker start.  Du kan till exempel behöva gå igenom [hur du ska logga in saker för säker start](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) för Ubuntu. I dessa fall måste du ange MOK-verktyget registrera nycklar för den virtuella datorn. För att göra detta måste du använda Azures serie konsol för att få åtkomst till MOK-verktyget.

1. Aktivera Azures serie konsol för Linux. Mer information finns i [serie konsol för Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter **virtuella datorer** och välj den virtuella datorn i listan.
1. I den vänstra menyn, under **support + fel sökning**, väljer du **seriell konsol**. En sida öppnas till höger med serie konsolen.
1. Logga in på den virtuella datorn med Azures serie konsol. För **inloggning** anger du det användar namn som du använde när du skapade den virtuella datorn. Till exempel *azureuser*. När du uppmanas till det anger du lösen ordet som är associerat med användar namnet.
1. När du är inloggad använder `mokutil` du för att importera den offentliga nyckel `.der` filen.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Starta om datorn från Azures serie konsol genom att skriva `sudo reboot` . En nedräkning på 10 andra kommer att börja.
1. Tryck på upp-eller ned-tangenten för att avbryta nedräkningen och vänta i UEFI-konsol läge. Om timern inte avbryts fortsätter start processen och alla ändringar i MOK går förlorade.
1. Välj lämplig åtgärd på MOK-Verktyg-menyn.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Skärm bild som visar de tillgängliga alternativen på MOK-hanterings menyn i serie konsolen.":::


## <a name="next-steps"></a>Nästa steg

Läs mer om [betrodda start](trusted-launch.md) -och [generation 2](generation-2.md) -datorer.
