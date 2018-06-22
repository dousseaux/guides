# Linux Notes

Pedro Dousseau
July 13th, 2017

___
####1. Run scripts as super user

Add the command to `/etc/sudoers` file as below:

```
your_user ALL = NOPASSWD: /path/to/your/script1.sh
your_user ALL = NOPASSWD: /path/to/your/script2.sh
```

Will let you run `sudo ./script1.sh` without asking for password or being a super user.
___
####2. List processes

List all processes that are running python:

```
$ ps -ef | grep python
# You can specify the output format
# Ex.: User, PID, Excecution Time, Arguments
$ ps -eo uname,pid,etime,%cpu,%mem,args | grep python
```

List processes by PID
```
$ ps -f --pid 1567
```
___
####3. Print boundaries of a file

Print first 20 lines of a file:

```
$ head -20 file.txt
```

Print last 20 lines of a file:

```
$ tail -20 file.txt
```
___
####4. Run programs in background

This will the program in background and detach the process from the session, so when you exit it will keep running.

```
# stdout redirect to nohup.out file
$ nohup python script.py &
# stdin and stdout defined to null
$ nohup python script.py >/dev/null 2>&1 &
```
___
####5. Verify if script is running as root

```
if [ "$EUID" -ne 0 ]
	then echo "Please run as root"
	exit
fi

# OR...

if [[ $EUID -eq 0 ]]; then
	error "This script should not be run using sudo or as the root user"
	exit
fi
```
___
####6. Add SSH key

```
ssh-keygen -t rsa
```
___
####7. IPTables

__List rules__

```
$ iptables -L
# With line numbers
$ iptables -L -n --line-numbers
```

__Add rules__

```
# Add to the bottom of chain
$ iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# Add to the beggining of chain
$ iptables -I INPUT -p tcp --dport 22 -j ACCEPT
# Rule for specific source
$ iptables -I INPUT -p tcp -s IP.ADDRESS --dport 22 -j ACCEPT
```

Port and source are optional. Default is any. `-j` parameter can be a final state or a chain

Final parameters:

- ACCEPT means to let the packet through.
- DROP means to drop the packet on the floor, i.e. to discard it and not send any response
- REJECT is used to send back an error packet in response to the matched packet: otherwise it is equivalent to DROP so it is a terminating TARGET, ending rule traversal.
- QUEUE means to pass the packet to userspace.
- RETURN means stop traversing this chain and resume at the next rule in the previous (calling) chain. If the end of a built-in chain is reached or a rule in a built-in chain with target RETURN is matched, the target specified by the chain policy determines the fate of the packet.


__Add chain__

```
# Create a chain
$ iptables -N chain-name
# Add it to a default chain
$ iptables -I INPUT -j chain-name
```

__Remove chains__

```
# iptables -D chain-name line-number
$ iptables -D chain-name 2
```

__Save, Import and auto-load on boot__

```
# Save configuration to a file
$  iptables-save > /etc/iptables.conf
# Restore configurationm from file
$ iptables-restore  < /etc/iptables.conf
```

Add the restore command to `/etc/rc.local`, so it can be executed on boot.

___
####8. Egrep regex

The character ‘.’ matches any single character except newline.

| Char | Meaning |ssh-keygen -t rsa
| ---- | --------- |
| +|indicates that the regular expression should match one or more occurrences of the previous atom or regexp.|
|?|indicates that the regular expression should match zero or one occurrence of the previous atom or regexp. 
|\\+|matches a ‘+’ |
|\?|matches a ‘?’.|

Bracket expressions are used to match ranges of characters. Bracket expressions where the range is backward, for example ‘[z-a]’, are ignored. Within square brackets, ‘\’ is taken literally. Character classes are supported; for example ‘[[:digit:]]’ will match a single decimal digit. Non-matching lists ‘[^...]’ do not ever match newline.

GNU extensions are supported:

| Char | Meaning |
| ---- | --------- |
|\w|matches a character within a word|
|\W| matches a character which is not within a word|
|\<| matches the beginning of a word|
|\>| matches the end of a word|
|\b| matches a word boundary|
|\B| matches characters which are not a word boundary|
|\`| matches the beginning of the whole input|
|\'| matches the end of the whole input|

Grouping is performed with parentheses ‘()’. A backslash followed by a digit acts as a back-reference and matches the same thing as the previous grouped expression indicated by that number. For example ‘\2’ matches the second group expression. The order of group expressions is determined by the position of their opening parenthesis ‘(’.

The alternation operator is ‘|’.

The caracthers ^ and $ always represent the beginning and end of a string respectively, except within square brackets. Within brackets, can be used to invert the membership of the character class being specified.

The characters ‘*’, ‘+’ and ‘?’ are special anywhere in a regular expression.

The longest possible match is returned; this applies to the regular expression as a whole and (subject to this constraint) to subexpressions within groups.