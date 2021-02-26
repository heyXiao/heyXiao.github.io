---
title: 彻底理解Promise原理及全功能实现
date: 2020-11-20 12:26:22
author: heyXiao
# password: 35482c55c9fcbe4ff1b6023476c7acd59c011b9e8870376a45b3416ba8092d3d
summary: Promise 作为前端异步解决方案的出现，可以说是火遍全网，几乎所有的异步场景甚至框架都会有它的身影
categories: 技术
tags:
  - JavaScript
  - 转载
---

# 彻底理解 Promise 原理及全功能实现

## 开篇

**Promise**作为前端异步解决方案的出现，可以说是火遍全网，几乎所有的异步场景甚至框架都会有它的身影，比如**Vue**的批量处理等。今天我们就按照**Promise A+** 规范来完整实现**Promise**全功能，话不多说，上代码。

## Promise 实现

### status 状态定义

**Promise**的设定是一个不可逆的状态机，包含：

```javascript
{% raw %}
    const PENDDING = "PENDDING"; // 初始化pendding状态
    const RESOLVED = "RESOLVED"; // 正确完成resolve状态
    const REJECTED = "REJECTED"; // 错误完成reject状态
{% endraw %}
```

### MyPromise

创建**MyPromise**类函数和初始化相对应的值和状态

```javascript
{% raw %}
    class MyPromise {
      constructor(executor) {
        // 初始化状态status
        // 返回值value
        // 错误原因reason
        this.status = PENDDING;
        this.value = undefined;
        this.reason = undefined;

        // 返回值回调队列和错误回调队列
        this.resolves = [];
        this.rejects = [];

        // 声明resolve函数
        const resolve = (value) => {
          if (this.status === PENDDING) {
            this.status = RESOLVED; // 变更状态为完成状态
            this.value = value; // 赋值

            // 执行resolves队列
            while (this.resolves.length) {
              const callback = this.resolves.shift();
              callback(value);
            }
          }
        };

        // 声明reject函数
        const reject = (reason) => {
          if (this.statue === PENDDING) {
            this.status = REJECTED; // 变更状态为拒绝状态
            this.reason = reason; // 赋值

            // 执行rejects队列
            while (this.rejects.length) {
              const callback = this.rejects.shift();
              callback(reason);
            }
          }
        };

        try{
          executor(resolve,reject)
        }catch(e){
          reject(e)
        }
      }
    }
{% endraw %}
```

### MyPromise.then

#### 同步和异步

```javascript
{% raw %}
    class MyPromise {
      // ...
      then(resolve, reject) {
        // 完成状态，推入完成队列
        if (this.status === RESOLVED) {
          resolve(this.value);
        }

        // 拒绝状态，推入拒绝队列
        if (this.status === REJECTED) {
          reject(this.reason);
        }

        // 异步情况
        if (this.status === PENDDING) {
          this.resolves.push(resolve);
          this.rejects.push(reject);
        }
      }
      // ...
    }

    // 测试同步任务

    const promise = new MyPromise((resolve, reject) => {
        resolve('promise sync')
    })

    promise.then(res => {
        console.log(res)
    })

    // 打印结果
    // promise sync

    // 测试异步任务
    const promise = new MyPromise((resolve, reject) => {
        setTimeout(() => {
            resolve('promise async)
        }, 500)
    })

    promise.then(res => {
        console.log(res)
    })

    // 打印结果
    // promise async
{% endraw %}
```

#### Promise A+规范场景

> 根据 Promise A+ 规范，then 方法会返回一个 promise，从而支持向下链式调用。同时可以根据上一个 then 的返回值，透传给下一个 then 方法。

