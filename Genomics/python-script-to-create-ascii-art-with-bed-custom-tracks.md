# Python script to create ASCII art with BED custom tracks

A really silly thing created in Python 3.6 to "draw" some text with introns and exons using BED format.

```python
#! /usr/bin/env python
import argparse

chars = {"A": [[(50, 250)], [(0, 100), (250, 100)], [(0, 350)], [(0, 100), (250, 100)], [(0, 100), (250, 100)]],
         "B": [[(0, 300)], [(0, 100), (250, 100)], [(0, 300)], [(0, 100), (250, 100)], [(0, 300)]],
         "C": [[(50, 300)], [(0, 100)], [(0, 100)], [(0, 100)], [(50, 300)]],
         "D": [[(0, 300)], [(0, 100), (250, 100)], [(0, 100), (250, 100)], [(0, 100), (250, 100)], [(0, 300)]],
         "E": [[(0, 350)], [(0, 100)], [(0, 250)], [(0, 100)], [(0, 350)]],
         "F": [[(0, 350)], [(0, 100)], [(0, 250)], [(0, 100)], [(0, 100)]],
         "G": [[(50, 300)], [(0, 100)], [(0, 100), (250, 150)], [(0, 100), (300, 100)], [(50, 300)]],
         "H": [[(0, 100), (250, 100)], [(0, 100), (250, 100)], [(0, 350)], [(0, 100), (250, 100)],
               [(0, 100), (250, 100)]],
         "I": [[(0, 100)], [(0, 100)], [(0, 100)], [(0, 100)], [(0, 100)]],
         "J": [[(250, 100)], [(250, 100)], [(250, 100)], [(0, 100), (250, 100)], [(50, 250)]],
         "K": [[(0, 100), (250, 100)], [(0, 100), (200, 100)], [(0, 250)], [(0, 100), (200, 100)],
               [(0, 100), (250, 100)]],
         "L": [[(0, 100)], [(0, 100)], [(0, 100)], [(0, 100)], [(0, 350)]],
         "M": [[(0, 150), (350, 150)], [(0, 200), (300, 200)], [(0, 100), (150, 200), (400, 100)],
               [(0, 100), (200, 100), (400, 100)], [(0, 100), (400, 100)]],
         "N": [[(0, 150), (350, 100)], [(0, 200), (350, 100)], [(0, 100), (150, 100), (350, 100)],
               [(0, 100), (200, 100), (350, 100)], [(0, 100), (250, 200)]],
         "O": [[(50, 300)], [(0, 100), (300, 100)], [(0, 100), (300, 100)], [(0, 100), (300, 100)], [(50, 300)]],
         "P": [[(0, 300)], [(0, 100), (250, 100)], [(0, 300)], [(0, 100)], [(0, 100)]],
         "Q": [[(50, 300)], [(0, 100), (300, 100)], [(0, 100), (300, 100)], [(0, 100), (150, 100), (300, 100)],
               [(50, 300)]],
         "R": [[(0, 300)], [(0, 100), (250, 100)], [(0, 300)], [(0, 100), (250, 100)], [(0, 100), (250, 100)]],
         "S": [[(0, 350)], [(0, 100)], [(0, 350)], [(250, 100)], [(0, 350)]],
         "T": [[(0, 400)], [(150, 100)], [(150, 100)], [(150, 100)], [(150, 100)]],
         "U": [[(0, 100), (300, 100)], [(0, 100), (300, 100)], [(0, 100), (300, 100)], [(0, 100), (300, 100)],
               [(50, 300)]],
         "V": [[(0, 100), (300, 100)], [(0, 100), (300, 100)], [(0, 100), (300, 100)], [(50, 100), (250, 100)],
               [(100, 200)]],
         "W": [[(0, 100), (350, 100)], [(0, 100), (350, 100)], [(0, 100), (200, 50), (350, 100)],
               [(0, 100), (150, 150), (350, 100)], [(50, 150), (250, 150)]],
         "X": [[(0, 100), (250, 100)], [(50, 100), (200, 100)], [(100, 150)], [(50, 100), (200, 100)],
               [(0, 100), (250, 100)]],
         "Y": [[(0, 100), (300, 100)], [(50, 100), (250, 100)], [(100, 200)], [(150, 100)], [(150, 100)]],
         "Z": [[(0, 350)], [(150, 100)], [(100, 100)], [(50, 100)], [(0, 350)]],
         "1": [[(50, 100)], [(0, 150)], [(50, 100)], [(50, 100)], [(50, 100)]],
         "2": [[(0, 300)], [(250, 100)], [(50, 250)], [(0, 100)], [(0, 350)]],
         "3": [[(0, 300)], [(250, 100)], [(50, 250)], [(250, 100)], [(0, 300)]],
         "4": [[(0, 100), (250, 100)], [(0, 100), (250, 100)], [(0, 350)], [(250, 100)], [(250, 100)]],
         "5": [[(0, 350)], [(0, 100)], [(0, 350)], [(250, 100)], [(0, 350)]],
         "6": [[(50, 300)], [(0, 100)], [(0, 350)], [(0, 100), (300, 100)], [(50, 300)]],
         "7": [[(0, 350)], [(250, 100)], [(200, 100)], [(150, 100)], [(150, 100)]],
         "8": [[(50, 250)], [(0, 100), (250, 100)], [(50, 250)], [(0, 100), (250, 100)], [(50, 250)]],
         "9": [[(50, 250)], [(0, 100), (250, 100)], [(50, 300)], [(250, 100)], [(50, 250)]],
         "0": [[(50, 300)], [(0, 100), (300, 100)], [(0, 100), (150, 100), (300, 100)], [(0, 100), (300, 100)],
               [(50, 300)]]}

exons = [([1], [0]), ([1], [0]), ([1], [0]), ([1], [0]), ([1], [0])]  # [([blockSizes], [blockStarts]), ...4 more...]
current_start = 0  # current start position for next char's blockStarts values
margin = 1500      # number of empty pbs before the first exon and after the last
space = 100        # space between letters


def width(c):
    w = 0
    lines = chars[c]
    for line in lines:
        last = line[-1]
        w = max(w, last[0] + last[1])
    return w


def add_char(c):
    if c == " ":
        update_current_start(3 * space)
    elif c in chars:
        lines = chars[c]
        for i in range(0, 5):
            for exon in lines[i]:
                exons[i][0].append(exon[1])
                exons[i][1].append(current_start + exon[0])
        update_current_start(width(c) + space)


def update_current_start(n):
    global current_start
    current_start += n


def terminate_exons():
    for i in range(0, 5):
        exons[i][0].append(1)
        exons[i][1].append(current_start)


def print_anotation(chrm, strt, rgb):
    print("")
    print("browser position chr", chrm, ": ", strt - margin, "-", strt + current_start + margin + 1, sep="")
    print("browser hide all")
    print("browser pack refGene")
    print("track name=Gentext description=Gentext visibility=2 color=" + rgb)
    for i in reversed(range(0, 5)):
        print("chr" + str(chrm), strt, strt + current_start + 1, "Gentext 0 + 0 0 0 ", len(exons[i][0]),
              ','.join(str(e) for e in exons[i][0]), ','.join(str(e) for e in exons[i][1]))
    print("")


def run(args):
    global current_start
    current_start += margin
    for c in args.txt:
        add_char(c)
    current_start += margin
    terminate_exons()
    print_anotation(args.chr, args.str, args.rgb)


def main():
    parser = argparse.ArgumentParser(description="""Converts a text into BED custom tracks. 
        By default it places the 'genes' at same genomic region as LRRTM1 gen in chromosome 2 
        and it uses a reddish color.""")
    parser.add_argument("-txt", dest="txt", type=str, required=True, help="Text to convert into BED custom tracks.")
    parser.add_argument("-chr", dest="chr", type=int, default=2, help="Chromosome.")
    parser.add_argument("-str", dest="str", type=int, default=80529003, help="Start position.")
    parser.add_argument("-rgb", dest="rgb", type=str, default="227,60,69",
                        help="Color in RGB format separated by commas (i.e. '160,64,32').")
    parser.set_defaults(func=run)
    args = parser.parse_args()
    args.func(args)


if __name__ == "__main__":
    main()
```

