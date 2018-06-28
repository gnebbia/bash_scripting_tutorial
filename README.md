# Bash Scripting
In order to start a script in bash we do:
```sh
#!/usr/bin/env  bash
printf "%s" Hello World"
```
First rule, even if online we see plenty of examples with "echo", we should  always use printf instead of the famous "echo" command.

## Good Rules for Bash Scripting
Here some good rules when writing Bash:
* Always prefer printf to echo
* Quote every variable, always, except when we want to compress spaces
* Don't expect speed in Bash scripts, but if you want to optimize prefer builtins instead of external prograsm
* Don't use cat but do this: ```sh content=$(< fileName)```
* Always use the new test command [[ instead of the old test [
* Use "set +x" and "set -x" to enable debugging mode
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
if((a<b)); then
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

### Bash Program General Structure
```sh
#!/usr/bin/env  bash
#
# AUTHORS, LICENSE AND DOCUMENTATION
#
set -eu -o pipefail
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
## Executing Commands in Shell Scripts
In order to execute a command and save its stdout we should always use $() and not backticks ``, this for two reasons mainly:
    - $() allows for nested commands, while `` does not without escaping
    - $() is POSIX portable and compliant, while `` does not

## Functions 
It is a good idea for scripts larger than a screen page to build functions, and make our program more modular as possible.There are no return values in bash, so we can return values by printing a string like:
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
    - <<
    - <<<
    - <()
    - >()
let's see some use cases in order to explain these:
```sh
cat fileName | tr ' ' '\n'
```
an alternative to this notation is a simpler:
```sh
tr ' ' '\n' < fileName
```
so we can use < to redirect the stdin to an actual file.
What if the content on which we want to apply our tr is not in a file but in a variable ? Well, this is the case for "<<<", let's see an example:
```sh
content="this is an example string"
tr ' ' '\n' <<< "$content"
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
Now let's see the << HEREDOC or HERESTRING (a heredoc with one string) example, this is useful when we want to create strings, the most common use of heredocs is dumping documentation, for example:
```sh
usage() {
    cat <<EOF
usage: foobar [-x] [-v] [-z] [file ...]
A short explanation of the operation goes here.
It might be a few lines long, but shouldn't be excessive.
EOF
}
```
We can have better indentation with "<<" accompanied by "-" format, for example:
```sh
usage() {
    cat <<-EOF
    usage: foobar [-x] [-v] [-z] [file ...]
    A short explanation of the operation goes here.
    It might be a few lines long, but shouldn't be excessive.
    EOF
}
```
Another example with herestring but using <<< is by using:
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

files=(*) #selects all the files in the current directory
files=(../*.tar.gz) #selects all the .tar.gz files in the previous directory

## Extended Globs Examples
Once we have enabled extended globs with:
```sh
shopt -s extglob
```
we can tell things such as "do this for all files except this" or "do this for this file or this file" and so on, to a certain degree extended globs and regex are interchangeable in terms of capabilities. Let's see some example:
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
:
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
Remember that when dealing with arrays, @ expands multiple arguments, while * concatenates them, so never do for field in "${array[*]}" !

## String and Arrays Manipulations
Let's see some example of string manipulation which will allow us to use builtins instead of awk, sed or perl, in order to gain some speed and not create subprocesses.
```sh
#Let's see how to create an array
names=("Bob" "Peter" "$USER" "Big Bad John")

#Now we create a normal string
example=" this is an example how do you do ?"

myarr=($example) #this creates an array from a string

echo "${myarr[@]:0:4}" #prints only from element 0 to 4 of the array

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

## Associative Arrays

We can create an associative array and create some key-value pair with the following code:

```sh declare -A my_assoc
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
while read -r -d $'\0'; do
	files+=("$REPLY")
done < <(find /foo -print0)
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



### References
* [Bash_Reference] - Bash Reference!

License
----

GPL

   [Bash_Reference]: <http://mywiki.wooledge.org/>
