# 环境搭建

## 环境说明

- 开发工具：subline3,phpstorm-2020.1
- 核心框架：[TP6稳定版ThinkPHP V6.0.2](https://www.kancloud.cn/manual/thinkphp6_0)
- 服务器：Nginx1.15.11
- [在线作图](https://processon.com/)
- [git仓库](https://github.com/mumozi/tp6_mall)

## 安装TP6稳定版

```bash
composer create-project topthink/think tp6_mall
```

!>下载慢可以更换国内源`composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/`另外php版本PHP >= 7.1.0否则下载失败。

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200609145045.png)

## 测试

```bash
php think run 
php think run -p 80
```

## 服务器Nginx

手动绑定域名`tp.cn`

```bash
#以下是伪静态配置
     location / { 
       if (!-e $request_filename) {
       rewrite  ^/index.php(.*)$  /index.php?s=$1  last;
       rewrite  ^(.*)$  /index.php?s=$1  last;
       break;
        }
     }
```

