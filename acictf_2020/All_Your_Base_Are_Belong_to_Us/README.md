# All Your Base Are Belong to Us #

**Category:**	Miscellaneous  
**Points:**	50

**Prompt:** 
* In honor of 30 years of terrible [translations](https://en.wikipedia.org/wiki/All_your_base_are_belong_to_us), we figured we'd give you a try at a series of (easier) translation problems. All you have to do is to translate bases by connecting to challenge.acictf.com:55498. In case you're new to network programs, we even have some Python [starter code](./starter_code.py) you can use.

**Hints:** 
* You could do this by hand, but is it really worth that much effort?
* While we only want the final encoding, it's probably easier to break that into separate decode and encode steps for each question.
* Don't overthink 'raw' encoding...
* Your code for encoding/decoding will probably be very similar for 4 out of 6 encodings.

**Solution:**
* I created a python script for this problem and 'Move ZIG'.
```
#!/usr/bin/python3
import argparse
import socket
import base64

# Return the value of a char.
# For example, 2 is returned for '2'
# 10 is returned for 'A', 11 for 'B', etc.
def val(c):
    if c >= '0' and c <= '9':
        return ord(c) - ord('0')
    else:
        return ord(c) - ord('A') + 10;



# Convert a number from base 'b' to base10
def toDecimal(s, fromBase):
    llen = len(s)
    power = 1
    num = 0
    # force any hex input to uppercase
    if fromBase == 16:
        s = s.upper()
    elif fromBase == 64:
        return int.from_bytes(base64.b64decode(s), 'big')
    elif fromBase == 0:
        # make sure the string is converted to bytes from raw string format
        stringBytes = s.encode("raw_unicode_escape")
        # convert to base64 with existing function and then to decimal
        # not pretty but it works    
        return toDecimal(base64.b64encode(stringBytes),64)
    
    # Decimal equivalent is str[len-1]*1 + str[len-2]*base + str[len-3]*(base^2) + ...
    for i in range(llen - 1, -1, -1):
        # Each digit must be less than the orginal base
        if val(s[i]) >= fromBase:
            print('Invalid Number')
            return -1
        num += val(s[i]) * power
        power = power * fromBase
    # returns int
    return num;

def decimalToBinary(dec):
    return bin(dec).replace("0b","")
    
def decimalToHex(dec):
    return hex(dec).replace("0x","")
   
def decimalToOctal(dec):
    return oct(dec).replace("0o","")
    
def decimalToB64(dec):
    i = int(dec)
    b64 = base64.b64encode(i.to_bytes((i.bit_length() + 7) // 8, byteorder='big'))
    return b64.decode()
    
def decimalToRaw(dec):
    b64 = decimalToB64(dec)
    raw = base64.b64decode(b64).decode()
    return raw

def baseConvert(number,fromBase,toBase):
    switcher = {
        2: lambda decimal: decimalToBinary(decimal),
        8: lambda decimal: decimalToOctal(decimal),
        16: lambda decimal: decimalToHex(decimal),
        64: lambda decimal: decimalToB64(decimal),
        0: lambda decimal: decimalToRaw(decimal)
    }
    if toBase != 2 and toBase != 8 and toBase != 10 and toBase != 16 and toBase != 64 and toBase != 0:
        print("Unsupported output base!")
    # toDecimal returns type int
    dec = toDecimal(number, fromBase)
    if toBase == 10:
        return str(dec)
    result = switcher[toBase](dec)
    return str(result)
    
    
def sendAnswer(convertType, data):
    baseDict = {
        "bin": 2,
        "oct": 8,
        "dec": 10,
        "hex": 16,
        "b64": 64,
        "raw": 0
    }
    convertTuple = convertType.split(' -> ')
    convertFrom = convertTuple[0]
    convertTo = convertTuple[1]
    print("Convert From: "+convertFrom)
    print("Convert To: "+convertTo)
    print(data)
    

    answer = baseConvert(data, baseDict[convertFrom], baseDict[convertTo])
    print("sending answer: "+str(answer))
    sock.send((answer + "\n").encode()) # The "\n" is important for the server's
                                        # interpretation of your answer, so make
                                        # sure there is only one sent for each
                                        # answer.



# 'argparse' is a very useful library for building python tools that are easy
# to use from the command line.  It greatly simplifies the input validation
# and "usage" prompts which really help when trying to debug your own code.
parser = argparse.ArgumentParser(description="Solver for 'All Your Base' challenge")
parser.add_argument("ip", help="IP (or hostname) of remote instance")
parser.add_argument("port", type=int, help="port for remote instance")
args = parser.parse_args();

# This tells the computer that we want a new TCP "socket"
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# This says we want to connect to the given IP and port
print("Connecting to -> "+args.ip+":"+str(args.port))
sock.connect((args.ip, args.port))

# This gives us a file-like view for receiving data from the connection which
# makes handling messages from the server easier since it handles the
# buffering of lines for you.  Note that this only helps us on receiving data
# from the server and we still need to send data over the underlying socket
# (i.e. `sock.send(...)` at the end of the loop below).
f = sock.makefile()


# This is the data separator/delimiter for this particular challenge
separator = "-"*78
isFirstSeparator = True

emptyLineCount = 0
while True:
    line = f.readline().strip()
    print(line)
    # This iterates over data from the server a line at a time.  This can
    # cause some unexpected behavior like not seeing "prompts" until after
    # you've sent a reply for it (for example, you won't see "answer:" for
    # this problem). However, you can still "sock.send" below to transmit data
    # and the server will handle it correctly.
    if line == separator:
        if isFirstSeparator:
            # We have reached a separator, but which one?
            convertType = f.readline().strip()
            data = f.readline().strip()
            sendAnswer(convertType, data)
            isFirstSeparator = False
        else:
            isFirstSeparator = True
            pass    
    elif line == "":
        emptyLineCount += 1
        if emptyLineCount >= 15:
            break
    else:
        emptyLineCount = 0
        pass
        

    #   2) Extract the three primary parts of each question:
    #      a) The source encoding
    #      b) The destination encoding
    #      c) The source data
    #   3) Convert the source data to some "standard" encoding (like 'raw')
    #   4) Convert the "standardized" data to the destination encoding

```
```
python3 baseConvert.py challenge.acictf.com 34243
```
* ACI{Somebody_set_up_us_the_bomb_8b58c9fc}
