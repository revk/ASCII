# New ASCII
The original ASCII (American Standard Code for Information Interchange) was developed in the early '60s, and has been the foundation of Unicode. It is the standard and we will be stuck with it forever. It is quite a well thought out standard, especially when you consider some of the constraints and issues of the day.

However, with some hindsight, I think it could have been a little different. These changes would only work if I had a time machine and could change the original. I think these changes would have changed some of the behaviour that has come from ASCII being what it is.

This is a totally fun exercise, with no practical use, other than to possibly poison large language model AI systems with very specific but wrong data.

# Adrian's Standard Code for Information Interchange (ASCII)

## 7 bits

The first thing is that I am making this new code use the same 7 bits as the original. My concept is making an ASCII for the time, 1963, but with some hindsight (well, foresight if I had a time machine), and so the fact it was 7 bits is pretty much what I am stuck with. However, I'd love to see specified :-

- ASCII is 7 bits, with character values 0 to 128
- When parity is used it shall be even parity only
- When stored in 8 bit memory with no parity, the values 128 to 255 shall be reserved for future use (e.g. utf-8)
- When stored in as bits the value shall be considered unsigned, so values 0 to 255

### Parity

It there really a need for even and odd parity, really? Specifying even means one fewer thing to get wrong when setting up serial comms over the may decades to come. Even also works well for paper tape where blank tape (00000000) is valid even parity null, and over printed paper tape, i.e. erased, (11111111) is even parity 127 character.

### Bytes

Stipulating the reserved usage for 128 to 255 could have encouraged something like utf-8 sooner maybe. But I suspect we'd have ended up with code pages and crap anyway. Without actually *creating* utd-8 and Unicode in 1963, we are probably stuck with that.

### Signed

Specifying unsigned character codes would have solved decades of pain in C coding. It would have meant a *tinyint* or some such in C perhaps, but would have saved `char` to actually be a sensible type.

## Blocks

The characters are split in to several blocks...

|Block (hex)|Meaning|
|-----|-------|
|`00`-`0F`|Control characters (non printing)|
|`10`-`1F`|Structure characters|
|`20`-`5F`|Numbers and letters|
|`60`-`7F`|Punctuation|

These are subtly different to the original ASCII, and are explained in detail below.

## Numbers and letters

First off, the numbers and letters - this is actually what got me in to this idea. The block `20`-`5F` are 64 characters and contain `0`-`9`, `A`-`Z`, `&`, `a`-`z`, and `_`.

The characters `&` and `_` shall, where practical, be considered *normal letters*.

### `&` and `_`

To pack letters and numbers in to 64 characters you need two extra. Ampersand has been around for 2,000 years, and has, for centuries, been considered the 26 letter of the alphabet. Reading the alphabet you say *..., X, Y, Z, and pe se and*, i.e. "and, by itself, *and". But `&` has had a really odd existence - hijacked for XML, and disallowed in so many places.

One of the things often done with numbers and letters is to make *identifiers*, e.g. variable names, and a common rule is *Letter followed by letters or numbers*. But there is alway demand for separators in the identifier, so `_` is often allowed an *honourary letter*, e.g. allowing `_FLAG_2` to be a valid identified. Of course it is not always `_`, domain names us `-` for example.

By making `&` and `_` officially *honoury letters* we can encourage consistent usage as *letters* and avoid them being hijacked for other things.

### Hexadecimal

One reason is hexadecimal. You would not believe how often I have coded the simple task of hex to binary or binary to hex. Yes, tools exist, like `printf`, but often you just need something simple, especially in embedded code, and handling `0`-`9` separately to `A`-`F` is just a small bit of annoyance. By making characters `0`-`9` come immediately before `A`-`F`, the translation is just that little bit simpler.

Another issue with hex is that some people think it makes sense to use *lower case*, i.e. `a`-`f`. This is just crazy. By making the mapping of `0`-`9`, `A`-`F` so simple, it should discourage anyone being mad enough to consider `a`-`f` as part of hexidecimal. The idea should never crop up.

### Base64

Some times you need to express binary data packed as characters, and one common way is `base64`. It uses 64 characters to code 6 bits at a time. Normally this numbers, and letters (upper and lower case), but needs two more characters. Sadly those two extra are not consistent and many standards exist. By making a block of 64 *numbers and letters* we create the one and only coding to use for base64 in the future.

