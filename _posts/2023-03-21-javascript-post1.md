---
layout: posts
permalink: /:categories/:title
title: "javascript 공부"
categories: javascript
---

javascript의 관련 지식을 기억하기 위한 포스트

## 변수명 앞에 오는 문자 열에 대한 정보
변수 명 앞에 '#' private, '_' protected, '$' element 

## 객체, 배열
'{}' 객체, '[]' 배열

### 배열 동적 키 생성
```javascript
var prefix = 'prop';
var i = 0;
var obj = {}; obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;
```

### 차이점
객체는 non-iterable, 배열은 iterable
속도도 배열이 더 빠르다.

## var, let, const
var 제약 없음 (ES6 사용시 var 키워드 사용 X 권장)
let 같은 변수명 사용 불가 ( 재 할당이 필요한 경우에 let 사용 권장)
const 상수

## Symbol
Enum 개념
const mySymbol = Symbol('symbolname'); 유일무이한 값
완전히 Enum 처럼 사용하려면, (값 변경을 막으려면) Object.freeze 사용
변수 할당 가능 const obj = {[Symbol('mybol')] : 1}; obj[Symbol('mybol')]; // -> 1

## module
### export
```html
<script type="module" src="app.mjs"></script>
```
mjs 확장자를 사용하고 
```javascript
export const pi = Math.PI;
```
- object, array, function 다 가능
처럼 export 하거나 문서 마지막에 
export { pi }; 할 수 있다.
### import
```javascript
import { pi } from './app.mjs';
console.log(pi); // 3.14159265
```

## strict mode
'use strict'

## 이름이 기억이..
```javascript
var a = true && 'Cat' // 'Cat'
var a = true && 'Cat' || 'Dog' // 'Cat'
var a = false && 'Cat' || 'Dog' // 'Dog'
var a = true ? 'Cat' : 'Dog' // 'Cat'
var a = false ? 'Cat' : 'Dog' // 'Dog'
```



