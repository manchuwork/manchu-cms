## LaraCMS 后台管理系统 

如果你觉得还不错，请 Star , Fork 给作者鼓励一下。

## LaraCMS
https://www.laracms.cn/

## QQ群：
**LaraCMS官方①群**: 172960867

## 开发手册：
https://www.kancloud.cn/wanglelecc/laracms/

## 在线预览：
https://www.56br.com/


## 说明

## LaraCMS 功能模块：
- 用户管理
- 权限管理
- 角色管理
- 站点信息
- 友情链接
- 栏目导航
- 分类管理
- 文章管理
- 页面管理
- 幻灯管理
- 微信公众号管理
- 第三方登录
- 前端 API
- 文章多图，多附件管理
- 集成文件秒传，分片上传
- 自定义表单
- 分词搜索
- 万能表单

## LaraCMS 特性：
- 支持文章多图
- 支持文章多附件
- 支持文件秒传，分片上传...等功能



基于 laravel 5.5 开发，包含了 内容管理 和 API 服务两部分。

LaraCMS 最初试图用 Laravel 为自己打造一把锋利建站工具，如今已渐渐成熟可用了，还是继续开源出来，提供给有需要的朋友使用，也希望自己能够继续完善。

目前基本上能满足各种企业站的需求了，下一步计划将商城模块集成进来。可能需要很长一段时间才能更新了，而且下一版本可能会改用扩展的方式开发，便于以后的升级维护。

如果想要商用需自行测试评估可用性。

之前因为功能变化频繁，未写使用说明文档，后面我会抽时间补上。

### 使用对象
有一定基础的 Laravel 开发者，非普通站长。

### 预览
<p><img src="http://img.56br.com/images/laracms-login.png"></p>
<p><img src="http://img.56br.com/images/laracms-main.png"></p>
<p><img src="http://img.56br.com/images/laracms.jpg"></p>

- http://img.56br.com/images/laracms-login.jpg
- http://img.56br.com/images/laracms-main.jpg
- http://img.56br.com/images/laracms.jpg


## 环境需求

* Composer
* PHP >= 7.1.3
* OpenSSL PHP 扩展
* PDO PHP 扩展
* Mbstring PHP 扩展
* Tokenizer PHP 扩展
* XML PHP 扩展
* Ctype PHP 扩展
* JSON PHP 扩展
* Mysql 5.7+
> 中文分词会占用内存，请将 `php.ini` 的 `memory_limit` 参数调整至 256M

## 安装
安装方法请移步
https://www.kancloud.cn/wanglelecc/laracms/840009


## 安装后

修改参数
```bash
cp .env .env.example
```
修改数据库配置

```bash
php artisan key:generate
php artisan vendor:publish --tag=config
php artisan vendor:publish --tag=public
php artisan vendor:publish --tag=laracms-view-errors
php artisan storage:link
```

可以去看一下 public 目录下的 storage 软连接是否已存在，以此来判断。

安装好之后，修改 .env 配置数据库。

### 强制覆盖 使用vendor替换目前的文件
```bash
php artisan key:generate

php artisan vendor:publish --tag=config --force
php artisan vendor:publish --tag=public --force
php artisan vendor:publish --tag=laracms-view-errors --force

php artisan storage:link
```
### 执行迁移
```bash
php artisan migrate

```

执行完就可以访问了（要先配置好虚拟主机）。
http://example.com/administrator

注：要先配置好数据库，默认用户: 
admin@manchu.work/admin

# 其他：
# 生成索引
```
php artisan laracms:article-index
```
#清理文件分片
前端文件上传开启分片上传后，可能会因网络问题上传失败，残留在服务器上，所以每天午夜执行定期执行一次清理工作。

### 手动清理

```
php artisan laracms:uploader
```
# 区块同步
区块信息虽然保存在数据库，但不可再后台创建和删除，只能配置。添加删除区块，只可以再配置文件配置，然后通过命令同步。主要是考虑到项目上线后，再次迭代开发，区块信息不同步的，通过命令可以一键同步，避免手工操作出错。

```
php artisan laracms:sync-block
```
# 清理分片
### 清理文件分片
前端文件上传开启分片上传后，可能会因网络问题上传失败，残留在服务器上，所以每天午夜执行定期执行一次清理工作。

手动清理
```
php artisan laracms:uploader
```
模型授权
定义授权
路径：app/Policies

示例：
定义授权
```php

app/Policies/BlockPolicy.php

namespace App\Policies;

use App\Models\User;
use App\Models\Block;

/**
 * 区块授权策略
 *
 * Class BlockPolicy
 * @package Wanglelecc\Laracms\Policies
 */
class BlockPolicy extends Policy
{

    public function index(User $user, Block $block)
    {
        return $user->can('manage_block');
    }

    public function create(User $user, Block $block)
    {
        return false;
    }

    public function update(User $user, Block $block)
    {
        return $user->can('manage_block');
    }

    public function destroy(User $user, Block $block)
    {
        return false;
    }
}
```

可通过 Laravel 提供的 php artisan make:policy 自动生成；检查的权限节点需要预先添加。

#注册授权
在 AuthServiceProvider 服务提供者的 policies 属性中：

```php
protected $policies = [
    .
    .
    .
   \App\Models\Block::class => \App\Policies\BlockPolicy::class,
    .
    .
    .
]
```

#使用授权
一般在控制器中使用
BlocksController:
```php

 public function create(Block $block, Request $request){
       # 调用授权
       $this->authorize('create', $block);
       $type = config('blocks.types.'.$request->type) ?  $request->type : '';

      return backend_view('blocks.create_and_edit', compact('block', 'type'));
  }
```
  
  
  

# 模型观察者
###  观察者
####  路径：app/Observers
###  示例：
###  定义观察者
```php
  app/Observers/BlockObserver.php
  
  namespace App\Observers;
  
  use App\Models\Block;
  use Illuminate\Support\Facades\Auth;
  
  // creating, created, updating, updated, saving,
  // saved,  deleting, deleted, restoring, restored
  
  /**
   * 区块观察者
   *
   * Class BlockObserver
   * @package Wanglelecc\Laracms\Observers
   */
  class BlockObserver
  {
      public function creating(Block $block)
      {
          $block->object_id || $block->object_id = create_object_id();
          $block->created_op || $block->created_op = Auth::id();
          $block->updated_op || $block->updated_op = Auth::id();
      }
  
      public function updating(Block $block)
      {
          $block->updated_op = Auth::id();
      }
      
      public function updated(Block $block){
          Block::clearCache($block->object_id);
      }
  
      public function saving(Block $block){
          if(is_array($block->content) || is_object($block->content)){
              $block->content = json_encode($block->content, JSON_UNESCAPED_UNICODE);
          }
      }
  }

```


备份：
```php
php artisan backup:run --disable-notifications
```
  
