# Установка R
## подключаем репозитории
sudo yum update
sudo yum -y install chrony
systemctl start chronyd
systemctl enable chronyd
systemctl status chronyd

sudo yum -y install epel-release
uname -a

## ставим R
sudo yum -y install wget epel-release chrony tree
sudo yum -y install R

## ставим доп либы, необходимые для пакетов R
sudo yum -y groupinstall X11
sudo yum -y groupinstall "Development Tools"

sudo yum -y install wget libcurl-devel openssl-devel cyrus-sasl-devel libxml2-devel libpng-devel libjpeg-devel python python-devel proj proj-devel mesa-libGL mesa-libGL-devel mesa-libGLU mesa-libGLU-devel gmp-devel mpfr-devel cairo-devel libXt-devel gtk2-devel v8-devel udunits2 udunits2-devel xorg-x11-server-Xvfb unixODBC* postgresql-devel mariadb-devel mysql-devel gcc-gfortran* texlive*, ufw, dejavu*, psmisc, rrdtool, wireshark, lrzsz

sudo yum -y install dejavu-fonts-common dejavu-sans-mono-fonts rrdtool psmisc lrzsz gdal* proj-devel proj-epsg proj-nad protobuf-devel geos-devel

## ставим все, что касается LaTeX
sudo yum -y install texlive texlive-latex texlive-xetex texlive-collection-fontsrecommended texlive-collection-latex texlive-collection-latexrecommended  texlive-xetex-def texlive-collection-xetex
Добавляем поддержку кириллицы
sudo yum -y install texlive-cyrillic texlive-collection-langcyrillic texlive-cyrillic-doc texlive-framed texlive-titling texlive-*font* linux-liber*

## обновляем пакеты в R
sudo -i R
update.packages(ask=FALSE)

- Как поставить пакеты в R, если есть проблемы с https сертификатами. Выдает ошибку:
```
Warning messages:
1: In download.file(url, destfile = f, quiet = TRUE) :
 URL 'https://cran.r-project.org/CRAN_mirrors.csv': status was 'Peer certificate cannot be authenticated with given CA certificates'
2: package ‘httr’ is not available (for R version 3.4.4) 
```
решил вопрос путем обхода секьюрности флагами: `install.packages("httr", method="wget", extra="--no-check-certificate")`

При установке пакетов под root (для всех) запускаем R от рута `sudo -i R` и прогоняему установку
================== В консоли R
После установки библиотек ищем расположение h файлов следующей командой `find . -type f -name udunits2.h` и запускаем инсталляцию с параметрами:
install.packages("udunits2", configure.args='--with-udunits2-include=/usr/include/udunits2/')

chooseCRANmirror(graphics=FALSE, ind=37)
pacman::p_load("gWidgetstcltk")


# Offfline Инсталляция
## Создаем репозиторий miniCRAN
- Используем пакет miniCRAN для инициализации репозитория. 
Считаем, что локальная директория /opt/miniCRAN уже создана.
```
library("miniCRAN")
tags <- c("tidyverse", "lubridate", "glue", "scales", "forcats", "readxl", "magrittr", "stringi", "stringr", 
          "futile.logger", "jsonlite", "Cairo", "RColorBrewer", "extrafont", "hrbrthemes", "DBI", "RPostgreSQL", 
          "config", "shiny", "shinyjqui", "shinythemes", "shinyBS", "shinyjs", "shinyWidgets", "shinycssloaders", 
          "formattable", "anytime", "tictoc", "digest", "officer", "openxlsx", "assertr", "checkmate")
pkgList <- pkgDep(tags, suggests=TRUE, enhances=FALSE)
makeRepo(pkgList, path="d:/temp/miniCRAN", repos="https://cloud.r-project.org/", type=c("source"))
```

## Установка собственных пакетов с Github
1. Скачали единым zip файлом https://github.com/iMissile/dvtdspack
2. Ставим с локального файла: `devtools::install_local(path="C:/Users/Ilya/Downloads/dvtdspack-master.zip")`. Если просто выкачивать zip с гитхаба, то инсталляция не получится, zip файл содержит файлы пакета внутри папки. 

Команда `install.packages("C:/Users/Ilya/Downloads/dvtdspack-master.zip", repos=NULL)` работает криво и просто гонит содержимое zip в библиотеку, без компиляции и без переименования (остается тэг `-master`)


# Установка продуктов RStudio

## Установка RStudio Server
[Страница загрузки](https://www.rstudio.com/products/rstudio/download-server/)

Не забыть завести отдельного пользователя с uid > 100 !:
`sudo useradd <username>`
`sudo passwd <username>`

## Установка RStudio Shiny Server free
[Страница загрузки](https://www.rstudio.com/products/shiny/download-server/)
Для удобства работы оставим однопользовательский режим работы Shiny Server, делаем мапирование домашних директорий R пользователей на `/srv/shiny-server` командой `ln -s <SOURCE> <LINK_NAME>`:
`sudo ln -s /home/ruser/R/<app> /srv/shiny-server/<app>`

Запуск скрипта из консоли linux: `R < script.R --no-save`. Выводит все на экран, можно поглядеть ошибки

## Установка RStudio Connect
1. Заходим на страницу загрузки https://www.rstudio.com/products/connect/download-commercial/
2. Проверяем срок демо лицензии: 
sudo /opt/rstudio-connect/bin/license-manager status-offline


================== Web Доступ
Shiny Server
- `http://10.0.0.239:3939/` -- общая стартовая страница с приложениями
RStudio Server
- `http://<server-ip>:8787`

#        ====== Инсталляция под UBUNTU ========
apt-get install libssl-dev curl  python-dev rrdtool fonts-dejavu fonts-dejavu-core fonts-dejavu-extra ttf-dejavu ttf-dejavu-core ttf-dejavu-extra texlive-fonts-extra postgresql-server-dev-9.3 libv8-dev unixODBC-dev texlive libxml2-dev libjpeg-dev libpng-dev lrzsz libudunits2-dev mesa-common-dev libglu1-mesa-dev libgl1-mesa-dev