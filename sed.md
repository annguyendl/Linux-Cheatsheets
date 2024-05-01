[TOC]

## Basic `sed` usage

| Description   | Command                              |
| ------------- | ------------------------------------ |
| Syntax        | `sed [options] command [input-file]` |
| With pipeline | `echo '123abc' | sed 's/[0-9]*//g'`  |

### Options

| Option | Example                                     | Description                                                  |
| ------ | ------------------------------------------- | ------------------------------------------------------------ |
| -i     | `sed -i.bak 's/On/Off/' input.txt`          | Backup input file (`php.ini.bak`) and modify input file directly<br />If obmit `.bak` then sed won't create backup file. |
| -E     | `sed -E 's/[0-9]+//g' input.txt`            | Use extended regular expressions                             |
| -n     | `sed -n '3 p' input.txt`                    | Suppress default pattern space printing.                     |
| -f     | `sed -f script.sed input.txt`               | Execute sed script file (`script.sed`)                       |
| -e     | `sed -e 'command1' -e 'command2' input.txt` | Execute multiple sed commands                                |
|        | `sed -e '/pattern/command' input.txt`       |                                                              |

- Refer "input.txt" sample at the end of Cheetsheat.

### Commands

| Command | Example                                  | Description                                                  |
| ------- | ---------------------------------------- | ------------------------------------------------------------ |
| p       | `sed -n '1,4 p' input.txt`               | Print lines 1-4                                              |
| p       | `sed -n -e '1,4 p' -e '6,7 p' input.txt` | Print lines 1-4 and 6-7                                      |
| d       | `sed '1,4 d' input.txt`                  | Print lines except 1-4                                       |
| w       | `sed -n '1,4 w output.txt' input.txt`    | Write lines 1-4 to "output.txt" file                         |
| a       | `sed '2 a new-line' input.txt`           | Append "new-line" after line 2 (new-line becomes line 3)     |
|         | `sed '$a new-line' input.txt`            | Append "new-line" after end of file                          |
|         | `sed '3~3a new-line' file.txt`           | Append "new-line" after every 3rd line starting from line 3  |
| i       | `sed '2 i new-line' input.txt`           | Insert "new-line" before line 2 (new-line becomes line 2)    |
| s       | `sed 's/old/new/[flags]' input.txt`      | Search and replace                                           |
| q       | `sed '21q;15,20p' input.txt`             | Print line Nos. 15-20 of a file then quit after line 21 (faster than `sed '15,20p' input.txt`) |

### Flags

| Flag   | Example                                                      | Description                                                  |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| g      | `sed 's/line/row/g' input.txt`                               | Global substitution<br />Replace all occurrences of a string |
| 1,2... | `sed 's/line/row/2' input.txt`                               | Substitute the nth occurrence.<br />E.g: Replace only the 2nd occurrence of a string |
| p      | `sed -n '/India/p' input.txt`<br />`sed -n '/India/!p' input.txt` | Print only the substituted line<br />E.g.: Print all line has "India"<br />E.g.: Print all line doesn't have "India" |
| w      | `sed -n 's/line/row/w output.txt' input.txt`                 | Write only the substituted line to a file                    |
| I      | `sed -n '/india/Ip' input.txt`                               | Ignore case while searching                                  |
| e      | `sed 's/##//e' input.txt`                                    | Substitute and execute in the command line                   |

### RegExp:

|       | Example                                                 | Description                                                  |
| ----- | ------------------------------------------------------- | ------------------------------------------------------------ |
| ^     | `sed -e '/^#/ p' input.txt`                             | Start with                                                   |
| $     | `sed 's/#.*$//' input.txt`<br />`sed '/^$/d' input.txt` | End of line.<br />E.g.: Remove all comment line although the comment (#) at the end of line.<br />E.g.: Remove all empty lines |
| [0-9] | `sed -e 's/[0-9]*/(&)/g' input.txt`                     | [0-9]: Numeric<br />*: repeat count, means any number of digits<br />&: corresponds to the pattern found<br />E.g.: surround "hit" pattern with () |
| \s    | `sed 's/^\s*//' input.txt`                              | Remove all whitespace from beginning of each line            |
|       |                                                         |                                                              |

## Advance `sed` usage:

### Line numbering:

| Example                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sed = input.txt \| sed 'N;s/\n/\t/'`                        | Number line of a file (simple left alignment)                |
| `sed = input.txt \| sed 'N; s/^/     /; s/ *\(.\{6,\}\)\n/\1  /'` | Print line number (number on left, right-aligned)            |
| `sed '/./=' input.txt \| sed '/./N; s/\n/ /'`                | Print line number and content in same line for not blank line only |
| `sed -n '$=' input.txt`                                      | Count number of line                                         |

### Text Conversion and Substitution:

| Example                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sed 's/^[ \t]*//' input.txt`                                | Delete leading whitespace (spaces, tabs) from front of each line aligns all text flush left |
| `sed 's/[ \t]*$//' input.txt`                                | Delete trailing whitespace (spaces, tabs) from end of each line |
| `sed 's/^[ \t]*//;s/[ \t]*$//' input.txt`                    | Delete BOTH leading and trailing whitespace from each line   |
| `sed 's/^/     /' input.txt`                                 | Insert 5 blank spaces at beginning of each line (make page offset) |
| `sed -e :a -e 's/^.\{1,78\}$/ &/;ta' input.txt`              | Align all text flush right on a 79-column width              |
| `sed  -e :a -e 's/^.\{1,77\}$/ & /;ta' input.txt`            | Center all text in the middle of 79-column width.            |
| `sed  -e :a -e 's/^.\{1,77\}$/ &/;ta' -e 's/\( *\)\1/\1/' input.txt` | Center all text in the middle of 79-column width. spaces at the beginning of the line are discarded in centering the line, and no trailing spaces appear at the end of lines. |
| `sed '/baz/s/foo/bar/g' input.txt`                           | substitute "foo" with "bar" ONLY for lines which contain "baz" |
| `sed '/baz/!s/foo/bar/g' input.txt`                          | substitute "foo" with "bar" EXCEPT for lines which contain "baz" |
| `sed 's/scarlet/red/g;s/ruby/red/g;s/puce/red/g' input.txt`  | change "scarlet" or "ruby" or "puce" to "red"                |
| `sed '1!G;h;$!d' input.txt`<br />`sed -n '1!G;h;$p' input.txt` | Reverse order of lines                                       |
| `sed '/\n/!G;s/\(.\)\(.*\n\)/&\2\1/;//D;s/.//' input.txt`    | Reverse each character on the line                           |
| `sed '$!N;s/\n/ /' input.txt`                                | Join pairs of lines side-by-side                             |
| `sed -e :a -e '/\\$/N; s/\\\n//; ta' input.txt`              | if a line ends with a backslash, append the next line to it  |
| `sed -e :a -e '$!N;s/\n=/ /;ta' -e 'P;D' input.txt`          | if a line begins with an equal sign, append it to the previous line and replace the "=" with a single space |
| `sed -e :a -e 's/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/;ta' input.txt` | add commas to numeric strings, changing "1234567" to "1,234,567" |
| `sed 'n;n;n;n;G;' input.txt`                                 | add a blank line every 5 lines (after lines 5, 10, 15, 20, etc.) |

### Selective Printing of certain lines:

| Example                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sed 10q input.txt`                                          | print first 10 lines of file (emulates behavior of "head")   |
| `sed -e :a -e '$q;N;11,$D;ba' input.txt`                     | print the last 10 lines of a file (emulates "tail")          |
| `sed '$!N;$!D' input.txt`                                    | print the last 2 lines of a file (emulates "tail -2")        |
| `sed '$!d' input.txt`<br />`sed -n '$p' input.txt`           | print the last line of a file (emulates "tail -1")           |
| `sed -e '$!{h;d;}' -e x input.txt`                           | print the next-to-the-last line of a file, for 1-line files, print blank line |
| `sed -e '1{$q;}' -e '$!{h;d;}' -e x input.txt`               | print the next-to-the-last line of a file, for 1-line files, print the line |
| `sed -e '1{$d;}' -e '$!{h;d;}' -e x input.txt`               | print the next-to-the-last line of a file, for 1-line files, print nothing |
| `sed -n '/regexp/p' input.txt`                               | print only lines which match regular expression (emulates "grep") |
| `sed -n '/regexp/!p' input.txt`                              | print only lines which do NOT match regexp (emulates "grep -v") |
| `sed -n '/regexp/{g;1!p;};h' input.txt`                      | print the line immediately before a regexp, but not the line containing the regexp |
| `sed -n '/regexp/{n;p;}' input.txt`                          | print the line immediately after a regexp, but not the line containing the regexp |
| `sed -n -e '/regexp/{=;x;1!p;g;$!N;p;D;}' -e h input.txt`    | print 1 line of context before and after regexp, with line number indicating where the regexp occurred (similar to "grep -A1 -B1") |
| `sed '/foo/!d; /bar/!d; /baz/!d' input.txt`                  | grep for "foo" and "bar" and "baz" (in any order)            |
| `sed '/baz.*foo.*bar/!d' input.txt`                          | grep for "foo" and "bar" and "baz" (in that order)           |
| `sed -e '/baz/b' -e '/foo/b' -e '/bar/b' -e d input.txt`     | grep for "foo" or "bar" or "baz" (emulates "egrep")          |
| `sed -n '/^.\{65\}/p' input.txt`                             | print only lines of 65 characters or longer                  |
| `sed -n '/^.\{65\}/!p' input.txt`<br />`sed '/^.\{65\}/d' input.txt` | print only lines of less than 65 characters                  |
| `sed -n '/regexp/,$p' input.txt`                             | print section of file from regular expression to end of file |

### Selective deletion of certain lines:

| Example                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sed '/Iowa/,/Montana/d' input.txt`                          | print all of file EXCEPT section between 2 regular expressions |
| `sed '$!N; /^\(.*\)\n\1$/!P; D' input.txt`                   | delete duplicate, consecutive lines from a file (emulates "uniq"). First line in a set of duplicate lines is kept, rest are deleted. |
| `sed -n 'G; s/\n/&&/; /^\([ -~]*\n\).*\n\1/d; s/\n//; h; P' input.txt` | delete duplicate, nonconsecutive lines from a file. Beware not to overflow the buffer size of the hold space, or else use GNU sed. |
| `sed '$!N; s/^\(.*\)\n\1$/\1/; t; D' input.txt`              | delete all lines except duplicate lines (emulates "uniq -d"). |
| `sed '1,10d' input.txt`                                      | delete the first 10 lines of a file                          |
| `sed '$d' input.txt`                                         | delete the last line of a file                               |
| `sed 'N;$!P;$!D;$d' input.txt`                               | delete the last 2 lines of a file                            |
| `sed -e :a -e '$d;N;2,10ba' -e 'P;D' input.txt`<br />`sed -n -e :a -e '1,10!{P;N;D;};N;ba' input.txt` | delete the last 10 lines of a file                           |
| `sed 'n;n;n;n;n;n;n;d;' input.txt`                           | delete every 8th line                                        |
| `sed '/^$/d' input.txt`                                      | delete ALL blank lines from a file (same as "grep '.' ")     |
| `sed '/./,/^$/!d' input.txt`<br />`sed '/^$/N;/\n$/D' input.txt` | delete all CONSECUTIVE blank lines from file except the first; also deletes all blank lines from top and end of file (emulates "cat -s") |
| `sed '/./,$!d' input.txt`                                    | delete all leading blank lines at top of file                |
| `sed -e :a -e '/^\n*$/{$d;N;ba' -e '}' input.txt`            | delete all trailing blank lines at end of file               |
| `sed -n '/^$/{p;h;};/./{x;/./p;}' input.txt`                 | delete the last line of each paragraph                       |



### Special Applications:

| Example                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `sed '/^$/q' input.txt`                                      | deletes everything after first blank line                    |
| `sed '1,/^$/d'  input.txt`                                   | deletes everything up to first blank line                    |
| `sed -e :a -e 's/<[^>]*>//g;/</N;//ba' input.txt`            | remove most HTML tags (accommodates multiple-line tags)      |
| `sed '/./{H;d;};x;s/\n/={NL}=/g' input.txt \| sort \| sed '1s/={NL}=//;s/={NL}=/\n/g' input.txt` | sort paragraphs of file alphabetically. Paragraphs are separated by blank lines. |
| `sed '/bar\|foo\|baz/b;d' input.txt`<br />`sed -e '/foo/b' -e '/bar/b' -e '/baz/b' -e d input.txt` | search for "bar", "foo", "baz" in any order                  |



### Sample "input.txt"

```tex
Questioning is the beginning of intelligence. -- Letsfindcourse
-------------------
Output Result #Comment line 1
#Comment line 2
Walden, Henry David Thoreau
The Discovery Of India, Jawaharlal Nehru
The Algebra of Infinite Justice, Arundhati Roy
Bookless in Baghdad, Shashi Tharoor
Adventures of Sherlock Holmes, Sir Arthur Conan Doyle
-------------------
Alabama	AL	Ala.	Ala.		01
Alaska	AK	Alaska	Alaska	Alas.	02
Arizona	AZ	Ariz.	Ariz.		04
Arkansas	AR	Ark.	Ark.	A.R.	05
California	CA	Calif.	Calif.	Cal., CF (USCG)	06
Colorado	CO	Colo.	Colo.	Col., CL (USCG)	08
Connecticut	CT	Conn.	Conn.		09
Delaware	DE	Del.	Del.	DL (USCG)	10
Washington D.C.	DC	D.C.	D.C.	(Đặc khu liên bang)	11
Florida	FL	Fla.	Fla.	Flor.	12
Georgia	GA	Ga.	Ga.		13
Hawaii	HI	Hawaii	Hawaii	H.I., HA (USCG)	15
Idaho	ID	Idaho	Idaho	Id. or Ida.	16
Illinois	IL	Ill.	Ill.	Ills.	17
Indiana	IN	Ind.	Ind.		18
Iowa	IA	Iowa	Iowa	Ia.	19
Kansas	KS	Kans.	Kan.	KA (USCG)	20
Kentucky	KY	Ky.	Ky.	Ken. or Kent.	21
Louisiana	LA	La.	La.		22
Maine	ME	Maine	Maine	Me.	23
Maryland	MD	Md.	Md.		24
Massachusetts	MA	Mass.	Mass.	MS (USCG)	25
Michigan	MI	Mich.	Mich.	MC (USCG)	26
Minnesota	MN	Minn.	Minn.		27
Mississippi	MS	Miss.	Miss.	MI (USCG)	28
Missouri	MO	Mo.	Mo.		29
Montana	MT	Mont.	Mont.		30
Nebraska	NE	Nebr.	Neb.	NB (USCG)	31
Nevada	NV	Nev.	Nev.		32
New Hampshire	NH	N.H.	N.H.		33
New Jersey	NJ	N.J.	N.J.		34
New Mexico	NM	N. Mex.	N.M.	New M.	35
New York	NY	N.Y.	N.Y.	N. York	36
North Carolina	NC	N.C.	N.C.	N. Car.	37
North Dakota	ND	N. Dak.	N.D.		38
Ohio	OH	Ohio	Ohio	O.	39
Oklahoma	OK	Okla.	Okla.		40
Oregon	OR	Oreg.	Ore.		41
Pennsylvania	PA	Pa.	Pa.	Penn. hay Penna.	42
Rhode Island	RI	R.I.	R.I.		44
South Carolina	SC	S.C.	S.C.	S. Car.	45
South Dakota	SD	S. Dak.	S.D.		46
Tennessee	TN	Tenn.	Tenn.		47
Texas	TX	Tex.	Texas		48
Utah	UT	Utah	Utah		49
Vermont	VT	Vt.	Vt.		50
Virginia	VA	Va.	Va.		51
Washington	WA	Wash.	Wash.	Wn., WN (USCG)	53
Tây Virgina	WV	W. Va.	W.Va.	W.V.	54
Wisconsin	WI	Wis.	Wis.	Wisc., WS (USCG)	55
Wyoming	WY	Wyo.	Wyo.		56
-------------------
This is the line has "baz", "bar", "foo".
This is the line has "baz", "foo", "bar" in order.
This is the line has "bar" and "foo" only.
This is the line has "scarlet" or "ruby" or "puce"
----regexp------------
sudo podman run -it --name testppl -p 8080:8080 \
  -e JAVA_OPTS="" \
  -e DB_URL="jdbc:" \
  -e DB_PASSWORD="Plaintext Password" \
  -e JWT_TOKEN="Plaintext Token" \
  localhost\baseimage.v1
------------------
This is the line
=was splitted to
=multi lines
=by using "=" character.
-----------------
123456789
12345678910 
-----------------

--AFTER BLANK LINE--
<!DOCTYPE html>
<html>
<body>

<h1>My First Heading</h1>
<p>My first paragraph.</p>

</body>
</html>
-----------------
Duplicate Line
Duplicate Line
Duplicate Line
-----------------
##echo "Running command line"
##ls -ltr .
```



- Refer: https://gist.github.com/r2k0/1152840
