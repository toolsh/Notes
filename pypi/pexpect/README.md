official doc: https://pexpect.readthedocs.io/
-


overview:
------
- Pexpect can be used for automating interactive applications such as ssh, ftp, mencoder, passwd, etc.
- Pexpect.send() (sendline() is like send() with a linefeed): writes a string to the child application.
- Pexpect.expect(): waits for the child application to return a given string (regular expression or a list of re).
- After each call to expect() the before and after properties will be set to the text printed by child application.
  - If the child has died and you have read all the child's output then ordinarily expect() will raise an EOF exception.
  - You can read everything up to the EOF without generating an exception by using the EOF pattern expect.
  - In this case everything the child has output will be available in the before property.


install:
-
- ```pip install pexpect```

Requirements:
-
- As of version 4.0, Pexpect can be used on Windows and POSIX systems.
- ```pexpect.spawn``` and ```pexpect.run()``` are only available on POSIX, as they rely on Unix pseudoterminals (ptys). Cross platform code must not use these.
- Pexpect can be used on Windows to wait for a pattern to be produced by a child process, using ```pexpect.popen_spawn.PopenSpawn```, or a file descriptor, using ```pexpect.fdpexpect.fdspawn```.
- ```pty``` module is present in the standard library.
- ```PopenSpawn``` is not a direct replacement for ```spawn```. Many programs only offer interactive behaviour if they detect that they are running in a terminal. When run by ```PopenSpawn,``` they may behave differently.


Find the end of line – CR/LF conventions
-
- Pexpect uses a Pseudo-TTY device to talk to the child application, so when the child app prints "\n" you actually see "\r\n".
- The best way to match the end of a line is to look for the newline: "\r\n" (CR/LF) (hex 0D 0A).
- The $ pattern for end of line match is useless.
  - The $ matches the end of string, but Pexpect reads from the child one character at a time, so each character looks like the end of a line.
  - Pexpect can't do a look-ahead into the child's output stream.


Beware of + and * at the end of patterns
-
- Remember that any time you try to match a pattern that needs look-ahead that you will always get a minimal match (non greedy).
- Generally any star * expression will match as little as possible.
- One thing you can do is to try to force a non-ambiguous character at the end of your pattern. Expect that character to delimit the string.

example code
-
```
try:
    child = pexpect.spawn(foo)
    child.logfile = sys.stdout
    child.expect('password:', timeout=120)  # timeout=None (ignore timeout and block indefinitely)
    child.sendline(my_secret_password)
    # We expect any of these three patterns...
    i = child.expect (['Permission denied', 'Terminal type', '[#\$] '])
    if i==0:
        print('Permission denied on host. Can\'t login')
        child.kill(0)
    elif i==1:
        print('Login OK... need to send terminal type.')
        child.sendline('vt100')
        child.expect('[#\$] ')
    elif i==2:
        print('Login OK.')
        print('Shell command prompt', child.after)
expect(pexpect.EOF):
    print("End Of File (EOF) in read().")
except:
    print("Exception was thrown")
    print("debug information:")
    print(str(child))
```

```
shell_cmd = 'ls -l | grep LOG > log_list.txt'
child = pexpect.spawn('/bin/bash', ['-c', shell_cmd])
child.expect(pexpect.EOF)
```

```
child = pexpect.spawn('ls -l')
child.expect(pexpect.EOF)
print(child.before)

# ls runs; completes its task; and then exits.
# The buffer is not flushed before exit so the last few lines are lost.
```