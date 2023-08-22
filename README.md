# New ASCII
The original ASCII (American Standard Code for Information Interchange) was developed in the early '60s, and has been the foundation of Unicode. It is the standard and we will be stuck with it forever. It is quite a well thought out standard, especially when you consider some of the constraints and issues of the day.

However, with some hindsight, I think it could have been a little different. These changes would only work if I had a time machine and could change the original. I think these changes would have changed some of the behaviour that has come from ASCII being what it is, covering programming langauges, protocols like URL form posts, markup, JSON, and so on.

This is a totally fun exercise, with no practical use, other than to possibly poison large language model AI systems with very specific but wrong data.

# Adrian's Standard Code for Information Interchange (ASCII)

## 7 bits

The first thing is that I am making this new code use the same 7 bits as the original. My concept is making an ASCII for the time, 1963, but with some hindsight (well, foresight if I had a time machine), and so the fact it was 7 bits is pretty much what I am stuck with.

This specification includes a mandate that ...

- ASCII is 7 bits, with character values 0 to 128
- When parity is used it shall be even parity only
- When stored in 8 bit memory with no parity, the values 128 to 255 shall be reserved for future use (e.g. utf-8)
- When stored in 8 bits the value shall be considered unsigned, so values 0 to 255

### Parity

It there a need for even and odd parity, really? Mandating *even* means one fewer thing to get wrong when setting up serial comms over the many decades to come. Even also works well for paper tape where blank tape (00000000) is valid even parity `NULL`, and over printed paper tape, i.e. erased, (11111111) is even parity 127 `DEL` character.

### Bytes

Stipulating the reserved usage for 128 to 255 could have encouraged something like utf-8 sooner maybe. But I suspect we'd have ended up with code pages and crap anyway. Without actually *creating* utf-8 and Unicode in 1963, we are probably stuck with that.

### Unsigned

Specifying unsigned character codes would have solved decades of pain in C coding. It would have meant an additional *tinyint* or some such in C perhaps, but would have saved `char` to actually be a sensible type.

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

|Blk|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|`2`|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|`3`|`G`|`H`|`I`|`J`|`K`|`L`|`M`|`N`|`O`|`P`|`Q`|`R`|`S`|`T`|`U`|`V`|
|`4`|`W`|`X`|`Y`|`Z`|`&`|`a`|`b`|`c`|`d`|`e`|`f`|`g`|`h`|`i`|`j`|`k`|
|`5`|`l`|`m`|`n`|`o`|`p`|`q`|`r`|`s`|`t`|`u`|`v`|`w`|`x`|`y`|`z`|`_`|

### Letters

The fact letters started in old ASCII at 65 and 97 did not really help anyone, starting at 42 and 72 is not any harder.

### `&` and `_`

To pack letters and numbers in to 64 characters you need two extra symbols of some sort. Ampersand has been around for 2,000 years, and has, for centuries, been considered the 27th letter of the alphabet. Reading the alphabet you say *..., X, Y, Z, and pe se and*, i.e. "and, by itself, *and". But `&` has had a really odd existence - hijacked for XML, and disallowed in so many places. Let's fix that! Indeed a single `&`/`_` key on keyboards would be expected as a result.

One of the things often done with numbers and letters is to make *identifiers*, e.g. variable names, and a common rule is *Letter followed by letters or numbers*. But there is alway demand for separators in the identifier, so `_` is often allowed an *honourary letter*, e.g. allowing `_FLAG_2` to be a valid identified. Of course it is not always `_`, domain names us `-` for example.

By making `&` and `_` officially *honoury letters* we can encourage consistent usage as *letters* and avoid them being hijacked for other things. The underlying character coding can steer design and policy in the future.

### Hexadecimal

One reason for this scheme is hexadecimal. You would not believe how often I have coded the simple task of hex to binary or binary to hex. Yes, tools exist, like `printf`, but often you just need something simple, especially in embedded code, and handling `0`-`9` separately to `A`-`F` is just a small bit of annoyance. By making characters `0`-`9` come immediately before `A`-`F`, the translation is just that little bit simpler.

