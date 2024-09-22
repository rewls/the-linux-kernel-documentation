# Introduction to HID report descriptors

- This chapter is meant to give a broad overview of what HID report descriptors are, and of how a casual (non-kernel) programmer can deal with HID devices that are not working well with Linux.

## Introduction

- HID stands for Human Interface Device, and can be whatever device you are using to interact with a computer, be it a mouse, a touchpad, a tablet, a microphone.

- Many HID devices work out the box, even if their hardware is different.

- This is because modern HID devices do advertise their capabilities through the *HID report descriptor*, a fixed set of bytes describing exactly what *HID reports* may be sent between the device and the host and the meaning of each individual bit in those reports.

- The HID report itself then merely carries the actual data values without any extra meta information.

- Note that HID reports may be sent from the device ("Input Reports", i.e. input events), to the device ("Output Reports" to e.g. change LEDs) or used for device configuration ("Feature reports").

- A device may support one or more HID reports.

- The HID subsystem is in charge of parsing the HID report descriptors, and converts HID events into normal input device interfaces (see HID I/O Transport Drivers).

- Devices may misbehave because the HID report descriptor provided by the device is wrong, or because it needs to be dealt with in a special way, or because some special device or interaction mode is not handled by the default code.

- The format of HID report descriptors is described by two documents, available from the USB Implementers Forem HID web page address:

    - the HID USB Device Class Definition (HID Spec from now on)

    - the HID Usage Tables (HUT from now one)

- The HID subsystem can deal with different transport drivers (USB, I2C, Bluetooth, etc.).

- See HID I/O Transport Drivers.

## Parsing HID report descriptors

- The current list of HID devices can be found at `/sys/bus/hid/devices/`.

- For each device, say `/sys/bus/hid/devices/0003\:093A\:2510.0002/`, one can read the corresponding report descriptor:

    ```sh
    $ hexdump -C /sys/bus/hid/devices/0003\:093A\:2510.0002/report_descriptor
    ```

- Optional: the HID report descriptor can be read also by directly accessing the hidraw driver.

- The basic structure of HID report descriptors is defined in the HID spec, while HUT "defines constants that can be interpreted by an application to identify the purpose and meaning of a data field in a HID report".

- Each entry is defined by at least two bytes, where the first one defines what type of value is following and is described in the HID spec, while the second one carries the actual value and is described in the HUT.

- In practice you should not parse HID report descriptors by hand; rather, you should use an existing parser.

    - the online USB Descriptor and Request Parser;

    - hidrdd, that provides very detailed and somewhat verbose descriptions;

    - hid-tools, a complete utility set that allows among other thins, to record and replay the raw HID reports and to debug and replay HID devices.

        - It is being actively developed by the Linux HID subsystem maintainers.

- Parsing the mouse HID report descriptor with hid-tools leads to: 

```sh
$ ./hid-decode /sys/bus/hid/devices/0003\:093A\:2510.0002/report_descriptor
```

- We can check the values sent by resorting e.g. to the *hid-recorder* tool, from hid-tools.

## Collections, Report IDs and Evdev events

- A single device can logically group data into different independent sets, called a *Collection*.

- Collections can be nested and there are different types of collections.

    - See the HID spec 6.2.2.6 "Collection, End Collection Items" for details.

- Different reports are identified by means of different *Report ID* fields, i.e. a number identifying the structure of the immediately following report.

- Whenever a Report ID is needed it is transmitted as the first byte of any report.

- A device with only one supported HID report may omit the report ID.

- A device can have different Report IDs for the same Application Collection.

- All the input data sent by the device should be translated into corresponding Evdev events, so that the remaining part of the stack can know what is going on.

## Events

- In Linux, one `/dev/input/event*` is created for each `Application Collection`.

```sh
$ sudo libinput record /dev/input/event1
```

## When something does not work

- There can be a number of reasons why a device does not behave correctly.

- For example

    - The HID report descriptor provided by the HID device may be wrong because e.g.

        - it does not follow the standard, so that the kernel will not able to make sense of the HID report descriptor;

        - the HID report descriptor *does not match* what is actually sent by the device;

    - the HID report descriptor may need some "quirks" (see later on).

- As a consequence, a `/dev/input/dvent*` may not be created for each Application Collection, and/or the events there may not match what you would expect.
