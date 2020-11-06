# Shared Memory and Atomics

## 简介

ECMAScript 2017 引入了一个新的构造函数 `SharedArrayBuffer` 和 具有辅助函数的命名空间对象 `Atomics`。

## 语法

### SharedArrayBuffer

JavaScript 是单线程，Web worker 引入了多线程：主线程用来与用户互动，Worker 线程用来承担计算任务。每个线程的数据都是隔离的，通过 `postMessage()` 通信。

```javascript
// 主线程
const w = new Worker('worker.js')

w.postMessage('hello')

w.onmessage = function (ev) {
  console.log(ev.data)
}
```

```javascript
// worker.js
onmessage = function (ev) {
  console.log(ev.data)
  postMessage('hi')
}
```

上面代码中，主线程创建了一个 Woker 线程，该线程与主线程之间通过 `postMessage()` 通信。

线程之间的数据交换可以是各种格式，这种数据交换采用复制机制，即一个进程将要分享的数据复制一份，通过 `postMessage` 方法发送给另一个进程。如果数据量较大，通信的效率会明显较低。共享内存的方式，会大大提高效率。

`SharedArrayBuffer` 对象用来表示一个通用的，固定长度的原始二进制数据缓冲区，类似于 `ArrayBuffer` 对象，唯一的区别是后者无法共享数据。

```javascript
new SharedArrayBuffer(length)
```

**参数**

`length`：所创建的数组缓冲区的大小，以字节(byte)为单位。

**返回值**

一个大小指定的新 `SharedArrayBuffer` 对象。其内容被初始化为 0。

**示例**

```javascript
// 主线程

// 新建 1KB 共享内存
const sharedBuffer = new SharedArrayBuffer(1024)

// 主线程将共享内存的地址发送出去
w.postMessage(sharedBuffer)

// 在共享内存上建立视图，供写入数据
const sharedArray = new Int32Array(sharedBuffer)
```

```javascript
// Worker 线程
onmessage = function (ev) {
  // 主线程共享的数据，就是 1KB 的共享内存
  const sharedBuffer = ev.data

  // 在共享内存上建立视图，方便读写
  const sharedArray = new Int32Array(sharedBuffer)

  // ...
}
```

共享内存也可以在 Worker 线程创建，发给主线程。

**注意：** `SharedArrayBuffer` 与 `ArrayBuffer` 一样，本身是无法读写的，必须在上面建立视图，然后通过视图读写。

### Atomics

多线程共享内存，最大的问题就是防止两个线程同时修改某个地址，或者说，当一个线程修改共享内存以后，必须有一个机制让其他线程同步。SharedArrayBuffer API 提供 `Atomics` 对象，保证所有共享内存的操作都是“原子性”的，并且可以在所有线程内同步。

> 原子操作：多个共享内存的线程能够同时读写同一位置上的数据。原子操作会确保正在读或写的数据的值是符合预期的，即下一个原子操作一定会在上一个原子操作结束后才会开始，其操作过程不会中断。

与一般的全局对象不同，`Atomics` 不是构造函数，因此不能使用 `new` 操作符调用，也不能将其当作函数直接调用。`Atomics` 的所有属性和方法都是静态的（与 `Math` 对象一样）。

#### 方法

##### Atomics.add()

将指定位置上的数组元素与给定的值相加，并返回相加前该元素的值。

##### Atomics.and()

将指定位置上的数组元素与给定的值相与，并返回与操作前该元素的值。

##### Atomics.compareExchange()

如果数组中指定的元素与给定的值相等，则将其更新为新的值，并返回该元素原先的值。

##### Atomics.exchange()

将数组中指定的元素更新为给定的值，并返回该元素更新前的值。

##### Atomics.isLockFree(size)

可以用来检测当前系统是否支持硬件级的原子操作。对于指定大小的数组，如果当前系统支持硬件级的原子操作，则返回 true；否则就意味着对于该数组，Atomics 对象中的各原子操作都只能用锁来实现。此函数面向的是技术专家。

##### Atomics.load()

返回数组中指定元素的值。

##### Atomics.notify()

唤醒等待队列中正在数组指定位置的元素上等待的线程。返回值为成功唤醒的线程数量。

##### Atomics.or()

将指定位置上的数组元素与给定的值相或，并返回或操作前该元素的值。

##### Atomics.store()

将数组中指定的元素设置为给定的值，并返回该值。

##### Atomics.sub()

将指定位置上的数组元素与给定的值相减，并返回相减前该元素的值。

##### Atomics.wait()

检测数组中某个指定位置上的值是否仍然是给定值，是则保持挂起直到被唤醒或超时。返回值为 "ok"、"not-equal" 或 "time-out"。调用时，如果当前线程不允许阻塞，则会抛出异常（大多数浏览器都不允许在主线程中调用 wait()）。

##### Atomics.xor()

将指定位置上的数组元素与给定的值相异或，并返回异或操作前该元素的值。

## 参考

[Shared memory and atomics for ECMAscript](https://github.com/tc39/ecmascript_sharedmem)  
[ArrayBuffer - ECMAScript 6 入门](https://es6.ruanyifeng.com/?search=SharedArrayBuffer&x=11&y=11#docs/arraybuffer#SharedArrayBuffer)  
[SharedArrayBuffer](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)  
[Atomics](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Atomics)
