### Repositoryler ###
Repositoryleri yüklemek için aşağıdaki komutu girin

    sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

### Installing etckeeper ###
Projemizi oluşturmadan önce etc dosyasında var olan değişiklikleri görmek adına **_etckeeper_** kuruyoruz.

**Kaynak :** https://www.digitalocean.com/community/tutorials/how-to-manage-etc-with-version-control-using-etckeeper-on-centos-7

Sunucuyu güncelleyip epel reposunu kuruyoruz.

    sudo dnf update

Yüklenmiş olan paketler üzerinde güncel olmayan paketleri güncellemek için yazılımı güncelleyip etckeeper kurulumunu başlatıyoruz.

    sudo dnf update && sudo dnf install etckeeper

etc klasörüne giriş yapın

    cd /etc

etckeeper'ı bu klasörde başlatıyoruz.

    sudo etckeeper init

Çıktı: 

    Initialized empty Git repository in /etc/.git/

Ayrıca, yerel depoyu daha önce başlattığınızdan, bu dosyaları git tarafından yönetilen önbellekten kaldırmanız gerekir.

    etckeeper vcs rm --cached file_to_ignore

Çıktı: 

    fatal: pathspec 'file_to_ignore' did not match any files

İlk komuyu göndermek için aşağıdaki komutu girin.

    sudo etckeeper commit "First commit of my /etc directory"

Bir değişiklik yapıp kontrol edelim . Aşağıdaki komutu girin;

    vi /etc/hosts

Aşağıdaki satırı dosyanın en altına ekleyip kaydedin. 

    192.168.0.2    node01

Kaydettikten sonra commit işlemini gerçekleştiriyoruz.

    sudo etckeeper commit "Added a line to the hosts file"

Son olarak dosyanın izinlerini değiştirmeniz gerekmektedir.

    sudo chmod 640 /etc/hosts

/etc/hosts üzerindeki izin değişikliklerini aşağıdaki komutla görüntüleyebilirsiniz.

    ls -l /etc/hosts

### Swap Kurulumu ###

Ram miktarı eğer ki yetersiz ise bunun için swap kullanabilirsiniz. 
Swap diskin sizin belirleyeceğiniz miktarda bir bölümünü ram olarak kullanır. 
Ram miktarı dolduğunda , aktif olmayan sayfalarınız  Swap'a taşınır. 

**Kaynak :** https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7

Herhangi bir swap işleminin var olup olmadığını kontrol etmek için aşağıdaki komutu terminale girebilirsiniz . Sonuç boş dönüyorsa herhangi bir swap kullanımı yok demektir.

    swapon -s

Swap kullanımının görmenin başka bir yolu da bellek kullanımını gösteren bir diğer komutumuz. Terminale girin ve swap  alanına bakın . Sonuç 0 ise herhangi bir kullanım olmadığı manasına gelir.

    free -m

Çıktı:

                    total        used        free      shared  buff/cache   available
    Mem:            821         137         384          10         299         545
    Swap:             0           0           0


Sürücü kullanımımızın miktarını öğrenebilmek için bu komutu girebilirsiniz.

    df -h

Çıktı: 

    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        396M     0  396M   0% /dev
    tmpfs           411M     0  411M   0% /dev/shm
    tmpfs           411M   11M  401M   3% /run
    tmpfs           411M     0  411M   0% /sys/fs/cgroup
    /dev/vda1        25G  1.4G   24G   6% /
    tmpfs            83M     0   83M   0% /run/user/0

Swap dosyamızı oluşturmak ve ne kadarlık bir disk kullanımını belirtmek için aşadağıdaki komutu girin

    sudo dd if=/dev/zero of=/swapfile count=1024 bs=1MiB

Çıktı: 

    1024+0 records in
    1024+0 records out
    1073741824 bytes (1.1 GB, 1.0 GiB) copied, 1.52208 s, 705 MB/s
    
Burada count alanında ne kadar kullanacığımızı belirtebiliyoruz. 
    
Ne kadar alanın ayrıldığını ve izinleri görüntülemek için aşağıdaki komutu girin.

    ls -lh /swapfile

