---
layout: post
title: Сервер не загружается после замены диска
modified: 2015-06-17
tags: [troubleshooting]
---
После замены неисправного диска в Linux он не всегда может быть обнаружен системой (типичное поведение для старых ядер).

В таком случае требуется перезагрузить сервер. Однако, бывает так, что после перезагрузки BIOS не может найти загрузчик – он мог быть установлен в MBR вылетевшего диска.
В таком случае требуется загрузить сервер с LiveCD. Я пользуюсь образами Gentoo. После загрузки в среду восстановления нужно разметить диск аналогично исправному. Если объём небольшой, и rebuild массива пройдёт быстро, то можно добавить диск в массив. После этого необходимо переустановить GRUB. Для этого надо подмонтировать массив и воспользоваться утилитой `grub-install`. Дело в том, что в LiveCD Gentoo нет утилит GRUB.

{% highlight console %}
# mkdir /mnt/tmp
# mount /dev/md126 /mnt/tmp
# mount -o bind /dev /mnt/tmp/dev
# chroot /mnt/tmp
{% endhighlight %}

В chroot-окружении доступны все утилиты с сервера. Пытаемся установить загрузчик:

{% highlight console %}
# grub-install /dev/sda
/dev/md126 does not have any corresponding BIOS drive
{% endhighlight %}

Неудача. Дело в том, что загрузчик грузится с MBR диска, он не может быть загружен с MD-устройства. Впрочем, это не мешает ему распознавать программные RAID-массивы и загружать ядро. Установить GRUB в /dev/sda всё же можно вручную:

{% highlight console %}
# grub
grub> root (hd0,0)        
root (hd0,0)
Filesystem type is ext2fs, partition type 0xfd
grub> setup (hd0)
setup (hd0)
Checking if "/boot/grub/stage1" exists... no
Checking if "/grub/stage1" exists... yes
Checking if "/grub/stage2" exists... yes
Checking if "/grub/e2fs_stage1_5" exists... yes
Running "embed /grub/e2fs_stage1_5 (hd0)"...  16 sectors are embedded.
succeeded
Running "install /grub/stage1 (hd0) (hd0)1+16 p (hd0,0)/grub/stage2 /grub/grub.conf"... succeeded
Done.
grub> quit
# reboot
{% endhighlight %}

Также не забудьте изменить имя MD-устройства, если оно изменилось (например, с md0 на md126 ) в файле `/etc/fstab`. После перезагрузки сервер должен успешно подняться.

Ссылки:
[http://idolinux.blogspot.ru/2009/07/reinstall-grub-bootloader-on-md0.html](http://idolinux.blogspot.ru/2009/07/reinstall-grub-bootloader-on-md0.html)