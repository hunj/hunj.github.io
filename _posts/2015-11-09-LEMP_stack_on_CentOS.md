---
layout: post
title: LEMP 스택 설치 및 설정하기
subtitle: "LEMP 스택은 웹서버를 설치하여 사용하기 위한 오픈 소스 소프트웨어의 한 묶음으로, Linux, nginx (Engine-X 라고 읽는다), MySQL, PHP로 이루어져 있다. 서버가 이미 CentOS가 올려져 있으므로, 리눅스 부분은 이미 설치된 셈이다. 나머지 설치를 하여 스택을 올릴 수 있다"
excerpt_separator: <!--more-->
tags: linux translation
---

(이 글은 [Digital Ocean에 올라온 Etel Sverdlov의 글](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-centos-6)을 번역한 글이며 문제가 될 시 삭제할 예정입니다)

<!--more-->

## 1. epel 저장소 설치하기

`yum`을 이용하여 필요한 소프트웨어를 설치한다. nginx는 CentOS 에서 바로 지원이 안 되기 때문에, epel 저장소를 설치해야 한다:

{% highlight shell %}
sudo yum install epel-release
{% endhighlight %}

## 2. MySQL 설치


{% highlight shell %}
sudo yum install mysql-server
{% endhighlight %}

설치 후 MySQL 재시작:

{% highlight shell %}
sudo /etc/init.d/mysqld restart
{% endhighlight %}

MySQL 설정을 바꾸고 싶으면 다음 설정을 사용하면 된다:

{% highlight shell %}
sudo /usr/bin/mysql_secure_installation
{% endhighlight %}

현재 루트 비밀번호를 물어보며, 지금 막 MySQL을 설치한 경우엔 루트 비밀번호가 없는 경우가 대부분이니 무시하고 엔터를 치면 된다:

{% highlight shell %}
Enter current password for root (enter for none):
OK, successfully used password, moving on...
{% endhighlight %}

다음으로는 루트 비밀번호를 설정하고 싶은지 묻는다. Y 를 입력하여 나오는 지시사항을 따르도록 한다.

CentOS 에서는 예/아니오 식으로 질문을 몇 개 던져 MySQL 설정을 자동화한다.

대부분의 경우 모든 옵션에 대하여 Yes 라고 답하게 될 것이다. 끝나면 MySQL 이 재시작하며 설정된 옵션을 적용하게 된다.

{% highlight shell %}
By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
... Success!

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!
{% endhighlight %}

## 3. nginx 설치하기

`nginx` 역시 MySQL 처럼 yum 을 이용하여 설치한다:

{% highlight shell %}
sudo yum install nginx
{% endhighlight %}

nginx는 자동으로 시작하지 않기에 다음과 같은 명령어를 사용한다:

{% highlight shell %}
sudo /etc/init.d/nginx start
{% endhighlight %}

브라우저를 실행하여 서버의 IP 주소로 접속하면 nginx 가 설치된 것을 확인할 수 있다.

참고로 서버의 IP 주소를 알려면 다음과 같이 입력하면 된다:

{% highlight shell %}
ifconfig eth0 | grep inet | awk '{ print $2 }'
{% endhighlight %}

## 4. PHP 설치하기

`php-fpm` 패키지는 REMI 저장소에 위치해있으나 현재 시점에서는 비활성화되어 있다. REMI 저장소를 활성화시키고 php와 php-fpm 을 설치한다:

{% highlight shell %}
sudo yum install php-fpm php-mysql
{% endhighlight %}

## 5. PHP 설정하기

PHP 설정 중 한가지를 변경해주어야 한다. `php.ini` 파일을 열자:

{% highlight shell %}
sudo vi /etc/php.ini
{% endhighlight %}

`cgi.fix_pathinfo=1` 의 설정값을 `1` 에서 `0`으로 바꿔준다:

{% highlight shell %}
cgi.fix_pathinfo=0
{% endhighlight %}

