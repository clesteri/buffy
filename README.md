# Buffy

Buffy is a debugging buffer library for embedded targets that consists of two
parts: 1) a small library that you link into your embedded code, 2) a python
script that uses OpenOCD to exchange data with the code on the embedded side.

## Usage

Prerequisites: you should be able to debug your target with OpenOCD, and
`tcl_port` should be enabled (it is by default). The client should work
with either Python 2 or 3.

On the embedded side: add `embedded/buffy.c` and `embedded/buffy.h` to your
project using your favorite build system. Somewhere in your code include
`buffy.h` and instantiate the debug buffer with `INSTANTIATE_BUFFY(buffy);`.
Then_set up data to be sent to host with `buffy_tx(&buffy, buf, len)`. You
might want to create some sort of `printf` function that `sprintf`s into
a buffer before calling `buffy_tx`.

On the host: from `client` directory run `./buffy.py --ram_start <RAM start
address>`. E.g., if you had an STM32F0 family microcontroller, you might
run `./buffy.py --ram_start 0x20000000`. It should find the Buffy datastructure
and automatically stream data from it when the target writes to it.

## More Details

When you instantiate Buffy on the target, it creates two circular buffers, one
for sending data from target to host, and one for reverse. The data structure
also contains a magic word that the client can use to find the structure in
memory.

Buffy uses OpenOCD's "RPC" interface to get data between the client and the
embedded target. It could use some improvements.

## Supported Devices

This has been tested on couple different Cortex-M0 devices (e.g., STM32F0). It
should work on other platforms with minimal changes as long as your target has
32-bit wide debug access, and allows OpenOCD to read/write memory while the
target is running.

## Contributing

Pull requests are welcome. Also, I would love if somebody cleaned up OpenOCDs
RPC interface so it was less spammy and more reliable.

## License

BSD. See LICENSE.

## Acknowledgements

Inspired by nekromant's stlinky and lk's cbuf.
