# X-Light BLE LED light bulb

The X-Light by [We-Smart](http://www.we-smart.cn/) is an LED light bulb controlled over Bluetooth Low Energy. 
The control application is available for [Android](https://play.google.com/store/apps/details?id=com.ws.up) and iOS, but
the control protocol is undocumented. 

## Commands

Data written to the `0x0012` handle has a trailing two-byte message counter `count`, presumably so the X-Light can throw
out old packets if they arrive out of order. Sending `00:00` in every message works.

### Light on/off
    
    count = 00:00
    to turn on:   value = 00
    to turn off:  value = 01
    
    write 0x0012: [count]:00:06:00:02:00:[value]:00:00:00:00:00:00:00:00:00:00
    
### Set RGB
    
    r = red byte
    g = green byte
    b = blue byte
    fade = 01 to fade to colour, 00 change immediately to colour
      
    write 0x0012: 00:08:00:06:00:0a:03:00:01:[fade]:00:[r]:[g]:[b]:00:00:00:00

### Set warmth/brightness (natural lighting)

    w = warmth byte
    b = brightness byte
    fade = 01 to fade to colour, 00 change immediately to colour

    write 0x0012: [count]:00:06:00:0a:03:00:01:[fade]:00:00:00:00:[w]:[b]:00:00
    
### Set advertising name

    name = name string
    len = length of chars in name
    
    write 0x0012: [count]:00:06:00:07:05:[len]:[name]:[pad 00 so total packet is 18 bytes]
    
For example:
  
    Set name to "bulbasaur"
    write 0x0012: 01:2a:00:06:00:0b:05:[09]:[62:75:6c:62:61:73:61:75:72]:[00]
 
### Sequences

The X-Light phone app allows setting fading/flashing sequences of colours (e.g. heartbeat). 
This sometimes uses writes to handles 0x0012, 0x0015, 0x0018, 0x001b, and reads to handles 0x001e, 0x0021, 0x0024.  
I haven't investigated the details, but for reference here are some sniffed message exchanges:

Purple Breathing:

    write 0x0015: ff:a6:f7:00:00:3a:32:ff:73:e1:00:00:3a:32:ff:a6:ec:00
    write 0x0018: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:61:00:06:00:1f:03:80:04:0b:32:ff:73:f2:00:00:3a:32
    
Multi-colour Breathing:

    write 0x0015: 00:00:00:00:00:03:01:01:ff:00:00:00:03:07:00:00:00:00
    write 0x0018: 00:03:01:00:02:ff:00:00:03:07:00:00:00:00:00:03:01:ff
    write 0x001b: 00:ff:00:00:03:07:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:63:00:06:00:3b:03:80:08:03:01:ff:00:00:00:00:03:07
    
Blue pulse: 

    write 0x0015: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:72:00:06:00:11:03:80:02:07:00:01:00:ff:00:00:07:05

Green pulse: 

    write 0x0015: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:71:00:06:00:11:03:80:02:07:00:01:ff:00:00:00:07:05

Red pulse:

    write 0x0015: 01:00:ff:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:6e:00:06:00:11:03:80:02:00:0c:ff:00:00:00:00:00:0c

### After connection

After connection, the app writes the phone's Bluetooth name over two handles, e.g. for "GT-9100":

    write 0x0015: [2d:49:39:31:30:30]:00:00:00:00:00:00:00:00:00:00:00:00 ["-9100"]
    write 0x0012: 00:05:00:00:00:12:00:00:00:01:4c:73:36:d0:bb:08:[47:54] ["GT"]
            
Every so often, the app performs some reads:
            
    read 0x001e: 34:9a:84:23:1e:65:ac:ec:ad:b5:4a:a0:b2:4e:28:cc:ad:ea
    
I don't know what this means.