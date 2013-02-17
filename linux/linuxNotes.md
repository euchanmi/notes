# Linux Notes


## Set ±o : Turns Shell Features On and Off:
+ The bash set builtin (there is a set builtin in tcsh, but it works differently), when used with the –o or +o option, 
enables, disables, and lists certain bash features. For example, the following command turns on the noclobber 
feature (page 129): $ set -o noclobber You can turn this feature off (the default) by giving the 
command $ set +o noclobber

+ The command set –o without an option lists each of the features controlled by set, followed by its state (on or off).
The command set +o without an option lists the same features in a form you can use as input to the shell.


## Shell Functions:

+ A bash shell function (tcsh does not have functions) is similar to a shell script in that it stores a series of 
commands for execution at a later time. However, because the shell stores a function in the computer’s main memory (RAM)
instead of in a file on the disk, the shell can access it more quickly than the shell can access a script. The shell
also preprocesses (parses) a function so that it starts up more quickly than a script. Finally the shell executes 
a shell function in the same shell that called it. If you define too many functions, the overhead of starting a subshell 
(as when you run a script) can become unacceptable. You can declare a shell function in the ~/.bash_profile startup 
file, in the script that uses it, or directly from the command line. You
+ {{{sh 
   [function] functionName {
         commands...
   }
}}}

## Shell Startup Files:

1. /etc/profile => system wide default properties for log-in shell.
2. ~/.bash_profile, ~/.bash_login ~/.profile 
   + Next, the shell looks for ~/.bash_profile, ~/.bash_login, and ~/.profile in that order.
   + You can override the default sets in the /etc/profile by putting commands in these files.
   + A shell running on a virtual terminal does not execute commands in these files.
3. ~/.bash_logout
   + executed when logged out. Usually used for cleaning up resources such as removing temp files
4. /etc/bashrc
   + Not called directly by BASH but many ~/.bashrc file calls /etc/bashrc file so system wide default propertices 
     can be set.
5. ~/.bashrc
   + An non-login interactive shell executes ~/.bashrc file. Typically a startup file for login shell such as 
     ~/.bash_profile file runs this file. Thus, both non-login and login shells run this file.
6. Non-interactive shells look for the environment variable BASH_ENV (or ENV if the shell is called as sh)  and execute
   commands in the file named by this variable.
7. Use ~/.bash_profile to set PATH
   + Because commands in ~/.bashrc file may be executed many times, and because subshell inherit exported variables, 
     it is a good idea to use ~/.bash_profile to set PATH or commands that add to existing variables.
   + Modifying a variable in ~/.bash_profile causes changes you make in an interactive session to propagate to 
     subshells. In constrast, modifying a variable in ~/.bashrc overrides changes inherited from a parent shell.

## File Descriptior:

+ is the place a program sends its output to and gets input from.
+ Linux opens three file descriptors for the program:
   + 0 => standard input
   + 1 => standard output
   + 2 => standard error
   + “<” is a shorthand for “0<”
   + “>” is a shorthand for “1>”
   + {{{sh cat x y 1> output 2> error}}}
   + • “&>” redirects both output and error
      + {{{sh cat x y &> output_and_error }}}
   + “2>&1” declares file descript 2 to be a duplicate of file descriptor 1. As a result, both standard output and 
     error are redirected to a same file:
      + {{{sh cat x y 1> output_and_error 2>&1 }}}

## Shell Scripts:  

+ “#!” => specifies a shell
   + Because the operating system checks the first line of the script before attempting to execute it using “exe”, 
     use “#!” to specify a shell which the script needs to be run on.
      + “#!/bin/bash”
+ “fork” and “exec” system calls.
   + A command on a command line causes the shell to “fork” a new process, subshell, and calls “exec” to run the
     command. “exec” runs a binary file. If “exec” fails, then the subshell assumes that the command is a shell script,
     and the subshell runs the commands in the script. Unlike a login shell which takes input from the command line, 
     subshell takes input from a file - namely, the shell script.
