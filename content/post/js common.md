---
title: "js常用函数"
date: 2021-09-13
draft: false
summary: "js常用函数"
---

# js常用函数

## 1、判断手机号是否正确

    function isEffectPhone(phone) {
        const reg = /^[1][3,4,5,6,7,8][0-9]{9}$/;
        if (!reg.test(phone)) {
            return false;
        } else {
            return ture;
        }
    }

## 2、判断传入时间是否大于当前实际时间

    function contrastTime(time) {
        return time.getTime() - new Date().getTime();
    }

## 3、获取屏幕宽度

    (document.documentElement && document.documentElement.clientWidth) ? document.documentElement.clientWidth : document.body.clientWidth

## 4、截取字符串后几位

    function subStringEnd(string, n) {
        return string.substring(string.length - n)
    }

## 5、根据数据获取所有值的和

    function getSum(array, type) {
        let result = 0;
        for (let i = 0; i < array.length; i++) {
            if (type) {
                result += (array[i][type] * 1000)
            } else {
                result += array[i]
            }
        }
        return result / 1000
    }

## 6、隐藏手机号中间四位

    function hiddenTel(tel) {
        return tel.substring(0, 3) + '****' + tel.substring(tel.length - 4)
    }

## 7、判断手机是ios/android

    function checkPlatform() {
        if (/android/i.test(navigator.userAgent)) {
            // console.log("This is Android'browser.");//这是Android平台下浏览器
            return '0';
        }
        if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
            // console.log("This is iOS'browser.");//这是iOS平台下浏览器
            return '1';
        }
        if (/Linux/i.test(navigator.userAgent)) {
            // console.log("This is Linux'browser.");//这是Linux平台下浏览器
            return '2';
        }
        if (/Linux/i.test(navigator.platform)) {
            // console.log("This is Linux operating system.");//这是Linux操作系统平台
            return '2';
        }
        if (/MicroMessenger/i.test(navigator.userAgent)) {
            // console.log("This is MicroMessenger'browser.");//这是微信平台下浏览器
        }
    }

## 8、生成随机的n位数

    function generateFiveRandomNum(n) {
        let code = ''
        for (let i = 0; i < n + 1; i++) {
            code += Math.floor(Math.random() * 10)
        }
        return code
    }

## 9、blob转base64

    function blobToDataURL(blob, callback) {
        let reader = new FileReader()
        reader.onload = function (e) {
            callback(e.target.result)
        }
        reader.readAsDataURL(blob)
    }

## 10、去掉一个字符串中的空格

    function removeBlank(string) {
        let reg = /\s+/g;
        if (typeof string === 'string') {
            return string.replace(reg, '')
        } else {
            return string
        }
    }

    string.split(' ').join('')

## 11、去除字符串中最后一个指定的字符

    function delString(str, delChar) {
        if (!str || typeof str !== 'string' || !deChar || typeof delChar !== 'string') return;
        let arr = str.split('')
        let idx = arr.lastIndexof(delChar)
        arr.splice(idx, 1)
        return arr.join('')
    }

## 12、将下划线命名转成大驼峰命名

    function strToUpperCase(str) {
        let string = '';
        let arr = str.split('_').filter(x => { return x && x.trim() });
        arr.map((v,i) => {string = (i === 0) ? v : string + v.substr(0,1).toUpperCase() + v.substr(1)})
        return string;
    }

## 13、把字符串大小写切换

    function caseConvert(str) {
        return str.replace(/([a-z]*)([A-Z]*)/g, (m, s1, s2) => {
            return `${s1.toUpperCase()}${s2.toLowerCase()}`
        })
    }

## 15、js对字符串进行加密和解密

    function compileStr(code) {
         let c = String.fromCharCode(code.charCodeAt(0) + code.lenght);
        for(let i = 1; i < code.length; i++) {
            c += String.fromCharCode(code.chartCodeAt(i) + code.chartCodeAt(i-1));
        }
        return escape(c);
    }

## 16、用递归算法实现，数组长度为5且元素的随机数在2-32间不重复的值

    function insertArr(arr, i=0, min=2, max=32) {
        const num = Math.max(min, Math.ceil(Math.random() * max))
        if(!arr[arr.length - 1]) {
            if(!arr.includes(num)) {
                arr[i++] = num;
            }
            return insertArr(arr, i);
        }
        return arr;
    }
    const arr = new Array(5);
    const result = insertArr(arr);

## 17、统一某一字符或字符串在另一个字符串中出现的次数

    function strCount(str, target) {
        let count = 0;
        if(!str || !target) return count;
        while (str.match(target)) {
            str = str.replace(target, '')
            count++
        }
        return count++
    }

    function strCount(str, target) {
        return str.split(target).length - 1
    }

## 18、判断数据类型的方法

    function type(obj) {
        return Object.prototype.toString.call(obj).replace(/\[object\s|\]/g,'');
    }

    console.log(type([]))  // 'Array'
    console.log(type(1)) // 'Number'