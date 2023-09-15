### exec插件
#### exec.toml文件
```toml
# # collect interval
# interval = 15

[[instances]]
# # commands, support glob
commands = [
     "/opt/categraf/scripts/*/collect_*.sh"
     #"/opt/categraf/scripts/*/collect_*.py"
     #"/opt/categraf/scripts/*/collect_*.go"
     #"/opt/categraf/scripts/*/collect_*.lua"
     #"/opt/categraf/scripts/*/collect_*.java"
     #"/opt/categraf/scripts/*/collect_*.bat"
     #"/opt/categraf/scripts/*/collect_*.cmd"
     #"/opt/categraf/scripts/*/collect_*.ps1"
]

# # timeout for each command to complete
# timeout = 5

# # interval = global.interval * interval_times
# interval_times = 1

# # choices: influx prometheus falcon
# # mesurement,labelkey1=labelval1,labelkey2=labelval2 field1=1.2,field2=2.3
data_format = "prometheus"
```

#### commands中的命令是顺序执行的，还是并发执行的，有没有相关的代码证明
commands中的所有命令都是并发执行的，具体的执行逻辑可见其源码，https://github.com/flashcatcloud/categraf/tree/main/inputs/exec
从go ins.ProcessCommand(slist, command, &waitCommands)这行代码可知，是goroutine并发执行的
```go
func (ins *Instance) Gather(slist *types.SampleList) {
	var commands []string
	for _, pattern := range ins.Commands {
		cmdAndArgs := strings.SplitN(pattern, " ", 2)
		if len(cmdAndArgs) == 0 {
			continue
		}

		matches, err := filepath.Glob(cmdAndArgs[0])
		if err != nil {
			log.Println("E! failed to get filepath glob of commands:", err)
			continue
		}

		if len(matches) == 0 {
			// There were no matches with the glob pattern, so let's assume
			// that the command is in PATH and just run it as it is
			commands = append(commands, pattern)
		} else {
			// There were matches, so we'll append each match together with
			// the arguments to the commands slice
			for _, match := range matches {
				if len(cmdAndArgs) == 1 {
					commands = append(commands, match)
				} else {
					commands = append(commands,
						strings.Join([]string{match, cmdAndArgs[1]}, " "))
				}
			}
		}
	}

	if len(commands) == 0 {
		log.Println("W! no commands after parse")
		return
	}

	var waitCommands sync.WaitGroup
	waitCommands.Add(len(commands))
	for _, command := range commands {
		go ins.ProcessCommand(slist, command, &waitCommands)
	}

	waitCommands.Wait()
}
```

#### 我有个脚本，在服务器本地手动执行是正常的，但是通过categraf执行的时候总是不正常；且用户都是root执行的，请问什么原因会造成这种现象
脚本中的环境变量问题，categraf看到的环境变量跟本地手动执行看到的环境变量是不一样的；因此脚本中的命令要使用绝对路径避免这个问题