```javascript
{% raw %}
    // Promise A+
    const promise = new Promise((resolve, reject) => {
      resolve("first");
    });

    // 第一种场景：返回常规值
    promise
      .then((res) => {
        console.log(res);

        return "second";
      })
      .then((res) => {
        console.log(res);
      });

    // 打印结果
    // first
    // second

    // 第二种场景：返回promise
    promise
      .then((res) => {
        console.log(res);
        return new Promise((resolve) => {
          resolve("promise");
        });
      })
      .then((res) => {
        console.log(res);
      });

    // 打印结果
    // first
    // promise

    // 第三种场景：值穿透
    promise
      .then((res) => {
        console.log(res);
        return res;
      })
      .then()
      .then((res) => {
        console.log(res);
      });

    // 打印结果
    // first
    // first
{% endraw %}
```

#### 实现 then

根据以上规范定义，我们来改造一下**then**方法：

```javascript
{% raw %}
    class MyPromise {
      // ...
      then(resolve, reject) {
        // 判断resolve和reject未传入的情况，解决空值透传问题
        // then()情况
        typeof resolve !== 'function' ? resolve = value => value : resolve
        typeof reject !== 'function' ? reject = reason => throw new Error(reason instanceof Error ? reason.message : reason )

        //根据规范，then会返回一个全新的promise
        return new MyPromise((resolveFn, rejectFn) => {
            // 重写传入的resolve方法
            // 判断返回值
            const fulfilished = value => {
                try{
                    // 接收返回值
                    const res = resolve(value)

                    // 判断返回值类型：promise或普通类型
                    // 如果是promise，则往下执行一次then
                    // 如果是普通类型，则直接执行resolveFn，保证value是最新值
                    res instanceof MyPromise ? res.then(resolveFn,rejectFn) : resolveFn(res)
                }catch(e) {
                    rejectFn(e)
                }
            }

            // 重写传入的reject方法
            // 判断返回值
            const rejected = reason => {
                try{
            // 接收返回值
                    const res = reject(reason)

                    // 判断返回值类型：promise或普通类型
                    // 如果是promise，则往下执行一次then
                    // 如果是普通类型，则直接执行rejectFn，保证value是最新值
                    res instanceof MyPromise ? res.then(resolveFn,rejectFn) : rejectFn(res)
                }catch(e){
                    rejectFn(e instanceof Error ? e.message: e)
                }

            }

            // 判断同步异步任务
            // 执行相对应的方法
            // 这里用switch方法改进
            switch(this.status) {
                case RESOLVED:
                    fulfilished(this.value)
                break;

                case REJECTED:
                    rejected(this.reason)
                break;

                case PENDDING:
                    this.resolves.push(fulfilished)
                    this.rejects.push(rejected)
                  break;
            }
        })

      }
      // ...
    }

    // 测试
    const promise = new MyPromise((resolve, reject) => {
        resolve('first')
    })

    promise.then(res => {
        console.log(res)
        return new MyPromise((resolve, reject) => {
            resolve('promise second')
        })
    }).then().then(res => {
        console.log(res)
        return 'third'
    }).then(res => {
        console.log(res)
    })

    // 打印结果
    // first
    // promise second
    // third
{% endraw %}
```

#### 小结

测试成功，**promise**的改造就算符合规范了。这个难点在于**then**内函数的返回值如果是**promise**，那么我们会先让他执行注册一次**then**，让**promise**接着往下执行。

### MyPromise.catch

**catch**方法相对比较简单，将拒绝的值放到**reject**方法里执行就可以。

#### Promise A+规范场景

```javascript
{% raw %}
    // Promise A+
    const promise = new Promise((resolve, reject) => {
      reject("promise reject");
    });

    promise.catch((e) => {
      console.log(e);
    });

    // 打印结果
    // promise reject
{% endraw %}
```

#### 实现 catch

```javascript
{% raw %}
    class MyPromise {
      // ...
      catch(errorFn) {
        // 这里只需注册执行下then，传入callback就能实现
        this.then(null, errorFn);
      }
      // ...
    }

    // 测试
    const promise = new MyPromise((resolve, reject) => {
      reject("my promise reject");
    });

    promise.catch((e) => {
      console.log(e);
    });

    // 打印结果
    // my promise reject
{% endraw %}
```

