[TOC]

# Basic `awk` usage:

### Syntax:

```sh
awk [option] '/pattern/ {program_actions}' input.txt
awk -F: '{print $1, $NF}' /etc/passwd
```

### Options:

| Option                                   | Example                      | Description                                  |
| ---------------------------------------- | ---------------------------- | -------------------------------------------- |
| -f program-file<br />--file program-file | awk -f scripts.awk input.txt | Use action scripts in file                   |
| -F fs<br />--field-sepearator fs         | awk -F :  input.txt          | Define field separator (default FS is Space) |
| -v var=val<br />--assign var=val         | awk -v DEF_MAX=100 input.txt | Set external variable with value             |

# `awk` Patterns

| Kind of pattern            | Example                                       | Description                                                  |
| -------------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| expression                 | `awk '$1 == "foo" { print $2 }'  input.txt`   | A single expression. It matches when its value is non-zero (if a number) or non-null (if a string). |
| begin-pattern, end-pattern |                                               | A pair of patterns separated by a comma, specifying a range of records. The range includes both the initial record that matches begin-pattern, and the final record that matches begin-pattern. |
| BEGIN<br />END             |                                               | Special patterns for you to supply start-up or clean-up actions for your `awk` program. |
| *empty*                    |                                               | The empty pattern matches every input record.                |
| /regexp/                   | `awk '/foo|bar|baz/  { printf $0}' input.txt` | A regular expression as a pattern. It matches when the text of the input record fits the regular expression. |



| Program Structure                                            | Example                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| BEGIN          {<initializations>}<br />    <pattern 1> {<program actions>}<br />    <pattern 2> {<program actions>}<br />    ...<br /> END            {< final actions >} | awk '<br />    BEGIN { print "\n>>>Start" }<br />    !/(login \| shutdown)/ { print NR, $0 }<br />    END { print "<<<END\n" } '<br /> /etc/passwd |



## `awk` Variables:

| Built-in Variable | Example                                                      | Description                                                  |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| $0                |                                                              | Whole line                                                   |
| $1, $2...$NF      |                                                              | First, second… last field                                    |
| NR                |                                                              | The total `N`umber of input `R`ecords                        |
| NF                |                                                              | `N`umber of `F`ields                                         |
| OFS               |                                                              | `O`utput `F`ield `S`eparator<br/>*(default " ")*             |
| FS                |                                                              | Input `F`ield `S`eparator<br/>*(default " ")*                |
| ORS               |                                                              | `O`utput `R`ecord `S`eparator<br/>*(default "\n")*           |
| RS                |                                                              | Input `R`ecord `S`eparator<br/>*(default "\n")*              |
| RT                |                                                              | The record terminator. Gawk sets RT to the input text that matched the character or regular expression specified by RS. |
| FNR               |                                                              | `F`ile `N`umber of `R`ecords. Contains number of lines read, but is reset for each file read. |
| OFMT              |                                                              | The default output format for numbers.<br/>*(default "%.6g")* |
| CONVFMT           |                                                              | The CONVFMT variable is used to specify the format when converting a number to a string. (*Default: "%.6g"*) |
| FILENAME          | `awk '/foo|bar|baz/  { printf $0}<br />END {print "filename=", FILENAME}' input.txt` | The name of the current input file. If no files are specified on the command line, the value of FILENAME is "-". However, FILENAME is undefined inside the BEGIN block (unless set by getline). |
| ARGC              |                                                              | Number or arguments                                          |
| ARGV              |                                                              | Array of arguments                                           |
| ARGIND            |                                                              | Argument Index                                               |
| BINMODE           |                                                              | On non-POSIX systems, specifies use of "binary" mode for all file I/O. Numeric values of 1, 2, or 3, specify that input files, output files, or all files, respectively,  should use binary I/O. String values of  "r", or "w" specify  that input files, or output files, respectively, should use binary I/O. String values of "rw" or "wr" specify that all files should use binary I/O. Any other string value is treated as "rw", but generates a warning message. |
| ENVIRON           | `awk 'BEGIN { print ENVIRON["JAVA_HOME"] }' </dev/null`      | An array containing the values of the current environment.   |
| ERRNO             |                                                              | If a system error occurs either doing a redirection for getline, during a read for getline, or during a close(), then ERRNO will contain a string describing the error. The value is subject to translation in non-English locales. |
| FIELDWIDTHS       |                                                              | A white-space separated list of fieldwidths. When set, gawk parses the input into fields of fixed width, instead of using the value of the FS variable as the field separator. |
| IGNORECASE        |                                                              | Controls the case-sensitivity of all regular expression and string operations. If IGNORECASE has a non-zero value, then string comparisons and pattern matching in rules, field splitting with FS, record separating with RS, regular expression matching with ~ and !~, and the gensub(), gsub(), index(), match(), split(), and sub() built-in functions all ignore case when doing regular expression operations.<br />NOTE: Array subscripting is not affected. However, the asort() and asorti() functions are affected |
| LINT              |                                                              | Provides dynamic control of the --lint option from within an AWK program. When true, gawk prints lint warnings. |
| RSTART            |                                                              | Location in the string. The index of the first character matched by match(); 0 if no match. |
| RLENGTH           |                                                              | Length of match. The length of the string matched by match(); -1 if no match. |
| SUBSEP            |                                                              | Multi-dimensional array separator. The character used to separate multiple subscripts in array elements.<br/>*(default "\034" : non-printable character, dec: 28, hex: 1C)* |
| TEXTDOMAIN        |                                                              | The text domain of the AWK program; used to find the localized translations for the program's strings. |

