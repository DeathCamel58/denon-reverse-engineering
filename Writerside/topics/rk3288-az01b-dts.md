# rk3288-az01b.dts

More DTS information on the [](Linux-DTSs.md) page.

```
/dts-v1/;

/ {

	fragment@0 {
		target-path = "/dwmmc@ff0f0000";

		__overlay__ {
			mmc-hs200-1_8v;
		};
	};

	fragment@1 {
		target-path = "/ethernet@ff290000";

		__overlay__ {
			rx_delay = <0x1a>;
			tx_delay = <0x23>;
		};
	};
};
```