#### 小结

**catch**方法在于执行回调去获取**reject**的结果，所以只需执行一下**then**并传入**callback**就实现了，相对好理解。

### MyPromise.all

业务场景中，我们经常会遇到不止一个**promie**的场景，因此需要合并一次执行多个**promise**，统一返回结果，**Promise.all**就是为了解决此问题。

#### Promise A+规范场景

```javascript
{% raw %}
    // Promise A+
    // 创建三个promise
    const promise1 = Promise.resolve(1)
    const promise2 = Promise.resolve(2)
    const promise3 = Promise.resolve(3)

    Promise.all([promise1,promise12,promise3]).then(res => {
      console.log(res)
    })

    // 打印结果
    // [1,2,3]

    // 添加一个reject
    const promise4 = Promise.resolve(1)
    const promise5 = Promise.reject('reject')
    const promise6 = Promise.resolve(3)

    Promise.all([promise4, promise5,promise6]).then(res => {
      console.log(res, 'resolve')
    }).catch(e => {
      console.log(e)
    })

    // 打印结果
    // reject
{% endraw %}
```

> 根据 Promise A+规范，Promise.all 可以同时执行多个 Promise，并且在所有的 Promise 方法都返回完成之后才返回一个数组返回值。当有其中一个 Promise reject 的时候，则返回 reject 的结果。

#### 实现 Promise.all

我们来实现一下：

```javascript
{% raw %}
    class MyPromise {
      // ...
      // all是静态方法
      static all(promises) {
        // 已然是返回一个promise
        return new MyPromise((resolve, reject) => {
          // 创建一个收集返回值的数组
          const result = []

          // 执行
          deepPromise(promises[0], 0 , result)

          // 返回结果
          resolve(result)

          // 这里我们用递归来实现
          // @param {MyPromise} promise 每一个promise方法
          // @param {number} index 索引
          // @param {string[]} result 收集返回结果的数组
          function deepPromise(promise, index, result) {
            // 边界判断
            // 所有执行完之后返回收集数组
            if(index > promises.length - 1) {
              return result
            }

            if(typeof promise.then === 'function') {
              // 如果是promise
              promise.then(res => {
                index++
                result.push(res)
                deepPromise(promises[index], index, result)
              }).catch(e => {
                // reject直接返回
                reject(e instanceof Error ? e.message : e)
              })
            }else {
              // 如果是普通值
              // 这里我们只做简单判断，非promise则直接当返回值处理
              index++
              result.push(promise)
              deepPromise(promises[index], index, res)
            }
          }
        })

      }
      // ...
    }

    // 测试
    // 创建三个MyPromise
    const promise1 = MyPromise.resolve(1)
    const promise2 = MyPromise.resolve(2)
    const promise3 = MyPromise.resolve(3)

    MyPromise.all([promise1,promise12,promise3]).then(res => {
      console.log(res)
    })

    // 打印结果
    // [1,2,3]

    // 添加一个reject
    const promise4 = MyPromise.resolve(1)
    const promise5 = MyPromise.reject('reject')
    const promise6 = MyPromise.resolve(3)

    MyPromise.all([promise4, promise5,promise6]).then(res => {
      console.log(res, 'resolve')
    }).catch(e => {
      console.log(e)
    })

    // 打印结果
    // reject
{% endraw %}
```

#### 小结

**Promise.all**作为一个批量处理的函数，让我们在使用时可以同时多个处理**promise**，简化了逐个执行的劣势。核心逻辑也相对比较简单，最重要的点在于执行完一个**promise**后再去执行下一个**promise**，处理完这个逻辑也就基本完成了**Promise.all**的全功能了。

### MyPromise.resolve

