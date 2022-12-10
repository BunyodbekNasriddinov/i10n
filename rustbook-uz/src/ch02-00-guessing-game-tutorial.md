# Guessing o'yinini dasturlash

Keling, birgalikda amaliy loyiha orqali Rustga o'taylik! Ushbu bob sizni bir nechta umumiy Rust tushunchalari bilan tanishtirib, ulardan haqiqiy dasturda qanday foydalanishni ko'rsatib beradi.  Siz `let`, `match`, usullari, bog'langan funksiyalar, external cratelardan foydalanish va boshqalar haqida bilib olasiz! Keyingi boblarda biz ushbu fikrlarni batafsilroq ko'rib chiqamiz. Ushbu bobda siz faqat asoslarni mashq qilasiz.

Biz klassik boshlang'ich dasturlash muammosini amalga oshiramiz: taxmin qilish o'yini. Bu qanday ishlaydi: dastur 1 dan 100 gacha tasodifiy butun son hosil qiladi. Keyin u o'yinchini taxmin qilishni taklif qiladi.Tahmin kiritilgandan so'ng, dastur taxmin kichik yoki katta ekanligini ko'rsatadi. IAgar taxmin to'g'ri bo'lsa, o'yin tabrik xabarini chop etadi va chiqadi.

## Yangi loyiha yaratish

Yangi loyihani o'rnatish uchun 1-bobda yaratgan *projects* jildiga o'ting va Cargo-dan foydalanib yangi loyiha yarating, masalan:

```console
$ cargo new guessing_game
$ cd guessing_game
```

Birinchi `cargo new` buyrug'i birinchi argument sifatida loyiha nomini (`guessing_game`)ni oladi. Ikkinchi buyruq yangi loyiha jildiga kiradi.

Yaratilgan *Cargo.toml* fayliga qarang:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial
rm -rf no-listing-01-cargo-new
cargo new no-listing-01-cargo-new --name guessing_game
cd no-listing-01-cargo-new
cargo run > output.txt 2>&1
cd ../../..
-->

<span class="filename">Fayl nomi: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

1-bobda ko'rganingizdek, `cargo new` “Hello, world!”  so'zini yaratadi. siz uchun dastur. *src/main.rs* faylini tekshiring:

<span class="filename">Fayl nomi: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Keling, ushbu "Hello, world!" dasturni yarating va cargo run buyrug'i yordamida ishga tushiring :

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50s
     Running `target/debug/guessing_game`
Hello, world!
```

`run` buyrug‘i loyihani tezda takrorlash kerak bo‘lganda foydali bo‘ladi, biz bu o‘yinda qilganimizdek, keyingisiga o‘tishdan oldin har bir iteratsiyani tezda sinab ko‘ramiz.

*src/main.rs* faylini qayta oching. Siz ushbu fayldagi barcha kodlarni yozasiz.

## Guessga o'yiniga ishlov berish

Taxmin qilish o'yini dasturining birinchi qismi foydalanuvchi kiritishini so'raydi, ushbu kiritishni qayta ishlaydi va kirish kutilgan shaklda ekanligini tekshiradi. Boshlash uchun biz o'yinchiga taxmin kiritishga ruxsat beramiz. 2-1 ro'yxatdagi kodni *src/main.rs* ichiga kiriting.

<span class="filename">Fayl nomi: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Raqamni topish o'yini!");

    println!("Iltimos, taxminingizni kiriting.");

    let mut taxmin = String::new();

    io::stdin()
        .read_line(&mut taxmin)
        .expect("Satrni o‘qib bo‘lmadi");

    println!("Sizning taxminingiz: {taxmin}");
}
```

<span class="caption">Ro'yxat 2-1: Foydalanuvchi tomonidan taxmin qilinadigan va uni chop etadigan kod</span>

Ushbu kod juda ko'p ma'lumotlarni o'z ichiga oladi, shuning uchun uni satrga o'tkazamiz. Foydalanuvchi kiritishini olish va natijani chiqish sifatida chop etish uchun biz `io` kirish/chiqish kutubxonasini qamrab olishimiz kerak. `io` kutubxonasi `std` deb nomlanuvchi standart kutubxonadan keladi:

```rust,ignore
use std::io;
```

Odatda, Rust standart kutubxonada belgilangan elementlar to'plamiga ega bo'lib, u har bir dastur doirasiga kiradi. Ushbu to'plam *prelude* deb ataladi va siz undagi hamma narsani [standart kutubxona texnik hujjatlarida][prelude] ko'rishingiz mumkin.

Agar siz foydalanmoqchi bo'lgan tur muqaddimada bo'lmasa, siz ushbu turni `use` iborasi bilan aniq kiritishingiz kerak. `std::io` kutubxonasidan foydalanish sizga bir qator foydali xususiyatlarni, jumladan, foydalanuvchi kiritishini qabul qilish imkoniyatini beradi.

1-bobda ko'rganingizdek, `main` funksiya dasturga kirish nuqtasidir:

```rust,ignore
fn main() {
```

`fn` sintaksisi yangi funktsiyani e'lon qiladi; Qavslar, `()`, hech qanday parametr yo'qligini bildiradi; va jingalak qavs, `{`, funksiyaning asosiy qismini boshlaydi.

1-bobda ham bilib olganingizdek, `println!` bu ekranga satrni chop etuvchi makros:

```rust,ignore
    println!("Raqamni topish o'yini!");

    println!("Iltimos, taxminingizni kiriting.");
```

Ushbu kod o'yin nima ekanligini ko'rsatuvchi va foydalanuvchidan ma'lumot so'rashni so'rashni chop etadi.

### O'zgaruvchilar bilan qiymatlarni saqlash

Keyinchalik, foydalanuvchi ma'lumotlarini saqlash uchun *o'zgaruvchi* yaratamiz, masalan:

```rust,ignore
    let mut taxmin = String::new();
```