#### Defining variable

```sh
awk -v var1="Hello" -v var2="World !" \
    'END {print var1, var2}' </dev/null
```

#### Use shell variable

```sh
awk -v varPwd="$PWD" 'END {print varPwd}' </dev/null
```

## `awk` Operators

| Operator                  | Example                                                      | Description           |
| ------------------------- | ------------------------------------------------------------ | --------------------- |
| `==`                      | $2 == "foo"                                                  | Equals                |
| `!=`                      | $2 != "foo"                                                  | Not equals            |
| in                        | awk 'BEGIN { <br />    assoc["foo"] = "bar";<br />    assoc["bar"] = "baz";<br />    if ("foo" in assoc)<br />        print "Fooey!";<br /> }' | in array              |
| `+` `-` * `/` % `++` `--` |                                                              | Arithmetic operations |
| `+=` `-=` `*=` `/=` %=    |                                                              | Shorthand assignments |
| `<` `<=` `>` `>=`         |                                                              | Comparison operators  |
| `||`  `&&`                | `($2 <= 4 || $3 < 20)`<br />`($1 == 4 && $3 < 20)`           | OR<br />AND           |

## `awk` Regular Expressions:

### Operators

| Operator | Example                                    | Description  |
| -------- | ------------------------------------------ | ------------ |
| ~        | awk -e '$1 ~ /p[el]/ {print $0}' input.txt | Matching     |
| !~       |                                            | Not matching |

### Anchors

| Anchor | Description                         |
| ------ | ----------------------------------- |
| **^**  | Indicates the beginning of the line |
| **$**  | Indicates the end of a line         |
| **\A** | Denotes the beginning of a string   |
| **\z** | Denotes the end of a string         |
| **\b** | Marks a word boundary               |

### Characters

| Character  | Function                                                     |
| ---------- | ------------------------------------------------------------ |
| **[ad]**   | Selects **a** or **d**                                       |
| **[a-d]**  | Selects any character **a** through **d** (a, b, c, or d)    |
| **[^a-d]** | Selects any character *except* **a** through **d** (e, f, g, h…) |
| **\w**     | Selects any word                                             |
| **\s**     | Selects any whitespace character                             |
| **\d**     | Selects any digit                                            |
|            | The capital versions of w, s, and d are negations:           |
| **\W**     |                                                              |
| **\S**     |                                                              |
| **\D**     |                                                              |

### POSIX mnemonic

