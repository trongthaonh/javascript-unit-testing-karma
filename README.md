Trong bài viết này mình sẽ trình bày những khái niệm cơ bản nhất khi làm quen với unit-testing và e2e-testing trên Javascript.

###### Unit-testing
Đây là test do chính lập trình viên viết, để kiểm tra các hàm do chúng ta viết ra có sai hay không. Unit test kiểm tra từng bộ phận nhỏ (unit) trong source code và đảm bảo các hàm này chạy đúng mỗi khi chúng ta sửa, cập nhật source code.

###### E2E-Testing
Ở mức độ cao hơn, để đảm bảo toàn bộ hệ thống chúng ta hoạt động trơn tru như mong đợi, E2E test ra đời giúp chúng ta kiểm thử toàn bộ hệ thống, từ giao diện cho đến các business logic trong hệ thống, giống như một người dùng thực thụ.

###### Khái niệm TDD (Test Driven Development)
Quay trở lại với cách làm việc thường thấy của một lập trình viên: Code - Test - Fix - Code - Test ... Hầu như chúng ta vẫn thường đánh giá thấp quá trình test và dành ít thời gian cho nó, nhưng một khi test, phát hiện bugs và fix bugs lại chiếm rất nhiều thời gian trong quá trình phát triển phần mềm.

TDD là một quy trình phát triển phần mềm với trọng tâm là hướng về phía kiểm thử, cụ thể các bước của TDD là:


> 1. Viết 1 test cho hàm mới. Đảm bảo rằng test sẽ fail.
2. Chuyển qua viết code sơ khai nhất cho hàm đó để test có thể pass.
3. Tối ưu hóa đoạn code của hàm vừa viết sao cho đảm bảo test vẫn pass và tối ưu nhất cho việc lập trình kế tiếp.
4. Lặp lại cho các hàm khác từ bước 1.

###### Kiến trúc Unit-test trên Javascript
Để bắt đầu viết một unit-tests với javascript, chúng ta cần tìm hiểu về kiến trúc, cách thức hoạt động khi một file unit-tests được thực thi.

<center>
![](/content/images/2016/01/UnitTests.png)
**Hình 1.1: Kiến trúc của một Unit tests** <br>
*Nguồn ảnh: https://priv.ly/pages/develop.html*
</center>

Theo hình 1.1, kiến trúc của unit-tests trong Javascript được thiết kế thành 3 tầng chính: <br>

