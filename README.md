# Bash Scripting

In order to start a script in bash we do:
```sh
#!/usr/bin/env  bash
printf "%s" "Hello World"
```
First rule, even if online we see plenty of examples with `echo`, we should  always use `printf` instead of the famous `echo` command.

## Good Rules for Bash Scripting

Here some good rules when writing Bash:

* Always prefer printf to echo
* Quote every variable, always, except when we want to compress spaces
* Don't expect speed in Bash scripts, but if you want to optimize prefer builtins instead of external prograsm
* Don't use cat but do this: `content=$(< fileName)`
* Always use the new test command [[ instead of the old test [
* Use "set +x" and "set -x" to enable/disable debugging mode
* Use "set -r" to set restricted mode, this can be useful for security reasons

## Variable Types

Although BASH is not a typed language, it does have a few different types of variables.
These types define the kind of content they have. They are stored in the variable's
attributes.
Attributes are settings for a variable. They define the way the variable will behave. Here
are the attributes you can assign to a variable:

* Array: declare -a variable: The variable is an array of strings.
* Associative array: declare -A variable: The variable is an associative array of strings (bash 4.0 or higher).
* Integer: declare -i variable: The variable holds an integer. Assigning values to this variable automatically triggers Arithmetic Evaluation.
* Read Only: declare -r variable: The variable can no longer be modified or unset.
* Export: declare -x variable: The variable is marked for export which means it will be inherited by any subshell or child process

echo "$BASH_VERSION" : Contains a string describing the version of BASH


## Control Structures

There are different control structures as in general programming languages:

### If Conrol Structure
```sh
if [[ condition ]]; then
    ...
fi
```
or:
```sh
if ((a<b)); then
    ...
fi
```
Example of more complex if statement:
```sh
if [[ condition ]]; then
    ...
elif [[ condition ]]; then
    ...
else
    ...
fi
```
### For Conrol Structure

```sh
for [[ condition ]]; do
    ...
done
```
```sh
for ((i=0;i<10;i++)); do
    ...
done
```
```sh
for my_var in {1..100}; do
    ...
done
```

The for loop can be useful to loop on files, but notice that we should not make use of the ls command (as seen in many scripts online), let's make an example:
```sh
for my_file in *.tar.gz; do
    tar -xvzf "$my_file";
done
```
To loop over the fields of an array we do:
my_array=(mam dad example another string)
```sh
for x in "${my_array[@]}"; do
    printf "$x";
done
```

### Ternary Operator

Bash supports ternary operators for variable assignments.

```sh
${varname:-word} # if varname exists and isn't null, return its value; otherwise return word
${varname:=word} # if varname exists and isn't null, return its value; otherwise set it word and then return its value
${varname:+word} # if varname exists and isn't null, return word; otherwise return null
${varname:offset:length} # performs substring expansion. It returns the substring of $varname starting at offset and up to length characters
```

### Case Control Structure
```sh
case $LANG in
    en*) echo 'Hello!' ;;
    fr*) echo 'Salut!' ;;
    de*) echo 'Guten Tag!' ;;
    nl*) echo 'Hallo!' ;;
    it*) echo 'Ciao!' ;;
    es*) echo 'Hola!' ;;
    C|POSIX) echo 'hello world' ;;
    *) echo 'I do not speak your language.' ;;
esac
```

let's see another example:
```sh
read ans

case "$ans" in
#List patterns for the conditions you want to meet
    [nN]) echo "NO";;
    [yY]) echo "YES";;
    *) echo "Wrong input";;
esac
```


### Bash Program General Structure

```sh
#!/usr/bin/env  bash
#
# AUTHORS, LICENSE AND DOCUMENTATION
#
set -e -o pipefail
# READONLY VARIABLES HERE
# GLOBAL VARIABLES HERE

# IMPORT EXTERNAL SOURCE CODE (DOTTING FILES)
# FUNCTIONS (USE LOCAL VARIABLES AND RETURN EITHER A CODE OR A STRING)

# MAIN FUNCTION:
    # OPTION PARSING
    # LOG FILE AND SYSLOG HANDLING
    # TEMPORARY FILES AND NAMED PIPE HANDLING
    # SIGNAL TRAPS
main "$@"
```

## Conditionals and Expressions

```sh
statement1 && statement2  # both statements are true
statement1 || statement2  # at least one of the statements is true

str1=str2       # str1 matches str2
str1!=str2      # str1 does not match str2
str1<str2       # str1 is less than str2
str1>str2       # str1 is greater than str2
-n str1         # str1 is not null (has length greater than 0)
-z str1         # str1 is null (has length 0)

-a file         # file exists
-d file         # file exists and is a directory
-e file         # file exists; same -a
-f file         # file exists and is a regular file (i.e., not a directory or other special type of file)
-r file         # you have read permission
-s file         # file exists and is not empty
-w file         # you have write permission
-x file         # you have execute permission on file, or directory search permission if it is a directory
-N file         # file was modified since it was last read
-O file         # you own file
-G file         # file's group ID matches yours (or one of yours, if you are in multiple groups)

file1 -nt file2     # file1 is newer than file2
file1 -ot file2     # file1 is older than file2

-lt     # less than
-le     # less than or equal
-eq     # equal
-ge     # greater than or equal
-gt     # greater than
-ne     # not equal
```


## Debugging with bash

Let's see the meaning of some debugging techniques with bash.

In Bash scripts, use set -x (or the variant set -v, which logs raw input,
including unexpanded variables and comments) for debugging output. Use
strict modes unless you have a good reason not to: Use set -e to abort on
errors (nonzero exit code). Use set -u to detect unset variable usages.
Consider set -o pipefail too, to on errors within pipes, too (though read up
on it more if you do, as this topic is a bit subtle). For more involved
scripts, also use trap on EXIT or ERR. A useful habit is to start a script
like this, which will make it detect and abort on common errors and print a
message:

```sh
set -eo pipefail 
trap "echo 'error: Script failed: see failed command above'" ERR
```

We can also use some flags with bash to debug a script.

Indeed we can easily debug the bash script by passing different options to bash
command. For example -n will not run commands and check for syntax errors only.
-v echo commands before running them. -x echo commands after command-line
processing.

```sh
bash -n scriptname
bash -v scriptname
bash -x scriptname
```

## Executing Commands in Shell Scripts

In order to execute a command and save its stdout we should always use $() and not backticks \`\`, 
this for two reasons mainly:
    - $() allows for nested commands, while backticks do not without escaping
    - $() is POSIX portable and compliant, while backticks do not

## Functions 
It is a good idea for scripts larger than a screen page to build functions, 
and make our program more modular as possible.There are no return values in bash, 
so we can return values by printing a string like:

```sh
my_function_name() {
    ...
    printf "%s" "$return_value"
}
```
Also consider that when we write modules our function name should be like:
```sh
my_module_name::my_function_name() {
    ...
    printf "%s" "$return_value"
}
```

all the functions related to a module should be contained inside the relative module file, in the last case the function would be contained in "my_module_name.sh".

To refer to all arguments passed to a function we can use "$@", and to remove the first argument we can use "shift", this is similar to Perl.


## Advanced Redirection

There are more advanced redirection operators from newer versions of bash, the operators are:
    - `<<`
    - `<<<`
    - `<()`
    - `>()`
let's see some use cases in order to explain these:
```sh
cat fileName | tr ' ' '\n'
```
an alternative to this notation is a simpler:
```sh
tr ' ' '\n' < fileName
```
so we can use `<` to redirect the stdin to an actual file.
What if the content on which we want to apply our tr is not in a file but in a variable ? Well, this is the case for `<<<`, let's see an example:
```sh
content="this is an example string"
tr ' ' '\n' <<< "$content"
```
Another example can be:

```sh
wc -w <<< "$( sed -n '1p' words )"
```

now let's consider another scenario, let's say we want to get a diff between two files, we would normally do:
```sh
diff -u file1 file2
```
how we could apply a diff on the output of another command instead of a file ?
Well we could first save the command output to a file for both command and then use those files, but there is a syntactic faster solution:

```sh
diff -u <(ls) <(ls anotherDir/)
```

Now let's see the `<<` HEREDOC or HERESTRING (a heredoc with one string) example,
this is useful when we want to create strings,
the most common use of heredocs is dumping documentation, for example:

```sh
usage() {
    cat <<EOF
usage: foobar [-x] [-v] [-z] [file ...]
A short explanation of the operation goes here.
It might be a few lines long, but shouldn't be excessive.
EOF
}
```

We can have better indentation with `<<` accompanied by `-` format, for example:

```sh
usage() {
    cat <<-EOF
    usage: foobar [-x] [-v] [-z] [file ...]
    A short explanation of the operation goes here.
    It might be a few lines long, but shouldn't be excessive.
    EOF
}
```
Another example with herestring but using `<<<` is by using:

```sh
grep proud <<<"I am a proud sentence"
```

## Pattern Matching

In Bash we have basically three way for pattern matching
  - Globs (used for files), e.g., * to match a range of files
  - Extended Globs (used for files), this has to be enabled with shopt -s extglob
  - Regex (used for strings), used with the =~ operator

## Globs

Let's see some example, where we can use globs to select multiple files:

```sh
files=(*) #selects all the files in the current directory
files=(../*.tar.gz) #selects all the .tar.gz files in the previous directory
```


## Extended Globs Examples

Once we have enabled extended globs with:

```sh
shopt -s extglob
```

we can tell things such as "do this for all files except this" or 
"do this for this file or this file" and so on, to a certain degree 
extended globs and regex are interchangeable in terms of capabilities. 
Let's see some example:

```sh
#remember that the * glob doesn't match hidden files, but .* matches only hidden files...
shopt -s extglob
rm !(fileName) #this removes all files in current directory except fileName
rm !(file1|file2) #this removes all files in current directory except the two mentioned files
rm !([bc]) #removes all but b and c
rm !(*.txt) #removes all files except those ending with .txt
ls !(*jpg|*bmp)
```


## Regex

Let's see an example of regex:

```sh
# Regex check is done within
myregex='co[ao]l'
if [[ "cool" =~ $myregex ]]; do
	echo "matched"
else
	echo "not matched"
fi
```


## Temporary Files

Occasionally we need to create temporary files to store data, in order to do this we can do:

```sh
my_file=$(mktemp)
printf "%s" "ciao" > "$my_file"
rm "$my_fil"ed
```


## Looping and Parsing Data
When we loop over data (either from a file or a variable) it is good practice to use while and use the IFS field separato variable, let's see some examples:

```sh
# loop through a file delimited by newlines
while IFS=$'\n' read -r line; do
	echo "$line"
done < $file_name
```

```sh
# loop through a variable delimited by newlines:
while IFS=$'\n' read -r line; do
	echo "$line"
done <<< $variable_name
```
```sh
# loop through fields of an array:
for field in "${myarray[@]}"; do
	echo "$field"
	echo "\n"
done
```
```sh
# Insert data separated by blanks in an array and loop through them
data="gatto cane mamma papa lol"
read -a myarray <<< $data; 
for field in "${myarray[@]}"; do
	echo "$field"
	echo "\n"
done
```
```sh
# Loop through fileds separate by another character such as comma:
lol="ciao,mamma,papa,gatto,cane"; 
IFS=, 
read -a myarray <<< "$lol"
```
Notice that if we have some strange combinations of characters as separators, 
it is generally a good idea to convert this combo with string substitution with another character such as comma, and then separate with IFS.

```sh
# loop through lines and put the first column into first_name, the second column into last_name and third column into phone variables.
while read -r first_name last_name phone; do
  # Only print the last name (second column)
  printf '%s\n' "$last_name"
done < "$file"
```
Remember that when dealing with arrays, @ expands multiple arguments, while * concatenates them, so never do for field in `"${array[*]}"` !

We can also read an array from the stdin where each element is separated
by a newline by doing:
```sh
i=0
while read line
do
    array[$i]=$line
    ((i+=1))
done

echo ${array[@]}
```

## String and Arrays Manipulations
Let's see some example of string manipulation which will allow us to use builtins instead of awk, sed or perl, in order to gain some speed and not create subprocesses.


```sh
#Let's see how to create an array
names=("Bob" "Peter" "$USER" "Big Bad John")

#Now we create a normal string
example=" this is an example how do you do ?"

myarr=($example) #this creates an array from a string

echo "${myarr[@]:0:4}" #prints only from element 0 and gets 4 elements from that
echo "${myarr[@]:2:4}" #prints only from element 2 and gets 4 elements from that


echo "${#Unix[@]}" #prints the number of elements

echo "${#Unix[3]}" #prints the length of the 4th element of the array

echo "${example/d/D}" #substitutes the first occurrence of d with D

echo "${example//d/D}" #substitutes all the occurrences of d with D 

echo "${example#*d}"  #deletes from left until the first occurrence of d included
echo "${example##*d}" #deletes from left until the last occurrence of d included

echo "${example%d*}" #deletes from right until the first occurrence of d included
echo "${example%%d*}" #deletes from right until the last occurrence of d included

# example, given the string:
example="this is an example, string with racecars and dragons"
#let's see what the following commands will output

echo "${example#*c}"   #ecars and dragons
echo "${example##*c}"  #ars and dragons
echo "${example%c*}"   #this is an example, string with race
echo "${example%%c*}"  #this is an example, string with ra

echo "${example:3}"   # Remove the first three chars (leaving 4..end)
echo "${example::3}"  # Return the first three characters

#Let's see still some other example:
VAR=foofoobar
${VAR/foo/bar} # barfoobar
${VAR//foo/bar} # barbarbar
${VAR//foo} # bar
```
Let's see an example where we have a one line separator 
and put it in array the separated fields into an array

```sh
IFS=. read -a ip_elements <<< "127.0.0.1"
```

now i can print the various elements with
```sh
print "${ip_elements[*]}"
```
or we can even print them singularly with:
```sh
printf "%s\n" "${ip_elements[3]}"
printf "%s\n" "${ip_elements[2]}"
printf "%s\n" "${ip_elements[1]}"
printf "%s\n" "${ip_elements[0]}"
```


### String Subbstitution Examples

`${variable#pattern}` # if the pattern matches the beginning of the variable's value, delete the shortest part that matches and return the rest
`${variable##pattern}` # if the pattern matches the beginning of the variable's value, delete the longest part that matches and return the rest
`${variable%pattern}` # if the pattern matches the end of the variable's value, delete the shortest part that matches and return the rest
`${variable%%pattern}` # if the pattern matches the end of the variable's value, delete the longest part that matches and return the rest
`${variable/pattern/string}`  # the longest match to pattern in variable is replaced by string. Only the first match is replaced
`${variable//pattern/string}` # the longest match to pattern in variable is replaced by string. All matches are replaced
`${#varname}` # returns the length of the value of the variable as a character string


## Associative Arrays

We can create an associative array and create some key-value pair with the following code:

```sh
declare -A my_assoc
my_assoc[name]="giuseppe"
my_assoc[surname]="nebbione"
```

notice that as with normal arrays we can append an element to the associative array with the
notation "+=":


```sh
var="random string"
aa=([hello]="world")
aa+=([b]="$var")           # aa now contains 2 items
```

The -A option declares aa to be an associative array. Assignments are then made 
by putting the "key" inside the square brackets rather than an array index. 
We can also assign multiple items at once:

```sh 
declare -A my_assoc
my_assoc=([name]="giuseppe" [surname]="nebbione" [address]="Italy, Napoli")
```

Let's see some use case:
```sh
if [[ ${my_assoc[name]} == "giuseppe" ]]; then
    printf "%s" "They are equal"
else
	printf "%s" "They are not equal"
fi
```

we can even use variables as keys, for example, this is possible and valid:

```sh
key="surname"
bb=${my_assoc["$key"]}
```

in order to loop over the keys of an associative array we do:

```sh
for key in "${!my_assoc[@]}"; do
	printf "%s" "$key"
done
```


## Bash Math
Bash alone isn't capable of doing floating point math, in order to do that either
use awk or bc, let's see now some example of integer math with pure bash:
```sh
count=$(( $count + 1 ))
```

Another example:
```sh
read x
read y

echo "$((x + y))"
echo "$((x - y))"
echo "$((x * y))"
echo "$((x / y))" # returns the integer part of the division
echo "$((x % y))" # returns the module of division

# notice that it is equivalent to write $x or x inside the double parenthesis
```

We can do also floating point math by using bc, let's be sure to call
bc with the `-l` flag which allows for long precision math and other
math library functions. Let's see an example:
```sh
# read an arbitrary math expression
read expression

# perform the operation with bc -l
res=$(bc -l <<<"$expression")

# round the result to three digits
printf "%.3f" "$res"
```

We can also round a result a priori (this is also a good choice)
by instructing the desired precision to bc, for example:
```sh
# read an arbitrary math expression
read expression

# perform the operation with bc -l and specifying the precision
res=$(bc -l <<<" scale=3; "$expression" ")

# print the rounded result to three digits
printf "%s" "$res"
```

Another example, is X greater than Y?
```sh
read x
read y

z="$((x - y))"

if  ((z == 0)); then
    echo "X is equal to Y"
elif ((z > 0 )); then
    echo "X is greater than Y"
else
    echo "X is less than Y"
fi
```

We can also build more complex conditions by doing:
```sh
read x
read y
read z

if (( (x==y) && (x==z) ));then
    echo "EQUILATERAL"
elif (( (x!=y) && (y!=z) && (x!=z)  ));then
    echo "SCALENE"
else
    echo "ISOSCELES"
fi
```



## Command groups

Command groups can be used to run multiple commands and have a single redirection
affect all of them:

```sh
{ echo "Starting at $(date)"; rsync -av . /backup; echo
"Finishing at $(date)"; } > backup.log 2>&1
```

Command groups are also useful to shorten certain common tasks:

```sh
[[ -f $CONFIGFILE ]] || { echo "Config file $CONFIGFILE not found" >&2; exit 1; }
```

## File Redirection and Process Substitution

Let's see how to use the powerful find command to make an array of files
```sh
files=()
while read -r -d $'\0' line; do
	files+=("$line")
done < <(find /foo -print0)

# notice that is is essential to use print0 in this find command
# in order to be able to produce a list which can be read by `read -r`
```

We can also exploit the full capabilities of find, for example let's say
we want to search recursively all .py files but exclude certain directories
from our search, we can do that with this:

```python
files=()
while read -r -d $'\0' file; do
    printf "%s\n" "$file"
    files+=("$file")
done < <(find -name "*.py" -not -path "./docs/*" -and -not -path "./env/*" -print0)

printf "%s\n" "done"
```

## Heredocs and Herestrings

Why they exist: Sometimes storing data in a file is overkill. We might only have a tiny bit of it -- enough
to fit conveniently in the script itself. Or we might want to redirect the contents of a
variable into a command, without having to write it to a file first.

#### interpolate output of commands 

 printf 'The date is: %s\n' "$(date)"


## Pipelines
Pipes are a very attractive means of post-processing application output. You
should, however, be careful not to over-use pipes. If you end up making a
pipeline that consists of three or more applications, it is time to ask yourself
whether you're doing things a smart way. You might be able to use more
application features of one of the post-processing applications you've used
earlier in the pipe. Each new command in a pipeline causes a new subshell
and a new application to be loaded. It also makes it very hard to follow the
logic in your script!


## Bash Shortcuts
A list of common shortcuts when dealing with bash command line:


* `CTRL+a`  # move to beginning of line
* `CTRL+b`  # moves backward one character
* `CTRL+c`  # halts the current command
* `CTRL+d`  # deletes one character backward or logs out of current session, it is similar to exit
* `CTRL+e`  # moves to end of line
* `CTRL+f`  # moves forward one character
* `CTRL+g`  # aborts the current editing command or search mode and rings the terminal bell
* `CTRL+j`  # same as RETURN
* `CTRL+k`  # deletes from the cursor to end of line
* `CTRL+l`  # clears screen 
* `CTRL+m`  # same as RETURN
* `CTRL+n`  # next line in command history
* `CTRL+o`  # same as RETURN, then displays next line in history file
* `CTRL+p`  # previous line in command history
* `CTRL+r`  # searches backward
* `CTRL+s`  # searches forward
* `CTRL+t`  # swaps two characters
* `CTRL+u`  # removes backward from cursor to the beginning of line
* `CTRL+v`  # makes the next character typed verbatim
* `CTRL+w`  # removes the word behind the cursor
* `CTRL+x`  # lists the possible filename completions of the current word
* `CTRL+y`  # retrieves (yank) last item killed
* `CTRL+z`  # stops the current command, resume with fg in the foreground or bg in the background
* `CTRL+xx` # Toggle between the start of line and current cursor position
* `CTRL+_`  # undo
* `ALT+. `  # cycles through previous arguments
* `ALT+#`   # useful whenever we want to save the command in history, makes the current command a comment
* `ALT+f`   # moves forward one word
* `ALT+b`   # moves backward one word
* `ALT+d`   # deletes a word
* `ALT+r`   # cancel the changes and put back the line as it was in the history (revert)
* `ALT+t`   # swaps two words
* `^ab^de`  # run previous command, replacing ab with de
* `!str`    # repeat the last command which was starting with "str"
* `!str:p`  # prints the last command starting with "str"
* `!str:2`  # second argument to last command starting with "str"
* `!!`      # repeat last command
* `!*`      # all arguments of previous command
* `!$`      # last argument of previous command
* `!n:p`    # print last command starting with n



### References
* [Bash_Reference] - Bash Reference!


## TODO
* Integrate from here [Bash Tutorial](https://github.com/denysdovhan/bash-handbook)
* Insert more snippets of code which accomplish specific functionalities

License
----

GPLv3

[Bash_Reference]: <http://mywiki.wooledge.org/>
