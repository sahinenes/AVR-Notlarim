# AVR Notlarım

## İsterseniz alt klöserdeki .md uzantılı dosyayı kullanabilirsiniz

# IO-PORTS
- [Datasheet] - Atmega328p
## Giriş - Çıkış ?

//

![1](https://i.ibb.co/7GSWgpw/2021-26-2-14-29-36.png)

• DDRx yazmacındaki porta ait ayak sıfır yapılır ve port yazmacındaki aynı bit de sıfır
yapılırsa giriş ve çıkış fonksiyonu giriş olur ve tri-state olarak kalır. (Ayak boşta iken bu
durum geçerlidir.)
• DDRx yazmacındaki porta ait ayak sıfır yapılır ve port yazmacındaki aynı bit de bir yapılırsa
ve PUD biti sıfır konumunda yani pull-up dirençleri devre dışı bırakılmamışsa o ayak dahili
pull-up özelliği gösterir.
• DDRx yazmacındaki porta ait ayak sıfır yapılır ve port yazmacındaki aynı ayağa karşılık
gelen bit de bir yapılır ama MCUCR yazmacındaki PUD biti 1 yapılırsa o ayak tri-state
konumunda olur. Yani pull-up olmadan giriş konumunda olur.
• DDRx yazmacındaki porta ait ayak bir yapılır ve port yazmacındaki aynı ayağa karşılık
gelen bit de sıfır yapılırsa o ayaktan dijital 0 (sıfır) çıkışı sağlanır.
• DDRx yazmacındaki porta ait ayak bir yapılır ve port yazmacındaki aynı ayağa karşılık
gelen bit de bir yapılırsa o ayaktan dijital 1 (bir) çıkışı sağlanır.

**üste bulunan yazı [lojikprob] - kitabının 33. sayfasından alınmıştır.

![2](https://microcontrollerslab.com/wp-content/uploads/2019/12/ATMEGA328P-Pin-Configuration-Diagram.png)

#### [pinout] resim link

## İşlem Basamakları
- DDRx ile hangi bitin giriş veya çıkış olarak tanımlamamıza olanak sağlar.
- Eğer bite "1" yazılırsa çıkış "0" yazılırsa giriş olarak tanımlanmış olur.
- Herhangi bir biti çıkış olarak tanımlayacağımızı varsayalım yani DDRx in herhangi bir bitine "1" yazıcaz. Bu işlemi yaptıktan sonra PORTx ile bu biti "low" (çıkış yok ,portta akım yok) veya "high" (çıkış var , portta akım var) yapılabilir.
- Eğer herhangi bir portun herhangi bir portunu giriş yapmak istersek DDRx in çıkış yapmak istediğimiz bitine "0" yazıcaz. Bu işlemdem sonra PORTx VE PUD bize 3 seçenek sunuyor. Öncelikle PUD MCUCR – MCU Control Register ında bulunan ve dahili pull-up direnci aktif veya pasif yapamamıza olanak sağlayan bir register(kayıtçı) dır.

- 1. Eğer DDRx yazmacındaki porta ait ayak sıfır yapılır ve port yazmacındaki aynı bit de sıfır yapılırsa giriş ve çıkış fonksiyonu giriş olur ve tri-state olarak kalır. 
- 2. İkinci durumda PORTx in herhangi bir bitine "1" yazılır ise ve PUD bitine "0" yazılır ise dahili pull-up özelliği gösterir.
- 3. Üçüncü durumda PUD bitine "1" ve PORTx in herhangi bir bitine de "1" yazılır ise dahili pull-up pasif olur.

## Peki ya nedir bu Pull-Up Pull-Down ?

//

![2](https://circuitdigest.com/sites/default/files/projectimage_tut/Pull-up-and-Pull-down-Resistor.png)

//
- Pull-Up ve Pull-Down dirençleri genelde butonlar için kararlı bir durum oluşturmak adına devreye eklenir. Mesala biz herhangi bir portu giriş olarak ayarladık ve bu porta bir buton bağlamak istiyoruz.Üsteki çizimde görüldüğü gibi Pull-Up direncini devremize eklediğimizi varsayalım. Şimdi butona basmadığımız sürece butonu bağladığımız portta +5 V gerilim vardır. Butona bastığımızda ise bu devreye  0 V gitmektedir. Bu durumu biz programsal olarak kontrol ederek herhangi bir işelemi mikrodenetleyicimize yaptırabiliriz.Bu dirençleri kullanmamızdaki amaç  devre için kararlı bir yapı oluşturmaktır.

### Örnekler 
![](https://i.ibb.co/FwCDmCm/UNOV3PDF.png)
## Örnek 1 (Sürekli Yanan Led)
- Bu örnekde Arduino Uno' yu Atmel Stduio yardımı ile register seviyesinde programlayacağız.
- Arduino nun PD2 bacağına bir led bağlaalım ve bu ledin sürekli yanamsı için gerekli kodları yazalım.
- Öncelikle ledimizin "D" portunda olduğunu biliyouz bu yüzden DDRD kayıtçısın 3. bitine(PD2) "1" yazmamız gerekiyor.

```sh
#include <avr/io.h>
int main(void)
{
	DDRD |=(1<<2);  //PORT ÇIKIŞ OLARAK AYARLANDI
    while (1) 
    {
    }
}
```
- Daha sonra bu porta PORTD yi kullanrak çıkış vermemiz (high) gerekiyor. 
```sh
#include <avr/io.h>
int main(void)
{
	DDRD |=(1<<2);  //PORT ÇIKIŞ OLARAK AYARLANDI
	PORTD |=(1<<2); //PORTA ÇIKIŞ VERİLDİ
    while (1) 
    {
    }
}
```
- veya
```sh
#include <avr/io.h>
int main(void)
{
	DDRD |=(1<<PORTD2);  //PORT ÇIKIŞ OLARAK AYARLANDI
	PORTD |=(1<<PORTD2); //PORTA ÇIKIŞ VERİLDİ
    while (1) 
    {
    }
}
```
## Örnek 2 (Yanıp Sönen Led)
- PORTB ni PB1 bacağına bağlı olan ledi 500 ms gecikme ile yakıp söndürme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
int main(void)
{
	DDRB |=(1<<1);  //PORT ÇIKIŞ OLARAK AYARLANDI
    while (1) 
    {
        PORTB|=(1<<1);
		_delay_ms(500);
		PORTB&=~(1<<1);
		_delay_ms(500);
    }
}
```
## Örnek 3 (Bit Seviyesinde) (Kara Şimşek)
- D portunun ilk 4 pinine bağlı ledler ile kara şimşek uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
int main(void)
{
	 DDRD|=(1<<PORTD0)|(1<<PORTD1)|(1<<PORTD2)|(1<<PORTD3);
    while (1) 
    {
       	for(uint16_t i=0x01;i<=0x08;i=i*2)
		{
			PORTD=i;
			_delay_ms(100);
		}
		for(uint16_t i=0x08;i>=0x01;i=i/2)
		{    PORTD=i;
			_delay_ms(100);
		}
    }
}
```
## Örnek 4 (Buton ile Led Kontrolü) (Dahili Pull-Up)
- PD2 de bulunan ledi PB2 de bulunan buton ile kontrol etme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
int main(void)
{
	  DDRD|=(1<<PORTD2);//ÇIKIŞ
	  DDRB&=~(1<<PORTB2);//GİRİŞ
	  PORTB|=(1<<PORTB2);//DAHİLİ PULL UP
	  MCUCR&=~(1<<PUD);
    while (1) 
    {
		if (!(PINB & (1<<PINB2)))
		{
			PORTD|=(1<<PORTD2);
		} 
		else
		{
			PORTD&=~(1<<PORTD2);
		}
    }
}
```
## Örnek 5 (Buton ile Led Kontrolü) (Pull-Down)
- PD2 de bulunan ledi PB2 de bulunan buton ile kontrol etme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
int main(void)
{
	  DDRD|=(1<<PORTD2);//ÇIKIŞ
	  DDRB&=~(1<<PORTB2);//GİRİŞ
      PORTB|=(1<<PORTB2);//DAHİLİ PULL UP
	  MCUCR|=(1<<PUD);
    while (1) 
    {
		if ((PINB & (1<<PINB2)))
		{
			PORTD|=(1<<PORTD2);
		} 
		else
		{
			PORTD&=~(1<<PORTD2);
		}
    }
}
```
/
#### veya
/
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
int main(void)
{
	  DDRD|=(1<<PORTD2);//ÇIKIŞ
	  DDRB&=~(1<<PORTB2);//GİRİŞ
    while (1) 
    {
		if ((PINB & (1<<PINB2)))
		{
			PORTD|=(1<<PORTD2);
		} 
		else
		{
			PORTD&=~(1<<PORTD2);
		}
    }
}
```
###### IO-PORTS da bahsedeceklerim bu kadar sizde datasheet veya diğer websitelerinde bulunan örnek ve dökümanları inceleyerek kendi uygulamalarınızı yapabilirsiniz.
### Özellikle  [lojikprob]  ve [lojikprob2] ücretsiz kitapalarını inceleyebilirsiniz.
[lojikprob2]: <http://www.lojikprob.com/diger/kitap/avr-ile-mikrodenetleyici-uygulamalari-gokhan-dokmetas-ucretsiz-e-kitap/>
[pinout]: <https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf>
[lojikprob]: <http://www.lojikprob.com/c-ile-avr-programlama-e-kitap-indir/>
[Datasheet]: <https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf>
[1]: <https://i.ibb.co/cwJhMX6/2021-26-2-14-18-47.png>
[2]: <https://i.ibb.co/bJ3tK1b/2021-26-2-14-22-30.png>
[3]: <https://i.ibb.co/18pJKSz/2021-26-2-14-22-38.png>
[4]: <https://i.ibb.co/7GSWgpw/2021-26-2-14-29-36.png>



# External Interrupt (Dış Kesmeler)
- [Datasheet] - Atmega328p

Mikrodenetleyici de kesmeler işlemciyi  dışarıdan veya içeriden bir müdahale ile durdurup özel bir fonksiyonu yürüten yapılardır.

Atmega328P mikrodenetleyicisinde 2 adet dış kesme ve 23 adet ayak değişim kesmesi bulunuyor.Şimdi sıra ile bu kesmeleri kontrol eden kayıtçıları inceleyelim.

![](https://i.ibb.co/tYC84y6/2021-28-2-14-01-28.png)
![](https://i.ibb.co/z64KPWf/2021-28-2-14-01-35.png)
![](https://i.ibb.co/vmK4qBc/2021-28-2-14-01-41.png)

Eğer EICRA Kayıtçısının;
- ISC00 ve ISC01 bitleri "0" yazılır ise LOW level de kesme işlemi yapılır.
- ISC00 -> "1" ISC01 -> "0" yazılır ise herhangi bir değişiklikte kesme işlemi yapılır.
- ISC00 -> "0" ISC01 -> "1" yazılır ise düşen kenar kesmesi işlemi yapılır.
- ISC00 -> "1" ISC01 -> "1" yazılır ise yükselen kenarda kesme işlemi yapılır.


![](https://i.ibb.co/34snSW3/2021-28-2-14-01-48.png)

Eğer EIMSK Kayıtçısının;
- INT0 -> "1" yazılır ise INT0 kesmesi etkinleştirilir 
- INT1 -> "1" yazılır ise INT1 kesmesi etkinleştirilir 

![](https://i.ibb.co/FwCDmCm/UNOV3PDF.png)

PINOUT dan görüldüğü gibi PD2 INT0 ve PD3 INT1 kesme pinleri olarak gözüküyor.Şimdi PD2 Pinine ve PD3 Pinine buton bağlayarak harici kesme uygulaması gerçekleştirelim.

### ÖRNEKLER
### Örnek 1 (Karaşimşek Hızının Ayarlanması) (INT0 , INT1)
- Bu örnekte karaşimşek hızını PD2 deki buton ile arttırıcaz veya PD3 deki butona basdığımızda hızı azaltıcaz.
- Aynı zamanda B potunu ilk 4 bacağına ledler bağlıdır.
```sh
#define  F_CPU 16000000
#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>
uint32_t bekle;
void my_delay_ms(uint32_t n);
int main(void)
{
	bekle=100;//led geçiş hızı
	
	DDRD = 1<<PORTD2; // ,INT0 kesmesi için kullanılan PD2 ayağı giriş
	PORTD = 1<<PORTD2; //DAHİLİ PULL UP
	
	DDRD = 1<<PORTD3; // ,INT1 kesmesi için kullanılan PD2 ayağı giriş
	PORTD = 1<<PORTD3; //DAHİLİ PULL UP
	
	DDRB|=(1<<PORTB0)|(1<<PORTB1)|(1<<PORTB2)|(1<<PORTB3);//LEDLER ÇIKIŞ
	
	EICRA|=(1<<ISC01)|(1<<ISC11)|(1<<ISC10); //INT1 Yükselen kenarda INT0 düşen kenerda kesme işlemi yapar
	EIMSK|=(1<<INT0)|(1<<INT1);//Kesmeler etkin
	
	sei();//kesmeler etkin interrupt.h kütüphanesini kullanmak için
	
    while (1) 
    {
		for(uint16_t i=0x01;i<=0x08;i=i*2)
		{
			PORTB=i;
			my_delay_ms(bekle);
		}
		for(uint16_t i=0x08;i>=0x01;i=i/2)
		{    PORTB=i;
			my_delay_ms(bekle);
			
		}
    }
}
ISR (INT0_vect)
{
	bekle=bekle-50;
}
ISR (INT1_vect)
{
	bekle=bekle+50;
}
void my_delay_ms(uint32_t n) {
	while(n--) {
		_delay_ms(1);
	}
}
```
Şimdi Kayıtçılarımıza devam edelim;
![](https://i.ibb.co/3yW2nhH/2021-28-2-15-38-00.png)
PCICR Kayıtçısını İnceleyelim;
- PCIE0 biti PCINT7..0 aralığındaki pinlerin ayak değişim kesmelerini etkinleştirir.
- PCIE1 biti PCINT14..8 aralığındaki pinlerin ayak değişim kesmelerini etkinleştirir.
- PCIE2 biti PCINT23..16 aralığındaki pinlerin ayak değişim kesmelerini etkinleştirir.
![](https://i.ibb.co/VQjSVC4/2021-28-2-15-38-13.png)
![](https://i.ibb.co/hFDL9xM/2021-28-2-15-38-18.png)
![](https://i.ibb.co/SR38k6F/2021-28-2-15-38-24.png)
Üste bulunan kayıtçılar ile pinlerin kesmesini bit bit etkinleştirebilirsiniz.
Şimdi PCINT13 ve PCINT21 ile Kara Şimşek ledlerinin hız ayarlarını yapalım.
### ÖRNEKLER
### Örnek 1 (Karaşimşek Hızının Ayarlanması) (PCINT23..0)
- PCINT23 pininde bulunan buton ile hızı arttırıcaz, PCINT21 deki buton ile hızı azaltıcaz.
- Yine B portunun ilk 4 bacağında ledler bulunmaktadır.
```sh
#define  F_CPU 16000000
#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>
uint32_t bekle;
void my_delay_ms(uint32_t n);
int main(void)
{
	bekle=100;
	sei();
	
	DDRB|=(1<<PORTB0)|(1<<PORTB1)|(1<<PORTB2)|(1<<PORTB3);//LEDLER ÇIKIŞ
	DDRD&=~(1<<PORTD5);
	PORTD|=(1<<PORTD5);
	DDRD&=~(1<<PORTD7);
	PORTD|=(1<<PORTD7);
	
	PCICR|=(1<<PCIE2);//23 ile 21 inci PCINT pinleri kullnıcaz ona uygun durum değişiklik kesmesini etkinleştirdik
	PCMSK2|= (1 << PCINT21)|(1<<PCINT23);
    while (1) 
    {
			for(uint16_t i=0x01;i<=0x08;i=i*2)
			{
				PORTB=i;
				my_delay_ms(bekle);
			}
			for(uint16_t i=0x08;i>=0x01;i=i/2)
			{    PORTB=i;
				my_delay_ms(bekle);
			}
}
ISR (PCINT2_vect)
{
	if(PIND &  (1 << PIND5))
	{
		bekle=bekle+50;
	}
	if(PIND & (1 << PIND7))
	{
		bekle=bekle-50;
	}
}
void my_delay_ms(uint32_t n) {
	while(n--) {
		_delay_ms(1);
	}
}
```
[Datasheet]: <https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf>





#  Analog Digital Converter

Bu yazımızda analog değeri dijital bir değere dönüştürme uygulamaları yapıcaz. Ama önceliklele analog ve dijital sinyal nedir bunları inceleyelim.

Fiziksel değişimlerle ilgili bilgiyi taşıyan ve bu bilgiyi matematiksel bir fonksiyon olarak ifade eden kavrama sinyal denir. 

![](https://i.ibb.co/gyBZWy6/2021-01-3-14-29-41.png)

### Analog Sinyal
Analog sinyal, sürekli (continuous) sinyallerdir. Yani zamana bağlı olarak sürekli bir değer alırlar. Analog sinyaller gürültüye dayanıklı değillerdir. Ayrıca analog sinyaller sürekli değiştiklerinden belli aralıklardaki tüm değerleri alabilirler. Doğadaki sinyallerin çoğu sürekli olarak bir değer aldıkları için analog sinyal olarak tanımlanırlar.
![](https://i.ibb.co/VSV6bxy/2021-01-3-14-31-10.png)

### Dijital Sinyal
Dijital sinyal, kesik zamanlı (discrete time) sinyaldir. Yani sürekli olarak belli bir değer almazlar. Analog sinyalden zamana bağlı olarak örnekler(sample) alınır ve dijital sinyal bu örneklerle oluşturulabilirler. Dijital sinyal veriyi binary formda taşır. Günlük hayatta kullandığımız elektronik cihazlardaki işlemlerin çoğu dijital sinyaller ile yapılmaktadır.
![](https://i.ibb.co/nQXWM3y/2021-01-3-14-32-14.png)

### Üsteki bulunan yazının ve görsellerin tamamı [Berkan Aydın] ' ın Analog Sinyal ve Dijital Sinyal yazısından alınmıştır.
### Kaynakçalar;
- [Yazı Kaynak 1]
- [Yazı Kaynak 2]
- [Yazı Kaynak 3]

### ADC işlemini adım adım anlatmak gerekirse şu şekilde olmalıdır;
• Seçili ADC kanal ayağını giriş olarak tanımlamalıdır.
• AVR’nin ADC modülü faal hale getirilmelidir. Çünkü açılış resetinde güç tüketimini önlemek
için devre dışıdır.
• Çevirim hızı seçilir. ADPS2:0 bitleri bu işlemi yapar.
• ADC giriş kanallarındaki gerilim referansı seçilir. ADMUX yazmacındaki REFS0 ve REFS1
bitleri referans seçmek için görevlidir. ADMUX yazmacındaki MUX4:0 bitleri ise ADC giriş
kanalını seçmeye yarar.
• ADCSRA yazmacındaki ADSC biti HIGH yapılarak çevirim başlatılır.
• ADCSRA yazmacındaki ADIF yani ADC kesme bayrağı biti kontrol edilerek çevirimin
bitmesi beklenir.
• ADIF biti HIGH konuma geçtikten sonra ADCL ve ADCH yazmaçlarındaki dijital veri çıkışı
okunur. Öncelikle ADCL yazmacını sonrası ADCH yazmacını okumak gereklidir.
İşte AVR mikrodenetleyicilerde ADC okuma işlemleri bu şekilde olmaktadır.

### Üste bulunan yazının tamamı Gökhan Dökmetaş ın C ile AVR Programalama kitabından alınmıştır.
### Kitap Linkleri;
-[C ile AVR Programlama]
-[AVR ile Mikrodenetleyici Uygulamaları]
### Özellikle İncele
-[lojikprob.com]

Şimdi ADC kayıtçılarını inceleyelim.

![](https://i.ibb.co/09NdGs0/2021-01-3-14-55-01.png)

![](https://i.ibb.co/k4h5wzt/2021-01-3-14-55-22.png)

![](https://i.ibb.co/W23FYcn/2021-01-3-14-55-09.png)

##### ADMUX Kayıtçısının;

- MUX3..0 bitleri hangi bacaktan analog okuma yapacağımzı seçmemizi sağlar
- REFS1..0 bitleri ise gerilim referansını seçmemizi sağlar

![](https://i.ibb.co/WGCn8z9/2021-01-3-14-55-31.png)

![](https://i.ibb.co/pxzQ5Rb/2021-01-3-14-55-43.png)

##### ADCSRA Kayıtçısının;
- ADPS2..0 bitleri sistem saat frekansı ile ADC’nin giriş saati arasındaki bölme faktörünü belirlemeye yarar.
- ADIE  biti "1" yapılırsa ve SREG kesmesindeki I biti de "1" konumdaysa ADC Çevirim Bitme Kesmesi faal hale gelir.
- ADIF  ADC Kesme Bayrağı
- ADSC bitini her bir çevirimi başlatırken bu biti "1" yaparız. Serbest
çalışma modunda ise ilk çevirimi yapmak için bu biti "1" yapmamız gerekir. Çevirim
yapmadan önce ADEN bitini "1" konumuna getirmek sonra ADSC bitini "1" konuma
getirmek gerekir. Bunun ardından Analog-Dijital Çevirimi yaparız. ADSC biti bu çevirim boyunca "1" konumunda kalır ve çevirim bittikten sonra tekrar "0" konumuna geçer. 
- ADEN bu biti HIGH yapmak ADC’yi faal hale getirir. Sıfır yapınca ADC kapatılır

### Örnekler
### Örnek 1 (ADC Hazırlama ve Okuma)
```sh
#include <avr/io.h>
uint16_t read_adc(uint8_t channel);
int main(void)
{
	
	ADCSRA |= ((1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0)); 
	ADMUX |= (1<<REFS0); // Referans AVCC yani 5V
	ADCSRA |= (1<<ADEN); // ADC'yi Aç
	ADCSRA |= (1<<ADSC); // İlk deneme ölçümünü yap ve diğer ölçüme hazır hale getir.
    while (1) 
    {
		read_adc(0x00);//ADC0 oku
    }
}
uint16_t read_adc(uint8_t channel){
	ADMUX &= 0xF0; // Eski kanal bilgisini temizle
	ADMUX |= channel; // Yeni kanal bilgisini yükle
	ADCSRA |= (1<<ADSC); // Yeni Çevirim Başlat
	while(ADCSRA & (1<<ADSC)); // Çevirim bitene kadar bekle (bu kısım çok önemli)
	return ADCW; // ADC çevirim değerini geri döndür.
}
```
### Örnek 2 (Potansiyometre Değerini Okumak)
- Arduino nun ADC2(A2) bacağına bağlı potansiyometre ve D portunun son 4 bitine led bağlıdır.
- Potansiyometre değerinin okuyoruz ve eğer değer 250 den büyük veya eşit ise D portunun son 4 bitini "1" (HIGH) yapıyoruz.
- 
```sh
#include <avr/io.h>
uint16_t read_adc(uint8_t channel);
int main(void)
{
	ADCSRA |= ((1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0)); 
	ADMUX |= (1<<REFS0); // Referans AVCC yani 5V
	ADCSRA |= (1<<ADEN); // ADC'yi Aç
	ADCSRA |= (1<<ADSC); // İlk deneme ölçümünü yap ve diğer ölçüme hazır hale getir.
   DDRD=0xf0;
   
    while (1) 
    {
		if (read_adc(0x02)>=250)
		{
			PORTD=0xf0;
		}
		else
		{
			PORTD=0x00;
		}
    }
}
uint16_t read_adc(uint8_t channel){
	ADMUX &= 0xF0; // Eski kanal bilgisini temizle
	ADMUX |= channel; // Yeni kanal bilgisini yükle
	ADCSRA |= (1<<ADSC); // Yeni Çevirim Başlat
	while(ADCSRA & (1<<ADSC)); // Çevirim bitene kadar bekle (bu kısım çok önemli)
	return ADCW; // ADC çevirim değerini geri döndür.
}
```
[Datasheet]: <https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf>
[lojikprob.com]: <http://www.lojikprob.com/>
[AVR ile Mikrodenetleyici Uygulamaları]: <http://www.lojikprob.com/diger/kitap/avr-ile-mikrodenetleyici-uygulamalari-gokhan-dokmetas-ucretsiz-e-kitap/>
[C ile AVR Programlama]: <http://www.lojikprob.com/avr/c-ile-avr-programlama-gokhan-dokmetas-e-kitap/>
[Berkan Aydın]: <https://medium.com/@berkanaydn16/analog-sinyal-ve-dijital-sinyal-dc5590cc5e1a>
[Yazı Kaynak 1]: <https://www.elprocus.com/differences-between-analog-signal-and-digital-signal/>
[Yazı Kaynak 2]: <https://teknolojiprojeleri.com/elektronik/analog-ve-sayisal-sinyaller-sistemler-nedir>
[Yazı Kaynak 3]: <http://mekatronik-sistemler.blogspot.com/2017/03/analog-dijital-sinyal-donusum-mantg.html>





# USART
### USART NEDİR ?
UART (Universal Asynchronous Receiver Transmitter), bilgisayar ve mikrokontroller veya mikrokontroller ve çevre birimler arasında haberleşmeyi sağlayan haberleşme protokolüdür. Asenkron olarak çalıştığı için herhangi bir “clock” ihtiyacı duymaz. USART (Universal Synchronous Asynchronous Receiver Transmitter) ise hem senkron hem de asenkron olarak çalışabilir. UART’a göre daha gelişmiş bir protokoldür. Haberleşme mantıklı aynı şekilde çalışır ancak USART aynı zamanda senkron haberleşmeleri de gerçekleştirebilir. Yeni çıkan bir mikroişlemcinin datasheet’ine baktığınız zaman bu birimleri genelde USART birimi olarak görüyoruz çünkü USART aynı zamanda UART’ı da kapsayan bir birim olarak tasarlanmıştır.

UART haberleşmesini gerçekleştirirken ilk olarak baudrate (veri taşıma hızı) ayarlanması gerekir. Veri taşıma hızı çok çeşitli aralıklarda olabilir ancak piyasada yaygın olarak kullanılan baudrate’ler 4800, 9600, 57600, 115200 ve mikroişlemciler için çok fazla tercih edilmese de 921600. (921600 genelde hızlı işlem gerektiren yerlerde kullanılır.) Baudrate bizim verimizin saniyede ne kadarlık byte’ını taşıyacağını belirlememize yarar.

### Üste bulunan yazının tamamı [herenkeskin.com] dan alınmıştır.

Şimdi USART Kayıtçılarını inceleyelim;

![](https://i.ibb.co/xXDTpDL/2021-02-3-16-25-16.png)

Burada gönderim ve alım için ortak kullanılan ve okuma/yazma yapılabilen 8 bitlik bir
yazmaç görüyoruz. Yapacağımız okuma ve yazmalar bu yazmaç üzerinden olacaktır.

![](https://i.ibb.co/n7tD7h8/2021-02-3-16-25-25.png)

UCSRnA kayıtçısı;
MPCMn : Bu bit çoklu işlemci iletişim modunu faal hale getirir
U2Xn : Bu bit ancak asenkron çalışmada etkili olur. Senkron iletişimde bu bit bir (1) olmalıdır. Bu biti bir (1) yapmak veri aktarım hızı bölücüsünü 16’dan 8’e düşürür ve böylelikle transfer hızını ikiye katlar.
UPEn : Bu bir eşlik hatası gerçekleştiğinde bir (1) konumuna geçer.
DORn : Bu bit veri aşma tespit edilirse bir (1) konumuna geçer. Bir veri aşma hatası alış tamponu dolu olduğunda gerçekleşir.
FEn : Bu bit alıcı tamponunun sonraki karakteri çerçeve hatasına sahipse bir (1) konumuna geçer.
UDREn : Bu bit gönderici tamponunun yani UDR0’ın yeni veri almaya müsait olduğunu belirtir. Eğer UDRE0 biti bir (1) konumunda ise tampon bellek boştur. Ayrıca bu bayrak biti Veri yazmacı boş kesmesini yürütmek için kullanılabilir.
TXC0 : UDR0 yazmacındaki tüm bitler gönderildiği zaman bu bit bir (1) konumuna geçer. TXC0 bayrak biti eğer gönderim tamamlanma kesmesi yürürse otomatik olarak sıfır (0) konumuna geçer.
RXC0 : Bu bayrak biti tampon bellekte okunmamış bir gelen veri olduğunda bir (1) konumuna geçer. Tampon bellekte veri kalmadığı zaman ise tekrar sıfır (0) konumuna geçer.

![](https://i.ibb.co/2ncsDnk/2021-02-3-16-25-38.png)

RXCIE0 : Alım Tamamlandı Kesmesi Etkinleştirme 0 

TXCIE0 : Gönderim Tamamlandı Kesmesi Etkinleştirme 0
Bu bite bir (1) yazmak TXC0 bayrak bitindeki kesmeyi etkin hale getirir.
UDRIE0 : USART Veri Yazmacı Boş Kesmesini Etkinleştirme 0
Bu biti bir (1) yapmak UDRE0 bayrak bitindeki kesmeyi etkin hale getirir.
RXEN0 : Alıcı Etkinleştirme 0 Bu biti bir (1) yapmak USART alıcısını etkinleştirir.
TXEN0 : Verici Etkinleştirme 0 Bu biti bir (1) yapmak USART vericisini etkinleştirir.
UCSZ02 : Karakter boyutu 0UCSZ02 biti UCSZ0 [1:0] bitleriyle beraber çalışır. Bu bitler alıcı ve verici görevleri yürütülürken belirlenen veri boyutu çerçevesini belirlemede kullanılır.
RXB80 : Alıcı 8. Veri Biti Eğer veri çerçevesi boyutu 9 bit ise burada 9. bit saklanır. UDR0’ın düşük bitlerini okumadan önce okunmalıdır.
TXB80 : Verici 8. Veri Biti Eğer veri çerçevesi boyutu 9 bit olarak belirlenirse gönderilecek 9. bit buraya yerleştirilir. UDR0’ın düşük bitlerini yazmadan önce yazılmalıdır.

![](https://i.ibb.co/jV2MQ3T/2021-02-3-16-25-48.png)


![](https://i.ibb.co/ch8T0YT/2021-02-3-18-24-02.png)

Bu bitler USART’ın çalışma modunu seçer

![](https://i.ibb.co/xzntc54/2021-02-3-18-24-09.png)

Bu bitler eşlik üretimi tipini ayarlar ve bunu denetler. Eğer etkinleştirilirse verici otomatik olarak eşlik bitini oluşturur ve veri çerçevesi içerisinde bunu gönderir

![](https://i.ibb.co/wz5MxgF/2021-02-3-18-24-18.png)

Bu bit verici tarafından yerleştirilen stop bitlerinin sayısını belirler. Alıcı bu ayarı görmezden gelir.

![](https://i.ibb.co/nf93TwJ/2021-02-3-18-24-24.png)

Bu bitler veri çerçevesindkei veri bitlerinin sayısını belirler

![](https://i.ibb.co/z2NRf16/2021-02-3-21-25-38.png)

![](https://i.ibb.co/93QDvdY/2021-02-3-18-44-21.png)

Bu 12 bitlik yazmaç USART veri iletim hızı bilgisini içinde bulundurur. UBRR0H baş dört biti
bulundururken UBRR0L son sekiz biti bulundurur. Baud hızı iletişim sırasında değişirse Alıcı ve verici düzgün işleyemez. UBRR0L yazmacına veri yazmak baud oranında ani bir değişme yapar


USART birimi yüklenmesi için şu adımlar gerçekleştirilmelidir,
- Baud Oranı Ayarlanmalıdır
- Veri Boyutu Ayarlanmalıdır
- TXEN ve RXEN bitleri ile Alıcı ve Verici Etkinleştirilmelidir.
- Eşlik biti ve Stop bitlerinin sayısı ayarlanmalıdır.

![](https://i.ibb.co/NtnsPSV/2021-02-3-21-59-46.png)

### Örnekler
### Örnek 1 (USART Kullanımı)
- 'a' karakteri usart ile gönderme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
#define BAUD 9600 // Baud Oranını Tanımlıyoruz
#define BAUDRATE ((F_CPU)/(BAUD*16UL)-1) // UBRR için baud verisini ayarlıyoruz
void uart_transmit (unsigned char data);
unsigned char veri_al();
int main(void)
{
   
   UCSR0B |=(1<<TXEN0)|(1<<RXEN0);//TX ve RX aktif
   UCSR0C|= (1<<UCSZ00)|(1<<UCSZ01); // Veri formatını ayarlıyoruz
   UBRR0= BAUDRATE; //Bant genişilği ayarlandı
    while (1) 
    {
		veri_gonder('a');
		_delay_ms(1000);
    }
}
// veri gönderme fonksiyonu
void veri_gonder (unsigned char data)
{
	while (!( UCSR0A & (1<<UDRE0))); // yazmacın boş olmasını bekle
	UDR0 = data; // yazmaca veri yükle
}
unsigned char veri_al()
{
	while(!(UCSR0A & (1<<RXC0)));
	return UDR0;
}
```
### Örnek 2 (USART Kullanımı Uzun Veri Gönderimi)
- "DENEME" kelimesi usart ile gönderme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
#define BAUD 9600 // Baud Oranını Tanımlıyoruz
#define BAUDRATE ((F_CPU)/(BAUD*16UL)-1) // UBRR için baud verisini ayarlıyoruz
void uart_transmit (unsigned char data);
unsigned char veri_al();
void veri_gonder_uzun(char *data);
int main(void)
{
   
   UCSR0B |=(1<<TXEN0)|(1<<RXEN0);//TX ve RX aktif
   UCSR0C|= (1<<UCSZ00)|(1<<UCSZ01); // Veri formatını ayarlıyoruz
   UBRR0= BAUDRATE; //Bant genişilği ayarlandı
    while (1) 
    {
    	veri_gonder_uzun("DENEME");
		_delay_ms(1000);
    }
}
// veri gönderme fonksiyonu
void veri_gonder (unsigned char data)
{
	while (!( UCSR0A & (1<<UDRE0))); // yazmacın boş olmasını bekle
	UDR0 = data; // yazmaca veri yükle
}
unsigned char veri_al()
{
	while(!(UCSR0A & (1<<RXC0)));
	return UDR0;
}
void veri_gonder_uzun(char *data)
{
	while (*data!='\0')
	{
		while (!( UCSR0A & (1<<UDRE0))); 
		UDR0=*data++;
	}
}
```
### Örnek 3 (USART Kullanımı Rakam veya Sayı Gönderme)
- "DENEME" kelimesi usart ile gönderme uygulaması
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
#include <stdlib.h>
#define BAUD 9600 // Baud Oranını Tanımlıyoruz
#define BAUDRATE ((F_CPU)/(BAUD*16UL)-1) // UBRR için baud verisini ayarlıyoruz
void uart_transmit (unsigned char data);
unsigned char veri_al();
void veri_gonder_uzun(char *data);
uint8_t sayi;
char str[50];
int main(void)
{
   
   UCSR0B |=(1<<TXEN0)|(1<<RXEN0);//TX ve RX aktif
   UCSR0C|= (1<<UCSZ00)|(1<<UCSZ01); // Veri formatını ayarlıyoruz
   UBRR0= BAUDRATE; //Bant genişilği ayarlandı
   
   sayi=200;
   
    while (1) 
    {
		itoa(sayi,str,10);
		veri_gonder_uzun(str);
		_delay_ms(1000);
    }
}
// veri gönderme fonksiyonu
void veri_gonder (unsigned char data)
{
	while (!( UCSR0A & (1<<UDRE0))); // yazmacın boş olmasını bekle
	UDR0 = data; // yazmaca veri yükle
}
unsigned char veri_al()
{
	while(!(UCSR0A & (1<<RXC0)));
	return UDR0;
}
void veri_gonder_uzun(char *data)
{
	
	while (*data!='\0')
	{
		while (!( UCSR0A & (1<<UDRE0))); 
		UDR0=*data++;
		
	}
}
```
### Örnek 4 (USART Kullanımı Bilgisayardan Veri Gönderme)
- Bilgisayardan gelen veri ile kullanıcı ledinin yakılması
- -PB5 de bağlı led vardır.
```sh
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
#include <stdlib.h>
#define BAUD 9600 // Baud Oranını Tanımlıyoruz
#define BAUDRATE ((F_CPU)/(BAUD*16UL)-1) // UBRR için baud verisini ayarlıyoruz
void uart_transmit (unsigned char data);
unsigned char veri_al();
void veri_gonder_uzun(char *data);
uint8_t sayi;
char str[50];
int main(void)
{
   
   UCSR0B |=(1<<TXEN0)|(1<<RXEN0);//TX ve RX aktif
   UCSR0C|= (1<<UCSZ00)|(1<<UCSZ01); // Veri formatını ayarlıyoruz
   UBRR0= BAUDRATE; //Bant genişilği ayarlandı
   
   DDRB=0x20;
   PORTB=0x00;
   
   sayi=200;
   
    while (1) 
    {
		if (veri_al()=='a')
		{
			PORTB=0x20;
		}
		else
		{
			PORTB=0x00;
			
		}
		
    }
}
// veri gönderme fonksiyonu
void veri_gonder (unsigned char data)
{
	while (!( UCSR0A & (1<<UDRE0))); // yazmacın boş olmasını bekle
	UDR0 = data; // yazmaca veri yükle
}
unsigned char veri_al()
{
	while(!(UCSR0A & (1<<RXC0)));
	return UDR0;
}
void veri_gonder_uzun(char *data)
{
	
	while (*data!='\0')
	{
		while (!( UCSR0A & (1<<UDRE0))); 
		UDR0=*data++;
		
	}
}
```
[herenkeskin.com]: <https://www.elprocus.com/differences-between-analog-signal-and-digital-signal/>


