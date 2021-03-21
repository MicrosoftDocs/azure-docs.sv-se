---
title: Anpassa RDP-egenskaper med PowerShell – Azure
description: Anpassa RDP-egenskaperna för virtuella Windows-datorer med PowerShell-cmdletar.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018315"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Anpassa Remote Desktop Protocol egenskaper (RDP) för en värd pool

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

Genom att anpassa en värd Pools egenskaper för Remote Desktop Protocol (RDP), till exempel flera skärmar och omdirigering av ljud, kan du leverera en optimal upplevelse för dina användare utifrån deras behov. Du kan anpassa RDP-egenskaperna i Windows Virtual Desktop genom att antingen använda Azure Portal eller genom att använda parametern *-CustomRdpProperty* i cmdleten **Update-AzWvdHostPool** .

Se [Inställningar för RDP-filer som stöds](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) för en fullständig lista över vilka egenskaper som stöds och deras standardvärden.

## <a name="default-rdp-file-properties"></a>Egenskaper för standard-RDP-fil

RDP-filer har som standard följande egenskaper:

|RDP-egenskap|På Skriv bordet|Som en RemoteApp|
|---|---|---|
|Läge för flera övervakare|Inaktiverad|Enabled|
|Aktiverade enhets omdirigeringar|Enheter, Urklipp, skrivare, COM-portar och smartkort|Enheter, urklipp och skrivare|
|Fjärr ljud läge|Spela lokalt|Spela lokalt|

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar följer du anvisningarna i [Konfigurera Windows Virtual Desktop PowerShell-modulen](powershell-module.md) för att konfigurera din PowerShell-modul och logga in på Azure.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Konfigurera RDP-egenskaper i Azure Portal

Konfigurera RDP-egenskaperna i Azure Portal:

1. Logga in på Azure på <https://portal.azure.com> .
2. Ange **det virtuella Windows-skrivbordet** i Sök fältet.
3. Under tjänster väljer du **virtuellt Windows-skrivbord**.
4. På sidan Windows Virtual Desktop väljer du **lagringspooler** på menyn till vänster på skärmen.
5. Välj **namnet på den modempool** som du vill uppdatera.
6. Välj **RDP-egenskaper** på menyn på vänster sida av skärmen.
7. Ange den egenskap som du vill använda.
   - Du kan också öppna fliken **Avancerat** och lägga till RDP-egenskaperna i ett semikolonavgränsat format som PowerShell-exemplen i följande avsnitt.
8. När du är klar väljer du **Spara** för att spara ändringarna.

I nästa avsnitt får du veta hur du redigerar anpassade RDP-egenskaper manuellt i PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Lägga till eller redigera en enskild anpassad RDP-egenskap

Om du vill lägga till eller redigera en enskild anpassad RDP-egenskap kör du följande PowerShell-cmdlet:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Om du vill kontrol lera om cmdleten du nyss körde har uppdaterat egenskapen kör du denna cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Om du till exempel söker efter egenskapen "audiocapturemode" i en pool med namnet 0301HP anger du denna cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Lägga till eller redigera flera anpassade RDP-egenskaper

Om du vill lägga till eller redigera flera anpassade RDP-egenskaper kör du följande PowerShell-cmdletar genom att ange de anpassade RDP-egenskaperna som en semikolonavgränsad sträng:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Du kan kontrol lera att RDP-egenskapen har lagts till genom att köra följande cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Baserat på vårt tidigare cmdlet-exempel, skulle din cmdlet se ut så här om du konfigurerar flera RDP-egenskaper på 0301HP-adresspoolen:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Återställ alla anpassade RDP-egenskaper

Du kan återställa enskilda anpassade RDP-egenskaper till sina standardvärden genom att följa anvisningarna i [lägga till eller redigera en enskild anpassad RDP-egenskap](#add-or-edit-a-single-custom-rdp-property), eller så kan du återställa alla anpassade RDP-egenskaper för en värd pool genom att köra följande PowerShell-cmdlet:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Kontrol lera att du har tagit bort inställningen genom att ange denna cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat RDP-egenskaperna för en specifik adresspool kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. I nästa instruktions guider får du information om hur du ansluter till en session med valfri klient:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-10.md)
- [Ansluta med webbklienten](connect-web.md)
- [Ansluta med Android-klienten](connect-android.md)
- [Ansluta med macOS-klienten](connect-macos.md)
- [Ansluta med iOS-klienten](connect-ios.md)