| POSIX mnemonic | Function                                                     |
| -------------- | ------------------------------------------------------------ |
| **[:alnum:]**  | Alphanumeric characters                                      |
| **[:alpha:]**  | Alphabetic characters                                        |
| **[:space:]**  | Space characters (such as space, tab, and formfeed)          |
| **[:blank:]**  | Space and tab characters                                     |
| **[:upper:]**  | Uppercase alphabetic characters                              |
| **[:lower:]**  | Lowercase alphabetic characters                              |
| **[:digit:]**  | Numeric characters                                           |
| **[:xdigit:]** | Characters that are hexadecimal digits                       |
| **[:punct:]**  | Punctuation characters (i.e., characters that are not letters, digits, control characters, or space characters) |
| **[:cntrl:]**  | Control characters                                           |
| **[:graph:]**  | Characters that are both printable and visible (e.g., a space is printable but not visible, whereas an **a** is both) |
| **[:print:]**  | Printable characters (i.e., characters that are not control characters) |

### Quantifiers

| Quantifier | Function                                                   |
| ---------- | ---------------------------------------------------------- |
| **.**      | Matches any character                                      |
| **+**      | Modifies the preceding set to mean *one or more times*     |
| *****      | Modifies the preceding set to mean *zero or more times*    |
| **?**      | Modifies the preceding set to mean *zero or one time*      |
| **{n}**    | Modifies the preceding set to mean *exactly n times*       |
| **{n,}**   | Modifies the preceding set to mean *n or more times*       |
| **{n,m}**  | Modifies the preceding set to mean *between n and m times* |

### Grouped matches

| Quantifier | Function                                                     |
| ---------- | ------------------------------------------------------------ |
| **(red)**  | Parentheses indicate that the enclosed letters must appear contiguously |
| **\|**     | Means *or* in the context of a grouped match                 |

# `awk` Program actions:

| Program and syntax                                           | Example                                                      | Description                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------- |
| if (<condition>) {<program actions>}<br />else {<program actions>} | awk -v count=2 '<br />  BEGIN {<br />    if (count == 1)<br />        print "Yes";<br />    else<br />        print "Huh?";<br /> }' | if ... else ... condition        |
| switch (<variable>) {<br />  case /regexp/:<br />    <program actions><br />    break;<br />  default:<br />    <program actions><br />} | awk -F: '{<br />    switch (NR * 2 + 1) {<br />        case 3:<br />        case "11":<br />            print NR - 1<br />            break<br />         case /2[[:digit:]]+/:<br />            print NR<br />         default:<br />            print NR + 1<br />         case -1:<br />            print NR * -1<br />    } }' /etc/passwd | switch case condition            |
| for (i = <start>; i < <end>; i++) {<program actions>}        | awk 'BEGIN {<br/>    for (i = 0; i < 10; i++)<br/>        print "i=" i;<br/>}' | for ... i                        |
| for (item in <array> ) {<program actions>}                   | awk 'BEGIN {<br/>    assoc["key1"] = "val1"<br/>    assoc["key2"] = "val2"<br/>    for (key in assoc)<br/>        print assoc[key];<br/>}'<br /><br />awk 'BEGIN {<br/>    for (argnum in ARGV)<br/>        print ARGV[argnum];<br/>}' input.txt | for ... in                       |
| while (<condition>) {<program actions>}                      | awk 'BEGIN {<br/>    while (a < 10) {<br/>        print "- " " concatenation: " a<br/>        a++;<br/>    }<br/>}' | while ...                        |
| do {<program actions>} while (<condition>)                   | awk '{<br/>    i = 1<br/>    do {<br/>        print $0<br/>        i++<br/>    } while (i <= 5)<br/>}' /etc/passwd | do ... while                     |
| break                                                        |                                                              | `break` command using in loop    |
| continue                                                     |                                                              | `continue` command using in loop |

## `awk` I/O Functions