Endi dastur qiziqarli bo'lib bormoqda! Bu kichik satrda juda ko'p narsa bor. O'zgaruvchini yaratish uchun `let` iborasidan foydalanamiz. Mana yana bir misol:

```rust,ignore
let olmalar = 5;
```

Bu qator `olmalar` nomli yangi o‘zgaruvchini yaratadi va uni 5 qiymatiga bog‘laydi. Rustda o'zgaruvchilar standard bo'yicha o'zgarmasdir, ya'ni o'zgaruvchiga qiymat berganimizdan keyin qiymat o'zgarmaydi.Biz ushbu kontseptsiyani 3-bobdagi [”O'zgaruvchilar va O'zgaruvchanlik”][variables-and-mutability]<!-- ignore --> bo'limida batafsil muhokama qilamiz. Oʻzgaruvchini oʻzgaruvchan qilish uchun oʻzgaruvchi nomidan oldin `mut` qoʻshamiz:

```rust,ignore
let olmalar = 5; // o'zgarmas
let mut bananlar = 5; // o'zgaruvchan
```

> Eslatma: `//` sintaksisi satr oxirigacha davom etadigan izohni
> boshlaydi. Rust izohlarda hamma narsani e'tiborsiz qoldiradi.
> Izohlarni [3-bobda][comments]<!-- ignore --> batafsilroq muhokama qilamiz.

Taxmin qilish o'yin dasturiga qaytsak, endi bilasizki, `let mut taxmin` `taxmin` nomli o'zgaruvchan o'zgaruvchini kiritadi. Teng belgisi (`=`) Rustga biz hozir biror narsani oʻzgaruvchiga bogʻlamoqchi ekanligimizni bildiradi. Tenglik belgisining o'ng tomonida `taxmin` bog'langan qiymat joylashgan bo'lib, u `String::new` funksiyasini chaqirish natijasidir, bu `String`ning yangi nusxasini qaytaradi.
[String][string]<!-- ignore --> standart kutubxona tomonidan taqdim etilgan satr turi bo'lib, u rivojlantirib boriladigan, UTF-8 kodlangan matn bitidir.

`::new` qatoridagi `::` sintaksisi `new` `String` tipidagi bog`langan funksiya ekanligini bildiradi. *Assosiatsiyalangan funksiya* bu funksiya
turida amalga oshiriladi, bu holda `String`. Ushbu `new` funksiya yangi, bo'sh qatorni yaratadi. Siz ko'p turdagi `new` funksiyani topasiz, chunki u qandaydir yangi qiymatni yaratadigan funksiyaning umumiy nomi.

To'liq `let mut taxmin = String::new();` qatori hozirda `String` ning yangi, bo'sh nusxasiga bog'langan o'zgaruvchan o'zgaruvchini yaratadi.

### Foydalanuvchi ma'lumotlarini qabul qilish

Eslatib o'tamiz, biz dasturning birinchi qatoriga `use std::io;` bilan standart kutubxonadan kiritish/chiqarish funksiyasini kiritgan edik. Endi biz `io` modulidan `stdin` funksiyasini chaqiramiz, bu bizga foydalanuvchi kiritishini boshqarish imkonini beradi:

```rust,ignore
    io::stdin()
        .read_line(&mut taxmin)
```

Agar biz dasturning boshida `use std::io;` bilan `io` kutubxonasini import qilmagan bo'lsak, biz ushbu funktsiya chaqiruvini `std::io::stdin` sifatida yozish orqali funksiyadan foydalanishimiz xam mumkin. `stdin` funksiyasi [`std::io::Stdin`][iostdin]<!-- ignore --> misolini qaytaradi, bu sizning terminalingiz uchun standart kirish uchun asosni ifodalovchi tur.

Keyinchalik, `.read_line(&mut guess)` qatori foydalanuvchidan ma'lumot olish uchun standart kiritish nuqtasidagi [`read_line`][read_line]<!--
ignore --> usulini chaqiradi.
Shuningdek, foydalanuvchi kiritgan maʼlumotlarni qaysi qatorda saqlash kerakligini aytish uchun `read_line` ga argument sifatida `&mut taxmin` ni oʻtkazamiz. `read_line` ning toʻliq vazifasi foydalanuvchi nima yozganidan qatʼiy nazar standart kiritishga olish va uni satrga qoʻshishdir (uning mazmunini qayta yozmasdan), shuning uchun biz bu qatorni argument sifatida beramiz. String argumenti o'zgaruvchan bo'lishi kerak, shuning uchun usul string tarkibini o'zgartirishi mumkin.

`&` bu argumentning *ma'lumotnoma* ekanligini bildiradi, bu sizga kodingizning bir nechta qismlariga ushbu ma'lumotni xotiraga bir necha marta nusxalash kerak bo'lmasdan bitta ma'lumotga kirish imkonini beradi. Ma'lumotnomalar murakkab xususiyat bo'lib, Rustning asosiy afzalliklaridan biri havolalardan foydalanish qanchalik xavfsiz va oson ekanligidir. Ushbu dasturni tugatish uchun ko'p bilimlrga ega bo'lishingiz shart emas. Hozircha siz bilishingiz kerak bo'lgan narsa shundaki, o'zgaruvchilar singari, havolalar ham standard bo'yicha o'zgarmasdir. Demak, uni oʻzgaruvchan qilish uchun `&taxmin` oʻrniga `&mut taxmin` yozish kerak. (4-bobda havolalar ko'proq va yaxshiroq tushuntiriladi)

<!-- Old heading. Do not remove or links may break. -->
<a id="handling-potential-failure-with-the-result-type"></a>

### Potensial nosozlikni `Result` turi bilan hal qilish

Biz hali ham ushbu kod qatori ustida ishlayapmiz. Biz hozir matnning uchinchi qatorini muhokama qilmoqdamiz, lekin u hali ham bitta mantiqiy kod qatorining bir qismi ekanligini unutmang. Keyingi qism bu method:

```rust,ignore
        .expect("Satrni o‘qib bo‘lmadi");