- **Assertion library/ Test framework** <br>
Bình thường chúng ta có thể viết unit tests bằng Javascript thuần, tuy nhiên với sự hỗ trợ từ các test framework như [Jasmine](http://jasmine.github.io/), [Mocha](https://mochajs.org/), ... cú pháp sẽ trở nên dễ viết, dễ đọc hơn.<br>
- **Test runner** <br>
Nhiệm vụ của test runner ([Karma](https://karma-runner.github.io/0.13/index.html), [Wallabyjs](http://wallabyjs.com/)...) là cung cấp môi trường để thực thi test, kết nối các đoạn test được viết bởi Jasmine đến các iframes được chạy trên trình duyệt và nhận kết quả test trả về (passed/ failed with error).
- **Web browser** <br>
Web browswer là nơi các đoạn test được chạy, có hai lựa chọn đó là dùng trình duyệt thật (Chrome, Firefox...) và trình duyệt giả lập (Phantomjs).

###### Viết Unit testing đầu tiên với Karma, Jasmine và Phantomjs
<br>
==**1. Cài đặt Nodejs** == <br>
Karma chạy trên Nodejs và được cung cấp thông qua NPM (Node package management), vì vậy trước tiên ta cài đặt Nodejs và NPM thông qua Brew (OSX)

    $ brew install node

==**2. Tạo Project với theo cấu trúc như sau:** == <br>

```
js
|--tests
|  |--fixtures
|  |--specs
|--lib
index.html
```

Trong đó, thư mục **js/test/fixtures** và **js/test/specs** sẽ chứa tất cả các thành phần liên quan đến unit test, thư mục **lib** sẽ chứa các function thực hiện các chức năng của chương trình.


==**3. Cài đặt Karma:** == <br>

- *Khởi tạo NPM*

```
$ cd unit-testing-javascript
$ npm init
```

- *Cài đặt Karma local*
```
$ npm install karma --save-dev
```

- *Cài đặt Karma client global*
```
$ npm install karma-cli -g
```

- *Cài đặt Jasmine framework và connector karma-jasmine*
```
$ npm install jasmine-core karma-jasmine --save-dev
```

- *Cài đặt Chrome launcher*
```
$ npm install karma-chrome-launcher --save-dev
```

- *Cài đặt Karma Fixture* <br><br>
Fixture trong unit test là các thành phần cố định được chuẩn bị sẵn để sử dụng cho việc testing (ví dụ như data, hay các file html). **Karma fixture** là một plugin của Karma hỗ trợ load các file *.html hoặc *.json như một fixture để chuẩn bị cho việc testing.
```
$ npm install karma-fixture --save-dev
```

- *Cài đặt Karma Preprocessor* <br><br>
Preprocessor trong karma được sử dụng để xử lý file trước khi được sử dụng ở trình duyệt. Ở đây ta sẽ sử dụng preprocessor **html2js** để convert các file html mẫu thành Javascript strings và đặt nó thành biến global và sử dụng chúng để test DOM.<br>
```
$ npm install karma-html2js-preprocessor --save-dev
```
Xem thêm về Karma preprocessor tại [đây](http://karma-runner.github.io/0.13/config/preprocessors.html).

==**4. Config Karma:**== <br>

- Khởi tạo karma config
```
$ karma init
```
Thực hiện các bước khai báo cần thiết, ta được file **karma.conf.js** với các điểm đáng lưu ý như sau:

> Khai báo testing framework
```js
    frameworks: ['jasmine', 'fixture']
```
> Đường dẫn đến các files sẽ được load bởi trình duyệt, bao gồm các file library, fixtures và các file unit test.
```js
    files: [
      'js/lib/*.js',
      'js/tests/fixtures/*.html',
      'js/tests/specs/*.js'
    ]
```

> Khai báo preprocessors sử dụng
```js
    preprocessors: {
      'js/tests/fixtures/*.html' : ['html2js']
    }
```

> Khai báo trình duyệt sử dụng
```js
   browsers: ['Chrome']
```

OK, như vậy là đã xong các bước cài đặt và cấu hình, bước tiếp theo chúng ta sẽ viết Test theo TDD pattern. Let's started!

==**5. Viết unit test theo TDD:** == <br>

- Thêm file **index.html** với nội dung như sau:
```html
<html>
    <head>
        <title>Magic Calculator</title>
    </head>
    <body>
        <input id="x" type="text">
        <input id="y" type="text">
        <input id="add" type="button" value="Add Numbers">
        Result: <span id="result" />
        <script type="text/javascript" src="lib/calculator.js"></script>
        <script>
            calculator.init();
        </script>
    </body>
</html>
```

Chương trình là phép toán cộng đơn giản:
<html>
    <head>
        <title>Magic Calculator</title>
    </head>
    <body>
        <input id="x" type="text">
        <input id="y" type="text">
        <input id="add" type="button" value="Add Numbers">
        Result: <span id="result" />
        <script type="text/javascript" src="lib/calculator.js"></script>
        <script>
            calculator.init();
        </script>
    </body>
</html>

- Thêm file **js/fixtures/calculator.fixture.html** <br>
```html
<input id="x" type="text">
<input id="y" type="text">
<input id="add" type="button" value="Add Numbers">
Result: <span id="result" />
```

- Thêm file **js/specs/calculatorSpec.js** <br>
Cú pháp của Jasmine, xem tại [đây](http://jasmine.github.io/2.4/introduction.html).
```js

  beforeEach(function() {
    // Load fixture và inject vào DOM của trình duyệt trước khi test
    fixture.base = 'js/tests/fixtures';
    fixture.load('calculator.fixture.html');

    // Gọi đến hàm init từ library
    window.calculator.init();
  });

  // Viết test case đầu tiên
  it('should calculate 3 for 1 + 2', function() {
    document.getElementById('x').value = 1;
    document.getElementById('y').value = 2;
    document.getElementById('add').click();
    expect(parseInt(document.getElementById('result').innerHTML)).toEqual(3);
  });
```

- Chạy test lần đầu tiên
```
$ karma start karma.conf.js
```

Kết quả:
![](/content/images/2016/01/Screen-Shot-2016-01-22-at-5-18-52-PM.png)

Với kết quả chạy test lần đầu tiên, test failed với 2 lỗi:
```
TypeError: Cannot read property 'init' of undefined
	    at Object.<anonymous> (/Users/gumi-thao/Documents/Gumi Project/unit-testing-javascript/js/tests/specs/calculatorSpec.js:8:22)
```
```
	TypeError: Cannot read property 'equal' of undefined
	    at Object.<anonymous> (/Users/gumi-thao/Documents/Gumi Project/unit-testing-javascript/js/tests/specs/calculatorSpec.js:21:55)
```

Song song với kết quả trả về tại console, trình duyệt tự động kết nối đến Karma server, nhấn vào chế độ Debug để xem kết quả:
![](/content/images/2016/01/Screen-Shot-2016-01-22-at-5-28-12-PM.png)
<br>
Xem kết quả tại chế độ debug, lỗi được hiển thị dưới console log của trình duyệt:
![](/content/images/2016/01/Screen-Shot-2016-01-22-at-5-28-24-PM.png)

Nguyên nhân lỗi là do chúng ta chưa implement function

- Fix lỗi tại lần chạy test đầu tiên: thêm file **js/lib/calculator.js**
```js
'use strict';

window.calculator = (function() {
  function calculate() {

  };

  function init() {
    document.getElementById('add').addEventListener('click', calculate);
  };

  return {
      init: init
  }
})();

```

- Chạy test lại lần thứ 2

```
	TypeError: Cannot read property 'equal' of undefined
	    at Object.<anonymous> (/Users/gumi-thao/Documents/Gumi Project/unit-testing-javascript/js/tests/specs/calculatorSpec.js:16:55)
```
Với việc khởi tạo hàm init, chúng ta đã giải quyết được lỗi ở dòng số 8 ở bước trước.
Cứ tiếp tục như vậy, follow theo TDD Pattern: viết test trước, sau đó viết code cho function, chạy test, fix bugs, refactor code, cho đến khi tất cả các test đều PASSED.

![](/content/images/2016/01/Screen-Shot-2016-01-22-at-6-23-40-PM.png)
