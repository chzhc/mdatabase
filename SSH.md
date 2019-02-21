# SSH keys (简体中文)

==大致意思是，本地做一把锁，配一把钥匙，然后把锁(公钥)发送给对方，然后身份验证只需要通过验证我的钥匙能不能打开你的锁，能打开就是本人==

SSH 密钥对可以让您方便的登录到 SSH 服务器，而无需输入密码。由于您无需发送您的密码到网络中，SSH 密钥对被认为是更加安全的方式。再加上使用密码短语 (passphrase) 的使用，安全性会更上一层楼。

同时，我们可以使用 SSH agent 来帮助我们记住密码短语，无需我们记住每一个密钥对的密码短语，减轻了我们的负担。

本文将为您介绍如何管理密钥对，以方便的连接到您的 SSH 服务器。本文默认您已经熟知 [SSH](https://wiki.archlinux.org/index.php/Secure_Shell_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，并安装好位于[官方软件仓库](https://wiki.archlinux.org/index.php/Official_repositories_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 的 [openssh](https://www.archlinux.org/packages/?name=openssh)。

## Contents

 [[hide]()] 

- [1背景](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E8.83.8C.E6.99.AF)
- 2生成密钥对
  - [2.1选择合适的加密方式](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E9.80.89.E6.8B.A9.E5.90.88.E9.80.82.E7.9A.84.E5.8A.A0.E5.AF.86.E6.96.B9.E5.BC.8F)
  - 2.2选择密钥存储位置以及密码短语
    - [2.2.1不修改密钥对的情况下修改密码短语](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E4.B8.8D.E4.BF.AE.E6.94.B9.E5.AF.86.E9.92.A5.E5.AF.B9.E7.9A.84.E6.83.85.E5.86.B5.E4.B8.8B.E4.BF.AE.E6.94.B9.E5.AF.86.E7.A0.81.E7.9F.AD.E8.AF.AD)
    - [2.2.2管理多组密钥对](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E7.AE.A1.E7.90.86.E5.A4.9A.E7.BB.84.E5.AF.86.E9.92.A5.E5.AF.B9)
- 3将公钥复制到远程服务器上
  - [3.1简单的方法](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E7.AE.80.E5.8D.95.E7.9A.84.E6.96.B9.E6.B3.95)
  - [3.2传统的方法](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E4.BC.A0.E7.BB.9F.E7.9A.84.E6.96.B9.E6.B3.95)
- 4安全性
  - [4.1保证 authorized_keys 文件的安全](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E4.BF.9D.E8.AF.81_authorized_keys_.E6.96.87.E4.BB.B6.E7.9A.84.E5.AE.89.E5.85.A8)
  - [4.2禁用密码登录](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E7.A6.81.E7.94.A8.E5.AF.86.E7.A0.81.E7.99.BB.E5.BD.95)
  - [4.3双因素认证与公钥](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.8F.8C.E5.9B.A0.E7.B4.A0.E8.AE.A4.E8.AF.81.E4.B8.8E.E5.85.AC.E9.92.A5)
- 5SSH agents
  - 5.1ssh-agent
    - [5.1.1ssh-agent 作为包装程序运行](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#ssh-agent_.E4.BD.9C.E4.B8.BA.E5.8C.85.E8.A3.85.E7.A8.8B.E5.BA.8F.E8.BF.90.E8.A1.8C)
  - [5.2GnuPG](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#GnuPG)
  - 5.3Keychain
    - [5.3.1另一种启动 keychain 的方式](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.8F.A6.E4.B8.80.E7.A7.8D.E5.90.AF.E5.8A.A8_keychain_.E7.9A.84.E6.96.B9.E5.BC.8F)
  - 5.4envoy
    - [5.4.1利用 KDE 电子钱包存储密码短语并和 envoy 配合工作](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.88.A9.E7.94.A8_KDE_.E7.94.B5.E5.AD.90.E9.92.B1.E5.8C.85.E5.AD.98.E5.82.A8.E5.AF.86.E7.A0.81.E7.9F.AD.E8.AF.AD.E5.B9.B6.E5.92.8C_envoy_.E9.85.8D.E5.90.88.E5.B7.A5.E4.BD.9C)
  - 5.5pam_ssh
    - [5.5.1pam_ssh 已知问题](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#pam_ssh_.E5.B7.B2.E7.9F.A5.E9.97.AE.E9.A2.98)
  - [5.6GNOME Keyring](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#GNOME_Keyring)
- 6疑难排解
  - [6.1使用 kdm](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E4.BD.BF.E7.94.A8_kdm)

## 背景

SSH 密钥对总是成双出现的，一把公钥，一把私钥。公钥可以自由的放在您所需要连接的 SSH 服务器上，而私钥必须稳妥的保管好。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录 shell，不再要求密码。这样子，我们即可保证了整个登录过程的安全，也不会受到中间人攻击。

## 生成密钥对

我们可以使用 `ssh-keygen` 命令生成密钥对

```
$ ssh-keygen -t ecdsa -b 521 -C "$(whoami)@$(hostname)-$(date -I)"
```

```
Generating public/private ecdsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_ecdsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/username/.ssh/id_ecdsa.
Your public key has been saved in /home/username/.ssh/id_ecdsa.pub.
The key fingerprint is:
dd:15:ee:24:20:14:11:01:b8:72:a2:0f:99:4c:79:7f username@localhost-2011-12-22
The key's randomart image is:
+--[ECDSA  521]---+
|     ..oB=.   .  |
|    .    . . . . |
|  .  .      . +  |
| oo.o    . . =   |
|o+.+.   S . . .  |
|=.   . E         |
| o    .          |
|  .              |
|                 |
+-----------------+
```

在上面这个例子中，`ssh-keygen` 生成了一对长度为 521 bit (`-b 521`) 的 ECDSA (`-t ecdsa`) 加密的密钥对，comment 为 `-C "$(whoami)@$(hostname)-$(date -I)"`。而 [randomart image](http://www.cs.berkeley.edu/~dawnsong/papers/randomart.pdf) 是 OpenSSH 5.1 引入的一种简单的识别指纹 (fingerprint) 的图像。

### 选择合适的加密方式

椭圆曲线数字签名算法 (ECDSA) 生成的密钥更小，安全性更高。OpenSSH 5.7 建议默认使用 ECDSA，详情参见 [OpenSSH 5.7 Release Notes](http://www.openssh.com/txt/release-5.7)。较旧的 OpenSSH 版本可能不支持 ECDSA 密钥，需要注意。而一些厂商因专利问题，暂未提供 ECDSA 的实现。

**注意:** 截至 2014 年06 月 10 日，这个 [GNOME bug](https://bugzilla.gnome.org/show_bug.cgi?id=641082) 导致 Gnome Keyring 暂不支持 ECDSA。

如果您要生成 RSA (768-16384 bit) 或者 DSA (1024 bit) 密钥对，需要使用 `-t rsa` 或者 `-t dsa`，并修改 `-b` 选项。`-b` 可以省略，`ssh-keygen` 会生成一个默认大小的密钥对。

**注意:** 这些密钥对是用于认证的，选择更加复杂的密钥类型并不会在登录时加重您的 CPU 负担。

### 选择密钥存储位置以及密码短语

输入 `ssh-keygen` 时，它会询问您将密钥对保存到何处，文件名如何命令等。默认情况下，密钥对保存到 `~/.ssh` 下，文件名则根据加密类型自动命名为 `id_ecdsa` (私钥)，`id_ecdsa.pub`(公钥)。建议您采用默认的存储位置和文件名。

而在 `ssh-keygen` 请求您输入一个密码短语时，您应该输入一些难以猜到的短语。如果短语足够随机和复杂，则私钥落入贼人之手时就不会容易被破解掉。

当然，您也可以不输入任何密码短语，也能够生成所需的密钥对。虽然这用起来挺方便的，但是您应该知道这会很危险。在没有输入密码短语的情况下，您的私钥未经加密就存储在您的硬盘上，任何人拿到您的私钥都可以随意的访问对应的 SSH 服务器。还有一种情况，如果您不是 `root` 用户，则该机器上的 `root` 用户可以完全拥有您的密钥对，因为他的权限是最大的。

#### 不修改密钥对的情况下修改密码短语

您可以使用 `ssh-keygen` 命令来修改密码短语，而无需改动密钥对。假设您要修改的密钥对使用 RSA 加密，输入以下命令即可：

```
$ ssh-keygen -f ~/.ssh/id_rsa -p
```

#### 管理多组密钥对

您可以创建 `~/.ssh/config` 来管理多组密钥对，每一个 SSH 服务器对应一组密钥对。或者，您甚至可以对所有的 SSH 服务器使用同一组密钥对。不过如果您觉得这样不合适，还是编辑配置文件：

```
~/.ssh/config
```

```
Host SERVERNAME1
  IdentitiesOnly yes
  IdentityFile ~/.ssh/id_rsa_SERVER1
  # CheckHostIP yes
  # Port 22
Host SERVERNAME2
  IdentitiesOnly yes
  IdentityFile ~/.ssh/id_rsa_SERVER2
  # CheckHostIP no
  # Port 2177
ControlMaster auto
ControlPath /tmp/%r@%h:%p
```

更多选项帮助请参考

```
$ man ssh_config 5
```

## 将公钥复制到远程服务器上

创建好密钥对之后，您需要将公钥上传到远程服务器上，以便用于 SSH 密钥认证登录。公钥文件名和私钥文件名相同，只不过公钥文件带有扩展名 `.pub` 而私钥文件名则没有。千万不要将私钥上传，私钥应该保存在本地。

### 简单的方法

**注意:** 如果您的远程服务器默认使用的是 non-`sh` 的 shell，比如 `tcsh`，则此方法可能不奏效。详情参见这个 [bug](https://bugzilla.redhat.com/show_bug.cgi?id=1045191)。

**注意:** 如果使用以下两种方法外的方法请不要忘记注册公钥文件，您只需要命令

```
$ cat ~/.ssh/id_ecdsa.pub >> ~/.ssh/authorized_keys
```

如果您的公钥文件为 `~/.ssh/id_rsa.pub`，您只需要输入命令

```
$ ssh-copy-id remote-server.org
```

如果您的远程服务器用户名与本地的不同，您需要指明用户名

```
$ ssh-copy-id username@remote-server.org
```

如果您的公钥文件名不是默认的，您会得到错误 `/usr/bin/ssh-copy-id: ERROR: No identities found`。这种情况下，您需要修改命令为

```
$ ssh-copy-id -i ~/.ssh/id_ecdsa.pub username@remote-server.org
```

如果远程服务器监听端口不是 22,您也需要指明端口

```
$ ssh-copy-id -i ~/.ssh/id_ecdsa.pub -p 221 username@remote-server.org
```

### 传统的方法

使用命令

```
$ scp ~/.ssh/id_ecdsa.pub username@remote-server.org:
```

将公钥上传到服务器。注意，该命令最末的 `:` 不可省略。上传成功之后，先使用口令登录到服务器，将公钥文件重命名为 `authorized_keys`，并移动到 `~/.ssh` 下，若 `~/.ssh` 不存在则新建一个。

```
$ ssh username@remote-server.org
username@remote-server.org's password:
$ mkdir ~/.ssh
$ cat ~/id_ecdsa.pub >> ~/.ssh/authorized_keys
$ rm ~/id_ecdsa.pub
$ chmod 600 ~/.ssh/authorized_keys
```

上面最后两个命令移除服务器上的公钥，并设置 `authorized_keys` 的权限为只有您，也即文件拥有者，有读写权限。

## 安全性

### 保证 authorized_keys 文件的安全

为了保证安全，您应该阻止其他用户添加新的公钥。

将 `authorized_keys` 的权限设置为对拥有者只读，其他用户没有任何权限：

```
$ chmod 400 ~/.ssh/authorized_keys
```

为保证 `authorized_keys` 的权限不会被改掉，您还需要设置该文件的 immutable 位权限：

```
# chattr +i ~/.ssh/authorized_keys
```

然而，用户还可以重命名 `~/.ssh`，然后新建新的 `~/.ssh` 目录和 `authorized_keys` 文件。要避免这种情况，您需要设置 `~./ssh` 的 immutable 位权限：

```
# chattr +i ~/.ssh
```

**注意:** 如果您需要添加新的公钥，您需要移除 `authorized_keys` 的 immutable 位权限。然后，添加好新的公钥之后，按照上述步骤重新加上 immutable 位权限。

### 禁用密码登录

将公钥上传到 SSH 服务器上之后，您就不再需要输入 SSH 账户密码来登录了。直接使用账户密码登录容易受到暴力破解的攻击。倘若您没有提供 SSH 私钥，默认情况下，SSH 服务器就会让您直接使用密码登录，这就有可能让不法之徒来猜测您的密码，有一定的安全隐患。要禁用这一行为，您需要编辑 SSH 服务器上的 `/etc/ssh/sshd_config`：

```
/etc/ssh/sshd_config
```

```
PasswordAuthentication no
ChallengeResponseAuthentication no
```

### 双因素认证与公钥

从 OpenSSH 6.2 开始，您可以使用 `AuthenticationMethods` 选项来自己添加工具链进行认证。这样就可以配合公钥使用双因素认证了。

谷歌身份验证器设置请参考 [Google Authenticator](https://wiki.archlinux.org/index.php/Google_Authenticator)。

如果您使用 PAM (Pluggable Authentication Module，插入式验证模块)，编辑下面这几行：

```
/etc/ssh/sshd_config
```

```
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,keyboard-interactive:pam
```

```
/etc/pam.d/sshd
```

```
#%PAM-1.0
auth required pam_google_authenticator.so
```

如果您设置 PAM 仅使用 `pam_google_authenticator.so`，则 `sshd` 则会采用双因素认证，而无需密码。如果双因素认证失败，即使有有效的公钥，*sshd* 也不允许登录。您可以加上 `nullok` 选项来允许用户使用公钥登录：

```
/etc/pam.d/sshd
```

```
#%PAM-1.0
auth required pam_google_authenticator.so nullok
```

## SSH agents

如果您的私钥使用密码短语来加密了的话，每一次使用 SSH 密钥对进行登录的时候，您都必须输入正确的密码短语。

而 SSH agent 程序能够将您的已解密的私钥缓存起来，在需要的时候提供给您的 SSH 客户端。这样子，您就只需要将私钥加入 SSH agent 缓存的时候输入一次密码短语就可以了。这为您经常使用 SSH 连接提供了不少便利。

SSH agent 一般会设置成在登录会话的时候自动启动，并在整个会话中保持运行。有不少的 SSH agent 供您选择，我们将为您介绍几种常用的 SSH agent，您可以根据您的需要进行选择。

### ssh-agent

ssh-agent 是 OpenSSH 自带的一个 SSH agent，它可以直接作为 SSH agent 来使用，或者作为其他 SSH agent 的后端。`ssh-agent` 运行时会自动 fork 它自身，然收打印出其所需的环境变量。

```
$ ssh-agent
SSH_AUTH_SOCK=/tmp/ssh-vEGjCM2147/agent.2147; export SSH_AUTH_SOCK;
SSH_AGENT_PID=2148; export SSH_AGENT_PID;
echo Agent pid 2148;
```

要使用这些环境变量，您需要使用 `eval` 命令来运行它

```
$ eval $(ssh-agent)
Agent pid 2157
```

您可以将上述命令添加到 `~/.bash_profile`，以便您启动 [登录外壳](https://wiki.archlinux.org/index.php/Bash_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E7.99.BB.E5.BD.95.E5.A4.96.E5.A3.B3) 的时候它自动运行。

```
$ echo 'eval $(ssh-agent)' >> ~/.bash_profile
```

如果您想要所有的用户都可以使用 `ssh-agent`，可以直接把上述命令加入到 `/etc/profile`。

```
# echo 'eval $(ssh-agent)' >> /etc/profile
```

`ssh-agent` 运行起来之后，您还需要将您的私钥加入它的缓存。

```
$ ssh-add ~/.ssh/id_ecdsa
Enter passphrase for /home/user/.ssh/id_ecdsa:
Identity added: /home/user/.ssh/id_ecdsa (/home/user/.ssh/id_ecdsa)
```

如果您想要在登录 shell 的时候自动添加您的私钥到 `ssh-agent` 的缓存，您需要在 `~/.bash_profile` 加入以下命令：

```
ssh-add
```

如果您的私钥已用密码短语加密，`ssh-add` 会提示您输入密码短语。输入争取的密码短语之后，您在*当前会话*中就不需要再次输入密码短语就能够使用密钥对进行 SSH 登录了。

如果您想要在用到私钥的时候再输入密码短语，您可以添加以下命令到 `~/.bashrc`：

```
$ ssh-add -l >/dev/null || alias ssh='ssh-add -l >/dev/null || ssh-add && unalias ssh; ssh'
```

但是这样做有个缺点，每次启动 [登录外壳](https://wiki.archlinux.org/index.php/Bash_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E7.99.BB.E5.BD.95.E5.A4.96.E5.A3.B3) 都会产生一个 `ssh-agent` 实例，并在会话期间一直运行。不久之后，您的系统中就会有多个根本不再需要的 `ssh-agent` 进程在运行。稍后，我们将为您介绍其他 `ssh-agent` 前端，它们能够避免这个问题。

#### ssh-agent 作为包装程序运行

根据加州大学伯克利分校实验室的这份 [ssh-agent 教程](http://upc.lbl.gov/docs/user/sshagent.shtml)，还有一个随 X 会话启动 `ssh-agent` 的方法，该法用于您在使用命令 `startx` 启动 X的时候，可以在命令前加上 `ssh-agent`：

```
$ ssh-agent startx
```

您也可以在 `.bash_aliases` 中设置别名以方便使用：

```
alias startx='ssh-agent startx'
```

这样做可以避免多个会话中存在多余的 `ssh-agent` 进程，实际上，整个 X 会话中只有一个 `ssh-agent` 在运行了。

### GnuPG

**注意:** GnuPG < 2.0.21 不支持 ECC。GnuPG >= 2.0.21 支持 ECDSA。

[GnuPG](https://wiki.archlinux.org/index.php/GnuPG) 可以从 [官方软件仓库](https://wiki.archlinux.org/index.php/Official_repositories_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 安装 [gnupg](https://www.archlinux.org/packages/?name=gnupg)。如果您已经在使用 GnuPG，您也许想要 GnuPG 来缓存您的私钥。当然咯，有些用户比较喜欢在 GnuPG 对话框来输入 PIN 码，这样子管理密码短语也是不错的选择。

{注意|如果您使用 KDE 作为桌面环境，且安装了 [kde-agent](https://www.archlinux.org/packages/?name=kde-agent)[[broken link](https://wiki.archlinux.org/index.php/ArchWiki:Requests#Broken_package_links): package not found] 的话，您只需在 `~/.gnupg/gpg-agent.conf` 中设置 `enable-ssh-support`。否则，请继续阅读。}

要使用 GnuPG agent，您首先要启动 *gpg-agent*，并加上 `--enable-ssh-support` 选项。比如，记得给脚本加上可执行权限：

```
/etc/profile.d/gpg-agent.sh
```

```
#!/bin/sh

# Start the GnuPG agent and enable OpenSSH agent emulation
gnupginf="${HOME}/.gpg-agent-info"

if pgrep -x -u "${USER}" gpg-agent >/dev/null 2>&1; then
    eval `cat $gnupginf`
    eval `cut -d= -f1 $gnupginf | xargs echo export`
else
    eval `gpg-agent -s --enable-ssh-support --daemon --write-env-file "$gnupginf"`
fi
```

`gpg-agent` 运行起来之后，您就可以使用 `ssh-add` 命令来认证密钥对，正如上文中 `ssh-agent` 做的那样。已认证的密钥对列表保存在 `~/.gnupg/sshcontrol` 文件中。此后，在需要使用您的私钥密码短语是，GnuPG 会显示一个对话框让您输入。您可以通过配置文件 `~/.gnupg/gpg-agent.conf` 来管理您的密码短语缓存。比如，下面这个例子说明如何使用 `gpg-agent` 来缓存您的密钥 3 小时：

```
~/.gnupg/gpg-agent.conf
```

```
  # Cache settings
  default-cache-ttl 10800
  default-cache-ttl-ssh 10800
```

您还可以在此文件中设置 PIN 输入框 (GTK、QT 或者 ncurses 版本) 等内容。

**注意:** 您必须创建一个 `gpg-agent.conf` 文件，`write-env-file` 变量必须设置好，以便允许 `gpg-agent` 在 SSH 登录过程中的使用。

```
~/.gnupg/gpg-agent.conf
```

```
  # Environment file
  write-env-file /home/username/.gpg-agent-info
  
  # Keyboard control
  #no-grab
    
  # PIN entry program
  #pinentry-program /usr/bin/pinentry-curses
  #pinentry-program /usr/bin/pinentry-qt4
  #pinentry-program /usr/bin/pinentry-kwallet
  pinentry-program /usr/bin/pinentry-gtk-2
```

要使用 `gpg-agent` 作为 SSH agent，您需要 source & export `gpg-agent` 写入 `~/.gpg-agent-info` 文件的环境变量，也就是 `write-env-file` 所指的文件。

```
~/.bashrc
```

```
...

if [ -f "${HOME}/.gpg-agent-info" ]; then
  . "${HOME}/.gpg-agent-info"
  export GPG_AGENT_INFO
  export SSH_AUTH_SOCK
fi
```

### Keychain

[Keychain](http://www.funtoo.org/Keychain) 是一个用来方便管理 SSH 密钥对的程序，它能尽最大努力去减少对用户的打扰。实际上，它就是一个 shell 脚本，驱动 `ssh-agent` 或者 `gpg-add` 来工作。一个值得注意的特性是，keychain 在多个会话中重复使用同一个 `ssh-agent` 进程。这意味着您只需要在机器启动时输入一次密码短语即可。

您可以从 [官方软件仓库](https://wiki.archlinux.org/index.php/Official_repositories_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 安装 [keychain](https://www.archlinux.org/packages/?name=keychain)。

将下列内容加入到 `~/.bash_profile`：

```
~/.bash_profile
```

```
eval $(keychain --eval --agents ssh -Q --quiet id_ecdsa)
```

如有需要，请把 `id_ecdsa` 换成您的私钥路径。如果您使用的 shell 不兼容 [Bash (简体中文)](https://wiki.archlinux.org/index.php/Bash_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，请参考 `keychain --help` 或者 [keychain(1)](https://jlk.fjfi.cvut.cz/arch/manpages/man/keychain.1)。

要测试 keychain 是否配置成功，请注销当前会话，重新登录。如果这是您第一次运行 keychain，则您会被要求输入私钥的密码短语。因为 keychain 复用已成功登录的会话中的 `ssh-agent` 进程，所以您再次登录是就无需输入密码短语了。当您重启机器时，您才会被要求再次输入密码短语。

#### 另一种启动 keychain 的方式

调用 keychain 的方法有很多种，您可以自行尝试，找到最适合您的那一种。`keychain` 命令提供了不少的选项来进行设置，您可以参考它的手册。

这里我们介绍其中一种不错的方法，以 `root` 身份创建 `/etc/profile.d/keychain.sh`，并添加下列内容：

```
/etc/profile.d/keychain.sh
```

```
/usr/bin/keychain -Q -q --nogui ~/.ssh/id_ecdsa
[[ -f $HOME/.keychain/$HOSTNAME-sh ]] && source $HOME/.keychain/$HOSTNAME-sh
```

记得给该脚本加上可执行权限：

```
# chmod +x /etc/profile.d/keychain.sh
```

如果您想要在第一次使用私钥时才输入密码短语，而非一开机登录就输入的话，您可以将下列内容加入 `~/.bashrc`：

```
alias ssh='eval $(/usr/bin/keychain --eval --agents ssh -Q --quiet ~/.ssh/id_ecdsa) && ssh'
```

这样子，当您开机后首次使用密钥对连接 SSH 服务器时，您才需要提供密码短语。但是，这只在 `~/.bashrc` 可用的情况下才奏效，所以您应该保证第一次使用 SSH 连接时是在终端下进行的。

### envoy

[Envoy](https://github.com/vodik/envoy) 算是 keychain 的一个替代品，您可以从 [官方软件仓库](https://wiki.archlinux.org/index.php/Official_repositories_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 下载安装 [envoy](https://aur.archlinux.org/packages/envoy/)AUR，或者从 [AUR](https://wiki.archlinux.org/index.php/Arch_User_Repository_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 安装 [envoy-git](https://aur.archlinux.org/packages/envoy-git/)AUR。

安装完成之后，使用以下命令启用 envoy 套接字 (socket)：

```
# systemctl enable envoy@ssh-agent.socket
```

加入您的外壳 rc 文件：

```
 envoy -t ssh-agent -a ssh_key
 source <(envoy -p)
```

如果您的私钥为 `~/.ssh/id_rsa`, `~/.ssh/id_dsa`, `~/.ssh/id_ecdsa`，或者 `~/.ssh/identity`，则不需要 `-a *ssh_key*` 参数。

#### 利用 KDE 电子钱包存储密码短语并和 envoy 配合工作

如果您的密码短语很长很复杂，那记忆起来是有点痛苦。您可以让 KDE 电子钱包来为您记住密码短语哦！

安装位于 [官方软件仓库](https://wiki.archlinux.org/index.php/Official_repositories_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 的 [ksshaskpass](https://www.archlinux.org/packages/?name=ksshaskpass) 和 [kdeutils-kwalletmanager](https://www.archlinux.org/packages/?name=kdeutils-kwalletmanager)[[broken link](https://wiki.archlinux.org/index.php/ArchWiki:Requests#Broken_package_links): package not found]，然后按照上文介绍的方法启用 envoy 套接字。

添加一个脚本到 `~/.kde4/Autostart/`，输入以下内容：

```
~/.kde4/Autostart/ssh-agent.sh
```

```
#!/bin/sh
envoy -t ssh-agent -a ssh_key
```

记得加上可执行权限：

```
$ chmod +x ~/.kde4/Autostart/ssh-agent.sh
```

添加一个脚本到 `~/.kde4/env/`，加入以下内容：

```
~/.kde4/env/ssh-agent.sh
```

```
#!/bin/sh
eval $(envoy -p)
```

同样不要忘记加上可执行权限：

```
$ chmod +x ~/.kde4/env/ssh-agent.sh
```

当您登录到 KDE 桌面环境时，它就会自动运行脚本 `ssh-agent.sh`。这样子就能够调用 `ksshaskpass`，当 `envoy` 调用 `ssh-agent` 时，`ksshaskpass` 就会请求您输入 KDE 电子钱包的密码了。而 KDE 电子钱包则会保存您的密码短语，在 `ssh-agent` 需要的时候由 KDE 电子钱包提供。

### pam_ssh

[pam_ssh](http://pam-ssh.sourceforge.net/) 项目为 SSH 私钥提供了一个 [插入式验证模块](https://en.wikipedia.org/wiki/Pluggable_authentication_module) (PAM)。当您的密码短语与系统登录用户密码相同的时候，可以为您减去再次输入密码的麻烦。开机登录时，您需要输入您的登录密码，如有需要，还要输入 ssh 密钥的密码短语。您成功登录系统之后，`ssh-agent` 则会在整个会话期间缓存您已解密的私钥。

要在 tty 模式下使用 pam_ssh，您需要安装位于 [AUR](https://wiki.archlinux.org/index.php/AUR) 的 [pam_ssh](https://aur.archlinux.org/packages/pam_ssh/)AUR 。

**注意:** pam_ssh 2.0 要求所有用于认证的私钥文件必须保存在 `~/.ssh/login-keys.d/` 下。

您可以为您的私钥文件创建一个软链接，并放到 `~/.ssh/login-keys.d/`：

```
$ mkdir ~/.ssh/login-keys.d/
$ cd ~/.ssh/login-keys.d/
$ ln -s ../id_rsa
```

注意将上述例子中的 `id_rsa` 换成您对应的私钥文件。

编辑 `/etc/pam.d/login`，将下面例子中高亮加粗的那几行加进去。请注意配置内容的顺序会影响到登录行为，应当按照例子中的来。

**警告:** PAM 配置丢失会导致系统中所有的用户被锁定。因此，您必须在保存任何 PAM 配置并生效之前，做好配置文件的备份，准备好一份 live CD，以防万一用户被锁定时还有办法恢复原样。您可以参阅 IBM 的 [这篇文章](http://www.ibm.com/developerworks/linux/library/l-pam/index.html) 详细了解一下 PAM 的配置。

```
/etc/pam.d/login
```

```
#%PAM-1.0

auth       required     pam_securetty.so
auth       requisite    pam_nologin.so
auth       include      system-local-login
auth       optional     pam_ssh.so        try_first_pass
account    include      system-local-login
session    include      system-local-login
session    optional     pam_ssh.so
```

在上面的例子中，登录认证初始化进程如平常那样启动，用户要输入登录密码。`try_first_pass` 选项传递给 `pam_ssh` 模块，它会尝试使用用户密码作为密码短语去解密 `~/.ssh/login-keys.d/` 下的私钥。如果用户密码与密码短语一致，那么私钥可以被解密，用户就无需再次输入相同的密码。如果两者不同，ssh_pam 模块会在用户正确输入登录密码之后输入密码短语。`optional` 可以保证 `~/.ssh/login-keys.d/` 下没有私钥时，用户也能够正常登录系统。这种情况下，ssh_pam 模块对于没有私钥的用户来说就等于无。

如果您使用其他方式登录，比如使用登录管理器 [SLiM (简体中文)](https://wiki.archlinux.org/index.php/SLiM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 或者 [XDM (简体中文)](https://wiki.archlinux.org/index.php/XDM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，您必须按照类似的方法来编辑 PAM 配置文件，才能正常工作。`/etc/pam.d/` 目录下保存有默认的配置文件，您可以参考默认配置文件来进行配置。

#### pam_ssh 已知问题

pam_ssh 并不广泛使用，其提供的文档也比较少。您应该注意一下该软件包的一些使用限制，比如：

- pam_ssh < 2.0 不支持 ECDSA，您必须使用 RSA 或者 DSA。
- pam_ssh 调用的 `ssh-agent` 进程仅限于当前会话，不能在多个会话中共享。上文提到的 [keychain](https://wiki.archlinux.org/index.php/SSH_keys_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#Keychain) 前端可以避免这个问题。

### GNOME Keyring

如果您使用 Gnome，您也可以使用 Gnome 钥匙圈 (GNOME Keyring) 作为 SSH agent。详情请参考 [GNOME Keyring](https://wiki.archlinux.org/index.php/GNOME_Keyring)。

## 疑难排解

如果您的 SSH 服务器忽略了您的 SSH 密钥对，您需要检查一下相关文件的权限是否正确。

本地机器上：

```
$ chmod 700 ~/
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/id_ecdsa
```

服务器上：

```
$ chmod 700 ~/
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```

如果这样还不能解决您的问题，您可以试试将 `sshd_config` 中的 `StrictModes` 设为 `no`。如果将 `StrictModes` 关闭就能够顺利认证的话，说明相关文件的权限还没有改对。

**提示：** 为保证安全，记得把 `StrictModes` 设为 `yes`。

请确认您的 SSH 服务器支持您所使用的密钥类型， 可以实施 RSA 或者 DSA。某些服务器可能不支持 ECDSA 密钥。

如果还不行，打开 sshd 的 debug 模式，查看连接时的日志输出，查找原因吧：

```
# /usr/bin/sshd -d
```

### 使用 kdm

KDM 并不能直接启动 `ssh-agent`，KDM 要用 [kde-agent](https://www.archlinux.org/packages/?name=kde-agent)[[broken link](https://wiki.archlinux.org/index.php/ArchWiki:Requests#Broken_package_links): package not found] 来启动 `ssh-agent`，但是 `kde-agent` 自 20140102-1 开始已经被 [移除](https://projects.archlinux.org/svntogit/packages.git/commit/trunk?h=packages/kde-agent&id=1070467b0f74b2339ceca2b9471d1c4e2b9c9c8f)。

为了让 KDE 启动时启动 `ssh-agent`，您可以创建一个脚本用于登录时启动 `ssh-agent`，还有一个脚本用于注销时杀死进程：

```
echo -e '#!/bin/sh\n[ -n "$SSH_AGENT_PID" ] || eval "$(ssh-agent -s)"' > ~/.kde4/env/ssh-agent-startup.sh
echo -e '#!/bin/sh\n[ -z "$SSH_AGENT_PID" ] || eval "$(ssh-agent -k)"' > ~/.kde4/shutdown/ssh-agent-shutdown.sh
chmod 755 ~/.kde4/env/ssh-agent-startup.sh ~/.kde4/shutdown/ssh-agent-shutdown.sh
```




# NEW

阮一峰的网络日志 » [首页](http://www.ruanyifeng.com/blog/) » [档案](http://www.ruanyifeng.com/blog/archives.html)

[![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAABGdBTUEAAK/INwWK6QAAABl0RVh0U29mdHdhcmUAQWRvYmUgSW1hZ2VSZWFkeXHJZTwAAAUzSURBVHjavFdbbFRVFF3nPjoz7dTWTittaW0jUDRAUqaNojyqREnEQKgfUj9MqqAmhqRt/OCD4CuY+Kckoh+aiGKC+gMJbdHoRysJ8dkhhmJLNdDKtJU+6GMK87j3Hs85d2Z6HzNtMYWb3Dn3NWftvfba+5xNYDl+e6Fkj6yqb/oDRbWq14vlPBLRKCITkxf0ROLt+hNjp1PPSRK4kA3vF1dXNRcWlyA2OQU9eos9opAkAiKxD+XkKO6t15aRWO7J/MgmAZU8MEgexgZHMX518Dh72sYMmVKShnxWuWHdHtxKIDIYTgMuDzgfmSOIQkYMpdUF8OY92Hytt4/jvkg47czzU16iQovM3QFwmNck+Yyduu7D6NA0Z6JR4THntFs9V4tWQg6Ui3s6MwKDncsFTnXKLJhDSeUK3AgPtyhccDzmVs999buRt/1Vm4i0od+hX7+MRG87jPGB/w1u8FPj9xEw7McVrnYuOCvtpjTth3J/nTg99c8LRhKhr6D3dTB5R24bXFwbMXBsyZzeoXaycEpJ95TB09AGX/NpqLVNtw8urnVzLvHjFNxiFqRy2OOHuqUVnue+ACkoWzo4O6lGzTmuHq6nPvY2m9rVqjrIK2rMEKxqyG5NPAKt+wjo0LklgfNxJkZMA3KJvqRUk3z5UFY3QH14P0h+WUY79HPvgv7VuSg4ZRGY1YgZgqXmORccF17sy2ehnf9AeO085K2HQFbtXBScj0LcpgF2cN+WV+DZ/LJQu6gD4R7oV7pBJwbSgtMvfiPoVp56DySwxm7EtkMs1WdAB7qzggsDJKQYsHucSkOudrkiCPWR/fA2nYCn8SNIK4NptSMyAu3sAdDRkIsJdfth0LzSrODUoPNZ4KI9SxJI5UHk7D4GdQfz2us31c7CoHMjRkKuDPHseCMrONVhNcDJwMJpKFVvg9L4OaTiNWm1x789KCqkrXhVBiEz0WYCT2nAzQAD1/vaETv1GrRfP4Vx5cfMNcDPwvP0h0DhanPym7OIf/+O67vcJ1/PCJ4KgdzaUP6Wz+dU+5yIL6fV+PsHGAOdwlPpvvUOyeeAVGyCdqkDNB6DPjsBSrnndfOGevOh3RhGItxvA+fX1CtbGFhgYUFkFMZPR6F1HnClHq8HyubWtJexX06CRmdt33hrd7nA7SFY4qoGpnYuOKcRykPPgDCBcsHx9Iv+fNL2PueBehCWUfYQIIMGLOCcOmXDXsh1+yCt35tUPfvzGFuSvzvoinXOxqa02qOhM6733nVP2MAdaej2XN11DPKjLZCD+yBvahGCo7JfTKAN9UD7s8Oe9zUNIhz8fWI8DG2k38WCFdxugANcXrvTVd1IEbuv3Jour7Hzn7jLMBNfKs7R3i67gRVrbeCOEDhinmWhAatsqdquM2XzHZINhK2cqTjHr/XZdVJUbgN3MWAVXKbSyg9jesRW2xP9di+lwrL5ojM3m2H/kG9hwcIA37c71W6wJdW2J2S5nrjYbq/t1AHAhJsKQeyfPvf6IMJgghPJhFZ4x0KlfLFvt22du45Au/A1SOlGc0P672XXwhLtOcM0kTTEMMd0qkVmMNXxMd/tsedUjInr4SQDgOfeXMSiN0FCL5WHah4L1qqYXPJOJlttd+a5M+YpcG5poLYKQ5f+6JJ4r8bbJYP47hq4r7QAs9PjYNhHJd4o8l5taiwuOpa7AS4XKqI/5NjJbTnaWK92nLdLuhQAJayRNMiygXPBeQN+Qbvu0zDc3y+aUzhbkGR73sI7ljvUnndx2q3t+X8CDAD66FtrIL864AAAAABJRU5ErkJggg==)](http://www.ruanyifeng.com/feed.html)

- 上一篇：[《禅与摩托车维修艺术》](http://www.ruanyifeng.com/blog/2011/12/zen_and_the_art_of_motorcycle_maintenance.html)
- 下一篇：[SSH原理与运用（二）](http://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html)

分类：

 

- [理解计算机](http://www.ruanyifeng.com/blog/computer/)

# SSH原理与运用（一）：远程登录

作者： [阮一峰](http://www.ruanyifeng.com/)

日期： [2011年12月21日](http://www.ruanyifeng.com/blog/2011/12/)

SSH是每一台Linux电脑的标准配置。

随着Linux设备从电脑逐渐扩展到手机、外设和家用电器，SSH的使用范围也越来越广。不仅程序员离不开它，很多普通用户也每天使用。

SSH具备多种功能，可以用于很多场合。有些事情，没有它就是办不成。本文是我的学习笔记，总结和解释了SSH的常见用法，希望对大家有用。

虽然本文内容只涉及初级应用，较为简单，但是需要读者具备最基本的"Shell知识"和了解"公钥加密"的概念。如果你对它们不熟悉，我推荐先阅读[《UNIX / Linux 初学者教程》](http://www.ee.surrey.ac.uk/Teaching/Unix/)和[《数字签名是什么？》](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)。

=======================================

**SSH原理与运用**

作者：阮一峰

![img](http://www.ruanyifeng.com/blogimg/asset/201112/bg2011122101.jpg)

**一、什么是SSH？**

简单说，SSH是一种网络协议，用于计算机之间的加密登录。

如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。

最早的时候，互联网通信都是明文通信，一旦被截获，内容就暴露无疑。1995年，芬兰学者Tatu Ylonen设计了SSH协议，将登录信息全部加密，成为互联网安全的一个基本解决方案，迅速在全世界获得推广，目前已经成为Linux系统的标准配置。

需要指出的是，SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。本文针对的实现是[OpenSSH](http://www.openssh.com/)，它是自由软件，应用非常广泛。

此外，本文只讨论SSH在Linux Shell中的用法。如果要在Windows系统中使用SSH，会用到另一种软件[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty)，这需要另文介绍。

**二、最基本的用法**

SSH主要用于远程登录。假定你要以用户名user，登录远程主机host，只要一条简单命令就可以了。

> 　　$ ssh user@host

如果本地用户名与远程用户名一致，登录时可以省略用户名。

> 　　$ ssh host

SSH的默认端口是22，也就是说，你的登录请求会送进远程主机的22端口。使用p参数，可以修改这个端口。

> 　　$ ssh -p 2222 user@host

上面这条命令表示，ssh直接连接远程主机的2222端口。

**三、中间人攻击**

SSH之所以能够保证安全，原因在于它采用了公钥加密。

整个过程是这样的：（1）远程主机收到用户的登录请求，把自己的公钥发给用户。（2）用户使用这个公钥，将登录密码加密后，发送回来。（3）远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

这个过程本身是安全的，但是实施的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，也就是说，都是自己签发的。

可以设想，如果攻击者插在用户与远程主机之间（比如在公共的wifi区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就荡然无存了。这种风险就是著名的["中间人攻击"](http://en.wikipedia.org/wiki/Man-in-the-middle_attack)（Man-in-the-middle attack）。

SSH协议是如何应对的呢？

**四、口令登录**

如果你是第一次登录对方主机，系统会出现下面的提示：

> 　　$ ssh user@host
>
> 　　The authenticity of host 'host (12.18.429.21)' can't be established.
>
> 　　RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
>
> 　　Are you sure you want to continue connecting (yes/no)?

这段话的意思是，无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？

所谓"公钥指纹"，是指公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。上例中是98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d，再进行比较，就容易多了。

很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。

假定经过风险衡量以后，用户决定接受这个远程主机的公钥。

> 　　Are you sure you want to continue connecting (yes/no)? yes

系统会出现一句提示，表示host主机已经得到认可。

> 　　Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.

然后，会要求输入密码。

> 　　Password: (enter password)

如果密码正确，就可以登录了。

当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。

**五、公钥登录**

使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个：

> 　　$ ssh-keygen

运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

运行结束以后，在$HOME/.ssh/目录下，会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。

这时再输入下面的命令，将公钥传送到远程主机host上面：

> 　　$ ssh-copy-id user@host

好了，从此你再登录，就不需要输入密码了。

如果还是不行，就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面"#"注释是否取掉。

> 　　RSAAuthentication yes
> 　　PubkeyAuthentication yes
> 　　AuthorizedKeysFile .ssh/authorized_keys

然后，重启远程主机的ssh服务。

> 　　// ubuntu系统
> 　　service ssh restart
>
> 　　// debian系统
> 　　/etc/init.d/ssh restart

**六、authorized_keys文件**

远程主机将用户的公钥，保存在登录后的用户主目录的$HOME/.ssh/authorized_keys文件中。公钥就是一段字符串，只要把它追加在authorized_keys文件的末尾就行了。
这里不使用上面的ssh-copy-id命令，改用下面的命令，解释公钥的保存过程：

> 　　$ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

这条命令由多个语句组成，依次分解开来看：（1）"$ ssh user@host"，表示登录远程主机；（2）单引号中的mkdir .ssh && cat >> .ssh/authorized_keys，表示登录后在远程shell上执行的命令：（3）"$ mkdir -p .ssh"的作用是，如果用户主目录中的.ssh目录不存在，就创建一个；（4）'cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub的作用是，将本地的公钥文件~/.ssh/id_rsa.pub，重定向追加到远程文件authorized_keys的末尾。

写入authorized_keys文件后，公钥登录的设置就完成了。

==============================================

关于shell远程登录的部分就写到这里，下一次接着介绍[《远程操作和端口转发》](http://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html)。

（完）



## 留言（74条）

[南 靖男](http://code.google.com/p/puttycn/wiki/ConfigurationGuide) 说：

必须：
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

2011年12月21日 18:55 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-242986) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

dindog 说：

第一次接触ssh是git，这东西还是挺方便的。虽然我只是同步自己的小代码

2011年12月21日 20:49 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-242996) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

paranonia 说：

chmod 700 ~/.ssh 必须

chmod 600 ~/.ssh/authorized_keys 非必须

2011年12月21日 20:54 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-242997) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

Coiby 说：

这篇也不错，http://wowubuntu.com/ssh-tips.html，作者可以参考下，

2011年12月21日 21:16 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243002) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[icyomik](http://icyomik.tk/) 说：

'mkdir .ssh && cat >> .ssh/authorized_keys'
或者可以改为：
'mkdir .ssh ; cat >> .ssh/authorized_keys'

2011年12月21日 23:20 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243018) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[依云](http://lilydjwg.is-programmer.com/) 说：

mkdir -p .ssh ，不然可能出错，后面的命令不执行。

2011年12月21日 23:25 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243019) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

Zind 说：

我觉得好像有点儿不对劲啊……
远程主机上也不一定存在 authorized_keys 文件
所以，是不是应该这样：
'mkdir -p .ssh/authorized_keys && cat >> .ssh/authorized_keys'

2011年12月21日 23:39 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243021) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

Zind 说：

呃，我错了……
'mkdir -p .ssh && cat >> .ssh/authorized_keys' 就好，其它的会自己处理的

2011年12月22日 09:06 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243051) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

yaoweibin 说：

ssh（ssl/tls）的原理很复杂，如果不对网络安全和数学有深入研究，很难讲得清楚。

2011年12月22日 10:05 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243053) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[Ansen](http://www.ansen.org/) 说：

很详细的文章
谢谢博主

2011年12月22日 11:07 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243058) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[在路上](http://www.zaihuwai.net/bbs) 说：

用虚拟主机非常方便

2011年12月22日 12:51 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243066) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

mercurate 说：

能否顺便讲讲非对称加密是如何做到的?

2011年12月22日 16:05 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243077) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[zhong.xu](http://coolweblog.appspot.com/) 说：

博主的文章总是能让人看的明白。
自己知道还能讲解的让别人也知道，
我想这就是博主自身功力深厚的证明吧。

2011年12月22日 17:00 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243079) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

daryl 说：

中间一段错了，公钥永远是用来加密的，私钥永远是用来解密。

2011年12月22日 23:31 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243103) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

ssdt 说：

不会是你看到我昨天用ssh,随便写了3个生成自己的ssh失败了，

你就以为我不会就写了这篇吧

我总怀疑你是个虚拟的人或者这文章不是你写的

2011年12月23日 02:29 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243115) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[大头龙仔](http://imlcl.com/) 说：

自从kernel.org被爆，现在多数改为公钥认证方式，password我平时只用在内网服务器。不过像VPS，如果开password认证，从auth.log可以看到，经常有人在试你密码。
这个是比较恶心的

2011年12月23日 09:05 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-243147) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[happypeter](http://happypeter.org/) 说：

我参考你的这篇博客，做了个视频，谢谢
<http://casts.happypeter.org/episodes/3>

2012年1月 7日 22:14 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-244596) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

Todd 说：

\>>$ ssh user@host 'mkdir .ssh && cat >> .ssh/authorized_keys'

这里最好用mkdir -p .ssh，保证不管.ssh是否已经存在都正确执行。

2012年1月 8日 13:39 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-244637) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

阮一峰 说：

@Todd：

谢谢指出。

我前面没有意识到，如果目录.ssh已经存在，mkdir .ssh会报错。我这就改过来，加上参数p。

2012年1月 8日 15:21 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-244647) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

simenyu 说：

用ssh-copy-id <host>木有成功，但也木有任何出错提示，完了之后ssh远端主机，还是需要输入密码。查看远端的authorized_keys文件，发现木有公钥字符串。
改用$ ssh <host> 'mkdir -p .ssh && cat >> .ssh/authorized_keys'

不知道方法一哪里出问题了。

2012年3月11日 09:54 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-250228) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

阮一峰 说：

@simenyu:

参考下面的文章，好像是说远程端的权限设置的原因。

<http://superuser.com/questions/189376/ssh-copy-id-does-not-work>

2012年3月11日 10:23 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-250231) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

feynixs 说：

五、公钥登录，这部分应该写错了。
应该是：公匙是用来加密的，私匙用来解密的。

2012年3月17日 13:51 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-250664) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[沙渺](http://shamiao.com/) 说：

> ```
> 引用feynixs的发言：
> ```
>
> 五、公钥登录，这部分应该写错了。
> 应该是：公匙是用来加密的，私匙用来解密的。

公私钥是对等的吧，公加私解或者私加公解都只是用法问题

2012年11月13日 06:40 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-265933) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[Zernel](http://zernel.me/) 说：

不错，虽然每天工作都要接触到SSH，文章提到的这些操作之前也有用过，但根本不知道原理，只是知道这样做可以达到怎么样的效果，现在看完这篇文章后清晰了很多，谢谢阮老师。

2013年4月 6日 16:06 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-270468) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

凯凯 说：

> ```
> 引用沙渺的发言：
> ```
>
> 
>
> 公私钥是对等的吧，公加私解或者私加公解都只是用法问题

我觉得“五、公钥登录”这部分是有问题的，我认为应该是这样：

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段用用户公钥加密的随机字符串，用户用自己的私钥解密后，再发回来。远程主机把这个信息和原始信息匹配，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。（其实，用户用私钥解密之后得到原始信息X，还要用会话号P和X一起算md5发回给服务器，服务器用之前保存的会话号P和原始信息X做md5运算，和用户发回的信息匹配，如果成功，就证明用户是可信的）

2013年4月 8日 21:51 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-270522) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

yinzhezq 说：

> ```
> 引用yinzhezq的发言：
> ```
>
> 最好改为这样：'mkdir -p .ssh && cat >> .ssh/authorized_keys'

'mkdir -p .ssh && umask 066 && cat >> .ssh/authorized_keys'

2013年4月15日 15:50 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-270777) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

吕朋 说：

解释的再清晰不过了

2013年8月10日 22:19 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-272992) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

南蛮虫 说：

$ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' 这条命令不成功，报错找不到~/.ssh/id_rsa.pub

2013年12月13日 14:36 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-292795) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

himagicxiao 说：

> ```
> 引用南蛮虫的发言：
> ```
>
> $ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' 
> 这条命令不成功，报错找不到~/.ssh/id_rsa.pub

检查本地有没有~/.ssh/id_rsa.pub

2013年12月16日 18:05 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-293295) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[Jak Wings](http://blog.likelikeslike.com/) 说：

@凯凯：

看他写的第三节「三、中间人攻击」，我估计是故意只强调客户端密钥的。

2014年3月27日 23:02 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-304934) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[Karl](http://karl.cauchies.org/) 说：

$ ssh user@example.com 'mkdir -p .ssh && touch .ssh/authorized_keys && chmod 644 .s
sh/authorized_keys && cat >> .ssh/authorized_keys'

2014年4月 1日 12:13 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-306365) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

BlueIceQ 说：

> ```
> 引用feynixs的发言：
> ```
>
> 五、公钥登录，这部分应该写错了。
> 应该是：公匙是用来加密的，私匙用来解密的。

在"加密数据"时，是公钥加密，私钥解密；但是在"身份认证"时，是私钥加密，公钥解密，你想啊，私钥就一个，公钥可以有多个，即多个公钥对应一个私钥，加密的数据用公钥解出来了，那肯定就知对应的私钥是谁了，这就验证了身份了。

2014年5月21日 18:59 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-324504) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[BlueIceQ](http://www.wanghui.asia/) 说：

> ```
> 引用凯凯的发言：
> ```
>
> 
>
> 我觉得“五、公钥登录”这部分是有问题的，我认为应该是这样：
>
> 所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段用用户公钥加密的随机字符串，用户用自己的私钥解密后，再发回来。远程主机把这个信息和原始信息匹配，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。（其实，用户用私钥解密之后得到原始信息X，还要用会话号P和X一起算md5发回给服务器，服务器用之前保存的会话号P和原始信息X做md5运算，和用户发回的信息匹配，如果成功，就证明用户是可信的）

如果远程主机上面有多个公钥，那它选择那个公钥来对数据进行加密呢?但是如果发送的是明文，用户用私钥加密，发送到远程主机，远程主机就可以遍历公钥来对数据解密了。不要说远程主机会遍历公钥来对数据加密发送给用户啊，这明显是浪费时间的。

2014年5月21日 19:06 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-324507) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[BlueIceQ](http://www.wanghui.asia/) 说：

> ```
> 引用沙渺的发言：
> ```
>
> 
>
> 公私钥是对等的吧，公加私解或者私加公解都只是用法问题

明显身份认证的时候私加公解划算啊。

2014年5月21日 19:08 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-324508) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

luochen1990 说：

稍稍纠正一下, centos 重启应该是 sudo /etc/init.d/sshd restart 对么?
另外补充一点跳坑信息, 如果是刚刚创建 ~/.ssh/authorized_keys 的话, 要注意该文件的权限, 可以改成如: chmod 640 ~/.ssh/authorized_keys

2014年6月13日 18:33 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-328465) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

Jason 说：

写得实在太好了！简洁明了！非常感谢！

2014年7月 8日 10:42 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-330492) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

hellowed 说：

写的很清楚，阮老师

2014年12月19日 14:54 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-346475) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

yjs 说：

感谢，
口令登陆ssh遵循公钥加密、公钥解密;win客户端登陆 ①向远端主机发送请求 ②远程主机发给win客户一个公钥(类似一个没有上锁的盒子),win客户将密码放入上锁后再次还给远程主机 ③远程主机用私钥打开公钥(盒子),验证密码一致允许登陆。
ssh公钥登陆同样遵循私钥加密、公钥解密;linux客户端先生成ssh公钥事先给远程主机更名为authorized_keys，①linux客户端向远程主机发送连接,远程主机随意发给linux客户端一串字符串 ②linux客户端用私钥加密字符串返还给远程主机 ③远程主机用事先存储的公钥进行解密,如果和事先发送的字符串一致则允许登陆。

2015年1月22日 23:17 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-347419) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

nyuxiao 说：

“……middle attack）。
SSH协议是如何应对的呢？
四、口令登录”

这个“如何应对”指的是应对中间人攻击吗？

2015年1月27日 19:14 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-347565) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

notracy 说：

很是实用，感谢博主

2015年3月10日 06:28 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-348409) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

wlb 说：

对这句执行的顺序有点不明白
cat >> .ssh/authorized_keys'

2015年3月18日 21:17 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-348656) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

AutumnLight 说：

楼主这一篇讲的太好了，井井有条。

2015年8月12日 12:02 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-351233) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

aoniao 说：

> 所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

这部分应该是有问题的。不会使用私钥去加密，公钥是解密的。参考这里的解释，http://www.unixwiz.net/techtips/ssh-agent-forwarding.html#chal

下面这个摘录自 skypeGNU1 的BLOG 的解释就合理。

> 当需要连接到SSH服务器上时，客户端软件就会向服务器发出请求，请求使用客户端的密钥进行安全验证。服务器收到请求之后，先在该用户的根目录下寻找共有密钥，然后把它和发送过来的公有密钥进行比较。如果两个密钥一致，服务器就用公有的密钥加密“质询”，并把它发送给客户端软件（putty,xshell等）。客户端收到质询之后，就可以用本地的私人密钥解密再把它发送给服务器，这种方式是相当安全的。

2015年11月 9日 00:31 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-352670) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

谢宏浩 说：

感觉 公钥登录 完全没有解决中间人攻击的问题

下面是原文摘抄 我加了些标号
“所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。1 登录的时候，远程主机会向用户发送一段随机字符串，2 用户用自己的私钥加密后，再发回来。3 远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。”

我的观点： 
如果2过程中发送的数据被中间人截取了， 中间人发送到了远程主机， 远程主机会把中间人作为可信用户 对吧？ 如此如何防止中间人攻击?

2015年12月 4日 19:12 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-353087) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

王松 说：

> ```
> 引用daryl的发言：
> ```
>
> 中间一段错了，公钥永远是用来加密的，私钥永远是用来解密。

公钥和私钥本身都是既可以加密也可以解密的，公钥加密私钥解密是正常的非对称加密过程，私钥加密公钥解密应该就是数字签名的过程了。

2016年1月17日 13:10 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-354079) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

胡扯 说：

卧槽，误导啊，公钥私钥谁加密谁解密都没弄明白，还来说ssh？

2016年1月21日 16:04 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-354218) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

jjnet 说：

> ```
> 引用沙渺的发言：
> ```
>
> 
>
> 公私钥是对等的吧，公加私解或者私加公解都只是用法问题

从非对称算法角度上， 有两个数e和d, 不等于d，通过公开函数f1(e,p)得到m
同样，也可以通过公开函数f2(d,m)得到p， 其中m是原始文本而p是经过变换后的文本。
一般而言成为为【编码】和【解码】， 其中持有e的人编码后的数据只有持有d的人能解码，
同样持有d的人编码后只有e能解。 这里习惯性的吧e定义为【公钥】，d定义为【私钥】
作为公钥私钥的生成着会保存【私钥】（大多数情况下私钥文件里面也会包含公钥），
然后讲【公钥】广而告之。

注意【编码】和【解码】和【加密】【解密】是有差别的。
密码学里面， 对文字进行编码后，只有特定的对象（对方）能够解码。 符合这种语义
的编解码方式称之为【加密】和【解密】。
显然用【私钥】去【编码】后的数据不能叫【加密】的，因为【公钥】所有人都知道，失去了
加密的意义。 在公钥私钥系统中， 统一采用【公钥】加密和【私钥】解密。

而且【私钥】去编码数据【公钥】再去解码数据也并不是没有意义，只是习惯性称之为【签名】
即保证这块数据由【私钥】的持有者生成而不是不让人看到数据是什么
（因为大家都有公钥都能解都能证明--比如约定解出来的数据带一个尾巴hahaha，那么所有符合xxxxhahaha的数据都可以认为是私钥发送的别人不能伪造的，发送
的有效数据是xxxx）

其中用e进行 一个公钥对应一个私钥，

2016年1月27日 16:48 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-354359) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

jay 说：

> ```
> 引用daryl的发言：
> ```
>
> 中间一段错了，公钥永远是用来加密的，私钥永远是用来解密。

瞎扯

2016年5月10日 23:54 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-356166) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[linchen1987](http://weibo.com/linchen1987) 说：

> ```
> 引用paranonia的发言：
> ```
>
> chmod 700 ~/.ssh 必须
>
> chmod 600 ~/.ssh/authorized_keys 非必须

谢谢！

2016年6月 3日 12:22 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-357057) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[dingding](http://dingdangmiao.top/) 说：

请问这样就可以远程登录主机了吗？我租的服务器一直上不去，好奇怪。

2016年8月23日 17:14 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-363004) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[robinjia](http://program.dengshilong.org/) 说：

还需要设置~/.ssh/authorized_keys的权限，否则在/var/log/secure里可以看到提示Authentication refused: bad ownership or modes for file。经测试，只有文件所有者有写权限时，才可以登录服务器。所以authorized_keys为640，600都可以登录服务器。但不能是660,620等。而执行完上面的命令，authorized_keys为660,需要修改这个文件的权限。

2016年9月 9日 08:55 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-364099) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[robinjia](http://program.dengshilong.org/) 说：

发现还需要设置.ssh的权限，因为.ssh目录必须700才允许登录，而mkdir -p .ssh新建的目录权限是755.

2016年9月 9日 21:21 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-364162) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

格桑花 说：

阮老师: 我有一个疑问,还望解答,谢谢.

中间人攻击那一端里面有如下一段:
整个过程是这样的：（1）远程主机收到用户的登录请求，把自己的公钥发给用户。（2）用户使用这个公钥，将登录密码加密后，发送回来。（3）远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

经测试, 1. 远程主机 foo 用户并未生成公钥和密钥,也就是 /home/foo/.ssh/目录下并未存在 id_rsa.pub和id_rsa;
\2. 本地主机 bar 用户并未生成公钥和密钥,也就是/home/bar/.ssh/目录下并未存在 id_rsa.pub和id_rsa;
\3. 本地主机 bar 用户运行 ssh foo@remote_serv, 输入yes加入known_hosts后再输入密码可成功登入 remote_serv
疑问: 远程主机未生成公钥和私钥,如何发送自己的公钥给本地主机呢?

实验结果和讲解不符, 不知道是不是我理解错了呢?

2016年9月27日 17:28 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-365283) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

郑 说：

发现，known_hosts里存入的不知道是什么东西，反正和对应主机的公钥不同，能详细说一下，这个中间是怎么加密的吗，用的什么加密算法

2016年11月29日 10:31 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-369389) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

区影 说：

很赞的文章。之前一直没理解为什么第一次会提示“The authenticity of host 'host (12.18.429.21)' can't be established.”，感谢。

另外，在“公钥登陆”章节，您提到“登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密。” 这段话，个人有几点想交换下意见的地方：
（1）通常术语上私钥加密说成签名，公钥解密说成验签。从数学上是类似操作，但用途上签名和验签是为了证明“对方是对方”，而加密和解密是为了“发来的信息没被人偷看”，更严谨的安全会话是加密和签名同时用，因为正如您说的加密不能证明发消息的人是靠谱的。
（2）客户端与服务器是多对1的关系。“远程主机用事先储存的公钥进行解密”，如何从一堆公钥中找到当前客户端的公钥，少了一个公钥索引的点。

2016年12月 4日 09:05 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-369727) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

[domen](http://domenlee.github.io/) 说：

就我一个人很在意这个配图吗。。

2016年12月 8日 14:36 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-370031) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

你好明天 说：

window下我使用给gitbash也挺好用的

2016年12月 8日 17:09 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-370038) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

cui 说：

公钥登陆部分，是客户端将自己的公钥发送到服务端，服务端用客户端的公钥加密一个256位的随机字符串，客户端接收后解密，然后将这个字符串和会话标识符合并在一起，对结果应用MD5散列函数并把散列值返回给服务器，服务器进行相同的MD5散列函数，如果客户端和该值可以匹配，那么匹配成功。

望更正。

2016年12月18日 20:16 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-370556) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

guanxianseng 说：

今天用github在github上保存ssh公钥，看到这里的公钥登录，明白了是怎么回事了

2017年2月 5日 11:06 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-372362) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

