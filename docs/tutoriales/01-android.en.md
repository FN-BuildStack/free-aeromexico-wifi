# Free Accessing Aeromexico's Wi-Fi Network on Devices Android

## Introduction

Initially, it is necessary to connect to the aircraft's wireless network by following these steps:

1. Select the Wi-Fi settings on the device and connect to the network named exactly **Aeromexico-WiFi**.
2. Enter the airline's captive portal form www.aeromexicowifi.com. On this screen, select the **15 minutes free** option.

![Aeroméxico's captive portal](../assets/captive-portal.png)

> Once this free period (15 minutes) has elapsed, the device will no longer have an internet connection. When this occurs, access to the network must be restored as described in the following sections of this document.

## MAC Address Configuration

Within the network's advanced settings, Android may display a setting called **Privacy**, **MAC address type**, or an equivalent option.

Depending on the device, options such as the following may appear:

- **Device MAC**
- **Randomized MAC**
- **Random MAC address**

Select the randomized MAC address option when available.

![Android Settings](../assets/non-persistent-option.png)

The new address is used for connections corresponding to that network configuration, according to the behavior implemented by the Android version and the device manufacturer.

> Developer mode must be enabled on the Android device to access these configuration options.

## Reconnecting to the Network

After modifying the connection's privacy settings:

1. Disconnect the device from the Wi-Fi network.
2. Reconnect to the network.
3. Verify that the connection is working correctly.
4. Complete the corresponding authentication process again by selecting free browsing for 15 minutes.

If the connection presents problems, you must delete the saved network configuration and reconnect.