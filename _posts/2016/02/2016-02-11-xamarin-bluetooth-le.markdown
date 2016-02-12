---
layout: post
title:  "Cross-Platform Bluetooth LE in Xamarin"
date:   2016-02-11 15:00:00
author: "Taylor"
permalink: "/xamarin-bluetooth-le"
tags:
- C#
- Bluetooth
- LE
- Xamarin
---

### What is Bluetooth LE?

Bluetooth Low Energy (BLE) is an iteration on Bluetooth technology that was built for the Internet of Things. It is [power-friendly and implemented on all major operating systems](https://www.bluetooth.com/what-is-bluetooth-technology/bluetooth-technology-basics/low-energy){:target="_blank"}, making it a great option for close-range communication with "smart" objects.

### As a developer, what do I need to know about Bluetooth LE?

Every BLE device has a set of endpoints that allow other BLE devices to interact with it. The endpoints are included in the [Generic Attribute Profile (GATT)](https://www.bluetooth.com/specifications/assigned-numbers/generic-attribute-profile){:target="_blank"} of the device.

A device has a single GATT profile that makes up its interface. At the top level, a device has a set of services. Each service is made up of a set of characteristics. These characteristics are the actual endpoints that allow interaction with the device. Each characteristic has a set of properties that determine how other devices are allowed to interact with it. Characteristics can allow any combination of the following interactions:

- __Read__: allows clients to read this characteristic using any of the read operations
- __Write__: allows clients to use the Write Request/Response operation
- __WriteWithoutResponse__: allows clients to use the Write Command operation
- __Notify__: allows the server to use the Handle Value Notification operation
- __Indicate__: allows the server to use the Handle Value Indication/Confirmation operation
- __SignedWrite__: allows clients to use the Signed Write Command operation
- __WritableAuxilliaries__: allows a client to write to the Characteristic User Description descriptor
- __Broadcast__: allows this characteristic value to be placed in advertising packets

There are reserved [services](https://developer.bluetooth.org/gatt/services/Pages/ServicesHome.aspx){:target="_blank"} and [characteristics](https://developer.bluetooth.org/gatt/characteristics/Pages/CharacteristicsHome.aspx){:target="_blank"} for specific types of devices and information. For example, a [heart rate monitor](https://developer.bluetooth.org/gatt/services/Pages/ServiceViewer.aspx?u=org.bluetooth.service.heart_rate.xml){:target="_blank"} will always have a service with identifier 0x180D that has characteristics 0x2A37, 0x2A38, and 0x2A39. Before beginning development, be sure to fully document the full GATT profile for the device.

### Bluetooth LE in iOS

The BLE implementation for iOS is all included in the Core Bluetooth framework. Apple has provided very [helpful documentation](https://developer.apple.com/library/ios/documentation/NetworkingInternetWeb/Conceptual/CoreBluetooth_concepts/CoreBluetoothOverview/CoreBluetoothOverview.html#//apple_ref/doc/uid/TP40013257-CH2-SW1){:target="_blank"} detailing the data structure and use of this framework.

Basically, the data structure follows its physical representation. Using a [CBCentralManager](https://developer.apple.com/library/prerelease/ios/documentation/CoreBluetooth/Reference/CBCentralManager_Class/index.html){:target="_blank"}, you can scan for and connect to devices ([CBPeripheral](https://developer.apple.com/library/prerelease/ios/documentation/CoreBluetooth/Reference/CBPeripheral_Class/index.html#//apple_ref/occ/cl/CBPeripheral){:target="_blank"}). The central manager's functionality is limited to device discovery, connection, and disconnection. Once connected to a device, the CBPeripheral object is the main interface for interacting with the device. You can discover the device's services ([CBService](https://developer.apple.com/library/prerelease/ios/documentation/CoreBluetooth/Reference/CBService_Class/index.html#//apple_ref/doc/c_ref/CBService){:target="_blank"}), discover a service's characteristics ([CBCharacteristic](https://developer.apple.com/library/prerelease/ios/documentation/CoreBluetooth/Reference/CBCharacteristic_Class/index.html#//apple_ref/swift/cl/c:objc(cs)CBCharacteristic){:target="_blank"}), and interact directly with the characteristics.

### Bluetooth LE in Android
BLE in Android is a little less straight-forward. The main interface is the [BluetoothAdapter](http://developer.android.com/reference/android/bluetooth/BluetoothAdapter.html){:target="_blank"}, which is required for any and all Bluetooth activity. Once a device ([BluetoothGatt](http://developer.android.com/reference/android/bluetooth/BluetoothGatt.html){:target="_blank"}) is discovered, you can connect to that object by calling a method ([.connectGatt()](http://developer.android.com/reference/android/bluetooth/BluetoothDevice.html#connectGatt(android.content.Context, boolean, android.bluetooth.BluetoothGattCallback)){:target="_blank"}) on it. The returned object is the connected device, with which you can interact the same as above.

The confusing part for me is the device discovery and connection. Callbacks ([LeScanCallback](http://developer.android.com/reference/android/bluetooth/BluetoothAdapter.LeScanCallback.html){:target="_blank"} & [BluetoothGattCallback](http://developer.android.com/reference/android/bluetooth/BluetoothGattCallback.html){:target="_blank"}) are passed to the discovery and connection to handle errors/success. These classes need to be extended and implemented to properly show devices and connect to them.

### Cross-Platform Bluetooth LE

Sharing code in a cross-platform app means that the implementation details for each platform need to be abstracted away. This gives us a blank canvas on which to build a new interface for BLE.

For ease of re-use, I have created a nuget package ([Xamarin.BluetoothLE](https://www.nuget.org/packages/XamarinBluetoothLE){:target="_blank"}) with the iOS and Android Bluetooth LE implementation abstracted away. You can [view/contribute to the source on Github](https://github.com/tbrushwyler/Xamarin.BluetoothLE){:target="_blank"}. Because of its similarities to the physical elements it represents, the interface is modeled more after the iOS implementation of BLE. Using C# allows us to raise events that will be handled by your code. The relevant interfaces are listed below:

// todo: use the XML doc to create the documentation

#### IAdapter

Used for device discovery and disconnection.

__Methods__:

- StartScanningForDevices()
- StopScanningForDevices()
- ConnectToDevice()
- DisconnectDevice()

__Events__:

- DeviceDiscovered
- DeviceConnected
- DeviceDisconnected
- DeviceFailedToConnect
- ScanTimeoutElapsed

#### IDevice

Used for interacting with a device.

__Methods__:

- DiscoverServices()
- Disconnect()
- Write()

__Events__:

- ServiceDiscovered

#### IService

Used for interacting with a service.

__Methods__:

- DiscoverCharacteristics()

__Events__:

- CharacteristicDiscovered

#### ICharacteristic

Used for interacting with a characteristic.

__Methods__:

- StartUpdates()
- StopUpdates()
- Read()
- Write()

__Events__:

- ValueUpdated
