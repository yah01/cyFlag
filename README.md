# cyflag

## README.md
- [English](README.md)
- [中文](README.zh_CN.md)

*cyflag* is a lib for parsing flags(CLI arguments), which being used frequently in CLI(Command-Line Interface). it's easier to use and has less limitation than the go standard lib *flag*.

## QuickStart
```go
var (
		parser cyflag.Parser
		args   = "i love cyflag -best -times 95"

		best  bool
		times int
		love  string
	)
	parser.BoolVar(&best, "-best", false, "whether the best")
	parser.IntVar(&times, "-times", 0, "-times [int]")
	parser.StringVar(&love, "love", "something", "love [string]")

	if err := parser.ParseString(args);err!=nil {
		log.Println(err)
	}


	fmt.Printf("best: %+v\n"+
		"times: %+v\n"+
		"love: %+v\n",
		best, times, love)
```
Sample above will output
```yaml
best: true
times: 95
love: cyflag
```

## Usage

*cyflag* looks like go standard package [flag](https://golang.org/pkg/flag/), as well the usage.

### Bind variable with flag
*cyflag* could bind 4 kinds of variable:
- bool
- int (and int32, int64)
- float64 (and float32)
- string (and []byte)

They are all similar, and there are four methods for binding:
```go
parser.BoolVar(&boolVariable,"-bool",false,"it's a bool flag")
parser.IntVar(&intVariable,"-int",0,"it's a int flag")
parser.FloatVar(&float64Variable,"-float64",0.0,"it's a float64 flag")
parser.StringVar(&stringVariable,"-string","empty","it's a string flag")
```

For convenience, cyflag provides a method for binding variable of every type, and it's the only way to bind the variable of type which is not default type of cyflog(int32, int64, float32, []byte).
```go
parser.Bind(&variable,"-anytype","value with same type with variable","the usage")
```

The parameters of the methods above are:
1. the address of the variable
2. the flag name
3. default value
4. usage information

**The flag does not have to start with character '-'**, it's different with go standard lib *flag* that adding '-' automatically.

### Parse
**after all bindings having finished**, just call
```go
parser.Parse(args)
```
If args = nil, the parser will try to parse parser.LeftArgs, so you can store arguments into parser.LeftArgs and call Parse with a parameter nil.

*cyflag* will parse the arguments and store the value into the binding variables, then store the arguments it can't parse into parser.LeftArgs.

**Notice:** The arguments format rule is a little different with go standard lib *flag*:
- *flagname*: only way to parse bool variable
- *flagname value*: only way to parse non-bool variable

**There are no limitation in the order of arguments and flags**, which is totally different with go standard lib *flag*, the latter parses until meet first non-flag arguments.

For parsing `os.Args[1:]`, just call cyflag.BoolVar(...) (or any other binding function), and then call cyflag.Parse().
