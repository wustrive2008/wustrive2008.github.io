title: Git钩子详解
date: 2016-12-02 23:12:11
categories: 版本控制
tags: [git,版本控制,项目管理,钩子函数]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexogit%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84.png
---

## Git钩子详解

### 钩子
Git钩子是在Git仓库中特定事件发生时自动运行的脚本。可以定制一些钩子，这些钩子可以在特定的情况下被执行，分为Client端的钩子和Server端的钩子。Client端钩子被operation触发，比如commit，merge等，Server端钩子被网络动作触发。

### 钩子位置
$project_name/.git/hooks

```bash
-rwxr-xr-x  1 wubaoguo  staff   478B 11  6 00:34 applypatch-msg.sample
-rwxr-xr-x  1 wubaoguo  staff   896B 11  6 00:34 commit-msg.sample
-rwxr-xr-x  1 wubaoguo  staff   189B 11  6 00:34 post-update.sample
-rwxr-xr-x  1 wubaoguo  staff   424B 11  6 00:34 pre-applypatch.sample
-rwxr-xr-x  1 wubaoguo  staff   1.6K 11  6 00:34 pre-commit.sample
-rwxr-xr-x  1 wubaoguo  staff   1.3K 11  6 00:34 pre-push.sample
-rwxr-xr-x  1 wubaoguo  staff   4.8K 11  6 00:34 pre-rebase.sample
-rwxr-xr-x  1 wubaoguo  staff   1.2K 11  6 00:34 prepare-commit-msg.sample
-rwxr-xr-x  1 wubaoguo  staff   3.5K 11  6 00:34 update.sample
```

### 常见钩子

- pre-commit  pre-commit脚本在每次你运行git commit命令时，Git向你询问提交信息或者产生提交对象时被执行。
- prepare-commit-msg  prepare-commit-msg钩子在pre-commit钩子在文本编辑器中生成提交信息之后被调用。这被用来方便地修改自动生成的squash或merge提交。
- commit-msg  commit-msg钩子和prepare-commit-msg钩子很像，但它会在用户输入提交信息之后被调用。
- post-commit  post-commit钩子在commit-msg钩子之后立即被运行 。
- post-checkout  post-checkout钩子和post-commit钩子很像，但它在你用git checkout查看引用的时候被调用。
- pre-rebase  pre-rebase钩子在git rebase发生更改之前运行
- pre-receive p re-receive钩子在有人用git push向仓库推送代码时被执行。
- update  update钩子在pre-receive之后被调用，分别被每个推送上来的引用分别调用。
- post-receive  post-receive钩子在成功推送后被调用，适合用于发送通知。

### 钩子使用示例

通过post-commit发送commit通知

```python
#!/usr/bin/env python

import smtplib
from email.mime.text import MIMEText
from subprocess import check_output

# 获得新提交的git log --stat输出
log = check_output(['git', 'log', '-1', '--stat', 'HEAD'])

# 创建一个纯文本的邮件内容
msg = MIMEText("Look, I'm actually doing some work:\n\n%s" % log)

msg['Subject'] = 'Git post-commit hook notification'
msg['From'] = 'mary@example.com'
msg['To'] = 'boss@example.com'

# 发送信息
SMTP_SERVER = 'smtp.example.com'
SMTP_PORT = 587

session = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
session.ehlo()
session.starttls()
session.ehlo()
session.login(msg['From'], 'secretPassword')

session.sendmail(msg['From'], msg['To'], msg.as_string())
session.quit()

```


### 参考

[Git钩子：自定义你的工作流](https://github.com/geeeeeeeeek/git-recipes/wiki/5.4-Git%E9%92%A9%E5%AD%90%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81)