年华果腹 说：

> ```
> 引用cui的发言：
> ```
>
> 公钥登陆部分，是客户端将自己的公钥发送到服务端，服务端用客户端的公钥加密一个256位的随机字符串，客户端接收后解密，然后将这个字符串和会话标识符合并在一起，对结果应用MD5散列函数并把散列值返回给服务器，服务器进行相同的MD5散列函数，如果客户端和该值可以匹配，那么匹配成功。
>
> 望更正。

很详细，确实是这样的。关于会话标识符想说的一点是客户端与服务器实际上是先有会话加密过程，再进行的客户端验证，所以这个会话标识符就是第一个阶段生成的共享密钥。

2017年2月15日 10:59 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-372953) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

付辉 说：

\- client向server端请求（用户A想访问server系统下的账户B）,该请求中包含public-key的modulus
\- server读取B账户，查找是否存在匹配的public-key。如果不存在，请求失败。
\- server端生成一个256 bit的字符串，用匹配的public-key加密发给client
\- client端用自己的私钥解密数据，然后和当前的session identifier HASH生成md5值发给server
\- server重新计算md5值，跟client进行比较

2017年4月30日 15:01 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-376794) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

随风 说：

@cui：

你所说的就是md54的对称加密方法吧

2017年5月21日 10:13 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-377322) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