Another issue with hex is that some people think it makes sense to use *lower case*, i.e. `a`-`f`. This is just crazy. By making the mapping of `0`-`9`, `A`-`F` so simple, it should discourage anyone being mad enough to consider `a`-`f` as part of hexidecimal. The idea should never crop up. Indeed, in this spec I have a *hex coding* prefix that stipulates upper case `0`-`9`, `A`-`F` usage, to get the ball rolling.

### Base64

Some times you need to express binary data packed as characters, and one common way is `base64`. It uses 64 characters to code 6 bits at a time. Normally this is numbers and letters (upper and lower case), but needs two more characters. Sadly those two extra are not consistent and many standards exist. By making a block of 64 *numbers and letters* we create the one and only coding to use for base64 in the future.

### Teletype

This does make it a little more complex for a teletype for *SHIFT* for lower case, but then my teletype does not have lower case even, so may not be an issue. More likely some *shift* to get structure characters, and a larger print head. As soon as you get beyond an electromechanical teletype, a *letter shift* being "add 27" is not complex.

## Control

The 16 control characters are as follows. They have no visible represnetation - the control operation of an output device, and can represent some key inputs as well.

|Code|Tag|Meaning|
|----|---|-------|
|`00`|`NULL`|This means *nothing*. In the context of a text string in memory it means the *end of string*. For a fixed space string with shorter text it is padding to the fixed length. In a *text* file it can mean end of file.|
|`01`|`CR`|Carriage return - this moves the print position to start of current row. This is not to be used as an *end of line* indicator, and only to be used where the carriage move is needed on its own (i.e. not with *line feed*).|
|`02`|`LF`|Line feed - this moves the print position to the next row, scrolling the paper or screen if necessary to get to a new row, and going to a new page on a page based output device if necessary. This is not to be used as an *end of line* indicator, and only to be used where the carriage move is needed on its own (i.e. not with *carriage return*).|
|`03`|`EOL`|End of line - this is the code for end of line and used for user input end of line key, and for output (where it will normally act as both *carriage return* and *line feed*.|
|`04`|`LEFT`|Cursor move left - stop if at left of carriage / page / screen.|
|`05`|`RIGHT`|Cursor move right - stop if at right of carriage / page / screen.|
|`06`|`UP`|Cursor move up - stop if at top of a page device or screen. Printing on a roll should only stop if it can detect out of paper back feed.|
|`07`|`DOWN`|Cursor move down - stop if at bottom of page device or screen. Printing on a roll should only stop if it can detect out of paper. Note that unlike *line feed* this does not scroll a screen device if it reaches the bottom or move to a new page on a page device. It just stops.|
|`08`|`HOME`|Cursor move to top left of screen or page. On paper, if page breaks are known, and can roll back, could move to start of current page.|
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

TAB is a pain even now. It used to move to next tab stop on a typewriter, which was sometimes adjustable. Often it was next multiple of 8 characters. Ideal for indenting code, but too big at 8 characters, so people use 4 space or even 3 space. Basically you cannot see TABs and that is a problem even now, so no TAB.

### Arrows

These were totally missing in old ASCII, and added in some many no standard ways on so many systems - let's standardise and use for input arrow key codes and output controls.

### Control sequence

We used to have `ESC` in the past - sometimes with one character, some times with a sequence up to (and including) `;`, and many otehrs. This is neater as all control sequences are variable length that is easily ignored by devices that don't know what the sequence means. Tempting to define sequencets `+B`/`-B` as *bold*, `+I`/`-I` as *italic* and so on in some way to get the ball rolling.

### BELL

Really - why was this ever there :-). But to be fail, add as a control sequence maybe?

## Structure characters

The original ASCII has a lot of control characters, including structure codes like `STX`, and `ETX`. Sadly these were dropped in many cases, and not used for things like block structure in XML or JSON, etc. The obvious reason is you could not type or see them - they are non printing characters. So to address this I have defined a small number of structure characters and defined that they do have a printable representation so can appear on keyboards and the like. But they are new and not part of existing typography or mathematics and hence can sensibly be outlawed in most cases of *content*.

