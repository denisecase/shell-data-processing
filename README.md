# Shell Data Processing

> Getting and processing text with Bash and PowerShell

## Bash 

Fetching with Bash.

```Bash
curl "http://shakespeare.mit.edu/romeo_juliet/full.html"
curl "http://shakespeare.mit.edu/romeo_juliet/full.html" -O
curl "http://shakespeare.mit.edu/romeo_juliet/full.html" | sed 's/<\/*[^>]*>//g' > rj.txt
```

Finding matches with Bash.

```Bash
grep 'Romeo' full.html
grep 'ROMEO' full.html
grep 'Romeo' full.html -c
grep 'ROMEO' full.html -c
grep -i 'romeo' full.html
grep -i 'romeo' full.html -c

cat full.html
cat full.html | grep -i 'romeo' -c

cat rj.txt | grep -i 'romeo' -c
cat rj.txt | grep -i 'romeo'

grep -i 'ROMEO' rj.txt
grep -n 'ROMEO' rj.txt
grep -in 'ROMEO' rj.txt
grep -iv 'ROMEO' rj.txt

grep -i '^ROMEO' rj.txt
grep -i '^ROMEO$' rj.txt
grep -i '^JULIET$' rj.txt

grep -i '^ROMEO$' rj.txt -c
grep -i '^JULIET$' rj.txt -c

```

Processing text with Bash (sorting, counting)

```Bash
tr ' ' '\12' < rj.txt
tr ' ' '\12' < rj.txt | sort
tr ' ' '\12' < rj.txt | sort | uniq -c
tr ' ' '\12' < rj.txt | sort | uniq -c | sort -nr
tr ' ' '\12' < rj.txt | sort | uniq -c | sort -nr > result.txt
```

Previewing content with Bash (cat, head, tail)

```Bash
cat result.txt
head -10 result.txt
tail -4 result.txt
```

## Notes on processing web pages

Web pages are written in HTML. 

The page appears like this:

http://shakespeare.mit.edu/romeo_juliet/full.html

But when we view the source content it is actually this:

https://raw.githubusercontent.com/TheMITTech/shakespeare/master/romeo_juliet/full.html

HTML uses tags to mark up text - we want to remove the HTML tags and leave just the inner html. 

```html
<A NAME=1.0.1>Two households, both alike in dignity,</A><br>
```

## sed to get web text

The sed ('stream editor') command uses regular expressions. 

The first s means 'substitute' or replace and it uses slashes to separate the old (the string to be found and replaced) with the new (the replacement string): 

```Bash
sed 's/<\/*[^>]*>//g'
``` 

We want to find all HTML tags (both opening and closing tags) and delete them - that is, replace them with nothing. 

The final g means 'globally' - make this substitution everywhere it occurs. 

Removing the initial s and final g, we have:

/<\/*[^>]*>//

We want /old/new/ - but there are 4 slashes, not 3. The second slash is preceded by a \ - an escape symbol which means treat it literally. Knowing this, we see that the old strings (any HTML tags) can be found with:

<\/*[^>]*>

And the new string is empty. There is nothing provided for the new text, so we estentially delete every occurance of the old (found) text. 

Looking again, we see:

The first character is the opening angle bracket on any html tag. 

The last character is the ending angle bracket on any html tag. 

That leaves to help us find everything in between: \/*[^>]*

The asterisk quanitier means "any" - 0 or more of the preceeding character. 

So, we have < followed by zero or more slashes (denoting a closing tag) followed by zero or more of anything that is not a closing angle bracket. Very nice. 

## Fun Bash Commands

- curl
- tr (transform/replace strings)
- grep (read only)
- sed (stream editing)

## PowerShell

Fetching with PowerShell (7 and up). Redirect output to a new file name. 

No luck with sed, use match and replace.

```PowerShell
curl "http://shakespeare.mit.edu/romeo_juliet/full.html"
curl "http://shakespeare.mit.edu/romeo_juliet/full.html" > full2.html

clear

(Get-Content full2.html) -Replace "<\/*[^>]*>","" | Set-Content rj2.txt
(Get-Content full2.html) -Replace "<\/*[^>]*>","" > rj3.txt
```

Finding matches with PowerShell. Unlike Bash, insensitive to case is the default. Use the length property to count. 

```PowerShell
Select-String 'Romeo' full2.html
Select-String 'ROMEO' full2.html
Select-String -CaseSensitive 'Romeo' full2.html
Select-String -CaseSensitive 'ROMEO' full2.html
(Select-String -CaseSensitive 'Romeo' full2.html).length
(Select-String -CaseSensitive 'ROMEO' full2.html).length
(Select-String 'Romeo' full2.html).length

Select-String '^ROMEO' rj2.txt
Select-String '^ROMEO$' rj2.txt
Select-String '^JULIET$' rj2.txt

(Select-String '^ROMEO$' rj2.txt).length
(Select-String '^JULIET$' rj2.txt).length
```

Processing text with PowerShell (sorting, counting)

```PowerShell
(Get-Content rj2.txt) -Replace " ","`n"  > 01.txt
(Get-Content 01.txt) -Replace "[;,?'!\.\-\:]",""  > 02.txt
(Get-Content 02.txt).ToLower()   > 03.txt
(Get-Content 03.txt)  | Sort-Object > 04.txt
(Get-Content 04.txt)  | Group-Object | Select-Object Count, Name > 05.txt
(Get-Content 05.txt)  | Sort-Object -Descending > 06.txt
```

Previewing content with PowerShell (gc)

```PowerShell
Get-Content 06.txt
gc 06.txt -head 2
gc 06.txt -tail 2
```

## Just for Fun- Custom Play

1. Replace each occurance of Romeo with a friend's name. 
1. Replace each occurance of Juliet with another friend's name. 
1. Replace other locations, objects and publish your play. 

## Practice Scripting Word Count with Bash

1. Start with a different web page. 
1. Delete the HTML tags.
1. Clean the remaining text: remove punctuation
1. Clean the remaining text: convert to lowercase
1. Sort, group, and count.
1. Sort the count in descending order. 
1. Find the top 5 words. 

## Practice Scripting Word Count with PowerShell

1. Follow the steps above, using PowerShell instead.

##  Just for Fun- Two sets of reviews from IMDB

See files A.txt and B.txt. 

1. Using only your intellect and script commands, which file holds the positive comments?
1. Which file holds negative comments?
1. How did you process the data?  How confident are you? 

## References

- [StackOverflow Answer](https://stackoverflow.com/questions/35777319/extract-the-source-of-the-webpage-without-tags-using-bash) by [SLePort](https://stackoverflow.com/users/1344961/sleport)
- [PowerShell Sort-Object](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/sort-object)