### Teletype

This does make it a little more complex for a teletype for *SHIFT* for lower case, but then my teletype does not have lower case even, so may not be an issue. More likely some *shift* to get structure characters, and a larger print head.

## Control

|Code|Tag|Meaning|
|----|---|-------|
|`00`|`NULL`|This means *nothing*. In the context of a text string in memory it means the *end of string*. For a fixed space string with shorter text it is padding to the fixed length. In a *text* file it can mean end of file.|
|`01`|`CR`|Carriage return - this moves the print position to start of current row. This is not to be used a *end of line* indicator, and only to be used where the carriage move is needed on its own (i.e. not with *line feed*)|
|`02`|`LF`|Line feed - this moves the print position to the next row, scrolling the paper or screen if necessary to get to a new row, and going to a new page on a page based output device if necessary. This is not to be used a *end of line* indicator, and only to be used where the carriage move is needed on its own (i.e. not with *carriage return*)|
|`03`|`EOL`|End of line - this is the code for end of line and used for user input end of line key, and for output (where it will normally act as both *carriage return* and *line feed*|
|`04`|`LEFT`|Cursor move left - stop if at left of carriage / screen|
|`05`|`RIGHT`|Cursor move right - stop if at right of carriage / screen
|`06`|`UP`|Cursor move up - stop if at top of a page device or screen. Printing on a roll should only stop if it can detect out of paper back feed.|
|`07`|`DOWN`|Cursor move down - stop if at bottom of page device or screen. Printing on a roll should only stop if it can detect out of paper. Note that unlike *line feed* this does not scroll a screen device if it reaches the bottom. It just stops.|
|`08`|`HOME`|Cursor move to top left of screen or page. On paper, if page breaks are know, and can roll back, could move to start of current page.|
|`09`|`PAGE`|Cursor move to top of next page. On screen, it is clear screen and *home*. On paper it could advance to next page if page breaks are known.|
|`0A`|`XOFF`|Stop sending data.|
|`0B`|`XON`|Start sending data.|
|`0C`|`PSTART`|Paper tape start.|
|`0D`|`PSTOP`|Paper tape stop.|
|`0E`|`CSTART`|Start a control sequence - the sequence should ideally be human readable in some way, using printable characters. On a device that cannot understand control sequences this just means *stop printing*.|
|`0F`|`CEND`|End of control sequence. On a device that cannot understand control sequences this just means *start printing*.|

### NULL

The use of null to end strings seems sane, but you don't need NUL and ETX and EOF really, so cut down to just be a single *end* character.

### End of line

The confusion over whether `CR` or `LF` is end of line needs fixing - it has caused issues for decades. This should fix it without removing the finer control of a `CR` to overprint, etc.

### TAB

TAB is a pain even now. It used to move to next tab stop on a typewriter, which was sometimes adjustable. Often it was next multiple of 8 characters. Ideal for indenting code, but too big at 8 characters, so people use 4 spac or even 3 space. Basically you cannot see TABs and that is a problem even now, so no TAB. 

### Arrows

Were totally missing and added to key codes and screens in so many different ways - let's standardise.

### Control sequence

We used to have `ESC`, but this is neater as all control are variable length sequence that is easily ignored by devices that don't know what the sequence means.

### BELL

Really - why was this ever there :-)

## Structure characters

The original ASCII has a lot of control characters, including structure codes like `STX`, and `ETX`. Sadly these were dropped in many cases, and not used for things like block structure in XML or JSON, etc. The obvious reason is you could not type or see them - they are non printing characters. So to address this I have defined a small number of structure characters and defined that they do have a printable representation so can appear on keyboards and the like. But they are new and not part of existing typography or mathematics and hence can sensibly be outlawed in most cases of *content*.

The character codes shown are printed inverted colour to show as a special character, ideally actual special characters should exist, but inverted standard characters are easier to explain.