123456 说：

五、公钥登录
这段写错了，博主赶紧改改，不要误导他人。
参见：
<https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys>
<https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process>

当然，也有可能博主是对的，这个只有撸过源码才能确定，有没有朋友有兴趣有时间找到真相的？

2017年7月 9日 16:29 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378595) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

榛子 说：

阮老师，关于你说的这一段我有点无法理解：
“很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。”
如果远程主机在自己网站上贴出来公钥指纹， 那么中间人也可以知道这个呀，再把假的公钥指纹发给你不就好了吗，这样你也无法判断了呀。

2017年7月 9日 18:23 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378596) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

阿布 说：

@榛子：

同樣是關於公鑰的問題，既然我們可以通過主機的網站來確認指紋是否正確，那主機直接在網站上公布自己的公鑰就好了，我們收到的公鑰對不對，對比網站上的公鑰就知道了。這樣想的話，指紋的存在好像是多餘的？

2017年7月25日 22:27 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378928) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

austyywl 说：

@榛子：

远程主机发送的，不是指纹(摘要），而是公钥本身，而指纹，是不可逆的，中间人知道了指纹，但无法推算出公钥，所以，是无法伪造公钥。

2017年7月26日 00:16 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378930) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

austyywl 说：

@阿布：

兄弟，文章中不是说了，公钥太长，不方便比对吗

