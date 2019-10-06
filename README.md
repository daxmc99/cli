cli
===

[![GoDoc](https://godoc.org/github.com/urfave/cli?status.svg)](https://godoc.org/github.com/urfave/cli)
[![codebeat](https://codebeat.co/badges/0a8f30aa-f975-404b-b878-5fab3ae1cc5f)](https://codebeat.co/projects/github-com-urfave-cli)
[![Go Report Card](https://goreportcard.com/badge/urfave/cli)](https://goreportcard.com/report/urfave/cli)
[![codecov](https://codecov.io/gh/urfave/cli/branch/master/graph/badge.svg)](https://codecov.io/gh/urfave/cli)

cli is a simple, fast, and fun package for building command line apps in Go. The
goal is to enable developers to write fast and distributable command line
applications in an expressive way.

## Usage Documentation

Usage documentation exists for each major version. Don't know what version you're on? You're probably using the version from the `master` branch, which is currently `v2`.

- `v2` - [./docs/v2/manual.md](./docs/v2/manual.md)
- `v1` - [./docs/v1/manual.md](./docs/v1/manual.md)

## Installation

Make sure you have a working Go environment.  Go version 1.11+ is supported. [See the install instructions for Go](http://golang.org/doc/install.html).

Go Modules are strongly recommended when using this package. [See the go blog guide on using Go Modules](https://blog.golang.org/using-go-modules).

### Using `v2` releases

```
$ GO111MODULE=on go get github.com/urfave/cli/v2
```

```go
...
import (
  "github.com/urfave/cli/v2" // imports as package "cli"
)
...
```

### Using `v1` releases

```
$ GO111MODULE=on go get github.com/urfave/cli
```

```go
...
import (
  "github.com/urfave/cli"
)
...
```

### GOPATH

<<<<<<< HEAD
Make sure your `PATH` includes the `$GOPATH/bin` directory so your commands can
be easily used:
=======
## Getting Started

One of the philosophies behind cli is that an API should be playful and full of
discovery. So a cli app can be as little as one line of code in `main()`.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "A new cli application"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  err := cli.NewApp().Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

This app will run and show help text, but is not very useful. Let's give an
action to execute and some help documentation:

<!-- {
  "output": "boom! I say!"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()
  app.Name = "boom"
  app.Usage = "make an explosive entrance"
  app.Action = func(c *cli.Context) error {
    fmt.Println("boom! I say!")
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Running this already gives you a ton of functionality, plus support for things
like subcommands and flags, which are covered below.

## Examples

Being a programmer can be a lonely job. Thankfully by the power of automation
that is not the case! Let's create a greeter app to fend off our demons of
loneliness!

Start by creating a directory named `greet`, and within it, add a file,
`greet.go` with the following code in it:

<!-- {
  "output": "Hello friend!"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()
  app.Name = "greet"
  app.Usage = "fight the loneliness!"
  app.Action = func(c *cli.Context) error {
    fmt.Println("Hello friend!")
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Install our command to the `$GOPATH/bin` directory:

```
$ go install
```

Finally run our new command:

```
$ greet
Hello friend!
```

cli also generates neat help text:

```
$ greet help
NAME:
    greet - fight the loneliness!

USAGE:
    greet [global options] command [command options] [arguments...]

VERSION:
    0.0.0

COMMANDS:
    help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS
    --version Shows version information
```

### Arguments

You can lookup arguments by calling the `Args` function on `cli.Context`, e.g.:

<!-- {
  "output": "Hello \""
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Action = func(c *cli.Context) error {
    fmt.Printf("Hello %q", c.Args().Get(0))
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

### Flags

Setting and querying flags is simple.

<!-- {
  "output": "Hello Nefertiti"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang",
      Value: "english",
      Usage: "language for the greeting",
    },
  }

  app.Action = func(c *cli.Context) error {
    name := "Nefertiti"
    if c.NArg() > 0 {
      name = c.Args().Get(0)
    }
    if c.String("lang") == "spanish" {
      fmt.Println("Hola", name)
    } else {
      fmt.Println("Hello", name)
    }
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

You can also set a destination variable for a flag, to which the content will be
scanned.

<!-- {
  "output": "Hello someone"
} -->
``` go
package main

import (
  "log"
  "os"
  "fmt"

  "github.com/urfave/cli"
)

func main() {
  var language string

  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name:        "lang",
      Value:       "english",
      Usage:       "language for the greeting",
      Destination: &language,
    },
  }

  app.Action = func(c *cli.Context) error {
    name := "someone"
    if c.NArg() > 0 {
      name = c.Args()[0]
    }
    if language == "spanish" {
      fmt.Println("Hola", name)
    } else {
      fmt.Println("Hello", name)
    }
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

See full list of flags at http://godoc.org/github.com/urfave/cli

#### Placeholder Values

Sometimes it's useful to specify a flag's value within the usage string itself.
Such placeholders are indicated with back quotes.

For example this:

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "&#45;&#45;config FILE, &#45;c FILE"
} -->
```go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag{
    cli.StringFlag{
      Name:  "config, c",
      Usage: "Load configuration from `FILE`",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Will result in help output like:

```
--config FILE, -c FILE   Load configuration from FILE
```

Note that only the first placeholder is used. Subsequent back-quoted words will
be left as-is.

#### Alternate Names

You can set alternate (or short) names for flags by providing a comma-delimited
list for the `Name`. e.g.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "&#45;&#45;lang value, &#45;l value.*language for the greeting.*default: \"english\""
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang, l",
      Value: "english",
      Usage: "language for the greeting",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

That flag can then be set with `--lang spanish` or `-l spanish`. Note that
giving two different forms of the same flag in the same command invocation is an
error.

#### Ordering

Flags for the application and commands are shown in the order they are defined.
However, it's possible to sort them from outside this library by using `FlagsByName`
or `CommandsByName` with `sort`.

For example this:

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "add a task to the list\n.*complete a task on the list\n.*\n\n.*\n.*Load configuration from FILE\n.*Language for the greeting.*"
} -->
``` go
package main

import (
  "log"
  "os"
  "sort"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang, l",
      Value: "english",
      Usage: "Language for the greeting",
    },
    cli.StringFlag{
      Name: "config, c",
      Usage: "Load configuration from `FILE`",
    },
  }

  app.Commands = []cli.Command{
    {
      Name:    "complete",
      Aliases: []string{"c"},
      Usage:   "complete a task on the list",
      Action:  func(c *cli.Context) error {
        return nil
      },
    },
    {
      Name:    "add",
      Aliases: []string{"a"},
      Usage:   "add a task to the list",
      Action:  func(c *cli.Context) error {
        return nil
      },
    },
  }

  sort.Sort(cli.FlagsByName(app.Flags))
  sort.Sort(cli.CommandsByName(app.Commands))

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Will result in help output like:

```
--config FILE, -c FILE  Load configuration from FILE
--lang value, -l value  Language for the greeting (default: "english")
```

#### Values from the Environment

You can also have the default value set from the environment via `EnvVar`.  e.g.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "language for the greeting.*APP_LANG"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang, l",
      Value: "english",
      Usage: "language for the greeting",
      EnvVar: "APP_LANG",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

The `EnvVar` may also be given as a comma-delimited "cascade", where the first
environment variable that resolves is used as the default.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "language for the greeting.*LEGACY_COMPAT_LANG.*APP_LANG.*LANG"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang, l",
      Value: "english",
      Usage: "language for the greeting",
      EnvVar: "LEGACY_COMPAT_LANG,APP_LANG,LANG",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

#### Values from files

You can also have the default value set from file via `FilePath`.  e.g.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "password for the mysql database"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "password, p",
      Usage: "password for the mysql database",
      FilePath: "/etc/mysql/password",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Note that default values set from file (e.g. `FilePath`) take precedence over
default values set from the environment (e.g. `EnvVar`).

#### Values from alternate input sources (YAML, TOML, and others)

There is a separate package altsrc that adds support for getting flag values
from other file input sources.

Currently supported input source formats:
* YAML
* JSON
* TOML

In order to get values for a flag from an alternate input source the following
code would be added to wrap an existing cli.Flag like below:

``` go
  altsrc.NewIntFlag(cli.IntFlag{Name: "test"})
```

Initialization must also occur for these flags. Below is an example initializing
getting data from a yaml file below.

``` go
  command.Before = altsrc.InitInputSourceWithContext(command.Flags, NewYamlSourceFromFlagFunc("load"))
```

The code above will use the "load" string as a flag name to get the file name of
a yaml file from the cli.Context.  It will then use that file name to initialize
the yaml input source for any flags that are defined on that command.  As a note
the "load" flag used would also have to be defined on the command flags in order
for this code snipped to work.

Currently only YAML, JSON, and TOML files are supported but developers can add support
for other input sources by implementing the altsrc.InputSourceContext for their
given sources.

Here is a more complete sample of a command using YAML support:

<!-- {
  "args": ["test-cmd", "&#45;&#45;help"],
  "output": "&#45&#45;test value.*default: 0"
} -->
``` go
package notmain

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
  "github.com/urfave/cli/altsrc"
)

func main() {
  app := cli.NewApp()

  flags := []cli.Flag{
    altsrc.NewIntFlag(cli.IntFlag{Name: "test"}),
    cli.StringFlag{Name: "load"},
  }

  app.Action = func(c *cli.Context) error {
    fmt.Println("yaml ist rad")
    return nil
  }

  app.Before = altsrc.InitInputSourceWithContext(flags, altsrc.NewYamlSourceFromFlagFunc("load"))
  app.Flags = flags

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

#### Precedence

The precedence for flag value sources is as follows (highest to lowest):

0. Command line flag value from user
0. Environment variable (if specified)
0. Configuration file (if specified)
0. Default defined on the flag

### Subcommands

Subcommands can be defined for a more git-like command line app.

<!-- {
  "args": ["template", "add"],
  "output": "new task template: .+"
} -->
```go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Commands = []cli.Command{
    {
      Name:    "add",
      Aliases: []string{"a"},
      Usage:   "add a task to the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("added task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:    "complete",
      Aliases: []string{"c"},
      Usage:   "complete a task on the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("completed task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:        "template",
      Aliases:     []string{"t"},
      Usage:       "options for task templates",
      Subcommands: []cli.Command{
        {
          Name:  "add",
          Usage: "add a new template",
          Action: func(c *cli.Context) error {
            fmt.Println("new task template: ", c.Args().First())
            return nil
          },
        },
        {
          Name:  "remove",
          Usage: "remove an existing template",
          Action: func(c *cli.Context) error {
            fmt.Println("removed task template: ", c.Args().First())
            return nil
          },
        },
      },
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

### Subcommands categories

For additional organization in apps that have many subcommands, you can
associate a category for each command to group them together in the help
output.

E.g.

```go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Commands = []cli.Command{
    {
      Name: "noop",
    },
    {
      Name:     "add",
      Category: "Template actions",
    },
    {
      Name:     "remove",
      Category: "Template actions",
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Will include:

```
COMMANDS:
  noop

  Template actions:
    add
    remove
```

### Exit code

Calling `App.Run` will not automatically call `os.Exit`, which means that by
default the exit code will "fall through" to being `0`.  An explicit exit code
may be set by returning a non-nil error that fulfills `cli.ExitCoder`, *or* a
`cli.MultiError` that includes an error that fulfills `cli.ExitCoder`, e.g.:
<!-- {
  "error": "Ginger croutons are not in the soup"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()
  app.Flags = []cli.Flag{
    cli.BoolFlag{
      Name:  "ginger-crouton",
      Usage: "Add ginger croutons to the soup",
    },
  }
  app.Action = func(ctx *cli.Context) error {
    if !ctx.Bool("ginger-crouton") {
      return cli.NewExitError("Ginger croutons are not in the soup", 86)
    }
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

### Combining short options

Traditional use of options using their shortnames look like this:

```
$ cmd -s -o -m "Some message"
```

Suppose you want users to be able to combine options with their shortnames. This
can be done using the `UseShortOptionHandling` bool in your app configuration,
or for individual commands by attaching it to the command configuration. For
example:

<!-- {
  "args": ["short", "&#45;som", "Some message"],
  "output": "serve: true\noption: true\nmessage: Some message\n"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()
  app.UseShortOptionHandling = true
  app.Commands = []cli.Command{
    {
      Name:  "short",
      Usage: "complete a task on the list",
      Flags: []cli.Flag{
        cli.BoolFlag{Name: "serve, s"},
        cli.BoolFlag{Name: "option, o"},
        cli.StringFlag{Name: "message, m"},
      },
      Action: func(c *cli.Context) error {
        fmt.Println("serve:", c.Bool("serve"))
        fmt.Println("option:", c.Bool("option"))
        fmt.Println("message:", c.String("message"))
        return nil
      },
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

If your program has any number of bool flags such as `serve` and `option`, and
optionally one non-bool flag `message`, with the short options of `-s`, `-o`,
and `-m` respectively, setting `UseShortOptionHandling` will also support the
following syntax:

```
$ cmd -som "Some message"
```

If you enable `UseShortOptionHandling`, then you must not use any flags that
have a single leading `-` or this will result in failures. For example,
`-option` can no longer be used. Flags with two leading dashes (such as
`--options`) are still valid.

### Bash Completion

You can enable completion commands by setting the `EnableBashCompletion`
flag on the `App` object to `true`.  By default, this setting will allow 
auto-completion for an app's subcommands, but you can write your own completion 
methods for the App or its subcommands as well.

#### Default auto-completion

```go
package main

import (
	"fmt"
	"log"
	"os"

	"github.com/urfave/cli"
)

func main() {
	app := cli.NewApp()
	app.EnableBashCompletion = true
	app.Commands = []cli.Command{
		{
			Name:    "add",
			Aliases: []string{"a"},
			Usage:   "add a task to the list",
			Action: func(c *cli.Context) error {
				fmt.Println("added task: ", c.Args().First())
				return nil
			},
		},
		{
			Name:    "complete",
			Aliases: []string{"c"},
			Usage:   "complete a task on the list",
			Action: func(c *cli.Context) error {
				fmt.Println("completed task: ", c.Args().First())
				return nil
			},
		},
		{
			Name:    "template",
			Aliases: []string{"t"},
			Usage:   "options for task templates",
			Subcommands: []cli.Command{
				{
					Name:  "add",
					Usage: "add a new template",
					Action: func(c *cli.Context) error {
						fmt.Println("new task template: ", c.Args().First())
						return nil
					},
				},
				{
					Name:  "remove",
					Usage: "remove an existing template",
					Action: func(c *cli.Context) error {
						fmt.Println("removed task template: ", c.Args().First())
						return nil
					},
				},
			},
		},
	}

	err := app.Run(os.Args)
	if err != nil {
		log.Fatal(err)
	}
}

```

#### Custom auto-completion
<!-- {
  "args": ["complete", "&#45;&#45;generate&#45;bash&#45;completion"],
  "output": "laundry"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  tasks := []string{"cook", "clean", "laundry", "eat", "sleep", "code"}

  app := cli.NewApp()
  app.EnableBashCompletion = true
  app.Commands = []cli.Command{
    {
      Name:  "complete",
      Aliases: []string{"c"},
      Usage: "complete a task on the list",
      Action: func(c *cli.Context) error {
         fmt.Println("completed task: ", c.Args().First())
         return nil
      },
      BashComplete: func(c *cli.Context) {
        // This will complete if no args are passed
        if c.NArg() > 0 {
          return
        }
        for _, t := range tasks {
          fmt.Println(t)
        }
      },
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

#### Enabling

To enable auto-completion for the current shell session, a bash script, 
`autocomplete/bash_autocomplete` is included in this repo.

To use `autocomplete/bash_autocomplete`, set an environment variable named `PROG` to 
the name of your program, and then `source` the `autocomplete/bash_autocomplete` file.

For example, if your cli program is called `myprogram`:

`PROG=myprogram source path/to/cli/autocomplete/bash_autocomplete`

Auto-completion is now enabled for the current shell, but will not persist to a new shell.

#### Distribution and Persistent Autocompletion

Copy `autocomplete/bash_autocomplete` into `/etc/bash_completion.d/` and rename
it to the name of the program you wish to add auto-complete support for (or
automatically install it there if you are distributing a package). Don't forget
to source the file or restart your shell to activate the auto-completion.

```
sudo cp path/to/autocomplete/bash_autocomplete /etc/bash_completion.d/<myprogram>
source /etc/bash_completion.d/<myprogram>
```

Alternatively, you can just document that users should source the generic
`autocomplete/bash_autocomplete` and set `$PROG` within their bash configuration
file, adding these lines:

```
PROG=<myprogram>
source path/to/cli/autocomplete/bash_autocomplete
```
Keep in mind that if they are enabling auto-completion for more than one program, 
they will need to set `PROG` and source `autocomplete/bash_autocomplete` for each 
program, like so:

```
PROG=<program1>
source path/to/cli/autocomplete/bash_autocomplete
PROG=<program2>
source path/to/cli/autocomplete/bash_autocomplete
```

#### Customization

The default bash completion flag (`--generate-bash-completion`) is defined as
`cli.BashCompletionFlag`, and may be redefined if desired, e.g.:

<!-- {
  "args": ["&#45;&#45;compgen"],
  "output": "wat\nhelp\nh"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  cli.BashCompletionFlag = cli.BoolFlag{
    Name:   "compgen",
    Hidden: true,
  }

  app := cli.NewApp()
  app.EnableBashCompletion = true
  app.Commands = []cli.Command{
    {
      Name: "wat",
    },
  }
  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

#### ZSH Support
Auto-completion for ZSH is also supported using the `autocomplete/zsh_autocomplete` 
file included in this repo. Two environment variables are used, `PROG` and `_CLI_ZSH_AUTOCOMPLETE_HACK`. 
Set `PROG` to the program name as before, set `_CLI_ZSH_AUTOCOMPLETE_HACK` to `1`, and 
then `source path/to/autocomplete/zsh_autocomplete`. Adding the following lines to your ZSH 
configuration file (usually `.zshrc`) will allow the auto-completion to persist across new shells:

```
PROG=<myprogram>
_CLI_ZSH_AUTOCOMPLETE_HACK=1
source  path/to/autocomplete/zsh_autocomplete
```

### Generated Help Text

The default help flag (`-h/--help`) is defined as `cli.HelpFlag` and is checked
by the cli internals in order to print generated help text for the app, command,
or subcommand, and break execution.

#### Customization

All of the help text generation may be customized, and at multiple levels.  The
templates are exposed as variables `AppHelpTemplate`, `CommandHelpTemplate`, and
`SubcommandHelpTemplate` which may be reassigned or augmented, and full override
is possible by assigning a compatible func to the `cli.HelpPrinter` variable,
e.g.:

<!-- {
  "output": "Ha HA.  I pwnd the help!!1"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "io"
  "os"

  "github.com/urfave/cli"
)

func main() {
  // EXAMPLE: Append to an existing template
  cli.AppHelpTemplate = fmt.Sprintf(`%s

WEBSITE: http://awesometown.example.com

SUPPORT: support@awesometown.example.com

`, cli.AppHelpTemplate)

  // EXAMPLE: Override a template
  cli.AppHelpTemplate = `NAME:
   {{.Name}} - {{.Usage}}
USAGE:
   {{.HelpName}} {{if .VisibleFlags}}[global options]{{end}}{{if .Commands}} command [command options]{{end}} {{if .ArgsUsage}}{{.ArgsUsage}}{{else}}[arguments...]{{end}}
   {{if len .Authors}}
AUTHOR:
   {{range .Authors}}{{ . }}{{end}}
   {{end}}{{if .Commands}}
COMMANDS:
{{range .Commands}}{{if not .HideHelp}}   {{join .Names ", "}}{{ "\t"}}{{.Usage}}{{ "\n" }}{{end}}{{end}}{{end}}{{if .VisibleFlags}}
GLOBAL OPTIONS:
   {{range .VisibleFlags}}{{.}}
   {{end}}{{end}}{{if .Copyright }}
COPYRIGHT:
   {{.Copyright}}
   {{end}}{{if .Version}}
VERSION:
   {{.Version}}
   {{end}}
`

  // EXAMPLE: Replace the `HelpPrinter` func
  cli.HelpPrinter = func(w io.Writer, templ string, data interface{}) {
    fmt.Println("Ha HA.  I pwnd the help!!1")
  }

  err := cli.NewApp().Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

The default flag may be customized to something other than `-h/--help` by
setting `cli.HelpFlag`, e.g.:

<!-- {
  "args": ["&#45;&#45halp"],
  "output": "haaaaalp.*HALP"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  cli.HelpFlag = cli.BoolFlag{
    Name: "halp, haaaaalp",
    Usage: "HALP",
    EnvVar: "SHOW_HALP,HALPPLZ",
  }

  err := cli.NewApp().Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

### Version Flag

The default version flag (`-v/--version`) is defined as `cli.VersionFlag`, which
is checked by the cli internals in order to print the `App.Version` via
`cli.VersionPrinter` and break execution.

#### Customization

The default flag may be customized to something other than `-v/--version` by
setting `cli.VersionFlag`, e.g.:

<!-- {
  "args": ["&#45;&#45print-version"],
  "output": "partay version 19\\.99\\.0"
} -->
``` go
package main

import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  cli.VersionFlag = cli.BoolFlag{
    Name: "print-version, V",
    Usage: "print only the version",
  }

  app := cli.NewApp()
  app.Name = "partay"
  app.Version = "19.99.0"
  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

Alternatively, the version printer at `cli.VersionPrinter` may be overridden, e.g.:

<!-- {
  "args": ["&#45;&#45version"],
  "output": "version=19\\.99\\.0 revision=fafafaf"
} -->
``` go
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

var (
  Revision = "fafafaf"
)

func main() {
  cli.VersionPrinter = func(c *cli.Context) {
    fmt.Printf("version=%s revision=%s\n", c.App.Version, Revision)
  }

  app := cli.NewApp()
  app.Name = "partay"
  app.Version = "19.99.0"
  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
>>>>>>> Added default auto-completion example, clarified enabling bash autocompletion, added zsh autocompletion section
```
export PATH=$PATH:$GOPATH/bin
```

### Supported platforms

cli is tested against multiple versions of Go on Linux, and against the latest
released version of Go on OS X and Windows. This project uses Github Actions for
builds. For more build info, please look at the [./.github/workflows/cli.yml](https://github.com/urfave/cli/blob/master/.github/workflows/cli.yml).
