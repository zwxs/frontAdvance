## 前端项目开发流程

### 1. 代码规范和格式化

- 首先项目代码格式化需使用 prettier 格式化
  1. 使用插件 npm run format 格式化
  2. 使用 vscode 的 Prettier - Code formatter 插件 然后把保存码格式化 选项改为当前插件的格式化方式

![流程图](https://confluence.mysre.cn/download/attachments/24320830/1.png?version=1&modificationDate=1587379747420&api=v2)

### 2. 项目分支开发

- 克隆项目 拉取 master 分支最新代码

- 新需求开发 需要创建新的开发分支
  - 分支名称命名规范: 日期-当月迭代更新次数-租户名称[-功能]
    1. 如果是基础标准代码的就使用 standard
       例如 20200417-sp4-standard-test
    2. 如果是单个租户的 代码的就使用 租户简写  
       例如 20200417-sp4-sjy-test
- 如果是项目原有 bug 修改 也创建新的开发分支
  - 分支名称命名规范: 日期-bug-租户名称[-功能] 如
    1. 如果是基础标准代码的就使用 standard
       例如 20200417-bug-standard-test
    2. 如果是单个租户的代码的就使用 租户简写  
       例如 20200417-bug-sjy-test
- 然后根据新功能需求写 代码 或者 根据 bug 文档去修改 bug

- 代码写完后如果是测试需要把当前开发分支的代码提交

  1. 如果是测试环境 需要把当前开发分支的代码合并到 origin/dev 分支上
  2. 如果是镜像环境 需要把当前开发分支的代码合并到 origin/alpha 分支上
  3. 如果是正式环境 需要把当前开发分支的代码合并到 origin/master 分支上

  - 注意:
    切记都是从当前开发分支 去 直接合并到 dev 或者 alpha 或者 master 不能 把 dev 合并到 alpha 或者 alpha 合并到 master
  - 原因：
    可能并不是所有的测试 或者 alpha 的 代码都需要上线 可能会导致需要和不需要上线的功能都到 master 导致代码的一些冲突回滚问题很难解决 所以都一定要从当前开发分支直接合并到对应要上的版本的分支

![流程图](https://confluence.mysre.cn/download/attachments/24320830/2.png?version=1&modificationDate=1587379760000&api=v2)

### 3. 项目代码发布版本



- 代码合并完成后在当前需要发布的分支上去执行发布命令

  1. 使用 cmd 输入 npm 命令 npm run publish
  2. 使用 vscode 插件底部的 npm 脚本 选中当前项目找到对应的 publish 去点击发布

  3. 发布过程中可能会遇到一些问题
     - 未指定发布项目的 本地目录地址
       1. 在项目根目录 创建一个 .env.local 的配置文件
       2. 在文件中 写入当前发布项目的本地目录地址  
          CZHOME_PUBLIC_PATH=C:\Users\zhengw16\Desktop\workspace\czhome\public\czbms
       3. 如果项目在 C 盘 或者 没有完全取得读写权限可能会报错 需要设置项目读写权限或者放入其他盘
     - git 版本问题 不同的 git 版本 提交成功后返回的 log 信息不一样
       在 新版本的 git 返回成功日志
       nothing to commit, working tree clean
       在 旧版本的 git 返回成功日志
       nothing to commit, working directory clean
       此时解决方案
       1. 最好是吧 git 升级到最新版本 2.14 以上都可以 (推荐)
       2. 修改项目根目录的 czhome.js 里面的判断 把 nothing to commit, working tree clean 改成 nothing to commit, working directory clean

![流程图](https://confluence.mysre.cn/download/attachments/24320830/3.png?version=1&modificationDate=1587379763000&api=v2)

### 4. 项目部署

- 项目发布完成后 打开 jenkins 自动化处理项目工作台 https://tool-jenkins.mysre.cn/ 1. 找到当前发布的项目 czsaas-test-czhome-ycg 并进入 2. 点击 左上角 Build with Parameters 配置项目的发布的参数 3. 选择当前要发布项目的分支 origin/dev 或者 origin/alpha 或者 origin/master 4. 后面打上发布的备注 tag 5. 点击开始构建 等待完成即可 打开项目地址预览发布效果
  ![流程图](https://confluence.mysre.cn/download/attachments/24320830/4.png?version=1&modificationDate=1587379767000&api=v2)

### 5. 老门户开发流程

#### 1. 根目录下面建立.env.local文件，其中有两个重要配置：
  -PAGE：表示开发的哪个页面，比如SupplierInfo对应的企业档案页面，对应的是src/containers文件下相关的目录
  -TENANT: 表示租户，对应开发的哪个租户
   ![流程图](https://confluence.mysre.cn/download/attachments/10923861/testfdafd.png?version=1&modificationDate=1572947884000&api=v2)
#### 2. 项目运行
  - 使用npm run start 运行当前开发的页面 

#### 3. 模拟登录
  - 如果开发的页面里面涉及到接口需要登录后访问的
  1. 打开对应环境的地址如测试 https://ycz-test.myyscm.com/hdjt 先正常登录账号
  2. 打开 cookies 将一些跟用户有关的cookies 的Domain 改成localhost
  3. 然后再访问本地的页面即可模拟登录状态 请求接口
  ![流程图](https://confluence.mysre.cn/download/attachments/10923861/Snipaste_2020-05-25_11-52-45.png?version=1&modificationDate=1590378775252&api=v2)

#### 4. 项目打包和部署
  1. 使用npm run build 将当前开发的页面文件进行打包
  2. 将打包完成的 当前项目根目录的build 下的static目录里面的所有文件复制
  3. 到PHP项目里面根据如下目录czsaas\web_assets\web 如是新页面需要创建当前页面的文件夹 如是旧的覆盖文件夹里面的内容即可
    例如当前开发 开发商租期管理 之前没有 就新建一个devLease文件夹 将static目录的css js文件夹复制进devLease文件夹
  4. 然后找到PHP的真实页面文件引入我们打包后的文件
    czsaas\web_saas\modules 找到对应的开发环境 例如开发商 就是 web_kfsbackstage
    然后再找到里面的views页面文件夹 后台配置的php文件  tenant-time\index.php
    在当前PHP文件里面 引入我们打包后的css和js

      ```php
          <?php

            use common\utils\DomainUtil;
            use \yii\helpers\Html;

            $this->title = "租期信息";
            $this->registerCssFile(DomainUtil::getDomainLink("JcsStatic", "/res1.5/css/developer/page/erp_state.css"), ['rel' => 'stylesheet']);
            $this->registerCssFile(DomainUtil::getDomainLink("JcsStatic",  "/devLease/css/main.css"), ['rel' => 'stylesheet']);
            $this->params["menuId"]="207";
            ?>
          <script type="text/javascript" src="<?= DomainUtil::GetDomainLink("JcsStatic", "/res1.5/js/common/jquery.form.js") ?>"></script>

          <div id="content" class="wrap clear con_store">
              <?php $this->beginContent('@web_kfsbackstage/views/layouts/sidebar.php'); ?>
              <?php $this->endContent(); ?>
              <div class="content" id="root"></div>
              <div style="clear: both;"></div>
          </div>


          <script src="<?= DomainUtil::getDomainLink("JcsStatic", "/devLease/js/main.js") ?>"></script>

      ```

      - 注意核心点 我们react元素是渲染到一个id为 root 元素里面
      所以默认情况下一定也要在当前php文件里面的对应位置添加id为root的元素 这样后面的js才能起效果

    5. 然后运行php的查看页面是否加载成功


![流程图](https://confluence.mysre.cn/download/attachments/10923861/Snipaste_2020-05-25_11-54-05.png?version=1&modificationDate=1590378858877&api=v2)
![流程图](https://confluence.mysre.cn/download/attachments/10923861/image2020-5-25_11-56-13.png?version=1&modificationDate=1590378973065&api=v2)
![流程图](https://confluence.mysre.cn/download/attachments/10923861/image2020-5-25_11-56-20.png?version=1&modificationDate=1590378980825&api=v2)
![流程图](https://confluence.mysre.cn/download/attachments/10923861/image2020-5-25_11-57-25.png?version=1&modificationDate=1590379045689&api=v2)

#### 5. 多页面嵌套打包部署
  1. 此时不能在PHP里面写多个id为 root的元素 可以通过在body中appendChild一个新元素的方式再添加新的要嵌入的页面或者组件
  2. 修改当前czbmsold里面的src\index.js 

  ```jsx
    if (process.env.PAGE == 'LeaseRemind') {
      var wrap = document.createElement('div');
      document.body.appendChild(wrap);

      ReactDOM.render(<Body />, wrap);
    } else {
      const render = (Component) => {
        ReactDOM.render(
          <AppContainer>
            <Provider store={store}>
              <LocaleProvider locale={zhCN}>
                <Component />
              </LocaleProvider>
            </Provider>
          </AppContainer>,
          document.getElementById('root')
        );
      };

      render(Body);
      // ReactDOM.render(<Provider store={store}><Body /></Provider>, document.getElementById('root'));

      if (module.hot) {
        module.hot.accept('./App', () => {
          render(Body);
        });
      }
    }

  ```
  - 通过制定的页面判断当前页面的渲染方式为追加渲染而不是默认的渲染到root里面
  - 然后打包把static 里面的 内容 复制到php的打包目录里面
  - 然后再将改页面要嵌入到的php文件中引入当前打包的css和js 此时因为我们是js里面创建的动态元素不需要依赖id 为root的元素了即可实现多个嵌入多个页面的方案
  ```php
  <?php
    use web_pub\util;
    use common\utils\DomainUtil;
    use \yii\helpers\Html;

    $this->params['developersModel'] =util\CommonUtil::initDeveloper();
    $this->registerCssFile(DomainUtil::getDomainLink("JcsStatic",  "/remind/css/main.css"), ['rel' => 'stylesheet']);
    ?>

    <div class="clear" id="footer">

      
        <div class="wrap">

            <p class="contact_info">
                <span><?= $this->params['developersModel']->companyName ?></span>
                <span class="ml10"></span>
            </p>
            <p class="copyright">
                ©<?=\common\utils\DateTimeUtil::currYear()?> &nbsp;&nbsp;&nbsp;<?= $this->params['developersModel']->copyright ?>
                &nbsp;&nbsp;&nbsp;


            </p>
        </div>
    </div>

    <script src="<?= DomainUtil::getDomainLink("JcsStatic", "/remind/js/main.js") ?>"></script>
  ```
  ![流程图](https://confluence.mysre.cn/download/attachments/10923861/image2020-5-25_11-58-44.png?version=1&modificationDate=1590379124352&api=v2)
  ![流程图](https://confluence.mysre.cn/download/attachments/10923861/image2020-5-25_12-0-13.png?version=1&modificationDate=1590379214005&api=v2)