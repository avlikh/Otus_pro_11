### Меняем время показа меню Almalinux:
в файле **/etc/default/grub** изменяем (если параметра нет, то добавляем)
**GRUB_TIMEOUT=5**
Далее выполняем:
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
