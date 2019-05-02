# deepin-terminal

主要是对deepin的终端两处修改:边框样式和远程管理,修改的最好备份下,系统名称更新就会被覆盖

## 边框样式

用linux主要是用它的terminal,但是deepin的terminal不是很理想,我也在[github](https://github.com/linuxdeepin/deepin-terminal/issues/133)上提交了issues但是没有人回复,当你在一个窗口工作时还好,但是窗口多打开多时不会找不到你用的窗口,于是决定自己修改它的样式.

`/usr/share/deepin-terminal` 样式主要是在这个目录下的`style.css` 我也添加了一些注释,tab菜单样式在`theme`目录下,修改完成后在命令窗口执行`reset`生效

## 远程管理

win下面对应的是`xshell`工具,但是在deepin系统里面你可以直接用自带的terminal,功能很强大,自带分屏,远程远程,但是安装完成后基本是废的,它默认使用的zsh远程链接的,同样我也在[github](https://github.com/linuxdeepin/deepin-terminal/issues/116) 上提交了issues,但是被他们的开发一直搪塞,于是决定自己修改他的代码, `/usr/lib/deepin-terminal`  目录下的`ssh_login.sh`文件直接修改

```shell
## Main
# Delete self for secret, will not affect the following code
file delete $argv0

# Setup variables
# Set timeout -1 to avoid remote server dis-connect.
set timeout -1
set user {<<USER>>}
set server {<<SERVER>>}
set password {<<PASSWORD>>}
set private_key {<<PRIVATE_KEY>>}
set port {<<PORT>>}
set authentication {<<AUTHENTICATION>>}
set ssh_cmd {/usr/bin/ssh}
set ssh_opt {$user@$server -p $port -o ConnectTimeout=5}
set remote_command {<<REMOTE_COMMAND>>}

# This code is use for synchronous pty's size to avoid terminal not update if login in remote server.
trap {
    stty rows [stty rows] columns [stty columns] < $spawn_out(slave,name)
} WINCH

# Spawn and expect
eval spawn $ssh_cmd $ssh_opt
if {[string length $password]} {
    expect {
        timeout {send_user "ssh connection time out, please operate manually\n"}
        -nocase "(yes/no)\\?" {send "yes\r"; exp_continue}
        -nocase -re "password:|enter passphrase for key" {
            send "$password\r"
		}
	}
	
}
interact
```

