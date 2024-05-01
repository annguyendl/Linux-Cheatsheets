# Basic `less` usage

## Syntax:

```sh
less [options] filename
echo "123456" | less [option]
```



## Options:

| Option | Description                                                  |
| ------ | ------------------------------------------------------------ |
| -g     | Only the current match is highlighted when searching.        |
| -i     | Searches are case-sensitive.                                 |
| -m     | Displays the enhanced prompt, showing the file's position.   |
| -N     | Displays the line numbers, idea for source code.             |
| -S     | Turns off line wrapping. Long lines can be horizontally scrolls. |
| -X     | Keeps the file's contents visible after exiting less         |
| +F     | Activates following mode, which is useful for monitoring log updates |



Common Commands of `less`:

| Command     | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| Space bar   | Advances to the next pages                                   |
| d           | Moves to the half-next page                                  |
| b           | Returns to the previous page                                 |
| u           | Goes back a half page                                        |
| v           | Opens the file for editing                                   |
| j or Enter  | Moves down one line                                          |
| k           | Moves up one line                                            |
| Home        | Jumps to start of file                                       |
| End         | Jumps to end of file                                         |
| F           | Follow mode for logs; interrupt to stop                      |
| g or <      | Goes to the first line                                       |
| G or >      | Jumps to the last line                                       |
| <n>+G       | Moves to the line number <n>                                 |
| /<text>     | Searches forward for <text>, interpret as regexp             |
| ?<text>     | Searches backward for <text>, interpret as regexp            |
| n           | Finds the next match of the search.                          |
| N           | Finds the previous match of the search.                      |
| Esc+u       | Disables match highlight                                     |
| -+<c>       | Toggles option <c>, for example -+i for case-sensitive searches |
| m+<c>       | Sets a bookmark at mark <c>                                  |
| '+<c>       | Moves to bookmark <c>                                        |
| = or Ctrl+G | Displays file information                                    |
| :n          | Opens next file                                              |
| :p          | Opens previous file                                          |
| h           | Help                                                         |
| q           | Quit                                                         |

