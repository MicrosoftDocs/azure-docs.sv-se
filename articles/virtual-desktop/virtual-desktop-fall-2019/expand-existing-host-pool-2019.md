---
title: Expandera den befintliga Windows Virtual Desktop-poolen (klassisk) med nya sessionsbaserade värdar – Azure
description: Hur du expanderar en befintlig adresspool med nya sessionsbaserade värdar i Windows Virtual Desktop (klassisk).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009179"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Expandera en befintlig adresspool med nya sessionsbaserade värdar i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../expand-existing-host-pool.md).

När du ramperar användningen i din värd pool kan du behöva expandera den befintliga poolen med nya sessionsbaserade värdar för att hantera den nya belastningen.

I den här artikeln får du lära dig hur du kan expandera en befintlig adresspool med nya sessionsbaserade värdar.

## <a name="what-you-need-to-expand-the-host-pool"></a>Vad du behöver för att expandera poolen

Innan du börjar ska du kontrol lera att du har skapat en adresspool och en sessions värd för virtuella datorer (VM) med någon av följande metoder:

- [Azure Marketplace-erbjudande](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell-2019.md)

Du behöver också följande information från första gången du skapade värddatorn och de virtuella datorerna i sessionen:

- Namn på virtuell dator, avbildning och namn
- Autentiseringsuppgifter för domän anslutning och Windows Virtual Desktop-innehavaradministratör
- Namn på virtuellt nätverk och undernät

Följande tre avsnitt är tre metoder som du kan använda för att expandera värddatorn. Du kan göra det med ett distributions verktyg som du är van vid.

>[!NOTE]
>Under distributions fasen visas fel meddelanden för den tidigare sessionen värd för VM-resurser om de för närvarande är avstängd. Felen inträffar eftersom Azure inte kan köra PowerShell DSC-tillägget för att kontrol lera att de virtuella datorerna i sessionen är korrekt registrerade i den befintliga poolen. Du kan ignorera dessa fel eller så kan du undvika felen genom att starta alla VM-värdar i den befintliga adresspoolen innan du påbörjar distributions processen.

## <a name="redeploy-from-azure"></a>Distribuera igen från Azure

Om du redan har skapat en adresspool och en session som är värd för en virtuell dator med [Azure Marketplace-erbjudandet](create-host-pools-azure-marketplace-2019.md) eller [Azure Resource Manager GitHub-mallen](create-host-pools-arm-template.md)kan du distribuera om samma mall från Azure Portal. Om du distribuerar om mallen automatiskt kommer all information som du har angett i den ursprungliga mallen, förutom lösen ord.

Så här distribuerar du om Azure Resource Manager-mallen för att expandera en adresspool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Från Sök fältet överst i Azure Portal söker du efter **resurs grupper** och väljer objektet under **tjänster**.
3. Sök efter och välj den resurs grupp som du skapade när du gjorde poolen för värdar.
4. I panelen på vänster sida av webbläsaren väljer du **distributioner**.
5. Välj lämplig distribution för processen för att skapa en värddator för poolen:
     - Om du har skapat den ursprungliga poolen med Azure Marketplace-erbjudandet väljer du distributionen som börjar med **RDS. WVD-repool-Host-pool**.
     - Om du har skapat den ursprungliga poolen med GitHub Azure Resource Manager-mallen väljer du distributionen med namnet **Microsoft. template**.
6. Välj **omdistribuera**.

     >[!NOTE]
     >Om mallen inte distribueras om automatiskt när du väljer **distribuera** om väljer du **mall** i panelen till vänster i webbläsaren och väljer sedan **distribuera**.

7. Välj den resurs grupp som innehåller aktuella virtuella dator värdar i den befintliga poolen.

     >[!NOTE]
     >Om du ser ett fel som uppmanar dig att välja en annan resurs grupp även om den du angav är korrekt väljer du en annan resurs grupp och väljer sedan den ursprungliga resurs gruppen.

8. Ange följande URL för *_artifactsLocation*: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Ange det nya totala antalet värddatorer som du vill använda för *RDSH-antalet instanser*. Om du till exempel expanderar din värddator från fem värdar till åtta, anger du **8**.
10. Ange samma befintliga domän lösen ord som du använde för det befintliga domän-UPN: t. Ändra inte användar namnet eftersom det kommer att orsaka ett fel när du kör mallen.
11. Ange samma klient administratörs lösen ord som du använde för användar-eller program-ID: t som du angav för *klient-admin-UPN eller program-ID*. Ändra inte användar namnet igen.
12. Slutför överföringen för att expandera din värd-pool.

## <a name="run-the-azure-marketplace-offering"></a>Kör Azure Marketplace-erbjudandet

Följ anvisningarna i [skapa en adresspool med hjälp av Azure Marketplace](create-host-pools-azure-marketplace-2019.md) tills du har kommit [till att köra Azure Marketplace-erbjudandet för att etablera en ny adresspool](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). När du kommer till den punkten måste du ange följande information för varje flik:

### <a name="basics"></a>Grundläggande inställningar

Alla värden i det här avsnittet ska matcha det du angav när du först skapade värddatorn och de virtuella datorerna i sessionen, förutom användare som är *Standard användare*:

1.    För *prenumeration* väljer du den prenumeration där du först skapade värd-poolen.
2.    För *resurs grupp* väljer du samma resurs grupp där de befintliga virtuella datorerna för anslutningspoolen värdar finns.
3.    För *region* väljer du samma region där de befintliga virtuella datorerna för anslutningspoolen värd för värdar finns.
4.    För *Hostpool-namn* anger du namnet på den befintliga poolen.
5.    För *Skriv bords typ* väljer du den Skriv bords typ som matchar den befintliga poolen.
6.    För *standard Skriv bords användare* anger du en kommaavgränsad lista över alla ytterligare användare som du vill logga in på de virtuella Windows-klienterna och får åtkomst till en stationär dator när Azure Marketplace-erbjudandet har slutförts. Om du till exempel vill tilldela user3@contoso.com och user4@contoso.com komma åt, anger du user3@contoso.com , user4@contoso.com .
7.    Välj **Nästa: Konfigurera virtuell dator**.

>[!NOTE]
>Förutom för *Standard användare av Skriv bordet* måste alla fält matcha exakt vad som har kon figurer ATS i den befintliga poolen. Om det finns ett matchnings fel som leder till en ny adresspool.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

Alla parameter värden i det här avsnittet ska överensstämma med vad du angav när du först skapade värddatorn och de virtuella datorerna i sessionen, förutom det totala antalet virtuella datorer. Antalet virtuella datorer som du anger är antalet virtuella datorer i den expanderade poolen:

1. Välj den virtuella dator storlek som matchar de befintliga virtuella datorerna i sessionen.

    >[!NOTE]
    >Om den angivna virtuella dator storleken som du söker efter inte visas i storleks väljaren för virtuell dator, beror det på att vi inte har publicerat den på Azure Marketplace-verktyget ännu. Om du vill begära en VM-storlek skapar du en begäran eller avröstar en befintlig begäran i [Windows Virtual Desktop UserVoice-forumet](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Anpassa *användnings profilen*, det *totala antalet användare* och *antalet virtuella dator* parametrar för att välja det totala antalet Sessionsgränser som du vill ha i din värddator. Om du till exempel expanderar din värddator från fem värddatorer till åtta kan du konfigurera de här alternativen för att komma till 8 virtuella datorer.
3. Ange ett prefix för namnen på de virtuella datorerna. Om du till exempel anger namnet "prefix" kommer de virtuella datorerna att kallas "prefix-0," prefix-1, "och så vidare.
4. Välj **Nästa: inställningar för virtuella datorer**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

Alla parameter värden i det här avsnittet ska överensstämma med vad du angav när du först skapade värddatorn och de virtuella datorerna i sessionen.

1. För *avbildnings källa* och *avbildnings-OS-version* anger du samma information som du angav när du först skapade Host-poolen.
2. Ange samma information som du angav när du först skapade den för att ansluta till de virtuella datorerna till Active Directory-domänen för *AD-domän anslutningens UPN* och de associerade lösen orden. Dessa autentiseringsuppgifter kommer att användas för att skapa ett lokalt konto på dina virtuella datorer. Du kan återställa de här lokala kontona om du vill ändra autentiseringsuppgifterna senare.
3. För information om det virtuella nätverket väljer du samma virtuella nätverk och undernät där dina befintliga VM-värdar för fjärrskrivbordssessioner finns.
4. Välj **Nästa: Konfigurera information om virtuella Windows-datorer**.

### <a name="windows-virtual-desktop-information"></a>Information om virtuella Windows-datorer

Alla parameter värden i det här avsnittet ska överensstämma med vad du angav när du först skapade värddatorn och de virtuella datorerna i sessionen.

1. Ange namnet på klient gruppen som innehåller din klient organisation för *Windows Virtual Desktop klient grupp namn*. Lämna det som standard om du inte angav ett angivet klient grupps namn.
2. Ange namnet på den klient där du vill skapa den här poolen för *Windows Virtual Desktop klient namn*.
3. Ange samma autentiseringsuppgifter som du använde när du först skapade Host-poolen och de virtuella datorerna i sessionen. Om du använder ett huvud namn för tjänsten anger du ID: t för den Azure Active Directory-instans där tjänstens huvud namn finns.
4. Välj **Nästa: granska + skapa**.

## <a name="run-the-github-azure-resource-manager-template"></a>Kör Azure Resource Managers mal len GitHub

Följ anvisningarna i [köra Azure Resource Manager-mallen för att skapa en ny adresspool](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) och ange alla samma parameter värden förutom *RDSH-antalet instanser*. Ange antalet virtuella dator värdar som du vill ha i värddatorn när du har kört mallen. Om du till exempel expanderar din värddator från fem värdar till åtta, anger du **8**.

## <a name="next-steps"></a>Nästa steg

Nu när du har expanderat den befintliga poolen kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. Du kan ansluta till en session med någon av följande klienter:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-10-2019.md)
- [Ansluta med webbklienten](connect-web-2019.md)
- [Ansluta med Android-klienten](connect-android-2019.md)
- [Ansluta med macOS-klienten](connect-macos-2019.md)
- [Ansluta med iOS-klienten](connect-ios-2019.md)