| Function                    | Example | Description                                                  |
| --------------------------- | ------- | ------------------------------------------------------------ |
| close(file [, how])         |         | Close file, pipe or co-process. The optional how should only be used when closing one end of a two-way pipe to a co-process. It must be a string value, either "to" or "from". |
| getline                     |         | Set $0 from next input record; set NF, NR, FNR. Returns 0 on EOF and –1 on an error. Upon an error, ERRNO contains a string describing the problem. |
| getline <file               |         | Set $0 from next record of file; set NF.                     |
| getline var                 |         | Set var from next input record; set NR, FNR.                 |
| getline var <file           |         | Set var from next record of file.                            |
| command \| getline [var]    |         | Run command piping the output either into $0 or var, as above. If using a pipe or co-process to getline, or from print or printf within a loop, you must use close() to create new instances |
| command \|& getline [var]   |         | Run command as a co-process piping the output either into $0 or var, as above.  Co-processes are a gawk extension. |
| next                        |         | Stop processing the current input record. The next input record is read and processing starts over with the first pattern in the AWK program. If the end of the input data is reached, the END block(s), if any, are executed. |
| nextfile                    |         | Stop processing the current input file. The next input record read comes from the next input file. FILENAME and ARGIND are updated, FNR is reset to 1, and processing starts over with the first pattern in the AWK program. If the end of the input data is reached, the END block(s), are executed. |
| print                       |         | Prints the current record. The output record is terminated with the value of the ORS variable. |
| print expr-list             |         | Prints expressions. Each expression is separated by the value of the OFS variable. The output record is terminated with the value of the ORS variable. |
| print expr-list >file       |         | Prints expressions on file. Each expression is separated by the value of the OFS variable. The output record is terminated with the value of the ORS variable. |
| printf fmt, expr-list       |         | Format and print.                                            |
| printf fmt, expr-list >file |         | Format and print on file.                                    |
| system(cmd-line)            |         | Execute the command cmd-line, and return the exit status.    |
| fflush([file])              |         | Flush any buffers associated with the open output file or pipe file. If file is missing, then stdout is flushed. If file is the null string, then all open output files and pipes have their buffers flushed. |
| print ... >> file           |         | Appends output to the file.                                  |
| print ... \| command        |         | Writes on a pipe.                                            |
| print ... \|& command       |         | Sends data to a co-process.                                  |

### `awk` Formatted Printing

#### Right align

```sh
awk 'BEGIN{printf "|%10s|\n", "hello"}'	
```

#### Left align

```sh
awk 'BEGIN{printf "|%-10s|\n", "hello"}'	
```

## `awk` Numeric Functions

| Function      | Example | Description                                                  |
| ------------- | ------- | ------------------------------------------------------------ |
| atan2(y, x)   |         | Returns the arctangent of y/x in radians.                    |
| cos(expr)     |         | Returns the cosine of expr, which is in radians.             |
| exp(expr)     |         | The exponential function. Hàm lũy thừa.                      |
| int(expr)     |         | Truncates to integer.                                        |
| log(expr)     |         | The natural logarithm function.                              |
| rand()        |         | Returns a random number N, between 0 and 1, such that 0 <= N < 1. |
| sin(expr)     |         | Returns the sine of expr, which is in radians.               |
| sqrt(expr)    |         | The square root function.                                    |
| srand([expr]) |         | Uses expr as a new seed for the random number generator. If no expr is provided, the time of day is used. The return value is the previous seed for the random number generator. |

## `awk` Bit Manipulation Functions

| Function           | Example | Description                                                 |
| ------------------ | ------- | ----------------------------------------------------------- |
| and(v1, v2)        |         | Return the bitwise AND of the values provided by v1 and v2. |
| compl(val)         |         | Return the bitwise complement of val.                       |
| lshift(val, count) |         | Return the value of val, shifted left by count bits.        |
| or(v1, v2)         |         | Return the bitwise OR of the values provided by v1 and v2.  |
| rshift(val, count) |         | Return the value of val, shifted right by count bits.       |
| xor(v1, v2)        |         | Return the bitwise XOR of the values provided by v1 and v2. |

## `awk` String Functions

