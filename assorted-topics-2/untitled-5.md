# Text Manipulation

## Overview

Some examples of using strings, grep, egrep, cut, etc. which I have and/or use often.

## misc stuff

Combine files and Number lines: nl text.txt test.txt &gt; newfile cat newfile

Format sentances so one space is between words: fmt -u myfeil.txt

## grep and egrep examples

Look for multiple grep terms:

```text
grep 'word1|word2|word3'
grep -E 'word1|word2|word3'
```

Exclude from grep:

```text
grep -Ev 'word1|word2|word3'
```

To check whether account lockout is in use:

```text
grep tally /etc/pam.d/ *grep tally /etc/pam.conf
```

Grep/fgrep/egrep:

```text
grep ^hello testfile
```

Count lines that match hello:

```text
grep -c ^hello testfile
```

Count lines that end in hello:

```text
grep -c hello$ testfile
```

Return lines that end in hello:

```text
grep hello$ testfile
```

Return all lines that have a given pattern and those that start with it:

```text
grep [ap0r] testf
grep ^[ap0r] testf
```

Look for files that contain specific content: in a directory

```text
grep -lr cron /etc
```

Below example displays username and home directory of users who has the login shell as “/bin/bash”.

```text
grep "/bin/bash" /etc/passwd | cut -d':' -f1,6
```

In this example, we are selecting field 1 through 4, 6 and 7

```text
grep "/bin/bash" /etc/passwd | cut -d':' -f1-4,6,7
```

The following example displays all the fields from /etc/passwd file except field 7

```text
grep "/bin/bash" /etc/passwd | cut -d':' --complement -s -f7
```

To change the output delimiter use the option –output-delimiter as shown below. In this example, the input delimiter is : \(colon\), but the output delimiter is \# \(hash\).

```text
grep "/bin/bash" /etc/passwd | cut -d':' -s -f1,6,7 --output-delimiter='#'
```

Look for alerts:

```text
egrep -i 'yandex|mail.ru' /var/log/snort/alert | grep –i detected | sort | uniq -c
strings /pcaps/conduit.pcap | grep -i User-Agent | sort -u
```

Ignore case and search for any lines with hello.world in it;:

```text
egrep -i 'hello.world*' test
```

Look for lines with hello or world - multiple patterns:

```text
egrep -i 'hello|world' test
```

Look for lines with do not contain hello or world - multiple patterns:

```text
egrep -vi 'hello|world' test
```

Look for lines that contain hello or world, but not those whci include jeff:

```text
egrep 'hello|world' testfile|grep -v jeff
```

List all the lines in myletter.txt containing the word the insensitive of case:

```text
egrep -i '\' myletter.txt
```

Look for 3 diff files in a directory in a case insensitive manner:

```text
egrep -i 'apple|banana|orange' ~/dir
```

Ignore the following case insensitive words in a search in linux.txt file:

```text
egrep -vi '"http"|"https"|"dns"|"internet explorer"' linux.txt
```

## Simple sed

Search and replace with sed:

```text
sed 's/parttime/fulltime/' team
```

sed - search and replace and export to file:

```text
sed 's/parttime/fulltime/w promotions.txt' team
```

## Tee Examples

Show output from redirect:

```text
ls -hal |tee ls_output
```

Show output from redirect and append to current file:

```text
ls -hal |tee -a ls_output
```

## Strings:

Display printable lines with a minimum of 10 characters searching for the word cookie or password and output to a file:

```text
strings -n 10 test.pcap |egrep -i "cookie|password" > goodies.txt
```

Same as above but print the text 2 lines above and below:

```text
strings -n 10 test.pcap |egrep -A 2 -B 2 -i "cookie|password" > goodies.txt*
```

Looks for strings 14 characters in length in the specified file:

```text
strings -n 14 trojan2 | more
```

Search for possible exe downloads:

```text
strings test.pcap |egrep -A 3 -B 3 -i "application/octet-stream” > evil-stuff.txt
```

Sort User Agents based on length:

```text
strings *.pcap | grep ^User-Agent: | sort -u | awk '{print length, $0;}' | sort -rn
strings *.pcap | grep -i 'agent' | sort | cut -d ' ' -f 2 | sort | uniq -c | sort -n
```

## Cut

The following example extracts first 3 characters of each line from a file called test.txt:

```text
cut -c1-3 test.txt
```

The following specifies only the start position before the ‘-‘. This example extracts from 3rd character to end of each line from test.txt file.

```text
cut -c3- test.txt
```

The following specifies only the end position after the ‘-‘. This example extracts 8 characters from the beginning of each line from test.txt file.

```text
cut -c-8 test.txt
```

The following example displays only first field of each lines from /etc/passwd file using the field delimiter : \(colon\). In this case, the 1st field is the username.

```text
cut -d':' -f1 /etc/passwd
```

Use cut to print all content before a certain character and then all content after a character:

```text
cut -d ";" -f1 text.txt
cut -d ";" -f2 text.txt
```

