# X-Light BLE LED light bulb

The X-Light by [We-Smart](http://www.we-smart.cn/) is an LED light bulb controlled over Bluetooth Low Energy. 
The control application is available for [Android](https://play.google.com/store/apps/details?id=com.ws.up) and iOS, but
the control protocol is undocumented. 

## Commands

Data written to the `0x0012` handle has a leading two-byte message counter (let's call it `count`), presumably so the 
X-Light can throw out old packets if they arrive out of order. Putting an arbitrary value in seems to work. I've been 
using `00:00` as `count` in every message with success.

### Light on/off
    
    count = 00:00 (2 bytes)
    value = 00 to turn on, 01 to turn off (1 byte)
    
    write 0x0012: [count]:00:06:00:02:00:[value]:00:00:00:00:00:00:00:00:00:00
    
### Set RGB
    
    count = 00:00 (2 bytes)
    r = red (1 byte)
    g = green (1 byte)
    b = blue (1 byte)
    fade = 01 to fade to colour, 00 change immediately to colour (1 byte)
      
    write 0x0012: [count]:00:06:00:0a:03:00:01:[fade]:00:[r]:[g]:[b]:00:00:00:00
    
### Set warmth/brightness (natural lighting)

    count = 00:00 (2 bytes)
    w = warmth (1 byte)
    l = lightness (1 byte)
    fade = 01 to fade to colour, 00 change immediately to colour (1 byte)

    write 0x0012: [count]:00:06:00:0a:03:00:01:[fade]:00:00:00:00:[w]:[l]:00:00
    
### Set advertising name

    count = 00:00 (2 bytes)
    u0 = unknown - has appeared as both 07 and 0b (1 byte)    
    len = length of chars in name (1 byte)
    name = name string (len bytes)
    pad = pad 00 so total packet length is 18 bytes
    
    write 0x0012: [count]:00:06:00:[u0]:05:[len]:[name]:[pad]
    
For example, a sniffed message to set the X-Light's advertising name to "bulbasaur":
      
    write 0x0012: [01:2a]:00:06:00:[0b]:05:[09]:[62:75:6c:62:61:73:61:75:72]:[00]
 
### Sequences

The phone app allows setting the X-Light to fade/flash sequences of colours (e.g. heartbeat). 
This uses writes to handles 0x0012, 0x0015, 0x0018, 0x001b. Here are some sniffed messages for reference:

Purple fading:

    write 0x0015: ff:a6:f7:00:00:3a:32:ff:73:e1:00:00:3a:32:ff:a6:ec:00
    write 0x0018: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:61:00:06:00:1f:03:80:04:0b:32:ff:73:f2:00:00:3a:32
    
Multi-colour fading (or potentially flashing):

    write 0x0015: 00:00:00:00:00:03:01:01:ff:00:00:00:03:07:00:00:00:00
    write 0x0018: 00:03:01:00:02:ff:00:00:03:07:00:00:00:00:00:03:01:ff
    write 0x001b: 00:ff:00:00:03:07:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:63:00:06:00:3b:03:80:08:03:01:ff:00:00:00:00:03:07
    
Red/blue siren:

    write 0x0015: 01:00:ff:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:6e:00:06:00:11:03:80:02:00:0c:ff:00:00:00:00:00:0c
    
Blue pulse: 

    write 0x0015: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:72:00:06:00:11:03:80:02:07:00:01:00:ff:00:00:07:05

Green pulse: 

    write 0x0015: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:71:00:06:00:11:03:80:02:07:00:01:ff:00:00:00:07:05

Red pulse:

    write 0x0015: 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
    write 0x0012: 00:6f:00:06:00:11:03:80:02:07:00:ff:00:00:00:00:07:05                  
    
It would appear that the sequences are set up as:
    
    count = 00:00 (2 bytes)
    n = number of colours in sequence (data maximum 9, although I've only observed a maximum of 8) (1 byte)
    tX = duration of colour X in sequence (2 byte)
    rX = red component of colour X (1 byte)
    gX = red component of colour X (1 byte)
    bX = red component of colour X (1 byte)
    wX = warmth component of colour X (1 byte)
    lX = brightness component of colour X (1 byte)
    u0 = unknown (11, 1f, or 3b) (1 byte)

    First set extra colour data:
    write 0x0015: [r1]:[g1]:[b1]:[w1]:[l1]:[t2]:[r2]:[g2]:[b2]:[w2]:[l2]:[t3]:[r3]:[g3]:[b3]:[w3]
    write 0x0018: [l3]:[t4]:[r4]:[g4]:[b4]:[w4]:[l4]:[t5]:[r5]:[g5]:[b5]:[w5]:[l5]:[t6]:[r6]
    write 0x001b: [g6]:[b6]:[w6]:[l6]:[t7]:[r7]:[g7]:[b7]:[w7]:[l7]:[t8]:[r8]:[g8]:[b8]:[w8]:[l8]
    
    Write command:
    write 0x0012: [count]:00:06:00:[u0]:03:80:[n]:[t0]:[r0]:[g0]:[b0]:[w0]:[l0]:[t1]
    

The mystery byte `u0` has appeared as `0x11` for single colour pulsing and red/blue flashing, `0x1f` for purple fading,
  and `0x3b` for multi-colour fading (which might have been flashing, I'm not 100% sure from the log). 
  How the fading/flashing is set is not entirely apparent. Experimentation is required here.

### Other

After connection, the app writes the phone's Bluetooth name over two handles, e.g. for "GT-9100":

    write 0x0015: [2d:49:39:31:30:30]:00:00:00:00:00:00:00:00:00:00:00:00 ["-9100"]
    write 0x0012: 00:05:00:00:00:12:00:00:00:01:4c:73:36:d0:bb:08:[47:54] ["GT"]
                   
Every so often, the app performs some reads, but I don't know what the data means, e.g.:
            
    read 0x001e: 34:9a:84:23:1e:65:ac:ec:ad:b5:4a:a0:b2:4e:28:cc:ad:ea
    
The app sometimes performs (at least once) the following write before reading from `0x001e`, e.g.:

    write 0x0012: 00:70:00:0e:00:01:01:00:00:00:00:00:00:00:00:00:00:00
    
After connection, the app also reads from handles `0x0021`, `0x0024`, e.g.:

    read 0x0021: 1e:9a:6d:d4:dd:e2:5d:aa:e4:36:9a:2a:8b:08:20:cb:2f:c7
    read 0x0024: cd:32:56:89:c1:2c:45:aa:91:dc:7b:22:65:86:4d:2a:07:58

This has also been written soon after connection, which is similar to the on/off command (i.e. the `06:00:02` part):
    
    write 0x0012 00:6c:00:06:00:02:01:03:00:00:00:00:00:00:00:00:00:00

## Notes

Some of the early section of data written to handle `0x0012` signifies the command, but it is difficult to work out
exactly where it starts and ends (and how much padding there is around it).