| Function                | Example | Description                                                  |
| ----------------------- | ------- | ------------------------------------------------------------ |
| asort(s [, d])          |         | Returns the number of elements in the source array s.  The contents of s are sorted using  gawk's normal rules for comparing values, and the indexes of the sorted values of s are replaced with sequential integers starting with 1. If the optional destination array d is specified, then s is first duplicated into d, and then d is sorted, leaving the indexes of the source array s unchanged. |
| asorti(s [, d])         |         | Returns the number of elements in the source array s. The behavior is the same as that of asort(),  except that the array indices are used for sorting, not the array values. When done, the array is indexed numerically, and the values are those of the original indices. The original values are lost; thus provide a second array if you wish to preserve the original. |
| gensub(r, s, h [, t])   |         | Search the target string t for matches of the regular expression r.  If h is a string beginning with g or G, then replace all matches of r with s. Otherwise, h is a number indicating which match of r to replace. If t is not supplied, $0 is used instead. Within the replacement text s, the sequence \n, where n is a digit from 1 to 9, may be used to indicate just the text that matched the n'th parenthesized subexpression. The sequence \0 represents the entire matched text, as does the character &. Unlike sub() and gsub(), the modified string is returned as the result of the function, and the original target string is not changed. |
| gsub(r, s [, t])        |         | For each substring matching the regular expression r in the string t, substitute the string s, and return the number of  substitutions.  If t is not supplied, use $0. An & in the replacement text is replaced with the text that was actually matched. Use \\& to get a literal &.  (This must be typed as  "\\\\&") |
| index(s, t)             |         | Returns the index of the string t in the string s, or 0 if t is not present. (This implies that characterindices start at one.) |
| length([s])             |         | Returns the length of the string s, or the length of $0 if s is not supplied. |
| match(s, r [, a])       |         | Returns the position in s where the regular expression r occurs, or 0 if r is not present, and sets the values of RSTART and RLENGTH.<br />Note that the argument order is the same as for the ~ operator:  str  ~  re. If array a is provided, a is cleared and then elements 1 through n are filled with the portions of s that match the corresponding parenthesized subexpression in r.  The 0'th element of a contains the portion of s matched by the entire regular expression r. Subscripts a[n, "start"], and a[n, "length"] provide the starting index in the string and length respectively, of each matching substring. |
| split(s, a [, r])       |         | Splits the string s into the array a on the regular expression r, and returns the number of fields. If r is omitted, FS is used instead. The array a is cleared first. Splitting behaves identically to field splitting. |
| sprintf(fmt, expr-list) |         | Prints expr-list according to fmt, and returns the resulting string. |
| strtonum(str)           |         | Examines str, and returns its numeric value. If str begins with a leading 0, strtonum()  assumes that  str is an octal number. If str begins with a leading 0x or 0X, strtonum() assumes that str is a hexadecimal number. |
| sub(r, s [, t])         |         | Just like gsub(), but only the first matching substring is replaced. |
| substr(s, i [, n])      |         | Returns the at most n-character substring of s starting at i.  If n is omitted, the rest of s is used. |
| tolower(str)            |         | Returns a copy of the string str, with all the upper-case characters in str translated to their corresponding lower-case counterparts. Non-alphabetic characters are left unchanged. |
| toupper(str)            |         | Returns a copy of the string str, with all the lower-case characters in str translated to their corresponding upper-case counterparts. Non-alphabetic characters are left unchanged. |

### `awk` Arrays

#### Array with index

```sh
awk 'BEGIN {
    arr[0] = "foo";
    arr[1] = "bar";
    print(arr[0]); # => foo
    delete arr[0];
    print(arr[0]); # => ""
}'
```

#### Array with key

```sh
awk 'BEGIN {
    assoc["foo"] = "bar";
    assoc["bar"] = "baz";
    print("baz" in assoc); # => 0
    print("foo" in assoc); # => 1
}'
```

#### Array with split

```sh
awk 'BEGIN {
    split("foo:bar:baz", arr, ":");
    for (key in arr)
        print arr[key];
}'
```

