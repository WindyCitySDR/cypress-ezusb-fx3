
                        CYPRESS SEMICONDUCTOR CORPORATION
                                    FX3 SDK

USB SPI (GPIO MODE) LOOPBACK EXAMPLE
----------------------------------------

  This example illustrates the use of the FX3 firmware APIs to implement
  a USB SPI flash programming example using GPIOs to control the SPI flash.
  The example makes use of GPIO Ids 53-56 to control the SPI Flash. By default
  this example makes use of the GPIO SDK API's to access the GPIO functionality.
  If direct GPIO register access is desired then define FX3_USE_GPIO_REGS in the
  header file.

  The device enumerates as a vendor specific USB device with only the control
  endpoint and provides a set of vendor commands to read/write the data on
  SPI flash devices.

  A SPI flash write requires the following sequences:

  1. Erase the required sector: CY_FX_RQT_SPI_FLASH_ERASE_POLL with wValue
     as 1 and length as 0.
  2. Wait for erase to complete: CY_FX_RQT_SPI_FLASH_ERASE_POLL with wValue
     as 0 and IN length as 1. Repeat this until the returned value has WIP zero.
  3. Write data to page: CY_FX_RQT_SPI_FLASH_WRITE with correct parameters.
  4. Wait for write to complete: CY_FX_RQT_SPI_FLASH_ERASE_POLL with wValue
     as 0 and IN length as 1. Repeat this until the returned value has WIP zero.


  Files:

    * cyfx_gcc_startup.S   : Start-up code for the ARM-9 core on the FX3 device.
      This assembly source file follows the syntax for the GNU assembler.

    * cyfxusbspigpiomode.h : Constant definitions for the USB SPI application.
      The USB connection speed, numbers and properties of the endpoints etc.
      can be selected through definitions in this file.

    * cyfxusbenumdscr.c     : C source file containing the USB descriptors that
      are used by this firmware example. VID and PID is defined in this file.

    * cyfxtx.c             : ThreadX RTOS wrappers and utility functions required
      by the FX3 API library.

    * cyfxusbspigpiomode.c : Main C source file that implements the USB SPI application
      example.

    * makefile             : GNU make compliant build script for compiling this
      example.

   Vendor Commands implemented:

   1. Read Firmware ID
      bmRequestType = 0xC0
      bRequest      = 0xB0
      wValue        = 0x0000
      wIndex        = 0x0000
      wLength       = 0x0008

      Data response = "fx3 spi"

   2. Write to SPI flash
      bmRequestType = 0x40
      bRequest      = 0xC2
      wValue        = 0x0000
      wIndex        = SPI flash page address (Each page is assumed to be of 256 bytes and the byte address is
                      computed by multiplying wIndex by 256)
      wLength       = Length of data to be written (Should be a multiple of 256 and less than or equal to 4096)

      Data phase should contain the actual data to be written

   5. Read from SPI flash
      bmRequestType = 0xC0
      bRequest      = 0xC3
      wValue        = 0x0000
      wIndex        = SPI flash page address (Each page is assumed to be of 256 bytes and the byte address is
                      computed by multiplying wIndex by 256)
      wLength       = Length of data to be read (Should be a multiple of 256 and less than or equal to 4096)

      Data phase will contain the data read from the flash device

   6. Erase SPI flash sector
      bmRequestType = 0x40
      bRequest      = 0xC4
      wValue        = 0x0001
      wIndex        = SPI flash sector address (Each sector is assumed to be of 64 KB and the byte address is
                      computed by multiplying wIndex by 65536)
      wLength       = 0x0000

      No data phase is associated with this command.

   7. Check SPI busy status
      bmRequestType = 0xC0
      bRequest      = 0xC4
      wValue        = 0x0000
      wIndex        = 0x0000
      wLength       = 0x0001

      Data phase will indicate SPI flash busy status
      0x00 means SPI flash has finished write/erase operation and is ready for next command.
      Non-zero value means that SPI flash is still busy processing previous write/erase command.


Note:- bmRequestType, bRequest, wValue, wIndex, wLength are fields of setup packet. Refer USB 
       specification for understanding format of setup data. 
[]

