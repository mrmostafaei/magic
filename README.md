<h1 align="center">جادو</h1>
<h2 align="center">پکیج کمکی لاراول برای ایرانیان</h2>

---

<div align="center">

[![Latest Stable Version](https://poser.pugx.org/miladtech/magic/v/stable)](https://packagist.org/packages/miladtech/magic)
[![Total Downloads](https://poser.pugx.org/miladtech/magic/downloads)](https://packagist.org/packages/miladtech/magic)
[![License](https://poser.pugx.org/miladtech/magic/license)](https://packagist.org/packages/miladtech/magic)

</div>

---

<div dir="rtl">

> پکیج لاراول مخصوص توسعه دهندگان ایرانی


امکانات:
* ذخیره و بازیابی تاریخ شمسی در الوکوئنت
* شرط گذاری روی کوئری با تاریخ شمسی
* میدلویر برای تعمیر حروف ک و ی عربی و اعداد فارسی و عربی
* تابع تهیه اسلاگ فارسی
* ولیدیتور شماره همراه و شماره تماس ثابت
* دیتابیس شهر ها و استان های ایران به همراه روت بایندینگ ها
* پیدا کردن بانک از روی شماره کارت

## نصب
برای نصب شما به لاراول نسخه 6 یا بالاتر نیاز دارید. با استفاده از کومپوزر در پروژه لاراولی خود این پکیج رو نصب کنید.

<div dir="ltr">

```bash
composer require miladtech/magic
```

</div>

بعد هم با این کامند فایل کانفیگ رو بسازین.

<div dir="ltr">

```bash
php artisan vendor:publish --provider=MiladTech\\MagicServiceProvider
```

</div>

## کانفیگ
در فایل
<div dir="ltr">

```
config/magic.php
```

</div>

میتونین کانفیگ رو تغییر بدین.

`geo`:
اگه نمیخاین از قابلیت استان و شهر استفاده کنین مقدار این قسمت رو برابر

`false`

قرار بدید تا جداولش ایجاد نشه.

## ذخیره و بازیابی تاریخ شمسی
این پکیج این امکان رو به شما میده تا به راحتی تاریخ های موجود در مدل لاراول رو به راحتی به کلاس [ورتا](https://github.com/hekmatinasser/verta) تبدیل کنید.
برای شروع

trait

<div dir="ltr">

`MiladTech\EloquentHelper`

</div>

رو به مدلی که میخاین اضافه کنین.

<div dir="ltr">

```php
use MiladTech\EloquentHelper;

class User extends Model
{
    use EloquentHelper; // trait
}
```

</div>

بعد به راحتی میتونین به صورت شمسی به مشخصات دسترسی داشته باشین. فقط کافیه به فیلد مورد نظرتون پسوند

<div dir="ltr">

`_fa`

</div>

اضافه کنید.

<div dir="ltr">

```php
$user = User::where(...)->first();
$user->created_at_fa // به صورت کلاس Hekmatinasser\Verta\Verta
$user->created_at_fa_f // 1390/1/1
$user->created_at_fa_ft // 1390/1/1 12:00
$user->created_at_fa_ftt // 1390/1/1 12:00:00
$user->updated_at_fa->format("%B %d %Y") // فروردین 01 1390
```

</div>

برای مدیریت بهتر میتونین به مستندات
[ورتا](https://github.com/hekmatinasser/verta)
.مراجعه کنین
همچنین با تنظیم کردن تاریخ هم از همین روش استفاده کنین.

<div dir="ltr">

```php
$user->created_at_fa = Verta::createJalaliDate(1390, 1, 1);
dd($user->created_at); // Illuminate\Support\Carbon { date: 2011-03-21 }
$user->created_at_fa = "1392/1/1";
dd($user->created_at); // Illuminate\Support\Carbon { date: 2013-03-21 }
$user->created_at_fa = "1395/1/1 14:22:11";
dd($user->created_at); // Illuminate\Support\Carbon { date: 2016-03-20 14:22:11 }
```

</div>

### استفاده از اتریبیوت های دلخواه
اگه میخاین یه فیلد دلخواه به جز

created_at, updated_at

داشته باشین که از همین قابلیت پشتیبانی کنه کافیه اون رو به تاریخ [کست](https://laravel.com/docs/9.x/eloquent-mutators#date-casting) کنین.

<div dir="ltr">

```php
use MiladTech\EloquentHelper;

class User extends Model
{
    use EloquentHelper;

    protected $casts = [
        'birth_date' => 'date',
        // یا
        'released_at' => 'datetime'
    ];
}
```

</div>

و بازم هم به همون روش میتونین بهش دسترسی داشته باشین.

<div dir="ltr">

```php
$user->birth_date_fa; // Hekmatinasser\Verta\Verta
```

</div>

## کوئری بیلدر تاریخ شمسی

این دستورات کمک میکنن بر روی ستون های از نوع
date/datetime
شرط با تاریخ شمسی بذارین.

### whereJalali
شرط با یک تاریخ و زمان به خصوص شمسی

<div dir="ltr">

```php
User::whereJalali('created_at', '1399/01/15 14:00:00')->get();
// یا
User::whereJalali('created_at', Verta::createJalali(1399,01,15, 14, 0, 0))->get();
// شرط با عملگر
User::whereJalali('created_at', '>', '1399/01/15 14:00:00')->get();
```

</div>

### whereDateJalali
شرط با یک تاریخ به خصوص شمسی

<div dir="ltr">

```php
User::whereDateJalali('created_at', '1399/01/15')->get();
// یا
User::whereDateJalali('created_at', Verta::createJalaliDate(1399,01,15))->get();
// شرط با عملگر
User::whereDateJalali('created_at', '>', '1399/01/15')->get();
```

</div>

### whereInMonthJalali
شرط یک ماه خاص شمسی در یک سال

<div dir="ltr">

```php
User::whereInMonthJalali('created_at', 3)->get(); // فقط کاربران ایجاد شده در خرداد ماه سال جاری

User::whereInMonthJalali('created_at', 3, 1397)->get(); // فقط کاربران ایجاد شده در خرداد ماه سال 1397
```

</div>

### whereInYearJalali
شرط یک سال خاص شمسی

<div dir="ltr">

```php
User::whereInYearJalali('created_at')->get(); // فقط کاربران ایجاد شده در سال جاری

User::whereInYearJalali('created_at', 1397)->get(); //فقط کاربران ایجاد شده در سال 1397
```

</div>
    
### whereBetweenJalali
شرط بین دو تاریخ مشخص شمسی

<div dir="ltr">

```php
User::whereBetweenJalali('created_at', ['1400/03/26 12:00:00', '1400/05/26 12:00:00'])->get(); // فقط کاربران ایجاد شده بین دو تاریخ مشخص شده
// یا
User::whereBetweenJalali('created_at', [Verta::createJalali(1400,01,15, 14, 0, 0), Verta::createJalali(1399,01,15, 14, 0, 0)])->get(); 
// شرط با Not
User::whereNotBetweenJalali('created_at', ['1400/03/26 12:00:00', '1400/05/26 12:00:00'])->get(); // همه کاربران به جز کاربران ایجاد شده در تاریخ مشخص

```

</div>

## میدلویر
یکی از مشکلاتی که تو پروژه ها سر و کله میزنم باهاشون یکی اعداد فارسی هست. مثلا طرف میاد موقع ثبت نام رمز عبورش رو با عدد فارسی میزنه بعد موقع ورود با عدد انگلیسی بعد این وسط میگه رمز عبورش اشتباهه درصورتی که اینطوری نیست.
مشکل دوم اینه که مثلا ادمین یه چیزی با ک و ی فارسی وارد میکنه اما یه کاربری تو سایت کیبوردش عربی و با ك,ي عربی سرچ میکنه و این وسط موقع سرچ چیزی پیدا نمیکنه در صورتی که اینطور نیستش.
برای حل این مشکل کافیه در فایل

`app/Http/Kernel.php`

کلاس

<div dir="ltr">

```php
\MiladTech\Middleware\FixRequestInputs
```

</div>

به آرایه

middleware

اضافه کنین

<div dir="ltr">

```php
protected $middleware = [
    ...
    \MiladTech\Middleware\FixRequestInputs::class,
];
```

</div>

به همین راحتی برای همیشه هم با مشکل اعداد فارسی و هم مشکل حروف عربی خداحافظی کنین.


## ولیدیشن
iran_phone:
برای وارد کردن شماره های ثابت ایرانی

iran_mobile:
برای ولیدیشن شماره موبایل های ایرانی

> در صورتی که میخواید شماره هایی که بدون صفر هم وارد میشن رو بپذیره, به این صورت وارد کنید :
iran_mobile:true
در غیر این صورت اگر صفر وارد نشه شماره تایید نمیشه.

برای استفاده:

<div dir="ltr">

```php
public function test(Request $request)
{
    $request->validate([
        'mobile1' => 'required|iran_mobile',
        'phone' => 'required|iran_phone',
        'mobile2' => 'required|iran_mobile:true', // وارد کردن صفر در اول شماره اختیاری
    ]);
}
```

</div>

> این قسمت نیاز به بهبود دارد

iran_national_code:
ولیدیشن کد ملی

## فیکر
برای فیکر یه سری بهبود ها انجام شده برای مثال
paragraph
فارسی سازی شده.

CustomImage:

<div dir="ltr">

```php
customImage($path, $width, $height, $prefix)
```

</div>

این فیکر عکس از سایت

https://picsum.photos

براتون فراهم میکنه.

$path: پوشه محل ذخیره
$width: طول عکس
$height: عرض عکس
$prefix: به طور پیشفرض فقط نام عکس بهتون داده میشه با کمک این میتونین یه پیشوند به اسم عکس اضافه کنین

نمونه:

<div dir="ltr">

```php
'image' => $faker->customImage(public_path('uploads/fake'), 640, 480, 'fake/')
```

</div>

همچنین اگه میخاین یه آرایه از عکس داشته باشین

<div dir="ltr">

```php
$faker->customImages(public_path('uploads/fake'), 640, 480, $faker->numberBetween(1, 3), 'fake/')
```

</div>

چهارمین پارامتر تعداد عکس هایی که لازم دارین رو ازتون دریافت میکنه.

iranMobile:

یه شماره موبایل ایرانی براتون میسازه

<div dir="ltr">

```php
$faker->iranMobile
```

</div>

iranPhone:

یه شماره ثابت ایرانی براتون میسازه

<div dir="ltr">

```php
$faker->iranPhone
```

</div>

## استان ها و شهرها
برای شروع در

<div dir="ltr">

`DatabaseSeeder.php`

</div>

این قسمت رو اضافه کنید.


<div dir="ltr">

```php
public function run()
{
    $this->call(\MiladTech\Database\CitiesTableSeeder::class); // سیدر شهر ها و استان ها
}
```

</div>

سپس بعد از میگریت سید رو انجام بدین.

<div dir="ltr">

```bash
php artisan migrate --seed
```

</div>

حالا جداول شما از شهر ها و استان ها پر شده برای استفاده از دو مدل پایین میتونین استفاده کنین.

مدل استان:
`MiladTech\Models\Province`

مدل شهر:
`MiladTech\Models\City`

نمونه:

<div dir="ltr">

```php
use MiladTech\Models\City;

City::where('name', 'آمل')->first()
```

</div>

### روت بایندینگ
اگه میخاین از استان و شهر در آدرس ها استفاده کنین از این روش استفاده کنین.


<div dir="ltr">

```php
use MiladTech\Models\Province;
use MiladTech\Models\City;

Route::get('test/{province}/{city}', function (Province $province, City $city) {
    abort_if($city->province_id != $province->id, 404);
    // ...
});
```

</div>

و سپس آدرس زیر رو باز کنید.

`/test/27/1068`

حالا به استان و شهر دسترسی دارین.

اگه میخاین آدرستون

seo freindly

باشه از

slug

موجود در شهر ها و استان ها استفاده کنین.
این اسلاگ ها به کمک

[Eloquent Sluggable](https://github.com/cviebrock/eloquent-sluggable)

تهیه شدن.
بنابراین آدرس رو به این شکل باز کنین.

`/test/مازندران/آمل`.

اگه فقط میخاین با آی دی باز باشه یا فقط اسلاگ و هر دو حالت رو قبول نکنه در اون صورت از این ها به جای

`province`, `city`

استفاده کنین.

`city_by_id`: شهر با آی دی

`city_by_slug`: شهر با اسلاگ

`province_by_id`: استان با آی دی

`province_by_slug`: استان با اسلاگ


استان: [Province](./src/Models/Province.php)

شهر: [City](./src/Models/City.php)

## اسلاگ
همونطور که میدونین در لاراول با تابع


<div dir="ltr">

```php
Str::slug('test test')
```

</div>

میشه یه اسلاگ برای آدرس دهی درست کرد اما اگه فارسی به این تابع بدین

<div dir="ltr">

```php
Str::slug('خونه ی مادربزرگه')
```

</div>

خروجی

`"khonh-i-madrbzrgh"`

میده که یه جورایی سعی کرده به فینگلیش تبدیلش کنه اما با تابع
`str_to_slug`
این پکیج به راحتی حروف فارسی رو هم مدیریت میکنه

<div dir="ltr">

```php
str_to_slug('خونه ی مادربزرگه')
```

</div>

`"خونه-ی-مادربزرگه"`

### استفاده با پکیج Eloquent Sluggable
کافیه در فایل

`config/sluggable.php`

قسمت

method

رو این شکلی بنویسین.


<div dir="ltr">

```php
'method' => 'str_to_slug',
```

</div>

## پیداکردن بانک از روی شماره کارت
برای پیدا کردن بانک از روی شماره کارت از این تابع استفاده کنین.


<div dir="ltr">

```php
find_bank_by_card_number("6037697531")
```

</div>

خروجی:

<div dir="ltr">

```php
[
    "class" => "bsi",
    "name" => "بانک صادرات ایران",
    "card_prefix" => "603769",
]
```

</div>



## بهبود یافته شده با کمک

- [Eloquent Sluggable](https://github.com/cviebrock/eloquent-sluggable)
- [MiladTech](https://milad-tech.com)
- [TestBench](https://github.com/orchestral/testbench)
- [Verta](https://github.com/hekmatinasser/verta)

</div>
