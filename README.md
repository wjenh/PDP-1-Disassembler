# disassemble_tape - a program to disassemble a PDP-1 binary prgram tape

## SYNOPSIS
```
disassemble_tape  [-m] [-i] [-k] [-d] [-r] tapeimagefile >resultfile
```
## DESCRIPTION
This is a two-pass disassembler for PDP-1 tape image files that contain RIM and BIN format data.
In the default use, an annotated listing is given on stdout.
A macro mode is supported to produce pure macro-compatible code, again on stdout.
For either mode, any memory location that is an address targeted by an instruction is assigned a label.
The label will them be used in the instruction and also shown at the target location.
In macro mode, the standard MACRO syntax is used.

## OPTIONS
- '-m' - macro mode, output is compatible with the MACRO1 assembler
- '-i' - list unknown IOTs encountered on stderr
- '-k' - if in macro mode an initial RIM code block will not be output because MACRO usuaally does it; this keeps it
- '-r' - raw mode, all binary data is dumped as instructions, no loader searching is done
- '-d' - debug mode, not useful except for debugging disassemble_tape
- Options can be combined, e.g. -mi.

## LIMITATIONS
This program expects a RIM mode loader section that loads the standards digital-1-3-s-mb.ddt.bin BIN loader.
If this is not the case, you can expect disassembly to fail with usually an error stating there is a malformed
BIN block.
In this case, the -r flag can be used to dump the tape as pure instructions with no other processing.
However, if the nonstandard loader uses BIN format, then the tape will be read as usual.
The key factor is that everything after the RIM section must conform to the standard BIN format.  

This program must be used with some care. A binary tape does not specify if a block of information is actually
PDP-1 instructions or if it's data. As an example, MACRO1 will produce a block for constants and andther
for variables. There is nothing to identify those blocks as such, that must be decided by the user.  

A tape image is expected to start with a 'read in' format block of data, RIM, usually followed by one or more BIN
blocks.
However, some programs are just loaded by RIM with no additonal BIN loading done.
WHen the program starts, it searches for the pattern that identifies the RIM block.
Normally, any tape character that does not have bit 0200 set is ignored,
all binary words are 18 bits and consist of 3 characters with bit 0200 set.
Any non-binary character is ignored, even if in a word sequence.

However, any non-binary characters that are seen up to the first binary character on a tape are considered to be
part of a label for the tape and will be output in the form they would appear if you were looking at a physical tape.

If binary characters occur outside of a RIM or BIN block, they could be data that is read under program control.
THey will be ignored.
## ERRORS
The following errors are possible and will be reported on stderr after which an exit(1) is done.
- Incorrect command line arguments
- Unterminated RIM block
- Malformed BIN block
- Premature EOF

## BUILDING
Just do:
cc disassemble_tape.c -o disassemble_tape

## AUTHOR
Bill Ezell (wje)  
Send comments or changes to pidp1@quackers.net
## LICENSE
This software may be freely used for any purpose as long as the author credit is kept.
It is strongly asked that the revision history be updated and any changes sent back to pdp1@quackers.net so
the master source can be maintained.

A note on formatting:
This code uses the One Really True formatting style.
While it might appear verbose, as in braces around single if() bodies and braces on separate lines,
please follow it.
It is based upon some research into causes of errors in C done at Stanford many decades ago, refined
by use over 30+ years in a commercial environment both for C and Java.
And, yes, real programmers comment their code!
