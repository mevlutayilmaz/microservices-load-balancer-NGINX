# C# Uygulamasında NGINX İle Load Balancer Yapılandırması

C# uygulamasında bir load balancer çözümü olarak NGINX'i kullanmak için aşağıdaki adımları takip edilebilir:

## NGINX'in Kurulumu

<img align="right" width="230" src="https://github.com/user-attachments/assets/5d63d385-915b-462a-a8da-d0faaf5cea0f">

1. **NGINX Kurulum:** NGINX'in kurulumu için [https://nginx.org/en/download.html](https://nginx.org/en/download.html) adresi üzerinden son versiyonun indirilmesi gerekmektedir. 
2. **NGINX'in Yapılandırılması:** İndirme işlemi bittikten sonra indirilen dosyayı klasöre ayıklayıp `nginx.exe` dosyası üzerinden ayağa kaldırılabilir. Ancak öncelikle sunucu bilgilerine dair konfigürasyonların yapılması gerekecektir. Bunun için `conf` klasörü içerisindeki `nginx.conf` dosyasında yandaki görseldeki gibi bir yapılandırmada bulunulabilir.
3. **NGINX'in Ayağa kaldırılması:** Artık bu yapılandırma yapıldıktan sonra `nginx.exe` dosyası üzerinden NGINX başlatılabilir. 

## C# Uygulamasının Load Balancer İçin Yapılandırılması

<img align="left" width="220" src="https://github.com/user-attachments/assets/e6cd1887-b6e1-407f-b973-fb0261845627">

1. **Profillerin Oluşturulması:** NGINX Load Balancer'ın doğru çalıştığını kontrol etmek için, uygulamanın launchSettings.json dosyasında yeni profiller oluşturulmalıdır. Örnek olarak, üç profil tanımlanabilir. Bu profillerin yapılandırmasını yandaki görselde inceleyebilirsiniz. Daha sonradan bu profiller üzerinden sunucular ayağa kaldırılacaktır.

2. **Endpoint Oluşturma:** Minimal API'da bir endpoint oluşturulmalıdır. Gelen Get isteği sırasında args[0] ile terminalden alınan argüman ekrana yazdırılacaktır. Bu argüman, profillere göre belirlendiği için Load Balancer'ın hangi profile yönlendirme yaptığı görülebilir.

3. **Profillerin Ayağa Kaldırılması:** Powershell üzerinden, tanımlanan profiller `dotnet run profileName --launch-profile argument` komutu ile çalıştırılabilir. Bu adımda, uygulama dizininde bulunulduğundan emin olunmalıdır.

<div style="display: flex; justify-content: center;">
  <img src="https://github.com/user-attachments/assets/d2424b3c-a6b0-40cf-b9c1-4871fdf84b9a" alt="Image 1" width="300"/>
  <img src="https://github.com/user-attachments/assets/eac0ef12-dc46-4e6a-8524-cb814e1da368" alt="Image 2" width="300"/>
  <img src="https://github.com/user-attachments/assets/15f97cea-0a55-46ab-8c98-4ef5b3483854" alt="Image 3" width="300"/>
</div>

## Load Balancer'ın Test Edilmesi

Load Balancer'ın çalışıp çalışmadığını `http://localhost:8080/` adresi üzerinden test edebilirsiniz.

![gifff](https://github.com/user-attachments/assets/92bb08d0-6713-45dd-a441-b8014ad8b506)

Görüldüğü üzere, Load Balancer başarılı bir şekilde çalışmaktadır. Varsayılan olarak Round Robin algoritması kullanılarak gelen istekler sunucular arasında eşit şekilde dağıtılmaktadır.

