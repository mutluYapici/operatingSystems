# operatingSystems
wsl --unregister Ubuntu
wsl --shutdown
wsl --uninstall
dism.exe /online /disable-feature /featurename:Microsoft-Windows-Subsystem-Linux /norestart
dism.exe /online /disable-feature /featurename:VirtualMachinePlatform /norestart

Restart-Computer



wsl --install
Restart-Computer
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential flex bison libssl-dev libelf-dev bc git dwarves


# Çekirdek kaynağını indirip hazırlayın
cd ~
git clone --depth=1 https://github.com/microsoft/WSL2-Linux-Kernel.git
cd WSL2-Linux-Kernel

# Otomatik '+' eki oluşmasını engelleyin (vermagic hatasını çözer)
touch .scmversion

# Mevcut çalışan WSL2 konfigürasyonunu kopyalayın
zcat /proc/config.gz > .config
make olddefconfig
make modules_prepare

# Çekirdek başlıklarını sisteme bağlayın
sudo mkdir -p /lib/modules/$(uname -r)
sudo ln -sf ~/WSL2-Linux-Kernel /lib/modules/$(uname -r)/build






Test Klasörü Oluşturun ve Dosyaları Yazın

cd ~
mkdir -p kernel_projects/simple
cd kernel_projects/simple





2. simple.c Dosyasını Oluşturun
nano simple.c yazıp aşağıdaki hatasız ve tam uyumlu C kodunu yapıştırın (Ctrl+O, Enter, Ctrl+X ile kaydedip çıkın):

#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>

/* Modül yüklendiğinde çalışacak fonksiyon */
static int __init simple_init(void)
{
    printk(KERN_INFO "Loading Kernel Module\n");
    return 0;
}

/* Modül kaldırıldığında çalışacak fonksiyon */
static void __exit simple_exit(void)
{
    printk(KERN_INFO "Removing Kernel Module\n");
}

module_init(simple_init);
module_exit(simple_exit);

MODULE_LICENSE("GPL");
MODULE_DESCRIPTION("Simple Module");
MODULE_AUTHOR("SGG");












3. Makefile Dosyasını Oluşturun
nano Makefile yazıp aşağıdaki satırları yapıştırın (girintilerin TAB tuşu ile yapıldığından emin olun):

obj-m += simple.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean









AŞAMA 5: Derleme ve Çalıştırma
Şimdi modülünüzü derleyin ve test edin:

    Modülü Derleyin:

make

(Sıfır hata ile simple.ko dosyası oluşacaktır.)

    Modülü Çekirdeğe Yükleyin:

sudo insmod simple.ko

    Çıktıyı Kontrol Edin:

sudo dmesg | tail -n 5

(Ekranda Loading Kernel Module mesajını göreceksiniz.)

    Modülü Çıkarın ve Çıkarılma Mesajını Görün:

sudo rmmod simple
sudo dmesg | tail -n 5