```

Biz ushbu kodni quyidagicha yozishimiz mumkin edi:

```rust,ignore
io::stdin().read_line(&mut taxmin).expect("Satrni o‘qib bo‘lmadi");
```

Biroq, bitta uzun qatorni o'qish qiyin, shuning uchun uni bo'lish yaxshidir. `.method_name()` sintaksisi bilan methodni chaqirganda uzun qatorlarni ajratishga yordam berish uchun yangi qator va boshqa bo'shliqlarni kiritish ko'pincha oqilona. Endi bu kod nima qilishini muhokama qilaylik.

Yuqorida aytib o'tilganidek, `read_line` foydalanuvchi kiritgan narsani biz unga o'tkazadigan qatorga qo'yadi, lekin u `Result` qiymatini ham qaytaradi. [`Result`][result]<!-- ignore --> - ko'pincha *enum* deb ataladigan [*enumeration*][enums]<!-- ignore -->, bu bir nechta mumkin bo'lgan holatlardan birida bo'lishi mumkin bo'lgan tur. Har bir mumkin bo'lgan holatni *variant* deb ataymiz.

[6-bobda][enums]<!-- ignore --> enumlar batafsilroq yoritiladi. Ushbu `Result` turlarining maqsadi xatolarni qayta ishlash ma'lumotlarini kodlashdir.

`Result` variantlari `Ok` va `Err`. `Ok` varianti operatsiya muvaffaqiyatli bo'lganligini bildiradi va `Ok` ichida muvaffaqiyatli yaratilgan qiymat.
`Err` varianti operatsiya bajarilmaganligini bildiradi va `Err` operatsiya qanday yoki nima uchun bajarilmagani haqida maʼlumotni oʻz ichiga oladi.

`Result` turidagi qiymatlar, har qanday turdagi qiymatlar kabi, ularda aniqlangan usullarga ega. `Result` misolida siz murojat qilishingiz mumkin bo'lgan [`expect` methodi][expect]<!-- ignore --> mavjud. Agar `Result` ning ushbu namunasi `Err` qiymati bo'lsa, `expect` dasturning ishlamay qolishiga olib keladi va `expect` ga argument sifatida siz uzatgan xabarni ko'rsatadi. Agar `read_line` usuli `Err`ni qaytarsa, bu asosiy operatsion tizimdan kelgan xato natijasi bo'lishi mumkin.

Agar `Result`ning ushbu namunasi `Ok` qiymati bo‘lsa, `expect` `Ok` ushlab turgan qaytarish qiymatini oladi va siz undan foydalanishingiz uchun aynan shu qiymatni sizga qaytaradi.
Bunday holda, bu qiymat foydalanuvchi kiritishidagi baytlar soni.

Agar siz `expect` ga murojat qilmasangiz, dastur kompilyatsiya qilinadi, lekin siz ogohlantirish olasiz:

```console
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
warning: unused `Result` that must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&taxmin guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: `#[warn(unused_must_use)]` on by default
   = note: this `Result` may be an `Err` variant, which should be handled

warning: `guessing_game` (bin "guessing_game") generated 1 warning
    Finished dev [unoptimized + debuginfo] target(s) in 0.59s
```

Rust `read_line` dan qaytarilgan `Result` qiymatini ishlatmaganligingiz haqida ogohlantiradi, bu dastur mumkin bo'lgan xatoni hal qilmaganligini ko'rsatadi.

Ogohlantirishni yo'qotishning to'g'ri yo'li aslida xatolarni qayta ishlash kodini yozishdir, ammo bizning holatlarimizda muammo yuzaga kelganda biz ushbu dasturni ishdan chiqarishni xohlaymiz, shuning uchun biz `expect` dan foydalanishimiz mumkin. Xatolarni tiklash haqida [9-bobda]recover]<!-- ignore --> bilib olasiz.

### Qiymatlarni `println!`  bilan chop etish

Yopuvchi jingalak qavsdan tashqari, kodda hozirgacha muhokama qilinadigan yana bitta satr mavjud:

```rust,ignore
    println!("Sizning taxminingiz: {taxmin}");
```

Ushbu satr foydalanuvchi kiritishini o'z ichiga olgan qatorni chop etadi. `{}` jingalak qavslar to'plami o'rnini egallaydi: `{}` qiymatini joyida ushlab turadigan qisqichbaqa qisqichlari deb tasavvur qiling. O'zgaruvchining qiymatini chop etishda o'zgaruvchi nomi jingalak qavslar ichiga kirishi mumkin. Ifodani baholash natijasini chop etishda format satriga bo'sh jingalak qavslarni joylashtiring, so'ngra har bir bo'sh jingalak qavs o'rnini egallagan holda bir xil tartibda chop etish uchun vergul bilan ajratilgan iboralar ro'yxati bilan format qatoriga amal qiling. O‘zgaruvchini va ifoda natijasini `println!` ga bitta chaqiruvda chop etish quyidagicha ko‘rinadi:

```rust
let x = 5;
let y = 10;

println!("x = {x} va y + 2 = {}", y + 2);
```

Bu kod `x = 5 va y = 12` ni chop etadi.

### Birinchi qismni sinovdan o'tkazish

Keling, taxmin qilish o'yinining birinchi qismini sinab ko'raylik. Uni `cargo run` yordamida ishga tushiring:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-01/
cargo clean
cargo run
input 6 -->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 6.44s
     Running `target/debug/guessing_game`
Raqamni topish o'yini!
Iltimos, taxminingizni kiriting.
6
Sizni taxminingiz: 6
```

Shu nuqtada, o'yinning birinchi qismi tugadi: biz klaviaturadan ma'lumotlarni olamiz va keyin uni chop etamiz.

## Yashirin raqam yaratish

