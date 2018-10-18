---
description: Commandline obfuscation
---

# Commandline Obfusaction

This lab is fully based on the research done by Daniel Bohannon from FireEye, see references below.

## Environment variables

```csharp
C:\Users\mantvydas>set a=/c & set b=calc
C:\Users\mantvydas>cmd %a% %b%
```

Note though that the commandline logging \(dynamic detection\) still works as the commandline needs to be expanded before it can get executed, but static detections could potentially be bypassed:

![](../.gitbook/assets/environment-variables.png)

## Duoble quotes

```text
C:\Users\mantvydas>c""m"d"
```

Note how double quotes can actually make both static and dynamic detections a bit more difficult:

![](../.gitbook/assets/double-quotes.png)

## Carets

```text
C:\Users\mantvydas>n^e^t u^s^er
```

Commandline logging, same as with using environment variables, is not affected, however some static detections could be bypassed:

![](../.gitbook/assets/carets.png)

## Garbage delimiters

A very interesting technique. Let's look at this first without garbage delimiters:

```text
PS C:\Users\mantvydas> cmd /c "set x=calc & echo %x% | cmd"
```

The above sets en environment variable x to `calc` and then prints it and pipes it to the standard input of the cmd

![](../.gitbook/assets/garbage1.png)

Introducing garbage delimiters `@` into the equation:

```text
PS C:\Users\mantvydas> cmd /c "set x=c@alc & echo %x:@=% | cmd"
```

The above does the same as the earlier example, except that it introduces more filth into the command \(`c@lc`\). You can see from the below screenshot that Windows does not recognise such `c@lc`, but later attempt when the `%x:@=%` removes that extraneous `@` symbol from the string, command is executed successfully:

![](../.gitbook/assets/garbage2.png)

If it is confusing, the below should help clear it up:

```text
PS C:\Users\mantvydas> cmd /c "set x=c@alc & echo %x:@=mantvydas% | cmd"
```

![](../.gitbook/assets/garbage3.png)

In the above, the value `mantvydas` got inserted in the c@lc in the place of @, suggesting that `%x:@=%` \(`:@=` to be precise\) is just a string replacement capability that is saying: replace the @ symbol with text that goes after the `=` sign, which is empty in this case, which effectively means - remove @ from the value stored in the variable x.

## Substring

```text
# take the first character of the value stored in %programdata% env variable

# this will take the C character from %programdata% and will launch the cmd prompt
%programdata:~0,1%md
```

Note that this is only good for bypassing static detections:

![](../.gitbook/assets/substring1.png)

## Batch FOR, DELIMS + TOKENS

We can use a builtin batch looping to extract the Powershell string from an environment variable in order to launch it and bypass the static detections.

{% code-tabs %}
{% code-tabs-item title="@cmd" %}
```text
set pSM 
PSModulePath=C:\Users\mantvydas\Documents\WindowsPowerShell\Modules;....
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Note how the WindowsPowerShell string is present in the PSModule env variable - this mean we can extract it like so:

```text
FOR /F "tokens=7 delims=s\" %g IN ('set^|findstr PSM') do %g
```

What the above does is:

1. Executes `set^|findstr PSM` to get the PSModulePath variable value
2. splits the string using delimiters `s` & `\`
3. Prints out the 7th token, which happens to be the `PowerShell`
4. Which effectively launches PowerShell

![](../.gitbook/assets/batch-powershell.png)

## Comma, semicolon

This may be used for both static and dynamic detection bypasses:

```text
C:\Users\mantvydas>cmd,/c;hostname
PC-MANTVYDAS
```

![](../.gitbook/assets/comasemicoma.png)

## FORCoding

What happens below is essentially there is a loop that goes through the list of indexes \(0 1 2 3 2 6 2 4 5 6 0 7\) which are used to point to characters in the variable `unique`. This allows the FOR loop to concatenate the final string that gets called with `CALL %final%` when the loop reaches the index 1337.

```text
PS C:\Users\mantvydas> cmd /V /C "set unique=nets /ao&&FOR %A IN (0 1 2 3 2 6 2 4 5 6 0 7 1337) DO set final=!final!!uni
que:~%A,1!&& IF %A==1337 CALL %final:~-12%"
```

![](../.gitbook/assets/forcoding.png)

In verbose python this could look something like this:

{% code-tabs %}
{% code-tabs-item title="forcoding.py" %}
```python
import os

dictionary = "nets -ao"
indexes = [0, 1, 2, 3, 2, 6, 2, 4, 5, 6, 0, 7, 1337]
final = ""

for index in indexes:
    if index == 1337:        
        break
    final += dictionary[index]
os.system(final)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/forcoding-python%20%281%29.png)

{% embed url="https://www.youtube.com/watch?v=mej5L9PE1fs" %}

{% embed url="https://www.fireeye.com/blog/threat-research/2018/03/dosfuscation-exploring-obfuscation-and-detection-techniques.html" %}