静态方法**resolve**的实现就相对简单了，返回一个**promise**，传入对应参数即可。

#### 实现 MyPromise.resolve

```javascript
{% raw %}
    class MyPromise {
      // ...
      static resolve(value) {
        return new MyPromise((resolveFn, rejectFn) => {
          resolveFn(value)
        })
      }
      // ...
    }

    // 测试
    MyPromise.resolve('static resolve').then(res => {
      console.log(res)
    })

    // 打印结果
    // static resolve
{% endraw %}
```

### MyPromise.reject

静态方法**reject**的实现跟**resolve**是类似，返回一个**promise**，传入对应参数即可。

```javascript
{% raw %}
    class MyPromise {
      // ...
      static reject(reason) {
        return new MyPromise((resolveFn, rejectFn) => {
          rejectFn(reason)
        })
      }
      // ...
    }

    // 测试
    MyPromise.reject('static reject').catch(e => {
      console.log(res)
    })

    // 打印结果
    // static reject
{% endraw %}
```

### 什么是 allSettled？

ECMA 官网最新更新了**Promise**的新的静态方法**Promise.allSettled**，那么这是一个怎样的方法呢？总体来讲他也是一个批量处理**Promise**的函数，但是我们已经有了**Promise.all**，为什么还需要**allSettled**。要解开这个问题，我们得回顾一下**Promise.all**。现有的**Promise.all**我们说过，如果**Promise**队列里有一个**reject**，那么他就只会返回**reject**，所以**Promise.all**不一定会返回所有结果，很显然**Promise.allSettled**能够解决这个问题。

#### Promise A+测试场景

```javascript
{% raw %}
    // Promise A+
    // 创建三个promise
    const promise1 = Promise.resolve(1)
    const promise2 = Promise.resolve(2)
    const promise3 = Promise.resolve(3)

    Promise.allSettled([promise1,promise12,promise3]).then(res => {
      console.log(res)
    })

    // 打印结果
    /*
    [
      {status: 'fulfilished', value: 1},
      {status: 'fulfilished', value: 2},
      {status: 'fulfilished', value: 3}
    ]
    */

    // 添加一个reject
    const promise4 = Promise.resolve(1)
    const promise5 = Promise.reject('reject')
    const promise6 = Promise.resolve(3)

    Promise.allSettled([promise4, promise5,promise6]).then(res => {
      console.log(res, 'resolve')
    }).catch(e => {
      console.log(e)
    })

    // 打印结果
      /*
    [
      {status: 'fulfilished', value: 1},
      {status: 'rejected', value: 'reject'},
      {status: 'fulfilished', value: 3}
    ]
    */
{% endraw %}
```

可以看出来**allSettled**和 all 最大的区别就是，**allSettled**不管是**resolve**，还是**reject**都能完整返回结果数组，只不过每个数组项都是以对象的形式输出，**status**描述状态，**value**接收返回值。

#### 实现 MyPromise.allSettled

**allSettled**的整体逻辑跟**all**是一样的，只不过返回值的处理稍微有所不同