Keyinchalik, foydalanuvchi taxmin qilishga harakat qiladigan maxfiy raqamni yaratishimiz kerak. Yashirin raqam har safar boshqacha bo'lishi kerak, shuning uchun o'yinni bir necha marta o'ynash qiziqarli bo'ladi. O'yin juda qiyin bo'lmasligi uchun biz 1 dan 100 gacha bo'lgan tasodifiy raqamdan foydalanamiz. Rust hali o'zining standart kutubxonasida tasodifiy raqamlar funksiyasini o'z ichiga olmaydi. Biroq, Rust jamoasi ushbu funksiyaga [`rand` crate][randcrate]i taqdim etadi.

### Ko'proq funksionallikka ega bo'lish uchun Cratedan foydalanish

Esda tutingki, crate Rust manba kodi fayllari to'plamidir. Biz qurayotgan loyiha *binary crate* bo'lib, u bajariladigan. `rand` crate boshqa dasturlarda foydalanish uchun moʻljallangan va mustaqil ravishda bajarib boʻlmaydigan kodni oʻz ichiga olgan *library crate*.

Cargning tashqi cratelarni muvofiqlashtirishi bu erda Cargp haqiqatan ham ishlaydi. `rand` dan foydalanadigan kodni yozishdan oldin, biz *Cargo.toml* faylini `rand` cratesini dependency sifatida qo‘shish uchun o‘zgartirishimiz kerak. Hozir o‘sha faylni oching va Cargo siz uchun yaratgan`[dependencies]` bo‘limi sarlavhasi ostiga quyidagi qatorni qo‘shing.`rand` ni aynan bizda boʻlganidek, ushbu versiya raqami bilan belgilaganingizga ishonch hosil qiling, aks holda ushbu qoʻllanmadagi kod misollari ishlamasligi mumkin:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
* ch14-03-cargo-workspaces.md
-->

<span class="filename">Fayl nomi: Cargo.toml</span>

```toml
rand = "0.8.3"
```

