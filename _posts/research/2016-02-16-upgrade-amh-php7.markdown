---
layout: post
title: AMH升级到PHP 7.0.0-dev脚本
date: 2016-02-16 13:49:25  +0800
categories: 折腾
---

众所周知PHP7已经是PHP dev的主branch了。
PHP7 代码可以在https://github.com/php/php-src 找到
注意主branch可能会编译不通过，你可以去手选旧的commit去编译，我当前使用的是 commit:ef058b7cb52fe845b1a4f69240324bd7f6b04575
可以参考一下，这个版本似乎socket编译不通过，我从编译脚本拿掉了
脚本保存在 任意目录 文件名:upgrade_php7_amh.sh

{% highlight shell %}


#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
# Check if user is root
if [ $(id -u) != "0" ]; then
echo "Error: You must be root to run this script, please use root to install lnmp"
exit 1
fi
clear
echo "========================================================================="
echo "Upgrade PHP for AMH, Written by Timothy Zhang"
echo "========================================================================="
echo ""
echo "For more information please visit http://php7.greencms.net/"
echo "========================================================================="
cur_dir=$(pwd)
old_php_version=`/usr/local/php/bin/php -r 'echo PHP_VERSION;'`
#set php version
php_version="7.0.0"
echo "=================================================="
echo "You want to upgrade php version to $php_version"
echo "=================================================="
get_char()
{
SAVEDSTTY=`stty -g`
stty -echo
stty cbreak
dd if=/dev/tty bs=1 count=1 2> /dev/null
stty -raw
stty echo
stty $SAVEDSTTY
}
echo ""
echo "Press any key to start...or Press Ctrl+c to cancel"
char=`get_char`
echo "============================check files=================================="
if [ -s php-7.0.0.zip ]; then
echo "php-7.0.0.zip [found]"
unzip php-7.0.0.zip
else
echo "Error: php-7.0.0.zip.tar.gz not found!!!download now......"
wget -O php-7.0.0.zip https://github.com/php/php-src/archive/master.zip
if [ $? -eq 0 ]; then
echo "Download php-7.0.0.zip successfully!"
unzip php-7.0.0.zip
else
echo "WARNING!May be the php version you input was wrong,please check!"
sleep 5
exit 1
fi
fi
if [ -s autoconf-2.59.tar.gz ]; then
echo "autoconf-2.59.tar.gz [found]"
else
echo "Error: autoconf-2.59.tar.gz not found!!!download now......"
wget -c http://ftp.gnu.org/gnu/autoconf/autoconf-2.59.tar.gz
fi
echo "============================check files=================================="
echo "Stoping AMH..."
/root/amh/nginx stop
/root/amh/php stop
/root/amh/mysql stop
if [ -s /usr/local/autoconf-2.59/bin/autoconf ] && [ -s /usr/local/autoconf-2.59/bin/autoheader ]; then
echo "check autconf 2.59: OK"
else
tar zxvf autoconf-2.59.tar.gz
cd autoconf-2.59/
./configure --prefix=/usr/local/autoconf-2.59
make && make install
cd ../
fi
echo "============================install re2c================================="
wget http://sourceforge.net/projects/re2c/files/re2c/0.13.5/re2c-0.13.5.tar.gz/download -O re2c-0.13.5.tar.gz
tar zxf re2c-0.13.5.tar.gz && cd re2c-0.13.5
./configure
make && make install
cd ../
echo "============================install bison================================="
wget http://ftp.gnu.org/gnu/bison/bison-3.0.3.tar.gz
tar zxf bison-3.0.3.tar.gz && cd bison-3.0.3
./configure
make && make install
cd ../
echo "============================install libmcrypt================================="
wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/attic/libmcrypt/libmcrypt-2.5.7.tar.gz
tar -zxvf libmcrypt-2.5.7.tar.gz && cd libmcrypt-2.5.7
./configure
make && make install
cd ../
echo "============================install libiconv================================="
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
tar -zxvf libiconv-1.14.tar.gz && cd libiconv-1.14
./configure --prefix=/usr/local
make && make install
cd ../
ldconfig
ln -s /usr/lib/libevent-1.4.so.2 /usr/local/lib/libevent-1.4.so.2
ln -s /usr/lib/libltdl.so /usr/lib/libltdl.so.3
cd $cur_dir
echo "Stop php-fpm....."
killall php-fpm
echo "Start install php-7-dev....."
#Backup old php version configure files
echo "Backup old php version configure files......"
mkdir -p /root/phpconf
cp /usr/local/php/etc/php-fpm.conf /root/phpconf/php-fpm.conf.old.bak
cp /usr/local/php/etc/php.ini /root/phpconf/php.ini.old.bak
mv /usr/local/php /usr/local/php_old
cd $cur_dir
export PHP_AUTOCONF=/usr/local/autoconf-2.59/bin/autoconf
export PHP_AUTOHEADER=/usr/local/autoconf-2.59/bin/autoheader
cd php-src-master/
./buildconf --force
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config --with-pdo-mysql=/usr/local/mysql --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --enable-discard-path --enable-magic-quotes --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --enable-mbstring --with-mcrypt --enable-ftp --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --with-mime-magic --enable-opcache --disable-fileinfo
make ZEND_EXTRA_LIBS='-liconv'
make install
# echo "Copy php-fpm init.d file......"
# cp $cur_dir/php-$php_version/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
# chmod +x /etc/init.d/php-fpm
echo "Copy new php configure file."
mkdir -p /usr/local/php/etc
cp php.ini-production /usr/local/php/etc/php.ini
cd $cur_dir
# php extensions
echo "Modify php.ini......"
sed -i 's/post_max_size = 8M/post_max_size = 50M/g' /usr/local/php/etc/php.ini
sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 50M/g' /usr/local/php/etc/php.ini
sed -i 's/;date.timezone =/date.timezone = PRC/g' /usr/local/php/etc/php.ini
sed -i 's/short_open_tag = Off/short_open_tag = On/g' /usr/local/php/etc/php.ini
sed -i 's/; cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /usr/local/php/etc/php.ini
sed -i 's/; cgi.fix_pathinfo=0/cgi.fix_pathinfo=0/g' /usr/local/php/etc/php.ini
sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /usr/local/php/etc/php.ini
sed -i 's/max_execution_time = 30/max_execution_time = 300/g' /usr/local/php/etc/php.ini
sed -i 's/register_long_arrays = On/;register_long_arrays = On/g' /usr/local/php/etc/php.ini
sed -i 's/magic_quotes_gpc = On/;magic_quotes_gpc = On/g' /usr/local/php/etc/php.ini
sed -i 's/disable_functions =.*/disable_functions = stream_socket_server,fsocket/g' /usr/local/php/etc/php.ini
echo "Restore old php-fpm configure file......"
mkdir -p /usr/local/php/etc/fpm/
cp -R /usr/local/php_old/etc/fpm/* /usr/local/php/etc/fpm/
mkdir -p /usr/local/php/var/run/pid/
echo "Creating new php-fpm configure file......"
cat >/usr/local/php/etc/php-fpm.conf<

{% endhighlight %}