Çıktı: 

    -rw-r--r--. 1 root root 1.0G May 27 13:11 /swapfile

Başka kullanıcıların bu dosyayı okuması ve yazması güvenlik riski oluşturacağından dolayı aşağıdaki komut ile bu izinleri kaldırıyoruz.

    sudo chmod 600 /swapfile

İzinleri tekrardan görüntüleyelim

    ls -lh /swapfile

Çıktı: 

    -rw-------. 1 root root 1.0G May 27 13:11 /swapfile

Swap alanına kullanım için artık artık yazmayı gerçekleştirebiliriz.

    sudo mkswap /swapfile

Çıktı: 

    Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
    no label, UUID=627c22e9-a59a-4ba2-bf23-3b28ec035e4c

Aşağıdaki komutla swap alanımızı kullanmaya başlayabiliriz.
    
    sudo swapon /swapfile

Şimdi tekrardan swap alanımızı kontrol edebiliriz

    swapon -s

Çıktı: 

    Filename                                Type            Size    Used    Priority
    /swapfile                               file            1048572 0       -2

Bununla bir dosyamızın var olduğunu görüntüleyebiliriz.

    free -m

Çıktı: 

                total        used        free      shared  buff/cache   available
    Mem:            821         138          98          15         583         540
    Swap:          1023           0        1023
    
Bu dosyayı düzenleyebilmek için komutu girin.

    sudo vi /etc/fstab

Server yeniden başlatıldığında swap dosyasını otomatik olarak oluşturmasını söylememiz gerekmektedir bunun için aşağıdaki satırı açmış olduğumuz dosyanın en alt satırına ekleyip kaydediyoruz.

    /swapfile   swap    swap    sw  0   0

### PHP Kurulumu ### 

Mevcut php sürümlerini görüntülemek için:

    dnf module list php

Çıktı: 

    CentOS-8 - AppStream                             12 kB/s | 4.3 kB     00:00
    CentOS-8 - Base                                  12 kB/s | 3.9 kB     00:00
    CentOS-8 - Extras                               4.6 kB/s | 1.5 kB     00:00
    CentOS-8 - AppStream
    Name      Stream       Profiles                       Summary
    php       7.2 [d]      common [d], devel, minimal     PHP scripting language
    php       7.3          common, devel, minimal         PHP scripting language

    Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled

Default olarak bir php sürümü aktif ise aşağıdaki komut ile pasif edilebilir.; 

    sudo dnf module reset php

Mevcut bir sürümü seçmek için: 

    sudo dnf module enable php:remi-7.3

PHP ve gerekli bazı kütüphaneleri kuruyoruz.

    sudo dnf install php php-opcache php-gd php-curl php-mysqlnd php-fpm

Projemiz için gerekli olan bazı kütüphaneleri kuruyoruz.

    sudo dnf install php-oauth php-imap php-cli wget unzip php-zip

FPM'in otomatik olarak başlatılması için; 

    sudo systemctl enable --now php-fpm

Çıktı: 

    Created symlink /etc/systemd/system/multi-user.target.wants/php-fpm.service → /usr/lib/systemd/system/php-fpm.service.

### Nginx Kurulumu : ###

    sudo dnf install nginx

### Git kurulumu : ###

Git kurulumunu aşağıdaki komutla gerçekleştirebilirsiniz.

    sudo dnf install git

### Composer kurulumu : ###
Yazılımı güncelliyoruz

    sudo dnf -y update

Ana klasöre girip aşağıdaki komutu giriyoruz.

    cd

    sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

Ardından, yükleyicinin, Besteci Ortak Anahtarları / İmzaları sayfasında bulunan en son yükleyici için SHA-384 karmasıyla eşleştiğini doğrulayın.

Ardından

    HASH="$(wget -q -O - https://composer.github.io/installer.sig)"

Yüklemenin gerçekleşip gerçekleşmediğini doğrulamak için aşağıdaki komutu girin

    php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer

Eğer yukarıdaki komutta "Installer verified" çıktısını alırsanız yüklemeniz başarıyla tamamlanmıştır.

