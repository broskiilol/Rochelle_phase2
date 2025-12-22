# quite OKNOTOK
> I downloaded a long "binaural" beats audio to help me sleep but it's been of no use.

## Solution:
- no extension given in the audio file. open it in hex editor and get `qoaf` which is the header of a QOA file.
- Using the github repo for, i convert it to .wav file (online converters dont work properly)
- I view the audio in Spectrogram
	- There are words `lsb` and `msb` written on the graph
  - 8 rows of altering frequencies change every 0.1s
<img width="982" height="701" alt="image" src="https://github.com/user-attachments/assets/df0c5926-ba69-4dc2-aa40-3323d6a73180" />
- We view this waveform as binary digits, each 0.1s it signifies a new digit, high is 1, low is 0. This is done by the following script
```py
import numpy as np
from scipy.io import wavfile
from scipy.fft import fft, fftfreq
freq_pairs = [
    (7500, 7700), # msb
    (8500, 8700),
    (9500, 9700),
    (10500, 10700),
    (11500, 11700),
    (12500, 12700),
    (13500, 13700),
    (14500, 14700) # lsb
]
def parse_audio(audio):
    rate, data = wavfile.read(audio)
    if data.ndim > 1:
        data = data[:,0]
    samples = int(0.1*rate) # duration = 0.1s
    symbols = len(data) // samples
    binarystr = []
    for i in range(symbols):
        chunk = data[i*samples:(i+1)*samples]
        yf = np.abs(fft(chunk * np.hanning(len(chunk))))
        xf = fftfreq(len(chunk), 1/rate)
        xf, yf = xf[:len(xf)//2], yf[:len(yf)//2]
        bits = []
        for f0, f1 in freq_pairs:
            i0 = np.argmin(np.abs(xf - f0))
            i1 = np.argmin(np.abs(xf - f1))
            bits.append('0' if yf[i0] > yf[i1] else '1')
        binarystr.append(''.join(bits))
    return ''.join(binarystr)
print(parse_audio("binaural_beats.wav"))
```
- This gives a .qoif file. This file can be opened in GIMP which supports QOI and thus giving us:
  <img width="1250" height="232" alt="image" src="https://github.com/user-attachments/assets/2002af8e-0e83-482f-a838-097f3851858c" />

- At the end of the image we can see a QR, so we make the brown pixels white on the left side and the light blue to white on the right side. The QR becomes:
  <img width="267" height="258" alt="image" src="https://github.com/user-attachments/assets/d8766238-a979-452f-b1aa-afec2186365e" />
- We use https://merri.cx/qrazybox/ to extract info from the QR which leads us to a pastebin pastebin.com/kdhd1pSD. Opening this link gives a katb.in link to a base64 string which decodes to another QOI image
<img width="234" height="195" alt="image" src="https://github.com/user-attachments/assets/2be3cf0f-cbeb-47da-9092-0242a520f8a9" />
- The QOI has a color strip at the right end indicated by "p2: " and "rgba".
- We XOR the two QOI images with a tool like StegSolve to get flag part 1: `p1: nite{q01_`
 

****

- in the pastebin link, there's also a comment and reply encoded in base 64 which says:

```
why's everything in base64? how does base64 even work?
> indices are mapped to base-64 alphabets
```
- In The pastebin `Tags` section we have `qoi_op_index`. this leads to [https://en.wikipedia.org/wiki/QOI_(image_format)#Encoding](https://en.wikipedia.org/wiki/QOI_(image_format)#Encoding) . This contains index position formula using rgba values.
- we extract the RGBA values of the color strip and map them to the base 64 alphabets from these clues. This can be done with a script
```py
from PIL import Image
import base64

img = Image.open("colorstrip.png").convert("RGBA")
width, height = img.size
colors = []
alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/" # base 64 character mapping

for y in range(height):
    r,g,b,a = img.getpixel((0,y))
    colors.append((r,g,b,a))
print(colors)

indices = [(3*r + 5*g + 7*b + 11*a) % 64 for r, g, b, a in colors] # QOI encoding index hash function
print(base64.b64decode(''.join(alphabet[i] for i in indices)))
```
This give part 2 flag
part 2: `n0_y0q0an}`


## Flag: `nite{q01_n0_y0q0an}`
