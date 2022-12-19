# Membina Function Berantai Dalam Javascript

> 原文： [https://dev.to/zam3858/membina-function-berantai-dalam-javascript-363e](https://dev.to/zam3858/membina-function-berantai-dalam-javascript-363e)

Setelah bertahun-tahun mengadap javascript sebagai seorang web-developer, baru-baru ni terfikir hendak tahu bagaimana hendak membuat Chaining Function. Selalu menggunakan tetapi tidak pernah sendiri buat.

Untuk sesetengah orang, Fungsi Berantai ini pernah diguna cuma tidak dikenali apakah namanya. Jadi paling mudah tengok sahaja code dibawah yang diambil dari dokumentasi MomentJS:

```
 let a = moment().add(7, 'days')
                 .subtract(1, 'months')
                 .year(2009)
                 .hours(0)
                 .minutes(0)
                 .seconds(0); 
```

Enter fullscreen mode Exit fullscreen mode

Setiap fungsi atau method class Moment() itu dipanggil berterusan. Selepas add(), dipanggil substract(), kemudian year(), selepas itu minutes() dan akhir sekali hasil seconds() diumpukkan ke pembolehubah a. Ianya tidak dipanggil berasingan seperti:

```
 let a = moment().add(7, 'days');
     a = a.subtract(1, 'months');
     a = a.year(2009);
     a = a.hours(0)
     a = a.minutes(0)
     a = a.seconds(0); 
```

Enter fullscreen mode Exit fullscreen mode

Hasil kedua-dua contoh code di atas sama tetapi tentunya lebih mudah untuk membaca yang menggunakan Chain Method.

Bagaimana kita hendak membuat Chain Method ini? Tidak susah sebenarnya. Cuma function itu perlu return sesuatu yang ada function yang akan dipanggil seterusnya. Lihat contoh dibawah:

```
 class Header {
     constructor(  ) {
       this.tajuk = "Tajuk baru";
       this.lebar = "100%";
       this.warna = "#000";
     }

     tukarWarna(warna) {
        this.warna = warna;
        return this;
     }

     tukarLebar(lebar) {
        this.lebar = lebar;
        return this;
     }

     tukarTajuk(tajuk) {
        this.tajuk = tajuk;
        return this;
     }

     paparkan() {
        return `<div style="width:${ this.lebar }; 
                            background-color: ${ this.warna };"
                >${ this.tajuk }</div>`;
     }
 }

    let myHeader = new Header()
                .tukarTajuk("Ini First")
                .tukarWarna("#fff")
                .tukarLebar("80%");

    console.log(myHeader.paparkan()); 
```

Enter fullscreen mode Exit fullscreen mode

Method-method dalam Header() ini akan memulangkan instance object itu sendiri. Jadi apabila tukarTajuk() dipanggil ia memulangkan instance object Header dan memanggil pula tukarWarna(). tukarWarna() pula akan memulangkan instance object yang dikemaskini dan memanggil pula method tukarLebar() pada instance itu. Akhirnya instance Header itu akan diumpukkan kepada pembolehubah myHeader.

Mudah saja kan. :D