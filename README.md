# tsc2uinput
User mode driver for Raspberry Pi's Tontek/MZTX-PI-EXT  320x240 LCD with TSC2003 resistive touchscreen controller

This short (kind of) experimental driver reads XY pos and touch state
though the I2C bus (on the connector P1 of Raspberries) and sends events
to /dev/uinput.  It is to be used with the Tontek / MZTX-PI-EXT LCD Touchscreen.

/etc/rc.local launches 
<pre>
/usr/local/bin/mztx06a & # touchscreen Tontec
</pre>
which sends screen buffer diffs through the SPI bus (which is slow and computer intensive)

Relevant documents :
* http://elinux.org/MZTX-PI-EXT
* http://www.ti.com/lit/ds/symlink/tsc2003.pdf (datasheet)
* https://github.com/derekhe/wavesahre-7inch-touchscreen-driver (/dev/uinput example)

usage : 'sudo ./tsc2uinput

Notes :
* The calibration is hardwired... (and there is debug output)
* Touchpad emulation with relative events should probably be more appropriate

Setup in /boot/config.txt (mztx06a is in 'landscape' mode)
<pre>
framebuffer_width=320
framebuffer_height=240
</pre>
----------------
There now exist affordable HDMI LCD screens.
-------------------------------------------------------------------------------------------------------------------------
+++++TROUBLESHOOTING++++++++:

1)Problem with i2c_smbus_read_word_data
When you do:
<pre>
$make
</pre>

and obtain the following result:
<pre>
gcc -o tsc2uinput tsc2uinput.c # -lbcm2835
/tmp/ccGJ6B5G.o: In function `i2c_rw':
tsc2uinput.c:(.text+0x3c): undefined reference to `i2c_smbus_read_word_data'
collect2: ld returned 1 exit status
Makefile:2: recipe for target 'tsc2uinput' failed
make: *** [tsc2uinput] Error 1
</pre>

you need to do:
<pre>
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install libi2c-dev
</pre>

And finally do (again):
<pre>
$make
</pre>

2) Problem whit /dev/i2c
Now when I do:
<pre>
sudo ./tsc2uinput
</pre>

I get:
<pre>
/dev/i2c-1: No such file or directory
</pre>

You need to comment out the blacklist in:
<pre>
sudo nano /etc/modprobe.d/raspi-blacklist.conf
</pre>

Finally do:
<pre>
sudo modprobe i2c-dev
</pre>

Now you could do:
<pre>
sudo ./tsc2uinput
</pre>

But my 'Touchscreen display Tontec 201 lates verison 2.8"  tftlcd240x320 rgp pixels touchscreen dsplymntr', just displays:
<pre>
4097  4097
</pre>
Anywhere I press the touchscreen display.
