# centos安装python3.10.11

### 为什么是3.10.11
因为本地开发环境就是3.10.11，为了避免不必要的麻烦，保持环境一致
### 一定要全部看完一遍再进行安装
### 一定要全部看完一遍再进行安装
### 一定要全部看完一遍再进行安装


### 1.安装相应的编译工具
建议在root下操作，会方便很多，一定要安装，否则编译安装会报错,这一步非常重要！

#### 下面这两个必须单独执行,特别是SSl部分
```
sudo yum install -y epel
sudo yum install -y openssl11-devel
```
如果安装epel显示`No package epel available`,则执行
```
$ yum search epel
================================ N/S matched: epel ================================
epel-release.noarch : Extra Packages for Enterprise Linux repository configuration

$ yum install epel-release.noarch
```
接着安装下面的依赖
```
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
yum install -y libffi-devel
yum install zlib* -y
yum install yum-utils
yum-builddep python
```

### 2.下载安装包
```wget https://www.python.org/ftp/python/3.10.11/Python-3.10.11.tgz```

### 3.解压
```tar -zxvf Python-3.10.11.tgz```

### 4.创建编译安装目录
``mkdir /usr/local/python3``

### 5.安装
```cd Python-3.10.11```

第一个指定安装的路径,不指定的话,安装过程中可能软件所需要的文件复制到其他不同目录,删除软件很不方便,复制软件也不方便.


第二个可以提高python10%-20%代码运行速度.


第三个是为了安装pip需要用到ssl,后面报错会有提到.


```./configure --prefix=/usr/local/python3 --enable-optimizations --with-ssl ```

```make && make install```

在python3.8后，使用enable-optimizations 这个参数在服务器使用的是低版本的gcc时会报错，有三个处理方法：

1、可以先升级gcc
2、不加–enable-optimizations
3、使用以下依赖

```
yum install yum-utils
yum-builddep python
```
不过方法三好像是个概率事件，不一定会成功,而且升级gcc是当前client有效，关闭后又会恢复到原来的版本
所以强烈建议不使用``--enable-optimizations``

### 6.创建软链接
相当于windows环境变量，如下写不会默认还是Python2.7，不需要修改yum配置。

```
ln -sf /usr/local/python3/bin/python3.10 /usr/local/bin/python3
# 软链接至/bin/python3方便写脚本
ln -s /usr/local/python3/bin/python3.10 /bin/python3
ln -sf /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```
如果建立时提示如下报错信息:
```ln: failed to create symbolic link '/usr/bin/python3': File exists```
解决方法：

ln -sf 即参数多加个f即可
```ln -sf /usr/local/python3/bin/python3.9 /bin/python3```

### 7.验证是否成功
```
python3 --version
pip3 --version
```

一般安装到这里就结束了，具体参考：https://blog.csdn.net/qq_61313949/article/details/124144829
后续还有错误处理和pipenv的安装


### 卸载python
```
rpm -qa|grep python3|xargs rpm -ev --allmatches --nodeps       #卸载pyhton3
whereis python3 |xargs rm -frv           #删除所有残余文件
```
成功卸载！
```
whereis   python       #查看现有安装的python
```

# Error处理

### 1. pip install包的时候的时候报错
```
Retrying (Retry(total=0, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.",)': /simple/pip/
Could not fetch URL https://pypi.org/simple/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/pip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.",)) - skipping
Requirement already up-to-date: pip in /usr/local/lib/python3.6/site-packages (18.1)
pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Could not fetch URL https://pypi.org/simple/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/pip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.",)) - skipping

```

解决方案:https://stackoverflow.com/questions/56552390/how-to-fix-ssl-module-in-python-is-not-available-in-centos