*Cargo.toml* faylida sarlavhadan keyingi hamma narsa boshqa bo'lim boshlanmaguncha davom etadigan bo'limning bir qismidir. `[dependencies]` da siz Cargo loyihangiz qaysi tashqi cratelarga bog'liqligini va bu cratelarning qaysi versiyalari kerakligini aytasiz. Bunday holda, biz `rand` crateni `0.8.5` semantik versiya spetsifikatsiyasi bilan belgilaymiz. Cargo versiya raqamlarini yozish uchun standart bo'lgan [Semantic Versioning][semver]<!-- ignore -->ni (ba'zan *SemVer* deb ataladi) tushunadi. `0.8.5` spetsifikatsiyasi aslida `^0.8.5` ning qisqartmasi boʻlib, kamida 0.8.5, lekin 0.9.0 dan past boʻlgan har qanday versiyani bildiradi.

Cargo ushbu versiyalarni 0.8.5 versiyasiga mos keladigan umumiy API-larga ega deb hisoblaydi va bu spetsifikatsiya sizga ushbu bobdagi kod bilan tuziladigan so‘nggi patch versiyasini olishingizni kafolatlaydi. 0.9.0 yoki undan kattaroq versiyalar quyidagi misollar ishlatadigan API bilan bir xil bo'lishi kafolatlanmaydi.

Endi, hech qanday kodni o'zgartirmasdan, 2-2 ro'yxatda ko'rsatilganidek, loyihani build qilaylik.

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
rm Cargo.lock
cargo clean
cargo build -->

```console
$ cargo build
    Updating crates.io index
  Downloaded rand v0.8.5
  Downloaded libc v0.2.127
  Downloaded getrandom v0.2.7
  Downloaded cfg-if v1.0.0
  Downloaded ppv-lite86 v0.2.16
  Downloaded rand_chacha v0.3.1
  Downloaded rand_core v0.6.3
   Compiling libc v0.2.127
   Compiling getrandom v0.2.7
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.16
   Compiling rand_core v0.6.3
   Compiling rand_chacha v0.3.1
   Compiling rand v0.8.5
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
```

<span class="caption">Ro'yxat 2-2: rand cratesini dependency sifatida qo'shgandan so'ng `cargo build` dan olingan natija</span>

Siz turli xil versiya raqamlarini (lekin ularning barchasi SemVer tufayli kod bilan mos keladi!) va turli xil satrlarni (operatsion tizimga qarab) ko'rishingiz mumkin va satrlar boshqa tartibda bo'lishi mumkin.

Biz tashqi dependency qo'shganimizda, Cargo [Crates.io][cratesio] ma'lumotlarining nusxasi bo'lgan  *registry* dan dependency uchun zarur bo'lgan barcha narsalarning so'nggi versiyalarini oladi.Crates.io - bu Rust ekotizimidagi odamlar o'zlarining ochiq manbali Rust loyihalarini boshqalar foydalanishi uchun joylashtiradigan joy.

registrni yangilagandan so'ng, Cargo  `[dependencies]`  bo'limini tekshiradi va ro'yxatda hali yuklab olinmagan cratelarni yuklab oladi. Bu holatda, garchi biz faqat `rand` ni dependency sifatida ko'rsatgan bo'lsak-da, Cargo `rand` ishlashga bog'liq bo'lgan boshqa cratelarni ham oldi. Cratelarni yuklab olgandan so'ng, Rust ularni kompilyatsiya qiladi va keyin mavjud bo'lgan dependency bilan loyihani tuzadi.

Agar siz hech qanday o'zgartirishlarsiz darhol `cargo build` ni qayta ishga tushirsangiz, `Finished` qatoridan boshqa hech qanday natija olmaysiz. Cargo allaqachon dependencylarni yuklab olganini va kompilyatsiya qilganini biladi va siz *Cargo.toml* faylida ular haqida hech narsani o'zgartirmagansiz. Cargo, shuningdek, kodingiz haqida hech narsani o'zgartirmaganligingizni biladi, shuning uchun u ham uni qayta kompilyatsiya qilmaydi. Hech narsa qilmasdan, u shunchaki chiqib ketadi.

Agar siz *src/main.rs* faylini ochsangiz, ahamiyatsiz o'zgarishlarni amalga oshirsangiz va keyin uni saqlab va qayta build qilsangiz, siz faqat ikkita chiqish qatorini ko'rasiz:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
touch src/main.rs
cargo build -->

```console
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

Bu satrlar shuni ko'rsatadiki, Cargo faqat *src/main.rs* fayliga kichik o'zgartirishingiz bilan buildni yangilaydi. Sizning dependencylaringiz o'zgarmadi, shuning uchun Cargo allaqachon yuklab olingan va ular uchun tuzilgan narsadan qayta foydalanishi mumkinligini biladi..

#### *Cargo.lock* fayli bilan qayta tiklanadigan tuzilmalarni ta'minlash

Cargoda siz yoki boshqa birov kodingizni har safar yaratganingizda bir xil artefaktni qayta tiklashingiz mumkinligini ta'minlaydigan mexanizm mavjud: Siz aksini ko'rsatmaguningizcha, cargo faqat siz ko'rsatgan dependency versiyalaridan foydalanadi. Masalan, kelasi hafta `rand` cratening 0.8.6 versiyasi chiqadi va bu versiyada muhim xatoliklar tuzatilgan, lekin u sizning kodingizni buzadigan regressiyani ham o‘z ichiga oladi. Buni hal qilish uchun Rust birinchi marta  `cargo build` dasturini ishga tushirganingizda *Cargo.lock* faylini yaratadi, shuning uchun biz endi bu *guessing_game* jildida mavjud.

Loyihani birinchi marta yaratganingizda, Cargo mezonlarga mos keladigan dependencylarning barcha versiyalarini aniqlaydi va keyin ularni *Cargo.lock* fayliga yozadi. Keyingi loyihangizni yaratganingizda, Cargo *Cargo.lock* fayli mavjudligini ko'radi va versiyalarni qayta aniqlash uchun barcha ishlarni bajarishdan ko'ra, u erda ko'rsatilgan versiyalardan foydalanadi. TBu sizga avtomatik ravishda takrorlanadigan tuzilishga ega bo'lish imkonini beradi. Boshqacha qilib aytganda, *Cargo.lock* fayli tufayli loyihangiz aniq yangilanmaguningizcha 0.8.5 da qoladi.
*Cargo.lock* fayli qayta tiklanadigan tuzilmalar uchun muhim bo'lgani uchun u ko'pincha loyihangizdagi kodning qolgan qismi bilan manba nazoratida tekshiriladi.

#### Yangi versiyani olish uchun Crateni yangilash

Crateni yangilamoqchi bo'lsangiz, Cargo `update` buyrug'ini beradi, bu buyruq *Cargo.lock* faylini e'tiborsiz qoldiradi va *Cargo.toml* dagi texnik xususiyatlaringizga mos keladigan barcha so'nggi versiyalarni aniqlaydi. Keyin Cargo ushbu versiyalarni *Cargo.lock* fayliga yozadi. Aks holda, standart bo'yicha, Cargo faqat 0.8.5 dan katta va 0.9.0 dan kichik versiyalarni qidiradi. Agar `rand` cratesi ikkita yangi 0.8.6 va 0.9.0 versiyalarini chiqargan bo'lsa, `cargo update` ni ishga tushirgan bo'lsangiz, quyidagilarni ko'rasiz:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
cargo update
assuming there is a new 0.8.x version of rand; otherwise use another update
as a guide to creating the hypothetical output shown here -->

```console
$ cargo update
    Updating crates.io index
    Updating rand v0.8.5 -> v0.8.6
```

Cargo 0.9.0 versiyasiga e'tibor bermaydi. Bu vaqtda siz *Cargo.lock* faylingizda oʻzgarishlarni ham sezasiz, bunda siz hozir foydalanayotgan `rand`  cratesi versiyasi 0.8.6. `rand` 0.9.0 versiyasidan yoki 0.9.*x* seriyasining istalgan versiyasidan foydalanish uchun *Cargo.toml* faylini quyidagi koʻrinishda yangilashingiz kerak boʻladi:

```toml
[dependencies]
rand = "0.9.0"
```

Keyingi safar `cargo build`ni ishga tushirganingizda, Cargo mavjud cratelar reestrini yangilaydi va siz ko‘rsatgan yangi versiyaga muvofiq `rand` talablaringizni qayta baholaydi.

[Cargo][doccargo]<!-- ignore --> va uning [ekotizimlari][doccratesio]<!-- ignore --> haqida ko'p gapirish mumkin, biz ularni 14-bobda muhokama qilamiz, ammo hozircha bilishingiz kerak bo'lgan narsa shu. Cargo kutubxonalarni qayta ishlatishni juda osonlashtiradi, shuning uchun Rustaceans bir nechta paketlardan yig'ilgan kichikroq loyihalarni yozishga qodir.

### Tasodifiy raqamni yaratish

Keling, taxmin qilish uchun raqam yaratishda `rand` dan foydalanishni boshlaylik. Keyingi qadam 2-3 ro'yxatda ko'rsatilganidek *src/main.rs* ni yangilashdir.

<span class="filename">Fayl nomi: src/main.rs</span>

```rust,ignore
use std::io;
use rand::Rng;

fn main() {
    println!("Raqamni topish o'yini!");

    let yashirin_raqam = rand::thread_rng().gen_range(1..=100);

    println!("Yashirin raqam: {yashirin_raqam}");

    println!("Iltimos, taxminingizni kiriting.");

    let mut taxmin = String::new();

    io::stdin()
        .read_line(&mut taxmin)
        .expect("Satrni o‘qib bo‘lmadi");

    println!("Sizning taxminingiz: {taxmin}");
}
```

<span class="caption">Ro'yxat 2-3: Tasodifiy raqam yaratish uchun kod qo'shiladi</span>

Avval `use rand::Rng;` qatorini qo'shamiz. `Rng` xususiyati tasodifiy sonlar generatorlari qo'llaydigan usullarni belgilaydi va biz ushbu usullardan foydalanishimiz uchun bu xususiyat mos bo'lishi kerak. 10-bobda xususiyatlar batafsil yoritiladi.

Keyin o'rtada ikkita qator qo'shamiz. Birinchi qatorda biz `rand::thread_rng` funksiyasini chaqiramiz, bu bizga biz foydalanmoqchi bo'lgan tasodifiy sonlar generatorini beradi: joriy bajarilish oqimi uchun mahalliy bo'lgan va operatsion tizim tomonidan ekilgan. Keyin tasodifiy sonlar generatorida `gen_range` usulini chaqiramiz. Bu usul biz `use rand::Rng;`  iborasi bilan qamrab olgan `Rng` xususiyati bilan aniqlanadi. `gen_range` usuli argument sifatida diapazon ifodasini oladi va diapazonda tasodifiy son hosil qiladi. Biz bu yerda foydalanayotgan diapazon ifodasi turi `start..=end`  shaklini oladi va pastki va yuqori chegaralarni qamrab oladi, shuning uchun biz 1 va 100 oralig‘idagi raqamni so‘rash uchun `1..=100` ni belgilashimiz kerak. .


> Eslatma: Siz faqat qaysi xususiyatlardan foydalanishni va qaysi usullar va funktsiyalarni
> cratedan chaqirishni bila olmaysiz, shuning uchun har bir crateda foydalanish bo'yicha
> ko'rsatmalar mavjud. Cargo-ning yana bir qulay xususiyati shundaki, `cargo doc --open` buyrug'ini
> ishga tushirish sizning barcha dependencylar tomonidan taqdim etilgan texnik hujjatlarni
> mahalliy sifatida tuzadi va uni brauzeringizda ochadi. Agar siz `rand` cratedagi boshqa
> funksiyalarga qiziqsangiz, masalan, `cargo doc --open` ni ishga tushiring va chap tomondagi
> yon paneldagi `rand` tugmasini bosing.

Ikkinchi yangi qator maxfiy raqamni chop etadi. Bu dasturni ishlab chiqishda uni sinab ko'rishimiz uchun foydalidir, lekin biz uni oxirgi versiyadan o'chirib tashlaymiz. Agar dastur boshlanishi bilanoq javobni chop etsa, bu unchalik o'yin emas!

Dasturni bir necha marta ishga tushirishga harakat qiling:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-03/
cargo run
4
cargo run
5
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
     Running `target/debug/guessing_game`
Raqamni topish o'yini!
Yashirin raqam: 7
Iltimos, taxminingizni kiriting.
4
Siznig taxminingiz: 4

$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
     Running `target/debug/guessing_game`
Raqamni topish o'yini!
Yashirin raqam: 83
Iltimos, taxminingizni kiriting.
5
Siznig taxminingiz: 5
```

Siz turli xil tasodifiy raqamlarni olishingiz kerak va ularning barchasi 1 dan 100 gacha raqamlar bo'lishi kerak. Ajoyib ish!

## Guessni maxfiy raqam bilan solishtirish

Endi bizda foydalanuvchi kiritishi va tasodifiy raqam bor, biz ularni solishtirishimiz mumkin. Ushbu qadam 2-4 ro'yxatda ko'rsatilgan. E'tibor bering, bu kod hozircha tuzilmaydi, biz tushuntiramiz.

<span class="filename">Fayl nomi: src/main.rs</span>

```rust,ignore,does_not_compile
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    // --snip--

    println!("Sizning taxminingiz: {taxmin}");

    match taxmin.cmp(&yashirin_raqam) {
        Ordering::Less => println!("Raqam Kichik!"),
        Ordering::Greater => println!("Raqam katta!"),
        Ordering::Equal => println!("Siz yutdingiz!"),
    }
}
```

<span class="caption">Listing 2-4: Ikki raqamni solishtirishning mumkin bo'lgan qaytish qiymatlarini boshqarish</span>

Avval biz standart kutubxonadan `std::cmp::Ording` deb nomlangan turni olib keladigan yana bir `use` iborasini qo'shamiz. `Ordering` turi boshqa raqam boʻlib, `Less`, `Greater` va `Equal` variantlariga ega. Bu ikkita qiymatni solishtirganda mumkin bo'lgan uchta natijadir.

Keyin pastki qismida `Ordering` turidan foydalanadigan beshta yangi qator qo'shamiz. `cmp` usuli ikkita qiymatni solishtiradi va uni solishtirish mumkin bo'lgan har qanday narsani chaqirish mumkin. Siz solishtirmoqchi bo'lgan narsaga havola kerak: bu erda `taxmin` bilan `yashirin_raqam` solishtiriladi. Keyin u biz `use`  iborasi bilan qamrab olgan `Ordering`  raqamining variantini qaytaradi. Biz `taxmin` va `yashirin_raqam` qiymatlari bilan `cmp` ga murojatdan `Ordering` ning qaysi varianti qaytarilganiga qarab, keyin nima qilish kerakligini hal qilish uchun [`match`][match]<!-- ignore --> ifodasidan foydalanamiz.

`Match` ifodasi *arms* dan tuzilgan. Arm mos keladigan *pattern* va agar `match` ga berilgan qiymat armning patterniga mos kelsa, bajarilishi kerak bo'lgan koddan iborat. Rust `match` ga berilgan qiymatni oladi va har bir armning patternini o'z navbatida ko'rib chiqadi. Patternlar va `match` konstruksiyasi Rust-ning kuchli xususiyatlari hisoblanadi: ular sizning kodingiz duch kelishi mumkin bo'lgan turli vaziyatlarni ifodalash imkonini beradi va ularning barchasini boshqarishingizga ishonch hosil qiladi. Bu xususiyatlar mos ravishda 6-bobda va 18-bobda batafsil yoritiladi.

Keling, bu yerda ishlatadigan `match` iborasi bilan bir misolni ko'rib chiqaylik. Aytaylik, foydalanuvchi 50 ni taxmin qilgan va bu safar tasodifiy yaratilgan maxfiy raqam 38 ni tashkil qiladi.

Kod 50 ni 38 ga solishtirganda, `cmp` usuli `Ordering::Greater` ni qaytaradi, chunki 50 38 dan katta. `match` ifodasi `Ordering::Greater` qiymatini oladi va har bir armning patternini tekshirishni boshlaydi. U birinchi armning `Ordering::Less` patternini koʻrib chiqadi va `Ordering::Greater` qiymati `Ordering::Less` qiymatiga mos kelmasligini koʻradi, shuning uchun u armdagi kodga eʼtibor bermaydi va keyingi armga oʻtadi. Keyingi armning namunasi `Ordering::Greater` boʻlib, `Ordering::Greater` bilan *does* match  keladi! Oʻsha armdagi bogʻlangan kod ishga tushadi va ekranga `Raqam katta!` deb chop etiladi. `match` iborasi birinchi muvaffaqiyatli o'yindan keyin tugaydi, shuning uchun bu senariydagi oxirgi armni ko'rib chiqmaydi.

Biroq, 2-4 ro'yxatdagi kod hali kompilyatsiya qilinmaydi. Keling, sinab ko'raylik:

<!--
The error numbers in this output should be that of the code **WITHOUT** the
anchor or snip comments
-->

```console
$ cargo build
   Compiling libc v0.2.86
   Compiling getrandom v0.2.2
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.10
   Compiling rand_core v0.6.2
   Compiling rand_chacha v0.3.0
   Compiling rand v0.8.3
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:22:21
   |
22 |     match taxmin.cmp(&yashirin_raqam) {
   |                     ^^^^^^^^^^^^^^ expected struct `String`, found integer
   |
   = note: expected reference `&String`
              found reference `&{integer}`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `guessing_game` due to previous error
```

Xatoning asosi *mos kelmaydigan turlar* mavjudligini bildiradi. Rust kuchli, statik turdagi tizimga ega. Biroq, u ham turdagi xulosaga ega. Biz `let mut taxmin = String::new()` deb yozganimizda, Rust `taxmin` `String` bo'lishi kerak degan xulosaga keldi va bizni turni yozishga majburlamadi. Boshqa tomondan, `yashirin_raqam` raqam turidir. Rust raqamlarining bir nechta turlari 1 dan 100 gacha qiymatga ega bo'lishi mumkin: `i32`, 32 bitli raqam; `u32`, imzosiz 32-bitli raqam; `i64`, 64-bitli raqam; boshqalar kabi. Agar boshqacha koʻrsatilmagan boʻlsa, Rust standart boʻyicha `i32` ga oʻrnatiladi, bu `yashirin_raqam` turiga, agar siz Rustning boshqa raqamli turini chiqarishiga olib keladigan turdagi maʼlumotlarni boshqa joyga qoʻshmasangiz. Xatoning sababi shundaki, Rust satr va raqam turini taqqoslay olmaydi.

Oxir-oqibat, biz dastur tomonidan kiritilgan `String` ni haqiqiy son turiga aylantirmoqchimiz, shuning uchun uni raqamli raqam bilan yashirin raqam bilan solishtirishimiz mumkin.Buni `main` funktsiya tanasiga ushbu qatorni qo'shish orqali qilamiz:

<span class="filename">Fayl nomi: src/main.rs</span>

```rust,ignore
    // --snip--

    let mut taxmin = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Satrni o‘qib bo‘lmadi");

    let taxmin: u32 = taxmin.trim().parse().expect("Iltimos, raqam yozing!");

    println!("Sizning taxminingiz: {taxmin}");

    match taxmin.cmp(&yashirin_raqam) {
        Ordering::Less => println!("Raqam Kichik!"),
        Ordering::Greater => println!("Raqam katta!"),
        Ordering::Equal => println!("Siz yutdingiz!"),
    }
```

Satr

```rust,ignore
let taxmin: u32 = taxmin.trim().parse().expect("Iltimos, raqam yozing!");
```

Biz `taxmin` nomli o'zgaruvchini yaratamiz. Ammo shoshilmang, dasturda allaqachon `taxmin` nomli o'zgaruvchi mavjud emasmi? Bu shunday, lekin foydali Rust bizga `taxmin` ning oldingi qiymatini yangisi bilan ergashtirish imkonini beradi. *Shadowing* bizga ikkita noyob oʻzgaruvchini yaratish oʻrniga, `taxmin` oʻzgaruvchi nomidan qayta foydalanish imkonini beradi, masalan, `taxmin_str` va `taxmin`. Biz buni [3-bobda][shadowing]<!-- ignore --> batafsil ko'rib chiqamiz, ammo hozircha shuni bilingki, bu xususiyat ko'pincha qiymatni bir turdan boshqa turga aylantirmoqchi bo'lganingizda ishlatiladi.

Biz bu yangi o'zgaruvchini `taxmin.trim().parse()` ifodasiga bog'laymiz. Ifodadagi `taxmin` matni qator sifatida kiritilgan asl `taxmin` o'zgaruvchisiga ishora qiladi. `String` misolidagi `trim` usuli boshida va oxiridagi har qanday bo‘shliqni yo‘q qiladi, bu qatorni faqat raqamli ma’lumotlarni o‘z ichiga olishi mumkin bo‘lgan `u32` bilan solishtirishimiz uchun buni qilishimiz kerak. Foydalanuvchi `read_line` ni to'ldirish uchun <span class="keystroke">enter</span>tugmasini bosib, ularni kiritishi kerak
satrga yangi satr belgisini qo'shadigan taxmin. For example, if the user
types <span class="keystroke">5</span> and presses <span
class="keystroke">enter</span>, `guess` looks like this: `5\n`. The `\n`
represents “newline.” (On Windows, pressing <span
class="keystroke">enter</span> results in a carriage return and a newline,
`\r\n`.) The `trim` method eliminates `\n` or `\r\n`, resulting in just `5`.

The [`parse` method on strings][parse]<!-- ignore --> converts a string to
another type. Here, we use it to convert from a string to a number. We need to
tell Rust the exact number type we want by using `let guess: u32`. The colon
(`:`) after `guess` tells Rust we’ll annotate the variable’s type. Rust has a
few built-in number types; the `u32` seen here is an unsigned, 32-bit integer.
It’s a good default choice for a small positive number. You’ll learn about
other number types in [Chapter 3][integers]<!-- ignore -->.

Additionally, the `u32` annotation in this example program and the comparison
with `secret_number` means Rust will infer that `secret_number` should be a
`u32` as well. So now the comparison will be between two values of the same
type!

The `parse` method will only work on characters that can logically be converted
into numbers and so can easily cause errors. If, for example, the string
contained `A👍%`, there would be no way to convert that to a number. Because it
might fail, the `parse` method returns a `Result` type, much as the `read_line`
method does (discussed earlier in [“Handling Potential Failure with
`Result`”](#handling-potential-failure-with-result)<!-- ignore-->). We’ll treat
this `Result` the same way by using the `expect` method again. If `parse`
returns an `Err` `Result` variant because it couldn’t create a number from the
string, the `expect` call will crash the game and print the message we give it.
If `parse` can successfully convert the string to a number, it will return the
`Ok` variant of `Result`, and `expect` will return the number that we want from
the `Ok` value.

Let’s run the program now:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-03-convert-string-to-number/
cargo run
  76
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.

We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!

## Allowing Multiple Guesses with Looping

The `loop` keyword creates an infinite loop. We’ll add a loop to give users
more chances at guessing the number:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-04-looping/src/main.rs:here}}
```

As you can see, we’ve moved everything from the guess input prompt onward into
a loop. Be sure to indent the lines inside the loop another four spaces each
and run the program again. The program will now ask for another guess forever,
which actually introduces a new problem. It doesn’t seem like the user can quit!

The user could always interrupt the program by using the keyboard shortcut
<span class="keystroke">ctrl-c</span>. But there’s another way to escape this
insatiable monster, as mentioned in the `parse` discussion in [“Comparing the
Guess to the Secret Number”](#comparing-the-guess-to-the-secret-number)<!--
ignore -->: if the user enters a non-number answer, the program will crash. We
can take advantage of that to allow the user to quit, as shown here:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-04-looping/
cargo run
(too small guess)
(too big guess)
(correct guess)
quit
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/main.rs:28:47
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Typing `quit` will quit the game, but as you’ll notice, so will entering any
other non-number input. This is suboptimal, to say the least; we want the game
to also stop when the correct number is guessed.

### Quitting After a Correct Guess

Let’s program the game to quit when the user wins by adding a `break` statement:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-05-quitting/src/main.rs:here}}
```

Adding the `break` line after `You win!` makes the program exit the loop when
the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.

### Handling Invalid Input

To further refine the game’s behavior, rather than crashing the program when
the user inputs a non-number, let’s make the game ignore a non-number so the
user can continue guessing. We can do that by altering the line where `guess`
is converted from a `String` to a `u32`, as shown in Listing 2-5.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-05/src/main.rs:here}}
```

<span class="caption">Listing 2-5: Ignoring a non-number guess and asking for
another guess instead of crashing the program</span>

We switch from an `expect` call to a `match` expression to move from crashing
on an error to handling the error. Remember that `parse` returns a `Result`
type and `Result` is an enum that has the variants `Ok` and `Err`. We’re using
a `match` expression here, as we did with the `Ordering` result of the `cmp`
method.

If `parse` is able to successfully turn the string into a number, it will
return an `Ok` value that contains the resultant number. That `Ok` value will
match the first arm’s pattern, and the `match` expression will just return the
`num` value that `parse` produced and put inside the `Ok` value. That number
will end up right where we want it in the new `guess` variable we’re creating.

If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does
match the `Err(_)` pattern in the second arm. The underscore, `_`, is a
catchall value; in this example, we’re saying we want to match all `Err`
values, no matter what information they have inside them. So the program will
execute the second arm’s code, `continue`, which tells the program to go to the
next iteration of the `loop` and ask for another guess. So, effectively, the
program ignores all errors that `parse` might encounter!

Now everything in the program should work as expected. Let’s try it:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-05/
cargo run
(too small guess)
(too big guess)
foo
(correct guess)
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 4.45s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Awesome! With one tiny final tweak, we will finish the guessing game. Recall
that the program is still printing the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-6 shows the final code.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-06/src/main.rs}}
```