2017年7月26日 00:19 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378931) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

austyywl 说：

关于公钥登录这一部分，我一开始的理解也是觉得有问题，一般我们都认为公钥加密，私钥解密，这个可能是非对称加密最常用的功能，大家可以参考下这个网页，RSA算法的作用：认证。
<http://www.cnblogs.com/scofi/p/6617394.html>

2017年7月26日 00:36 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-378932) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

kim 说：

When a client connects to the host, wishing to use SSH key authentication, it will inform the server of this intent and will tell the server which public key to use. The server then check its authorized_keys file for the public key, generate a random string and encrypts it using the public key. This encrypted message can only be decrypted with the associated private key. The server will send this encrypted message to the client to test whether they actually have the associated private key.

Upon receipt of this message, the client will decrypt it using the private key and combine the random string that is revealed with a previously negotiated session ID. It then generates an MD5 hash of this value and transmits it back to the server. The server already had the original message and the session ID, so it can compare an MD5 hash generated by those values and determine that the client must have the private key.

说的很清楚啦这段

2017年7月27日 20:19 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-379027) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

austyywl 说：

> ```
> 引用kim的发言：
> ```
>
> ...
>
> 说的很清楚啦这段

当客户端希望通过SSH KEY 鉴权登录时，它会告诉服务端，并附带上自己的公钥KEY信息。服务端检查它的KEY文件，同时生成一个随机串，并且用公钥加密。加密过的随机串，只有客户端用自己的私钥才可以解。
一旦收到消息，客户端将用自己的私钥解密，获取到随机串，同时，将先前跟服务端商定的SESSIONID一起，再次做MD5一致性HASH（md5(randomKey,sessionId），发回给服务端。服务端也按同样的方式生成摘要，如果两者一致，则验证通过。

> 所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

感觉还是有差异，网上也没能找到权威的解释

@kim ，能提供下出处吗？

2017年7月30日 14:14 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-379095) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