```javascript
{% raw %}
    class MyPromise {
      // ...
      static allSettled(promises) {
          // 已然是返回一个promise
          return new MyPromise((resolve, reject) => {
            // 创建一个收集返回值的数组
            const result = []

            // 执行
            deepPromise(promises[0], 0 , result)

            // 返回结果
            resolve(result)

            // 这里我们用递归来实现
            // @param {MyPromise} promise 每一个promise方法
            // @param {number} index 索引
            // @param {string[]} result 收集返回结果的数组
            function deepPromise(promise, index, result) {
              // 边界判断
              // 所有执行完之后返回收集数组
              if(index > promises.length - 1) {
                return result
              }

              if(typeof promise.then === 'function') {
                // 如果是promise
                promise.then(res => {
                  index++
                  result.push({status: 'fulfilished', value: res}) // 这里推入的是对象
                  deepPromise(promises[index], index, result)
                }).catch(e => {
                  // reject直接返回
                  index ++
                  result.push({status: 'rejected', value: res}) // 这里推入的是对象
                  deepPromise(promises[index], index, result)
                })
              }else {
                // 如果是普通值
                // 这里我们只做简单判断，非promise则直接当返回值处理
                index++
                result.push({status: 'fulfilished', value: res}) // 这里推入的是对象
                deepPromise(promises[index], index, res)
              }
            }
          })
        }
    // ...
    }

    // 测试
    // 创建三个promise
    const promise1 = MyPromise.resolve(1)
    const promise2 = MyPromise.resolve(2)
    const promise3 = MyPromise.resolve(3)

    MyPromise.allSettled([promise1,promise12,promise3]).then(res => {
      console.log(res)
    })

    // 打印结果
    /*
    [
      {status: 'fulfilished', value: 1},
      {status: 'fulfilished', value: 2},
      {status: 'fulfilished', value: 3}
    ]
    */

    // 添加一个reject
    const promise4 = MyPromise.resolve(1)
    const promise5 = MyPromise.reject('reject')
    const promise6 = MyPromise.resolve(3)

    Promise.allSettled([promise4, promise5,promise6]).then(res => {
      console.log(res, 'resolve')
    }).catch(e => {
      console.log(e)
    })

    // 打印结果
      /*
    [
      {status: 'fulfilished', value: 1},
      {status: 'rejected', value: 'reject'},
      {status: 'fulfilished', value: 3}
    ]
    */
{% endraw %}
```

## 完整代码

