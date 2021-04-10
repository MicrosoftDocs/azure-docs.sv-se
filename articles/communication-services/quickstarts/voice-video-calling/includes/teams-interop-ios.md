---
title: Snabb start – ansluta till ett team möte från en iOS-app
description: I den här självstudien får du lära dig hur du ansluter till ett team möte med Azure Communication Services som anropar SDK för iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8ff85dc5571f549027e208409db3f4e80c68b205
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108216"
---
I den här snabb starten får du lära dig hur du ansluter till ett team möte med Azure Communication Services som anropar SDK för iOS.

## <a name="prerequisites"></a>Förutsättningar

- En fungerande [kommunikations tjänst som anropar iOS-appen](../getting-started-with-calling.md).
- En [team distribution](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Lägg till Team UI-kontroller och aktivera team UI-kontroller

Ersätt Code i ContentView. Swift med följande kodfragment. Text rutan används för att ange team Mötes kontexten och knappen kommer att användas för att ansluta till det angivna mötet:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Hämta team Mötes länken

Länken Teams möte kan hämtas med Graph API: er. Detta beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Kommunikations tjänsterna som anropar SDK accepterar en länk till en fullständig Teams möte. Den här länken returneras som en del av `onlineMeeting` resursen och är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta). Du kan också hämta nödvändig Mötes information från URL: en **till kopplings mötet** i team Mötes mötet.

## <a name="launch-the-app-and-join-teams-meeting"></a>Starta appen och gå med i Teams möte

Du kan skapa och köra din app på iOS-simulatorn genom att välja **produkt**  >  **körning** eller genom att använda kortkommandot (&#8984;-R).

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Skärm bild som visar det färdiga programmet.":::

Infoga team-kontexten i text rutan och tryck på *Anslut Teams möte* för att ansluta till teamen i kommunikations tjänst programmet.
