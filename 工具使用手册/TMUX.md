# Session
```
ctrl+b  D        #挂起当前tmux session
tmux new -s bob  #创建新的session 名字是bob
tmux ls          #显示tmux的session 列表
tmux a           #进入最近之前使用的session
tmux a -t 1      #进入序列号为1 的session
tmux kill-session bob  #关闭bob session
tmux kill-server  #关闭tmux
```

# Window
```
ctrl+b %       #竖向分割window
ctrl+b "       #横向分割windwo
ctrl+b q  1    #跳到序号对应的窗口
ctrl+b c       #创建新的window
ctrl+b n       #按需列循环跳转windows
ctrl+b ，      #重命名window
ctrl+b w       #显示session和windows列表，可选择
ctrl+b x       #关闭当前window
ctrl+b &       #关闭整个窗口
ctrl+b \[      #进入copy模式 使用空格选择  使用enter确定  
ctrl+b \]      #在copy模式中粘帖
```