```javascript
{% raw %}
    class MyPromise {
      constructor(executor) {
        // 初始化状态status
        // 返回值value
        // 错误原因reason
        this.statue = PENDDING;
        this.value = undefined;
        this.reason = undefined;

        // 返回值回调队列和错误回调队列
        this.resolves = [];
        this.rejects = [];

        // 声明resolve函数
        const resolve = (value) => {
          if (this.status === PENDDING) {
            this.status = RESOLVED; // 变更状态为完成状态
            this.value = value; // 赋值

            // 执行resolves队列
            while (this.resolves.length) {
              const callback = this.resolves.shift();
              callback(value);
            }
          }
        };

        // 声明reject函数
        const reject = (reason) => {
          if (this.statue === PENDDING) {
            this.status = REJECTED; // 变更状态为拒绝状态
            this.reason = reason; // 赋值

            // 执行rejects队列
            while (this.rejects.length) {
              const callback = this.rejects.shift();
              callback(reason);
            }
          }
        };

        try{
        	executor(resolve,reject)
        }catch(e){
        	reject(e)
        }
      }

      // then
      then(resolve, reject) {
        // 判断resolve和reject未传入的情况，解决空值透传问题
        // then()情况
        typeof resolve !== 'function' ? resolve = value => value : resolve
        typeof reject !== 'function' ? reject = reason => throw new Error(reason instanceof Error ? reason.message : reason): reject

        //根据规范，then会返回一个全新的promise
        return new MyPromise((resolveFn, rejectFn) => {
            // 重写传入的resolve方法
            // 判断返回值
            const fulfilished = value => {
                try{
                    // 接收返回值
                    const res = resolve(value)

                    // 判断返回值类型：promise或普通类型
                    // 如果是promise，则往下执行一次then
                    // 如果是普通类型，则直接执行resolveFn，保证value是最新值
                    res instanceof MyPromise ? MyPromise.then(resolveFn,rejectFn) : resolveFn(res)
                }catch(e) {
                    rejectFn(e)
                }
            }

            // 重写传入的reject方法
            // 判断返回值
            const rejected = reason => {
                try{
            // 接收返回值
                    const res = reject(reason)

                    // 判断返回值类型：promise或普通类型
                    // 如果是promise，则往下执行一次then
                    // 如果是普通类型，则直接执行rejectFn，保证value是最新值
                    res instanceof MyPromise ? MyPromise.then(resolveFn,rejectFn) : rejectFn(res)
                }catch(e){
                    rejectFn(e instanceof Error ? e.message: e)
                }

            }

            // 判断同步异步任务
            // 执行相对应的方法
            // 这里用switch方法改进
            switch(this.status) {
                case RESOLVED:
                    fulfilished(this.value)
                break;

                case REJECTED:
                    rejected(this.reason)
                break;

                case PENDDING:
                    this.resolves.push(fulfilished)
                    this.rejects.push(rejected)
                  break;
            }
        })

      }

      catch(errorFn) {
        // 这里只需注册执行下then，传入callback就能实现
        this.then(null, errorFn);
      }

      // resolve
      static resolve(value) {
        return new MyPromise((resolveFn, rejectFn) => {
          resolveFn(value)
        })
      }

        // reject
      static reject(reason) {
        return new MyPromise((resolveFn, rejectFn) => {
          rejectFn(reason)
        })
      }

      // all
      static all(promises) {
        // 已然是返回一个promise
        return new MyPromise((resolve, reject) => {
          // 创建一个收集返回值的数组
          const result = []

          // 执行
          deepPromise(promises[0], 0 , result)

          // 返回结果
          resolve(result)

          // 这里我们用递归来实现
          // @param {MyPromise} promise 每一个promise方法
          // @param {number} index 索引
          // @param {string[]} result 收集返回结果的数组
          function deepPromise(promise, index, result) {
            // 边界判断
            // 所有执行完之后返回收集数组
            if(index > promises.length - 1) {
              return result
            }

            if(typeof promise.then === 'function') {
              // 如果是promise
              promise.then(res => {
                index++
                result.push(res)
                deepPromise(promises[index], index, result)
              }).catch(e => {
                // reject直接返回
                reject(e instanceof Error ? e.message : e)
              })
            }else {
              // 如果是普通值
              // 这里我们只做简单判断，非promise则直接当返回值处理
              index++
              result.push(promise)
              deepPromise(promises[index], index, res)
            }
          }
        })

      }

      // allSettled
      static allSettled(promises) {
          // 已然是返回一个promise
          return new MyPromise((resolve, reject) => {
            // 创建一个收集返回值的数组
            const result = []

            // 执行
            deepPromise(promises[0], 0 , result)

            // 返回结果
            resolve(result)

            // 这里我们用递归来实现
            // @param {MyPromise} promise 每一个promise方法
            // @param {number} index 索引
            // @param {string[]} result 收集返回结果的数组
            function deepPromise(promise, index, result) {
              // 边界判断
              // 所有执行完之后返回收集数组
              if(index > promises.length - 1) {
                return result
              }

              if(typeof promise.then === 'function') {
                // 如果是promise
                promise.then(res => {
                  index++
                  result.push({status: 'fulfilished', value: res}) // 这里推入的是对象
                  deepPromise(promises[index], index, result)
                }).catch(e => {
                  // reject直接返回
                  index ++
                  result.push({status: 'rejected', value: res}) // 这里推入的是对象
                  deepPromise(promises[index], index, result)
                })
              }else {
                // 如果是普通值
                // 这里我们只做简单判断，非promise则直接当返回值处理
                index++
                result.push({status: 'fulfilished', value: res}) // 这里推入的是对象
                deepPromise(promises[index], index, res)
              }
            }
          })

        }
    }
{% endraw %}
```

## 总结

至此**Promise A+**的完整的方法和实现就完成了，个人觉得实现**promise**的难点在于理解**then**的值如何处理透传，这一个点能够理解的话，其它方法和逻辑都会比较顺其自然

## 关于转载

转载自[掘金](https://juejin.cn/post/6866372840451473415#heading-0) 作者[MichaelHong](https://juejin.cn/user/448256474885159)
