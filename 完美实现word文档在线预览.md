# 完美实现Word文档在线预览 - libreoffice4+jodconverter+swftools+FlexPaper

http://www.libreoffice.org/download/libreoffice-fresh/



rpm -ivh axel-2.4-1.el6.rf.i686.rpm



axel -n 30 -o /home/work http://download.documentfoundation.org/libreoffice/stable/4.4.5/rpm/x86/LibreOffice_4.4.5_Linux_x86_rpm.tar.gz

axel -n 30 -o /home/work http://192.168.1.15/work.zip





axel -n 30 -o /home/work http://192.168.1.38/pdf/LibreOffice_4.4.5_Linux_x86_rpm.tar.gz



-------------------------------------

安装LibreOffice

1、安装依赖包：

yum -y install giflib-devel libjpeg-devel freetype-devel t1lib-devel zlib





tar -zxvf LibreOffice_4.4.5_Linux_x86_rpm.tar.gz

解压后生成文件夹zh-CN 进到RPMS目录下，直接



cd LibreOffice_4.4.5.2_Linux_x86_rpm/RPMS

yum localinstall *.rpm



mv ziti/* /opt/libreoffice4.4/share/fonts/truetype/



开启libreoffice4之前，先确认java是否已经安装：java -version

开机启动：vim /etc/rc.d/rc.local

/opt/libreoffice4.4/program/soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard &

查看服务是否启动（端口8100是否被soffice占用）：netstat -lnp |grep 8100

开启：

killall soffice.bin



/opt/libreoffice4.4/program/soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard &



java -jar /opt/jodconverter/lib/jodconverter-cli-2.2.2.jar /www/wwwroot/default/cuixiang.docx /www/wwwroot/default/1.pdf >/dev/null 2>/www/wwwroot/default/1.log &

/opt/swftools/bin/pdf2swf -o /www/wwwroot/default/1.swf -T -z -t -f /www/wwwroot/default/1.pdf -s languagedir=/usr/share/xpdf/xpdf-chinese-simplified -s flashversion=9



-------------------------------------------------------------

卸载：java-1.6.0-openjdk-1.6.0.0-1.45.1.11.1.el6.i686

yum -y remove java

1.下载Java

http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

# wget http://download.oracle.com/otn-pub/java/jdk/8u51-b16/jdk-8u51-linux-x64.rpm?AuthParam=1438217079_73696a3b281213d10ad745e342d15b34



axel -n 30 -o /www/work http://download.oracle.com/otn-pub/java/jdk/8u51-b16/jdk-8u51-linux-x64.rpm?AuthParam=1438217079_73696a3b281213d10ad745e342d15b34



2.重命名下载文件





3.安装下载文件

#yum localinstall jdk-8u51-linux-i586.rpm

JDK默认安装在/usr/java中。



4.检查是否安装成功

# java -version



5.配置环境

# vi /etc/profile

在最后增加下面信息

#set java environment

JAVA_HOME=/usr/java/default

JRE_HOME=/usr/java/default/jre

PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin

CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib

export JAVA_HOME JRE_HOME PATH CLASSPATH



6.修改生效

# source /etc/profile

# echo $PATH

/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin:/usr/java/default/bin:/usr/java/default/jre/bin



-------------------------------------------------------------

安装jodconverter，安装之后可以实现doc文档转成pdf。



文件下载地址为http://www.artofsolving.com/opensource/jodconverter

axel -n 30 -o /www/data http://nchc.dl.sourceforge.net/project/jodconverter/JODConverter/2.2.2/jodconverter-2.2.2.zip

unzip jodconverter-2.2.2.zip

mkdir -p /opt/jodconverter

cp -a jodconverter-2.2.2/* /opt/jodconverter/



下载了之后直接解压，解压到/opt目录下/opt/jodconverter/，使用到的文件是安装包内的lib/jodconverter-cli-2.2.2.jar。

测试是否可以使用



java -jar /opt/jodconverter/lib/jodconverter-cli-2.2.2.jar /www/wwwroot/default/test.txt /www/wwwroot/default/1.pdf





java -jar /opt/jodconverter/lib/jodconverter-cli-2.2.2.jar /www/wwwroot/default/test.txt /www/wwwroot/default/1.pdf >/dev/null 2>/www/wwwroot/default/1.log &



出现此提示，运行成功：

Jul 29, 2015 10:37:52 AM com.artofsolving.jodconverter.openoffice.connection.AbstractOpenOfficeConnection connect

INFO: connected

Jul 29, 2015 10:37:52 AM com.artofsolving.jodconverter.openoffice.connection.AbstractOpenOfficeConnection disposing

INFO: disconnected



----------------------------------------

----

中文支持安装：

mkdir -p /usr/local/share/xpdf

cd /usr/local/share/xpdf/

下载中文支持及字体库

axel -n 30 -o /www/work ftp://ftp.foolabs.com/pub/xpdf/xpdf-chinese-simplified.tar.gz

axel -n 30 -o /www/work http://www.nginxs.com/download/font.zip

tar zxvf xpdf-chinese-simplified.tar.gz

mv xpdf-chinese-simplified /usr/local/share/xpdf/

unzip font.zip

mv Gbsn00lp.ttf gkai00mp.ttf /usr/local/share/xpdf/xpdf-chinese-simplified/CMap/

cd /usr/local/share/xpdf/xpdf-chinese-simplified

编辑,加入新增的字体



vi add-to-xpdfrc

内容如下：

cidToUnicode    Adobe-GB1       /usr/local/share/xpdf/chinese-simplified/Adobe-GB1.cidToUnicode

unicodeMap      ISO-2022-CN     /usr/local/share/xpdf/chinese-simplified/ISO-2022-CN.unicodeMap

unicodeMap      EUC-CN          /usr/local/share/xpdf/chinese-simplified/EUC-CN.unicodeMap

unicodeMap      GBK             /usr/local/share/xpdf/chinese-simplified/GBK.unicodeMap

cMapDir         Adobe-GB1       /usr/local/share/xpdf/chinese-simplified/CMap

toUnicodeDir                    /usr/local/share/xpdf/chinese-simplified/CMap

#fontFileCC     Adobe-GB1       /usr/..../gkai00mp.ttf

displayCIDFontTT Adobe-GB1      /usr/local/share/xpdf/chinese-simplified/CMap/gkai00mp.ttf



保存后退出



安装swftools，安装之后可以实现pdf文件转成swf

axel -n 30 -o /www/data http://www.swftools.org/swftools-2013-04-09-1007.tar.gz

tar xzf swftools-2013-04-09-1007.tar.gz

cd swftools-2013-04-09-1007

./configure --prefix=/opt/swftools && make && make install



测试是否可以使用

/opt/swftools/bin/pdf2swf -o /www/wwwroot/default/1.swf -T -z -t -f /www/wwwroot/default/1.pdf -s flashversion=9

出现此提示即成功！

NOTICE  processing PDF page 1 (595x842:0:0)

NOTICE  Writing SWF file /www/wwwroot/default/1.swf



java -jar /opt/jodconverter/lib/jodconverter-cli-2.2.2.jar /home/work/work/cuixiang.docx /home/work/work/1.pdf >/dev/null 2> /home/work/work/1.log &

/opt/swftools/bin/pdf2swf -o /home/work/work/1.swf -T -z -t -f /home/work/work/1.pdf -s languagedir=/usr/local/share/xpdf/chinese-simplified -s flashversion=9





mkdir -p /home/chroot/home/utomorro/domains/utomorrow.com/public_html/trunk/files/pdf

mkdir -p /home/chroot/home/utomorro/domains/utomorrow.com/public_html/trunk/files/swf