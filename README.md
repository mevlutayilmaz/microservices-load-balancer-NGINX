# C# Uygulamasında NGINX İle Load Balancer Yapılandırması

C# uygulamanız için bir load balancer çözümü olarak NGINX'i kullanmak için aşağıdaki adımları takip edebilirsiniz:

## NGINX'in Kurulumu

<img align="right" width="230" src="https://github.com/user-attachments/assets/5d63d385-915b-462a-a8da-d0faaf5cea0f">

1. **NGINX Kurulum:** NGINX'in kurulumu için [https://nginx.org/en/download.html](https://nginx.org/en/download.html) adresi üzerinden son versiyonun indirilmesi gerekmektedir. 
2. **NGINX'in Yapılandırılması:** İndirme işlemi bittikten sonra indirilen dosyayı klasöre ayıklayıp `nginx.exe` dosyası üzerinden ayağa kaldırılabilir. Ancak öncelikle sunucu bilgilerine dair konfigürasyonları yapmamız gerekecektir. Bunun için `conf` klasörü içerisindeki `nginx.conf` dosyasında yandaki görseldeki gibi bir yapılandırmada bulunulabilir.
3. **NGINX'in Ayağa kaldırılması:** Artık bu yapılandırma yapıldıktan sonra `nginx.exe` dosyası üzerinden NGINX ayağa kaldırılabilir. 

## C# Uygulamasının Load Balancer İçin Yapılandırılması

<img align="left" width="220" src="https://github.com/user-attachments/assets/e6cd1887-b6e1-407f-b973-fb0261845627">

1. **Profillerin Oluşturulması:** Öncelikle NGINX Load Balancer'ın çalışığ çalışmadığını kontrol edebilmek için uygulamanın `launchSettings.json` dosyası üzerinde yeni profiller oluşturmamız gerekecek. Örnek olarak 3 tane profil oluşturdum sonrasında bu profillerden uygulamalar ayağa kaldırıcaz. Oluşturduğum profilleri soldaki görselden kontrol edebilirisiniz.

2. **Endpoint Oluşturma:** Minimal API'da bir endpoint oluşturdum. Get isteği yapıldığında `args[0]` ile terminalden gelen argüman ekrana yazdırılacak. Bu argümanı terminalden verirken profil isimlerine göre vereceğiz ki Load Balancer hangi profile yönlendirmiş görebilelim.bu
3. **Profillerin Ayağa Kaldırılması:** Powershell üzerinden oluşturduğumuz profilleri `dotnet run profileName --launch-profile arguments` komudu ile ayağa kaldırıyoruz. Burada uygulamanın dizininde olduğumuzdan emin olmamız gerekmektedir.

<div style="display: flex; justify-content: center;">
  <img src="https://github.com/user-attachments/assets/d2424b3c-a6b0-40cf-b9c1-4871fdf84b9a" alt="Image 1" width="300"/>
  <img src="https://github.com/user-attachments/assets/eac0ef12-dc46-4e6a-8524-cb814e1da368" alt="Image 2" width="300"/>
  <img src="https://github.com/user-attachments/assets/15f97cea-0a55-46ab-8c98-4ef5b3483854" alt="Image 3" width="300"/>
</div>

## Load Balancer'ın Test Edilmesi

`http://localhost:8080/` adresi üzerinden Load Balancer'ın testini yapabiliriz.

![gifff](https://github.com/user-attachments/assets/92bb08d0-6713-45dd-a441-b8014ad8b506)

Görüldüğü üzere Load Balancer sorunsuz bir şekilde çalışıyor. Defaul olarak Round Robin algoritmasını kullandığı için gelen istekleri sunucular arası eşit olarak dağıtıyor.