이 설정이 `1` 로 되어있으면 php 인터프레터가 요청받은 파일과 가장 근접한 파일을 처리하게 된다. 이는 보안상의 문제를 야기할 수 있으므로, `0`으로 설정하여 요청받은 파일명 그대로만을 찾아보게 할 수 있다. `:wq!` 라고 입력 후 엔터를 쳐 설정을 저장하고 vi에서 빠져나오자.

## 6. nginx 설정하기

nginx 기본 설정 파일을 열자:

{% highlight shell %}
sudo vi /etc/nginx/nginx.conf
{% endhighlight %}

`worker processes` 숫자를 서버의 코어 수만큼 지정하고 `:wq!` 로 저장하고 나온다.

이제 nginx의 가상 호스트를 설정해야 한다. nginx 기본 설정 파일과 가상 호스트 설정 파일은 다른 위치에 있다.

{% highlight shell %}
sudo vi /etc/nginx/conf.d/default.conf
{% endhighlight %}

설정 파일은 다음과 같은 변경사항이 있어야 한다:

{% highlight shell %}
#
# The default server
#
server {
    listen       80;
    server_name example.com;


    location / {
        root   /usr/share/nginx/html;
        index index.php  index.html index.htm;
    }

    error_page  404              /404.html;
    location = /404.html {
        root   /usr/share/nginx/html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root           /usr/share/nginx/html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME   $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
{% endhighlight %}

설정 변경의 요약은 다음과 같다:

* `index` 항에 `index.php` 파일을 추가
* `server_name`을 자신의 도메인명 혹은 IP 주소로 변경 (`example.com`을 바꾸면 된다)
* 루트 폴더를 `/usr/share/nginx/html` 로 변경
* `location ~ /.php$ {` 로 시작하는 구간의 주석 해제
* 실제 루트 디렉토리 `/usr/share/nginx/html` 로 접속하게끔 설정
* `fastcgi_param` 을 변경하여 PHP 인터프레터가 루트 디렉토리에서 PHP 스크립트를 찾을 수 있게끔 변경

이제 `php-fpm` 설정을 변경한다:

{% highlight shell %}
sudo vi /etc/php-fpm.d/www.conf
{% endhighlight %}

`user`와 `group`을 `nginx`로 설정한다:

{% highlight shell %}
[..생략..]
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;	will be used.
; RPM: apache Choosed to be able to access some dir as httpd
user = nginx
; RPM: Keep a group allowed to write in log dir.
group = nginx
[..생략..]
{% endhighlight %}

php-fpm을 재시작하여 작업을 마친다:

{% highlight shell %}
sudo service php-fpm restart
{% endhighlight %}

## 7. php info 페이지를 만들어 결과 확인하기

LEMP 스택이 이제 잘 설치되었는지 간단한 php info 페이지를 만들어 확인해보자.

새로운 파일을 작성한다:

{% highlight shell %}
sudo vi /usr/share/nginx/html/info.php
{% endhighlight %}

다음과 같은 내용을 넣는다:

{% highlight php %}
<?php
phpinfo();
?>
{% endhighlight %}

`:wq!` 를 입력하여 저장하고 닫는다.

nginx를 재시작하여 변경사항이 적용되도록 한다:

{% highlight shell %}
sudo service nginx restart
{% endhighlight %}

이렇게 해서 브라우저에서 `http://123.456.789.10/info.php` 페이지로 접속해서 (아이피를 자신의 아이피로 변경한다) php info 페이지를 확인한다.

## 8. 자동 시작 설정하기

서버가 재시작되거나 할 때 대비해서 자동으로 서비스가 실행되게끔 설정한다:

{% highlight shell %}
sudo chkconfig --levels 235 mysqld on
sudo chkconfig --levels 235 nginx on
sudo chkconfig --levels 235 php-fpm on
{% endhighlight %}