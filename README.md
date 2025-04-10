# Git本地仓库与远程仓库创建联系的方法

### **方法一：直接从远程仓库克隆**

**适用场景**：需要获取远程仓库完整副本到本地时

**操作步骤**：

1. 获取远程仓库地址（HTTPS/SSH）（链接可以从`Github\Gitee`获取）

   ```bash
   git clone https://github.com/用户名/仓库名.git  # HTTPS协议 
   git clone git@github.com:用户名/仓库名.git     # SSH协议
   ```

### **方法二：本地仓库推送至远程**

**适用场景**：已有本地仓库，需关联到远程新仓库

**操作步骤**：

1. **初始化本地仓库**

   ```bash
   git init
   ```

2. **关联远程仓库**

   ```bash
   git remote add origin 远程仓库地址  # 添加远程别名（如origin）
   ```

3. **提交代码并推送**

   ```bash
   git add .                          # 添加文件到暂存区
   git commit -m "仓库初始化"     # 提交到本地仓库
   git push -u origin master          # 推送并设置上游分支（首次需加-u）
   ```

### **关键配置与问题处理**

1. **认证方式选择**

   - **HTTPS协议**：需用户名+密码（或GitHub个人访问令牌）

   - **SSH协议**（推荐）：

     - 生成密钥对：

       ```bash
     ssh-keygen -t rsa -C "your_email@example.com"  # 三次回车完成生成
       ```

     - 将公钥`id_rsa.pub`内容添加到`GitHub/Gitee`的SSH Keys设置中

2. **权限问题处理**

   - 推送时若报错`403 Permission denied`，需检查：

     - 远程仓库是否添加当前用户为协作者（GitHub设置 → Collaborators）

     - 本地Git配置的用户名/邮箱是否与远程账户匹配

       ```bash
       git config --global user.name "用户名"
       git config --global user.email "邮箱"
   
3. **分支管理**

   - 首次推送需指定远程分支：`git push -u origin 分支名`
   - 后续推送可简化：`git push`

4. **出现22端口拒绝的问题**

   - 出现报错: `ssh: connect to host github.com port 22: Connection refused`可以修改端口至443

     1. **找到 SSH 配置文件**

        在 Windows 下，SSH 配置文件通常位于用户目录的 `.ssh` 文件夹中（例如：`C:\Users\<你的用户名>\.ssh\config`）。如果文件不存在，可以手动创建一个：

        1. 打开资源管理器并导航到 `C:\Users\<你的用户名>\.ssh`。
        2. 在 `.ssh` 文件夹下，新建一个文件，命名为 `config`（没有扩展名）

     2. **编辑SSH配置文件**

        - 用记事本或其他文本编辑器打开 `config` 文件，添加以下内容：

        ```bash
        Host github.com
        HostName ssh.github.com  # 这是最重要的部分
        User git
        Port 443
        PreferredAuthentications publickey
        IdentityFile C:\Users\<你的用户名>\.ssh\id_rsa
        ```

        **注意**:

        - `IdentityFile` 的路径需要根据你实际存储 SSH 密钥的位置调整，通常是 `id_rsa` 或 `id_ed25519`。

     3. **验证SSH配置**

        - 开命令提示符或 PowerShell，运行以下命令测试连接：

        ```bash
        ssh -T git@github.com
        ```

        - 如果配置正确，你应该看到以下输出：

        ```bash
        Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.
        ```

     4. **配置Git使用新端口**

        - 在命令提示符或 PowerShell 中运行以下命令：

        ```bash
        git config --global url."ssh://git@ssh.github.com:443".insteadOf "ssh://git@github.com"
        ```