#### Array with asort

```sh
awk 'BEGIN {
    arr[0] = 3
    arr[1] = 2
    arr[2] = 4
    n = asort(arr)
    for (i = 1; i <= n ; i++)
        print(arr[i])
}'
```

#### Multi-dimensional

```sh
awk 'BEGIN {
    multidim[0,0] = "foo";
    multidim[0,1] = "bar";
    multidim[1,0] = "baz";
    multidim[1,1] = "boo";
}'
```

#### Multi-dimensional iteration

```sh
awk 'BEGIN {
    array[1,2]=3;
    array[2,3]=5;
    for (comb in array) {
        split(comb,sep,SUBSEP);
        print sep[1], sep[2], 
        array[sep[1],sep[2]]
    }
}'
```

### Common specifiers

| Specifier | Description           |
| --------- | --------------------- |
| c         | Common specifiers     |
| d         | Decimal integer       |
| e, E, f   | Floating-point format |
| o         | Unsigned octal value  |
| s         | String                |
| %         | Literal %             |

### Escape Sequences

| Escape | Description     |
| ------ | --------------- |
| \b     | Backspace       |
| \f     | Form feed       |
| \n     | New line        |
| \r     | Carriage return |
| \t     | Horizontal tab  |
| \v     | Vertical tab    |

## `awk` Time Functions

| Function                        | Example | Description                                                  |
| ------------------------------- | ------- | ------------------------------------------------------------ |
| mktime(datespec)                |         | Turns datespec into a time stamp of the same form as returned by systime(). The datespec is a string of the form YYYY MM DD HH MM SS[ DST].<br />The contents of the string are six or seven numbers representing respectively the full year including century, the month from 1 to 12, the day of the month from 1 to 31, the hour of the day from 0 to 23, the minute from 0 to 59, and the second from 0 to 60, and an optional daylight saving flag. The values of these numbers need not be within the ranges specified; for example, an hour of -1 means 1 hour before midnight. The origin-zero Gregorian calendar is assumed, with year 0 preceding year 1 and year -1 preceding year 0. The time is assumed to be in the local timezone. If the daylight saving flag is positive, the time is assumed to be daylight saving time; if zero, the time is assumed to be standard time; and if negative (the default), mktime() attempts to determine whether daylight saving time is in effect for the specified time. If datespec does not contain enough elements or if the resulting time is out of range, mktime() returns -1. |
| strftime([format[, timestamp]]) |         | Formats timestamp according to the specification in format. The timestamp should be of the same form as returned by systime(). If timestamp is missing, the current time of day is used.If format is missing, a default format equivalent to the output of date(1) is used.  See the specification for the strftime() function in ANSI C for the format conversions that are guaranteed to be available. A public-domain version of strftime(3) and a man page for it come with gawk; if that version was used to build gawk, then all of the conversions described in that man page are available to gawk. |
| systime()                       |         | Returns the current time of day as the number of seconds since the Epoch (1970-01-01 00:00:00 UTC on POSIX systems). |

## `awk` Internationalization (I18N)  Functions

| Function                                                     | Example | Description                                                  |
| ------------------------------------------------------------ | ------- | ------------------------------------------------------------ |
| bindtextdomain(directory [, domain])                         |         | Specifies the directory where gawk looks for the .mo files. It returns the directory where domain is "bound.'' The default domain is the value of TEXTDOMAIN.  If directory is the null string (""), then bindtextdomain() returns the current binding for the given domain |
| dcgettext(string [, domain [, category]])                    |         | Returns the translation of string in text domain domain for locale category category. The default value for domain is the current value of TEXTDOMAIN. The default value for category is "LC_MESSAGES". If you supply a value for category, it must be a string equal to one of the known locale categories. You must also supply a text domain. Use TEXTDOMAIN if you want to use the current domain. |
| dcngettext(string1 , string2 , number [, domain [, category]]) |         | Returns the plural form used for number of the translation of string1 and string2 in text domain domain for locale category category. The default value for domain is the current value of TEXTDOMAIN. The default value for category is "LC_MESSAGES". If you supply a value for category, it must be a string equal to one of the known locale categories. You must also supply a text domain. Use TEXTDOMAIN if you want to use the current domain. |

