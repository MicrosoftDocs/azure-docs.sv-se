---
title: Kända problem med Azure percept
description: Läs mer om kända problem med Azure percept och deras lösningar
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193538"
---
# <a name="known-issues"></a>Kända problem

Om du stöter på något av dessa problem är det inte nödvändigt att öppna ett fel. Öppna ett ärende om du har problem med någon av lösningarna.

|Område|Beskrivning av problem|Lösning|
|-------|---------|---------|
| On-Board-upplevelse | Det går inte att slutföra den här funktionen om inte enhetens Wi-Fi har kon figurer ATS (Azure-inloggningen Miss lyckas). | 1. SSH till enhetens åtkomst punkt (10.1.1.1) <br> 2. identifiera och kopiera enhetens Ethernet IP-adress <br> 3. Anslut till den inspelade upplevelsen med den kopierade Ethernet IP-baserade URL: en |
| On-Board-upplevelse | Genom att klicka på länkarna i licens avtalet under den här funktionen kan ibland inte öppna en ny webb sida. | Kopiera länken och öppna den i ett separat webbläsarfönster. |
| On-Board-upplevelse | Det går inte att arbeta med den inspelade upplevelsen när du är ansluten till en mobil Wi-Fi hotspot. | Anslut enheten direkt till SoftAP, ett Wi-Fi nätverk eller till ett nätverk över Ethernet. |
| Wi-Fi/SoftAP | SoftAP kan ibland koppla från eller försvinna. | Vi undersöker.  Omstarten av enheten kommer normalt att ta tillbaka den igen. |
| Wi-Fi | Knappen maskin vara som växlar Wi-Fi SoftAP på och stängas av ibland fungerar inte. | Fortsätt genom att trycka på knappen eller starta om enheten. |
| Wi-Fi | Användare kan se ett meddelande efter att ha anslutit till Wi-Fi säger "det här Wi-Fi nätverket använder en äldre säkerhets standard." | Devkit för hotspot/SoftAP använder WEP-krypteringsalgoritmen. |
| Wi-Fi | Det går inte att ansluta till SoftAP från Windows 10-datorn med följande fel meddelande: <br> "Det går inte att ansluta till det här nätverket" | Starta om både devkit och datorn. |
| Enhets uppdatering | Behållare körs inte efter en OTA-uppdatering. | SSH till enheten och starta om IoT Edge containern med det här kommandot `systemctl restart iotedge` . Alla behållare kommer att startas om. |
| Enhets uppdatering | Användare kan få ett meddelande om att uppdateringen misslyckades, även om den lyckades. | Bekräfta att enheten har uppdaterats genom att navigera till enheten på enheten i IoT Hub. Detta korrigeras efter den första uppdateringen. |
| Enhets uppdatering | Användare kan förlora sina Wi-Fi anslutnings inställningar efter den första uppdateringen. | Kör genom att aktivera den här funktionen när du har uppdaterat för att konfigurera den Wi-Fi anslutningen. Detta korrigeras efter den första uppdateringen. |
| Enhets uppdatering | När du har genomfört en OTA-uppdatering kan användarna inte längre logga in via SSH med hjälp av tidigare skapade användar konton och nya SSH-användare kan inte skapas genom den här funktionen. Det här problemet påverkar system som utför OTA-uppdateringar från följande förinstallerade avbildnings versioner: 2020.110.114.105 och 2020.109.101.105. | För att återställa dina användar profiler, utför följande steg efter OTA-uppdateringen: <br> [Ssh i din devkit](./how-to-ssh-into-percept-dk.md) med "root" som användar namn. Om du inaktiverade SSH-slutanvändarens inloggning genom att aktivera den här funktionen måste du aktivera den igen. Kör det här kommandot när du har anslutit: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Om du vill återställa tidigare användares hem data kör du följande kommando: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Enhets uppdatering | När du har tagit en OTA uppdatering försvinner uppdaterings grupper. | Uppdatera enhetens tagg genom att följa [dessa anvisningar](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group). |
| Installations program för dev tools Pack | Valfri Caffe-installation kan Miss lyckas om Docker inte körs på rätt sätt i systemet. | Se till att Docker är installerat och körs och försök sedan Caffe installationen igen. |
| Installations program för dev tools Pack | Valfri CUDA-installation Miss lyckas på inkompatibla system. | Kontrol lera systemkompatibilitet med CUDA innan du kör installations programmet. |
| Docker, nätverk IoT Edge | Om det interna nätverket använder 172. x. x. x kan Docker-behållare inte ansluta till kanten. | Lägg till ett särskilt BIP-avsnitt till/etc/Docker/-daemon.jspå filen så här: `{    "bip": "192.168.168.1/24"}` |
|Azure percept Studio | Länkar för "Visa ström" i Azure percept Studio öppnar inte ett nytt fönster som visar enhetens webb data ström. | 1. Öppna [Azure Portal](https://portal.azure.com) och välj **IoT Hub**. <br> 2. Klicka på den IoT Hub som enheten är ansluten till. <br> 3. Välj **IoT Edge** under **Automatisk enhets hantering** på din IoT Hub sida. <br> 4. Välj din enhet i listan. <br> 5. Välj **Ange moduler** överst på din enhets sida. <br> 6. Klicka på ikonen pappers korg bredvid **HostIpModule** för att ta bort modulen. <br> 7. bekräfta åtgärden genom att klicka på **Granska + skapa** och sedan på **skapa**. <br> 8. Öppna [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) och klicka på **enheter** i den vänstra panelen. <br> 9. Välj din enhet i listan. <br> 10. Klicka på **Visa enhets strömmen** på fliken **syn** . Enheten kommer att ladda ned en ny version av HostIpModule och öppna en webbläsare-flik med enhetens webb data ström. |