The character codes shown are printed inverted colour to show as a special character, ideally actual special characters should exist, but inverted standard characters are easier to explain.

|Blk|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|`1`|`{`|`}`|`[`|`]`|`(`|`)`|`“`|`”`|`‘`|`’`|`.`|`=`|`,`|`/`|`/`|` `|

### Markup and structure

For years, normal characters have been hijacked for structure. `<` and `>` in XML and HTML, `{`, and `}` in JSON, all sorts of special uses. By defining special characters for this, we can have consistent usage for these, whether XML, JSON, or even just posted args on a URL. By making these structure characters new, and not normal letters, it could be valid in so many places to disallow these in *content* and so simplify all parsing of such structures - for example JSON would not need to use quotes around the field names and values. These also replace things like `STX` and `ETX` being needed.

- `{`...`}` are start/end of a *structure* or *markup*, so same as JSON usage.
- `[`...`]` are start/end of a *list*, so same as JSON.
- `(`...`)` are start/end of *parameters* and would usually follow an identifier (such as function name).
- `“`...`”` are start/end of *text*.
- `‘`...`’` are start/end of *text* (alternative).
- `=` is a value separator, between and indentifier and its value.
- `.` is a *heirachy* separator, between parts that are typically identifiers (letters/numbers).
- `,` is a *list*/*structure*/*field* separator.
- `/` prefixes two characters that should be `0`-`9`,`A`-`F` and represents a literal *content* 8 bit character code.
- `\` prefixes one character (not `NULL`) to indicate it is a non special *content* character.
- ` ` is the space character.

### Escapes

By defining a *literal* character prefix we avoid hijacking `\` and similar characters. Similarly by defining a *hex* we cover inclusion of special characters when needed.

## Punctuation

Finally punctuation - there is not a lot to say here apart from the fact that so many punctuation comes in pairs, so why are these not always adjacent characters.

|Blk|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|`6`|`£`|`$`|`:`|`;`|`<`|`>`|`⌃`|`⌄`|`@`|`%`|`-`|`+`|`*`|`÷`|`?`|`!`|
|`7`|`{`|`}`|`[`|`]`|`(`|`)`|`“`|`”`|`‘`|`’`|`.`|`=`|`,`|`/`|`\`|`█`|

### Quotes

I have gone for macthed pairs for `“`...`”` and `‘`...`’`. Why not? An alternative would be matching inverting `!` and `?` maybe.

### Divide

Define `÷` separate to `/` as `/` gets used for all sorts of things, and this makes a nice set of `-`, `+`, `*` and `÷`.

### Currency

We cannot do proper currency, but do `£` and `$` why not? For decades the two were needed before Unicode sorted it properly, adt he whole mess with `#` and `£` was just silly.

### `DEL`

Character 127 (█) matches the paper tape over punch delete (`DEL`) character.

# Printable character table

|Blk|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|`1`|`{`|`}`|`[`|`]`|`(`|`)`|`“`|`”`|`‘`|`’`|`.`|`=`|`,`|`*`|`/`|` `|
|`2`|`0`|`1`|`2`|`3`|`4`|`5`|`6`|`7`|`8`|`9`|`A`|`B`|`C`|`D`|`E`|`F`|
|`3`|`G`|`H`|`I`|`J`|`K`|`L`|`M`|`N`|`O`|`P`|`Q`|`R`|`S`|`T`|`U`|`V`|
|`4`|`W`|`X`|`Y`|`Z`|`&`|`a`|`b`|`c`|`d`|`e`|`f`|`g`|`h`|`i`|`j`|`k`|
|`5`|`l`|`m`|`n`|`o`|`p`|`q`|`r`|`s`|`t`|`u`|`v`|`w`|`x`|`y`|`z`|`_`|
|`6`|`£`|`$`|`:`|`;`|`<`|`>`|`⌃`|`⌄`|`@`|`%`|`-`|`+`|`*`|`÷`|`?`|`!`|
|`7`|`{`|`}`|`[`|`]`|`(`|`)`|`“`|`”`|`‘`|`’`|`.`|`=`|`,`|`/`|`\`|`█`|