+ You can run a shell script without exec permision but with read permision by “bash” command to exec a shell that runs
  the script directly. 
   + {{{sh bash scriptname }}}
   + Although this works, using “bash” command to exec a script is slower than running a script directly with exec 
     permission.
+ Arithmetic expression: shell performs arithmetic expression by replacing arithmetic expression and evaluate it.
   + $((Arithmetic Expression)) 
   • When use $((...)) then $ sings inside the paranthesis are options.
+ Command Substitution:
   + $(commnad) or `command`
   + echo “current directory is $(pwd) and the sub directorys are `ls -d`”
+ Process Substitution:
   + <(command) causes command to be executed and the output written to a named pipe (FIFO).
   + Similarly an argument with the syntax >(command) is replaced by the name of a pipe that command reads as standard input.
   + sort -m -f <(grep "[^A-Z]..$" memo1 | sort)
+ trap command:
   + trap command can be used in script to stop from user to interupt the script by seding it a signal.
   + Example will be trapping signam 18, which means that no one can use control-z to defeat the lock.
   + {{{sh #!/bin/bash
        trap ‘’ 1 2 3 18
     }}}
+ If stathement:
   + {{{sh 
        if test-command then
           command
        elif test-command then
        else 
           command
        fi
     }}}
+ For if statement:
   + Use “test” builtin. 
   + {{{sh
       if test $num -eq 7 then
          echo “number is 7”
       fi
     }}}
   + [] is synonym for test
   + {{{sh
       if [$num -eq 7] then
          echo “number is 7”
       fi
     }}}
+ For loop
   + {{{sh 
      for fruit in apples oranges pears bananas
      do 
         echo “$fruit”
      done
     }}}
+ While
   + {{{sh
      while [ “$number” -lt 10 ] # => or ‘while :’ to omit the while condition
      do 
            echo -n “$number”
            ((number += 1))
      done
     }}}
+ Until ==> negate of while
   + {{{sh
      until [ “$number” -lt 10 ]
      do 
            echo -n “$number”
            ((number += 1))
      done
     }}}
+ Break and condtiue ==> use them to brean or contine in loops
+ Case:
   + case _test-string_ in 
        _pattern-1_)
            _commands-1
            ;;
        _pattern-2_)
            commands-2
            ;;
        _pattern-3_)
            commands-3
            ;;
   + {{{sh 
        case “$letter” in 
            A) 
               echo “The letter is A”
            B)
              echo “The letter is B”
            *) 
               echo “The letter is..I don’t know”
     }}}
+ Select:
   + It displays a menu, assigns a value to a variable based on the user’s choice of items, and executes a series of commands.
   + select _varname_ [in arg...]
     do
        commands
     done
   + If _in arg.._ is omitted, then select uses the positional argument for the varname.
   + Afther displying the menu, select displays the value of PS3. The default of PS3 is “?#”. But set it to something
     meaningful.
   + {{{sh
     PS3=”Chhose your favoraite fruite: “
     select FRUIT in apple banana blueburry kiwi orange STOP
     do 
        if [ "$FRUIT" == "" ]; then 
          echo "Invalud entry!"
          continue
        elif [ $FRUIT = STOP ]; then 
          echo "Thanks for playing!"
          break
        fi
      echo "You choose $FRUIT as your fruite."
      echo -e "That is choise number $REPLY.\n"
    done
     }}}
+ Here document:
  * A Here document allows you to redirect input to a shell script from within the shell script itself.
  * The two less than symbols (<<) indicate a Here document follows.
  * One or more characters that delimit the Here document follow the less than symbols—this example uses a plus sign.
  * {{{sh
      grep -i "$1" <<+
      Max Jun 22
      John January 21
      Zach January 23
      +
   }}}
+ Debugging script:
   + USe “-x” option to debug a shell script. This option causes the shell to display each command before it runs the 
     command. Tracing a script’s execution in this way can give you information about where a problem lies.
   + {{{sh bash -x ./age_check.sh }}}
   + Put set “–x” anywhere in the script you want to turn debugging on. Turn the debugging option off with a plus 
     sign: “set +x” The “set –o” xtrace and “set +o xtrace” commands do the same things as “set –x” and “set +x”, respectively.