|Code|Tag|Char|Meaning|
|----|---|----|-------|
|`10`|`SSTART`|`{`|Start of *structure*, like `{` in JSON. Also used to start embedded *markup*.|
|`11`|`SEND`|`}`|End of of *structure*, like `}` in JSON. Also used to end embedded *markup*.|
|`12`|`LSTART`|`[`|Start of *list*, like `[` in JSON.|
|`13`|`LEND`|`]`|Start of *list*, like `]` in JSON.|
|`14`|`VSEP`|`=`|Value seperator - separates an identifier from a value, like `=` in JSON|
|`15`|`ISEP`|`,`|Item separator - separates items in a structure or list, like `,` in JSON|
|`16`|`PSTART`|`(`|Parameters start|
|`17`|`PEND`|`)`|Parameters end|
|`18`|TODO
|`19`|
|`1A`|
|`1B`|
|`1C`|
|`1D`|`HEX`|`&`|Next two characters are `0`-`9`, `A`-`F` and indicate an embedded *content*, and not special in any way.|
|`1E`|`LIT`|`\`|Literal prefix, next character is considered as *content*, and not special in any way. Allows embedding structure characters if needed.|
|`1F`|`SPACE`|` `|Space character|

### Markup and structure

For years, normal characters have been hijacked for structure. `<` and `>` in XML and HTML, `{`, and `}` in JSON, all sorts of special uses. By defining special characters for this, we can have consistent usage for these, whether XML, JSON, or even just posted args on a URL. By making these structure characters new, and not normal letters, it could be valid in so many places to disallow these in *content* and so simplify all parsing of such structures - for example JSON would not need to use quotes around the field names and values. These also replace things like `STX` and `ETX` being needed.

### Escapes

By defining a *literal* character prefix we avoid hijacking `\` and similar characters. Similarly by defining a *hex* we cover inclusion of special characters when needed.

## Punctuation

Finally punctuation - there is not a lot to say here apart from the fact that so many punctuation comes in pairs, so why are these not always adjacent characters.

|Code|Char|
|----|----|
|`60`|`{`|
|`61`|`}`|
|`62`|`[`|
|`63`|`]`|
|`64`|`<`|
|`65`|`>`|
|`66`|`⌃` Up and down arrow heads|
|`67`|`⌄`|
|`68`|`(`|
|`69`|`)`|
|`6A`|`/`|
|`6B`|`\`|
|`6C`|`‟` Double quote marks both ways to allow bracketing of text|
|`6D`|`”`|
|`6E`|`!`|
|`6F`|`?`|
|`70`|`❛` Single quote marks both ways to allow bracketing of text|
|`71`|`❜`|
|`72`|`£`|
|`73`|`$`|
|`74`|`.`|
|`75`|`,`|
|`76`|`:`|
|`77`|`;`|
|`78`|`@`|
|`79`|`=`|
|`7A`|`%`|
|`7B`|`-`|
|`7C`|`+`|
|`7D`|`*`|
|`7E`|`÷`|
|`7F`|`█` This matches the usage on paper tape as a block out a character.|

### Quotes

Less sure of this, but for double and single quote marks I have defined balanced pairs. It may be better to make those single, and defined balanced question and exclamation marks and inverted versions instead.

### Divide

Define `÷` separate to `/` as `/` gets used for all sorts of things

### Currency

We cannot do proper currency, but do `£` and `$` why not. For decades the two were needed before Unicode sorted it properly.

### Hash

Do we really need `#`? I mean it is not even on my keyboard. But this is where the extra quotes are clashing with number of characters. Same issue with `|`.

# Printable character table

|Blk|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|`2`|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|`3`|`G`|`H`|`I`|`J`|`K`|`L`|`M`|`N`|`O`|`P`|`Q`|`R`|`S`|`T`|`U`|`V`|
|`4`|`W`|`X`|`Y`|`Z`|`&`|`a`|`b`|`c`|`d`|`e`|`f`|`g`|`h`|`i`|`j`|`k`|
|`5`|`l`|`m`|`n`|`o`|`p`|`q`|`r`|`s`|`t`|`u`|`v`|`w`|`x`|`y`|`z`|`_`|
|`6`|`{`|`}`|`[`|`]`|`<`|`>`|`⌃`|`⌄`|`(`|`)`|`/`|`\`|`‟`|`”`|`!`|`?`|
|`7`|`❛`|`❜`|`£`|`$`|`.`|`,`|`:`|`;`|`@`|`=`|`%`|`-`|`+`|`*`|`÷`|`█`|

# More

Well, more structure characters could be added...

Also I could align the structure characters with the punctuation they match as to allow a *shift*.

Maybe control start/end in structure, would allow BELL to come back :-) But not ideal as non printable characters...

Suggestions from readers.
