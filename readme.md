# Lab1 - 驗證計算機邏輯

目標：熟悉 PHPUnit 的基本用法，以及如何撰寫單元測試。

## 安裝 PHPUnit

開啟 `composer.json` ，確認在 `require-dev` 中有引入 `"phpunit/phpunit": "~4.0"` 。

切換到 Terminal 視窗，執行：

```
php composer.phar install
```

照畫面指示完成安裝。

## 建立計算機類別

開啟 `src/Calculator.php` ，輸入以下內容：

```php
<?php

namespace Lab1;

class Calculator
{
    private $result;

    public function num($num)
    {
        $this->result = $num;
    }

    public function add($num)
    {
        $this->result += $num;
    }

    public function result()
    {
        return $this->result;
    }
}
```

## 手動測試

在 `Calculator` 類別下方，加上：

```php

$cal = new Calculator();
$cal->num(1);
$cal->add(2);
echo $cal->result();
```

打開 Terminal 視窗，執行：

```bash
php src/Calculator.php
```

## 建立測試類別

建立 `tests` 資料夾。

在 `tests` 裡建立 `CalculatorTest.php` 檔，寫入以下內容：

```php
<?php

class CalculatorTest extends \PHPUnit_Framework_TestCase
{
}
```

切換到 Terminal 視窗，執行 `./vendor/bin/phpunit` 。此步驟即為「執行測試」，後續不再重複描述。

### 第一種寫法

加入第一種測試的寫法，加入 `testAdd()` 方法：

```php
    public function testAdd()
    {
        // Arrange
        $calculator = new Lab1\Calculator();
        $expected = 3;

        // Act
        $calculator->num(1);
        $calculator->add(2);
        $actual = $calculator->result();

        // Assert
        $this->assertEquals($expected, $actual);
    }
```

執行測試。

### 第二種寫法

加入第二種不同的測試寫法，將以下程式碼貼到 `testAdd()` 方法的下方：

```php
    /**
     * @test
     */
    public function Add_two_numbers()
    {
        // Arrange
        $calculator = new Lab1\Calculator();
        $expected = 3;

        // Act
        $calculator->num(1);
        $calculator->add(2);
        $actual = $calculator->result();

        // Assert
        $this->assertEquals($expected, $actual);
    }
```

執行測試。

## 撰寫預期有異常的測試

修改 `num` 及 `add` 方法：

```php
    public function num($num)
    {
        if (!is_numeric($num)) {
            throw new \InvalidArgumentException;
        }
        $this->result = $num;
    }

    public function add($num)
    {
        if (!is_numeric($num)) {
            throw new \InvalidArgumentException;
        }
        $this->result += $num;
    }
```

在 `CalculatorTest` 類別中加入：

```php
    /**
     * @test
     * @expectedException InvalidArgumentException
     */
    public function Set_char()
    {
        // Arrange
        $calculator = new Lab1\Calculator();

        // Act
        $calculator->num('a');
    }

    /**
     * @test
     * @expectedException InvalidArgumentException
     */
    public function Add_chars()
    {
        // Arrange
        $calculator = new Lab1\Calculator();

        // Act
        $calculator->num('a');
    }
```

執行測試。

## 撰寫有 Data Provider 的測試

加入 `provider` 方法：

```php
    public function provider()
    {
        return [
            [1, 2, 3],
            [0, 0, 0],
        ];
    }
```

將 `Add_two_numbers` 方法改為：

```php
    /**
     * @test
     * @dataProvider provider
     */
    public function Add_two_numbers($augend, $addend, $expected)
    {
        // Arrange
        $calculator = new Lab1\Calculator();

        // Act
        $calculator->num($augend);
        $calculator->add($addend);
        $actual = $calculator->result();

        // Assert
        $this->assertEquals($expected, $actual);
    }
```

執行測試。

## 忽略測試

任選一個測試案例，並加入：

```php
$this->markTestSkipped();
```

執行測試。

## 分組測試

在測試方法的註解中，加入 `@group` ，如：

```
    /**
     * @group exception
     *
     * @test
     * @expectedException InvalidArgumentException
     */
```

切換到 Terminal 視窗，執行：

```bash
./vendor/bin/phpunit --group=exception
```

## 加入 phpunit.xml

建立 `phpunit.xml` ，內容為：

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit backupGlobals="false"
         backupStaticAttributes="false"
         bootstrap="vendor/autoload.php"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false"
         syntaxCheck="false">
    <testsuites>
        <testsuite name="Application Test Suite">
            <directory>./tests/</directory>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist>
            <directory suffix=".php">src/</directory>
        </whitelist>
    </filter>
</phpunit>
```