二哈 说：

> ```
> 引用沙渺的发言：
> ```
>
> 公私钥是对等的吧，公加私解或者私加公解都只是用法问题

这里讨论的这个有没有一个正确的结论？如果不确定就不要把大家误导了，ssh rsa是非对称加密吧？

2017年11月 9日 21:08 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-382202) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

我是谁 说：

@凯凯：

和其他网站将的不一样，谁能告诉我哪个说的是对的？！！

参看：https://www.cnblogs.com/hukey/p/6248468.html

2017年11月 9日 21:19 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-382204) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

羊羊羊 说：

阮老师，我想请问对于公钥登陆的方式，为什么就能阻止中间人攻击呢？

我做过小测试，不管口令登录还是公钥登录，第一次尝试连接的时候都会有 The authenticity of host 'host (11.22.33.44)' can't be established.这个提示，让我们判断主机是否是真实的。 如果这个IP是假的， 对于口令登录会丢失账号密码，即受到了中间人攻击；而对于公钥登录呢？我们虽然并没有丢失自己的密钥，但是也没能和真正的主机建立联系呀？

2017年12月23日 10:27 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-383504) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

kur0mi 说：

之前对ssh只看了一些简单的，这次认真重新看了一遍，感觉很多东西都变得更透彻了，中间人攻击，公钥验证，密码登录验证的原理等等，通俗易懂，大赞