For example:

```bash
$ ./gentext.py -txt="PYTHON"

browser position chr2: 80527503-80536454
browser hide all
browser pack refGene
track name=Gentext description=Gentext visibility=2 color=227,60,69
chr2 80529003 80534954 Gentext 0 + 0 0 0  10 1,100,100,100,100,100,300,100,200,1 0,1500,2100,2600,2950,3200,3450,3900,4150,5950
chr2 80529003 80534954 Gentext 0 + 0 0 0  12 1,100,100,100,100,100,100,100,100,100,100,1 0,1500,2100,2600,2950,3200,3400,3700,3900,4100,4250,5950
chr2 80529003 80534954 Gentext 0 + 0 0 0  11 1,300,200,100,350,100,100,100,100,100,1 0,1500,2050,2600,2950,3400,3700,3900,4050,4250,5950
chr2 80529003 80534954 Gentext 0 + 0 0 0  13 1,100,100,100,100,100,100,100,100,100,200,100,1 0,1500,1750,2000,2200,2600,2950,3200,3400,3700,3900,4250,5950
chr2 80529003 80534954 Gentext 0 + 0 0 0  11 1,300,100,100,400,100,100,300,150,100,1 0,1500,1950,2250,2450,2950,3200,3450,3900,4250,5950
```

And [this is what we get](python-script-to-create-ascii-art-with-bed-custom-tracks.pdf) copying and pasting those annotations in the [UCSC Genome Browser](http://genome.cse.ucsc.edu/cgi-bin/hgGateway).   
 