## User defined function:

```sh
awk '
    # Returns minimum number
    function find_min(num1, num2){
       if (num1 < num2)
       return num1
       return num2
    }
    # Script execution starts here
    BEGIN {
       find_min(10, 60)
    }
'
```

# Advance `awk` usage

| Code                                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `awk '{sub(/^[ \t]+/, "")};1' input.txt`                     | Delete leading whitespace (spaces, tabs) from front of each line aligns all text flush left |
| `awk '{sub(/[ \t]+$/, "")};1' input.txt`                     | Delete trailing whitespace (spaces, tabs) from end of each line |
| `awk '{gsub(/^[ \t]+|[ \t]+$/,"")};1' input.txt`             | Delete BOTH leading and trailing whitespace from each line   |
| `awk '{printf "%79s\n", $0}' input.txt`                      | Align all text flush right on a 79-column width              |
| `awk '{l=length();s=int((79-l)/2); printf "%"(s+l)"s\n",$0}' input.txt` | Center all text on a 79-character width                      |
| `awk '{a[i++]=$0} END {for (j=i-1; j>=0;) print a[j--] }' input.txt` | Reverse order of lines (emulates "tac")                      |
| `awk '{for (i=NF; i>0; i--) printf("%s ",$i);print ""}' input.txt` | print in reverse order the fields of every line              |
| `awk 'ORS=NR%5?",":"\n"' input.txt`                          | concatenate every 5 lines of input, using a comma separator concatenate every 5 lines of input, using a comma separator |
| `awk '/regexp/{print x};{x=$0}' input.txt`<br />`awk '/regexp/{print (NR==1 ? "match on line 1" : x)};{x=$0}' input.txt` | print the line immediately before a regex, but not the line containing the regex |
| `awk '/regexp/{getline;print}' input.txt`                    | print the line immediately after a regex, but not the line containing the regex |
| `awk '/bar/ && /foo/ && /baz/' input.txt`                    | grep for "foo" and "bar" and "baz" (in any order on the same line) |
| `awk '/baz.*foo.*bar/' input.txt`                            | grep for "baz" and "foo" and "bar" (in that order)           |
| `awk 'length > 64' input.txt`                                | print only lines of 65 characters or longer                  |
| `awk 'length < 64' input.txt`                                | print only lines of less than 65 characters                  |
| `awk '/regexp/,0' input.txt`<br />`awk '/regexp/,EOF' input.txt` | print section of file from regular expression to end of file |
| `awk 'NR==8,NR==12' input.txt`                               | print section of file based on line numbers (lines 8-12, inclusive) |
| `awk 'NR==52 {print;exit}' input.txt`                        | print line number 52 and exit (more efficient on large files) |
| `awk '/Iowa/,/Montana/' input.txt`                           | print section of file between two regular expressions (inclusive), case sensitive |
| `awk NF input.txt`<br />`awk '/./' input.txt`                | delete ALL blank lines from a file (same as "grep '.' ")     |
| `awk 'a !~ $0; {a=$0}' input.txt`                            | remove duplicate, consecutive lines (emulates "uniq")        |
| `awk '!a[$0]++' input.txt`<br />`awk '!($0 in a){a[$0];print}' input.txt` | remove duplicate, nonconsecutive lines, most concise script<br />most efficient script |

## Special Applications:

| Example                                               | Description              |
| ----------------------------------------------------- | ------------------------ |
| `ss -lunt | awk 'NR>1{i=split($5,a,":");print a[i]}'` | List all listening ports |
|                                                       |                          |
|                                                       |                          |

# Sample "input.txt"

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



Reference:

- https://quickref.me/awk
- https://catonmat.net/ftp/awk.cheat.sheet.txt
- https://www.math.utah.edu/docs/info/gawk_9.html
- https://gist.github.com/Rafe/3102414
