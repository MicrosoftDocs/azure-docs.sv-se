---
title: 'Förhandsversion: Distribuera en betrodd virtuell start-VM'
description: Distribuera en virtuell dator som använder betrodd start.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565924"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Distribuera en virtuell dator med betrodd start aktiverad (förhandsversion)

[Betrodd start](trusted-launch.md) är ett sätt att förbättra säkerheten för virtuella [datorer i generation 2.](generation-2.md) Betrodd start skyddar mot avancerade och beständiga angreppstekniker genom att kombinera infrastrukturtekniker som vTPM och säker start.

> [!IMPORTANT]
> Betrodd start är för närvarande i offentlig förhandsversion.
> 
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Distribuera med hjälp av portalen

Skapa en virtuell dator med betrodd start aktiverad.

1. Logga in på Azure [Portal.](https://aka.ms/TL_preview)
   > [!NOTE] 
   > Portallänken är unik för den betrodda startförhandsvisningen.
   >  
2. Sök efter **Virtual Machines**.
3. Under **Tjänster** väljer du **Virtuella datorer**.
4. På sidan **Virtuella datorer** väljer du **Lägg till** och sedan **Virtuell dator.**
5. Kontrollera **att rätt** prenumeration har valts under Projektinformation.
6. Under **Resursgrupp** väljer du **Skapa ny** och anger ett namn för resursgruppen eller väljer en befintlig resursgrupp i listrutan.
7. Under **Instansinformation** anger du ett namn för den virtuella datorns namn och väljer en region som stöder betrodd [start.](trusted-launch.md#public-preview-limitations)
8. Under **Avbildning** väljer du en Gen 2-avbildning [som stöder betrodd start.](trusted-launch.md#public-preview-limitations) Kontrollera att du ser följande meddelande: Den här **avbildningen stöder förhandsversionen av betrodd start. Konfigurera på fliken Avancerat.**
   > [!TIP]
   > Om du inte ser Gen 2-versionen av avbildningen som  du vill använda i listrutan väljer du Visa alla avbildningar och ändrar sedan **vm-genereringsfiltret** så att endast Gen 2-avbildningar visas. Hitta avbildningen i listan och använd sedan **listrutan** Välj för att välja Gen 2-versionen.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Skärmbild som visar meddelandet som bekräftar att det här är en gen2-avbildning som stöder betrodd start.":::

13. Välj en VM-storlek som stöder betrodd start. Se listan över [storlekar som stöds.](trusted-launch.md#public-preview-limitations)
14. Fyll i **kontoinformationen administratör** och sedan **regler för inkommande portar.** 
1. Växla till fliken **Avancerat** genom att välja den överst på sidan.
1. Rulla ned till avsnittet **om VM-generering.** Kontrollera att **Gen 2** är markerat.
1. Medan du fortfarande är **på** fliken Avancerat rullar du ned **till Betrodd** start och markerar sedan **kryssrutan Betrodd** start. Detta gör att ytterligare två alternativ visas – Säker start och vTPM. Välj lämpliga alternativ för distributionen.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Skärmbild som visar alternativen för betrodd start.":::

15. Längst ned på sidan väljer du **Granska + skapa**
16. På sidan **Skapa en virtuell dator** kan du se information om den virtuella dator som du håller på att distribuera. När du är klar väljer du **Skapa**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="En bild av valideringssidan som visar de betrodda startalternativen ingår.":::


Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="deploy-using-a-template"></a>Distribuera med hjälp av en mall

Du kan distribuera betrodda start-VM:ar med hjälp av en snabbstartsmall:

**Linux**:    
[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Visa och uppdatera

Du kan visa konfigurationen för betrodd start för en befintlig virtuell dator genom att **gå till sidan** Översikt för den virtuella datorn i portalen.

Om du vill ändra konfigurationen för betrodd start väljer du Konfiguration i **den vänstra menyn** under **avsnittet** Inställningar. Du kan aktivera eller inaktivera Säker start och vTPM från avsnittet **Betrodd start.** Välj **Spara** överst på sidan när du är klar. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Skärmbild av hur du ändrar konfigurationen för betrodd start.":::

Om den virtuella datorn körs får du ett meddelande om att den virtuella datorn startas om för att tillämpa den ändrade konfigurationen för betrodd start. Välj **Ja** och vänta sedan tills den virtuella datorn startas om för att ändringarna ska börja gälla.


## <a name="verify-secure-boot-and-vtpm"></a>Verifiera säker start och vTPM

Du kan kontrollera att säker start och vTPM är aktiverade på den virtuella datorn.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: verifiera om säker start körs

SSH till den virtuella datorn och kör sedan följande kommando: 

```bash
mokutil --sb-state
```

Om säker start är aktivera returnerar kommandot:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: verifiera om vTPM är aktiverat

SSH till den virtuella datorn. Kontrollera om tpm0-enheten finns: 

```bash
ls /dev/tpm0
```

Om vTPM är aktiverat returnerar kommandot:

```output
/dev/tpm0
```

Om vTPM är inaktiverat returnerar kommandot:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: verifiera att säker start körs

Anslut till den virtuella datorn med hjälp av fjärrskrivbordet och kör sedan `msinfo32.exe` .

I den högra rutan kontrollerar du att Säker starttillstånd är **PÅ**.

## <a name="enable-the-azure-security-center-experience"></a>Aktivera Azure Security Center upplevelse

Om du Azure Security Center att visa information om dina betrodda virtuella start-VM:ar måste du aktivera flera principer. Det enklaste sättet att aktivera principerna är att distribuera den här [Resource Manager mallen](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) till din prenumeration. 

Välj knappen nedan för att distribuera principerna till din prenumeration:

[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Mallen behöver bara distribueras en gång per prenumeration. Den installerar och tillägg `GuestAttestation` automatiskt på alla virtuella datorer som `AzureSecurity` stöds. Om du får fel kan du försöka distribuera om mallen igen.

Information om hur du hämtar rekommendationer för vTPM och säker start för virtuella datorer med betrodd start finns i [Lägga till ett anpassat initiativ i din prenumeration.](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Signera saker för Säker start i Linux

I vissa fall kan du behöva signera saker för säker UEFI-start.  Du kan till exempel behöva gå igenom Så här [signerar du saker för Säker start](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) för Ubuntu. I dessa fall måste du ange MOK-verktygets registreringsnycklar för den virtuella datorn. Om du vill göra detta måste du använda Azure-seriekonsolen för att få åtkomst till UPPL-verktyget.

1. Aktivera Azure-seriekonsolen för Linux. Mer information finns i [Seriekonsol för Linux.](/troubleshoot/azure/virtual-machines/serial-console-linux)
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter **Virtuella datorer** och välj den virtuella datorn i listan.
1. I den vänstra menyn under **Support + felsökning** väljer du **Seriell konsol**. En sida öppnas till höger med seriekonsolen.
1. Logga in på den virtuella datorn med Azure-seriekonsolen. För **inloggning** anger du det användarnamn som du använde när du skapade den virtuella datorn. Till exempel *azureuser*. När du uppmanas till det anger du lösenordet som är associerat med användarnamnet.
1. När du har loggat in använder du `mokutil` för att importera den offentliga `.der` nyckelfilen.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Starta om datorn från Azure-seriekonsolen genom att skriva `sudo reboot` . En nedräkning på 10 sekunder börjar.
1. Tryck på upp- eller nedtangenten för att avbryta nedräkningen och vänta i UEFI-konsolläge. Om timern inte avbryts fortsätter startprocessen och alla KK-ändringar går förlorade.
1. Välj lämplig åtgärd på menyn för KK-verktyget.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Skärmbild som visar de tillgängliga alternativen på KK-hanteringsmenyn i seriekonsolen.":::


## <a name="next-steps"></a>Nästa steg

Läs mer om [betrodd start](trusted-launch.md) och virtuella datorer i [generation 2.](generation-2.md)