## Shell Variables:

+ user “${}” to concat variable values with string:
   + {{{sh echo “Hello, ${NAME}” }}}
+ To remove “value” of the variable, set the variable to null value:
   + NAME=
   + echo $NAME => prints nothing
+ To remove the variable, use “unset” command.
   + unset NAME
+ To make a variable readonly, use “readonly” commnad:
   +{{{sh readonly PATH=”...”}}} 
+ Declare and Typeset:
   + are used to set attirubutes for variables.
   + Using declare without any option is the same as not using declare.
      + {{{sh declare var=”...” # <== this can be just written as var=”...” }}}
   + Declare options:
      + “-a” => declares a variable as array.
      + “-f” => declares a variable to be a function name.
      + “-i” => declares a variable to be of type integer.
      + “-r” => Makes a variable readonly; same as “readonly” command.
      + “-x” => exports a variable (makes it global); same as “export” command.
      + “declare -r” without any variable name lists all the readonly variables.
+ The “PS1” variable holds the prompt string that the shell uses to let you know that it is waiting for a command.
+ “PS2” for user prompt(secondary)
+ “PS3” for menu prompt
+ “PS4” for debugging prompt
+ The “IFS” (Internal Field Separator) shell variable (not under tcsh) specifies the characters you can use to separate arguments on a command line.
+ “HISTSIZE”, “HISTFILE”, and “HISTFILESIZE” are for “history” command.
+ The “!!” command reexecutes the previous event, and the shell replaces the “!$” token with the last word on the previous command line.
+ To change default editor when using Readline Library in bash:
   + {{{sh set -o vi}}}
+ Arithmetic Expension => ${{expression}}
+ Variable Substituion => ${}
+ When you use variables within $(( and )), the dollar signs that precede individual variable references are optional.
+ Array Variable:
   + {{{sh 
      NAME=(Max Helen Sam Zach)
      echo ${NAME[0]} => Max
     }}}
   + The subscripts [*] and [@] both extract the entire array but work differently when used within double quotation 
     marks. An @ produces an array that is a duplicate of the original array; an * produces a single element of an 
     array (or a plain variable) that holds all the elements of the array separated by the first character 
     in IFS (normally a SPACE).
   + You can apply the ${#name[*]} operator to array variables, returning the number of elements in the array:
   + The same operator, when given the index of an element of an array in place of *, returns the length of the element:
     {{{ sh echo “${#NAME[1]}” => 5 }}}
+ export:
   + Once you use the export builtin with a variable name as an argument, the shell places the value of the variable
     in the calling environment of child processes. This call by value gives each child process a copy of the variable 
     for its own use.
   + Although it is rarely done, you can export a variable before you assign a value to it. You do not need to export 
     an already-exported variable a second time after you change its value.

## Special Parameters:

+ As with positional parameters, it is not possible to modify the value of a special parameter by assignment.
+ $$: PID Number
   + The shell stores in the $$ parameter the PID number of the process that is executing it.
   + the shell substitutes the value of $$ before it forks a new process to run a command.
+ $!
   + The shell stores the value of the PID number of the last process that ran in the background in $!
+ $?: Exit Status
   + When a process stops executing for any reason, it returns an exit status to its parent process. The exit status 
     is also referred to as a condition code or a return code. The $? ($status under tcsh) variable stores the exit
     status of the last command.
   + By convention a nonzero exit status represents a false value and means the command failed. A zero is true and 
     indicates the command executed successfully. In the following example, the first ls command succeeds and the second fails, as demonstrated by the exit status:
+ $#: Number of Command-Line Arguments
+ $0: Name of the Calling Program
+ $1–$n: Command-Line Arguments
+ set 
   + Initializes Command-Line Arguments When you call the set builtin with one or more arguments, it assigns the values 
     of the arguments to the positional parameters, starting with $1 (not available in tcsh).
   + {{{sh 
        set this is it
        echo $3 $2 $1 => “it is this”
     }}}
+ “$*” and “$@”: Represent All Command-Line Arguments
   + The $* and $@ parameters work the same way except when they are enclosed within double quotation marks. 
     Using “$*” yields a single argument (with SPACEs or the value of the first character of IFS between the positional
     parameters), whereas using “$@” produces a list wherein each positional parameter is a separate argument.
     This difference typically makes “$@” more useful than “$*” in shell scripts.
   + The “:-” modifier uses a default value in place of a null or unset variable while allowing a nonnull variable to 
     represent itself:
     {{{sh $name:-default }}}
   + Use “:=” modifier to assign a default value.
   + the :? modifier causes the script to display an error message and terminate with an exit status of 1: ${name:?message}


## Function:

+ Because functions run in the same environment as the shell that calls, them, variables are implicitly shared by 
  a shell and a function it calls. That’s why you need to be careful and use local variables. When used within a function, 
  the typeset builtin declares a variable to be local to the function it is defined in. 

## Grep:

+ grep [options] _pattern_ [files]
+ “-i” => case insensitive matching
+ “-c” => output only count of matching lines
+ “-v” => output non-matching lines
+ “-l” => output only names of files which contain matching lines
+ “-q” => quiet mode. Dont output to stdout
+ “-s” => suppress error messages
+ “-n” => add line numbers to output
+ “-w” => search for pattern as a word (delimited by non-word separators)
+ “-f” => search file contained in file.

## Redirection Operations:

+ “< filename” => redirects standard input from finename
+ “> filename” => redirects standard output to filename unless filename exists and noclobber is set. If noclobber 
                  is set, this redirection creates filename if it does not exist and overwrites if it exists.
+ “>| filename” => redirects standard output to filename even if it exists and noclobber is set.
+ “>> filename” => redirects and appends output to filename.
+ “&> filename” => redirects both output and error to filename.
+ “<&m” => duplicate standard input from file descriptor m
+ “[n]>&m” => duplicate standard ouput or file desriptor n if speicifed from the file descriptor m.
+ “[n]<&-” => closes standard input or file descriptor n if specified.
+ “[n]>&-” => closes standard output or file decriptor n if specified. 

## Shell Utilities:

+ “tr” => maps one string of characters to another
+ “tee” => send standartd input to both file and standard output
+ “bg” => moves a job to background
+ “fg” => moves a job to foreground
+ “jobs” => displays a list of suspended jobs and jobs running in the background.
+ “chsh” => change log-in shell permanentanly.
   + {{{sh chsh -s /bin/bash}}}
+ “pbcopy”, “pbpaste” => provide copying and pasting to the pasteboard (the Clipboard) from command line
+ “service” => executes services in “/etc/init.d/” directory. Not included in Mac OS X.
   + {{{sh service ssh start}}}
+ “groups” => checks which gruop a user belongs to. 
   + {{{sh groups eun}}}

## SSH Utilities:

+ “ssh-add -t 36000” => put ssh key into an ssh0agent session for 10 hours so no need to enter passphrase repeatedly.
+ “ssh-keygen -p” => adds a passphrase to the existing key.

## File Utilities:

+ “head” => displays the lines at the beginning of a file
+ “sort” => puts a file in order by lines
+ “tail” => displays the lines at the end of a file
+ “uniq” => displays the contents of a file, skipping adjacent duplicate lines. 

## Compression Utilities:

+ “bunzip2” => Returns a file compressed with bzip2 to its original size and format
+ “bzcat” => displays a file compressed with bzip2
+ “bzip2” => compreses a file
+ “compress” => compresses a file(not as well as _bzip2_ or _gzip_)
+ “gunzip” => returns a file compressed with _gzip_ or _compress_ to its original size and format
+ “gzip” => compresses a file(not as well as _bzip2_)
+ “zcat” => displays a file compressed with _gzip_
+ “tar” => Creates or extracts files form an archive file
+ “locate” => Searches for files on the local system
+ “whereis” => Displays the full pathnames of a utility, source code, or man page
+ “which” => Displays the full pathname of a command you can run
+ “type” => The type builtin (use which under tcsh) provides information about a command:
+ “read” => accept user input
   + {{{sh read name }}}
   + “$REPLY” contains the user’s input when you do not specify a variable name.
   + “-p” options prompts instead of using ech => {{{sh read -p “enter your name:” }}}
+ “exec” => Executes a Command or Redirects File Descriptors
   + when shell runs a command that is not build-in, it spawns a new process which then inherits exported variables.
     But when run a commnad via “exec”, it runs it in the current shell.
   + does not return control so it needs to be the last command in a script.
   +

## Crontab:

+ “sudo crontab -l” => displays current cronjobs
+ “sudo crontab -e” => edits the list of cronjobs
+ scheduling:
   + minute (from 0 to 59)
     hour (from 0 to 23)
     day of month (from 1 to 31) 
     month (from 1 to 12)
     day of week (from 0 to 6) (0=Sunday)
+ {{{sh * * * * * /bin/execute/this/script.sh }}} => every minute
+ {{{sh 0 1 * * 5 /bin/execute/this/script.sh }}} => every Friday 1AM
   + minute: 0
     of hour: 1
     of day of month: * (every day of month)
     of month: * (every month)
     and weekday: 5 (=Friday)
+ Special words: if you use the first(minute) field, you can also put in a keyword instead of a number:
   {{{sh
   @reboot     Run once, at startup
   @yearly     Run once  a year     "0 0 1 1 *"
   @annually   (same as  @yearly)
   @monthly    Run once  a month    "0 0 1 * *"
   @weekly     Run once  a week     "0 0 * * 0"
   @daily      Run once  a day      "0 0 * * *"
   @midnight   (same as  @daily)
   @hourly     Run once  an hour    "0 * * * *"

   @daily /bin/execute/this/script.sh
   }}}
+ Mailing the crontab:
   + By default cron saves the output in the user’s mailbox (root in this case) on the local system. 
     But you can also configure crontab to forward all output to a real email address by starting your crontab
     with the following line:
   + {{{sh MAILTO="yourname@yourdomain.com" }}}
+ Mailing the crontab output of just one cronjob:
   + {{{sh aptitude install mailx }}}
   + {{{sh */10 * * * * /bin/execute/this/script.sh 2>&1 | mail -s "Cronjob ouput" yourname@yourdomain.com }}}

## Disk Utilities:

+ “df” : report file system disk space usagei
   + df [Option...] [FILE_NAME...]
   + If [FILE_NAME...] is not given df displays the following information for each file system that was activated with 
     mount: total space, used space, free space and use in %.
   + eunsong@Euns-MacBook-Pro:~ » df -h
      Filesystem      Size   Used  Avail Capacity  Mounted on
      /dev/disk0s2   698Gi  180Gi  517Gi    26%    /
      devfs          183Ki  183Ki    0Bi   100%    /dev
      map -hosts       0Bi    0Bi    0Bi   100%    /net
      map auto_home    0Bi    0Bi    0Bi   100%    /home
+ “du”: estimate file space usage
   + du [Option...] [FILE_NAME...]
   + If [FILE_NAME...] is not given du will print the disk usage of every file from the current directory and all subdir.
   + # du -hsc /var/*
      5.3M	/var/backups
      188M	/var/cache
      112M	/var/lib
      4.0K	/var/local
      16K	/var/lock
      177M	/var/log
      16K	/var/lost+found
      12K	/var/mail
      4.0K	/var/opt
      100K	/var/run
      604K	/var/spool
      4.0K	/var/tmp
      120K	/var/www
      481M	total

## User and System Information Utilities:


+ “finger” => Displays detailed information about users, including their full names.
+ “hostname” => Displays the name of the local system.
+ “w” => Displays detailed information about users who are logged in on the local system.
+ “who” => Displays information about users who are logged in on the local system.

## User Communication Utilities:

+ “mesg” => Permits or denies messages sent by write.
+ “write” => Sends a message to another user who is logged in.

## Miscellaneous Utilities:

+ “nohub” => Allows to ignore HUP(hangup) signal and keep running the command after user logged out.
+ “date” => Displays the current date and time.
+ “echo” => Copies its arguments to the screen.

## User Access And Permission Utilities:

+ “visudo” => Allows editing of sudoers file.
