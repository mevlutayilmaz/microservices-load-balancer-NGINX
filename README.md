# C# Uygulamasında NGINX İle Load Balancer Yapılandırması

C# uygulamasında bir load balancer çözümü olarak NGINX'i kullanmak için aşağıdaki adımları takip edilebilir:

## NGINX'in Kurulumu

<img align="right" width="230" src="https://github.com/user-attachments/assets/5d63d385-915b-462a-a8da-d0faaf5cea0f">

1. **NGINX Kurulum:** NGINX'in kurulumu için [https://nginx.org/en/download.html](https://nginx.org/en/download.html) adresi üzerinden son versiyonun indirilmesi gerekmektedir. 
2. **NGINX'in Yapılandırılması:** İndirme işlemi bittikten sonra indirilen dosyayı klasöre ayıklayıp `nginx.exe` dosyası üzerinden ayağa kaldırılabilir. Ancak öncelikle sunucu bilgilerine dair konfigürasyonların yapılması gerekecektir. Bunun için `conf` klasörü içerisindeki `nginx.conf` dosyasında yanda bulunan görseldeki gibi bir yapılandırmada bulunulabilir.
3. **NGINX'in Ayağa kaldırılması:** Artık bu yapılandırma yapıldıktan sonra `nginx.exe` dosyası üzerinden NGINX başlatılabilir. 

## C# Uygulamasının Load Balancer İçin Yapılandırılması

<img align="left" width="220" src="https://github.com/user-attachments/assets/e6cd1887-b6e1-407f-b973-fb0261845627">

1. **Profillerin Oluşturulması:** NGINX Load Balancer'ın doğru çalıştığını kontrol etmek için, uygulamanın launchSettings.json dosyasında yeni profiller oluşturulmalıdır. Örnek olarak, üç profil tanımlanabilir. Bu profillerin yapılandırmasını yandaki görselden inceleyebilirsiniz. Daha sonradan bu profiller üzerinden sunucular ayağa kaldırılacaktır.

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

## NGINX İle Algoritmaların Kullanımı

<table>
  <tr>
    <td>
      <h3>Round Robin</h3>
      <img src="https://github.com/user-attachments/assets/d0509976-4696-468a-9d8b-1dcf9957e8cd" alt="Round Robin" width="300">
      <p>En basit algoritmadır.  Gelen istekleri, sırayla sunuculara dağıtır.  Tüm sunucuların eşit kapasitede olduğu durumlarda etkilidir.</p>
    </td>
    <td>
      <h3>Least Connection</h3>
      <img src="https://github.com/user-attachments/assets/52648921-d1fa-45bd-bd84-386f35701362" alt="Least Connection" width="300">
      <p>En az aktif bağlantıya sahip sunucuya istek gönderir.  Sunucuların farklı kapasitelere sahip olduğu durumlarda daha dengeli bir yük dağılımı sağlar.  Performans açısından iyidir.</p>
    </td>
    <td>
      <h3>IP Hash</h3>
      <img src="https://github.com/user-attachments/assets/3bf2db85-7740-449b-841f-187d9e2abfdf" alt="IP Hash" width="300">
      <p>İstemcinin IP adresini kullanarak bir hash değeri üretir ve bu değeri kullanarak sunucuları seçer.  Her istemci için aynı sunucu kullanılır, bu da oturum tutarlılığı için uygundur (session stickiness).</p>
    </td>
  </tr>
  <tr>
    <td>
      <h3>Weighted Round Robin</h3>
      <img src="https://github.com/user-attachments/assets/864cfc9f-fc4b-4273-9f99-74890cb82ae6" alt="Weighted Round Robin" width="300">
      <p>Round Robin'e benzer ancak her sunucuya bir ağırlık değeri atanır.  Daha yüksek ağırlıklı sunucular daha fazla istek alır.  Sunucuların farklı kapasitelerine göre ağırlıklandırma yapılabilir.</p>
    </td>
    <td>
      <h3>Generic Hash</h3>
      <img src="https://github.com/user-attachments/assets/68b49c4a-54da-4d7b-b4b9-dd5b318e2781" alt="Generic Hash" width="300">
      <p>İstemci isteğinin herhangi bir özelliğini (IP, header, vs.) kullanarak hash değeri üretir ve sunucu seçer.  Esneklik sağlar, ancak doğru konfigürasyon önemlidir.</p>
    </td>
    <td>
      <h3>Least Time - header</h3>
      <img src="https://github.com/user-attachments/assets/ffaf6987-7a9c-4302-8c40-745345782d22" alt="Least Time - header" width="300">
      <p>HTTP header bilgilerini kullanarak sunucuları seçer ve en hızlı yanıt süresine sahip olanı tercih eder.  Dinamik olarak değişen yük durumlarına uyum sağlar.</p>
    </td>
  </tr>
  <tr>
    <td>
      <h3>Least Time - last_byte</h3>
      <img src="https://github.com/user-attachments/assets/c245a712-9fec-46ca-bd8e-039d1885f3a6" alt="Least Time - last_byte" width="300">
      <p>Son baytın alındığı süreyi baz alarak en hızlı sunucuyu seçer.  Büyük dosya transferlerinde daha etkili olabilir.</p>
    </td>
    <td>
      <h3>Least Time - last_byte inflight</h3>
      <img src="https://github.com/user-attachments/assets/18d955bb-1ec5-487d-a43f-684126c89414" alt="Least Time - last_byte inflight" width="300">
      <p>`last_byte` ile benzer, ancak aktif transferler de hesaba katılır. Daha hassas bir yük dağılımı sağlar.</p>
    </td>
    <td>
      <h3>Least Time - random</h3>
      <img src="https://github.com/user-attachments/assets/eb5015dd-f159-444f-98ec-321292a00d09" alt="Least Time - random" width="300">
      <p>Sunucu grubu içerisinden rastgele iki(random two) sunucu seçilir. Seçilen iki sunucu arasında, son bayt yanıt süresi en kısa olan sunucu tercih edilir.</p>
    </td>
  </tr>
