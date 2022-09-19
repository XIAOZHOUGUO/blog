---
title: javascript string、array常用API练习
category:
  - 小窥 javascript
tags:
  - JavaScript
date: 2017-08-18 16:45:21
---
在 [freeCodeCamp](https://freecodecamp.cn/challenges/reverse-a-string) 做了一些练习，因此来总结一下有关javascript中常用的有关字符串和数组处理的官方API。
<!-- more -->
![科技感](http://bpic.588ku.com//back_pic/03/68/74/1657b3cea5bb265.jpg!/fh/170/quality/90/unsharp/true/compress/true)
### 1. Reverse a String 

又名 `翻转字符串` ，其要求如下：将给定的字符串进行翻转，并进行返回。
> 提示：先把字符串转化成数组，再借助数组的reverse方法翻转数组顺序，最后把数组转化成字符串。

```javascript
function reverseString(str) {
  str=str.split("").reverse().join("");
  return str;
}

reverseString("hello");
```
其中应用到了 `split()` `reverse()` `join()` 方法。

    split() : string对象方法，将字符串分割成字符串数组。如果把空字符串 ("") 用作 separator，那么字符串中的每个字符之间都会被分割。此方法不改变原始字符串。

    reverse() : array对象方法，颠倒数组元素的顺序。

    join() : array对象方法，将数组的所有元素放到一个字符串里。元素是通过指定的分隔符进行分隔的。

### 2. Factorialize a Number 

计算一个整数的阶乘。阶乘通常简写成 n!，例如: 5! = 1 * 2 * 3 * 4 * 5 = 120。要求返回运算结果。

方法一：for循环
```javascript
function factorialize(num) {
  if(num<0){
    return false;
  }else if(num === 0 || num === 1){
    return 1;
  }else{
    for(var i=num-1;i>0;i--){
      num*=i;
    }
  }
  return num;
}

factorialize(5);

```

方法二：递归

    将for循环改为：return factorialize(num-1)*num;

### 3. Check for Palindromes 
又名 `检查回文字符串` ，要求：如果给定的字符串是回文，返回true，反之，返回false。
> 如果一个字符串忽略标点符号、大小写和空格，正着读和反着读一模一样，那么这个字符串就是palindrome(回文)。提示：去掉字符串多余的标点符号和空格，然后把字符串转化成小写来验证此字符串是否为回文。

```javascript
function palindrome(str) {
  str=str.replace(/[\W_]/g,'').toLowerCase();
  var str2 = str.split('').reverse().join('');
  if(str === str2){
    return true;
  }else{
    return false;
  }
}

palindrome("1 eye for of 1 eye.");
```

其中新应用到了 `replace()` `toLowerCase()` 方法。

    replace() : string对象方法，在字符串中查找匹配的子串， 并替换与正则表达式匹配的子串。

    toLowerCase() : string对象方法，将字符串中的字符全部转换为小写。同理 `toUpperCase()` 转换为大写。

### 4. Find the Longest Word in a String 

`找出最长单词`，在句子中找出最长的单词，并返回它的长度。函数的返回值应该是一个数字。

```javascript
function findLongestWord(str) {
  str=str.split(' ').sort(function(a,b){
    return b.length-a.length; // 降序排列，return a.length - b.length 为升序
  });
  return str[0].length;
  //return '最长的单词：'+str[0]+' 长度：'+str[0].length;
}

findLongestWord("The quick brown fox jumped over the lazy dog");
```
其中新应用到了 `sort()` 方法。

    sort() ：array对象方法，对数组的元素进行排序。排序顺序可以是字母或数字，并按升序或降序。默认排序顺序为按字母升序。

### 5. Title Case a Sentence 

又名 `首字母大写` ，要求：确保字符串的每个单词首字母都大写，其余部分小写。

```javascript
 function titleCase(str) {
  var arr = str.toLowerCase().split(" ");
  for(var i=0;i<arr.length;i++){
    arr[i] = arr[i][0].toUpperCase() + arr[i].slice(1);
  }
  return arr.join(" ");

  /*方法二：
  str=str.toLowerCase().split(' ');
  var newstr='';
  for(var i in str){
    newstr+=str[i].substring(0,1).toUpperCase()+str[i].substring(1)+' ';
  }
  return newstr;*/
}

titleCase("HERE IS MY HANDLE HERE IS MY SPOUT");
```

其中新应用到了 `slice()` `substring()` 方法。

    slice() ：array对象方法，选取数组的的一部分，并返回一个新数组，不改变原数组。array.slice(start, end) 返回start和end之间的内容，如果没有指定end参数，那么返回包含从 start 到数组结束的所有元素。

    substring() : string对象方法，返回字符串中介于两个指定下标之间的字符。string.substring(from, to)，如果没有 to 参数，则从 from 一直到 结束。

### 6. Return Largest Numbers in Arrays 

找出多个数组中的最大数形成一个新数组返回之。

```javascript
function largestOfFour(arr) {
  var newarr=[];
  for(var i=0;i<arr.length;i++){
    arr[i].sort(function(a,b){
      return b-a;
    });
   newarr.push(arr[i][0]);
  }
  return newarr;
}

largestOfFour([[4, 5, 1, 3], [13, 27, 18, 26], [32, 35, 37, 39], [1000, 1001, 857, 1]]);
```

其中新应用到了 `push()` 方法。

    push() : array对象方法，向数组的末尾添加一个或更多元素，并返回新的长度。此方法改变数组的长度。同理 `pop()` 删除数组的最后一个元素并返回删除的元素。

### 7. Confirm the Ending 

又名 `检查字符串末尾` 。判断一个字符串是否以给定的字符串结尾，是true，否false。

```javascript
function confirmEnding(str, target) {
  var len=target.length;
  var strfrom=str.length-len;
  if(str.substr(strfrom,len)===target){
    return true;
  }else{
    return false;
  }
}

confirmEnding("Bastian", "n");

```

其中新应用到了 `substr()` 方法。

    substr() : string对象方法，从起始索引号提取字符串中指定数目的字符。string.substr(start,length)，如果省略了length参数，那么返回从 start 到结尾的字串。

### 8. Repeat a string repeat a string 

又名 `重复输出字符串` ，要求：重复一个指定的字符串 num次，如果num是一个负数则返回一个空字符串。

```javascript
function repeat(str, num) {
  // 请把你的代码写在这里
  var newstr='';
  if(num<0){
    return newstr;
  }else if(num==1){
    return str;
  }else{
    for(var i=0;i<num;i++){
      newstr+=str;
    }
    return newstr;
  }
}

repeat("abc", 3);
```

### 9. Truncate a string 

又名 `截断字符串` ，如果字符串的长度比指定的参数num长，则把多余的部分用...来表示。
切记，插入到字符串尾部的三个点号也会计入字符串的长度。
但是，如果指定的参数num小于或等于3，则添加的三个点号不会计入字符串的长度。

```javascript
function truncate(str, num) {
  if(str.length <= num){
    return str;
  }else if(num <= 3){
    return str.replace(str.substring(num,str.length),'...');
  }else{
    return str.replace(str.substring(num-3,str.length),'...');
  }
}

truncate("A-tisket a-tasket A green and yellow basket", 11);
```

### 10. Chunky Monkey 

猴子吃香蕉, 分割数组。把一个数组arr按照指定的数组大小size分割成若干个数组块。
例如:

    chunk([1,2,3,4],2)=[[1,2],[3,4]];
    chunk([1,2,3,4,5],2)=[[1,2],[3,4],[5]];

```javascript
function chunk(arr, size) {
  var newarr=[];
  if(size<0){
    return false;
  }else if(size === 1){
    return arr;
  }else{
    for(var i=0;i<arr.length;i=i+size){
      newarr.push(arr.slice(i,i+size));
    }
    return newarr;
  }
}

chunk(["a", "b", "c", "d"], 2);
```

### 11. Slasher Flick 

`截断数组` ，返回一个数组被截断n个元素后还剩余的元素，截断从索引0开始。

```javascript
function slasher(arr, howMany) {
  var newarr=[];
  if(howMany === 0){
    return arr;
  }else if(arr.length <= howMany){
    return newarr;
  }else{
    arr.splice(0,howMany);
    //var haha=arr.slice(howMany,arr.length);
    return arr;
  }
}

slasher([1, 2, 3], 2);
```

其中新应用到了 `splice()` 方法。

    splice() : array对象方法，从数组中添加或删除元素。这种方法会改变原始数组！。
    array.splice(index,howmany,item1,.....,itemX)，参数依次为：规定从何处添加/删除元素，规定应该删除多少元素，要添加到数组的新元素。

### 12. Mutations 

又名 `比较字符串` ，如果数组第一个字符串元素包含了第二个字符串元素的所有字符，函数返回true。
举例，["hello", "Hello"]应该返回true，因为在忽略大小写的情况下，第二个字符串的所有字符都可以在第一个字符串找到。

```javascript
function mutation(arr) {
  for(var i=0;i<arr[1].length;i++){
     if(arr[0].toLowerCase().indexOf(arr[1].toLowerCase().charAt(i)) == -1){
       return false;
     }
  }
 return true;
}

mutation(["hello", "hey"]);
```

其中新应用到了 `indexOf()` `charAt()` 方法。

    indexOf() : string对象方法，返回某个指定的字符串值在字符串中首次出现的位置。如果没有找到匹配的字符串则返回 -1。array对象也有这个方法，搜索数组中的元素，并返回它首次所在的位置。如果在数组中没找到字符串则返回 -1。

    charAt() : string对象方法，返回在指定位置的字符。

### 13. Falsy Bouncer 

又名 `过滤假值` 。删除数组中的所有假值。在JavaScript中，假值有false、null、0、""、undefined 和 NaN。

```javascript
function bouncer(arr) {
  var a =[];
  for(var i=0;i<arr.length;i++){
    if(arr[i]){
      a.push(arr[i]);
    }
  }
  return a;
}

bouncer([7, "ate", "", false, 9]);
```

### 14. Seek and Destroy 

实现一个摧毁(destroyer)函数，第一个参数是待摧毁的数组，其余的参数是待摧毁的值。

```javascript
function destroyer(arr) {
  var arg = [];
  for(var i=1;i<arguments.length;i++){
    arg.push(arguments[i]);
  }
  var temp = arr.filter(function(item){
    return arg.indexOf(item) === -1;
  });
  return temp;
}

destroyer([1, 2, 3, 1, 2, 3], 2, 3);
```

其中新应用到了 `filter()` 方法。

    filter() : array对象方法，检测数值元素，并返回符合条件所有元素的数组。filter() 不会改变原始数组。

`arguments` 是一个类似数组的对象, 对应于传递给函数的参数。它类似于数组，但除了 length 之外没有任何数组属性。

### 15. Where do I belong 

我身在何处？先插入数组后给数组排序，然后找到指定的值在数组的位置，最后返回位置对应的索引
举例：where([1,2,3,4], 1.5) 应该返回 1。因为1.5插入到数组[1,2,3,4]后变成[1,1.5,2,3,4]，而1.5对应的索引值就是1。

```javascript
function where(arr, num) {
  // 请把你的代码写在这里

  arr.push(num);
  arr.sort(function(a,b){
    return a-b;
  });
  return arr.indexOf(num);
  /*方法二
   for(var i=0;i<arr.length;i++){
    if(arr[i] === num){
      return i;
    }
  }*/
}

where([40, 60], 50);
```

### 16. Caesars Cipher 

`凯撒密码` ，下面我们来介绍风靡全球的凯撒密码Caesar cipher，又叫移位密码。
移位密码也就是密码中的字母会按照指定的数量来做移位。一个常见的案例就是ROT13密码，字母会移位13个位置。由'A' ↔ 'N', 'B' ↔ 'O'，以此类推。写一个ROT13函数，实现输入加密字符串，输出解密字符串。

```javascript
function rot13(str) {
  var newStr = "";
    for (var i in str) {
        var temp = str.charCodeAt(i);
        if (temp < 65 || temp > 91) {
            newStr+= str[i];
        } else if (temp > 77) {
            newStr += String.fromCharCode(temp - 13);
        } else {
            newStr += String.fromCharCode(temp + 13);
        }
    }
    return newStr;
}

rot13("SERR PBQR PNZC");  // 你可以修改这一行来测试你的代码
```

其中新应用到了 `charCodeAt()` `fromCharCode()` 方法。

    charCodeAt() : 返回在指定的位置的字符的 Unicode 编码。

    fromCharCode() : 将 Unicode 编码转为字符。

### 17. other importent

`map()` 方法可以方便的迭代数组，它会迭代数组中的每一个元素，并根据回调函数来处理每一个元素，最后返回一个新数组。注意，这个方法不会改变原始数组。

```javascript
//示例
var oldArray = [1,2,3,4,5];
var newArray = oldArray.map(function (val){
  return val+3;
});
// [4,5,6,7,8]
```

数组方法 `reduce()` 用来迭代一个数组，并且把它累积到一个值中。使用 reduce() 时，你要传入一个回调函数，这个回调函数的参数是一个 `累加(减)器` （比如例子中的 previousVal) 和当前值 (currentVal）。

reduce() 有一个可选的第二参数，它可以被用来设置累加器的初始值。如果没有在这定义初始值，那么初始值将变成数组中的第一项，而 currentVal 将从数组的第二项开始。

```javascript
var array = [4,5,6,7,8];
var singleVal = 0;
singleVal = array.reduce(function (previousVal,currentVal){
  return previousVal+currentVal;
},0);
//30
```

`concat()` 方法可以用来把两个数组的内容合并到一个数组中。concat 方法的参数应该是一个数组。参数中的数组会拼接在原数组的后面，并作为一个新数组返回。

```javascript
var oldArray = [1,2,3];
var newArray = [];
var concatMe = [4,5,6];

newArray = oldArray.concat(concatMe); // [1,2,3,4,5,6]
```
