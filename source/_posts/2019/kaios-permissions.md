---
layout: "post"
title: "KaiOS Permissions Table"
date: "2019-06-17"
author: "webkong"
categories:
  - JavaScript
tags:
  - KaiOS
---

KaiOS 权限列表

| Name                                                 | type:web | type:privileged | type:certified |
| ---------------------------------------------------- | -------- | --------------- | -------------- |
| geolocation                                          | !        | !               | !              |
| geolocation-noprompt                                 | ✕        | ✕               | ✓              |
| mmi-test                                             | ✕        | ✕               | ✓              |
| camera                                               | ✕        | !               | ✓              |
| alarms                                               | ✓        | ✓               | ✓              |
| tcp-socket                                           | ✕        | ✓               | ✓              |
| udp-socket                                           | ✕        | ✓               | ✓              |
| network-events                                       | ✕        | ✕               | ✓              |
| contacts                                             | ✕        | ✓               | ✓              |
| device-storage:apps                                  | ✕        | ✕               | ✓              |
| device-storage:apps-storage                          | ✕        | ✕               | ✓              |
| device-storage:crashes                               | ✕        | ✕               | ✓              |
| device-storage:pictures                              | ✕        | !               | ✓              |
| device-storage:videos                                | ✕        | !               | ✓              |
| device-storage:music                                 | ✕        | !               | ✓              |
| device-storage:sdcard                                | ✕        | !               | ✓              |
| sms                                                  | ✕        | ✕               | ✓              |
| speech-recognition                                   | ✕        | ✓               | ✓              |
| telephony                                            | ✕        | ✕               | ✓              |
| browser                                              | ✕        | ✓               | ✓              |
| browser:universalxss                                 | ✕        | ✓               | ✓              |
| browser:embedded-system-app                          | ✕        | ✕               | ✓              |
| bluetooth                                            | ✕        | ✓               | ✓              |
| mobileconnection                                     | ✕        | ✕               | ✓              |
| mobilenetwork                                        | ✕        | ✓               | ✓              |
| power                                                | ✕        | ✕               | ✓              |
| push                                                 | ✓        | ✓               | ✓              |
| serviceworker                                        | ✓        | ✓               | ✓              |
| settings                                             | ✕        | ✕               | ✓              |
| settings-clear                                       | ✕        | ✕               | ✕              |
| softkey                                              | ✕        | ✕               | ✓              |
| permissions                                          | ✕        | ✕               | ✓              |
| phonenumberservice                                   | ✕        | ✕               | ✓              |
| fmradio                                              | ✕        | ✓               | ✓              |
| attention                                            | ✕        | ✕               | ✓              |
| global-clickthrough-overlay                          | ✕        | ✓               | ✓              |
| moz-attention                                        | ✕        | ✓               | ✓              |
| webapps-manage                                       | ✕        | ✕               | ✓              |
| homescreen-webapps-manage                            | ✕        | ✓               | ✓              |
| backgroundservice                                    | ✕        | ✕               | ✓              |
| desktop-notification                                 | !        | !               | !              |
| networkstats-manage                                  | ✕        | ✕               | ✓              |
| resourcestats-manage                                 | ✕        | ✕               | ✓              |
| wifi-manage                                          | ✕        | ✕               | ✓              |
| systemXHR                                            | ✕        | ✓               | ✓              |
| voicemail                                            | ✕        | ✕               | ✓              |
| idle                                                 | ✕        | ✕               | ✓              |
| time                                                 | ✕        | ✕               | ✓              |
| embed-apps                                           | ✕        | ✕               | ✓              |
| embed-widgets                                        | ✕        | ✓               | ✓              |
| storage                                              | ✓        | ✓               | ✓              |
| background-sensors                                   | ✕        | ✕               | ✓              |
| cellbroadcast                                        | ✕        | ✕               | ✓              |
| audio-channel-normal                                 | ✓        | ✓               | ✓              |
| audio-channel-content                                | ✓        | ✓               | ✓              |
| audio-channel-notification                           | ✕        | ✓               | ✓              |
| audio-channel-alarm                                  | ✕        | ✓               | ✓              |
| audio-channel-system                                 | ✕        | ✓               | ✓              |
| audio-channel-telephony                              | ✕        | ✕               | ✓              |
| moz-audio-channel-telephony                          | ✕        | ✓               | ✓              |
| audio-channel-ringer                                 | ✕        | ✕               | ✓              |
| moz-audio-channel-ringer                             | ✕        | ✓               | ✓              |
| audio-channel-publicnotification                     | ✕        | ✕               | ✓              |
| volumemanager                                        | ✕        | ✓               | ✓              |
| open-remote-window                                   | ✕        | ✕               | ✓              |
| input                                                | ✕        | ✓               | ✓              |
| input-manage                                         | ✕        | ✕               | ✓              |
| wappush                                              | ✕        | ✕               | ✓              |
| audio-capture                                        | !        | !               | ✓              |
| audio-capture:3gpp                                   | ✕        | ✓               | ✓              |
| audio-capture:3gpp2                                  | ✕        | ✓               | ✓              |
| nfc                                                  | ✕        | ✓               | ✓              |
| nfc-share                                            | ✕        | ✕               | ✓              |
| nfc-manager                                          | ✕        | ✕               | ✓              |
| nfc-hci-events                                       | ✕        | ✓               | ✓              |
| speaker-control                                      | ✕        | ✓               | ✓              |
| downloads                                            | ✕        | ✕               | ✓              |
| video-capture                                        | !        | !               | ✓              |
| feature-detection                                    | ✕        | ✓               | ✓              |
| mobileid                                             | ✕        | !               | !              |
| test-permission                                      | !        | !               | ✓              |
| kaios-accounts                                       | ✕        | ✕               | ✓              |
| kaios-accounts:service                               | ✕        | !               | ✓              |
| themeable                                            | ✕        | ✕               | ✓              |
| settings:wallpaper.image                             | ✕        | ✓               | ✓              |
| engineering-mode                                     | ✕        | ✕               | ✓              |
| tv                                                   | ✕        | ✕               | ✓              |
| before-after-keyboard-event                          | ✕        | ✕               | ✓              |
| presentation-device-manage                           | ✕        | ✕               | ✓              |
| requestsync-manager                                  | ✕        | ✕               | ✓              |
| secureelement-manage                                 | ✕        | ✓               | ✓              |
| inputport                                            | ✕        | ✕               | ✓              |
| system-update                                        | ✕        | ✕               | ✓              |
| presentation                                         | ✕        | ✓               | ✓              |
| open-hidden-window                                   | ✕        | ✕               | ✓              |
| moz-extremely-unstable-and-will-change-webcomponents | ✕        | ✓               | ✓              |
| system-app-only-audio-channels-in-app                | ✕        | ✕               | ✓              |
| killswitch                                           | ✕        | ✕               | ✓              |
| flip                                                 | ✕        | ✕               | ✓              |
| flashlight                                           | ✕        | ✕               | ✓              |
| datacall                                             | ✕        | ✕               | ✓              |
| keyboard-event-generator                             | ✕        | ✕               | ✓              |
| customization                                        | ✕        | ✕               | ✓              |
| external-api                                         | ✕        | ✓               | ✓              |
| engmode-extension                                    | ✕        | ✕               | ✓              |
| cloud-authorization                                  | ✕        | ✕               | ✓              |
| spatialnavigation-app-manage                         | ✕        | ✓               | ✓              |
| sandboxed-cookies                                    | ✓        | ✓               | ✓              |

MDN Link [https://developer.mozilla.org/en-US/docs/Archive/B2G_OS/Firefox_OS_apps/App_permissions](https://developer.mozilla.org/en-US/docs/Archive/B2G_OS/Firefox_OS_apps/App_permissions)