</table>

## Bağlantı Sayısını Sınırlama

<img align="right" width="300" src="https://github.com/user-attachments/assets/3c2aa527-8ceb-4250-b720-5350375663a3">

Gelen isteklerin yönlendirileceği sunucuların bağlantı sayıları sınırlanabilmektedir. Bu işlem için`max_conns`etiketi kullanılmaktadır.

`max_conns` değeri dolduğu taktirde istekler `queue` değeri kadar kuyruğa alınır. queue, kuyruk isteklerle dolduğu taktirde veya istek neticesinde sunucu seçimi `timeout` değeri kadar zaman aşımına uğradığında client'a bir hata gönderilir.


## İstek Yapan Client'ın IP Değerine Erişmek

Load balancing süreçlerinde türlü ihtiyaçlar doğrultusunda sunucular üzerinde istek yapan client'ların IP değerlerine erişmemiz gerekebilmektedir. Uygulama sunucularının client'ların IP değerlerini öğrenmesi gerektiğinde HTTP Header'da `X-Forwarded-For` key inin doldurulması gerekmektedir.

Bunun için ise load balancer'lar üzerinde ilgili key'e karşılık altta bulunan görseldeki düzenlemenin yapılması gerekmektedir. Böylece istek yapan client'in bilgisi `X-Forwarded-For` keyine karşılık HTTP Header'ında taşınması gerektiği bildirilmektedir.

![image](https://github.com/user-attachments/assets/ec8f1337-3aa0-4067-b6c0-1b7138de0b83)

## Health Check

<img align="right" width="300" src="https://github.com/user-attachments/assets/23aaa1cb-e71f-4632-bdee-d96785d28ffc">

NGINX, load balancing yapılacağı sunuculan sürekli olarak test edebilir ve gelen isteği başarısız yahut sıkıntılı sunuculara yönlendirmeden boşa gitmeyecek şekilde işleyebilmektedir.

Görseli incelersek, `fail_timeout` parametresi ile sunucunun kullanılamaz olarak işaretlenmesi için ne kadar sürelik başarısız girişimin gerçekleşmesi gerektiğini ve aynı zamanda sunucunun uygun değil olarak işaretlendiği süreyi ayarlamaktayız. Bu yapılana Pasif Sağlık Kontrolü denmektedir. Bu ifade gelen isteğin yönlendirilmesi neticesinde sunucudan alınan tepkiye göre kontrollerin yapılmasını ifade etmektedir.

Bu süre varsayılan olarak 10 saniyedir. Görselde 30 saniye olarak yapılandırılmıştır. `max_fails` parametresi ise sunucunun kullanılamıyor olarak işaretlenmesi için gereken süre boyunca gerçekleşmesi gereken başarısız denemelerin sayısını ayarlamamızı sağlamaktadır. Yani bu örnekte, 30 saniye içerisinde 3 kez yanıt alınamazsa ilgili sunucu 30 saniye boyunca kullanılamaz olarak işaretlenecektir. 

<img align="left" width="200" src="https://github.com/user-attachments/assets/3e1defe6-1576-467c-9e3f-9844616be1bc">

Ayrıca NGINX'de her sunucuya özel bir denetim isteği göndererek, önceden bir denetleme yapan ve sunucu durumlarını düzenli kontrol eden Aktif Sağlık Kontrolü de mevcuttur. Bunun için `health_check` etiketinin yandaki gibi kullanılması yeterlidir.




