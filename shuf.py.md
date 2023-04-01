```python
import sys
import argparse
import random

def main():
    parser = argparse.ArgumentParser( usage = """shuf.py [OPTION]... FILE
    Output randomly selected lines from FILE.""",
                                       description='Shuffle the input, and output a random permutation of the inputs lines')
    #Arguments
    parser.add_argument('filename',default='-', nargs='?',
                        help='file we take input from, default is STDIN')
    parser.add_argument('-e','--echo',nargs="*",dest="echo",help="echo the string you use here")
    parser.add_argument('-i','--input-range',action="store",dest="inputR",help="input range - range from lo-hi, lo...hi")
    parser.add_argument('-n','--head-count',action="store",dest="numlines",help="head count - number of lines to output")
    parser.add_argument('-r','--repeat',action="store_true",help="repeat - will repeatedly shuffle output")
    args = parser.parse_args()
    numL = None#Variable for doing some checking/storing
    arr = []#where ill store the random lines that will be printed later
    try:
        if args.numlines is not None:
            numL = int(args.numlines)
    except:
        print("Head count not valid")
        exit()#should catch non inte args?
    if numL != None and numL < 0:
        print("Negative head count, not valid")
        exit()
    if args.inputR is not None:
        try:
            IR = str(args.inputR)
            sp = IR.split('-')
            lo = int(sp[0])
            hi = int(sp[1])
            arr = list(range(lo, hi +1))
        except:
            print("Input Range not valid")
            exit()
        if lo<0 or hi <0:
            print("negative range")
            exit()
    ##we know n is good atp , output is done here then exits
    if args.echo is not None:
        if args.inputR is not None:
            sys.stdout.write('shuf: cannot combine -e and -i options'+ '\n'+
                             'Try ''shuf --help'' for more information.'+'\n')
            exit()
        arr = sys.argv#read args
        arr.pop(0)#delete 'shuf.py' from our array
        i = 0#delete other arguments
        while i < len(arr):
                if arr[i] == '-i':
                    arr.pop(i)
                    arr.pop(i)
                elif(arr[i]=='-r'):
                    arr.pop(i)
                elif arr[i] == '-n':
                    arr.pop(i)
                    arr.pop(i)
                elif arr[i] == '-e':
                    arr.pop(i)
                else:
                    i += 1
        random.shuffle(arr)
        #atp we have our array shuffled
        if args.repeat:
            if len(arr)==0:
                sys.stdout.write('shuf: no lines to repeat'+'\n')
                exit()
            if args.numlines != None:
                rans = int(args.numlines)
                for i in range(0,rans):
                    sys.stdout.write(str(arr[0])+'\n')
                    random.shuffle(arr)
            else:
                while True:
                    sys.stdout.write(str(arr[0])+'\n')
                    random.shuffle(arr)
        else:#no repeats
            if args.numlines == None:#no numline, so print all of file
                rans = len(arr)
            else:
                rans =  int(args.numlines)#print n lines
                for i in range(0,rans):
                    sys.stdout.write(str(arr[i])+'\n')
        exit()
        #end of echo
    #atp we have a good numline ( unless not specified, will be handled by repeat if/else stmnt  
    numAll = 0
    fileEmpty = False#to do some checks later
    try:
        if args.inputR is None:
            if vars(args)['filename']=="<stdin>":
                arr += [line.strip()
                        for line in sys.stdin.readlines()]
                numAll = len(arr) 
            else:
                inp = vars(args)['filename']
                with open(inp) as f:
                        content = f.read()
                        if not content:
                            arr=[]#for now..
                        else:#we read from that file
                            lines = open(inp).read().splitlines()
                            if (args.numlines == None):
                                numL = len(lines)#if no n specified, it takes all
                            else:#takes n lines
                                numL = int(args.numlines)
                                if numL > len(lines):
                                    numL = len(lines)#unless n > our file, then its all the file
                            arr = random.sample(lines,numL)
        random.shuffle(arr)
        #atp we have our random array shuffled
        if args.repeat:
            if len(arr)==0:
                sys.stdout.write('shuf: no lines to repeat'+'\n')
                exit()
            if args.numlines != None:
                rans = int(args.numlines)
                for i in range(0,rans):
                    sys.stdout.write(str(arr[0])+'\n')
                    random.shuffle(arr)
            else:
                while True:
                    sys.stdout.write(str(arr[0])+'\n')
                    random.shuffle(arr)
        else:#no repeats
            if args.numlines == None:#no numline, so print all of file
                rans = len(arr)
            else:
                rans =  int(args.numlines)#print n lines
            for i in range(0,rans):
                sys.stdout.write(str(arr[i])+'\n')
    except:
        exit()
if __name__ == '__main__':
    main()
```