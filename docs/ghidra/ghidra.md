# Ghidra

*The Dragon can’t be contained.*


## Cheatsheet
- `ALT-Left Arrow` - Go to the previous location
- `ALT-Right Arrow` - Go to the next location
- `L` - Rename a variable
- `CTRL-L` - Retype a variable
- `Middle mouse button` - Set a highlight
- `Space` - In disassembly view will Collapse/Uncollapse a function
- `;` - In listing view, open comment menu
- `F` - Edit function


## Configuration

## Common knowledge
### Dynamic Libraries 
The message:
> *The external program "NAME" is not associated with a Ghidra Program. Would you like to create an association ?*

Allow to associate an external dll (which can be analysed separetly) to your program under analysis.

### Listing view:
- Enable/Disable source view removes the line/source file info in disassembly view
### Info
- In sequence like `LEA EAX=>some_variable, [EBP + -0x32]` the part `EAX=>some_variable`is Ghidra annotation to mean that EAX now points to `0x32` which is the `some_variable`

## Stack

### in_stack artefacts
The `in_stack_ffffffff...` names might appear because Ghidra's decompiler doesn't understand a function signature or it's calling convention, or the function using parameters is using custom-storage that Ghidra had custom stack storage locked on, so it never mapped them to the actual registers (`RCX`/`DL`/`R8D`) and it invents phantom stack params. If you fix the prototype of the caller or callee function these artefacts will vanish.
For example in main:
```c
char *in_stack_ffffffffffffff58;
char in_stack_ffffffffffffff60;
int in_stack_ffffffffffffff68;

xor_encrypt(in_stack_ffffffffffffff58,in_stack_ffffffffffffff60,in_stack_ffffffffffffff68);
```
After fixing `xor_encrypt` by removing the custom storage the main function looks like:
```c
xor_encrypt(str,'2',(int)sVar4);
```

### Stack Variable References

- `[EBP + local_20]` means the local variable whose frame offset Ghidra labeled local_20, which the CPU actually reads as `EBP - 0x1c`. The name is the offset from the frame base, and EBP sits 4 bytes above that base.
- `[ESP + local_20]` means the same variable addressed off the stack pointer instead, so the real displacement is whatever positive number puts you at that slot given how far ESP has moved.

!!! note
    To disable this go to `Edit > Tools Options > Listing Fields > Operands fields > uncheck Markup Stack Variable References` 
    
    The listing then shows the real encoded displacement, e.g. `MOV dword ptr [EBP + -0x1c], EAX` instead of `[EBP + local_20]`.
    
    If you only want it off for one function, right-click it `Function > Edit Stack Frame` and `delete the variable rows`; that window also shows every variable's offset side by side, which is often faster than flipping the global option.

### Tips
- You can clean up those pesky runs of cc's, ff's, 90's, and/or 00's by placing the cursor on the byte value you wish to condense and running the CondenseAllRepeatingBytes script.

- Sometimes Ghidra keeps showing parameter names on registers even after those registers are reused for other things; to fix it, uncheck `Edit → Tool Options → Listing Fields → Operands Field → "Markup Register Variable References"`.
- You can add symbol information to your comments that automatically update when your symbols change. Hit F1 while making a comment to learn how. 


### Type prefixes

Only two are hardcoded, and they stack recursively:

| Letter | Meaning |
|---|---|
| `p` | pointer - prepended per level, then recurses (`ppcVar1` = `char **`) |
| `a` | array — prepended, then recurses (`auStack_20` = array of `undefined`) |

Everything else falls out of the type name:

| Letter | Common types |
|---|---|
| `i` | `int` |
| `u` | `uint`, `undefined`, `undefined4`, `ushort`, `ulong` |
| `c` | `char`, `code` |
| `s` | `short` |
| `l` | `long`, `longlong` |
| `b` | `bool`, `byte` |
| `f` | `float` |
| `d` | `double`, `dword` |
| `w` | `word`, `wchar_t` |
| `q` | `qword` |
| `v` | `void` |

!!! note 
    Not a fixed table. The decompiler emits the **first character of the data-type's name** (`printNameBase` in `type.hh`), so custom types get their own letter a struct `Point` gives `P`, an enum `Mode` gives `M`. To this day, August 2026, Ghidra does not provide official documentation regarding variable prefix.

!!! Warning
    Observe  the collisions: `bVar1` is `bool` or `byte`, `dVar1` is `double` or `dword`. The letter tells you the first character, not the type.

### Storage prefixes

These replace the `<type><Var|Stack>` form entirely.

| Prefix | Meaning |
|---|---|
| `param_<n>` | declared parameter, positional |
| `local_<hex>` | stack local named by Ghidra's analysis |
| `<type>Stack_<hex>` | stack local found by the decompiler itself |
| `<type>Var<n>` | register/temporary with no fixed storage |
| `in_<reg>` | read before entry; ABI does *not* preserve it |
| `unaff_<reg>` | read before entry; ABI promises it is preserved |
| `unaff_retaddr` | return-address storage, read before entry |
| `extraout_<reg>` | produced after a call, as a side effect of that call |

## Tips
### Functions
Ghidra might not recognize variadic functions properly and display weird PCode such as:
```c
printf("Enter Password To Continue : ");
scanf("%s");
```
A quick fix is to right click on the function `Edit Function Signature` and specify that `scanf` is variadic such as `int scanf(char * format, ...)` . This will nicely display the function like:
```c
scanf("%s", local);
```