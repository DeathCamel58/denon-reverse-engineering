# rk3288-az05-nh08.dts

More DTS information on the [](Linux-DTSs.md) page.

```
/dts-v1/;

/ {
	#address-cells = <0x02>;
	#size-cells = <0x02>;
	compatible = "inmusic,nh08", "inmusic,az05", "rockchip,rk3288";
	interrupt-parent = <0x01>;
	model = "Numark MIXSTREAM PRO";
	inmusic,product-code = "NH08";
	inmusic,panel-rotation = <0x5a>;

	aliases {
		ethernet0 = "/ethernet@ff290000";
		i2c0 = "/i2c@ff650000";
		i2c1 = "/i2c@ff140000";
		i2c2 = "/i2c@ff660000";
		i2c3 = "/i2c@ff150000";
		i2c4 = "/i2c@ff160000";
		i2c5 = "/i2c@ff170000";
		mmc0 = "/dwmmc@ff0f0000";
		mmc1 = "/dwmmc@ff0c0000";
		mmc2 = "/dwmmc@ff0d0000";
		mmc3 = "/dwmmc@ff0e0000";
		serial0 = "/serial@ff180000";
		serial1 = "/serial@ff190000";
		serial2 = "/serial@ff690000";
		serial3 = "/serial@ff1b0000";
		serial4 = "/serial@ff1c0000";
		spi0 = "/spi@ff110000";
		spi1 = "/spi@ff120000";
		spi2 = "/spi@ff130000";
	};

	arm-pmu {
		compatible = "arm,cortex-a12-pmu";
		interrupts = <0x00 0x97 0x04 0x00 0x98 0x04 0x00 0x99 0x04 0x00 0x9a 0x04>;
		interrupt-affinity = <0x02 0x03 0x04 0x05>;
	};

	cpus {
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		enable-method = "rockchip,rk3066-smp";
		rockchip,pmu = <0x06>;

		cpu@500 {
			device_type = "cpu";
			compatible = "arm,cortex-a12";
			reg = <0x500>;
			resets = <0x07 0x00>;
			operating-points-v2 = <0x08>;
			#cooling-cells = <0x02>;
			clock-latency = <0x9c40>;
			clocks = <0x07 0x06>;
			dynamic-power-coefficient = <0x172>;
			cpu0-supply = <0x09>;
			phandle = <0x02>;
		};

		cpu@501 {
			device_type = "cpu";
			compatible = "arm,cortex-a12";
			reg = <0x501>;
			resets = <0x07 0x01>;
			operating-points-v2 = <0x08>;
			#cooling-cells = <0x02>;
			clock-latency = <0x9c40>;
			clocks = <0x07 0x06>;
			dynamic-power-coefficient = <0x172>;
			phandle = <0x03>;
		};

		cpu@502 {
			device_type = "cpu";
			compatible = "arm,cortex-a12";
			reg = <0x502>;
			resets = <0x07 0x02>;
			operating-points-v2 = <0x08>;
			#cooling-cells = <0x02>;
			clock-latency = <0x9c40>;
			clocks = <0x07 0x06>;
			dynamic-power-coefficient = <0x172>;
			phandle = <0x04>;
		};

		cpu@503 {
			device_type = "cpu";
			compatible = "arm,cortex-a12";
			reg = <0x503>;
			resets = <0x07 0x03>;
			operating-points-v2 = <0x08>;
			#cooling-cells = <0x02>;
			clock-latency = <0x9c40>;
			clocks = <0x07 0x06>;
			dynamic-power-coefficient = <0x172>;
			phandle = <0x05>;
		};
	};

	opp-table-0 {
		compatible = "operating-points-v2";
		opp-shared;
		phandle = <0x08>;

		opp-126000000 {
			opp-hz = <0x00 0x7829b80>;
			opp-microvolt = <0xdbba0>;
		};

		opp-216000000 {
			opp-hz = <0x00 0xcdfe600>;
			opp-microvolt = <0xdbba0>;
		};

		opp-312000000 {
			opp-hz = <0x00 0x1298be00>;
			opp-microvolt = <0xdbba0>;
		};

		opp-408000000 {
			opp-hz = <0x00 0x18519600>;
			opp-microvolt = <0xdbba0>;
		};

		opp-600000000 {
			opp-hz = <0x00 0x23c34600>;
			opp-microvolt = <0xdbba0>;
		};

		opp-696000000 {
			opp-hz = <0x00 0x297c1e00>;
			opp-microvolt = <0xe7ef0>;
		};

		opp-816000000 {
			opp-hz = <0x00 0x30a32c00>;
			opp-microvolt = <0xf4240>;
		};

		opp-1008000000 {
			opp-hz = <0x00 0x3c14dc00>;
			opp-microvolt = <0x100590>;
		};

		opp-1200000000 {
			opp-hz = <0x00 0x47868c00>;
			opp-microvolt = <0x10c8e0>;
		};

		opp-1416000000 {
			opp-hz = <0x00 0x54667200>;
			opp-microvolt = <0x124f80>;
		};

		opp-1512000000 {
			opp-hz = <0x00 0x5a1f4a00>;
			opp-microvolt = <0x13d620>;
		};

		opp-1608000000 {
			opp-hz = <0x00 0x5fd82200>;
			opp-microvolt = <0x149970>;
		};
	};

	reserved-memory {
		#address-cells = <0x01>;
		#size-cells = <0x01>;
		ranges;

		dma-unusable@fe000000 {
			reg = <0xfe000000 0x1000000>;
		};

		framebuffer {
			compatible = "rockchip,framebuffer-memory";
			status = "disabled";
			phandle = <0x0d>;
		};
	};

	oscillator {
		compatible = "fixed-clock";
		clock-frequency = <0x16e3600>;
		clock-output-names = "xin24m";
		#clock-cells = <0x00>;
		phandle = <0x0a>;
	};

	timer {
		compatible = "arm,armv7-timer";
		arm,cpu-registers-not-fw-configured;
		interrupts = <0x01 0x0d 0xf04 0x01 0x0e 0xf04 0x01 0x0b 0xf04 0x01 0x0a 0xf04>;
		clock-frequency = <0x16e3600>;
		arm,no-tick-in-suspend;
	};

	timer@ff810000 {
		compatible = "rockchip,rk3288-timer";
		reg = <0x00 0xff810000 0x00 0x20>;
		interrupts = <0x00 0x48 0x04>;
		clocks = <0x07 0x161 0x0a>;
		clock-names = "pclk", "timer";
	};

	display-subsystem {
		compatible = "rockchip,display-subsystem";
		ports = <0x0b 0x0c>;
		memory-region = <0x0d>;
	};

	dwmmc@ff0c0000 {
		compatible = "rockchip,rk3288-dw-mshc";
		max-frequency = <0x8f0d180>;
		clocks = <0x07 0x1c8 0x07 0x44 0x07 0x72 0x07 0x76>;
		clock-names = "biu", "ciu", "ciu-drive", "ciu-sample";
		fifo-depth = <0x100>;
		interrupts = <0x00 0x20 0x04>;
		reg = <0x00 0xff0c0000 0x00 0x4000>;
		resets = <0x07 0x80>;
		reset-names = "reset";
		status = "disabled";
		bus-width = <0x04>;
		card-detect-delay = <0xc8>;
		cd-gpios = <0x0e 0x16 0x01>;
		num-slots = <0x01>;
		pinctrl-names = "default";
		pinctrl-0 = <0x0f 0x10 0x11 0x12 0x13>;
		rockchip,default-sample-phase = <0xb4>;
		vmmc-supply = <0x14>;
		vqmmc-supply = <0x15>;
		wp-gpios = <0x16 0x0c 0x00>;
		phandle = <0x9d>;
	};

	dwmmc@ff0d0000 {
		compatible = "rockchip,rk3288-dw-mshc";
		max-frequency = <0x8f0d180>;
		clocks = <0x07 0x1c9 0x07 0x45 0x07 0x73 0x07 0x77>;
		clock-names = "biu", "ciu", "ciu-drive", "ciu-sample";
		fifo-depth = <0x100>;
		interrupts = <0x00 0x21 0x04>;
		reg = <0x00 0xff0d0000 0x00 0x4000>;
		resets = <0x07 0x81>;
		reset-names = "reset";
		status = "okay";
		broken-cd;
		bus-width = <0x04>;
		cap-sd-highspeed;
		sd-uhs-sdr12;
		cap-sdio-irq;
		disable-wp;
		mmc-pwrseq = <0x17>;
		non-removable;
		num-slots = <0x01>;
		pinctrl-names = "default";
		pinctrl-0 = <0x18 0x19 0x1a 0x1b>;
		post-power-on-delay-ms = <0x96>;
		vmmc-supply = <0x1c>;
		vqmmc-supply = <0x1d>;
	};

	dwmmc@ff0e0000 {
		compatible = "rockchip,rk3288-dw-mshc";
		max-frequency = <0x8f0d180>;
		clocks = <0x07 0x1ca 0x07 0x46 0x07 0x74 0x07 0x78>;
		clock-names = "biu", "ciu", "ciu-drive", "ciu-sample";
		fifo-depth = <0x100>;
		interrupts = <0x00 0x22 0x04>;
		reg = <0x00 0xff0e0000 0x00 0x4000>;
		resets = <0x07 0x82>;
		reset-names = "reset";
		status = "disabled";
	};

	dwmmc@ff0f0000 {
		compatible = "rockchip,rk3288-dw-mshc";
		max-frequency = <0x8f0d180>;
		clocks = <0x07 0x1cb 0x07 0x47 0x07 0x75 0x07 0x79>;
		clock-names = "biu", "ciu", "ciu-drive", "ciu-sample";
		fifo-depth = <0x100>;
		interrupts = <0x00 0x23 0x04>;
		reg = <0x00 0xff0f0000 0x00 0x4000>;
		resets = <0x07 0x83>;
		reset-names = "reset";
		status = "okay";
		broken-cd;
		bus-width = <0x08>;
		cap-mmc-highspeed;
		mmc-ddr-1_8v;
		mmc-hs200-1_8v;
		disable-wp;
		non-removable;
		mmc-pwrseq = <0x1e>;
		pinctrl-names = "default";
		pinctrl-0 = <0x1f 0x20 0x21>;
		vmmc-supply = <0x1c>;
		vqmmc-supply = <0x22>;
	};

	saradc@ff100000 {
		compatible = "rockchip,saradc";
		reg = <0x00 0xff100000 0x00 0x100>;
		interrupts = <0x00 0x24 0x04>;
		#io-channel-cells = <0x01>;
		clocks = <0x07 0x49 0x07 0x15b>;
		clock-names = "saradc", "apb_pclk";
		resets = <0x07 0x57>;
		reset-names = "saradc-apb";
		status = "okay";
		vref-supply = <0x1d>;
		phandle = <0x91>;
	};

	spi@ff110000 {
		compatible = "rockchip,rk3288-spi", "rockchip,rk3066-spi";
		clocks = <0x07 0x41 0x07 0x152>;
		clock-names = "spiclk", "apb_pclk";
		dmas = <0x23 0x0b 0x23 0x0c>;
		dma-names = "tx", "rx";
		interrupts = <0x00 0x2c 0x04>;
		pinctrl-names = "default";
		pinctrl-0 = <0x24 0x25 0x26 0x27>;
		reg = <0x00 0xff110000 0x00 0x1000>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		status = "disabled";
	};

	spi@ff120000 {
		compatible = "rockchip,rk3288-spi", "rockchip,rk3066-spi";
		clocks = <0x07 0x42 0x07 0x153>;
		clock-names = "spiclk", "apb_pclk";
		dmas = <0x23 0x0d 0x23 0x0e>;
		dma-names = "tx", "rx";
		interrupts = <0x00 0x2d 0x04>;
		pinctrl-names = "default";
		pinctrl-0 = <0x28 0x29 0x2a 0x2b>;
		reg = <0x00 0xff120000 0x00 0x1000>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		status = "disabled";
	};

	spi@ff130000 {
		compatible = "rockchip,rk3288-spi", "rockchip,rk3066-spi";
		clocks = <0x07 0x43 0x07 0x154>;
		clock-names = "spiclk", "apb_pclk";
		dmas = <0x23 0x0f 0x23 0x10>;
		dma-names = "tx", "rx";
		interrupts = <0x00 0x2e 0x04>;
		pinctrl-names = "default";
		pinctrl-0 = <0x2c 0x2d 0x2e 0x2f>;
		reg = <0x00 0xff130000 0x00 0x1000>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		status = "disabled";
	};

	i2c@ff140000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff140000 0x00 0x1000>;
		interrupts = <0x00 0x3e 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x14d>;
		pinctrl-names = "default";
		pinctrl-0 = <0x30>;
		status = "okay";

		hym8563@51 {
			compatible = "haoyu,hym8563";
			reg = <0x51>;
			#clock-cells = <0x00>;
			clock-frequency = <0x8000>;
			clock-output-names = "xin32k";
			haoyu,clock-is-critical;
			interrupt-parent = <0x31>;
			interrupts = <0x06 0x02>;
			pinctrl-names = "default";
			pinctrl-0 = <0x32>;
			phandle = <0x3b>;
		};
	};

	i2c@ff150000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff150000 0x00 0x1000>;
		interrupts = <0x00 0x3f 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x14f>;
		pinctrl-names = "default";
		pinctrl-0 = <0x33>;
		status = "okay";
	};

	i2c@ff160000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff160000 0x00 0x1000>;
		interrupts = <0x00 0x40 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x150>;
		pinctrl-names = "default";
		pinctrl-0 = <0x34>;
		resets = <0x07 0x2e>;
		reset-names = "i2c";
		status = "okay";

		ili2116@41 {
			compatible = "ilitek,ili2116";
			status = "okay";
			reg = <0x41>;
			pinctrl-names = "default";
			pinctrl-0 = <0x35 0x36>;
			interrupt-parent = <0x16>;
			interrupts = <0x06 0x02>;
			irq-gpios = <0x16 0x06 0x00>;
			reset-gpios = <0x16 0x05 0x00>;
		};

		ili2117@26 {
			compatible = "ilitek,ili2117";
			status = "disabled";
			reg = <0x26 0x62>;
			reg-names = "default", "upgrade";
			pinctrl-names = "default";
			pinctrl-0 = <0x35 0x36>;
			interrupt-parent = <0x16>;
			interrupts = <0x06 0x02>;
			irq-gpios = <0x16 0x06 0x00>;
			reset-gpios = <0x16 0x05 0x00>;
		};

		st1727@55 {
			compatible = "sitronix,st1727";
			status = "disabled";
			reg = <0x55>;
			pinctrl-names = "default";
			pinctrl-0 = <0x35 0x36>;
			interrupt-parent = <0x16>;
			interrupts = <0x06 0x02>;
			gpios = <0x16 0x05 0x01>;
		};
	};

	i2c@ff170000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff170000 0x00 0x1000>;
		interrupts = <0x00 0x41 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x151>;
		pinctrl-names = "default";
		pinctrl-0 = <0x37>;
		status = "disabled";
	};

	serial@ff180000 {
		compatible = "rockchip,rk3288-uart", "snps,dw-apb-uart";
		reg = <0x00 0xff180000 0x00 0x100>;
		interrupts = <0x00 0x37 0x04>;
		reg-shift = <0x02>;
		reg-io-width = <0x04>;
		clocks = <0x07 0x4d 0x07 0x155>;
		clock-names = "baudclk", "apb_pclk";
		pinctrl-names = "default";
		pinctrl-0 = <0x38 0x39 0x3a>;
		status = "okay";
		assigned-clocks = <0x07 0x4d>;
		assigned-clock-rates = <0x16e3600>;

		bluetooth {
			compatible = "brcm,bcm43438-bt";
			clocks = <0x3b>;
			max-speed = <0x16e360>;
			pinctrl-names = "default";
			pinctrl-0 = <0x3c 0x3d 0x3e>;
			device-wakeup-gpios = <0x3f 0x1a 0x00>;
			shutdown-gpios = <0x3f 0x1d 0x00>;
		};
	};

	serial@ff190000 {
		compatible = "rockchip,rk3288-uart", "snps,dw-apb-uart";
		reg = <0x00 0xff190000 0x00 0x100>;
		interrupts = <0x00 0x38 0x04>;
		reg-shift = <0x02>;
		reg-io-width = <0x04>;
		clocks = <0x07 0x4e 0x07 0x156>;
		clock-names = "baudclk", "apb_pclk";
		pinctrl-names = "default";
		pinctrl-0 = <0x40 0x41 0x42>;
		status = "okay";

		control-surface {
			compatible = "serial-midi";
			current-speed = <0x1c200>;
			label = "Control Surface";
		};
	};

	serial@ff690000 {
		compatible = "rockchip,rk3288-uart", "snps,dw-apb-uart";
		reg = <0x00 0xff690000 0x00 0x100>;
		interrupts = <0x00 0x39 0x04>;
		reg-shift = <0x02>;
		reg-io-width = <0x04>;
		clocks = <0x07 0x4f 0x07 0x157>;
		clock-names = "baudclk", "apb_pclk";
		pinctrl-names = "default";
		pinctrl-0 = <0x43>;
		status = "okay";
	};

	serial@ff1b0000 {
		compatible = "rockchip,rk3288-uart", "snps,dw-apb-uart";
		reg = <0x00 0xff1b0000 0x00 0x100>;
		interrupts = <0x00 0x3a 0x04>;
		reg-shift = <0x02>;
		reg-io-width = <0x04>;
		clocks = <0x07 0x50 0x07 0x158>;
		clock-names = "baudclk", "apb_pclk";
		pinctrl-names = "default";
		pinctrl-0 = <0x44>;
		status = "disabled";
	};

	serial@ff1c0000 {
		compatible = "rockchip,rk3288-uart", "snps,dw-apb-uart";
		reg = <0x00 0xff1c0000 0x00 0x100>;
		interrupts = <0x00 0x3b 0x04>;
		reg-shift = <0x02>;
		reg-io-width = <0x04>;
		clocks = <0x07 0x51 0x07 0x159>;
		clock-names = "baudclk", "apb_pclk";
		pinctrl-names = "default";
		pinctrl-0 = <0x45>;
		status = "disabled";
	};

	dma-controller@ff250000 {
		compatible = "arm,pl330", "arm,primecell";
		reg = <0x00 0xff250000 0x00 0x4000>;
		interrupts = <0x00 0x02 0x04 0x00 0x03 0x04>;
		#dma-cells = <0x01>;
		arm,pl330-broken-no-flushp;
		arm,pl330-periph-burst;
		clocks = <0x07 0xc2>;
		clock-names = "apb_pclk";
		phandle = <0x23>;
	};

	thermal-zones {

		reserve-thermal {
			polling-delay-passive = <0x3e8>;
			polling-delay = <0x1388>;
			thermal-sensors = <0x46 0x00>;
		};

		cpu-thermal {
			polling-delay-passive = <0x64>;
			polling-delay = <0x1388>;
			thermal-sensors = <0x46 0x01>;

			trips {

				cpu_crit {
					temperature = <0x1b968>;
					hysteresis = <0x7d0>;
					type = "critical";
				};
			};
		};

		gpu-thermal {
			polling-delay-passive = <0x64>;
			polling-delay = <0x1388>;
			thermal-sensors = <0x46 0x02>;

			trips {

				gpu_crit {
					temperature = <0x1b968>;
					hysteresis = <0x7d0>;
					type = "critical";
				};
			};
		};
	};

	tsadc@ff280000 {
		compatible = "rockchip,rk3288-tsadc";
		reg = <0x00 0xff280000 0x00 0x100>;
		interrupts = <0x00 0x25 0x04>;
		clocks = <0x07 0x48 0x07 0x15a>;
		clock-names = "tsadc", "apb_pclk";
		resets = <0x07 0x9f>;
		reset-names = "tsadc-apb";
		pinctrl-names = "init", "default", "sleep";
		pinctrl-0 = <0x47>;
		pinctrl-1 = <0x48>;
		pinctrl-2 = <0x47>;
		#thermal-sensor-cells = <0x01>;
		rockchip,grf = <0x49>;
		rockchip,hw-tshut-temp = <0x1c138>;
		status = "okay";
		rockchip,hw-tshut-mode = <0x00>;
		rockchip,hw-tshut-polarity = <0x00>;
		phandle = <0x46>;
	};

	ethernet@ff290000 {
		compatible = "rockchip,rk3288-gmac";
		reg = <0x00 0xff290000 0x00 0x10000>;
		interrupts = <0x00 0x1b 0x04 0x00 0x1c 0x04>;
		interrupt-names = "macirq", "eth_wake_irq";
		rockchip,grf = <0x49>;
		clocks = <0x07 0x97 0x07 0x66 0x07 0x67 0x07 0x63 0x07 0x98 0x07 0xc4 0x07 0x15d>;
		clock-names = "stmmaceth", "mac_clk_rx", "mac_clk_tx", "clk_mac_ref", "clk_mac_refout", "aclk_mac", "pclk_mac";
		resets = <0x07 0x42>;
		reset-names = "stmmaceth";
		status = "disabled";
		assigned-clocks = <0x07 0x97>;
		assigned-clock-parents = <0x4a>;
		clock_in_out = "input";
		phy-handle = <0x4b>;
		phy-mode = "rgmii";
		phy-supply = <0x4c>;
		pinctrl-names = "default";
		pinctrl-0 = <0x4d 0x4e 0x4f>;
		rx_delay = <0x16>;
		tx_delay = <0x28>;

		mdio {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			compatible = "snps,dwmac-mdio";

			ethernet-phy@1 {
				compatible = "ethernet-phy-ieee802.3-c22";
				reg = <0x01>;
				eee-broken-100tx;
				eee-broken-1000t;
				interrupt-parent = <0x50>;
				interrupts = <0x0f 0x08>;
				reset-gpios = <0x3f 0x08 0x01>;
				reset-assert-us = <0x2710>;
				reset-deassert-us = <0x7530>;
				phandle = <0x4b>;
			};
		};
	};

	usb@ff500000 {
		compatible = "generic-ehci";
		reg = <0x00 0xff500000 0x00 0x100>;
		interrupts = <0x00 0x18 0x04>;
		clocks = <0x07 0x1c2>;
		phys = <0x51>;
		phy-names = "usb";
		status = "okay";
	};

	usb@ff520000 {
		compatible = "generic-ohci";
		reg = <0x00 0xff520000 0x00 0x100>;
		interrupts = <0x00 0x29 0x04>;
		clocks = <0x07 0x1c2>;
		phys = <0x51>;
		phy-names = "usb";
		resets = <0x07 0x48>;
		reset-names = "usbhost";
		status = "okay";
	};

	usb@ff540000 {
		compatible = "rockchip,rk3288-usb", "rockchip,rk3066-usb", "snps,dwc2";
		reg = <0x00 0xff540000 0x00 0x40000>;
		interrupts = <0x00 0x19 0x04>;
		clocks = <0x07 0x1c3>;
		clock-names = "otg";
		dr_mode = "host";
		phys = <0x52>;
		phy-names = "usb2-phy";
		snps,reset-phy-on-wake;
		status = "okay";
	};

	usb@ff580000 {
		compatible = "rockchip,rk3288-usb", "rockchip,rk3066-usb", "snps,dwc2";
		reg = <0x00 0xff580000 0x00 0x40000>;
		interrupts = <0x00 0x17 0x04>;
		clocks = <0x07 0x1c1>;
		clock-names = "otg";
		dr_mode = "peripheral";
		g-np-tx-fifo-size = <0x10>;
		g-rx-fifo-size = <0x113>;
		g-tx-fifo-size = <0x100 0x80 0x80 0x40 0x40 0x20>;
		phys = <0x53>;
		phy-names = "usb2-phy";
		status = "okay";
	};

	usb@ff5c0000 {
		compatible = "generic-ehci";
		reg = <0x00 0xff5c0000 0x00 0x100>;
		interrupts = <0x00 0x1a 0x04>;
		clocks = <0x07 0x1c4>;
		status = "disabled";
	};

	dma-controller@ff600000 {
		compatible = "arm,pl330", "arm,primecell";
		reg = <0x00 0xff600000 0x00 0x4000>;
		interrupts = <0x00 0x00 0x04 0x00 0x01 0x04>;
		#dma-cells = <0x01>;
		arm,pl330-broken-no-flushp;
		arm,pl330-periph-burst;
		clocks = <0x07 0xc1>;
		clock-names = "apb_pclk";
		status = "disabled";
	};

	i2c@ff650000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff650000 0x00 0x1000>;
		interrupts = <0x00 0x3c 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x14c>;
		pinctrl-names = "default";
		pinctrl-0 = <0x54>;
		status = "okay";

		act8846@5a {
			compatible = "active-semi,act8846";
			reg = <0x5a>;
			system-power-controller;
			inl1-supply = <0x1c>;
			inl2-supply = <0x55>;
			inl3-supply = <0x56>;
			vp1-supply = <0x55>;
			vp2-supply = <0x55>;
			vp3-supply = <0x55>;
			vp4-supply = <0x55>;

			regulators {

				REG1 {
					regulator-name = "VCC_DDR";
					regulator-min-microvolt = <0x124f80>;
					regulator-max-microvolt = <0x16e360>;
					regulator-always-on;
				};

				REG2 {
					regulator-name = "VCC_IO";
					regulator-min-microvolt = <0x325aa0>;
					regulator-max-microvolt = <0x325aa0>;
					regulator-always-on;
					phandle = <0x1c>;
				};

				REG3 {
					regulator-name = "VDD_LOG";
					regulator-min-microvolt = <0xe7ef0>;
					regulator-max-microvolt = <0x155cc0>;
					regulator-always-on;
				};

				REG4 {
					regulator-name = "VCC_20";
					regulator-min-microvolt = <0x1e8480>;
					regulator-max-microvolt = <0x1e8480>;
					regulator-always-on;
					phandle = <0x56>;
				};

				REG5 {
					regulator-name = "VCCIO_SD";
					regulator-min-microvolt = <0x1b7740>;
					regulator-max-microvolt = <0x325aa0>;
					regulator-always-on;
					phandle = <0x15>;
				};

				REG6 {
					regulator-name = "VDD10_LCD";
					regulator-min-microvolt = <0xf4240>;
					regulator-max-microvolt = <0xf4240>;
					regulator-always-on;
				};

				REG7 {
					regulator-name = "VCCA_CODEC";
					regulator-min-microvolt = <0x325aa0>;
					regulator-max-microvolt = <0x325aa0>;
					regulator-always-on;
				};

				REG8 {
					regulator-name = "VCCA_TP";
					regulator-min-microvolt = <0x325aa0>;
					regulator-max-microvolt = <0x325aa0>;
					regulator-always-on;
				};

				REG9 {
					regulator-name = "VCCIO_PMU";
					regulator-min-microvolt = <0x325aa0>;
					regulator-max-microvolt = <0x325aa0>;
					regulator-always-on;
					phandle = <0x4c>;
				};

				REG10 {
					regulator-name = "VDD_10";
					regulator-min-microvolt = <0xf4240>;
					regulator-max-microvolt = <0xf4240>;
					regulator-always-on;
				};

				REG11 {
					regulator-name = "VCC_18";
					regulator-min-microvolt = <0x1b7740>;
					regulator-max-microvolt = <0x1b7740>;
					regulator-always-on;
					phandle = <0x1d>;
				};

				REG12 {
					regulator-name = "VCC18_LCD";
					regulator-min-microvolt = <0x1b7740>;
					regulator-max-microvolt = <0x1b7740>;
					regulator-always-on;
				};
			};
		};

		syr827@40 {
			compatible = "silergy,syr827";
			reg = <0x40>;
			fcs,suspend-voltage-selector = <0x01>;
			regulator-always-on;
			regulator-boot-on;
			regulator-enable-ramp-delay = <0x12c>;
			regulator-initial-mode = <0x01>;
			regulator-name = "vdd_cpu";
			regulator-min-microvolt = <0xcf850>;
			regulator-max-microvolt = <0x16e360>;
			regulator-ramp-delay = <0x1f40>;
			vin-supply = <0x55>;
			phandle = <0x09>;
		};

		syr828@41 {
			compatible = "silergy,syr828";
			reg = <0x41>;
			fcs,suspend-voltage-selector = <0x01>;
			regulator-always-on;
			regulator-enable-ramp-delay = <0x12c>;
			regulator-initial-mode = <0x01>;
			regulator-min-microvolt = <0xcf850>;
			regulator-max-microvolt = <0x149970>;
			regulator-name = "vdd_gpu";
			regulator-ramp-delay = <0x1f40>;
			vin-supply = <0x55>;
			phandle = <0x89>;
		};
	};

	i2c@ff660000 {
		compatible = "rockchip,rk3288-i2c";
		reg = <0x00 0xff660000 0x00 0x1000>;
		interrupts = <0x00 0x3d 0x04>;
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		clock-names = "i2c";
		clocks = <0x07 0x14e>;
		pinctrl-names = "default";
		pinctrl-0 = <0x57>;
		status = "okay";

		tas5805m@2d {
			compatible = "ti,tas5805m";
			reg = <0x2d>;
			#sound-dai-cells = <0x00>;
			clocks = <0x58 0x02 0x58 0x08>;
			clock-names = "sclk", "lrclk";
			dvdd-supply = <0x59>;
			interrupt-parent = <0x16>;
			interrupts = <0x14 0x02>;
			pdn-gpios = <0x16 0x13 0x01>;
			pinctrl-names = "default";
			pinctrl-0 = <0x5a 0x5b>;
			pvdd-supply = <0x55>;
			phandle = <0xa0>;
		};

		eta5805@2f {
			compatible = "eta,eta5805";
			reg = <0x2f>;
			#sound-dai-cells = <0x00>;
			clocks = <0x58 0x02 0x58 0x08>;
			clock-names = "bclk", "fsync";
			eta,dsp-config-name = "nh08";
			dvdd-supply = <0x59>;
			pdn-gpios = <0x16 0x13 0x01>;
			pinctrl-names = "default";
			pinctrl-0 = <0x5a 0x5b>;
			pvdd-supply = <0x55>;
			status = "disabled";
		};
	};

	pwm@ff680000 {
		compatible = "rockchip,rk3288-pwm";
		reg = <0x00 0xff680000 0x00 0x10>;
		#pwm-cells = <0x03>;
		pinctrl-names = "default";
		pinctrl-0 = <0x5c>;
		clocks = <0x07 0x15f>;
		status = "okay";
		phandle = <0x95>;
	};

	pwm@ff680010 {
		compatible = "rockchip,rk3288-pwm";
		reg = <0x00 0xff680010 0x00 0x10>;
		#pwm-cells = <0x03>;
		pinctrl-names = "default";
		pinctrl-0 = <0x5d>;
		clocks = <0x07 0x15f>;
		status = "disabled";
	};

	pwm@ff680020 {
		compatible = "rockchip,rk3288-pwm";
		reg = <0x00 0xff680020 0x00 0x10>;
		#pwm-cells = <0x03>;
		pinctrl-names = "default";
		pinctrl-0 = <0x5e>;
		clocks = <0x07 0x15f>;
		status = "disabled";
	};

	pwm@ff680030 {
		compatible = "rockchip,rk3288-pwm";
		reg = <0x00 0xff680030 0x00 0x10>;
		#pwm-cells = <0x03>;
		pinctrl-names = "default";
		pinctrl-0 = <0x5f>;
		clocks = <0x07 0x15f>;
		status = "disabled";
	};

	sram@ff700000 {
		compatible = "mmio-sram";
		reg = <0x00 0xff700000 0x00 0x18000>;
		#address-cells = <0x01>;
		#size-cells = <0x01>;
		ranges = <0x00 0x00 0xff700000 0x18000>;

		smp-sram@0 {
			compatible = "rockchip,rk3066-smp-sram";
			reg = <0x00 0x10>;
		};
	};

	sram@ff720000 {
		compatible = "rockchip,rk3288-pmu-sram", "mmio-sram";
		reg = <0x00 0xff720000 0x00 0x1000>;
	};

	power-management@ff730000 {
		compatible = "rockchip,rk3288-pmu", "syscon", "simple-mfd";
		reg = <0x00 0xff730000 0x00 0x100>;
		phandle = <0x06>;

		power-controller {
			compatible = "rockchip,rk3288-power-controller";
			#power-domain-cells = <0x01>;
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			assigned-clocks = <0x07 0x68>;
			assigned-clock-parents = <0x0a>;
			phandle = <0x71>;

			power-domain@9 {
				reg = <0x09>;
				rockchip,pd-always-on;
				clocks = <0x07 0xca 0x07 0xcd 0x07 0xc8 0x07 0xcc 0x07 0xc5 0x07 0xc6 0x07 0xbe 0x07 0xbf 0x07 0x1d4 0x07 0x1d5 0x07 0x1d6 0x07 0x1d9 0x07 0x1d1 0x07 0x1d2 0x07 0x163 0x07 0x168 0x07 0x167 0x07 0x166 0x07 0x164 0x07 0x165 0x07 0x68 0x07 0x69 0x07 0x6c 0x07 0x6b 0x07 0x6a>;
				pm_qos = <0x60 0x61 0x62 0x63 0x64 0x65 0x66 0x67 0x68>;
				#power-domain-cells = <0x00>;
			};

			power-domain@11 {
				reg = <0x0b>;
				clocks = <0x07 0xcf 0x07 0x6f 0x07 0x70>;
				pm_qos = <0x69 0x6a>;
				#power-domain-cells = <0x00>;
			};

			power-domain@12 {
				reg = <0x0c>;
				clocks = <0x07 0xd0 0x07 0x1dc>;
				pm_qos = <0x6b>;
				#power-domain-cells = <0x00>;
			};

			power-domain@13 {
				reg = <0x0d>;
				clocks = <0x07 0xc0>;
				pm_qos = <0x6c 0x6d>;
				#power-domain-cells = <0x00>;
			};
		};

		reboot-mode {
			compatible = "syscon-reboot-mode";
			offset = <0x94>;
			mode-normal = <0x5242c300>;
			mode-recovery = <0x5242c303>;
			mode-bootloader = <0x5242c309>;
			mode-loader = <0x5242c301>;
		};

		bootcount {
			compatible = "syscon-bootcount";
			offset = <0xa0>;
			magic = <0xb001c041>;
		};
	};

	syscon@ff740000 {
		compatible = "rockchip,rk3288-sgrf", "syscon";
		reg = <0x00 0xff740000 0x00 0x1000>;
	};

	clock-controller@ff760000 {
		compatible = "rockchip,rk3288-cru";
		reg = <0x00 0xff760000 0x00 0x1000>;
		clocks = <0x0a>;
		clock-names = "xin24m";
		rockchip,grf = <0x49>;
		#clock-cells = <0x01>;
		#reset-cells = <0x01>;
		assigned-clocks = <0x07 0xbe 0x07 0xbf 0x07 0x04 0x07 0x03 0x07 0x05 0x07 0xd1 0x07 0x1dd 0x07 0x16a 0x07 0xd2 0x07 0x1de 0x07 0x16b>;
		assigned-clock-rates = <0x00 0x00 0x2367b880 0x17d78400 0x00 0x11e1a300 0x8f0d180 0x47868c0 0x11e1a300 0x8f0d180 0x47868c0>;
		assigned-clock-parents = <0x07 0x04 0x07 0x05>;
		phandle = <0x07>;
	};

	syscon@ff770000 {
		compatible = "rockchip,rk3288-grf", "syscon", "simple-mfd";
		reg = <0x00 0xff770000 0x00 0x1000>;
		phandle = <0x49>;

		edp-phy {
			compatible = "rockchip,rk3288-dp-phy";
			clocks = <0x07 0x68>;
			clock-names = "24m";
			#phy-cells = <0x00>;
			status = "disabled";
			phandle = <0x83>;
		};

		io-domains {
			compatible = "rockchip,rk3288-io-voltage-domain";
			status = "okay";
			audio-supply = <0x1c>;
			bb-supply = <0x1d>;
			dvp-supply = <0x1c>;
			flash0-supply = <0x22>;
			flash1-supply = <0x4c>;
			gpio30-supply = <0x4c>;
			gpio1830 = <0x1c>;
			lcdc-supply = <0x1c>;
			sdcard-supply = <0x15>;
			wifi-supply = <0x1d>;
		};

		usbphy {
			compatible = "rockchip,rk3288-usb-phy";
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			status = "okay";

			usb-phy@320 {
				#phy-cells = <0x00>;
				reg = <0x320>;
				clocks = <0x07 0x5d>;
				clock-names = "phyclk";
				#clock-cells = <0x00>;
				resets = <0x07 0x85>;
				reset-names = "phy-reset";
				phandle = <0x53>;
			};

			usb-phy@334 {
				#phy-cells = <0x00>;
				reg = <0x334>;
				clocks = <0x07 0x5e>;
				clock-names = "phyclk";
				#clock-cells = <0x00>;
				resets = <0x07 0x88>;
				reset-names = "phy-reset";
				phandle = <0x51>;
			};

			usb-phy@348 {
				#phy-cells = <0x00>;
				reg = <0x348>;
				clocks = <0x07 0x5f>;
				clock-names = "phyclk";
				#clock-cells = <0x00>;
				resets = <0x07 0x8b>;
				reset-names = "phy-reset";
				phandle = <0x52>;
			};
		};
	};

	watchdog@ff800000 {
		compatible = "rockchip,rk3288-wdt", "snps,dw-wdt";
		reg = <0x00 0xff800000 0x00 0x100>;
		clocks = <0x07 0x170>;
		interrupts = <0x00 0x4f 0x04>;
		status = "okay";
	};

	sound@ff8b0000 {
		compatible = "rockchip,rk3288-spdif", "rockchip,rk3066-spdif";
		reg = <0x00 0xff8b0000 0x00 0x10000>;
		#sound-dai-cells = <0x00>;
		clocks = <0x07 0x54 0x07 0x1d0>;
		clock-names = "mclk", "hclk";
		dmas = <0x6e 0x03>;
		dma-names = "tx";
		interrupts = <0x00 0x36 0x04>;
		pinctrl-names = "default";
		pinctrl-0 = <0x6f>;
		rockchip,grf = <0x49>;
		status = "disabled";
	};

	i2s@ff890000 {
		compatible = "rockchip,rk3288-i2s", "rockchip,rk3066-i2s";
		reg = <0x00 0xff890000 0x00 0x10000>;
		#sound-dai-cells = <0x00>;
		interrupts = <0x00 0x35 0x04>;
		clocks = <0x07 0x52 0x07 0x1ce>;
		clock-names = "i2s_clk", "i2s_hclk";
		dmas = <0x6e 0x00 0x6e 0x01>;
		dma-names = "tx", "rx";
		pinctrl-names = "default";
		pinctrl-0 = <0x70>;
		rockchip,playback-channels = <0x08>;
		rockchip,capture-channels = <0x02>;
		status = "okay";
		assigned-clocks = <0x07 0x80 0x07 0x81>;
		assigned-clock-parents = <0x07 0x82 0x07 0x04>;
		assigned-clock-rates = <0x00 0x2367b880>;
		phandle = <0x9e>;
	};

	crypto@ff8a0000 {
		compatible = "rockchip,rk3288-crypto";
		reg = <0x00 0xff8a0000 0x00 0x4000>;
		interrupts = <0x00 0x30 0x04>;
		clocks = <0x07 0xc7 0x07 0x1cd 0x07 0x7d 0x07 0xc1>;
		clock-names = "aclk", "hclk", "sclk", "apb_pclk";
		resets = <0x07 0xae>;
		reset-names = "crypto-rst";
	};

	iommu@ff900800 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff900800 0x00 0x40>;
		interrupts = <0x00 0x11 0x04>;
		clocks = <0x07 0xca 0x07 0x1d4>;
		clock-names = "aclk", "iface";
		#iommu-cells = <0x00>;
		status = "disabled";
	};

	iommu@ff914000 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff914000 0x00 0x100 0x00 0xff915000 0x00 0x100>;
		interrupts = <0x00 0x0e 0x04>;
		clocks = <0x07 0xcd 0x07 0x1d5>;
		clock-names = "aclk", "iface";
		#iommu-cells = <0x00>;
		rockchip,disable-mmu-reset;
		status = "disabled";
	};

	rga@ff920000 {
		compatible = "rockchip,rk3288-rga2";
		reg = <0x00 0xff920000 0x00 0x10000>;
		interrupts = <0x00 0x12 0x04>;
		clocks = <0x07 0xc8 0x07 0x1d6 0x07 0x6a>;
		clock-names = "aclk", "hclk", "rga";
		power-domains = <0x71 0x09>;
		resets = <0x07 0x69 0x07 0x6c 0x07 0x6d>;
		reset-names = "core", "axi", "ahb";
		status = "okay";
	};

	vop@ff930000 {
		compatible = "rockchip,rk3288-vop";
		reg = <0x00 0xff930000 0x00 0x19c 0x00 0xff931000 0x00 0x1000>;
		interrupts = <0x00 0x0f 0x04>;
		clocks = <0x07 0xc5 0x07 0xbe 0x07 0x1d1>;
		clock-names = "aclk_vop", "dclk_vop", "hclk_vop";
		power-domains = <0x71 0x09>;
		resets = <0x07 0x64 0x07 0x65 0x07 0x66>;
		reset-names = "axi", "ahb", "dclk";
		iommus = <0x72>;
		status = "disabled";

		port {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			phandle = <0x0c>;

			endpoint@0 {
				reg = <0x00>;
				remote-endpoint = <0x73>;
				phandle = <0x86>;
			};

			endpoint@1 {
				reg = <0x01>;
				remote-endpoint = <0x74>;
				phandle = <0x84>;
			};

			endpoint@3 {
				reg = <0x03>;
				remote-endpoint = <0x75>;
				phandle = <0x81>;
			};
		};
	};

	iommu@ff930300 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff930300 0x00 0x100>;
		interrupts = <0x00 0x0f 0x04>;
		clocks = <0x07 0xc5 0x07 0x1d1>;
		clock-names = "aclk", "iface";
		power-domains = <0x71 0x09>;
		#iommu-cells = <0x00>;
		status = "disabled";
		phandle = <0x72>;
	};

	vop@ff940000 {
		compatible = "rockchip,rk3288-vop";
		reg = <0x00 0xff940000 0x00 0x19c 0x00 0xff941000 0x00 0x1000>;
		interrupts = <0x00 0x10 0x04>;
		clocks = <0x07 0xc6 0x07 0xbf 0x07 0x1d2>;
		clock-names = "aclk_vop", "dclk_vop", "hclk_vop";
		power-domains = <0x71 0x09>;
		resets = <0x07 0xb0 0x07 0xb1 0x07 0xb2>;
		reset-names = "axi", "ahb", "dclk";
		iommus = <0x76>;
		status = "okay";

		port {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			phandle = <0x0b>;

			endpoint@1 {
				reg = <0x01>;
				remote-endpoint = <0x77>;
				phandle = <0x85>;
			};

			endpoint@2 {
				reg = <0x02>;
				remote-endpoint = <0x78>;
				phandle = <0x7a>;
			};

			endpoint@3 {
				reg = <0x03>;
				remote-endpoint = <0x79>;
				phandle = <0x82>;
			};
		};
	};

	iommu@ff940300 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff940300 0x00 0x100>;
		interrupts = <0x00 0x10 0x04>;
		clocks = <0x07 0xc6 0x07 0x1d2>;
		clock-names = "aclk", "iface";
		power-domains = <0x71 0x09>;
		#iommu-cells = <0x00>;
		status = "okay";
		phandle = <0x76>;
	};

	mipi@ff960000 {
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		compatible = "rockchip,rk3288-mipi-dsi", "snps,dw-mipi-dsi";
		reg = <0x00 0xff960000 0x00 0x4000>;
		interrupts = <0x00 0x13 0x04>;
		clocks = <0x07 0x7e 0x07 0x164>;
		clock-names = "ref", "pclk";
		power-domains = <0x71 0x09>;
		resets = <0x07 0x73>;
		reset-names = "apb";
		rockchip,grf = <0x49>;
		status = "okay";

		ports {
			#address-cells = <0x01>;
			#size-cells = <0x00>;

			port@0 {
				reg = <0x00>;
				#address-cells = <0x01>;
				#size-cells = <0x00>;

				endpoint@1 {
					reg = <0x01>;
					remote-endpoint = <0x7a>;
					phandle = <0x78>;
				};
			};

			port@1 {
				reg = <0x01>;

				endpoint {
					remote-endpoint = <0x7b>;
					phandle = <0x7f>;
				};
			};
		};

		panel@0 {
			compatible = "urt,umo-p076md-t";
			reg = <0x00>;
			pinctrl-names = "default";
			pinctrl-0 = <0x7c>;
			power-supply = <0x7d>;
			reset-gpios = <0x16 0x03 0x00>;
			backlight = <0x7e>;
			rotation = <0x5a>;

			ports {
				#address-cells = <0x01>;
				#size-cells = <0x00>;

				port@0 {
					reg = <0x00>;

					endpoint {
						remote-endpoint = <0x7f>;
						phandle = <0x7b>;
					};
				};
			};
		};
	};

	lvds@ff96c000 {
		compatible = "rockchip,rk3288-lvds";
		reg = <0x00 0xff96c000 0x00 0x4000>;
		clocks = <0x07 0x167>;
		clock-names = "pclk_lvds";
		pinctrl-names = "lcdc";
		pinctrl-0 = <0x80>;
		power-domains = <0x71 0x09>;
		rockchip,grf = <0x49>;
		status = "disabled";

		ports {
			#address-cells = <0x01>;
			#size-cells = <0x00>;

			port@0 {
				reg = <0x00>;
				#address-cells = <0x01>;
				#size-cells = <0x00>;

				endpoint@0 {
					reg = <0x00>;
					remote-endpoint = <0x81>;
					phandle = <0x75>;
				};

				endpoint@1 {
					reg = <0x01>;
					remote-endpoint = <0x82>;
					phandle = <0x79>;
				};
			};
		};
	};

	dp@ff970000 {
		compatible = "rockchip,rk3288-dp";
		reg = <0x00 0xff970000 0x00 0x4000>;
		interrupts = <0x00 0x62 0x04>;
		clocks = <0x07 0x69 0x07 0x163>;
		clock-names = "dp", "pclk";
		phys = <0x83>;
		phy-names = "dp";
		power-domains = <0x71 0x09>;
		resets = <0x07 0x6f>;
		reset-names = "dp";
		rockchip,grf = <0x49>;
		status = "disabled";

		ports {
			#address-cells = <0x01>;
			#size-cells = <0x00>;

			port@0 {
				reg = <0x00>;
				#address-cells = <0x01>;
				#size-cells = <0x00>;

				endpoint@0 {
					reg = <0x00>;
					remote-endpoint = <0x84>;
					phandle = <0x74>;
				};

				endpoint@1 {
					reg = <0x01>;
					remote-endpoint = <0x85>;
					phandle = <0x77>;
				};
			};
		};
	};

	hdmi@ff980000 {
		compatible = "rockchip,rk3288-dw-hdmi";
		reg = <0x00 0xff980000 0x00 0x20000>;
		reg-io-width = <0x04>;
		#sound-dai-cells = <0x00>;
		rockchip,grf = <0x49>;
		interrupts = <0x00 0x67 0x04>;
		clocks = <0x07 0x168 0x07 0x6d 0x07 0x6e>;
		clock-names = "iahb", "isfr", "cec";
		power-domains = <0x71 0x09>;
		status = "disabled";

		ports {

			port {
				#address-cells = <0x01>;
				#size-cells = <0x00>;

				endpoint@0 {
					reg = <0x00>;
					remote-endpoint = <0x86>;
					phandle = <0x73>;
				};
			};
		};
	};

	video-codec@ff9a0000 {
		compatible = "rockchip,rk3288-vpu";
		reg = <0x00 0xff9a0000 0x00 0x800>;
		interrupts = <0x00 0x09 0x04 0x00 0x0a 0x04>;
		interrupt-names = "vepu", "vdpu";
		clocks = <0x07 0xd0 0x07 0x1dc>;
		clock-names = "aclk", "hclk";
		iommus = <0x87>;
		power-domains = <0x71 0x0c>;
	};

	iommu@ff9a0800 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff9a0800 0x00 0x100>;
		interrupts = <0x00 0x0b 0x04>;
		clocks = <0x07 0xd0 0x07 0x1dc>;
		clock-names = "aclk", "iface";
		#iommu-cells = <0x00>;
		power-domains = <0x71 0x0c>;
		phandle = <0x87>;
	};

	iommu@ff9c0440 {
		compatible = "rockchip,iommu";
		reg = <0x00 0xff9c0440 0x00 0x40 0x00 0xff9c0480 0x00 0x40>;
		interrupts = <0x00 0x6f 0x04>;
		clocks = <0x07 0xcf 0x07 0x1db>;
		clock-names = "aclk", "iface";
		#iommu-cells = <0x00>;
		status = "disabled";
	};

	gpu@ffa30000 {
		compatible = "rockchip,rk3288-mali", "arm,mali-t760";
		reg = <0x00 0xffa30000 0x00 0x10000>;
		interrupts = <0x00 0x06 0x04 0x00 0x07 0x04 0x00 0x08 0x04>;
		interrupt-names = "job", "mmu", "gpu";
		clocks = <0x07 0xc0>;
		operating-points-v2 = <0x88>;
		#cooling-cells = <0x02>;
		power-domains = <0x71 0x0d>;
		status = "okay";
		dynamic-power-coefficient = <0x2dd>;
		upthreshold = <0x4b>;
		downdifferential = <0x0a>;
		mali-supply = <0x89>;

		power_model {
			compatible = "arm,mali-simple-power-model";
			static-coefficient = <0x64578>;
			dynamic-coefficient = <0x2dd>;
			ts = <0x7d00 0x125c 0xffffffb0 0x02>;
			thermal-zone = "gpu-thermal";
		};
	};

	opp-table-1 {
		compatible = "operating-points-v2";
		phandle = <0x88>;

		opp-100000000 {
			opp-hz = <0x00 0x5f5e100>;
			opp-microvolt = <0xe7ef0>;
		};

		opp-200000000 {
			opp-hz = <0x00 0xbebc200>;
			opp-microvolt = <0xe7ef0>;
		};

		opp-300000000 {
			opp-hz = <0x00 0x11e1a300>;
			opp-microvolt = <0xf4240>;
		};

		opp-400000000 {
			opp-hz = <0x00 0x17d78400>;
			opp-microvolt = <0x10c8e0>;
		};
	};

	qos@ffaa0000 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffaa0000 0x00 0x20>;
		phandle = <0x6c>;
	};

	qos@ffaa0080 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffaa0080 0x00 0x20>;
		phandle = <0x6d>;
	};

	qos@ffad0000 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0000 0x00 0x20>;
		phandle = <0x61>;
	};

	qos@ffad0100 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0100 0x00 0x20>;
		phandle = <0x62>;
	};

	qos@ffad0180 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0180 0x00 0x20>;
		phandle = <0x63>;
	};

	qos@ffad0400 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0400 0x00 0x20>;
		phandle = <0x64>;
	};

	qos@ffad0480 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0480 0x00 0x20>;
		phandle = <0x65>;
	};

	qos@ffad0500 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0500 0x00 0x20>;
		phandle = <0x60>;
	};

	qos@ffad0800 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0800 0x00 0x20>;
		phandle = <0x66>;
	};

	qos@ffad0880 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0880 0x00 0x20>;
		phandle = <0x67>;
	};

	qos@ffad0900 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffad0900 0x00 0x20>;
		phandle = <0x68>;
	};

	qos@ffae0000 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffae0000 0x00 0x20>;
		phandle = <0x6b>;
	};

	qos@ffaf0000 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffaf0000 0x00 0x20>;
		phandle = <0x69>;
	};

	qos@ffaf0080 {
		compatible = "rockchip,rk3288-qos", "syscon";
		reg = <0x00 0xffaf0080 0x00 0x20>;
		phandle = <0x6a>;
	};

	efuse@ffb10000 {
		compatible = "rockchip,rk3288-secure-efuse";
		reg = <0x00 0xffb10000 0x00 0x20>;
		#address-cells = <0x01>;
		#size-cells = <0x01>;
		clocks = <0x07 0x172>;
		clock-names = "pclk_efuse";
		rockchip,efuse-size = <0x80>;
	};

	dma-controller@ffb20000 {
		compatible = "arm,pl330", "arm,primecell";
		reg = <0x00 0xffb20000 0x00 0x4000>;
		interrupts = <0x00 0x00 0x04 0x00 0x01 0x04>;
		#dma-cells = <0x01>;
		arm,pl330-broken-no-flushp;
		arm,pl330-periph-burst;
		clocks = <0x07 0xc1>;
		clock-names = "apb_pclk";
		phandle = <0x6e>;
	};

	efuse@ffb40000 {
		compatible = "rockchip,rk3288-efuse";
		reg = <0x00 0xffb40000 0x00 0x20>;
		#address-cells = <0x01>;
		#size-cells = <0x01>;
		clocks = <0x07 0x171>;
		clock-names = "pclk_efuse";

		cpu-id@7 {
			reg = <0x07 0x10>;
		};

		cpu_leakage@17 {
			reg = <0x17 0x01>;
		};
	};

	interrupt-controller@ffc01000 {
		compatible = "arm,gic-400";
		interrupt-controller;
		#interrupt-cells = <0x03>;
		#address-cells = <0x00>;
		reg = <0x00 0xffc01000 0x00 0x1000 0x00 0xffc02000 0x00 0x2000 0x00 0xffc04000 0x00 0x2000 0x00 0xffc06000 0x00 0x2000>;
		interrupts = <0x01 0x09 0xf04>;
		phandle = <0x01>;
	};

	pinctrl {
		compatible = "rockchip,rk3288-pinctrl";
		rockchip,grf = <0x49>;
		rockchip,pmu = <0x06>;
		#address-cells = <0x02>;
		#size-cells = <0x02>;
		ranges;
		pinctrl-names = "default";
		pinctrl-0 = <0x8a>;

		gpio@ff750000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff750000 0x00 0x100>;
			interrupts = <0x00 0x51 0x04>;
			clocks = <0x07 0x140>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "PMU_SLEEP", "DDRIO_PWROFF", "DDRIO_RETEN", "EFUSE_PWR", "GPIO_3V3_PU_0", "PWR_KEY", "RTC_INT", "REV_2", "REV_1", "REV_0", "", "", "", "", "", "I2C0_SDA_PMU", "I2C0_SCL_PMU", "", "";
			phandle = <0x31>;
		};

		gpio@ff780000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff780000 0x00 0x100>;
			interrupts = <0x00 0x52 0x04>;
			clocks = <0x07 0x141>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
		};

		gpio@ff790000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff790000 0x00 0x100>;
			interrupts = <0x00 0x53 0x04>;
			clocks = <0x07 0x142>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "nCODEC_RESET", "AN_POWER_EN", "nMUTE", "", "", "", "", "", "", "", "", "", "", "", "", "", "I2C3_SCL_CAM", "I2C3_SDA_CAM";
			phandle = <0x99>;
		};

		gpio@ff7a0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7a0000 0x00 0x100>;
			interrupts = <0x00 0x54 0x04>;
			clocks = <0x07 0x143>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "FLASH0_D0", "FLASH0_D1", "FLASH0_D2", "FLASH0_D3", "FLASH0_D4", "FLASH0_D5", "FLASH0_D6", "FLASH0_D7", "", "EMMC_PWREN", "", "", "", "", "", "ETHPHY_INT", "EMMC_CMD", "", "EMMC_CLKO", "", "", "", "", "", "ETH_TXD2", "ETH_TXD3", "ETH_RXD2", "ETH_RXD3", "ETH_TXD0", "ETH_TXD1", "ETH_RXD0", "ETH_RXD1";
			phandle = <0x50>;
		};

		gpio@ff7b0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7b0000 0x00 0x100>;
			interrupts = <0x00 0x55 0x04>;
			clocks = <0x07 0x144>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "", "ETH_RXCTL", "", "ETH_CLK125", "ETH_PME", "ETH_MDIO", "ETH_RXC", "", "ETH_PHYRST", "ETH_TXC", "", "", "", "", "", "", "UART0_RX", "UART0_TX", "UART0_CTS", "UART0_RTS", "WIFI_D0", "WIFI_D1", "WIFI_D2", "WIFI_D3", "WIFI_CMD", "WIFI_CLK", "BT_WAKE", "", "WIFI_REG_ON", "BT_RESET", "WIFI_HOST_WAKE", "BT_HOST_WAKE";
			phandle = <0x3f>;
		};

		gpio@ff7c0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7c0000 0x00 0x100>;
			interrupts = <0x00 0x56 0x04>;
			clocks = <0x07 0x145>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "", "", "", "", "", "", "", "", "UART1_RX", "UART1_TX", "UART1_CTS", "UART1_RTS", "", "", "", "", "", "", "", "";
		};

		gpio@ff7d0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7d0000 0x00 0x100>;
			interrupts = <0x00 0x57 0x04>;
			clocks = <0x07 0x146>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "I2S0_SCLK", "I2S0_LRCK_RX", "I2S0_LRCK_TX", "I2S0_SDI", "I2S0_SDO0", "I2S0_SDO1", "I2S0_SDO2", "I2S0_SDO3", "I2S0_CLK", "I2C2_SDA", "I2C2_SCL", "AUDIO_CLK_SEL_1", "", "", "", "", "SDMMC_D0", "SDMMC_D1", "SDMMC_D2", "SDMMC_D3", "SDMMC_CLK", "SDMMC_CMD", "SDMMC_DET";
			phandle = <0x0e>;
		};

		gpio@ff7e0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7e0000 0x00 0x100>;
			interrupts = <0x00 0x58 0x04>;
			clocks = <0x07 0x147>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "MIPI_PWM", "", "nMIPI_POWER_EN", "MIPI_RESET", "", "TOUCH_RESET", "TOUCH_INT", "", "", "GPIO_3V3_PU_1", "GPIO_3V3_PU_2", "SDMMC_PWR", "SDMMC_WP", "", "GPIO_3V3_PD_8", "LED_HEARTBEAT", "", "I2C4_SDA", "I2C4_SCL", "GPIO_3V3_PD_7", "GPIO_3V3_PD_6", "", "UART2_RX", "UART2_TX";
			phandle = <0x16>;
		};

		gpio@ff7f0000 {
			compatible = "rockchip,gpio-bank";
			reg = <0x00 0xff7f0000 0x00 0x100>;
			interrupts = <0x00 0x59 0x04>;
			clocks = <0x07 0x148>;
			gpio-controller;
			#gpio-cells = <0x02>;
			interrupt-controller;
			#interrupt-cells = <0x02>;
			gpio-line-names = "", "", "PMU_VSEL", "", "I2C1_SDA", "I2C1_SCL", "", "", "", "";
		};

		hdmi {

			hdmi-cec-c0 {
				rockchip,pins = <0x07 0x10 0x02 0x8b>;
			};

			hdmi-cec-c7 {
				rockchip,pins = <0x07 0x17 0x04 0x8b>;
			};

			hdmi-ddc {
				rockchip,pins = <0x07 0x13 0x02 0x8b 0x07 0x14 0x02 0x8b>;
			};

			hdmi-ddc-unwedge {
				rockchip,pins = <0x07 0x13 0x00 0x8c 0x07 0x14 0x02 0x8b>;
			};
		};

		pcfg-output-low {
			output-low;
			phandle = <0x8c>;
		};

		pcfg-pull-up {
			bias-pull-up;
			phandle = <0x8d>;
		};

		pcfg-pull-down {
			bias-pull-down;
			phandle = <0x8e>;
		};

		pcfg-pull-none {
			bias-disable;
			phandle = <0x8b>;
		};

		pcfg-pull-none-12ma {
			bias-disable;
			drive-strength = <0x0c>;
			phandle = <0x8f>;
		};

		suspend {

			global-pwroff {
				rockchip,pins = <0x00 0x00 0x01 0x8b>;
			};

			ddrio-pwroff {
				rockchip,pins = <0x00 0x01 0x01 0x8b>;
			};

			ddr0-retention {
				rockchip,pins = <0x00 0x02 0x01 0x8d>;
			};

			ddr1-retention {
				rockchip,pins = <0x00 0x03 0x01 0x8d>;
			};
		};

		edp {

			edp-hpd {
				rockchip,pins = <0x07 0x0b 0x02 0x8e>;
			};
		};

		i2c0 {

			i2c0-xfer {
				rockchip,pins = <0x00 0x0f 0x01 0x8b 0x00 0x10 0x01 0x8b>;
				phandle = <0x54>;
			};
		};

		i2c1 {

			i2c1-xfer {
				rockchip,pins = <0x08 0x04 0x01 0x8b 0x08 0x05 0x01 0x8b>;
				phandle = <0x30>;
			};
		};

		i2c2 {

			i2c2-xfer {
				rockchip,pins = <0x06 0x09 0x01 0x8b 0x06 0x0a 0x01 0x8b>;
				phandle = <0x57>;
			};
		};

		i2c3 {

			i2c3-xfer {
				rockchip,pins = <0x02 0x10 0x01 0x8b 0x02 0x11 0x01 0x8b>;
				phandle = <0x33>;
			};
		};

		i2c4 {

			i2c4-xfer {
				rockchip,pins = <0x07 0x11 0x01 0x8b 0x07 0x12 0x01 0x8b>;
				phandle = <0x34>;
			};
		};

		i2c5 {

			i2c5-xfer {
				rockchip,pins = <0x07 0x13 0x01 0x8b 0x07 0x14 0x01 0x8b>;
				phandle = <0x37>;
			};
		};

		i2s0 {

			i2s0-bus {
				rockchip,pins = <0x06 0x00 0x01 0x8b 0x06 0x01 0x01 0x8b 0x06 0x02 0x01 0x8b 0x06 0x03 0x01 0x8b 0x06 0x04 0x01 0x8b 0x06 0x05 0x01 0x8b 0x06 0x06 0x01 0x8b 0x06 0x07 0x01 0x8b>;
				phandle = <0x70>;
			};
		};

		lcdc {

			lcdc-ctl {
				rockchip,pins = <0x01 0x18 0x01 0x8b 0x01 0x19 0x01 0x8b 0x01 0x1a 0x01 0x8b 0x01 0x1b 0x01 0x8b>;
				phandle = <0x80>;
			};
		};

		sdmmc {

			sdmmc-clk {
				rockchip,pins = <0x06 0x14 0x01 0x8b>;
				phandle = <0x0f>;
			};

			sdmmc-cmd {
				rockchip,pins = <0x06 0x15 0x01 0x8d>;
				phandle = <0x10>;
			};

			sdmmc-cd {
				rockchip,pins = <0x06 0x16 0x01 0x8d>;
			};

			sdmmc-bus1 {
				rockchip,pins = <0x06 0x10 0x01 0x8d>;
			};

			sdmmc-bus4 {
				rockchip,pins = <0x06 0x10 0x01 0x8d 0x06 0x11 0x01 0x8d 0x06 0x12 0x01 0x8d 0x06 0x13 0x01 0x8d>;
				phandle = <0x11>;
			};

			sdmmc-cd-gpio {
				rockchip,pins = <0x06 0x16 0x00 0x8d>;
				phandle = <0x12>;
			};

			sdmmc-pwr {
				rockchip,pins = <0x07 0x0b 0x00 0x8b>;
				phandle = <0x98>;
			};

			sdmmc-wp-gpio {
				rockchip,pins = <0x07 0x0c 0x00 0x8d>;
				phandle = <0x13>;
			};
		};

		sdio0 {

			sdio0-bus1 {
				rockchip,pins = <0x04 0x14 0x01 0x8d>;
			};

			sdio0-bus4 {
				rockchip,pins = <0x04 0x14 0x01 0x8d 0x04 0x15 0x01 0x8d 0x04 0x16 0x01 0x8d 0x04 0x17 0x01 0x8d>;
				phandle = <0x18>;
			};

			sdio0-cmd {
				rockchip,pins = <0x04 0x18 0x01 0x8d>;
				phandle = <0x19>;
			};

			sdio0-clk {
				rockchip,pins = <0x04 0x19 0x01 0x8b>;
				phandle = <0x1a>;
			};

			sdio0-cd {
				rockchip,pins = <0x04 0x1a 0x01 0x8d>;
			};

			sdio0-wp {
				rockchip,pins = <0x04 0x1b 0x01 0x8d>;
			};

			sdio0-pwr {
				rockchip,pins = <0x04 0x1c 0x01 0x8d>;
			};

			sdio0-bkpwr {
				rockchip,pins = <0x04 0x1d 0x01 0x8d>;
			};

			sdio0-int {
				rockchip,pins = <0x04 0x1e 0x01 0x8d>;
				phandle = <0x1b>;
			};
		};

		sdio1 {

			sdio1-bus1 {
				rockchip,pins = <0x03 0x18 0x04 0x8d>;
			};

			sdio1-bus4 {
				rockchip,pins = <0x03 0x18 0x04 0x8d 0x03 0x19 0x04 0x8d 0x03 0x1a 0x04 0x8d 0x03 0x1b 0x04 0x8d>;
			};

			sdio1-cd {
				rockchip,pins = <0x03 0x1c 0x04 0x8d>;
			};

			sdio1-wp {
				rockchip,pins = <0x03 0x1d 0x04 0x8d>;
			};

			sdio1-bkpwr {
				rockchip,pins = <0x03 0x1e 0x04 0x8d>;
			};

			sdio1-int {
				rockchip,pins = <0x03 0x1f 0x04 0x8d>;
			};

			sdio1-cmd {
				rockchip,pins = <0x04 0x06 0x04 0x8d>;
			};

			sdio1-clk {
				rockchip,pins = <0x04 0x07 0x04 0x8b>;
			};

			sdio1-pwr {
				rockchip,pins = <0x04 0x09 0x04 0x8d>;
			};
		};

		emmc {

			emmc-clk {
				rockchip,pins = <0x03 0x12 0x02 0x8b>;
				phandle = <0x1f>;
			};

			emmc-cmd {
				rockchip,pins = <0x03 0x10 0x02 0x8d>;
				phandle = <0x20>;
			};

			emmc-pwr {
				rockchip,pins = <0x03 0x09 0x02 0x8d>;
			};

			emmc-bus1 {
				rockchip,pins = <0x03 0x00 0x02 0x8d>;
			};

			emmc-bus4 {
				rockchip,pins = <0x03 0x00 0x02 0x8d 0x03 0x01 0x02 0x8d 0x03 0x02 0x02 0x8d 0x03 0x03 0x02 0x8d>;
			};

			emmc-bus8 {
				rockchip,pins = <0x03 0x00 0x02 0x8d 0x03 0x01 0x02 0x8d 0x03 0x02 0x02 0x8d 0x03 0x03 0x02 0x8d 0x03 0x04 0x02 0x8d 0x03 0x05 0x02 0x8d 0x03 0x06 0x02 0x8d 0x03 0x07 0x02 0x8d>;
				phandle = <0x21>;
			};

			emmc-reset {
				rockchip,pins = <0x03 0x09 0x00 0x8b>;
				phandle = <0x92>;
			};
		};

		spi0 {

			spi0-clk {
				rockchip,pins = <0x05 0x0c 0x01 0x8d>;
				phandle = <0x24>;
			};

			spi0-cs0 {
				rockchip,pins = <0x05 0x0d 0x01 0x8d>;
				phandle = <0x27>;
			};

			spi0-tx {
				rockchip,pins = <0x05 0x0e 0x01 0x8d>;
				phandle = <0x25>;
			};

			spi0-rx {
				rockchip,pins = <0x05 0x0f 0x01 0x8d>;
				phandle = <0x26>;
			};

			spi0-cs1 {
				rockchip,pins = <0x05 0x10 0x01 0x8d>;
			};
		};

		spi1 {

			spi1-clk {
				rockchip,pins = <0x07 0x0c 0x02 0x8d>;
				phandle = <0x28>;
			};

			spi1-cs0 {
				rockchip,pins = <0x07 0x0d 0x02 0x8d>;
				phandle = <0x2b>;
			};

			spi1-rx {
				rockchip,pins = <0x07 0x0e 0x02 0x8d>;
				phandle = <0x2a>;
			};

			spi1-tx {
				rockchip,pins = <0x07 0x0f 0x02 0x8d>;
				phandle = <0x29>;
			};
		};

		spi2 {

			spi2-cs1 {
				rockchip,pins = <0x08 0x03 0x01 0x8d>;
			};

			spi2-clk {
				rockchip,pins = <0x08 0x06 0x01 0x8d>;
				phandle = <0x2c>;
			};

			spi2-cs0 {
				rockchip,pins = <0x08 0x07 0x01 0x8d>;
				phandle = <0x2f>;
			};

			spi2-rx {
				rockchip,pins = <0x08 0x08 0x01 0x8d>;
				phandle = <0x2e>;
			};

			spi2-tx {
				rockchip,pins = <0x08 0x09 0x01 0x8d>;
				phandle = <0x2d>;
			};
		};

		uart0 {

			uart0-xfer {
				rockchip,pins = <0x04 0x10 0x01 0x8d 0x04 0x11 0x01 0x8b>;
				phandle = <0x38>;
			};

			uart0-cts {
				rockchip,pins = <0x04 0x12 0x01 0x8d>;
				phandle = <0x39>;
			};

			uart0-rts {
				rockchip,pins = <0x04 0x13 0x01 0x8b>;
				phandle = <0x3a>;
			};
		};

		uart1 {

			uart1-xfer {
				rockchip,pins = <0x05 0x08 0x01 0x8d 0x05 0x09 0x01 0x8b>;
				phandle = <0x40>;
			};

			uart1-cts {
				rockchip,pins = <0x05 0x0a 0x01 0x8d>;
				phandle = <0x41>;
			};

			uart1-rts {
				rockchip,pins = <0x05 0x0b 0x01 0x8b>;
				phandle = <0x42>;
			};
		};

		uart2 {

			uart2-xfer {
				rockchip,pins = <0x07 0x16 0x01 0x8d 0x07 0x17 0x01 0x8b>;
				phandle = <0x43>;
			};
		};

		uart3 {

			uart3-xfer {
				rockchip,pins = <0x07 0x07 0x01 0x8d 0x07 0x08 0x01 0x8b>;
				phandle = <0x44>;
			};

			uart3-cts {
				rockchip,pins = <0x07 0x09 0x01 0x8d>;
			};

			uart3-rts {
				rockchip,pins = <0x07 0x0a 0x01 0x8b>;
			};
		};

		uart4 {

			uart4-xfer {
				rockchip,pins = <0x05 0x0f 0x03 0x8d 0x05 0x0e 0x03 0x8b>;
				phandle = <0x45>;
			};

			uart4-cts {
				rockchip,pins = <0x05 0x0c 0x03 0x8d>;
			};

			uart4-rts {
				rockchip,pins = <0x05 0x0d 0x03 0x8b>;
			};
		};

		tsadc {

			otp-pin {
				rockchip,pins = <0x00 0x0a 0x00 0x8b>;
				phandle = <0x47>;
			};

			otp-out {
				rockchip,pins = <0x00 0x0a 0x01 0x8b>;
				phandle = <0x48>;
			};
		};

		pwm0 {

			pwm0-pin {
				rockchip,pins = <0x07 0x00 0x01 0x8b>;
				phandle = <0x5c>;
			};
		};

		pwm1 {

			pwm1-pin {
				rockchip,pins = <0x07 0x01 0x01 0x8b>;
				phandle = <0x5d>;
			};
		};

		pwm2 {

			pwm2-pin {
				rockchip,pins = <0x07 0x16 0x03 0x8b>;
				phandle = <0x5e>;
			};
		};

		pwm3 {

			pwm3-pin {
				rockchip,pins = <0x07 0x17 0x03 0x8b>;
				phandle = <0x5f>;
			};
		};

		gmac {

			rgmii-pins {
				rockchip,pins = <0x03 0x1e 0x03 0x8b 0x03 0x1f 0x03 0x8b 0x03 0x1a 0x03 0x8b 0x03 0x1b 0x03 0x8b 0x03 0x1c 0x03 0x8f 0x03 0x1d 0x03 0x8f 0x03 0x18 0x03 0x8f 0x03 0x19 0x03 0x8f 0x04 0x00 0x03 0x8b 0x04 0x05 0x03 0x8b 0x04 0x06 0x03 0x8b 0x04 0x09 0x03 0x8f 0x04 0x04 0x03 0x8f 0x04 0x01 0x03 0x8b 0x04 0x03 0x03 0x8b>;
				phandle = <0x4d>;
			};

			rmii-pins {
				rockchip,pins = <0x03 0x1e 0x03 0x8b 0x03 0x1f 0x03 0x8b 0x03 0x1c 0x03 0x8b 0x03 0x1d 0x03 0x8b 0x04 0x00 0x03 0x8b 0x04 0x05 0x03 0x8b 0x04 0x04 0x03 0x8b 0x04 0x01 0x03 0x8b 0x04 0x02 0x03 0x8b 0x04 0x03 0x03 0x8b>;
			};

			phy-int {
				rockchip,pins = <0x03 0x0f 0x00 0x8d>;
				phandle = <0x4e>;
			};

			phy-rst {
				rockchip,pins = <0x04 0x08 0x00 0x90>;
				phandle = <0x4f>;
			};
		};

		spdif {

			spdif-tx {
				rockchip,pins = <0x06 0x0b 0x01 0x8b>;
				phandle = <0x6f>;
			};
		};

		pcfg-output-high {
			output-high;
			phandle = <0x90>;
		};

		amp {

			amp-fault {
				rockchip,pins = <0x07 0x14 0x00 0x8b>;
				phandle = <0x5a>;
			};

			amp-pdn {
				rockchip,pins = <0x07 0x13 0x00 0x8f>;
				phandle = <0x5b>;
			};
		};

		az05 {

			az05-pcb-rev {
				rockchip,pins = <0x00 0x04 0x00 0x8d 0x00 0x07 0x00 0x8d 0x00 0x08 0x00 0x8d 0x00 0x09 0x00 0x8d>;
				phandle = <0x8a>;
			};
		};

		bluetooth {

			bt-wake {
				rockchip,pins = <0x04 0x1a 0x00 0x8b>;
				phandle = <0x3d>;
			};

			bt-reg-on {
				rockchip,pins = <0x04 0x1d 0x00 0x8b>;
				phandle = <0x3c>;
			};

			bt-host-wake {
				rockchip,pins = <0x04 0x1f 0x00 0x8d>;
				phandle = <0x3e>;
			};
		};

		buttons {

			pwr-key-l {
				rockchip,pins = <0x00 0x05 0x00 0x8d>;
				phandle = <0x93>;
			};
		};

		leds {

			led1 {
				rockchip,pins = <0x07 0x0f 0x00 0x8b>;
				phandle = <0x94>;
			};
		};

		mipi-panel {

			mipi-reset {
				rockchip,pins = <0x07 0x03 0x00 0x8b>;
				phandle = <0x7c>;
			};

			bl-en {
				rockchip,pins = <0x07 0x02 0x00 0x8b>;
				phandle = <0x97>;
			};
		};

		rtc {

			rtc-int {
				rockchip,pins = <0x00 0x06 0x00 0x8d>;
				phandle = <0x32>;
			};
		};

		sdio {

			wifi-enable {
				rockchip,pins = <0x04 0x1c 0x00 0x8b>;
				phandle = <0x96>;
			};
		};

		sound-codec {

			an-power-en {
				rockchip,pins = <0x02 0x01 0x00 0x8b>;
				phandle = <0x9a>;
			};

			codec-clksel {
				rockchip,pins = <0x06 0x08 0x00 0x8b 0x06 0x0b 0x00 0x8b>;
			};

			codec-reset {
				rockchip,pins = <0x02 0x00 0x00 0x8b>;
				phandle = <0x9c>;
			};

			mute {
				rockchip,pins = <0x02 0x02 0x00 0x8b>;
				phandle = <0xa1>;
			};
		};

		touch {

			touch-int {
				rockchip,pins = <0x07 0x06 0x00 0x8d>;
				phandle = <0x35>;
			};

			touch-rst {
				rockchip,pins = <0x07 0x05 0x00 0x8b>;
				phandle = <0x36>;
			};
		};
	};

	chosen {
		stdout-path = "serial2:115200n8";
	};

	memory@0 {
		reg = <0x00 0x00 0x00 0x80000000>;
		device_type = "memory";
	};

	ac-power {
		compatible = "inmusic,az01-power";
		io-channels = <0x91 0x02>;
		inmusic,power-always-on;
	};

	emmc-pwrseq {
		compatible = "mmc-pwrseq-emmc";
		pinctrl-0 = <0x92>;
		pinctrl-names = "default";
		reset-gpios = <0x50 0x09 0x01>;
		phandle = <0x1e>;
	};

	external-gmac-clock {
		compatible = "fixed-clock";
		#clock-cells = <0x00>;
		clock-frequency = <0x7735940>;
		clock-output-names = "ext_gmac";
		phandle = <0x4a>;
	};

	gpio-keys {
		compatible = "gpio-keys";
		#address-cells = <0x01>;
		#size-cells = <0x00>;
		pinctrl-names = "default";
		pinctrl-0 = <0x93>;

		power {
			label = "Power";
			gpios = <0x31 0x05 0x01>;
			linux,code = <0x74>;
			debounce-interval = <0x14>;
			wakeup-source;
		};
	};

	leds {
		compatible = "gpio-leds";
		pinctrl-names = "default";
		pinctrl-0 = <0x94>;

		led1 {
			gpios = <0x16 0x0f 0x00>;
			label = "az01:red:usr1";
			linux,default-trigger = "heartbeat";
			retain-state-shutdown;
		};
	};

	mipi-backlight {
		compatible = "pwm-backlight";
		brightness-levels = <0x00 0xff>;
		num-interpolated-steps = <0xff>;
		default-brightness-level = <0x95>;
		power-supply = <0x55>;
		pwms = <0x95 0x00 0xc350 0x00>;
		phandle = <0x7e>;
	};

	sdio-pwrseq {
		compatible = "mmc-pwrseq-simple";
		clocks = <0x3b>;
		clock-names = "ext_clock";
		pinctrl-names = "default";
		pinctrl-0 = <0x96>;
		pre-power-on-delay-ms = <0x0a>;
		reset-gpios = <0x3f 0x1c 0x01>;
		phandle = <0x17>;
	};

	vcc-3v3 {
		compatible = "regulator-fixed";
		regulator-name = "3.3V";
		regulator-min-microvolt = <0x325aa0>;
		regulator-max-microvolt = <0x325aa0>;
		vin-supply = <0x55>;
		phandle = <0x59>;
	};

	flash-regulator {
		compatible = "regulator-fixed";
		regulator-name = "vcc_flash";
		regulator-min-microvolt = <0x1b7740>;
		regulator-max-microvolt = <0x1b7740>;
		startup-delay-us = <0x96>;
		vin-supply = <0x55>;
		phandle = <0x22>;
	};

	vcc-mipi {
		compatible = "regulator-fixed";
		gpio = <0x16 0x02 0x01>;
		startup-delay-us = <0x2710>;
		pinctrl-names = "default";
		pinctrl-0 = <0x97>;
		regulator-boot-on;
		regulator-min-microvolt = <0x325aa0>;
		regulator-max-microvolt = <0x325aa0>;
		regulator-name = "vcc_mipi";
		vin-supply = <0x59>;
		phandle = <0x7d>;
	};

	sdmmc-regulator {
		compatible = "regulator-fixed";
		gpio = <0x16 0x0b 0x01>;
		pinctrl-names = "default";
		pinctrl-0 = <0x98>;
		off-on-delay-us = <0x7a120>;
		regulator-name = "vcc_sd";
		regulator-min-microvolt = <0x325aa0>;
		regulator-max-microvolt = <0x325aa0>;
		vin-supply = <0x59>;
		status = "okay";
		phandle = <0x14>;
	};

	vsys-regulator {
		compatible = "regulator-fixed";
		regulator-name = "vcc_sys";
		regulator-min-microvolt = <0x4c4b40>;
		regulator-max-microvolt = <0x4c4b40>;
		regulator-always-on;
		regulator-boot-on;
		phandle = <0x55>;
	};

	analog-audio-supply {
		compatible = "regulator-fixed";
		regulator-name = "vdd_3.3va";
		regulator-min-microvolt = <0x325aa0>;
		regulator-max-microvolt = <0x325aa0>;
		enable-active-high;
		gpio = <0x99 0x01 0x00>;
		off-on-delay-us = <0x2710>;
		pinctrl-0 = <0x9a>;
		pinctrl-names = "default";
		startup-delay-us = <0x3e8>;
		vin-supply = <0x55>;
		phandle = <0xa2>;
	};

	clock-generator {
		compatible = "ti,lv4040a";
		#clock-cells = <0x01>;
		clocks = <0x9b>;
		clr-gpios = <0x99 0x00 0x01>;
		pinctrl-0 = <0x9c>;
		pinctrl-names = "default";
		phandle = <0x58>;
	};

	mclk {
		compatible = "fixed-clock";
		#clock-cells = <0x00>;
		clock-frequency = <0x1588800>;
		clock-output-names = "mclk";
		phandle = <0x9b>;
	};

	sd-mux {
		compatible = "virtual-mux";
		default-state = "basic";

		state-basic {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					status = "okay";
				};
			};
		};

		state-hs {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					cap-sd-highspeed;
					status = "okay";
				};
			};
		};

		state-sdr12 {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					sd-uhs-sdr12;
					status = "okay";
				};
			};
		};

		state-sdr25 {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					sd-uhs-sdr12;
					sd-uhs-sdr25;
					status = "okay";
				};
			};
		};

		state-sdr50 {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					sd-uhs-sdr12;
					sd-uhs-sdr25;
					sd-uhs-sdr50;
					status = "okay";
				};
			};
		};

		state-sdr104 {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					sd-uhs-sdr12;
					sd-uhs-sdr25;
					sd-uhs-sdr50;
					sd-uhs-sdr104;
					status = "okay";
				};
			};
		};

		state-ddr50 {

			fragment@0 {
				target = <0x9d>;

				__overlay__ {
					sd-uhs-sdr12;
					sd-uhs-sdr25;
					sd-uhs-sdr50;
					sd-uhs-ddr50;
					status = "okay";
				};
			};
		};
	};

	sound {
		compatible = "inmusic,nh08-audio";
		inmusic,cpu = <0x9e>;
		inmusic,master-codec = <0x9f>;
		inmusic,speaker-codec = <0xa0>;
	};

	sound-codec {
		compatible = "inmusic,nh08-audio-codec";
		#sound-dai-cells = <0x00>;
		clocks = <0x9b 0x58 0x02 0x58 0x08>;
		clock-names = "mclk", "bclk", "lrclk";
		mute-gpios = <0x99 0x02 0x00>;
		pinctrl-0 = <0xa1>;
		pinctrl-names = "default";
		vdd-supply = <0xa2>;
		cvdd-supply = <0xa2>;
		phandle = <0x9f>;
	};
};
```