2018年4月10日 10:53 | [#](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-387806) | [引用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html#comment-text)

## 我要发表看法

您的留言 （HTML标签部分可用）您的大名： «-必填电子邮件： «-必填，不公开个人网址： «-我信任你，不会填写广告链接记住个人信息？ «- 点击按钮

[微博](http://weibo.com/ruanyf) | [推特](https://twitter.com/ruanyf) | [GitHub](https://github.com/ruanyf)

2018 © [我的邮件](http://www.ruanyifeng.com/contact.html) |



# WinSCP

https://winscp.net/eng/docs/scripting

# Scripting and Task Automation

*This article contains detailed description of scripting/automation functionality. You may want to see simplified guide to the functionality instead.*

In addition to [graphical interface](https://winscp.net/eng/docs/interfaces), WinSCP offers scripting/console interface with many [commands](https://winscp.net/eng/docs/scripting#commands). The commands can be typed in interactively, or read from script file or another source.

Using scripting interface directly is recommended for simple tasks not requiring any control structures. For complex tasks, using [WinSCP .NET assembly](https://winscp.net/eng/docs/library) is preferred.

*Advertisement*

[![img](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/scripting.png?v=2596)](https://winscp.net/eng/data/media/screenshots/scripting.png)

- [Using Scripting](https://winscp.net/eng/docs/scripting#using_scripting)
- [Checking Results](https://winscp.net/eng/docs/scripting#result)
- Commands Syntax
  - [Command Parameters with Spaces](https://winscp.net/eng/docs/scripting#quotes)
  - [Environment Variables](https://winscp.net/eng/docs/scripting#variables)
  - [Timestamp](https://winscp.net/eng/docs/scripting#timestamp)
  - [Script Arguments](https://winscp.net/eng/docs/scripting#arguments)
  - [Case Sensitivity of File Names](https://winscp.net/eng/docs/scripting#case_sensitivity_of_file_names)
  - [Comments](https://winscp.net/eng/docs/scripting#comments)
- [Commands](https://winscp.net/eng/docs/scripting#commands)
- [The Console Interface Tool](https://winscp.net/eng/docs/scripting#console)
- [Verifying the Host Key or Certificate in Script](https://winscp.net/eng/docs/scripting#hostkey)
- [Running a Script under a Different Account (e.g., Using a Scheduler)](https://winscp.net/eng/docs/scripting#different_account)
- [Sharing Configuration with Graphical Mode](https://winscp.net/eng/docs/scripting#configuration)
- [Generating Script](https://winscp.net/eng/docs/scripting#generating_script)
- [Example](https://winscp.net/eng/docs/scripting#example)
- [Converting Script to Code Based on .NET Assembly](https://winscp.net/eng/docs/scripting#converting_script_to_code_based_on_.net_assembly)
- [Further Reading](https://winscp.net/eng/docs/scripting#further_reading)

## Using Scripting

Enter the console/scripting mode by using `winscp.com`; or `/console` command-line parameter with `winscp.exe`. For details see [console/scripting command-line parameters](https://winscp.net/eng/docs/commandline#scripting).

For automation, commands can be read from a script file specified by `/script` switch, passed from the command-line using the `/command` switch, or read from standard input of `winscp.com`.

The script file must use UTF-8 or UTF-16 (with BOM) encoding.

When running commands specified using `/script` or `/command`, batch mode is used implicitly and overwrite confirmations are turned off. In an interactive scripting mode, the user is prompted in the same way as in GUI mode. To force batch mode (all prompts are automatically answered negatively) use the command `option batch abort`. For batch mode it is recommended to turn off confirmations using `option confirm off` to allow overwrites (otherwise the [overwrite confirmation prompt](https://winscp.net/eng/docs/ui_overwrite) would be answered negatively, making overwrites impossible).

Multiple sessions can be opened simultaneously. Use the `session` command to switch between them.

Note that the first connection to an SSH server requires [verification of the host key](https://winscp.net/eng/docs/scripting#hostkey). Also the first connection to FTPS or WebDAVS host with [certificate](https://winscp.net/eng/docs/tls#certificate) signed by untrusted authority requires verification of the certificate.

*Advertisement*

## Checking Results

WinSCP [executables](https://winscp.net/eng/docs/executables) return exit code 1 when any command is interrupted due to an error or any prompt is answered *Abort*(even automatically in batch mode). Otherwise it returns the exit code 0.

To further analyze results of scripted operations, you will find [XML logging](https://winscp.net/eng/docs/logging_xml) useful.

## Commands Syntax

All WinSCP commands have syntax:

```
command -switch -switch2 parameter1 parameter2 ... parametern
```

### Command Parameters with Spaces

Command parameters that include space(s) have to be surrounded by double-quotes. To use double-quote literally, double it:

```
put "file with spaces and ""quotes"".html"
```

Note that when you are specifying commands on [command-line](https://winscp.net/eng/docs/commandline#scripting) using `/command`, you need to surround each command by double-quote and [escape the in-command double-quotes by doubling them](https://winscp.net/eng/docs/commandline#syntax).

To debug the quoting, enable session logging on level *Debug 1* (`/loglevel=1`). The log will show how WinSCP understands both your command-line and individual scripting commands.

### Environment Variables

You can use environment variables in the commands, with syntax `%NAME%`:[1](https://winscp.net/eng/docs/scripting#fn1)

```
put "%FILE_TO_UPLOAD%"
```

Note that variable expansion is different than in Windows batch files:

- You cannot use any [string processing syntax](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#String_processing).
- You cannot use [dynamic/pseudo environment variables](https://en.wikipedia.org/wiki/Environment_variable#Windows_2), such as `%DATE%` or `%RANDOM%`.
- References to undefined variables are kept intact (not removed).
- You can use `%WINSCP_PATH%` to refer to WinSCP [executable](https://winscp.net/eng/docs/executables) path.

### Timestamp

WinSCP automatically resolves `%TIMESTAMP[rel]#format%` to a real time (optionally to a past or future time) with the given format. The `format` may include `yyyy` for year, `mm` for month, `dd` for day, `hh` for hour, `nn` for minute and `ss` for second. For example, the `%TIMESTAMP#yyyy-mm-dd%` resolves to `2016-06-22` on 22 June 2016. See [other formats you can use](http://docwiki.embarcadero.com/Libraries/Berlin/en/System.SysUtils.FormatDateTime).

*Advertisement*

The optional `rel` part, with syntax `[-+]time[YDHNS]`, produces past (`-`) or future (`+`) timestamps. One of the following units must be used: `Y` (years), `D` (days), `H` (hours), `N` (minutes) or `S` (seconds). For example, the `%TIMESTAMP-1D#yyyy-mm-dd%` (the `-1D` meaning one day in the past) resolves to `2016-06-21` on 22 June 2016.

To use `%TIMESTAMP%` on a command-line in a batch file, you need to escape the `%` by doubling it to `%%TIMESTAMP%%`, to avoid a batch file interpreter trying to resolve the variable.

### Script Arguments

You can reference script arguments (passed on command-line using parameter `/parameter`) using syntax `%N%`, where `N` is ordinal number of argument:[1](https://winscp.net/eng/docs/scripting#fn1)

```
put "%1%"
```

### Case Sensitivity of File Names

Note that WinSCP treats filenames in case sensitive manner. So even if your server treats filenames in case insensitive manner, make sure you specify case properly.[2](https://winscp.net/eng/docs/scripting#fn2)

### Comments

To insert comments into the script file, start the line with `#` (hash):

```
# Connect to the server
open mysession
```

## Commands

The following commands are implemented.

To see help for the command, read respective documentation article below or type command `help <command>` directly in console.

| Command                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [call](https://winscp.net/eng/docs/scriptcommand_call)       | Executes arbitrary remote shell command                      |
| [cd](https://winscp.net/eng/docs/scriptcommand_cd)           | Changes remote working directory                             |
| [checksum](https://winscp.net/eng/docs/scriptcommand_checksum) | Calculates checksum of remote file                           |
| [chmod](https://winscp.net/eng/docs/scriptcommand_chmod)     | Changes permissions of remote file                           |
| [close](https://winscp.net/eng/docs/scriptcommand_close)     | Closes session                                               |
| [cp](https://winscp.net/eng/docs/scriptcommand_cp)           | Duplicates remote file                                       |
| [echo](https://winscp.net/eng/docs/scriptcommand_echo)       | Prints message onto script output                            |
| [exit](https://winscp.net/eng/docs/scriptcommand_exit)       | Closes all sessions and terminates the program               |
| [get](https://winscp.net/eng/docs/scriptcommand_get)         | Downloads file from remote directory to local directory      |
| [help](https://winscp.net/eng/docs/scriptcommand_help)       | Displays help                                                |
| [keepuptodate](https://winscp.net/eng/docs/scriptcommand_keepuptodate) | Continuously reflects changes in local directory on remote one |
| [lcd](https://winscp.net/eng/docs/scriptcommand_lcd)         | Changes local working directory                              |
| [lls](https://winscp.net/eng/docs/scriptcommand_lls)         | Lists the contents of local directory                        |
| [ln](https://winscp.net/eng/docs/scriptcommand_ln)           | Creates remote symbolic link                                 |
| [lpwd](https://winscp.net/eng/docs/scriptcommand_lpwd)       | Prints local working directory                               |
| [ls](https://winscp.net/eng/docs/scriptcommand_ls)           | Lists the contents of remote directory                       |
| [mkdir](https://winscp.net/eng/docs/scriptcommand_mkdir)     | Creates remote directory                                     |
| [mv](https://winscp.net/eng/docs/scriptcommand_mv)           | Moves or renames remote file                                 |
| [open](https://winscp.net/eng/docs/scriptcommand_open)       | Connects to server                                           |
| [option](https://winscp.net/eng/docs/scriptcommand_option)   | Sets or shows value of script options                        |
| [put](https://winscp.net/eng/docs/scriptcommand_put)         | Uploads file from local directory to remote directory        |
| [pwd](https://winscp.net/eng/docs/scriptcommand_pwd)         | Prints remote working directory                              |
| [rm](https://winscp.net/eng/docs/scriptcommand_rm)           | Removes remote file                                          |
| [rmdir](https://winscp.net/eng/docs/scriptcommand_rmdir)     | Removes remote directory                                     |
| [session](https://winscp.net/eng/docs/scriptcommand_session) | Lists connected sessions or selects active session           |
| [stat](https://winscp.net/eng/docs/scriptcommand_stat)       | Retrieves attributes of remote file                          |
| [synchronize](https://winscp.net/eng/docs/scriptcommand_synchronize) | Synchronizes remote directory with local one                 |

*Advertisement*

## The Console Interface Tool

Learn about `winscp.com`, the console interface tool.

## Verifying the Host Key or Certificate in Script

The first connection to an SSH server requires [verification of the host key](https://winscp.net/eng/docs/ssh_verifying_the_host_key). To automate the verification in script, use `-hostkey` switch of `open` command to accept the expected host key automatically.

You can find the key fingerprint on [Server and Protocol Information Dialog](https://winscp.net/eng/docs/ui_fsinfo). You can also copy the key fingerprint to clipboard from the confirmation prompt on the first (interactive) connection using *Copy Key* button. *Learn more about obtaining host key fingerprint*.

FTPS/WebDAVS [TLS/SSL certificate](https://winscp.net/eng/docs/tls#certificate) signed by untrusted authority may also need to be verified. To automate the verification in script, use `-certificate` switch of `open` command to accept the expected certificate automatically.

## Running a Script under a Different Account (e.g., Using a Scheduler)

If you are going to run the script under a different account (for example using the Windows scheduler), don’t forget that WinSCP still needs to access its configuration. Note that when using registry as [configuration storage](https://winscp.net/eng/docs/config), the settings are accessible only for your Windows account, so in such a case you may need to either transfer the configuration from your account registry to the other account’s registry or use the [INI file](https://winscp.net/eng/docs/config) instead.

Note that the configuration also includes [verified SSH host keys](https://winscp.net/eng/docs/scripting#hostkey) and FTPS/WebDAVS [TLS/SSL certificates](https://winscp.net/eng/docs/tls#certificate).

## Sharing Configuration with Graphical Mode

WinSCP in scripting/console mode shares [configuration](https://winscp.net/eng/docs/config) with [graphical mode](https://winscp.net/eng/docs/interfaces). This can be both advantageous and disadvantageous.

The advantage is that you can use graphical interface to [configure options for which there are no script commands](https://winscp.net/eng/docs/faq_script_ini).

The disadvantage is that change to configuration in graphical mode may break your script (common example is enabling *Existing files only* option for [synchronization](https://winscp.net/eng/docs/task_synchronize_full)). Also the script is not portable to other machines, when it relies on an external configuration.

If you want to protect your script from such inadvertent change or if you want to make the script portable, you should isolate its configuration from graphical mode explicitly.

The best way to do that is to configure all the options you need using script commands only (`option` command, switches of other commands, [session URL](https://winscp.net/eng/docs/session_url)), or if no such command is available, using [raw site settings](https://winscp.net/eng/docs/rawsettings) and [raw configuration](https://winscp.net/eng/docs/rawconfig). Finally force scripting mode to start with the default configuration using `/ini=nul` command-line parameter.

Alternatively [export your configuration](https://winscp.net/eng/docs/config#export) to a separate INI file and reference it using `/ini=` command-line parameter. Also consider setting the INI file read-only, to prevent WinSCP writing to it, when exiting. Particularly, if you are running multiple scripts in parallel, to prevent different instances of WinSCP trying to write it at the same time.

## Generating Script

You can have WinSCP [generate a script template for you](https://winscp.net/eng/docs/ui_generateurl).

*Advertisement*

## Example

In the example below, WinSCP connects to `example.com` server with account `user`, downloads file and closes the session. Then it connects to the same server with the account `user2` and uploads the file back.

```
# Connect
open sftp://user:password@example.com/ -hostkey="ssh-rsa 2048 xx:xx:xx:xx:xx:xx:xx:xx..."
# Change remote directory
cd /home/user
# Download file to the local directory d:\
get examplefile.txt d:\
# Disconnect
close
# Connect as a different user
open sftp://user2:password@example.com/
# Change the remote directory
cd /home/user2
# Upload the file to current working directory
put d:\examplefile.txt 
# Disconnect
close
# Exit WinSCP
exit
```

Save the script to the file `example.txt`. To execute the script file use the following command.

```
winscp.com /ini=nul /script=example.txt
```

For simple scripts you can specify all the commands on [command-line](https://winscp.net/eng/docs/commandline) using `/command` switch:

```
winscp.com /ini=nul /command "open sftp://user:password@example.com/ -hostkey=""ssh-rsa 2048 xx:xx:xx:xx:xx:xx:xx:xx...""" "get examplefile.txt d:\" "exit"
```

In Windows batch file, you can use `^` to split too long command-line to separate lines by [escaping](https://en.wikipedia.org/wiki/Batch_file#Escaped_characters_in_strings) following new-line character:

```
winscp.com /ini=nul /command ^
    "open sftp://user:password@example.com/ -hostkey=""ssh-rsa 2048 xx:xx:xx:xx:xx:xx...""" ^
    "get examplefile.txt d:\" ^
    "exit"
```

*See other useful example scripts*.

## Converting Script to Code Based on .NET Assembly

When you find yourself limited by scripting capabilities, you may consider [converting your script to code that uses WinSCP .NET assembly](https://winscp.net/eng/docs/library_from_script).













# OpenWrt SSH登录

由于`openwrt`采用的方式`dropbear`代理登录，因此`ssh`的密钥不在用户根目录



通过使用ssh-keygen生成公钥，在两台机器之间互相建立

假设本地机器是client，远程机器为server。

1、使用ssh-keygen生成rsa keygen（在这里会覆盖以前生成的~/.ssh/id_rsa文件，请提前做好备份。）

ssh-keygen -b 1024 -t -rsa

然后一直按回车即可。

2、拷贝公钥到目标机器上，并更名为authorized_keys

scp ~/.ssh/id_rsa.pub root@192.168.8.1:/etc/dropbear/authorized_keys

3、拷贝完成后，正常登陆一次目标主机。

4、退出后重新登陆，这个时候就不在需要ssh密钥就可以登陆目标主机了。



