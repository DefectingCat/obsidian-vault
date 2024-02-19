https://www.baeldung.com/linux/vim-search-replace

## Basic Search and Replace Using Slash and 

Let’s open the _sample.txt_ file using the Vim editor:

```bash
$ vim sample.txt
```

Inside Vim editor, we need to press the forward-slash(/) key, then search for the word _“article”_:

```bash
/article
```

This will highlight the first occurrence of the word _“article”_ and we can press the Enter key to jump to it.

We can then type in the _[cgn](https://vimtricks.com/p/go-to-next-match-and-select/)_ command combo:

```bash
cgn
```

**This Vim editor command finds the last thing we searched for, deletes it, and then put us into insert mode.**

## Search and Replace Using the substitute Command

One line 

```
:s/article/tutorial/g
```

All occurrences

```
:%s/article/tutorial/g
```

Case-Insensitive

```
:%s/vim/baeldung/gi
```

With confirmation

```
:%s/article/tutorial/gc
```

Within specific lines

```
:start_line_number, end_line_number s/<search_term>/<replace_term>/g
```