<span class="caption">Listing 2-6: Complete guessing game code</span>

At this point, you’ve successfully built the guessing game. Congratulations!

## Summary

This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, functions, the use of external crates, and more. In the next
few chapters, you’ll learn about these concepts in more detail. Chapter 3
covers concepts that most programming languages have, such as variables, data
types, and functions, and shows how to use them in Rust. Chapter 4 explores
ownership, a feature that makes Rust different from other languages. Chapter 5
discusses structs and method syntax, and Chapter 6 explains how enums work.

[prelude]: ../std/prelude/index.html
[variables-and-mutability]: ch03-01-variables-and-mutability.html#variables-and-mutability
[comments]: ch03-04-comments.html
[string]: ../std/string/struct.String.html
[iostdin]: ../std/io/struct.Stdin.html
[read_line]: ../std/io/struct.Stdin.html#method.read_line
[result]: ../std/result/enum.Result.html
[enums]: ch06-00-enums.html
[expect]: ../std/result/enum.Result.html#method.expect
[recover]: ch09-02-recoverable-errors-with-result.html
[randcrate]: https://crates.io/crates/rand
[semver]: http://semver.org
[cratesio]: https://crates.io/
[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html
[match]: ch06-02-match.html
[shadowing]: ch03-01-variables-and-mutability.html#shadowing
[parse]: ../std/primitive.str.html#method.parse
[integers]: ch03-02-data-types.html#integer-types
