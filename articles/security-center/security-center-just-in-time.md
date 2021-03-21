---
title: Just-in-Time-åtkomst för virtuella datorer i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur just-in-Time VM Access (JIT) i Azure Security Center hjälper dig att styra åtkomsten till dina virtuella Azure-datorer.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916158"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Skydda dina hanterings portar med just-in-Time-åtkomst

Lås inkommande trafik till din Azure-Virtual Machines med Azure Security Centers funktioner för virtuell dator med JIT (just-in-Time) (VM). Detta minskar risken för attacker och ger enkel åtkomst när du behöver ansluta till en virtuell dator.

En fullständig förklaring av hur JIT fungerar och den underliggande logiken finns i [förklaring för just-in-Time](just-in-time-explained.md).

Den här sidan lär dig hur du inkluderar JIT i ditt säkerhets program. Du lär dig följande: 

- **Aktivera JIT på dina virtuella datorer** – du kan aktivera JIT med dina egna anpassade alternativ för en eller flera virtuella datorer med hjälp av Security Center, PowerShell eller REST API. Du kan också aktivera JIT med standard, hårdkodade parametrar från virtuella Azure-datorer. När det här alternativet är aktiverat låser sig JIT inkommande trafik till dina virtuella Azure-datorer genom att skapa en regel i nätverks säkerhets gruppen.
- **Begär åtkomst till en virtuell dator som har JIT aktiverat** – målet med JIT är att se till att även om den inkommande trafiken är låst, Security Center fortfarande ger enkel åtkomst till virtuella datorer när det behövs. Du kan begära åtkomst till en JIT-aktiverad virtuell dator från Security Center, Azure Virtual Machines, PowerShell eller REST API.
- **Granska aktiviteten** – för att säkerställa att dina virtuella datorer är korrekt skyddade kan du granska åtkomsten till dina JIT-aktiverade virtuella datorer som en del av dina vanliga säkerhets kontroller.   



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Virtuella datorer som stöds:|![Ja ](./media/icons/yes-icon.png) distribuerade virtuella datorer via Azure Resource Manager.<br>![Inga ](./media/icons/no-icon.png) virtuella datorer har distribuerats med klassiska distributions modeller. [Läs mer om de här distributions modellerna](../azure-resource-manager/management/deployment-models.md).<br>![Inga ](./media/icons/no-icon.png) virtuella datorer skyddas av Azure-brandväggar som styrs av [Azure Firewall Manager](../firewall-manager/overview.md)|
|Nödvändiga roller och behörigheter:|**Reader** -och **SecurityReader** -roller kan båda Visa JIT-status och parametrar.<br>Om du vill skapa anpassade roller som kan fungera med JIT, se [vilka behörigheter som krävs för att konfigurera och använda JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Om du vill skapa en roll med minst privilegier för användare som behöver begära JIT-åtkomst till en virtuell dator och inte utföra några andra JIT-åtgärder, använder du [set-JitLeastPrivilegedRole-skriptet](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) från community-sidorna för Security Center GitHub.|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="enable-jit-vm-access"></a>Aktivera JIT VM-åtkomst <a name="jit-configure"></a>

Du kan aktivera JIT VM-åtkomst med dina egna anpassade alternativ för en eller flera virtuella datorer som använder Security Center eller program mässigt. 

Du kan också aktivera JIT med standard, hårdkodade parametrar från virtuella Azure-datorer.

Vart och ett av dessa alternativ beskrivs i en separat flik nedan.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Aktivera JIT på dina virtuella datorer från Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Konfigurera JIT VM-åtkomst i Azure Security Center":::

Från Security Center kan du aktivera och konfigurera JIT VM-åtkomsten.

1. Öppna Azure Defender-instrumentpanelen och i det avancerade skydds avsnittet väljer du **VM-åtkomst just-in-Time**.

    Den **just-in-Time-** sidan för VM-åtkomst öppnas med de virtuella datorerna grupperade på följande flikar:

    - **Konfigurerade** – virtuella datorer som redan har kon figurer ATS för att stödja VM-åtkomst just-in-Time. Den konfigurerade fliken i varje virtuell dator visar:
        - antalet godkända JIT-begäranden under de senaste sju dagarna
        - datum och tid för senaste åtkomst
        - anslutnings informationen har kon figurer ATS
        - den senaste användaren
    - **Inte konfigurerad** – virtuella datorer utan JIT aktive rad, men som kan stödja JIT. Vi rekommenderar att du aktiverar JIT för dessa virtuella datorer.
    - Virtuella datorer som inte **stöds** utan JIT är aktiverade och som inte stöder funktionen. Den virtuella datorn kan vara på den här fliken av följande orsaker:
      - Nätverks säkerhets gruppen saknas (NSG) – JIT kräver att en NSG har kon figurer ATS
      - Klassisk virtuell dator – JIT stöder virtuella datorer som distribueras via Azure Resource Manager, inte den klassiska distributionen. [Läs mer om klassiska kontra Azure Resource Manager distributions modeller](../azure-resource-manager/management/deployment-models.md).
      - Annan – din virtuella dator kan finnas på den här fliken om JIT-lösningen är inaktive rad i prenumerationens eller resurs gruppens säkerhets princip.

1. På fliken **inte konfigurerad** markerar du de virtuella datorer som du vill skydda med JIT och väljer **Aktivera JIT på virtuella datorer**. 

    Sidan JIT VM Access öppnas och visar de portar som Security Center rekommenderar att du skyddar:
    - 22 – SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM

    Om du vill godkänna standardinställningarna väljer du **Spara**.

1. Så här anpassar du JIT-alternativen:

    - Lägg till anpassade portar med knappen **Lägg till** . 
    - Ändra en av standard portarna genom att välja den i listan.

    För varje port (anpassat och standard) innehåller fönstret **Lägg till port konfiguration** följande alternativ:

    - **Protokoll**– det protokoll som tillåts på den här porten när en begäran godkänns
    - **Tillåtna käll**-IP-adresser – IP-intervall som tillåts på den här porten när en begäran godkänns
    - **Maximal begär ande tid**– maximalt tids period då en bestämd port kan öppnas

     1. Ställ in port säkerheten på dina behov.

     1. Välj **OK**.

1. Välj **Spara**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Redigera JIT-konfigurationen på en JIT-aktiverad virtuell dator med hjälp av Security Center <a name="jit-modify"></a>

Du kan ändra en VM: s just-in-Time-konfiguration genom att lägga till och konfigurera en ny port för att skydda den virtuella datorn, eller genom att ändra någon annan inställning som är relaterad till en redan skyddad port.

Redigera befintliga JIT-regler för en virtuell dator:

1. Öppna Azure Defender-instrumentpanelen och välj **adaptiva program kontroller** i avsnittet Avancerat skydd.

1. Från fliken **konfigurerad** högerklickar du på den virtuella dator som du vill lägga till en port i och väljer Redigera. 

    ![Redigera en JIT VM Access-konfiguration i Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. Under **konfiguration av JIT VM-åtkomst** kan du antingen redigera befintliga inställningar för en redan skyddad port eller lägga till en ny anpassad port.

1. När du är klar med att redigera portarna väljer du **Spara**.
 


### <a name="azure-virtual-machines"></a>[**Virtuella Azure-datorer**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Aktivera JIT på dina virtuella datorer från virtuella Azure-datorer

Du kan aktivera JIT på en virtuell dator från sidorna för virtuella Azure-datorer i Azure Portal.

![Konfigurera JIT VM-åtkomst på virtuella Azure-datorer](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Om en virtuell dator redan har aktiverat just-in-Time, och du går till konfigurations sidan, ser du att just-in-Time är aktiverat och att du kan använda länken för att öppna sidan för just-in-Time-åtkomst till virtuell dator i Security Center och Visa och ändra inställningarna.

1. Sök efter och välj **virtuella datorer** från [Azure Portal](https://ms.portal.azure.com). 

1. Välj den virtuella dator som du vill skydda med JIT.

1. I menyn väljer du **konfiguration**.

1. Under **just-in-Time-åtkomst** väljer du **Aktivera just-in-Time**. 

    Detta möjliggör just-in-Time-åtkomst för den virtuella datorn med följande standardinställningar:

    - Windows-datorer:
        - RDP-port 3389
        - Tre timmar med högsta tillåtna åtkomst
        - Tillåtna käll-IP-adresser har angetts till valfri
    - Linux-datorer:
        - SSH-port 22
        - Tre timmar med högsta tillåtna åtkomst
        - Tillåtna käll-IP-adresser har angetts till valfri

1. Om du vill redigera något av dessa värden eller lägga till fler portar i JIT-konfigurationen använder Azure Security Center sidan just-in-Time:

    1. Från Security Center menyn väljer du **VM-åtkomst just-in-Time**.

    1. Från fliken **konfigurerad** högerklickar du på den virtuella dator som du vill lägga till en port i och väljer Redigera. 

        ![Redigera en JIT VM Access-konfiguration i Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. Under **konfiguration av JIT VM-åtkomst** kan du antingen redigera befintliga inställningar för en redan skyddad port eller lägga till en ny anpassad port.

    1. När du är klar med att redigera portarna väljer du **Spara**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Aktivera JIT på dina virtuella datorer med PowerShell

Använd den officiella Azure Security Center PowerShell-cmdleten för att aktivera just-in-Time VM-åtkomst från PowerShell `Set-AzJitNetworkAccessPolicy` .

**Exempel** – aktivera just-in-Time VM-åtkomst på en angiven virtuell dator med följande regler:

* Stäng portarna 22 och 3389
* Ange ett maximalt tidsintervall på 3 timmar för varje så att de kan öppnas per godkänd begäran
* Tillåt den användare som begär åtkomst att kontrol lera Källans IP-adresser
* Tillåt den användare som begär åtkomst att upprätta en lyckad session vid en godkänd just-in-Time-åtkomstbegäran

Följande PowerShell-kommandon skapar den här JIT-konfigurationen:

1. Tilldela en variabel som innehåller just-in-Time-regler för VM-åtkomst för en virtuell dator:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Sätt i den virtuella datorns åtkomst regler för just-in-Time-VM i en matris:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Konfigurera åtkomst reglerna för just-in-Time-VM på den valda virtuella datorn:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Använd parametern-name för att ange en virtuell dator. Om du till exempel vill upprätta JIT-konfigurationen för två olika virtuella datorer, VM1 och VM2, använder du: ```Set-AzJitNetworkAccessPolicy -Name VM1``` och ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST-API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Aktivera JIT på dina virtuella datorer med hjälp av REST API

Just-in-Time-funktionen för VM-åtkomst kan användas via Azure Security Center-API: et. Använd det här API: et för att hämta information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator med mera. 

Läs mer om [JIT-principer för nätverks åtkomst](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Begära åtkomst till en JIT-aktiverad virtuell dator

Du kan begära åtkomst till en JIT-aktiverad virtuell dator från Azure Portal (i Security Center eller Azure Virtual Machines) eller program mässigt.

Vart och ett av dessa alternativ beskrivs i en separat flik nedan.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Begär åtkomst till en JIT-aktiverad virtuell dator från Azure Security Center 

När en virtuell dator har JIT-aktiverad måste du begära åtkomst för att ansluta till den. Du kan begära åtkomst på något av de sätt som stöds, oavsett hur du har aktiverat JIT.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Begär JIT-åtkomst från Security Center":::

1. På sidan **just-in-Time VM-åtkomst** väljer du fliken **konfigurerad** .

1. Markera de virtuella datorer som du vill ha åtkomst till.

    - Ikonen i kolumnen **anslutnings information** visar om JIT är aktiverat i nätverks säkerhets gruppen eller brand väggen. Om det är aktiverat på båda visas bara brand Väggs ikonen.

    - Kolumnen **anslutnings information** innehåller den information som krävs för att ansluta den virtuella datorn och dess öppna portar.

1. Välj **begär åtkomst**. Fönstret **begär åtkomst** öppnas.

1. Under **begär åtkomst**, för varje virtuell dator, konfigurerar du de portar som du vill öppna och käll-IP-adresserna som porten är öppen på och tids perioden som porten ska vara öppen för. Det går bara att begära åtkomst till de konfigurerade portarna. Varje port har en längsta tillåten tid som härletts från den JIT-konfiguration som du har skapat.

1. Välj **öppna portar**.

> [!NOTE]
> Om en användare som begär åtkomst ligger bakom en proxyserver kanske inte alternativet **min IP** fungerar. Du kan behöva definiera det fullständiga IP-adressintervallet för organisationen.



### <a name="azure-virtual-machines"></a>[**Virtuella Azure-datorer**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Begär åtkomst till en JIT-aktiverad virtuell dator från sidan Connect på den virtuella Azure-datorn

När en virtuell dator har JIT-aktiverad måste du begära åtkomst för att ansluta till den. Du kan begära åtkomst på något av de sätt som stöds, oavsett hur du har aktiverat JIT.

  >![JIT just-in-Time-begäran](./media/security-center-just-in-time/jit-request-vm.png)


Så här begär du åtkomst från virtuella Azure-datorer:

1. Öppna sidorna för virtuella datorer i Azure Portal.

1. Välj den virtuella dator som du vill ansluta till och öppna sidan **Anslut** .

    Azure kontrollerar om JIT är aktiverat på den virtuella datorn.

    - Om JIT inte är aktiverat för den virtuella datorn uppmanas du att aktivera det.

    - Om JIT har Aktiver ATS väljer du **begär åtkomst** för att skicka en åtkomstbegäran med den begärda IP-adressen, tidsintervallet och portarna som har kon figurer ATS för den virtuella datorn.

> [!NOTE]
> När en begäran har godkänts för en virtuell dator som skyddas av Azure-brandväggen ger Security Center användaren rätt anslutnings information (port mappningen från tabellen DNAT) som ska användas för att ansluta till den virtuella datorn.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Begära åtkomst till en JIT-aktiverad virtuell dator med PowerShell

I följande exempel kan du se en just-in-Time-begäran om VM-åtkomst till en angiven virtuell dator där port 22 begärs att öppnas för en speciell IP-adress och under en angiven tids period:

Kör följande i PowerShell:

1. Konfigurera åtkomst egenskaper för VM-begäran:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Infoga parametrarna för VM-åtkomstbegäran i en matris:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Skicka begär ande åtkomst (Använd resurs-ID: t från steg 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Läs mer i [PowerShell-cmdlet-dokumentationen](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST-API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Begär åtkomst till en JIT-aktiverad virtuell dator med hjälp av REST API

Just-in-Time-funktionen för VM-åtkomst kan användas via Azure Security Center-API: et. Använd det här API: et för att hämta information om konfigurerade virtuella datorer, lägga till nya, begära åtkomst till en virtuell dator med mera. 

Läs mer om [JIT-principer för nätverks åtkomst](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Granska JIT Access-aktivitet i Security Center

Du kan få insikter om VM-aktiviteter med loggs ökning. Så här visar du loggarna:

1. Välj fliken **konfigurerad** från **just-in-Time VM-åtkomst**.

1. Öppna menyn med tre punkter i slutet av raden för den virtuella dator som du vill granska.
 
1. Välj **aktivitets logg** på menyn.

   ![Välj JIT-aktivitets logg för just-in-Time](./media/security-center-just-in-time/jit-select-activity-log.png)

   Aktivitets loggen innehåller en filtrerad vy över tidigare åtgärder för den virtuella datorn tillsammans med tid, datum och prenumeration.

1. Om du vill hämta logg informationen väljer du **Hämta som CSV**.








## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar och använder just-in-Time-åtkomst till virtuella datorer. Om du vill veta varför JIT bör användas läser du artikeln begrepp som förklarar de hot som det är försvara mot:

> [!div class="nextstepaction"]
> [JIT-Förklarning](just-in-time-explained.md)