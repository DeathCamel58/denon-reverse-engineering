# Linux DTSs

Linux (the OS running on the device)
uses [device trees](https://www.kernel.org/doc/html/latest/devicetree/usage-model.html) to specify the hardware layout
of the device currently running.

The DTBs, which are DTSs that are pre-compiled can be found at `/boot`. These can be converted back into DTSs by running
`dtc -I dtb -O dts [file].dtb[o] -o [file].dts`.

You can view the DTS's that I've found in the sidebar. These will not be useful for end users, probably will only be
useful if you're working on establishing a working [Buildroot](https://www.buildroot.org) environment.
