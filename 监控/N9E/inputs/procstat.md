### 关于categraf的procstats插件的一些使用心得



#### 通过路径模糊匹配可执行程序（规范的服务路径可以省却大量的进程指标采集的配置功夫）
```toml
# # executable name (ie, pgrep <search_exec_substring>)
search_exec_substring = "nginx"

# # pattern as argument for pgrep (ie, pgrep -f <search_cmdline_substring>)
search_cmdline_substring = "n9e server"
# 经过测试发现 这个可以匹配到程序的执行路径，如D:\\Application ，而且支持部分匹配如/usr/bin/python时可以用search_cmdline_substring = "usr"来匹配
# 实际上就是pgrep -f 能匹配到的，这个也能匹配到
#  pgrep -f, --full                use full process name to match

# # windows service name
search_win_service = ""
```