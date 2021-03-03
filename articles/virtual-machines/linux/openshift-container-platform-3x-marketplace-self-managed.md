---
title: Distribuera OpenShift container Platform 3,11 Self-Managed Marketplace-erbjudande i Azure
description: Distribuera OpenShift container Platform 3,11 Self-Managed Marketplace-erbjudandet i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670438"
---
# <a name="configure-prerequisites"></a>Konfigurera krav

Innan du använder Marketplace-erbjudandet för att distribuera ett självhanterat OpenShift container Platform 3,11-kluster i Azure måste du konfigurera ett fåtal krav.  Läs artikeln [OpenShift-krav](./openshift-container-platform-3x-prerequisites.md) för instruktioner för att skapa en SSH-nyckel (utan lösen fras), Azure Key Vault, Key Vault Secret och ett huvud namn för tjänsten.

 
## <a name="deploy-using-the-marketplace-offer"></a>Distribuera med Marketplace-erbjudandet

Det enklaste sättet att distribuera en egen hanterad OpenShift container Platform 3,11-kluster till Azure är att använda [Azure Marketplace-erbjudandet](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Det här alternativet är det enklaste, men det har också begränsade anpassnings möjligheter. Marketplace-erbjudandet distribuerar OpenShift container Platform-3.11.82 och innehåller följande konfigurations alternativ:

- **Huvudnoder**: tre (3) huvud noder med konfigurerbar instans typ.
- **Infraröda noder**: tre (3) fjärrnoder med konfigurerbar instans typ.
- **Noder**: antalet noder (mellan 1 och 9) och instans typen kan konfigureras.
- **Disktyp**: Managed disks används.
- **Nätverk**: stöd för nya eller befintliga nätverk och anpassade CIDR-intervall.
- **CNS**: CNS kan aktive ras.
- **Mått**: Hawkular-mått kan aktive ras.
- **Loggning**: EFK-loggning kan aktive ras.
- **Azure Cloud Provider**: aktiverat som standard, kan inaktive ras.

Klicka på **skapa en resurs** längst upp till vänster i Azure Portal, ange "OpenShift container Platform" i sökrutan och tryck på RETUR.

   ![Ny resurs sökning](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Sidan resultat öppnas med **Red Hat OpenShift container Platform 3,11 själv hantering** i listan. 

   ![Nytt resurs Sök Resultat](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klicka på erbjudandet om du vill visa information om erbjudandet. Klicka på **skapa** om du vill distribuera erbjudandet. Användar gränssnittet för att ange nödvändiga parametrar visas. Den första skärmen är bladet **grundläggande** .

   ![Rubrik sida för erbjudande](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Grundläggande inställningar**

Om du vill ha hjälp med någon av indataparametrarna hovrar du över ***i*** rutan bredvid parameter namnet.

Ange värden för indataparametrarna och klicka på **OK**.

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| Användar namn för administratör för virtuell dator | Administratörs användaren som ska skapas på alla VM-instanser |
| Offentlig SSH-nyckel för administratörs användare | Offentlig SSH-nyckel som används för att logga in på en virtuell dator – får inte ha en lösen fras |
| Prenumeration | Azure-prenumeration för att distribuera kluster till |
| Resursgrupp | Skapa en ny resurs grupp eller Välj en befintlig tom resurs grupp för kluster resurser |
| Location | Azure-region för att distribuera kluster till |

   ![Bladet med grundläggande erbjudanden](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastruktur inställningar**

Ange värden för indataparametrarna och klicka på **OK**.

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| OCP för kluster namn | Kluster prefix som används för att konfigurera värdnamn för alla noder. Mellan 1 och 20 tecken |
| Huvudnode-storlek | Godkänn standard storleken för virtuell dator eller klicka på **ändra storlek** om du vill välja en annan VM-storlek.  Välj lämplig VM-storlek för din arbets belastning |
| Infrastrukturens Node-storlek | Godkänn standard storleken för virtuell dator eller klicka på **ändra storlek** om du vill välja en annan VM-storlek.  Välj lämplig VM-storlek för din arbets belastning |
| Antal programnoder | Godkänn standard storleken för virtuell dator eller klicka på **ändra storlek** om du vill välja en annan VM-storlek.  Välj lämplig VM-storlek för din arbets belastning |
| Programnode-storlek | Godkänn standard storleken för virtuell dator eller klicka på **ändra storlek** om du vill välja en annan VM-storlek.  Välj lämplig VM-storlek för din arbets belastning |
| Storlek på skydds-värd | Godkänn standard storleken för virtuell dator eller klicka på **ändra storlek** om du vill välja en annan VM-storlek.  Välj lämplig VM-storlek för din arbets belastning |
| Nya eller befintliga Virtual Network | Skapa ett nytt vNet (standard) eller Använd ett befintligt vNet |
| Välj standard CIDR-inställningar eller anpassa IP-intervall (CIDR) | Acceptera standard CIDR-intervall eller Välj **anpassat IP-intervall** och ange anpassad CIDR-information.  Standardinställningar skapar vNet med CIDR för 10.0.0.0/14, huvud under nätet med 10.1.0.0/16, fjärrundernät med 10.2.0.0/16 och Compute-och CNS-undernätet med 10.3.0.0/16 |
| Namn på Key Vault resurs grupp | Namnet på den resurs grupp som innehåller Key Vault |
| Key Vault namn | Namnet på Key Vault som innehåller hemligheten med den privata SSH-nyckeln.  Endast alfanumeriska tecken och bindestreck tillåts och måste vara mellan 3 och 24 tecken |
| Hemligt namn | Namnet på hemligheten som innehåller den privata SSH-nyckeln.  Endast alfanumeriska tecken och bindestreck tillåts |

   ![Bladet erbjudande infrastruktur](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Ändra storlek**

Om du vill välja en annan storlek på virtuell dator klickar du på ***ändra storlek** _.  Fönstret för VM-val öppnas.  Välj den VM-storlek som du vill använda och klicka på _ * Välj * *.

   ![Välj storlek på virtuell dator](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Befintliga Virtual Network**

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| Befintligt Virtual Network namn | Namn på befintligt vNet |
| Under näts namn för huvudnoder | Namn på befintligt undernät för överordnade noder.  Måste innehålla minst 16 IP-adresser och följa RFC 1918 |
| Under näts namn för infraröda noder | Namn på befintligt undernät för infraröda noder.  Måste innehålla minst 32 IP-adresser och följa RFC 1918 |
| Under näts namn för Compute-och CNS-noder | Namnet på det befintliga under nätet för Compute-och CNS-noder.  Måste innehålla minst 32 IP-adresser och följa RFC 1918 |
| Resurs grupp för den befintliga Virtual Network | Namnet på den resurs grupp som innehåller det befintliga virtuella nätverket |

   ![Erbjud infrastruktur för befintligt VNet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Anpassat IP-intervall**

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| Adress intervall för Virtual Network | Anpassad CIDR för vNet |
| Adress intervall för det undernät som innehåller huvudnoderna | Anpassad CIDR för huvud under nät |
| Adress intervall för det undernät som innehåller infrastruktur-noderna | Anpassat CIDR för infrastruktur under nät |
| Adress intervall för undernät som innehåller Compute-och CNS-noderna | Anpassad CIDR för beräknings-och CNS-noderna |

   ![Erbjud anpassat IP-intervall för infrastruktur](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Ange värden för indataparametrarna och klicka på **OK**

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| OpenShift administratör användar lösen ord | Lösen ord för den inledande OpenShift-användaren.  Den här användaren kommer också att vara kluster administratören |
| Bekräfta lösen ordet för OpenShift admin-användare | Skriv lösen ordet för OpenShift admin-användaren |
| Användar namn för Red Hat Subscription Manager | Användar namn för att få åtkomst till din Red Hat-prenumeration eller organisations-ID.  Den här autentiseringsuppgiften används för att registrera RHEL-instansen för din prenumeration och kommer inte att lagras av Microsoft eller Red Hat |
| Användar lösen ord för Red Hat Subscription Manager | Lösen ord för att få åtkomst till din Red Hat-prenumeration eller aktiverings nyckel.  Den här autentiseringsuppgiften används för att registrera RHEL-instansen för din prenumeration och kommer inte att lagras av Microsoft eller Red Hat |
| Red Hat Subscription Manager OpenShift pool-ID | Pool-ID som innehåller OpenShift container Platform-rättigheter. Se till att du har tillräckligt med rättigheter för OpenShift container Platform för att installera klustret |
| Red Hat Subscription Manager OpenShift pool-ID för Broker/Master-noder | Pool-ID som innehåller OpenShift container Platform-rättigheter för Broker/Master-noder. Se till att du har tillräckligt med rättigheter för OpenShift container Platform för att installera klustret. Om du inte använder Broker/Master pool-ID anger du pool-ID för programnoder |
| Konfigurera Azure Cloud Provider | Konfigurera OpenShift för att använda Azure Cloud Provider. Krävs om du använder Azure disk Attach för permanenta volymer.  Standardvärdet är ja |
| Klient-ID GUID för Azure AD service-huvudobjektet | Azure AD service huvud namn klient-ID GUID – kallas även AppID. Krävs endast om konfigurera Azure Cloud provider har angetts till **Ja** |
| Hemlighet för klient-ID för Azure AD-tjänstens huvud namn | Hemlighet för klient-ID för Azure AD-tjänstens huvud namn. Krävs endast om konfigurera Azure Cloud provider har angetts till **Ja** |
 
   ![Erbjudande bladet OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Ytterligare inställningar**

Bladet ytterligare inställningar gör det möjligt att konfigurera CNS för glusterfs-lagring, loggning, Mät värden och under domänen router.  Som standard installeras inte något av dessa alternativ och kommer att använda nip.io som router-underdomänen för test ändamål. Om du aktiverar CNS installeras tre ytterligare datornoder med tre ytterligare anslutna diskar som är värdar för glusterfs poddar.  

Ange värden för indataparametrarna och klicka på **OK**

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| Konfigurera behållarens inbyggda lagrings utrymme (CNS) | Installerar CNS i OpenShift-klustret och aktiverar det som lagrings utrymme. Blir standard om Azure Provider är inaktive rad |
| Konfigurera kluster loggning | Installerar EFK loggnings funktioner i klustret.  Ändra storlek på de noder som är värdar för EFK-poddar |
| Konfigurera mått för klustret | Installerar Hawkular-mått i OpenShift-klustret.  Ändra storlek på de noder som är värdar för Hawkular-mått poddar |
| Standard under domän för router | Välj Nipio för testning eller anpassad om du vill ange en egen under domän för produktion |
 
   ![Erbjud ytterligare blad](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Ytterligare inställningar-extra parametrar**

| Indataparameter | Parameter Beskrivning |
|-----------------------|-----------------|
| CNS Node-storlek | Acceptera standardvärdet för noder eller Välj **ändra storlek** om du vill välja en ny storlek på virtuell dator |
| Ange din anpassade under domän | Den anpassade routningsdomänen som ska användas för att exponera program via routern i OpenShift-klustret.  Se till att skapa rätt jokertecken DNS-post] |
 
   ![Erbjud ytterligare CNS-installation](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Sammanfattning**

Verifiering sker i det här steget för att kontrol lera att kärn kvoten räcker för att distribuera det totala antalet virtuella datorer som har valts för klustret.  Granska alla parametrar som angavs.  Om indatana är godkända klickar du på **OK** för att fortsätta.

   ![Bladet erbjudande Sammanfattning](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Köp**

Bekräfta kontakt information på sidan Köp och klicka på **köp** för att godkänna användnings villkoren och börja distribuera för OpenShift container Platform-klustret.

   ![Bladet erbjudande köp](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Ansluta till OpenShift-klustret

När distributionen är klar hämtar du anslutningen från avsnittet utdata i distributionen. Anslut till OpenShift-konsolen med webbläsaren med hjälp av **URL: en för OpenShift-konsolen**. Du kan också SSH till skydds-värden. Följande är ett exempel där admin-användarnamnet är clusteradmin och skydds offentliga IP DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Kom igång med OpenShift container Platform](https://docs.openshift.com)
- 
