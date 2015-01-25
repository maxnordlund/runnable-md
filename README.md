[ ]: ];eval "$(
  sed -n '/^```/ {s|^```|#!/usr/bin/env |p;q;}' <$0
  sed -n '/^```/,/^```/ s/^[^`][^`][^`]/&/p' <$0
  echo exit \$?
);exit \$?"
# Executable Markdown
Instead of having a seperate file conatining the code inside your Markdown, you
should be able to run it diretly. This also allows for some very nice literate
programming.

## How to use
Prepend your Markdown file with the following to make it executable. It needs
to be run using your shell, `sh README.md`.

~~~markdown
[ ]: ];eval "$(
  sed -n '/^```/ {s|^```|#!/usr/bin/env |p;q;}' <$0
  sed -n '/^```/,/^```/ s/^[^`][^`][^`]/&/p' <$0
  echo exit \$?
);exit \$?"
~~~

## How it's done
By prepending the file with a special link reference, that also happens to be
valid shell script, it can parse itself and feed that to your shell.

The magical string is of the format: `[ ]: ];eval "$(...);exit \$?"`

The parsing is done using `sed`. To execute different languages correctly the
resulting script needs to have a shebang, e.g. `#!/usr/bin/env ...`. This is
done by finding the first code fence and extracting its language. This is
assumed to be usable in a `#!/usr/bin/env ...` context.

For the rest of the program all lines between code fences are extracted and
concatanated using another `sed` script. However to allow just a part of a file
to be executed you can use an alternative code fencing marker, e.g. tilde.

This is used above to hide the Markdown to ensure the example will work.

## Example
To demonstrate this trick, this README contains the trick and the following
will be executed if you run this file.

First code fence sets the executing enviroment:
```bash
echo $SHELL # /bin/bash
export SOMETHING="nice"
```

All code fences get concatenated before execution, effectivly sharing the same
variable scope.
```bash
echo $SOMETHING # nice
```

