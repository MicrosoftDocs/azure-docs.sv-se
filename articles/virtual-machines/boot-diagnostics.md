---
title: Azure Boot-diagnostik
description: Översikt över Azure Boot Diagnostics och Managed Boot Diagnostics
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: dc9f7dfdf33411f11d51734b48105be200874d02
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738126"
---
# <a name="azure-boot-diagnostics"></a>Azure Boot-diagnostik

Startdiagnostik är en fel söknings funktion för Azure Virtual Machines (VM) som gör det möjligt att diagnostisera VM-startfel. Med startdiagnostik kan en användare se statusen för den virtuella datorn när den startas genom att samla in information om seriell logg information och skärm dum par.

## <a name="boot-diagnostics-storage-account"></a>Lagrings konto för startdiagnostik
När du skapar en virtuell dator i Azure Portal aktive ras startdiagnostik som standard. Den rekommenderade versionen av startdiagnostik är att använda ett hanterat lagrings konto, eftersom det ger avsevärda prestanda förbättringar i tiden för att skapa en virtuell Azure-dator. Detta beror på att ett Azure-hanterat lagrings konto kommer att användas och tar bort den tid det tar att skapa ett nytt användar lagrings konto för att lagra startdiagnostikens data.

> [!IMPORTANT]
> Data blobbar för startdiagnostik (som består av loggar och ögonblicks bilds avbildningar) lagras i ett hanterat lagrings konto. Kunderna debiteras bara på använda GiBs av Blobbarna, inte på diskens etablerade storlek. Bildernas mätare används för fakturering av det hanterade lagrings kontot. Eftersom de hanterade kontona skapas på antingen standard-LRS eller standard-ZRS debiteras kunderna med $0,05/GB per månad enbart för storleken på deras diagnostikdata. Mer information om den här prissättningen finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Kunderna ser den här avgiften som är kopplad till deras resurs-URI för virtuella datorer. 

En alternativ starts upplevelse är att använda ett hanterat lagrings konto för användare. En användare kan antingen skapa ett nytt lagrings konto eller använda ett befintligt.
> [!NOTE]
> Användar hanterade lagrings konton som är kopplade till startdiagnostik kräver lagrings kontot och de associerade virtuella datorerna finns i samma prenumeration. 



## <a name="boot-diagnostics-view"></a>Vyn Boot Diagnostics
Alternativet startdiagnostik finns på bladet för den virtuella datorn under avsnittet *support och fel sökning* i Azure Portal. Om du väljer Boot Diagnostics visas en skärm bild och information om serie loggen. Serie loggen innehåller kernel-meddelanden och skärm bilden är en ögonblicks bild av den virtuella datorns aktuella tillstånd. Baserat på om den virtuella datorn kör Windows eller Linux bestämmer vad den förväntade skärm bilden skulle se ut. För Windows ser användarna en Skriv bords bakgrund och för Linux visas en inloggnings tolk.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Skärm bild av Linux Boot Diagnostics":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Skärm bild av Windows-startdiagnostik":::

## <a name="enable-managed-boot-diagnostics"></a>Aktivera hanterad startdiagnostik 
Hanterad startdiagnostik kan aktive ras via Azure Portal-, CLI-och ARM-mallarna. Det finns ännu inte stöd för att aktivera via PowerShell. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Aktivera hanterad startdiagnostik med Azure Portal
När du skapar en virtuell dator i Azure Portal är standardinställningen att startdiagnostik aktive ras med ett hanterat lagrings konto. Om du vill visa detta navigerar du till fliken *hantering* när du skapar en virtuell dator. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Skärm bild som aktiverar hanterad startdiagnostik under skapandet av virtuella datorer.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Aktivera hanterad startdiagnostik med CLI
Startdiagnostik med ett hanterat lagrings konto stöds i Azure CLI-2.12.0 och senare. Om du inte anger ett namn eller en URI för ett lagrings konto används ett hanterat konto. Mer information och kod exempel finns i [CLI-dokumentationen för startdiagnostik](/cli/azure/vm/boot-diagnostics).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Aktivera hanterad startdiagnostik med Azure Resource Manager ARM-mallar
Allt efter att API-version 2020-06-01 har stöd för hanterad startdiagnostik. Mer information finns i [instans visning av Boot Diagnostics](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Begränsningar
- Startdiagnostik är endast tillgängligt för virtuella datorer med Azure Resource Manager.
- Managed Boot Diagnostics stöder inte virtuella datorer med ohanterade OS-diskar.
- Startdiagnostiken har inte stöd för Premium Storage-konton, om ett Premium Storage-konto används för att starta diagnostiska användare får ett `StorageAccountTypeNotSupported` fel meddelande när den virtuella datorn startas. 
- Hanterade lagrings konton stöds i Resource Manager API-versionen "2020-06-01" och senare.
- Azures serie konsol är för närvarande inte kompatibel med ett hanterat lagrings konto för startdiagnostik. Lär dig mer om [Azures serie konsol](./troubleshooting/serial-console-overview.md).
- Portalen stöder bara användning av startdiagnostik med ett hanterat lagrings konto för virtuella datorer med en instans.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures serie konsol](./troubleshooting/serial-console-overview.md) och hur du använder startdiagnostik för att [Felsöka virtuella datorer i Azure](./troubleshooting/boot-diagnostics.md).
