# Opus Codec for Asterisk 

*Digium, currently do not provide an ARM binary version of opus codec for Astersik.* 

Since Asterisk 13.12 (and Asterisk 14.0.1), Opus is not only supported for pass-through but can be transcoded as well. This allows you to translate to/from other audio codecs like those for landline telephones (ISDN: G.711; DECT: G.726-32; and HD: G.722) or mobile phones (GSM, AMR, AMR-WB, 3GPP EVS). This can be achieved by enabling `codec_opus` via `make menuselect`.

This repository is for Asterisk 13+ when building from source for ARM based CPU. 

## Prerequisites
At least Asterisk 13.7 is required. 

## Install libraries:
To support transcoding, you’ll need to install an Opus library, for example in Debian/Ubuntu:

	$ sudo apt-get install libopus-dev libopusfile-dev

## Update Asterisk:

	$ git clone https://github.com/InnovateAsterisk/asterisk-opus.git
	$ cp ../asterisk-opus/include/asterisk/* include/asterisk
	$ cp ../asterisk-opus/codecs/* codecs
	$ cp ../asterisk-opus/res/* res

## Codec Configuration
You cannot use `codecs.conf` to change the configuration of the audio when transcoding. Remember that while not transcoding or when your client device is encoding the audio (like in Chrome/Firefox etc) Asterisk is not encoding the audio so codec config options are not applicable. However if you want to have transcoding *TO* opus, and you want to change the way Asterisk encodes the audio you have to change the file `include/asterisk/opus.h` and re-make Asterisk. The binary module from Digium supports the configuration file codecs.conf.

## Optional
(Optionally) apply the patch for File Formats (untested):

Two format modules are added which allow you to play VP8 and Ogg Opus files without transcoding.

	cp --verbose ./asterisk-opus*/formats/* ./formats
	patch -p1 <./asterisk-opus*/asterisk.patch

(Optionally) apply the patch for Native PLC (experimental):

Out of the box, Asterisk does not detect lost (or late) RTP packets. Such a detection is required to conceal lost packets (PLC). PLC improves situations like Wi-Fi Roaming or mobile-phone handovers. This patch detects lost/late packets but is experimental. If your scenario requires PLC and you find an issue with this patch, please, continue with [ASTERISK-25629…](http://issues.asterisk.org/jira/browse/ASTERISK-25629)

	patch -p1 <./asterisk-opus*/enable_native_plc.patch

Run the bootstrap script to re-generate configure:

	./bootstrap.sh

Configure your patched Asterisk:

	./configure

Enable slin16 in menuselect for transcoding, for example via:

	make menuselect.makeopts
	./menuselect/menuselect --enable-category MENUSELECT_CORE_SOUNDS

Compile and install:

	make
	sudo make install

Alternatively, you can use the Makefile of this repository to create just the shared libraries of the modules. That way, you do not have to (re-) make your whole Asterisk. 


## Thanks go to
* [Opus team](http://www.opus-codec.org/contact/),
* Ron Lee packaging the libraries for Debian/Ubuntu,
* [Lorenzo Miniero](https://github.com/meetecho/asterisk-opus) created the original code for Asterisk 11,
* [Tzafrir Cohen](http://issues.asterisk.org/jira/browse/ASTERISK-21981) drove the pass-through support for Asterisk 13,
* [Sean Bright](https://github.com/seanbright/asterisk-opus) ported the transcoding code over to Asterisk 13, added many changes directly into Asterisk 13, and maintained the port until Asterisk 13.11.