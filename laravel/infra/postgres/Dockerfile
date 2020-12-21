FROM postgres:11.10

# OSの日本語化
RUN localedef -i ja_JP -c -f UTF-8 -A /usr/share/locale/locale.alias ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV TZ 'Asia/Tokyo'
