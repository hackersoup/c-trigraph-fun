# C-Trigraph-Fun
Supporting code for my talk on C Trigraphs

What will this loop do? The answer might surprise those unfamiliar with trigraphs:

```
#include <stdio.h>

int main() {
  int i = 0;
  while (i < 5) {
    //Why doesn't this work??/
    i++;
    printf("%d\n", i);
  }
}
```

At first glance it should print out `1 2 3 4 5` with newlines. Not quite! Instead, it will infinitely print out `0`! How come?

In C there are items known as trigraphs which are 3 character sequences interpreted by the preprocessor as a single character. The trigraph `??/` is interpreted as `\` Ending a line with a `\` will delete the following newline, joining the two lines without the `\`, whats known as a line continuation. So in this case…
```
// Why doesn't this work??/
i++;
```
turns into…
```
// Why doesn't this work\
i++;
```
which finally becomes…
`//Why doesn't this worki++`

`i` never gets incremented in the loop and stays zero forever!

Here is another fun example. The code below will compile and print out my name:

```
??=include <stdio.h>

int main(int argc, char *argv??(??)) ??<
  char name??(??) = ??<'B', 'r', 'a', 'd', '??/0'??>;
  printf("%s??/n", name);
  return 0;
??>
```

Because of trigraphs, the above code is perfectly valid C :) The original code is below:

```
#include <stdio.h>

int main(int argc, char *argv[]) {
  char name[] = {'B', 'r', 'a', 'd', '\0'};
  printf("%s\n", name);
  return 0;
}
```

Since trigraphs are simple substitutions that can occur anywhere in a source file, a simple list of sed regex can substitute all characters in a source file with their trigraph representation. Note that this was written for BSD sed provided on Mac, Linux sed may operate differently with the `-i` flag.

```
#!/bin/bash

sed -i.bkp 's/\\/\?\?\//' $1 # \\
sed -i.bkp 's/{/\?\?\</g' $1 # {
sed -i.bkp 's/}/\?\?\>/g' $1 # }
sed -i.bkp 's/\[/\?\?\(/g' $1 # [
sed -i.bkp 's/\]/\?\?\)/g' $1 # ]
sed -i.bkp 's/|/\?\?\!/g' $1 # |
sed -i.bkp "s/\^/\?\?\'/g" $1 # ^
sed -i.bkp 's/\#/\?\?\=/g' $1 # #
sed -i.bkp 's/\~/\?\?\-/g' $1 # ~
```
