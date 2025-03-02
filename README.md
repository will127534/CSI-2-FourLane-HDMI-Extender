# CSI-2-FourLane-HDMI-Extender
A simple MIPI CSI-2 extender with 4-lane support through HDMI cable

![](https://github.com/will127534/CSI-2-FourLane-HDMI-Extender/blob/main/Images/_DSC7815.jpg)

## Hardware
Technically, this isn’t supposed to work, but it kind of does. It relies on HDMI cables with Ethernet support to provide the fifth differential pair needed to transmit 4-lane MIPI signals through the HDMI cable.

The board is very simple. The receiver can either use the 3.3V from the FPC cable or an external power input to supply the HDMI connector’s power output. The HDMI connector location matches the USB connector on the Raspberry Pi 5 to simplify case design.

The transmitter board has a DC-DC step-down converter with a 3.3V output and a bypass option to power camera boards. For the StarlightEye (which needs a 5V input), there is also a connector that taps into the HDMI power input to supply power—this could also power something else, like an LED light source. Additionally, there is a QWIIC connector for sideband communication. Finally, the board’s form factor includes a cutout for the OneInchEye 2.0.

## Notes

This project came about because GMSL2 was too difficult to implement, and I needed something to extend my 4-lane MIPI cameras. If you’re reading this, you probably already know there are a few HDMI extender boards for MIPI cameras out there. However, most only support 2-lane MIPI, while I specifically needed 4-lane MIPI.

Where’s the catch? The standard HDMI cable only has four high-speed differential pairs, so that’s typically not enough for 4-lane MIPI (which requires 4 data pairs + 1 clock pair). However, HDMI includes an extension standard that reuses the HPD and UTILITY pins on the connector for a fifth differential pair called HEAC. This pair is meant for a bi-directional higher-speed sideband signal for Ethernet or Audio. Spec-wise, it doesn't have to carry signals as high-speed as the main video data. However, from a manufacturing standpoint, it’s often just as capable, because many manufacturers use the same type of differential pair cable for the fifth pair. (See [this teardown video by ChargerLab ](https://www.youtube.com/watch?v=UTeQvG3jk0k) as an example.)

For testing I'm targeting Cable Matters 2-Pack Ultra Thin HDMI Cable series, and the 6ft one is the maximum limit running both OneInchEye and StarlightEye at 1440Mbps/lane speed at 4-lane.

For testing, I used the Cable Matters 2-Pack Ultra Thin HDMI Cable series; the 6-ft version is the maximum length that can run both the OneInchEye and StarlightEye at 1440 Mbps/lane with 4-lane MIPI.

Note: If you are using my IMX585 driver, you must lower the link speed from 1782 Mbps/lane to 1440 Mbps/lane by changing [this](https://github.com/will127534/imx585-v4l2-driver/blob/main/imx585.c#L210) line to 0x03.


Lastly, I did try using MIPI redrivers and retimers from [Ti](https://www.ti.com/product/SN65DPHY440SS) and [Diodes](https://www.diodes.com/part/view/PI2MEQX2505). The receiver board was originally designed with an active redriver/retimer (both parts are conveniently pin-to-pin compatible). However, due to the board’s pin layout, the differential pairs would need a polarity swap, and TI’s retimer wouldn’t work, even with [this register](https://e2e.ti.com/support/interface-group/interface/f/interface-forum/1187578/sn65dphy440ss-in-a-csi-application-is-it-possible-to-invert-n-and-p-on-both-sides-of-the-chip-except-for-the-clk) configured. As for the redriver, for some reason I couldn’t get it to work well with a 6-ft cable—only with a very short HDMI cable. The redriver is polarity-invariant, but even after playing around with the different tuning options, it just didn’t seem to work reliably. Suggestions are welcome in the issues/comments section.

## Footnotes

* Gerber, BOM, and CPL files for JLCPCB are under /Gerber and /bom in each Transmitter and Receiver board folder.
* [Interactive BOM for Receiver](https://htmlpreview.github.io/?https://github.com/will127534/CSI-2-FourLane-HDMI-Extender/blob/main/Receiver/bom/ibom.html) [(provided by InteractiveHtmlBom)
](https://github.com/openscopeproject/InteractiveHtmlBom)
* [Interactive BOM for Transmitter](https://htmlpreview.github.io/?https://github.com/will127534/CSI-2-FourLane-HDMI-Extender/blob/main/Transmitter/bom/ibom.html) [(provided by InteractiveHtmlBom)
](https://github.com/openscopeproject/InteractiveHtmlBom)
* Yes It still supports 2-lane MIPI cameras (tested with the IMX708 Raspberry Pi Camera V3).
* I have almost no intention of selling this on Tindie, because I can already imagine users who don’t fully understand the details running into troubles and complaining about it.