### MySQL kurulumu : ###

**Kaynak :** https://www.tecmint.com/install-mysql-on-centos-8/

MySQL'in son versiyonunu kurmak için aşağıdaki komutu girin.

    sudo dnf -y install @mysql

@mysql, mysql'in son sürümünü ve ona ait bağımlılıkları yükler

MySQL servisini başlatıyoruz.

    sudo systemctl start mysqld

Sistem açıldığında otomatik olarak açılması için aşağıdaki komutu girin.

    sudo systemctl enable mysqld

MySQL durumuna buradan bakabiliriz.

    sudo systemctl status mysqld

**_Mysql Şifresi Belirlemek için :_**

MySQL şifresini değiştirmek için aşağıdaki iki komutu sırasıyla girin 

Yeni şifre girebilmeniz için şifrenin zorluk durumunu belirleminizi isteyecektir, ben 1 olarak seçip şifreyi değiştirdim. 
Ardından karşınıza çıkacak diğer tüm alanlar için "y" deyip geçebilirsiniz . 

    mysql_secure_installation
    mysql -u root -p

Burada şifremizi girdikten bir tane kullanıcı oluşturuyoruz.

    CREATE USER 'burhan'@'localhost' IDENTIFIED BY '1234';

Kullanıcı yetki erişmesi için gereken yetkiyi veriyoruz. 

    GRANT ALL PRIVILEGES ON *.* TO 'burhan'@'localhost';

Burada kullanıcının tüm veritabanlarına erişme yetkisini vermiş oluyoruz.

Yeni veritabanımızı oluşturuyoruz.

    create database database_name;

İşimiz bitti. Artık çıkış yapabiliriz.

    exit

Yazılımı şimdi tekrardan güncelleyebiliriz.

    sudo dnf update

Projemizi gitten almak için aşağıdaki komutları takip edin.

/var/www klasörüne giriyoruz.

    cd /var/www

Bitbucket'tan projeyi clone linkini kopyalayıp consola girin

    git clone your_laravel_project_repository.git

Proje klasörüne girin

    cd your_project

Paketleri yüklemek için aşağıdaki komutu girin

    sudo composer install

env dosyasını oluşturabilmek için .env.example dosyasını kopyalayabilirsiniz. 

    cp .env.example .env

.env dosyamızı güncelliyoruz.
    
    vi .env

.env dosyanızı aşağıdaki bilgilerle güncelleyin.

    DB_HOST=localhost
    DB_PORT=3306
    DB_DATABASE=
    DB_USERNAME=
    DB_PASSWORD=
    
    QUEUE_CONNECTION=database

****
**Aşağıdaki komutları giriyoruz**

Veritabanızdaki proje tablolarımızı oluşturabilmek için laravel migrate komutunu çalıştırıyoruz.

    php artisan migrate

laravelin storage klasörüne erişebilmesi için aşağıdaki komutları izinleri değiştirmek için giriyoruz.

    sudo chmod -R gu+w storage/

    sudo chmod -R guo+w storage/
    
    sudo chmod -R gu+w bootstrap/cache/
    
    sudo chmod -R guo+w bootstrap/cache/

.env dosyasındaki key değerini güncellememiz gerekmektedir . php artisan key:generate komutu rastgele bir anahtar oluşturmak için kullanılır. Bu komut, uygulamanın ortam dosyasında depolanan anahtarı güncelleştirir.

    php artisan key:generate

SELinux yetkilendirme: 

    semanage permissive -a httpd_t

    sudo semanage port -m -t http_port_t -p tcp 8080
    
    sudo setsebool -P httpd_can_network_connect_db 1

Yapılandırmaları temizleyip tekrar oluşturmak için

    php artisan config:cache

erişim verildikten sonra cache temizleme ihtiyacı duyulabilir.

Laravelde nginx yapılandırmasını gerçekleştirmek için aşağıdaki komutları takip edin.

Aşağıdaki komutu girip dosyanın içindeki user ve group alanlarını nginx olarak değiştiriyoruz. 

    vi /etc/php-fpm.d/www.conf

