## 前端项目开发流程

### 1. 代码规范和格式化

- 首先项目代码格式化需使用 prettier 格式化  
    1. 使用插件 npm run format  格式化
    2. 使用vscode的 Prettier - Code formatter 插件 然后把保存码格式化 选项改为当前插件的格式化方式

![流程图](http://baidu.com/pic/doge.png)

### 2. 项目分支开发

- 克隆项目 拉取master分支最新代码

- 新需求开发 需要创建新的开发分支 
    - 分支名称命名规范: 日期-当月迭代更新次数-租户名称[-功能]  
        1. 如果是基础标准代码的就使用 standard
            例如 20200417-sp4-standard-test 
        2. 如果是单个租户的 代码的就使用 租户简写  
            例如20200417-sp4-sjy-test
- 如果是项目原有bug修改 也创建新的开发分支
    - 分支名称命名规范: 日期-bug-租户名称[-功能]  如
        1. 如果是基础标准代码的就使用 standard
            例如 20200417-bug-standard-test 
        2. 如果是单个租户的代码的就使用 租户简写  
            例如20200417-bug-sjy-test
- 然后根据新功能需求写 代码 或者 根据bug文档去修改bug

![流程图](http://baidu.com/pic/doge.png)

### 3. 项目代码发布版本

- 代码写完后如果是测试需要把当前开发分支的代码提交 
    1. 如果是测试环境 需要把当前开发分支的代码合并到 origin/dev 分支上 
    2. 如果是镜像环境 需要把当前开发分支的代码合并到 origin/alpha 分支上
    3. 如果是正式环境 需要把当前开发分支的代码合并到 origin/master 分支上  
    - 注意: 
        切记都是从当前开发分支 去 直接合并到 dev 或者 alpha  或者 master 不能 把dev合并到alpha 或者 alpha合并到master
    - 原因： 
        可能并不是所有的测试 或者 alpha 的 代码都需要上线  可能会导致需要和不需要上线的功能都到master 导致代码的一些冲突回滚问题很难解决 所以都一定要从当前开发分支直接合并到对应要上的版本的分支

- 代码合并完成后在当前需要发布的分支上去执行发布命令
    1. 使用cmd 输入npm命令 npm run publish
    2. 使用vscode插件底部的 npm 脚本 选中当前项目找到对应的publish去点击发布

    3. 发布过程中可能会遇到一些问题
        - 未指定发布项目的 本地目录地址
            1. 在项目根目录 创建一个 .env.local 的配置文件
            2. 在文件中 写入当前发布项目的本地目录地址      
                CZHOME_PUBLIC_PATH=C:\Users\zhengw16\Desktop\workspace\czhome\public\czbms
            3. 如果项目在C盘 或者 没有完全取得读写权限可能会报错 需要设置项目读写权限或者放入其他盘
        - git版本问题 不同的git版本 提交成功后返回的log信息不一样
            在 新版本的git 返回成功日志 
            nothing to commit, working tree clean
            在 旧版本的git 返回成功日志
            nothing to commit, working directory clean
            此时解决方案
            1. 最好是吧git升级到最新版本 2.14 以上都可以 (推荐)
            2. 修改项目根目录的 czhome.js 里面的判断 把 nothing to commit, working tree clean 改成  nothing to commit, working directory clean
   
![流程图](http://baidu.com/pic/doge.png)

### 4. 项目部署

- 项目发布完成后 打开 jenkins 自动化处理项目工作台 https://tool-jenkins.mysre.cn/
    1. 找到当前发布的项目 czsaas-test-czhome-ycg 并进入
    2. 点击 左上角 Build with Parameters  配置项目的发布的参数
    3. 选择当前要发布项目的分支 origin/dev 或者  origin/alpha 或者 origin/master
    4. 后面打上发布的备注tag
    5. 点击开始构建 等待完成即可 打开项目地址预览发布效果
![流程图](http://baidu.com/pic/doge.png)