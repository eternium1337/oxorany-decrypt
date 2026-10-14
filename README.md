# oxorany_decrypt
base way to decrypt strings protected by oxorany (in arm64-v8a and x64)

# Watch this to continue!

[![Watch this to continue](http://img.youtube.com/vi/itKsZDpIxx4/0.jpg)](http://www.youtube.com/watch?v=itKsZDpIxx4)

# Watch this to continue!


in our binary file, we immediately see the bytes of our encrypted string bytes being saved into a variable (xmmword_510)
<img width="591" height="539" alt="изображение" src="https://github.com/user-attachments/assets/40fccd42-797f-44d7-81b7-30b6341c1e9c" />

next we will move on to the graph to understand what is going on
<img width="2019" height="788" alt="изображение" src="https://github.com/user-attachments/assets/22cb38c6-064a-4aab-b209-20766f401dc8" />

we see obfuscated switch case statement that always returns false

next we watching the way that executes when check == false

<img width="1530" height="843" alt="изображение" src="https://github.com/user-attachments/assets/061db246-df48-49d9-8a4d-7d625e6174d3" />
<img width="834" height="244" alt="изображение" src="https://github.com/user-attachments/assets/5b82251f-fe6c-4ba2-8d23-5f46b4524885" />

we found main byte by byte decryption

<img width="615" height="337" alt="изображение" src="https://github.com/user-attachments/assets/a0ac1e09-d809-4a0b-ba1d-ce2caf632cc3" />

very interesting moment in pseudocode 0x50 this is the low byte of the register w20 what using in the decryption and 0x30 is bias

я устал писать это д*рьмо

next we extract the byte array

<img width="1103" height="105" alt="изображение" src="https://github.com/user-attachments/assets/f0155007-a971-480a-87bf-1ccae3954fb2" />

and we paste the values ​​into my script and run it

````python
key = 0x3695fc50
bias  = 0x30
data = bytearray.fromhex(
    "50be53e354bd56b558bc5be90c5dba5e"
    "d060d1638364da66"
)
for i in range(len(data)):
    data[i] = (((i + key) ^ data[i]) - bias) & 0xFF
print(data.decode("utf-8", errors="replace").rstrip("\x00"))
````

<img width="698" height="475" alt="изображение" src="https://github.com/user-attachments/assets/a8f2c119-5245-4613-a910-7cf565661ab1" />

we lost one letter

<img width="712" height="372" alt="изображение" src="https://github.com/user-attachments/assets/f81c65d4-7488-4ad6-852f-ea6190dc89cd" />

add bytes and...

````python
key = 0x3695fc50
bias  = 0x30
data = bytearray.fromhex(
    "50be53e354bd56b558bc5be90c5dba5e"
    "d060d1638364da66"
    "D759" #inverted because it is writing by str to 0x18 (0x90-0x78)(le)
)
for i in range(len(data)):
    data[i] = (((i + key) ^ data[i]) - bias) & 0xFF
print(data.decode("utf-8", errors="replace").rstrip("\x00"))
````

<img width="670" height="487" alt="изображение" src="https://github.com/user-attachments/assets/e4634d2a-77bd-4c94-8039-77db37a89488" />

done!
for test try on libtrain.so


# x86 & x64
same work
but we are patching the instructions that prevent us from seeing the pseudocode

<img width="558" height="467" alt="изображение" src="https://github.com/user-attachments/assets/10b8c16f-f28c-422c-9220-20ceb599427a" />

we immediately see the bytes of our encrypted string bytes

<img width="784" height="880" alt="изображение" src="https://github.com/user-attachments/assets/4eec1900-27df-4cf1-ab66-5a30725d5d4f" />

as in the previous example here too there are only negative conditions

<img width="1106" height="762" alt="изображение" src="https://github.com/user-attachments/assets/72eb2a49-b746-4e0d-ad49-0d3f1a6294b7" />

when we translated encrypted bytes to little endian, we need to paste the values ​​into script and run it

````python
key  = 0x2F
bias = 0x1A
data = bytearray.fromhex(
    "be9e8487"
    "8b5d958e"
    "8c8d9450"
    "6875"
)
for i in range(len(data)):
    data[i] = (((i + key) ^ data[i]) - (bias + key)) & 0xFF
print(data.decode("utf-8", errors="ignore"))
````

<img width="801" height="493" alt="изображение" src="https://github.com/user-attachments/assets/4a38413a-56da-4de4-a276-8b59e08ccf7b" />

done!


# i noticed that newcomers struggle with deciphering oxorany, even though it is open source and the schematic is provided