projemizi nginx ile oluşturmak için /etc/nginx/conf.d klasöründe bir config dosyasımızı oluşturmamız gerekiyor

    cd /etc/nginx/conf.d

    vi your_conf.conf

        server {
    
            listen 8080 default_server;
            
            server_name _;
            root /var/www/your_project/public/;
            
            add_header X-Frame-Options "SAMEORIGIN";
            add_header X-XSS-Protection "1; mode=block";
            add_header X-Content-Type-Options "nosniff";
            
            index index.html index.htm index.php;
            
            charset utf-8;
        
            location / {
                try_files $uri $uri/ /index.php?$query_string;
            }
            
            location = /favicon.ico { access_log off; log_not_found off; }
            location = /robots.txt  { access_log off; log_not_found off; }
            
            error_page 404 /index.php;
            
            location ~ \.php$ {
                fastcgi_pass unix:/var/run/php-fpm/www.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
                include fastcgi_params;
            }
            
            location ~ /\.(?!well-known).* {
                deny all;
            }
        }


proje klasörüne girelim

    cd /var/www/your_project 

Nginx'e yetki vermek için ( nginx'i tekrardan başlatmamız gerekiyor.  ) : 

    sudo chown nginx:nginx * -R

geçici olarak  selinux'u kapatıyoruz

    sudo setenforce 0

Nginx'i tekrardan başlatıyoruz.

    sudo service nginx restart

Nginx'in otomatik olarak açılması için aşağıdaki komutu kullanabilirsiniz.

    sudo systemctl enable nginx

proje klasörüne giriyoruz.

    cd /var/www/your_project

Localde çalışan ama server'da çalışmayan seed class dosyalarımızı çalıştırabilmek için aşağıdaki komutu girin

    composer dump

yapılandırmaları yeniliyoruz.

    php artisan config:cache

seeder dosyalarını çalıştırıyoruz

    php artisan db:seed

## CRON EKLEME ##

crontab eklemeyebilmek için aşağıdaki komutu girin

    crontab -e

Aşağıdaki satırı açılan dosyaya ekleyin ve kaydedin.

    * * * * * php /var/www/your_project/artisan receipt:run

## Supervisor kurulumu : ##
Yazılımı güncelliyoruz

    sudo dnf update
    
supervisor indirmek için aşağıdaki komutu girin

    sudo dnf install supervisor

supervisoru başlatıyoruz.

    sudo service supervisord start

laraveldeki kuyruğu çalıştırmak için kendi config dosyamızı oluşturmamız gerekiyor.

    cd /etc/supervisord.d

    vi laravel-worker.ini 

Açılan dosyaya aşağıdakilerini kopyalayıp kaydedin.

    [program:laravel-worker]
    process_name=%(program_name)s_%(process_num)02d
    command=php /var/www/your_project/artisan queue:work database --sleep=3
    autostart=true
    autorestart=true
    ;user=forge
    numprocs=1
    redirect_stderr=true
    stdout_logfile=/var/www/your_project/public/worker.log
    stopwaitsecs=3600 

nginx ile ilgili laravel loglarına bakmak için aşağıdaki komutu girin

    cat /var/www/your_project/public/worker.log

Eklediğimiz supervisor config dosyamızı aktifleştirmek için aşağıdaki komutları girelim . Aşağıdaki komutları yaptığımız değişiklikleri okuyup güncelle ve çalışmasını istediğimiz komutu çalıştıracaktır.

    sudo supervisorctl reread
    sudo supervisorctl update
    sudo supervisorctl start laravel-worker:*

Eğer böyle bir hata alıyorsanız :
    <class 'FileNotFoundError'>, [Errno 2] No such file or directory: file: /usr/lib/python3.6/site-packages/supervisor/xmlrpc.py line: 560

**Çözüm :** 

    sudo supervisord -c /etc/supervisord.conf

bu durumda supervisord.d klasörünüzdeki laravel-worker.ini dosyanız silinmiştir.
Bu durumda tekrardan bir tane oluşturup supervisor tekrar çalıştırmayı deneyebilirsiniz.