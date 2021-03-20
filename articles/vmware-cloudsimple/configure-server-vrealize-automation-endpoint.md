---
title: Azure VMware-lösning av CloudSimple – konfigurera vCenter i privat moln för vRealize Automation
description: Beskriver hur du konfigurerar en VMware vCenter-Server i ditt CloudSimple privata moln som en slut punkt för VMware vRealize Automation
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895061"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Konfigurera vCenter i ditt privata moln för VMware vRealize Automation

Du kan konfigurera en VMware vCenter-Server i ditt CloudSimple privata moln som en slut punkt för VMware vRealize Automation.

## <a name="before-you-begin"></a>Innan du börjar

Slutför de här uppgifterna innan du konfigurerar vCenter-servern:

* Konfigurera en [plats-till-plats-VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mellan din lokala miljö och ditt privata moln.
* [Konfigurera DNS-vidarebefordran av lokala DNS-begäranden](on-premises-dns-setup.md) till DNS-servrarna för ditt privata moln.
* Skicka en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) för att skapa en administrativ användare av VRealize Automation IaaS med den uppsättning behörigheter som anges i följande tabell.

| Attributvärde | Behörighet |
------------ | ------------- |  
| Datalager |  Allokera utrymme <br> Bläddra i data lager |
| Data lager kluster | Konfigurera ett data lager kluster |
| Mapp | Skapa mapp <br>Ta bort mapp |
| Global |  Hantera anpassade attribut<br>Ange anpassat attribut |
| Nätverk | Tilldela nätverk |
| Behörigheter | Ändra behörigheter |
| Resurs | Tilldela en virtuell dator till en resurspool<br>Migrera avstängd virtuell dator<br>Migrera på den virtuella datorn |
| Inventering av virtuell dator |  Skapa från befintlig<br>Skapa Ny<br>Flytta<br>Ta bort | 
| Interaktion för virtuell dator |  Konfigurera CD-medier<br>Konsol interaktion<br>Enhets anslutning<br>Stäng av<br>Slå på<br>Återställ<br>Suspend<br>Verktyg installera | 
| Konfiguration av virtuell dator |  Lägg till befintlig disk<br>Lägg till ny disk<br>Lägg till eller ta bort<br>Ta bort disk<br>Avancerat<br>Ändra antal processorer<br>Ändra resurs<br>Utöka virtuell disk<br>Disk Ändringsspårning<br>Minne<br>Ändra enhets inställningar<br>Byt namn<br>Ange anteckning (version 5,0 och senare)<br>Inställningar<br>Swapfile placering |
| Etablering |  Anpassa<br>Klona mall<br>Klona virtuell dator<br>Distribuera mallen<br>Läs anpassnings specifikationer |
| Tillstånd för virtuell dator | Skapa ögonblicks bild<br>Ta bort ögonblicks bild<br>Återgå till ögonblicks bild |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installera vRealize Automation i din lokala miljö

1. Logga in på vRealize Automation IaaS Server-installationen som IaaS-administratör som CloudSimple-supporten har skapat åt dig.
2. Distribuera en vSphere-agent för Automation-slutpunkten för vRealize.
    1. Gå till https://*vra-URL*: 5480/Installer, där *vra-URL* är den URL som du använder för att komma åt vRealize Automation administration UI.
    2. Klicka på **installations programmet för IaaS** för att hämta installations programmet.<br>
    Namngivnings konventionen för installations filen är setup_ *vra-URL* @5480.exe .
    3. Kör installationsprogrammet. Klicka på **Nästa** på välkomstskärmen.
    4. Godkänn licens avtalet och klicka på **Nästa**.
    5. Ange inloggnings informationen, klicka på **acceptera certifikat** och klicka sedan på **Nästa**.
    ![vRA-autentiseringsuppgifter](media/configure-vra-endpoint-login.png)
    6. Välj **anpassad installation** och **proxy agenter** och klicka på **Nästa**.
    ![Installations typ för vRA](media/configure-vra-endpoint-install-type.png)
    7. Ange inloggnings informationen för IaaS-servern och klicka på **Nästa**. Om du använder Active Directory anger du användar namnet i formatet **domän \ användare** . Annars använder du **user@domain** format.
    ![vRA inloggnings information](media/configure-vra-endpoint-account.png)
    8. Ange **vSphere** för **agent typ** för proxyinställningarna. Ange ett namn för agenten.
    9. Ange IaaS-serverns FQDN i värd fälten för **hanterings tjänst** och i **webb tjänstens värd fält i modell hanteraren** . Klicka på **testa** för att testa anslutningen för varje FQDN-värde. Om testet Miss lyckas ändrar du DNS-inställningarna så att IaaS-serverns värdnamn är löst.
    10. Ange ett namn för vCenter Server-slutpunkten för det privata molnet. Registrera namnet för senare användning i konfigurations processen.

        ![vRA installera proxy](media/configure-vra-endpoint-proxy.png)

    11. Klicka på **Nästa**.
    12. Klicka på **Installera**.

## <a name="configure-the-vsphere-agent"></a>Konfigurera vSphere-agenten

1. Gå till https://*vra-URL*/vcac och logga in som **ConfigurationAdmin**.
2. Välj   >    >  **slut punkter** för infrastruktur slut punkter.
3. Välj **ny**  >  **virtuell**  >  **vSphere**.
4. Ange slut punkts namnet för vSphere som du angav i föregående procedur.
5. För **adress** anger du det privata molnets vCenter Server URL i formatet https://*vCenter – FQDN*/SDK, där *vCenter-FQDN* är namnet på vCenter-servern.
6. Ange autentiseringsuppgifterna för den administrativa användaren vRealize Automation IaaS som CloudSimple-stöd har skapats för dig.
7. Verifiera användarautentiseringsuppgifterna genom att klicka på **Testa anslutning** . Om testet Miss lyckas, verifiera URL, konto information och [slut punkts namn](#verify-the-endpoint-name) och försök igen.
8. När testet är klart klickar du på **OK** för att skapa vSphere-slutpunkten.
    ![vRA Endpoint config-åtkomst](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Kontrol lera slut punktens namn

Gör så här för att identifiera rätt slut punkts namn för vCenter-servern:

1. Öppna en kommando tolk på IaaS-enheten.
2. Ändra katalogen till C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, där *agent namn* är det namn som du tilldelade vCenter-serverns slut punkt.
3. Kör följande kommando.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Utdata ser ut ungefär så här. Värdet för `managementEndpointName` fältet är slut punktens namn.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
